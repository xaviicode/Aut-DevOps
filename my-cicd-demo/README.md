## Estructura del Proyecto

```
my-cicd-demo/
│
├── force-app/              # Código fuente Salesforce DX (Apex, LWC, etc.)
├── manifest/               # Archivos package.xml para recuperar/deployar metadata
├── .github/workflows/      # Workflows de GitHub Actions para CI/CD
│   └── deploy.yml
├── sfdx-project.json       # Configuración del proyecto SFDX
├── README.md               # Documentación principal
└── ...otros archivos
```

---

## Instalación y Configuración

1. **Clona el repositorio:**
   ```
   git clone https://github.com/tuusuario/tu-repo.git
   cd tu-repo
   ```

2. **Autentica tu Salesforce Developer Org:**
   ```
   sfdx auth:web:login -a devorg
   ```

3. **Recupera la metadata desde tu org:**
   ```
   sfdx force:source:retrieve -u devorg -x manifest/package.xml
   ```

4. **Inicializa el repositorio y haz tu primer commit:**
   ```
   git init
   git add .
   git commit -m "Primer commit: estructura inicial"
   git remote add origin 
   git push -u origin main
   ```

---

## Flujo de Trabajo con Ramas

- **main**: Rama principal, refleja el estado productivo.
- **develop**: Rama de integración para pruebas.
- **feature/nombre**: Rama para cada nueva funcionalidad.
- **hotfix/nombre**: Rama para correcciones urgentes.

**Ejemplo de creación de ramas:**
```
git checkout -b feature/mi-nueva-feature
# Realiza cambios...
git add .
git commit -m "Agrega nueva feature"
git checkout develop
git merge feature/mi-nueva-feature
git push origin develop
```

---

## Uso del Proyecto

1. **Desarrolla nuevas funcionalidades en ramas feature.**
2. **Haz merge a develop para pruebas.**
3. **Cuando esté listo, merge a main para producción.**
4. **El pipeline CI/CD se ejecuta automáticamente al hacer push en main.**

---

## Pipeline CI/CD

El pipeline automatiza los siguientes pasos:

- Validación de código y formato
- Despliegue automático a sandbox de pruebas
- Ejecución de pruebas Apex
- Reporte de resultados


Ver ejemplo de workflow YAML

```
name: Deploy to Salesforce Dev Org

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Salesforce CLI
        uses: amtrack/sfdx-actions@v1
      - name: Authenticate to Salesforce
        run: sfdx auth:jwt:grant --clientid ${{ secrets.SF_CLIENT_ID }} --jwtkeyfile assets/server.key --username ${{ secrets.SF_USERNAME }} --instanceurl https://login.salesforce.com
      - name: Deploy source to org
        run: sfdx force:source:deploy -p force-app -u ${{ secrets.SF_USERNAME }} --checkonly
```


---

## Buenas Prácticas

- Versiona todo el código y configuración relevante.
- Usa ramas para separar trabajo y evitar conflictos.
- Automatiza pruebas para asegurar calidad.
- Documenta cada cambio y proceso.
- Usa variables y secretos para credenciales sensibles.

---

## Preguntas Frecuentes


¿Qué archivos debo versionar?
Versiona todo lo que esté en `force-app/`, `manifest/`, y archivos de configuración. No subas credenciales ni carpetas temporales.



¿Cómo agrego una nueva funcionalidad?
Crea una rama desde develop, desarrolla, haz commit y mergea a develop cuando esté listo.



¿Cómo configuro los secretos del pipeline?
Entra a la sección “Secrets” de tu repositorio en GitHub y agrega las variables necesarias: `SF_CLIENT_ID`, `SF_USERNAME`, etc.


---

## Créditos y Licencia

Proyecto desarrollado por [Tu Nombre o Equipo].  
Licencia MIT.


---
---

