# AI Canvas Calculator

A project where users can draw mathematical expressions on a canvas, and the application evaluates the expressions and displays the results in real time using FastAPI and Google Gemini API for backend processing.

## Table of Contents
1. [Project Overview](#project-overview)
2. [Setup Guide](#setup-guide)
   - [Frontend Setup](#frontend-setup)
   - [Backend Setup](#backend-setup)
3. [Code Structure and Workflow](#code-structure-and-workflow)
   - [Frontend Explanation](#frontend-explanation)
   - [Backend Explanation](#backend-explanation)
4. [Core Functionalities](#core-functionalities)

---

## Project Overview

This application provides a dynamic canvas where users can draw mathematical expressions. The project has a React frontend that allows drawing and selecting colors and a FastAPI backend that processes images of drawn expressions, computes results, and returns them in LaTeX format to be displayed on the canvas.

## Setup Guide

### Frontend Setup
1. **Install Node.js** (recommended version 16+).
2. **Clone the repository** and navigate to the frontend directory:
   ```bash
   cd frontend
   ```
3. **Install dependencies**:
   ```bash
   npm install
   ```
4. **Run the development server**:
   ```bash
   npm start
   ```

### Backend Setup
1. **Install Python** (recommended version 3.8+).
2. **Navigate to the backend directory**:
   ```bash
   cd backend
   ```
3. **Create and activate a virtual environment**:
   ```bash
   python -m venv env
   source env/bin/activate  # On Windows, use 'env\Scripts\activate'
   ```
4. **Install dependencies**:
   ```bash
   pip install -r requirements.txt
   ```
5. **Set up environment variables**:  
   In the `.env` file, specify:
   ```env
   GEMINI_API_KEY=your_api_key_here
   ```
6. **Run the FastAPI server**:
   ```bash
   uvicorn main:app --reload
   ```

---

## Code Structure and Workflow

### Frontend Explanation

The frontend, built with React and TypeScript, renders the canvas and handles user interactions, such as drawing and sending data to the backend.

#### Key Files and Code

1. **`App.tsx`**  
   This file sets up the main application, including routing and theming.
   ```typescript
   const paths = [
       {
           path: '/',
           element: <Home />,
       },
   ];
   const BrowserRouter = createBrowserRouter(paths);
   ...
   ```

2. **`Home.tsx`**
   - Contains the core drawing and API interaction logic.
   - Sets up the canvas and functions like `startDrawing`, `draw`, and `stopDrawing` for user input on the canvas.

3. **`runRoute` function** (Home component)  
   This function sends the current canvas drawing to the backend as a base64 image for processing.
   ```typescript
   const runRoute = async () => {
       const response = await axios({
           method: 'post',
           url: `${import.meta.env.VITE_API_URL}/calculate`,
           data: {
               image: canvas.toDataURL('image/png'),
               dict_of_vars: dictOfVars
           }
       });
       ...
   };
   ```

4. **Utility Files**
   - `utils.ts`: Contains helper functions for managing class names using `clsx` and `tailwind-merge`.

### Backend Explanation

The backend, created with FastAPI, processes the base64 image from the frontend to detect and evaluate mathematical expressions. The response is sent back as LaTeX formatted text, allowing the frontend to render the results dynamically.

#### Key Files and Code

1. **`main.py`**
   - Sets up the FastAPI app, CORS configuration, and includes the router from `route.py`.
   ```python
   app.add_middleware(
       CORSMiddleware,
       allow_origins=['*'],
       allow_credentials=True,
       allow_methods=["*"],
       allow_headers=["*"],
   )
   ...
   ```

2. **`constants.py`**
   - Loads environment variables, specifically the `GEMINI_API_KEY`.
   ```python
   load_dotenv()
   GEMINI_API_KEY = os.getenv('GEMINI_API_KEY')
   ```

3. **`route.py`**
   - Defines the main API route `/calculate`, which accepts the image and evaluates the expression.
   ```python
   @router.post('/calculate')
   async def calculate(data: ImageData):
       ...
   ```

4. **`utils.py`**
   - `analyze_image` function: Uses the Google Gemini API to process the mathematical expression within the image and return the computed result.
   ```python
   def analyze_image(image_data: str) -> dict:
       ...
   ```

5. **`schema.py`**
   - Defines the Pydantic model `ImageData` for request validation.
   ```python
   class ImageData(BaseModel):
       image: str
       dict_of_vars: dict
   ```

---

## Core Functionalities

### Drawing on the Canvas
- Users can draw on the canvas, and the drawing is recorded in real time.
- Users can choose different colors and reset the canvas if needed.

### Sending Drawn Data to Backend
- The `runRoute` function in `Home.tsx` sends the drawn image to the backend as a base64 string.

### Backend Processing
- The backend receives the base64 image and sends it to the Google Gemini API.
- The API response is parsed to identify the expression and calculate the result.

### Displaying Results
- The backend returns the result in LaTeX format, which is rendered on the canvas using MathJax.

---
