# 🛕 Kumbh Safe — Group Safety Tracker for Kumbh Mela

> *"Don't get lost at the world's largest gathering."*

A real-time group safety web app built for Kumbh Mela pilgrims. Register yourself, link your group, and get instant alerts if anyone drifts beyond your safe zone — with one-tap SOS navigation to find them.

---

## 🎯 Problem

Kumbh Mela hosts **40–50 million people** on peak bathing days. Getting separated from your group is not just stressful — it can become a genuine emergency. Existing solutions (phone calls, walkie-talkies, meeting points) break down in the noise and crowd density of the mela grounds.

## 💡 Solution

Kumbh Safe gives every group member:
- A **unique ID** generated on registration
- A **live map** showing where all group members are in real time
- A **configurable safe zone** (100m–1km) that fires an alert the moment anyone drifts too far
- A **one-tap SOS screen** with compass direction, distance, and an emergency helpline button

---

## ✨ Features

| Feature | Description |
|---|---|
| 📲 Register & Link | Get a unique `KS-XXXX` ID and connect it with your group members |
| 🗺️ Live Map | Real-time canvas map showing all members, distances, and safe zone boundary |
| 📡 Fused Location | GPS → BLE proximity → Cell tower fallback chain for maximum reliability |
| 🔔 Safe Zone Alerts | Customisable radius (100m–1km); alerts fire on both sender and receiver |
| 🆘 SOS Navigation | Compass direction + distance to lost member + one-tap emergency call (1920) |
| 🌐 Multilingual Ready | English, Hindi, Tamil, Bengali — structure in place for expansion |
| 🔄 Real-time Sync | Socket.io WebSocket — location updates every 5–10 seconds with Haversine distance |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────┐
│                   Browser (Client)               │
│                                                  │
│  Register → Link Group → Live Tracking → SOS    │
│                                                  │
│  Location stack:                                 │
│  GPS (3–15m) → BLE zone → Cell fallback         │
│  All fused via browser FusedLocationProvider    │
└─────────────────────┬───────────────────────────┘
                      │  Socket.io (WebSocket)
                      │  emit: register, join_group,
                      │        location_update
                      │  on:   group_update
                      ▼
┌─────────────────────────────────────────────────┐
│              Node.js + Express Server            │
│                                                  │
│  In-memory user store                           │
│  Group room management                          │
│  Haversine distance computation                 │
│  Bearing + compass direction                    │
│  Broadcast personalised updates per member      │
└─────────────────────────────────────────────────┘
```

---

## 🚀 Quick Start

### Prerequisites
- [Node.js](https://nodejs.org) v18+ (LTS recommended)

### Run locally

```bash
# 1. Clone the repo
git clone https://github.com/YOUR_USERNAME/kumbh-safe.git
cd kumbh-safe

# 2. Install dependencies
npm install

# 3. Start the server
node server.js
```

Open **http://localhost:3000** in your browser.

### Test multi-user (the real demo)

1. Open `http://localhost:3000` in **Tab 1** → Register as Person A
2. Open `http://localhost:3000` in **Tab 2** → Register as Person B
3. Copy Tab 1's ID → paste into Tab 2's "Add member" → Add
4. Copy Tab 2's ID → paste into Tab 1's "Add member" → Add
5. Both hit **Start tracking** → watch them appear on each other's live map in real time

### Test on a phone (same WiFi)

```bash
# Find your laptop's local IP
ipconfig        # Windows
ifconfig        # Mac / Linux
```

Open `http://YOUR_IP:3000` on your phone. Allow location when prompted.

---

## 📁 Project Structure

```
kumbh-safe/
├── server.js          # Node.js backend — Socket.io, groups, distance logic
├── package.json       # Dependencies (express, socket.io)
└── public/
    └── index.html     # Full frontend — registration, live map, SOS screen
```

---

## 🛰️ Location Strategy

The app uses a three-layer fused location approach:

```
Priority 1 — GPS / GNSS
  Accuracy: 3–15m in open areas, 15–30m in dense crowds
  Used when: signal is strong (accuracy < 30m)

Priority 2 — GPS + Cell fusion
  Accuracy: 30–100m
  Used when: GPS degrades in crowd or under canopies

Priority 3 — Cell tower triangulation
  Accuracy: 100–300m
  Used when: GPS fails entirely (last resort)

Dead reckoning via accelerometer fills short gaps
BLE beacon infrastructure (future) will add 1–5m zone-level accuracy
```

For a 500m safe zone threshold, even the worst-case 30m GPS drift is well within tolerance.

---

## 🔧 Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Vanilla HTML/CSS/JS, Canvas API for map |
| Real-time | Socket.io (WebSocket with polling fallback) |
| Backend | Node.js + Express |
| Location | Browser Geolocation API (FusedLocationProvider) |
| Distance | Haversine formula (server-side) |
| Hosting | Runs locally; deployable to Railway / Render / any VPS |

---

## 📡 Socket.io Events

| Event | Direction | Payload |
|---|---|---|
| `register` | client → server | `{ id, name }` |
| `join_group` | client → server | `{ groupId }` |
| `location_update` | client → server | `{ lat, lng, accuracy, source }` |
| `group_update` | server → client | `{ members: [{id, name, lat, lng, distance, bearing, direction}] }` |

---

## 🗺️ Roadmap

- [ ] BLE beacon integration for indoor / dense-crowd accuracy
- [ ] Offline map tile caching (PWA) for low-connectivity zones
- [ ] Push notifications when app is backgrounded
- [ ] Check-in system — periodic "I'm okay" pings
- [ ] Admin dashboard for Kumbh Mela authorities
- [ ] Multilingual voice readout for SOS navigation
- [ ] Lost & Found integration with official mela helpdesk

---

## 🙏 Emergency Contact

The SOS button dials **1920** — the official Kumbh Mela emergency helpline.

---

## 👥 Team

Built as a Problem-Based Learning (PBL) project at **MIT School of Computing, MIT-ADT University, Pune** (Semester 6).

| Name | Role |
|---|---|
| Vedant | Developer & Project Lead |
| Payal Gavhane | Team Member |
| Prathmesh | Team Member |
| Dr. Monali Tingane | Faculty Guide |

---

## 📄 License

MIT License — free to use, modify, and deploy.

---

> Built with the belief that technology should protect people at scale — especially the most vulnerable ones in the biggest crowds.
