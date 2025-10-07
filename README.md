# 🤖 Chatbot SMS basé sur un RAG (Retrieval-Augmented Generation)

## 📌 Contexte du projet
Ce projet met en place un **chatbot SMS intelligent** reposant sur la technique du **RAG (Retrieval-Augmented Generation)**.  
L’architecture combine plusieurs briques technologiques modernes :  

- **n8n** : pour l’orchestration des workflows.  
- **OpenAI** : pour la génération de texte (GPT) et la création d’embeddings.  
- **Supabase (Postgres + pgvector)** : pour stocker et rechercher efficacement les vecteurs d’embeddings.  
- **OVH SMS API** : pour l’envoi et la réception de messages SMS.  

🎯 L’objectif est simple : permettre à un utilisateur de dialoguer par SMS avec un chatbot capable de donner des réponses enrichies et contextualisées grâce à une base documentaire stockée dans **Postgres / Supabase**.

---

## 🏗️ Architecture du projet

Le système repose sur **trois workflows principaux** développés dans **n8n** :

---

### 1. 🚀 Création du RAG (Vector Store)
**Workflow : Intégration et traitement de fichiers**
- Import d’un fichier (PDF, DOCX, TXT, etc.) via un formulaire ou un upload.  
- Découpage en *chunks* grâce au `Recursive Character Text Splitter`.  
- Génération d’**embeddings OpenAI**.  
- Stockage dans **Supabase** (Postgres avec extension `pgvector`).  

👉 Cette étape permet d’alimenter la base de connaissances du chatbot.

---

### 2. 💬 Chatbot de test RAG
**Workflow : Agent conversationnel avec mémoire**
- Réception d’un message test.  
- Requête vers **Supabase Vector Store** pour retrouver les chunks pertinents.  
- L’**AI Agent (GPT)** combine la question + le contexte extrait.  
- Réponse générée et renvoyée à l’utilisateur.  

👉 Sert à vérifier que la base vectorielle (RAG) fonctionne correctement avant de la connecter aux SMS.

---

### 3. 📲 Chatbot SMS connecté à OVH
**Workflow : Intégration SMS + RAG**
- Réception d’un SMS via le webhook de l’API OVH.  
- Filtrage et formatage du message utilisateur.  
- Passage du message à l’**AI Agent** avec accès à Supabase Vector Store.  
- Vérification/formatage de la réponse.  
- Renvoi de la réponse par SMS via un `HTTP Request` vers **OVH SMS API**.  

👉 Au final : un utilisateur peut interagir par SMS avec un chatbot qui exploite une base documentaire enrichie via embeddings.

---

## 🛠️ Technologies utilisées

- **Langage / Orchestration** : [n8n](https://n8n.io/)  
- **IA / Embeddings** : [OpenAI API](https://platform.openai.com/)  
- **Base de données** : [Postgres](https://www.postgresql.org/) hébergée sur [Supabase](https://supabase.com/) avec l’extension `pgvector`  
- **Stockage vectoriel** : Supabase Vector Store  
- **Communication SMS** : [OVH SMS API](https://www.ovh.com/)  

---

## 🚀 Guide de démarrage

### 1. Pré-requis
- Compte [OpenAI](https://platform.openai.com/) + clé API.  
- Instance [Supabase](https://supabase.com/) avec une base Postgres et `pgvector` activé.  
- Compte [OVH SMS API](https://www.ovh.com/).  
- Instance [n8n](https://n8n.io/) (Docker ou Cloud).  

### 2. Variables d’environnement (`.env`)
```env
OPENAI_API_KEY=sk-xxxxxxxx
SUPABASE_URL=https://xxxx.supabase.co
SUPABASE_KEY=xxxx
OVH_APP_KEY=xxxx
OVH_APP_SECRET=xxxx
OVH_CONSUMER_KEY=xxxx
OVH_ENDPOINT=ovh-eu
3. Importation des workflows

Importer les 3 fichiers JSON du dossier /workflows dans votre instance n8n.

Configurer les credentials (OpenAI, Supabase, OVH).

4. Lancer les workflows

Uploader un document → génération des embeddings → stockage dans Postgres (Supabase).

Tester le chatbot RAG (sans SMS).

Activer le webhook OVH → démarrer la conversation par SMS.


📂 Structure du projet
/workflows
  ├── 1_rag_creation.json        # Workflow de création du RAG
  ├── 2_rag_chatbot_test.json    # Workflow de test du chatbot RAG
  └── 3_sms_chatbot_rag.json     # Workflow chatbot SMS (OVH)
README.md

📊 Schéma d’architecture (Mermaid)
flowchart TD
    A[Fichier uploadé] --> B[Découpage en chunks]
    B --> C[Embeddings OpenAI]
    C --> D[Supabase - Postgres + pgvector]

    E[SMS Utilisateur] --> F[Webhook OVH]
    F --> G[AI Agent + RAG]
    D --> G
    G --> H[Réponse générée]
    H --> I[Retour SMS via OVH API]

    
✨ Résultat attendu

Un chatbot SMS intelligent capable de :
✔️ Comprendre et répondre en langage naturel.
✔️ Exploiter une base documentaire enrichie stockée dans Postgres / Supabase.
✔️ Interagir directement par SMS grâce à OVH API.


---

