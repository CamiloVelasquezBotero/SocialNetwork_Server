# ChatApp — Real-Time Social Network Server

The WebSocket backend for a real-time chat and social network application. Built with **Express**, **Socket.io**, and **TypeScript**, this server manages all live connections: online presence, friend requests, and instant message delivery between users.

> This repository is the **backend WebSocket server**.
> The frontend Next.js client lives at [SocialNetwork_Client](https://github.com/CamiloVelasquezBotero/SocialNetwork_Client).

---

## Responsibilities

This server does not interact with the database directly. Its sole responsibility is real-time event routing:

- Maintaining a registry of connected users and their associated socket IDs.
- Notifying friends when a user connects or disconnects.
- Relaying friend request events to the target user if they are online.
- Emitting friend request acceptance notifications to the sender.
- Broadcasting removal notifications when a user removes a contact.
- Forwarding new chat messages instantly to the recipient.

All persistent data (users, messages, chats) is managed exclusively by the Next.js client through Prisma and PostgreSQL.

---

## Tech Stack

| Layer | Technology |
| :--- | :--- |
| Runtime | Node.js |
| Language | TypeScript |
| HTTP Server | Express 5 |
| WebSocket | Socket.io 4 |
| Environment | dotenv |
| Development | node --watch (native) |

---

## Socket Events

### Events received from the client

| Event | Payload | Description |
| :--- | :--- | :--- |
| `register-user` | `{ userId, friendsId }` | Registers the user and notifies online friends |
| `friend-request-sent` | `{ idReceiver }` | Notifies the target user of a new friend request |
| `friend-request-accepted` | `{ userData, idSender, action }` | Notifies the sender that their request was accepted |
| `friend-removed` | `{ userId, userRemovedId }` | Updates friend lists for both users and notifies the removed user |
| `new-message-sent` | `{ newMessage, idReceiver }` | Forwards a message to the recipient |
| `disconnect` | _(automatic)_ | Removes the user and notifies their friends |

### Events emitted to the client

| Event | Payload | Description |
| :--- | :--- | :--- |
| `friends-online` | `number[]` | List of friend IDs currently online |
| `friend-connected` | `number` | A specific friend just connected |
| `friend-disconnected` | `number` | A specific friend just disconnected |
| `update-notifications` | _(none)_ | Triggers a notification refresh on the recipient |
| `request-accepted-notification` | `UserData` | Delivers accepted friend data to the sender |
| `friend-removed-notification` | `number` | Notifies the removed user |
| `new-message` | `Message` | Delivers an incoming message to the recipient |

---

## Project Structure

```
Server_Socket.io_SocialNetwork/
├── index.ts       # Express app + Socket.io event handlers
├── types/
│   └── index.ts   # Type definitions (ConnectedUsers, RegisterUserType, etc.)
├── package.json
└── tsconfig.json
```

---

## Getting Started

### Prerequisites

- Node.js 18+
- The [frontend client](https://github.com/CamiloVelasquezBotero/SocialNetwork_Client) running on port `3000`

### Installation

```bash
git clone https://github.com/CamiloVelasquezBotero/SocialNetwork_Server.git
cd SocialNetwork_Server
npm install
```

### Environment Variables

Create a `.env` file in the root of the project:

```env
PORT=4000
FRONTEND_URL=http://localhost:3000
```

### Running the Development Server

```bash
npm run dev
```

The server uses Node.js's native `--watch` flag, so it automatically restarts when any file changes.

The server will be available at [http://localhost:4000](http://localhost:4000).

---

## Architecture Overview

```
┌─────────────────────┐         WebSocket          ┌──────────────────────┐
│   Next.js Client    │ ◄─────────────────────────► │  Socket.io Server    │
│   (port 3000)       │                             │  (port 4000)         │
│                     │                             │                      │
│  - Zustand store    │                             │  - ConnectedUsers {}  │
│  - React UI         │                             │  - Event routing     │
│  - Prisma + Neon DB │                             │  - No DB access      │
└─────────────────────┘                             └──────────────────────┘
```

---

## Related Repository

| Repository | Description |
| :--- | :--- |
| [SocialNetwork_Client](https://github.com/CamiloVelasquezBotero/SocialNetwork_Client) | Next.js frontend client |
| [SocialNetwork_Server](https://github.com/CamiloVelasquezBotero/SocialNetwork_Server) | This repository — Express + Socket.io backend |

---

## Author

**Camilo Velasquez Botero**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/camilodeveloper/)
