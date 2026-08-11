# Registro da Semana - Integrações com APIs Externas

**Escola Manoel Ignácio** - Desenvolvimento de Sistemas - 3ª série B - 2026  
**Programação Front-End** - 3º Bimestre - Semana 17 - Aula 2  
`nome@dev:~$_`

---

## 🎯 Contexto e Objetivo

Este guia prático ensina como criar, configurar e conectar uma aplicação React Native desenvolvida com Expo e TypeScript ao Firebase Database (Cloud Firestore ou Realtime Database), permitindo gravar seu primeiro dado de forma simples, didática e segura.

---

## ⏳ Prazos e Pontuação

* **Entrega na 1ª semana (até 14 de agosto):** 10 pontos de semana AVA.
* **Entrega na 2ª semana (até 21 de agosto):** 5 pontos de semana AVA.
* **Entrega após 21 de agosto:** 1 ponto de semana AVA.

---

## ✈️ Envio

* **Identificação Obrigatória:** Adicione o seu nome completo nos arquivos para envio como identificação.
* **Opções de envio no AVA:** Cole a captura de tela ou gravação dos resultados, envie os arquivos, ou copie os códigos desenvolvidos e cole-os diretamente na plataforma AVA.

---

## 🛠️ Pré-requisitos

Antes de começar, certifique-se de que possui os seguintes itens instalados e configurados:

