# Tablero de Baloncesto - Documentación del Proyecto

## Explicación del Proyecto

Este proyecto es una aplicación Android para llevar el marcador de partidos de baloncesto. La idea surgió de la necesidad de tener un tablero digital sencillo que pueda usarse durante partidos amateur o entrenamientos.

La aplicación está desarrollada en Java para Android y utiliza Data Binding para mantener la interfaz de usuario sincronizada con los datos del marcador. Esto hace que el código sea más mantenible y reduce la cantidad de código boilerplate que normalmente se necesita para actualizar la interfaz cuando cambian los datos.

<img width="437" height="690" alt="image" src="https://github.com/user-attachments/assets/71d4c467-cd0f-4c7b-a061-e0c061e29414" />

## Implementación de Data Binding

Para implementar Data Binding, primero tuve que habilitarlo en el archivo build.gradle del módulo de la aplicación. Luego creé una clase ScoreData que contiene los marcadores de ambos equipos. Esta clase tiene los métodos getter y setter necesarios para que Data Binding funcione correctamente.

En los archivos XML de layout, usé la sintaxis @{} para vincular directamente los TextView con las propiedades de ScoreData. Por ejemplo, el TextView que muestra el marcador local usa android:text="@{String.valueOf(scoreData.localScore)}". Esto significa que cada vez que cambia el valor de localScore, el TextView se actualiza automáticamente sin necesidad de llamar a setText() manualmente.

Una ventaja importante de este enfoque es que los botones de restar puntos se deshabilitan automáticamente cuando el marcador llega a cero, gracias a la propiedad android:enabled="@{scoreData.localScore > 0}".

## Paso de Datos entre Activities

Para pasar los marcadores de la pantalla principal a la pantalla de resultados, utilicé el sistema de Intents de Android. En MainActivity, cuando el usuario presiona el botón de ver resultados, creo un Intent y agrego los marcadores como extras usando putExtra().

En ScoreActivity, recupero estos valores en el método onCreate() usando getIntent().getIntExtra(). Es importante usar constantes para los nombres de las keys para evitar errores de escritura. En este caso, definí EXTRA_LOCAL_SCORE y EXTRA_VISITOR_SCORE como constantes en MainActivity.

Una consideración importante es que siempre debo proporcionar valores por defecto por si ocurre algún error al pasar los datos. En este caso, uso 0 como valor por defecto para ambos marcadores.

## Estructura de la Aplicación

### MainActivity - Pantalla Principal

**Archivo: activity_main.xml**

Este layout utiliza ConstraintLayout para organizar todos los elementos de manera responsive. La estructura principal incluye:

- **Título de la aplicación**: Un TextView centrado en la parte superior con el nombre "Tablero Baloncesto"
- **Sección del equipo local**: 
  - TextView con la etiqueta "LOCAL"
  - TextView grande que muestra el marcador actual, vinculado con Data Binding a scoreData.localScore
  - Tres botones en horizontal: -1 (rojo), +1 (verde claro), +2 (verde oscuro)
  
- **Sección del equipo visitante**:
  - Misma estructura que el local pero con colores naranjas
  - TextView del marcador vinculado a scoreData.visitorScore

- **Botones de acción**:
  - ImageButton para resetear (icono de revertir en gris)
  - ImageButton para ver resultados (icono de siguiente en azul)

Los botones de restar puntos tienen la propiedad android:enabled condicionada al valor del marcador, lo que los desactiva automáticamente cuando el marcador es cero.

**Archivo: MainActivity.java**

Esta clase maneja la lógica principal de la aplicación. En el método onCreate() configuro el Data Binding y establezco los listeners para los botones. Cada botón llama a los métodos actualizarMarcadorLocal() o actualizarMarcadorVisitante() con el valor correspondiente de puntos.

La clase interna ScoreData actúa como modelo de datos y contiene los marcadores de ambos equipos. Usé una clase estática para poder serializarla fácilmente si fuera necesario.

<img width="245" height="546" alt="PrimeraPantalla_Tablero_EFM" src="https://github.com/user-attachments/assets/9d3ef3d6-403b-40ee-aa35-64c18a441f61" />

### ScoreActivity - Pantalla de Resultados

**Archivo: activity_score.xml**

Este layout es más simple y está centrado en mostrar información más que en interactuar. Su estructura incluye:

- **Título "Puntuación final"**: TextView centrado en la parte superior
- **Marcador final**: TextView muy grande que muestra el resultado en formato "X - Y", combinando ambos marcadores mediante Data Binding
- **Mensaje del resultado**: TextView que muestra quién ganó o si hubo empate, vinculado a la variable resultMessage
- **Botón "Volver al juego"**: Permite regresar a la pantalla principal

El layout utiliza margenes consistentes y centrado de todos los elementos para crear una presentación clara y fácil de leer.

**Archivo: ScoreActivity.java**

Esta actividad recibe los marcadores a través del Intent y los utiliza para crear una instancia de ScoreData. Luego determina el mensaje del resultado comparando los valores de ambos marcadores.

