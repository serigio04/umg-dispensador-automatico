### Diagrama de Flujo del Sistema

```mermaid
graph TD
    %% Inicialización
    A([Encendido del Arduino R4]) --> B[Setup: Inicializar I/O y Matriz LED]
    B --> C[Recuperar Índice de Memoria EEPROM]
    C --> D[Conectar Wi-Fi y Sincronizar Reloj NTP]
    D --> E[Levantar Servidor Web Local]
    
    %% Bucle Principal
    E --> F{Bucle Principal<br>Task Scheduler}
    
    %% Rama 1: Servidor Web
    F -->|Petición de Usuario| G{¿Ruta Web?}
    G -->|/ (Dashboard)| H[Leer Temperatura Actual]
    H --> I[Leer Historial de EEPROM]
    I --> J[Renderizar Interfaz HTML/JSON]
    J --> F
    G -->|/feed (Manual)| K[Forzar Alimentación]
    K --> L
    
    %% Rama 2: Control Autónomo
    F -->|Evaluación de Tiempo| M{¿Es la hora programada?}
    M -->|No| F
    M -->|Sí| L[Leer Sensor DS18B20]
    
    %% Lógica de Seguridad Térmica
    L --> N{¿Temperatura >= 20°C?}
    
    N -->|Sí| O[Mover Servomotor 90°]
    O --> P[Matriz LED: Mostrar ✔️]
    P --> Q[Variable Estado = Éxito]
    
    N -->|No| R[Bloquear Servomotor]
    R --> S[Matriz LED: Mostrar ❌]
    S --> T[Variable Estado = Falla]
    
    %% Data Logger y Buffer Circular
    Q --> U[Empaquetar Struct: Timestamp, Temp, Estado]
    T --> U
    U --> V[Escribir en EEPROM]
    V --> W[Rotar Índice: índice = índice + 1 % MAX]
    W --> X[Guardar nuevo Índice en EEPROM]
    X --> F
```