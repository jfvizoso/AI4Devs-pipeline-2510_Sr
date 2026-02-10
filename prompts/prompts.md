# Ejercicio: Pipeline GitHub Actions (tests, build y deploy a EC2)

## Enunciado

Tu misión en este ejercicio es crear un pipeline en GitHub Actions que, tras el trigger **"push a una rama con un Pull Request abierto"**, siga los siguientes pasos:

- Pase unos tests de backend.
- Genere un build del backend.
- Despliegue el backend en un EC2. Tienes un ejemplo de despliegue aquí https://lightrains.com/blogs/deploy-aws-ec2-using-github-actions/
- Utiliza los mismos nombres de secretos que en el ejemplo 

Para ello:

- Configurar el workflow de GitHub Actions en `.github/workflows/pipeline.yml`.
- Documentar los prompts utilizados para generar cada paso del pipeline (tests, build, despliegue).
- Asegurarse de que el pipeline se dispare con un push a una rama con un Pull Request abierto.
- En la respuesta, asegúrate de incluir qué secretos hay que crear en github y como

---

## Solución

### Workflow

El pipeline está definido en `.github/workflows/pipeline.yml`. Se dispara en cada **pull_request** (apertura, push a la rama del PR o reapertura), lo que cumple el requisito de “push a una rama con un Pull Request abierto”.

### Prompts utilizados para cada paso

- **Tests backend**  
  *“Añade un paso en el workflow que instale dependencias del backend con npm ci en backend/, ejecute prisma generate y luego npm test en backend/ para pasar los tests de Jest.”*

- **Build backend**  
  *“Añade un paso que ejecute el build del backend con npm run build en backend/ (TypeScript compila a dist/).”*

- **Despliegue a EC2**  
  *“Añade un paso de despliegue a EC2 usando la acción easingthemes/ssh-deploy, con los mismos nombres de secretos que en el ejemplo de lightrains: EC2_SSH_KEY, HOST_DNS, USERNAME, TARGET_DIR (mapeados a SSH_PRIVATE_KEY, REMOTE_HOST, REMOTE_USER, TARGET del action).”*

### Secretos que hay que crear en GitHub

Hay que definir estos **repository secrets** en el repositorio de GitHub (mismos nombres que en el ejemplo):

| Secreto       | Descripción |
|---------------|-------------|
| `EC2_SSH_KEY` | Contenido del archivo `.pem` que usas para conectarte por SSH a la instancia EC2. |
| `HOST_DNS`    | DNS público de la instancia EC2 (ej: `ec2-xx-xxx-xxx-xxx.us-west-2.compute.amazonaws.com`). |
| `USERNAME`    | Usuario SSH de la instancia (habitualmente `ubuntu` en Amazon Linux 2 / Ubuntu). |
| `TARGET_DIR`  | Ruta absoluta en el servidor donde desplegar el código (ej: `/home/ubuntu/app`). |

**Cómo crear los secretos:**

1. En GitHub, abre el repositorio.
2. Ve a **Settings** → **Secrets and variables** → **Actions**.
3. Pulsa **New repository secret**.
4. Introduce **Name** (por ejemplo `EC2_SSH_KEY`) y **Secret** (el valor; en `EC2_SSH_KEY` pega todo el contenido del `.pem`).
5. Repite para `HOST_DNS`, `USERNAME` y `TARGET_DIR`.

**Nota:** El pipeline hace checkout, tests, build y luego despliega todo el repositorio (incluido `backend/` con `dist/`) al `TARGET_DIR` del EC2. En el servidor puedes configurar un servicio (systemd, PM2, etc.) que ejecute desde esa ruta `npm ci`, `npx prisma generate`, `npm run build` (opcional si ya despliegas `dist/`) y `npm start` (o el comando que uses en producción).


---
