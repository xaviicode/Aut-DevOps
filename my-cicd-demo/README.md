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
