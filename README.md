# MLOps Pipeline Deployment — Predicción de Costos de Seguro Médico

Proyecto de despliegue end-to-end de un pipeline de Machine Learning: desde un modelo entrenado con **PyCaret** hasta una aplicación web funcional, containerizada con **Docker**, que predice el costo anual estimado de un seguro médico a partir de datos demográficos y de salud del paciente.

## Descripción

Este repositorio contiene una aplicación web construida con **Flask** que carga un modelo de regresión previamente entrenado (pipeline de PyCaret) y expone un formulario donde el usuario ingresa información del paciente (edad, sexo, IMC, número de hijos, si es fumador y región) para obtener una predicción del costo anual del seguro médico.

## Cómo funciona (arquitectura)

```
Usuario → Formulario Web (HTML/CSS) → Flask (app.py) → Pipeline PyCaret (.pkl) → Predicción → Usuario
```


1. **Modelo (`deployment_20260516.pkl`)**: un pipeline de regresión lineal entrenado con PyCaret sobre el dataset de seguros médicos (`insurance`), que incluye pasos automáticos de normalización, codificación de variables categóricas, generación de características polinomiales e interacciones entre variables.
2. **Backend (`app.py`)**: aplicación Flask que carga el pipeline con `load_model()`, recibe los datos del formulario, los transforma en un DataFrame y llama a `predict_model()` para generar la predicción.
3. **Frontend (`templates/`, `static/`)**: formulario HTML donde el usuario ingresa los datos.
4. **Contenedor (`Dockerfile`)**: empaqueta la aplicación, sus dependencias (`requirements.txt`) y el modelo en una imagen Docker que expone el puerto `5000`.

## Requisitos previos

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) instalado y en ejecución.
- Git.

##  Cómo ejecutar el proyecto localmente

1. Clona este repositorio:
```bash
   git clone https://github.com/migue2818/mlops-pipeline-deployment.git
   cd mlops-pipeline-deployment
```

2. Construye la imagen de Docker:
```bash
   docker build -t mlops-pipeline-deployment:latest .
```

3. Ejecuta el contenedor:
```bash
   docker run -d -p 5000:5000 mlops-pipeline-deployment:latest
```

4. Abre tu navegador en: http://localhost:5000


5. Completa el formulario y haz clic en **"Predict Bill"** para obtener la predicción.

##  Nota importante / limitación conocida

Los campos categóricos (**Sex**, **Smoker**, **Region**) son **sensibles a mayúsculas y minúsculas**. El modelo fue entrenado con los valores en minúsculas exactas del dataset original. Se deben ingresar así:

| Campo    | Valores válidos                                       |
|----------|--------------------------------------------------------|
| Sex      | `male`, `female`                                       |
| Smoker   | `yes`, `no`                                             |
| Region   | `northeast`, `northwest`, `southeast`, `southwest`      |

Si se ingresan con mayúsculas (ej. `Female`, `Yes`, `SouthWest`), el pipeline no reconoce la categoría correctamente y genera predicciones erróneas y desproporcionadamente altas, debido a que el pipeline incluye generación de características polinomiales e interacciones entre variables.

## Estructura del repositorio

```
mlops-pipeline-deployment/
├── app.py                          # Backend Flask: carga el modelo y expone las rutas
├── deployment_20260516.pkl         # Pipeline de PyCaret entrenado (modelo + preprocesamiento)
├── Dockerfile                      # Instrucciones para construir la imagen del contenedor
├── requirements.txt                # Dependencias de Python
├── Insurance - Model Training Notebook.ipynb   # Notebook con el entrenamiento del modelo
├── templates/                      # HTML del frontend
├── static/                         # Archivos estáticos (CSS, imágenes)
└── README.md                       # Este archivo
```


## Tecnologías usadas

- **PyCaret** — entrenamiento y gestión del pipeline de ML
- **Flask** — backend / API web
- **Docker** — containerización
- **Python 3.11**