# Proyecto Hospital Villarica - n8n Workflow

**Descripción**
- **Resumen**: Flow de `n8n` para automatizar captura y generación de documentos clínicos desde un formulario interno. Extrae datos del paciente (nombre, RUT, previsión, edad, fecha de nacimiento, complejidad), guarda un historial en Google Sheets y crea/actualiza plantillas en Google Drive/Docs según los exámenes seleccionados.

**Requisitos Previos**
- **n8n**: Instancia de `n8n` (self-hosted o n8n.cloud).
- **Credenciales**: cuentas con permisos para Google Drive, Google Docs, Google Sheets y OpenAI (API Key).
- **Archivo del flow**: `Proyecto Hospital Villarica copia (1).json` (exportación del workflow de `n8n`).

**Instalación / Importar el Flow**
- **Importar**: En tu instancia de `n8n` ve a Workflows → Import → pega o sube `Proyecto Hospital Villarica copia (1).json`.
- **Revisar**: Abre el workflow y revisa todos los nodos y credenciales.
- **Activar**: Activa el workflow cuando la configuración esté completa.

**Configuración de Credenciales**
- **Google Drive / Docs / Sheets**: configurar OAuth2 en `n8n` con una cuenta que tenga acceso a las plantillas y carpetas destino.
- **OpenAI**: configurar la credencial con tu API Key; el nodo de chat se encarga de la extracción de texto.
- **Zona horaria**: asegurar que `n8n` tenga la zona horaria correcta (impacta cálculo de fechas).

**Cómo funciona (resumen de flujo)**
- **Formulario**: `Formulario Ingreso Pacientes` recibe texto pegado desde HCE.
- **Workflow Configuration**: normaliza campos básicos (`datos_paciente_texto`, `fecha_ingreso`, etc.).
- **Code in JavaScript**: parsea entradas tabuladas y calcula `fechanacimiento` desde patrones como `40 A 2 M 3 D` utilizando `fecha_ingreso`.
- **OpenAI Chat Model + Extraer Datos del Paciente**: usa un modelo para estructurar `nombre`, `apellido_paterno`, `apellido_materno`, `rut`, `prevision`, `edad`, `complejidad` (salida parseada con `Parser Datos Estructurados`).
- **Preparar Datos Paciente / Médico**: mapea resultados a campos para los siguientes nodos.
- **Guardar Historial Pacientes**: agrega una fila a Google Sheets con los datos del paciente.
- **Recorrido de Exámenes**: separa el array de exámenes seleccionados y enruta por tipo (`Enrutar por Tipo de Examen`) para seleccionar plantilla correspondiente.
- **Copiar Archivo / Sobreescribir documento**: duplica plantillas de Google Docs y reemplaza marcadores (p. ej. `RUTPACIENTE`, `NOMBREPACIENTE`, `fechanacimiento`).

**Campos importantes y ejemplo**
- **Entrada (ejemplo)**:

```
CRISTIAN ALEJANDRO CAMPOS IRRIBARRA	15764390-1		FONASA - D	NO	NO	38 A 2 M 3 D	( ESOS "NO" SON PORQUE VIENEN DE UN COPIAR PEGAR DE DESORDENADO PERO NUESTRO SISTEMA LOS OMITE )
```

- **Salida esperada**: JSON con `nombre`, `apellido_paterno_paciente`, `apellido_materno_paciente`, `rut`, `prevision`, `edad`, `fechanacimiento`, `complejidad`.
- **Marcadores en plantillas**: `RUTPACIENTE`, `NOMBREPACIENTE`, `APELLIDOPATERNO`, `APELLIDOMATERNO`, `EDADPACIENTE`, `PREVISIONPACIENTE`, `fechanacimiento`, `FechaSolicitud`, `NombreMedico`.

**Personalización**
- **Plantillas**: subir/editar tus Google Docs en la carpeta indicada y actualizar los IDs en los nodos `Copiar Archivo`.
- **Mapeos**: editar nodos `Set` y la configuración del nodo `Google Sheets` para cambiar columnas o añadir campos.
- **Parsing**: ajustar el nodo `Code in JavaScript` o el prompt de OpenAI para otros formatos de entrada.

**Seguridad y privacidad**
- **Datos sensibles**: el flow maneja información de salud (PHI). Asegura que:
  - las cuentas Google y la instancia `n8n` estén en entornos seguros;
  - el acceso esté restringido por roles;
  - se cumpla la normativa local de protección de datos.
- **Logs**: evitar exportar logs con PHI a sistemas públicos.

**Despliegue**
- **Producción**: usar `n8n` con HTTPS y control de acceso; preferible self-hosting en infraestructura segura.
- **Testing**: usar `formMode=test` o datos de `pinData` para validar antes de producción.

**Resolución de problemas**
- **OAuth falla**: revisar permisos y refrescar credenciales Google.
- **OpenAI falla**: verificar API Key, límites y configuración del modelo en el nodo de chat.
- **Fechas erróneas**: comprobar zona horaria de `n8n` y formato `fecha_ingreso` (`DD/MM/YYYY HH:mm`).
- **Campos no extraídos**: ajustar el prompt o la lógica del `Code` parser si el texto pegado varía.

**Contribuir**
- **Cómo contribuir**: abrir issue o PR con mejoras al parsing, plantillas adicionales o nuevos tipos de examen.
- **Pruebas**: incluye ejemplos (anónimos) de `datos_paciente` para validar parsing.

**Licencia y contacto**
- **Licencia**: agrega la licencia que prefieras (p. ej. MIT).
- **Contacto**: añadir correo o responsable del proyecto para soporte.

---

_Archivo generado automáticamente a partir del flow `Proyecto Hospital Villarica copia (1).json`._
