# Sistema de Gestión de Pacientes

## Descripción
Aplicación web para gestión de pacientes con Vue.js que permite CRUD completo con validaciones, búsqueda y notificaciones.

## Requisitos
- Node.js v14+
- npm v6+ o yarn

## Instalación
1. Clonar repositorio:
git clone https://github.com/xxskaxx1/gestion-pacientes.git
cd pacientes

2. Instalar dependencias:
npm install

3. Configurar entorno (opcional):
Crear .env con:
VUE_APP_API_URL=http://localhost:3000

4. Ejecutar:
npm run serve

## Comandos útiles
Desarrollo: npm run serve
Build producción: npm run build
Tests: npm run test:unit
Linter: npm run lint

## Estructura principal
src/
├── components/       # Componentes reutilizables
├── router/           # Configuración de rutas  
├── store/            # Estado global (Vuex)
├── views/            # Vistas principales
├── App.vue           # Componente raíz
└── main.js           # Entrada principal

## Características
✔ CRUD completo de pacientes
✔ Validación en tiempo real
✔ Búsqueda/filtrado avanzado
✔ Diseño responsive
✔ Persistencia con localStorage
✔ Sistema de notificaciones
