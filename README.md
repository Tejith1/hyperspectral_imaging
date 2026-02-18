<div align="center">

# 🌈 Hyperspectral Image Classifier

**AI-powered hyperspectral image classification — upload, classify, and visualize land-cover predictions in seconds.**

![React](https://img.shields.io/badge/React-19-61DAFB?style=for-the-badge&logo=react&logoColor=white)
![Vite](https://img.shields.io/badge/Vite-6-646CFF?style=for-the-badge&logo=vite&logoColor=white)
![Express](https://img.shields.io/badge/Express-4-000000?style=for-the-badge&logo=express&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-3-000000?style=for-the-badge&logo=flask&logoColor=white)
![TensorFlow](https://img.shields.io/badge/TensorFlow-2.19-FF6F00?style=for-the-badge&logo=tensorflow&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-Atlas-47A248?style=for-the-badge&logo=mongodb&logoColor=white)

</div>

---

## 📌 About the Project

**Hyperspectral Image Classifier** is a full-stack web application for classifying hyperspectral satellite/aerial images using a CNN-based deep learning model. Users can sign up, upload an image, and instantly receive a **colorized prediction map** showing detected land-cover classes.

### Use Cases

- 🌾 **Precision Agriculture** — crop health and soil analysis
- 🌍 **Environmental Monitoring** — land-use and land-cover mapping
- 🛰️ **Remote Sensing** — satellite image analysis
- 🏭 **Industrial Quality Control** — material classification
- 🤖 **AI-Driven Image Analysis** — research and prototyping

---

## 🏗️ Architecture

The project follows a **three-service architecture**:

```
┌──────────────────────────────────────────────────────────────┐
│                       BROWSER (React)                        │
│   Login / Signup ─────────────► Image Upload & Results       │
└──────────┬───────────────────────────────┬───────────────────┘
           │  Auth requests                │  Classification
           ▼                               ▼
┌─────────────────────┐         ┌─────────────────────────────┐
│  Express Auth API   │         │   Flask Inference Service    │
│  :5001              │         │   :5000                      │
│  ┌───────────────┐  │         │  ┌────────────────────────┐  │
│  │ JWT + bcrypt   │  │         │  │ CNN Model (model.pkl)  │  │
│  └───────┬───────┘  │         │  └────────────────────────┘  │
│          │          │         │         │                     │
│          ▼          │         │         ▼                     │
│  ┌───────────────┐  │         │  ┌────────────────────────┐  │
│  │   MongoDB     │  │         │  │ Colorized Output Image │  │
│  └───────────────┘  │         │  └────────────────────────┘  │
└─────────────────────┘         └─────────────────────────────┘
```

---

## 📂 Project Structure

```
hyperspectral_imaging/
│
├── frontend/                 # React + Vite UI
│   ├── src/
│   │   ├── App.jsx           # Router setup
│   │   ├── main.jsx          # Entry point
│   │   └── components/
│   │       ├── login.jsx     # Sign in / Sign up (dual panel)
│   │       ├── home.jsx      # Landing page with navigation
│   │       ├── detect.jsx    # Image upload & classification
│   │       ├── profile.jsx   # User profile & logout
│   │       └── aboutus.jsx   # About Us page
│   └── package.json
│
├── Backend/                  # Express Auth API
│   └── src/
│       ├── server.js         # Express app & routes
│       ├── controller/
│       │   ├── auth.js       # signup, login, logout, checkAuth
│       │   └── tokengen.js   # JWT generation & route guard
│       └── lib/
│           └── db.js         # Mongoose connection & User model
│
└── python_backend/           # Flask Inference Service
    ├── app.py                # Flask app & /classify endpoint
    ├── model.pkl             # Pickled CNN model (not in repo)
    ├── requirements.txt      # Python dependencies
    ├── templates/
    │   └── index.html        # Standalone upload UI
    └── static/
        ├── uploads/          # Uploaded images
        └── output/           # Classification result images
```

---

## ✨ Features

| Feature | Description |
|---|---|
| 🔐 **Authentication** | Signup, login, logout with passwords hashed via bcrypt |
| 🍪 **JWT Cookies** | Auth tokens stored in HTTP-only cookies (7-day expiry) |
| 🛡️ **Protected Routes** | Middleware verifies JWT before serving guarded endpoints |
| 📤 **Image Upload** | Drag-and-drop or file picker on the Detect page |
| 🧠 **CNN Classification** | Patch-based prediction using a TensorFlow/Keras model |
| 🎨 **Colorized Output** | Predicted classes mapped to a 10-color RGB palette |
| 👤 **User Profile** | View name, email, and logout from a dedicated page |

---

## 🚀 Getting Started

### Prerequisites

| Tool | Version |
|---|---|
| Node.js | 18+ |
| Python | 3.10+ |
| MongoDB | Local or [Atlas](https://www.mongodb.com/atlas) |

### 1️⃣ Frontend (React + Vite)

```bash
cd frontend
npm install
npm run dev
```

> Runs at **http://localhost:5173**

### 2️⃣ Auth Backend (Express)

```bash
cd Backend
npm install
npm run start
```

> Runs at **http://localhost:5001**

### 3️⃣ Python Inference Service (Flask)

```bash
cd python_backend
python -m venv .venv
.venv\Scripts\activate        # Windows
# source .venv/bin/activate   # macOS / Linux
pip install -r requirements.txt
python app.py
```

> Runs at **http://localhost:5000**

> **Note:** Place your trained `model.pkl` file inside `python_backend/` before starting the Flask service.

---

## 🔌 API Reference

### Express Auth API — `http://localhost:5001`

| Method | Endpoint | Body | Description |
|---|---|---|---|
| `POST` | `/signup` | `{ fullName, email, password }` | Create a new account. Password must be ≥ 6 chars. |
| `POST` | `/login` | `{ email, password }` | Authenticate and receive a JWT cookie. |
| `POST` | `/logout` | — | Clear the JWT cookie. |
| `GET` | `/checkAuth` | — | 🔒 Returns the authenticated user's profile. |

### Flask Inference API — `http://localhost:5000`

| Method | Endpoint | Params | Description |
|---|---|---|---|
| `GET` | `/` | — | Serves a standalone upload UI. |
| `POST` | `/classify` | `image` (form file) | Classify an image and return prediction paths. |

<details>
<summary><strong>Classification pipeline (what happens on <code>POST /classify</code>)</strong></summary>

1. **Upload** — Image is saved to `static/uploads/`.
2. **Normalize** — Convert to float32 and scale pixel values to `[0, 1]`.
3. **Channel tiling** — If the image has 3 channels, tile to 10 channels to match model input.
4. **Patch extraction** — Slide a 5×5 window across the image; each patch becomes one sample.
5. **Prediction** — Feed all patches to the CNN model; take `argmax` of output probabilities.
6. **Reconstruction** — Map predictions back to pixel positions to form a full label image.
7. **Colorization** — Convert integer labels to an RGB image using a fixed 10-class color palette.
8. **Save** — Output saved to `static/output/classified_<name>.png`.

**Color palette:**

| Label | Color | Label | Color |
|---|---|---|---|
| 0 | ⬛ Black (background) | 5 | 🟣 Purple |
| 1 | 🔵 Blue | 6 | 🟤 Brown |
| 2 | 🟢 Green | 7 | 🩷 Pink |
| 3 | 🟡 Yellow | 8 | ⚫ Gray |
| 4 | 🟠 Orange | 9 | 🩵 Cyan |

</details>

---

## 🗺️ Frontend Pages

| Route | Page | Description |
|---|---|---|
| `/` `/login` | **Login** | Dual-panel sign-in / sign-up form |
| `/Home` | **Home** | Landing page with navigation to Detect, Profile, About |
| `/Detect` | **Detect** | Upload an image and view classification results side-by-side |
| `/Profilepage` | **Profile** | View user info (name, email) and logout |
| `/AboutUs` | **About Us** | Mission, use cases, and technology overview |

---

## ⚙️ Configuration

| Setting | Location | Default |
|---|---|---|
| Express port | `Backend/src/server.js` | `5001` |
| Flask port | `python_backend/app.py` | `5000` |
| CORS origin | `Backend/src/server.js` | `http://localhost:5173` |
| JWT secret | `Backend/src/controller/tokengen.js` | `mysecretkey` |
| JWT expiry | `Backend/src/controller/tokengen.js` | `7 days` |
| MongoDB URI | `Backend/src/lib/db.js` | Atlas cluster |
| Model file | `python_backend/` | `model.pkl` |
| Upload dir | `python_backend/static/` | `uploads/` |
| Output dir | `python_backend/static/` | `output/` |

> ⚠️ **Security:** The JWT secret and MongoDB URI are hardcoded. For production, move them to environment variables.

---

## 🛠️ Tech Stack

<table>
  <tr>
    <th>Layer</th>
    <th>Technology</th>
  </tr>
  <tr>
    <td><strong>Frontend</strong></td>
    <td>React 19 · Vite 6 · React Router 7 · Tailwind CSS 4 · Axios</td>
  </tr>
  <tr>
    <td><strong>Auth API</strong></td>
    <td>Express 4 · Mongoose 8 · bcryptjs · jsonwebtoken · cookie-parser</td>
  </tr>
  <tr>
    <td><strong>ML Service</strong></td>
    <td>Flask 3 · TensorFlow 2.19 · NumPy · Pillow · Matplotlib · OpenCV</td>
  </tr>
  <tr>
    <td><strong>Database</strong></td>
    <td>MongoDB Atlas</td>
  </tr>
</table>

---

## 📜 Available Scripts

### Frontend

```bash
npm run dev       # Start dev server with HMR
npm run build     # Production build
npm run preview   # Preview production build
npm run lint      # Run ESLint
```

### Backend

```bash
npm run start     # Start Express server with nodemon
```

### Python Backend

```bash
python app.py     # Start Flask in debug mode
```

---

## 🐛 Troubleshooting

| Problem | Solution |
|---|---|
| Flask crashes on startup | Ensure `model.pkl` exists in `python_backend/` |
| Auth requests fail | Verify MongoDB is running and the connection string is valid |
| CORS errors in browser | Check that the frontend origin matches the Express CORS config |
| Classification returns empty output | Confirm the uploaded image is large enough (≥ 5×5 px) |
| JWT cookie not sent | Ensure `credentials: "include"` is set in fetch requests |

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/my-feature`)
3. Commit changes (`git commit -m "Add my feature"`)
4. Push to the branch (`git push origin feature/my-feature`)
5. Open a Pull Request

---

## 📄 License

Distributed under the **MIT License**. See `LICENSE` for details.

---

<div align="center">

**Built with ❤️ using React, Express, Flask & TensorFlow**

</div>
