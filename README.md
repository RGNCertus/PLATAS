# Documento de arquitectura de software — PLATAS

**Versión:** 4.0
**Tipo de sistema actual:** Plataforma web de Salud
**Integrantes:** Rodrigo Jean Pierre Grimaldo Napán y Yemer Raul Cori Santiago

**Fecha:** Lunes 12 de octubre del 2026

## 1. Propósito
PLATAS busca apoyar el seguimiento nutricional conectando dos perfiles: paciente y nutricionista. El paciente consulta indicaciones y registra hábitos; el nutricionista revisa pacientes y gestiona planes. La interfaz actual sirve como prototipo navegable para validar el flujo de uso.

## 2. Estado del sistema
El proyecto contiene `index.html`, `paciente.html`, `nutricionista.html`, `styles.css` y `script.js`. Donde la interfaz usa JavaScript en el navegador.

## 3. Requisitos del MVP
### Funcionales
1. Permitir acceder a un panel según el rol seleccionado/registrado.
2. Mostrar un panel de paciente con plan y seguimiento de ejemplo.
3. Mostrar un panel de nutricionista con funciones de gestión de ejemplo.
4. Permitir registrar una comida desde la interfaz.
5. Permitir enviar mensajes de demostración.
6. Proporcionar navegación y salida de la sesión de demostración.

### No funcionales
- Código organizado y mantenible.
- No usar datos reales de pacientes en el prototipo.
- Documentar limitaciones de seguridad y persistencia.

## 4. Arquitectura actual
La arquitectura implementada es principalmente monolítica del lado del cliente:

```text
Navegador
 ├── index.html (acceso/registro)
 ├── paciente.html (interfaz paciente)
 ├── nutricionista.html (interfaz nutricionista)
 ├── styles.css (estilos compartidos)
 └── script.js + JavaScript incrustado
```

## 5. Principios SOLID: aplicación propuesta
SOLID está pensado principalmente para diseño de código orientado a objetos. En el frontend actual, existen tiene funciones y scripts globales.

### S — Responsabilidad única (SRP)
Cada módulo debe tener una única responsabilidad. Ejemplo propuesto: separar `AuthService` para autenticación, `MealService` para registros de comidas y `ChatService` para mensajes, evitando que una función gestione a la vez interfaz, almacenamiento y validación.

### O — Abierto/cerrado (OCP)
Las reglas de planes alimenticios pueden extenderse mediante estrategias o tipos de plan sin modificar la lógica principal que muestra los planes. Ejemplo: `PlanRenderer` consume un objeto de plan y delega la representación por tipo.

## 6. Patrones creacionales
### 6.1 Factory Method / Simple Factory
Una fábrica puede construir el objeto de perfil según el rol (`patient` o `nutritionist`) y devolver la configuración correspondiente. Esto evita repetir condicionales de creación de perfil en varios lugares.

### 6.2 Builder
Un constructor de planes alimenticios puede armar un objeto `Plan` a partir de datos validados (nombre, comidas, indicaciones y fechas), manteniendo consistente su estructura.

## 7. Patrones de comportamiento
### 7.1 Observer
Cuando se añade una comida, los componentes interesados (contador diario y lista de comidas) pueden recibir una notificación del evento `mealAdded`. Se desacopla el registro de comida de las actualizaciones de la interfaz.

### 7.2 Strategy
La validación o presentación de planes puede variar según el tipo de perfil o tipo de plan, mediante estrategias intercambiables que compartan un contrato común.

## 8. Seguridad y privacidad
- No almacenar contraseñas en texto plano en `localStorage`.
- En una versión real, autenticar en servidor y autorizar cada acceso por rol.
- Restringir el acceso de cada paciente a su propio plan.
- Proteger API y base de datos mediante reglas de acceso y HTTPS.

## 9. Pruebas de aceptación
- Acceder con una cuenta de demostración válida y llegar al panel correcto.
- Introducir credenciales incorrectas y comprobar el mensaje de error.
- Registrar una comida y verificar que aparezca en la lista.
- Enviar un mensaje y verificar que aparezca en la conversación.
- Probar navegación en pantalla de escritorio y móvil.
- Documentar por separado qué pruebas son locales y cuáles se hicieron en nube.
