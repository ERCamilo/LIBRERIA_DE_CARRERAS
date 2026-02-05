Guía de Estructura de Datos para StudyTrack ProEsta guía detalla cómo crear los archivos JSON necesarios para agregar nuevas universidades y carreras a la plataforma StudyTrack. El sistema se basa en dos tipos de archivos:index.json: El catálogo o menú principal.carrera.json: El archivo individual con el pénsum de una carrera específica.1. El Archivo Maestro (index.json)Este archivo vive en la raíz del repositorio y le dice a la aplicación qué carreras están disponibles para mostrar en el menú de selección.EstructuraEs un Array de objetos, donde cada objeto representa una carrera disponible.[
  {
    "id": "uapa-soft-2018",
    "institution": "UAPA",
    "degree_type": "Grado",
    "career_name": "Ingeniería en Software",
    "path": "data/uapa/grado/software.json",
    "last_update": "2024-01-20"
  },
  {
    "id": "uasd-quim-2011",
    "institution": "UASD",
    "degree_type": "Grado",
    "career_name": "Licenciatura en Química",
    "path": "data/uasd/grado/quimica.json",
    "last_update": "2023-11-15"
  }
]
Definición de Camposid: Un identificador único (sin espacios). Se recomienda: universidad-carrera-año.institution: Nombre o siglas de la Universidad (ej. "UASD", "INTEC").degree_type: Nivel académico (ej. "Grado", "Postgrado", "Técnico", "Doctorado").career_name: Nombre completo de la carrera.path: La ruta relativa donde se guardará el archivo de la carrera dentro del repositorio.Estándar sugerido: data/{universidad}/{nivel}/{nombre_carrera}.json2. El Archivo de Carrera (carrera.json)Este archivo contiene toda la lógica, materias y reglas de una carrera.Estructura General{
  "id": "uapa-soft-2018",
  "metadata": { ... },
  "requirements": [ ... ],
  "periods": [ ... ]
}
A. Metadatos (metadata)Información general sobre el plan de estudios."metadata": {
  "institution": "UAPA",
  "career_name": "Ingeniería en Software",
  "degree": "Ingeniero/a en Software",
  "period_type": "Trimestre", 
  "total_credits": 209,
  "duration_years": 4
}
period_type: Puede ser "Semestre", "Trimestre", "Cuatrimestre", etc.B. Requisitos de Graduación (requirements)Lista de requisitos no académicos (que no son materias per se) necesarios para graduarse."requirements": [
  { "id": "req-1", "name": "Servicio Social (60h)", "completed": false },
  { "id": "req-2", "name": "Prueba de Inglés", "completed": false }
]
C. Periodos y Materias (periods)Es un array donde cada objeto es un periodo (Semestre 1, Semestre 2, etc.)."periods": [
  {
    "period_number": 1,
    "name": "Primer Semestre",
    "subjects": [
      {
        "id": "MAT-101",
        "code": "MAT-101",
        "name": "Matemática Básica",
        "credits": 4,
        "prerequisites": [] 
      }
    ]
  },
  {
    "period_number": 2,
    "name": "Segundo Semestre",
    "subjects": [
        { ... }
    ]
  }
]
3. Lógica de Prerrequisitos (¡Importante!)El campo prerequisites dentro de cada materia es el motor lógico de la aplicación. Es un Array de strings o arrays anidados.Casos de Uso:Sin Prerrequisitos: La materia se puede dar desde el inicio."prerequisites": []
Prerrequisito Simple: Debes haber pasado una materia específica."prerequisites": ["MAT-101"]
Múltiples Prerrequisitos (Lógica "Y"): Debes haber pasado TODAS las materias listadas."prerequisites": ["MAT-101", "FIS-101"]
Se lee: Aprobar MAT-101 Y FIS-101.Prerrequisitos Opcionales (Lógica "O"): Debes haber pasado AL MENOS UNA de las materias listadas. Se representa como un array dentro del array principal."prerequisites": [ ["QUI-101", "BIO-101"] ]
Se lee: Aprobar (QUI-101 O BIO-101).Combinación Compleja (Y + O):"prerequisites": ["MAT-101", ["FIS-101", "QUI-101"]]
Se lee: Aprobar MAT-101 Y TAMBIÉN (FIS-101 O QUI-101).Todo lo anterior ("ALL"): Usado para cursos finales o tesis. Requiere aprobar todas las materias anteriores del pénsum."prerequisites": ["ALL"]
4. Prompt para Generar JSON con IASi tienes un PDF de un pénsum, puedes copiar y pegar el siguiente texto en ChatGPT, Claude o Gemini adjuntando el PDF para que genere el JSON por ti.🤖 Prompt para la IA:"Actúa como un experto en estructuración de datos académicos. Tengo este PDF con el plan de estudios de una carrera universitaria. Necesito que extraigas la información y generes un archivo JSON válido siguiendo estrictamente este esquema:Estructura base:{ "id": "siglas-uni-carrera", "metadata": {...}, "requirements": [...], "periods": [...] }Detalles de Subjects (Materias):Cada materia debe tener: id (código único), code (clave visual), name (nombre), credits (número entero).Reglas para Prerrequisitos (prerequisites):Si no tiene, usa [].Si requiere materia A Y materia B, usa ["A", "B"].Si requiere materia A O materia B, usa un array anidado: [["A", "B"]].Si requiere materia A Y (materia B O materia C), usa: ["A", ["B", "C"]].Si requiere haber aprobado todo lo anterior (o dice 'Todas las asignaturas'), usa ["ALL"].Usa SIEMPRE los códigos (claves) de las materias como IDs, no los nombres.Requirements (Requisitos):Si el documento menciona requisitos de grado como Tesis, Pasantía, Inglés o Servicio Social, agrégalos en el array requirements con la estructura: { "id": "req-1", "name": "Nombre", "completed": false }.Por favor, genera el JSON completo para la carrera que aparece en el documento adjunto."5. Estructura de Carpetas RecomendadaPara mantener el repositorio de GitHub ordenado:/
├── index.json  (El menú principal)
├── data/
│   ├── uapa/
│   │   ├── grado/
│   │   │   └── software.json
│   │   │   └── mercadeo.json
│   │   └── postgrado/
│   ├── uasd/
│   │   └── grado/
│   │       └── quimica.json
│   └── intec/
│       └── grado/
