# Desafío #10: Instalación y Configuración de ArgoCD en Kubernetes

## Introducción

En este repositorio, se documentan los pasos realizados para cumplir con el desafío de instalar y configurar ArgoCD en un entorno Kubernetes utilizando Minikube. Este proyecto tiene como objetivo implementar un flujo de despliegue continuo basado en la filosofía GitOps, gestionando aplicaciones de forma declarativa desde un repositorio Git.

## Requisitos Previos

Antes de comenzar, asegúrate de contar con lo siguiente:

- [Minikube](https://minikube.sigs.k8s.io/docs/start/) instalado y funcionando.
- [Kubectl](https://kubernetes.io/docs/tasks/tools/) configurado y accesible desde la línea de comandos.
- Un repositorio Git en GitHub con una aplicación de ejemplo basada en [argocd-example-apps](https://github.com/argoproj/argocd-example-apps).

## Pasos Realizados

### 1. Instalación de Minikube y Configuración del Entorno

1. Instalé Minikube en mi máquina local siguiendo las instrucciones oficiales.
2. Inicié el clúster con:
   ```bash
   minikube start
   ```
3. Verifiqué la conexión al clúster:
   ```bash
   kubectl cluster-info
   ```

### 2. Instalación de ArgoCD

1. Creé un namespace para ArgoCD:
   ```bash
   kubectl create namespace argocd
   ```
2. Instalé ArgoCD aplicando los manifiestos oficiales:
   ```bash
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   ```
3. Configuré el acceso al dashboard exponiendo el servicio `argocd-server`:
   ```bash
   kubectl port-forward svc/argocd-server -n argocd 8080:443
   ```
   Accedí al dashboard en: [https://localhost:8080](https://localhost:8080).

### 3. Configuración del Repositorio Git

1. Creé un repositorio en GitHub y subí la aplicación de ejemplo obtenida desde mi repositorio personal: [portafolio](https://github.com/nescalan/nelson-portfolio.git).
2. Agregué el repositorio a ArgoCD utilizando el CLI:
   ```bash
   argocd repo add https://github.com/mi-usuario/mi-repo --username MI_USUARIO --password MI_PASSWORD
   ```

### 4. Creación de la Aplicación Gestionada

1. Redacté un manifiesto para la aplicación (`app.yaml`):
   ```yaml
   apiVersion: argoproj.io/v1alpha1
   kind: Application
   metadata:
     name: example-app
     namespace: argocd
   spec:
     project: default
     source:
       repoURL: "https://github.com/nescalan/nelson-portfolio.git"
       targetRevision: HEAD
       path: "path/de/mi/aplicacion"
     destination:
       server: "https://kubernetes.default.svc"
       namespace: default
     syncPolicy:
       automated:
         prune: true
         selfHeal: true
   ```
2. Apliqué el manifiesto:
   ```bash
   kubectl apply -f app.yaml -n argocd
   ```

### 5. Verificación y Evidencias

1. Verifiqué que la aplicación se sincronizara correctamente en el dashboard de ArgoCD.
2. Realicé capturas de pantalla del dashboard mostrando el despliegue exitoso.

## Diagrama de Alto Nivel

El siguiente diagrama ilustra el flujo de trabajo:

1. **Repositorio Git**: Almacena la configuración declarativa de la aplicación.
2. **ArgoCD**: Monitorea el repositorio y sincroniza los cambios en el clúster de Kubernetes.
3. **Kubernetes**: Aloja y gestiona la aplicación desplegada.
4. **Aplicacion**: Aplicación desplegada

```
Git --> ArgoCD --> Kubernetes --> Aplicación
```

## Conclusión

Este desafío permitió entender cómo integrar ArgoCD en un entorno Kubernetes, configurar repositorios Git para despliegues automatizados y gestionar aplicaciones de manera declarativa. La implementación fue exitosa y se lograron todos los objetivos planteados.

---

### Referencias

- [Minikube Documentation](https://minikube.sigs.k8s.io/docs/start/)
- [Kubectl Installation](https://kubernetes.io/docs/tasks/tools/)
- [ArgoCD Getting Started](https://argo-cd.readthedocs.io/en/stable/getting_started/)
- [ArgoCD Example Apps](https://github.com/argoproj/argocd-example-apps)
