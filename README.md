# Innovatech - Microservicio Despachos

API REST en Spring Boot para gestión de despachos, parte del sistema de Innovatech Chile. Corre en AWS ECS Fargate, puerto 8081, conectado a MariaDB.

## Arquitectura
- **Contenedor**: imagen Docker desde este repo, en Amazon ECR (`innovatech-despachos`)
- **Orquestación**: ECS Fargate, cluster `innovatech-cluster-v2`, servicio `svc-despachos`
- **Red**: subred privada, ALB interno (`alb-despachos-interno`) y ALB público (`alb-despachos-publico`)
- **Base de datos**: MariaDB en EC2 (`despachos_db`)
- **Autoscaling**: Target Tracking CPU 50%, mín 1 / máx 3

## Variables de entorno
| Variable | Descripción |
|---|---|
| DB_ENDPOINT | IP privada EC2 con MariaDB |
| DB_PORT | 3306 |
| DB_NAME | despachos_db |
| DB_USERNAME | Usuario DB |
| DB_PASSWORD | Contraseña DB |

## Cómo ejecutar localmente
```bash
./mvnw clean package -DskipTests
docker build -t innovatech-despachos .
docker run -p 8081:8081 --env-file .env innovatech-despachos
```

## Pipeline CI/CD
Push a `main` → `.github/workflows/deploy.yml`: build → push a ECR → force deploy en `svc-despachos`. Secrets AWS gestionados en GitHub (temporales, AWS Academy).

## Endpoints principales
- `GET /api/v1/despachos`
- `POST /api/v1/despachos`
- Comunicación con backend Ventas vía `idCompra` para generar despacho de una venta existente