El método mensajesResultados() contiene la lógica simple para decidir qué mensaje mostrar basado en qué equipo tiene más puntos o si hay empate.

<img width="244" height="549" alt="SegundaPantalla_Tablero_EFM" src="https://github.com/user-attachments/assets/e61d0bd4-b1f1-464a-b6e3-1c09a910c440" />

### Archivo strings.xml

#### Propósito del archivo
El archivo `strings.xml` contiene todas las cadenas de texto utilizadas en la aplicación. Centralizar los textos aquí permite un mejor mantenimiento y facilita la traducción a otros idiomas en el futuro.

#### Contenido del archivo
- **app_name**: "Tablero Baloncesto" - Nombre de la aplicación
- **local_team**: "LOCAL" - Etiqueta para el equipo de casa
- **visitor_team**: "VISITANTE" - Etiqueta para el equipo visitante
- **minus_one**: "-1" - Texto del botón para restar un punto
- **plus_one**: "+1" - Texto del botón para sumar un punto
- **plus_two**: "+2" - Texto del botón para sumar dos puntos
- **reset**: "Resetear marcador" - Texto para el botón de reinicio
- **view_results**: "Ver resultados" - Texto para ver los resultados finales
- **final_score**: "Puntuación final" - Título en la pantalla de resultados
- **back_to_game**: "Volver al juego" - Texto del botón para regresar

#### Ventajas de este enfoque
Al tener todos los textos en un solo archivo, cualquier cambio se refleja automáticamente en toda la aplicación. Además, facilita la internacionalización si decido agregar soporte para otros idiomas.

### Archivo AndroidManifest.xml

#### Propósito del archivo
El archivo `AndroidManifest.xml` es el archivo de configuración principal de la aplicación Android. Define la estructura esencial de la app, sus componentes, permisos y características requeridas.

#### Contenido del archivo

##### Configuración de la aplicación
- **Nombre del paquete**: `com.example.tablero_efm` - Identificador único de la aplicación
- **Icono de la aplicación**: `@mipmap/ic_launcher` - Icono que aparece en el dispositivo
- **Nombre visible**: "Enrique de la Fuente Mendez" - Nombre que ven los usuarios
- **Tema**: `@style/Theme.AppCompat.Light.DarkActionBar` - Estilo visual de la interfaz

#### Activities declaradas

**MainActivity**
- **Actividad principal**: Es el punto de entrada de la aplicación
- **Filtro de intent**: Configurada como `LAUNCHER` para que aparezca en el menú de aplicaciones
- **Exportada**: `true` - Permite que otras aplicaciones la puedan iniciar

**ScoreActivity**
- **Actividad secundaria**: Muestra los resultados finales del partido
- **Actividad padre**: `MainActivity` - Define la jerarquía de navegación
- **No exportada**: Solo puede ser iniciada desde dentro de la aplicación

### Características de la aplicación
- **Backup automático**: Habilitado con reglas personalizadas en `@xml/backup_rules`
- **Soporte RTL**: `true` - Compatible con idiomas de derecha a izquierda
- **Extracción de datos**: Configurada en `@xml/data_extraction_rules`

### Ventajas de este enfoque
Al centralizar toda la configuración esencial en el Manifest, se mantiene un control centralizado sobre los componentes de la aplicación. Esto facilita la gestión de permisos, la configuración de actividades y asegura que el sistema Android comprenda correctamente la estructura de la app.

## Problemas Encontrados y Soluciones

### Problema con la Rotación de Pantalla
Inicialmente, cuando rotaba el dispositivo, los marcadores se reiniciaban a cero. Esto ocurría porque Android destruye y recrea las activities durante la rotación. Para solucionarlo, implementé la persistencia del estado usando onSaveInstanceState() y onRestoreInstanceState().

### Validación de Puntuaciones Negativas
Al principio, si el usuario presionaba repetidamente el botón de restar puntos, los marcadores podían volverse negativos. Agregué una verificación antes de cada actualización que asegura que el nuevo valor sea mayor o igual a cero.

### Navegación entre Activities
Tuve problemas con el paso de datos entre activities cuando usaba nombres de keys incorrectos. Solucioné esto creando constantes para los nombres de las extras y usándolas consistentemente en ambas activities.

## Posibles Mejoras

Para futuras versiones de la aplicación, estoy considerando:

- Agregar un cronómetro para controlar el tiempo de partido
- Implementar un historial de cambios de puntuación
- Permitir personalizar los nombres de los equipos
- Agregar sonidos para las acciones de sumar puntos
- Implementar modo landscape con layout optimizado

## Consideraciones Técnicas

La aplicación está diseñada para Android API 21 o superior y utiliza las librerías de AndroidX. El uso de Data Binding requiere una configuración específica en el archivo build.gradle pero reduce significativamente la cantidad de código necesario para mantener la interfaz actualizada.

El proyecto sigue una estructura clara separando la lógica de presentación de la lógica de negocio, aunque en aplicaciones más complejas consideraría implementar el patrón MVVM con ViewModel.
