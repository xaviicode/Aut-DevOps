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

> [!NOTE]
> Este README está pensado para ser dinámico y crecer con tu proyecto. Modifica y adapta las secciones según tus necesidades y las de tu equipo[2][3][1].
```

[1] https://www.bacancytechnology.com/blog/salesforce-ci-cd-integration
[2] https://coderefinery.github.io/documentation/writing-readme-files/
[3] https://hackernoon.com/cicd-best-practices-for-salesforce-dx
[4] https://github.com/coalesceio/salesforce/blob/salesforce_dynamic_model/README.md
[5] https://github.com/scolladon/sfdx-git-delta/blob/main/README.md
[6] https://github.com/salto-io/salesforce-ci-cd-org-dev/blob/master/README.md
[7] https://dev.to/rohit19060/how-to-write-stunning-github-readme-md-template-provided-5b09
[8] https://abhisheksubbu.github.io/salesforce-azure-devops-with-github-source-control/
[9] https://gearset.com/blog/salesforce-cicd-best-practices/
[10] https://gearset.com/blog/cicd-for-salesforce-that-just-works-out-of-the-box/



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
   openssl req -new -x509 -key server.key -out server.crt -days 365
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
```
Este formato con pestañas desplegables (usando ``) hace el README muy claro, dinámico y fácil de navegar, siguiendo las mejores prácticas de documentación para proyectos Salesforce CI/CD[1][2][3][5][7][8].

[1] https://gitlab.com/gitlab-org/project-templates/salesforcedx/-/blob/main/README.md
[2] https://github.com/salto-io/salesforce-cicd-template
[3] https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_ci.htm
[4] https://github.com/SalesforceSFDC/Salesforce-Documentation/blob/master/readme.md
[5] https://www.minusculetechnologies.com/blogs/how-to-build-a-ci-cd-pipeline-for-salesforce
[6] https://www.apexhours.com/ci-cd-pipeline-using-gitlab-for-salesforce/
[7] https://github.com/iantendu/Salesforce-CI-CD-Template
[8] https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_ci_package_dev.htm
[9] https://www.salesforceben.com/build-your-own-ci-cd-pipeline-in-salesforce-using-github-actions/
[10] https://gitlab.com/sfdx/sfdx-cicd-template
[11] https://github.com/salto-io/salesforce-cicd-template/blob/main/README.md
[12] https://github.com/zoltan-nz/ci-cd-pipeline-template-for-data-projects/blob/master/README.md
[13] https://sfdx-hardis.cloudity.com/salesforce-ci-cd-work-on-task-configuration/
[14] https://trailhead.salesforce.com/content/learn/modules/b2c-build-processes-and-tests-for-technical-architects/b2c-automate-and-test-during-integration
[15] https://sfdx-hardis.cloudity.com/salesforce-ci-cd-home/
[16] https://trailhead.salesforce.com/pt-BR/content/learn/modules/b2c-build-processes-and-tests-for-technical-architects/b2c-automate-and-test-during-integration
[17] https://trailhead.salesforce.com/content/learn/modules/salesforce-devops-with-copado/let-innovation-flow-with-continuous-delivery
