# Configuración inicial de Git

Antes de comenzar a trabajar con Git, es recomendable configurar la identidad del usuario. Esta información se utilizará para registrar quién realiza cada cambio en el historial del repositorio.

## Configuración global

```bash
git config --global user.name "Cris575"
git config --global user.email "test@email.com"
```

### ¿Qué hace este comando?

Configura el nombre de usuario y el correo electrónico que Git asociará a todos los commits realizados desde este equipo.

### Parámetros

| Parámetro    | Descripción                                                                  |
| ------------ | ---------------------------------------------------------------------------- |
| `user.name`  | Nombre que aparecerá como autor de los commits.                              |
| `user.email` | Correo electrónico asociado al autor de los commits.                         |
| `--global`   | Aplica la configuración a todos los repositorios del usuario en este equipo. |

> 💡 **Nota**
>
> Si omites `--global`, la configuración solo afectará al repositorio actual.

### Ver la configuración actual

```bash
git config --list
```

O consultar un valor específico:

```bash
git config user.name
git config user.email
```

---

# Consultar el estado del repositorio

```bash
git status
```

## ¿Qué hace este comando?

Muestra el estado actual del repositorio y proporciona información sobre los archivos.

Entre otras cosas, indica:

- Archivos nuevos sin seguimiento (_Untracked_).
- Archivos modificados.
- Archivos preparados para el próximo commit (_Staged_).
- Si el árbol de trabajo está limpio.
- La rama en la que te encuentras.

> 💡 **Nota**
>
> `git status` es uno de los comandos más utilizados en Git. Es recomendable ejecutarlo con frecuencia para conocer el estado del repositorio antes de realizar un commit.

---

# Inicializar un repositorio

```bash
git init
```

## ¿Qué hace este comando?

Inicializa un nuevo repositorio Git en el directorio actual.

Al ejecutarlo, Git crea una carpeta oculta llamada `.git`, donde almacena toda la información necesaria para controlar versiones del proyecto.

A partir de este momento, el directorio pasa a ser un repositorio Git.

### ¿Qué contiene la carpeta `.git`?

Entre otros elementos, almacena:

- Historial de commits.
- Configuración del repositorio.
- Referencias a ramas y etiquetas.
- Objetos y snapshots del proyecto.

> ⚠️ **Importante**
>
> Nunca elimines ni modifiques manualmente la carpeta `.git`, ya que contiene toda la información del repositorio.

### Verificar que el repositorio fue creado

```bash
git status
```

Si el repositorio fue inicializado correctamente, Git mostrará el estado del proyecto en lugar de indicar que no existe un repositorio.

## Flujo habitual

```text
Crear carpeta
      │
      ▼
git init
      │
      ▼
Se crea la carpeta .git
      │
      ▼
git status
      │
      ▼
Comenzar a agregar archivos
```

## ¿Por qué existe la recomendación de **no ejecutar `git init` dentro de otro repositorio**?

Es común encontrar advertencias como:

> **WARNING**
>
> **DO NOT INIT A REPO INSIDE OF A REPO!**
>
> _Before running `git init`, use `git status` to verify that you are not currently inside of a repo._

A primera vista puede parecer que esta advertencia significa **"nunca ejecutes `git init` en un repositorio existente"**, pero en realidad **se refiere a una situación diferente**.

---

## Reinicializar un repositorio existente (Seguro)

Supongamos que tienes el siguiente proyecto:

```text
Proyecto/
│── .git/
│── src/
│── README.md
```

Si te encuentras en la carpeta **Proyecto** y ejecutas:

```bash
git init
```

Git responderá algo similar a:

```text
Reinitialized existing Git repository in C:/Proyecto/.git/
```

En este caso:

- ✅ No se elimina el historial.
- ✅ No se borran los commits.
- ✅ No desaparecen las ramas.
- ✅ No se pierde la configuración del repositorio.
- ✅ No se eliminan los remotos (`origin`).

Simplemente Git verifica que la estructura del directorio `.git` exista y la reinicializa si es necesario.

---

## Inicializar un repositorio dentro de otro

Ahora considera la siguiente estructura:

```text
Proyecto/
│── .git/
│── src/
│── Documentacion/
```

Si entras a la carpeta **Documentacion** y ejecutas:

```bash
cd Documentacion
git init
```

Obtendrás una estructura como esta:

```text
Proyecto/
│── .git/
│── src/
│── Documentacion/
│   └── .git/
```

Ahora existen **dos repositorios Git independientes**:

- El repositorio principal (`Proyecto`).
- Un nuevo repositorio (`Documentacion`).

Este es el escenario contra el que advierte la imagen.

---

## ¿Qué problemas puede ocasionar?

Crear un repositorio dentro de otro puede generar comportamientos inesperados, por ejemplo:

- Los archivos del repositorio interno dejan de pertenecer al repositorio principal.
- Git puede ignorar los cambios realizados dentro del repositorio anidado.
- Al clonar el repositorio principal, el repositorio interno no se clona automáticamente.
- Puede generar conflictos al realizar `git add`, `git commit` o `git push`.
- Es fácil confundir el historial y los remotos de ambos repositorios.

Ejemplo:

```text
Proyecto/
│── .git/
│── API/
│   └── .git/
```

Si ejecutas:

```bash
git add .
```

Git tratará la carpeta `API` como un repositorio independiente, por lo que su contenido no será administrado de la misma forma que el resto del proyecto.

---

## ¿Qué hacer si realmente necesito un repositorio dentro de otro?

Si el objetivo es incluir otro repositorio dentro del proyecto, Git ofrece una solución diseñada para ello:

```bash
git submodule add <url-del-repositorio>
```

Los **submódulos** permiten administrar un repositorio dentro de otro de manera controlada, conservando el historial y la relación entre ambos proyectos.

---

## Resumen

| Situación                                                      | ¿Es recomendable? | Resultado                                                                                  |
| -------------------------------------------------------------- | ----------------- | ------------------------------------------------------------------------------------------ |
| Ejecutar `git init` nuevamente en el mismo repositorio         | ✅ Sí             | Reinicializa el repositorio sin perder historial.                                          |
| Ejecutar `git init` dentro de una subcarpeta de un repositorio | ❌ No             | Se crea un repositorio independiente dentro del principal, lo que puede generar problemas. |
| Incluir otro repositorio de forma controlada                   | ✅ Sí             | Utilizar `git submodule`.                                                                  |

---

## Conclusión

La advertencia **"Do not init a repo inside of a repo"** no significa que esté prohibido ejecutar `git init` sobre un repositorio existente. Su propósito es evitar la creación accidental de **repositorios anidados**, una práctica que suele ocasionar problemas de administración del código fuente.

Si únicamente ejecutas `git init` en la raíz de un repositorio que ya contiene un directorio `.git`, Git simplemente lo **reinicializará** sin afectar el historial, las ramas o los commits existentes.