1. **Node.js**: Versão LTS instalada em sua máquina (recomendado versão 18 ou superior).
2. **Expo CLI / Create Expo App**: Ambiente de desenvolvimento React Native com suporte a TypeScript configurado com Expo.
3. **Conta Google / Firebase**: Uma conta Google ativa para acessar o [Firebase Console](https://console.firebase.google.com/).

---

## 📍 Passo 1: Criando e Configurando o Projeto no Firebase Console

1. **Criar um Novo Projeto no Firebase**:
   - Acesse o [Firebase Console](https://console.firebase.google.com/).
   - Clique em **"Adicionar projeto"** (ou "Criar um projeto").
   - Digite um nome para o seu projeto (exemplo: `meu-app-expo`) e clique em **Continuar**.
   - *(Opcional)* Na etapa do Google Analytics, você pode desativá-lo caso seja apenas um projeto de testes/estudos e clicar em **Criar projeto**.
   - Aguarde a criação e clique em **Continuar**.

2. **Adicionar uma Aplicação Web para Obter Credenciais**:
   - No painel principal (*Overview*) do seu projeto, clique no ícone da **Web (`</>`)** para registrar uma aplicação web.
   - Digite um apelido para o app (exemplo: `Expo App Web`).
   - Não é necessário marcar a opção de hospedagem do Firebase (*Firebase Hosting*).
   - Clique em **Registrar app**.
   - O Firebase exibirá o bloco de código contendo o objeto de configuração `firebaseConfig`. Mantenha esta página aberta ou copie os seguintes campos exibidos:
     - `apiKey`
     - `authDomain`
     - `projectId`
     - `storageBucket`
     - `messagingSenderId`
     - `appId`
     - `databaseURL` *(exibido se utilizar Realtime Database)*

3. **Ativar o Banco de Dados em Modo de Teste**:
   - **Opção A: Cloud Firestore (Recomendado)**:
     1. No menu lateral esquerdo, vá em **Criação > Firestore Database**.
     2. Clique em **Criar banco de dados**.
     3. Escolha a localização do banco de dados (exemplo: `southamerica-east1` em São Paulo ou `nam5` nos EUA).
     4. Na etapa de regras de segurança, selecione **Iniciar no modo de teste** (permite leituras e escritas temporárias para desenvolvimento por 30 dias).
     5. Clique em **Criar / Concluir**.
   - **Opção B: Realtime Database (Alternativa)**:
     1. No menu lateral esquerdo, vá em **Criação > Realtime Database**.
     2. Clique em **Criar banco de dados**, escolha a região e clique em **Avançar**.
     3. Selecione **Iniciar no modo de teste** e clique em **Ativar**.

---

## 📦 Passo 2: Configurando o Projeto Expo / React Native

1. **Instalar o SDK do Firebase**:
   No terminal, navegue até a pasta raiz do seu projeto Expo e execute o comando de instalação:

   ```bash
   npx expo install firebase
   ```
   > 💡 **Nota**: O comando `npx expo install` garante a instalação de uma versão do SDK do Firebase compatível com a versão do Expo utilizada no seu projeto.

2. **Configurar Variáveis de Ambiente (`.env`)**:
   Nas versões recentes do Expo, variáveis de ambiente públicas acessíveis pelo aplicativo devem ter o prefixo **`EXPO_PUBLIC_`**.

   Crie um arquivo chamado `.env` na raiz do seu projeto Expo e adicione as credenciais obtidas no Passo 1:

   ```env
   # Credenciais do Firebase (.env)
   EXPO_PUBLIC_FIREBASE_API_KEY=AIzaSy...SuaApiKeyAqui
   EXPO_PUBLIC_FIREBASE_AUTH_DOMAIN=seu-projeto.firebaseapp.com
   EXPO_PUBLIC_FIREBASE_PROJECT_ID=seu-projeto-id
   EXPO_PUBLIC_FIREBASE_STORAGE_BUCKET=seu-projeto.appspot.com
   EXPO_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=123456789012
   EXPO_PUBLIC_FIREBASE_APP_ID=1:123456789012:web:abcdef123456

   # Opcional: Apenas se você optar pelo Realtime Database
   # EXPO_PUBLIC_FIREBASE_DATABASE_URL=https://seu-projeto-default-rtdb.firebaseio.com
   ```

   > ⚠️ **Importante**: Nunca envie o arquivo `.env` para repositórios públicos. Adicione `.env` ao seu arquivo `.gitignore`.

---

## ⚡ Passo 3: Arquivo de Conexão (`firebaseConfig.ts`)

Crie o arquivo de inicialização do Firebase com TypeScript dentro do seu projeto (por exemplo, na pasta `src/services/firebaseConfig.ts` ou na raiz como `firebaseConfig.ts`).

### Código TypeScript para Cloud Firestore:

```typescript
// firebaseConfig.ts
import { FirebaseApp, initializeApp, getApps, getApp } from 'firebase/app';
import { Firestore, getFirestore } from 'firebase/firestore';

// Objeto de configuração consumindo as variáveis de ambiente do Expo
const firebaseConfig = {
  apiKey: process.env.EXPO_PUBLIC_FIREBASE_API_KEY,
  authDomain: process.env.EXPO_PUBLIC_FIREBASE_AUTH_DOMAIN,
  projectId: process.env.EXPO_PUBLIC_FIREBASE_PROJECT_ID,
  storageBucket: process.env.EXPO_PUBLIC_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: process.env.EXPO_PUBLIC_FIREBASE_MESSAGING_SENDER_ID,
  appId: process.env.EXPO_PUBLIC_FIREBASE_APP_ID,
};

// Evita que o app Firebase seja inicializado mais de uma vez durante o Fast Refresh do Expo
const app: FirebaseApp = !getApps().length ? initializeApp(firebaseConfig) : getApp();

// Inicializa a instância do banco de dados (Cloud Firestore) com tipagem
const db: Firestore = getFirestore(app);

export { app, db };
```

*(Caso opte por usar o **Realtime Database**, substitua a importação do Firestore por `import { Database, getDatabase } from 'firebase/database';` e exporte `const db: Database = getDatabase(app);`)*.

---

## 🚀 Passo 4: Gravando o Primeiro Dado / Documento

Para validar se a conexão e as credenciais foram configuradas corretamente, crie um exemplo mínimo e direto utilizando TypeScript dentro de uma tela do Expo (exemplo: no arquivo `App.tsx`).

### Exemplo de Componente React Native com TypeScript (`App.tsx`):

```tsx
import React, { useState } from 'react';
import {
  StyleSheet,
  Text,
  View,
  TouchableOpacity,
  ActivityIndicator,
  Alert
} from 'react-native';

// Importações do Firestore e da nossa configuração
import { collection, addDoc, serverTimestamp, DocumentReference } from 'firebase/firestore';
import { db } from './firebaseConfig'; // Ajuste o caminho do arquivo se necessário

export default function App(): React.JSX.Element {
  const [loading, setLoading] = useState<boolean>(false);
  const [status, setStatus] = useState<string>('');

  // Função disparada ao clicar no botão para salvar um documento de teste
  const handleSalvarPrimeiroDado = async (): Promise<void> => {
    setLoading(true);
    setStatus('');

    try {
      // Adiciona um novo documento na coleção "teste_conexao"
      const docRef: DocumentReference = await addDoc(collection(db, 'teste_conexao'), {
        mensagem: 'Olá! Conexão entre Expo (TypeScript) e Firebase realizada com sucesso.',
        plataforma: 'React Native (Expo + TypeScript)',
        criadoEm: serverTimestamp(),
      });

      console.log('Documento gravado com sucesso! ID:', docRef.id);
      setStatus(`✅ Documento criado com sucesso!\nID: ${docRef.id}`);
      Alert.alert('Sucesso 🎉', `Dado gravado no Firebase!\nID: ${docRef.id}`);
    } catch (error: unknown) {
      const errorMessage = error instanceof Error ? error.message : 'Erro desconhecido ao salvar no banco.';
      console.error('Erro ao gravar dado no Firebase:', error);
      setStatus(`❌ Erro: ${errorMessage}`);
      Alert.alert('Erro ao Gravar ❌', errorMessage);
    } finally {
      setLoading(false);
    }
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Teste de Conexão Firebase</Text>
      <Text style={styles.subtitle}>Expo + TypeScript + Cloud Firestore</Text>

      <TouchableOpacity
        style={styles.button}
        onPress={handleSalvarPrimeiroDado}
        disabled={loading}
        activeOpacity={0.8}
      >
        {loading ? (
          <ActivityIndicator color="#FFFFFF" />
        ) : (
          <Text style={styles.buttonText}>Gravar Primeiro Dado</Text>
        )}
      </TouchableOpacity>

      {status !== '' && (
        <View style={styles.statusBox}>
          <Text style={styles.statusText}>{status}</Text>
        </View>
      )}
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#F8FAFC',
    alignItems: 'center',
    justifyContent: 'center',
    padding: 24,
  },
  title: {
    fontSize: 24,
    fontWeight: 'bold',
    color: '#0F172A',
    marginBottom: 8,
    textAlign: 'center',
  },
  subtitle: {
    fontSize: 16,
    color: '#64748B',
    marginBottom: 32,
  },
  button: {
    backgroundColor: '#0284C7',
    paddingVertical: 14,
    paddingHorizontal: 28,
    borderRadius: 10,
    elevation: 3,
    shadowColor: '#0284C7',
    shadowOffset: { width: 0, height: 4 },
    shadowOpacity: 0.3,
    shadowRadius: 6,
  },
  buttonText: {
    color: '#FFFFFF',
    fontSize: 16,
    fontWeight: '600',
  },
  statusBox: {
    marginTop: 32,
    padding: 16,
    backgroundColor: '#E2E8F0',
    borderRadius: 8,
    width: '100%',
  },
  statusText: {
    fontSize: 14,
    color: '#334155',
    textAlign: 'center',
  },
});
```

---

## ⚠️ Passo 5: Alerta Importante sobre Contas e Limites do Plano Spark

### 💳 Plano Spark (Gratuito) e Cuidados de Segurança Financeira

- **O que é o Plano Spark?**
  O Firebase opera por padrão no **Plano Spark**, que é **100% gratuito** e oferece limites diários generosos para desenvolvimento e aprendizado (como 50.000 leituras, 20.000 escritas e 1 GB de armazenamento por dia no Firestore). Enquanto mantiver seu projeto no plano Spark, **não haverá nenhuma cobrança**.

- **Cuidado ao Utilizar Contas Pessoais**:
  - Caso utilize sua **conta Google pessoal** no Firebase e eventualmente cadastre uma forma de pagamento ou altere o plano do projeto para o plano pago **Blaze (Pay-as-you-go)**, existe o risco de cobranças indesejadas em caso de vazamento de credenciais, loops infinitos no código ou alto volume inesperado de requisições.
  - **Recomendação**: Para projetos pessoais de aprendizado, mantenha sempre o projeto estritamente no plano **Spark** e nunca cadastre cartões de crédito no Console se não pretender utilizar recursos pagos.

- **Vantagens de Utilizar E-mails Institucionais de Estudantes**:
  - Ao criar e gerenciar seus projetos no Firebase utilizando um **e-mail institucional fornecido por sua escola, faculdade ou universidade**, as políticas administrativas dessas contas educacionais geralmente bloqueiam a adição de cartões de crédito pessoais e impedem a alteração para planos pagos.
  - Isso garante total **segurança financeira para o estudante**, eliminando qualquer risco de transição acidental de plano ou cobranças na fatura pessoal, sendo a opção mais recomendada para atividades acadêmicas.

### 🔒 Ajuste das Regras de Segurança antes da Publicação

> ⚠️ **Atenção**: Ao ativar o banco de dados no **Modo de Teste**, o Firebase configura regras que permitem leitura e escrita aberta por **30 dias**. 
>
> Antes de distribuir ou publicar seu aplicativo, acesse o **Firebase Console > Firestore Database > Regras** e altere as regras para exigir autenticação de usuários (exemplo: `allow read, write: if request.auth != null;`), protegendo seus dados contra acessos indevidos.