```markdown
# 🚀 Salesforce CI/CD Pipeline con GitHub Actions

Este README documenta, de forma clara y dinámica, cómo configurar un pipeline de CI/CD para Salesforce usando Salesforce DX, Git y GitHub Actions. Incluye ejemplos, comandos y recomendaciones para que puedas replicar el proceso paso a paso.

---


1. Crear una Salesforce Developer Org de Prueba

- Regístrate gratis en [developer.salesforce.com/signup](https://developer.salesforce.com/signup).
- Usa un correo válido y elige un username único (formato email).
- El perfil por defecto es System Administrator, ideal para pruebas y automatización.



---


2. Preparar el Proyecto Salesforce DX

- Crea un nuevo proyecto Salesforce DX:
  ```
  sfdx force:project:create -n my-cicd-demo
  cd my-cicd-demo
  ```
- Autentica tu org de prueba:
  ```
  sfdx auth:web:login -a devorg
  ```
- Recupera la metadata con un package.xml básico:
  ```
  sfdx force:source:retrieve -u devorg -x manifest/package.xml
  ```
- Ejemplo de package.xml:
  ```
  
  
    
      *
      ApexClass
    
    
      *
      ApexTrigger
    
    
      *
      CustomObject
    
    59.0
  
  ```



---


3. Versionar el Código con Git

- Inicializa Git y haz el primer commit:
  ```
  git init
  git add .
  git commit -m "Primer commit: proyecto Salesforce DX inicial"
  ```
- Crea un repositorio en GitHub y enlázalo:
  ```
  git remote add origin 
  git push -u origin main
  ```



---


4. Estrategia de Ramas

- main: rama principal, refleja el estado productivo.
- feature/xxx: para nuevas funcionalidades o cambios.
- Ejemplo de flujo:
  ```
  git checkout -b feature/mi-cambio
  # ...haz cambios...
  git add .
  git commit -m "Agrego nueva funcionalidad"
  git checkout main
  git merge feature/mi-cambio
  git push origin main
  ```



---


5. Configurar Autenticación JWT para CI/CD

1. Genera claves:
   ```
openssl genrsa -out server.key 2048
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout server.key -out server.crt

Abre la terminal en la carpeta de tu proyecto (o en la carpeta donde deseas que se cree el archivo).

Ejecuta el siguiente comando para crear un certificado autofirmado y su clave privada:

Durante el proceso se te solicitarán algunos datos (país, organización, etc.). Estos datos se integrarán en el certificado.

Una vez completado el comando, confirma que el archivo se generó ejecutando:
   ```
2. Crea una Connected App en Salesforce:
   - Habilita OAuth y sube server.crt.
   - Agrega scopes: full y refresh_token, offline_access.
   - Anota el Consumer Key (Client ID).
3. Configura los secretos en GitHub:
   - SF_CLIENT_ID: Consumer Key.
   - SF_USERNAME: username de Salesforce.
   - SF_JWT_KEY: contenido de server.key.
   - SF_INSTANCE_URL: normalmente https://login.salesforce.com.



---


6. Pipeline CI/CD con GitHub Actions

Crea el archivo .github/workflows/deploy.yml en tu repo:

```
name: Deploy to Salesforce Dev Org

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Set up Salesforce CLI
        uses: amtrack/sfdx-actions@v1

      - name: Write server.key file
        run: echo "${{ secrets.SF_JWT_KEY }}" > assets/server.key

      - name: Authenticate to Salesforce (JWT)
        run: |
          sfdx auth:jwt:grant \
            --clientid ${{ secrets.SF_CLIENT_ID }} \
            --jwtkeyfile assets/server.key \
            --username ${{ secrets.SF_USERNAME }} \
            --instanceurl ${{ secrets.SF_INSTANCE_URL }} \
            --setdefaultusername

      - name: Deploy source to org (Check Only)
        run: sfdx force:source:deploy -p force-app -u ${{ secrets.SF_USERNAME }} --checkonly

      - name: Run Apex tests
        run: sfdx force:apex:test:run -u ${{ secrets.SF_USERNAME }} --resultformat human --wait 10
```

- El pipeline se ejecuta automáticamente al hacer push a main.
- Modifica --checkonly si deseas hacer un despliegue real.



---


7. Buenas Prácticas y Recursos

- Documenta cada paso en este README.
- Usa ramas para cada feature.
- Automatiza pruebas y validaciones antes de desplegar.
- Consulta la guía oficial de Salesforce DX para CI/CD[3].
- Explora ejemplos y plantillas en la comunidad Salesforce[1][2][7][8].

---
¿Dudas o sugerencias? ¡Abre un issue o contribuye con mejoras!



---
---

Aquí tienes de dónde sacar la información para cada valor que te pide GitHub Actions Secrets para tu pipeline de Salesforce CI/CD, y una referencia rápida de dónde encontrar cada uno:

1. SF_CLIENT_ID
¿Qué es?
El Consumer Key de tu Connected App en Salesforce.

¿Dónde lo obtienes?

Ve a Salesforce Setup > App Manager > busca tu Connected App > haz clic en "View" > copia el valor de Consumer Key.

2. SF_USERNAME
¿Qué es?
El usuario de Salesforce que usas para autenticarte (por ejemplo, tuusuario@empresa.com).

¿Dónde lo obtienes?

Es tu username de Salesforce, el mismo que usas para iniciar sesión en la plataforma y que asociaste a la Connected App.

3. SF_JWT_KEY
¿Qué es?
El contenido completo del archivo server.key (clave privada RSA que generaste).

¿Dónde lo obtienes?

Lo generaste con OpenSSL al crear el flujo JWT (el archivo suele estar en tu máquina local, por ejemplo en ~/.ssh/jwt/server.key).

Abre el archivo con un editor de texto, copia TODO su contenido (incluyendo las líneas de inicio y fin) y pégalo en el secret.

4. SF_INSTANCE_URL
¿Qué es?
La URL de login de Salesforce.

¿Dónde lo obtienes?

Usualmente es https://login.salesforce.com para la mayoría de las Developer Orgs y entornos de producción estándar.

Solo cambia si tu empresa usa un entorno de Salesforce Sandbox (https://test.salesforce.com).


git add .
git commit -m "Prueba de pipeline CI/CD"
git push origin main

O crea una rama y abre un Pull Request si tu flujo lo requiere.

3. **Verifica la ejecución en GitHub Actions**
- Ve a la pestaña **Actions** de tu repositorio en GitHub.
- Busca la ejecución más reciente del workflow.
- Haz clic para ver los detalles y logs de cada paso.

4. **Revisa los resultados**
- Si todo está correcto, los pasos aparecerán en verde (“success”).
- Si hay errores, revisa el mensaje y corrige según el log.

---

## 📋 Referencia rápida: ¿De dónde saco cada valor?

- **SF_CLIENT_ID:**  
`Setup > App Manager > [tu Connected App] > View > Consumer Key`

- **SF_USERNAME:**  
Usuario/login de Salesforce (ejemplo: `tuusuario@empresa.com`)

- **SF_JWT_KEY:**  
Contenido del archivo `server.key` generado con OpenSSL (copia todo, incluidas las líneas de inicio y fin)

- **SF_INSTANCE_URL:**  
Generalmente `https://login.salesforce.com`  
(Si usas sandbox: `https://test.salesforce.com`)

---

## 🌐 Enlace de referencia a tu org Salesforce

[Acceso a tu Salesforce Developer Org](https://xaviicode-dev-ed.develop.lightning.force.com/lightning/setup/SetupOneHome/home)

---

## 📝 Notas

- No subas nunca tus secrets ni archivos sensibles al repositorio.
- Puedes iterar y mejorar el pipeline agregando más pasos de pruebas, validaciones o despliegues a otros entornos.
- Si tienes errores en el pipeline, revisa los logs detallados en la pestaña **Actions** y ajusta la configuración según sea necesario.

---

¡Listo! Ahora puedes empezar a trabajar y validar tu pipeline de CI/CD en Salesforce con GitHub Actions.


