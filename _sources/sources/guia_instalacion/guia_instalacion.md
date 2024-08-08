# Guía de instalación de las herramientas computacionales

## Descargar Miniforge o Miniconda

Miniforge o Miniconda son herramientas de gestión de entornos de Python.
Los entornos de Python nos brindan las posibilidad de instalar todas las bibliotecas que utilicemos para nuestro propósitos particulares, en nuestro caso resolver los problemas de la materias de control, sin interferir con otras instalaciones de Python que se tengan en el sistema operativo o para otros propósitos.

Los link de descargas son los siguientes:

- [Miniforge](https://github.com/conda-forge/miniforge/releases)
- [Miniconda](https://docs.conda.io/en/latest/miniconda.html)

Seguir el enlace de  y elegir la última versión disponible para su sistema operativo.

Si ya se cuenta con una instalación de Anaconda, entonces ya se dispone del gestor de entornos utilizado, y se puede obviar tanto este paso como el siguiente.

## Instalar Miniforge/Miniconda

Seguir las recomendaciones por defecto dadas por el instalador. Una vez terminada la instalación tendremos Python instalado en nuestro sistema. Además tendremos en el menú de inicio un ítem
que se llama Anaconda Powershell Prompt, el cual abriremos para continuar con la instalación de las herramientas del curso.

## Instalación de las herramientas especificas del curso

Para continuar con la instalación necesitaremos descargar el archivo [dyc.yml](https://drive.google.com/file/d/1KPSj-89Q4wTa5ifQvaCoa0_ohCtBXUGf/view?usp=sharing) haciendo clock sobre el enlace anterior. Una vez descargado, dirigirse con la consola de Anaconda Powershell Prompt abierta a la carpeta de descargas donde se encuentra el archivo `dyc.yml` descargado anteriormente. En general, para dirigirse a esta carpeta debemos tipear

```bash
cd ~\Downloads\
```

Para verificar que el archivo se encuentre ahí, podemos tipear:

```bash
dir dyc.yml
```

Si nos encontramos en la carpeta donde está el archivo este deberá aparecer luego del comando anterior.

Finalmente para instalar todas las herramientas necesarias en el curso debemos ejecutar en este comando:

```bash
conda env create --file dyc.yml
```

Una vez terminado este paso tendremos instaladas todas las herramientas necesarias para la materia. Este paso puede tardar un tiempo largo dependiendo de la velocidad de conexión. Si la instalación fué exitosa encontraremos dos ítems en el menú de inicio que son los que utilizaremos:

1. Spyder (dyc)
1. Jupyter Notebooks (dyc)

## Retoques a la herramientas para mayor comodidad de uso

### Jupyter Notebooks en Spyder

Spyder nos proporciona un entorno de desarrollo muy parecido a  Matlab, donde tenemos paneles similares a los de Matlab:

1. Panel de Comandos (command window)
1. Explorador de variables, donde podemos ver las variables  definidas y sus valores
1. Un editor de scripts para programar
1. Un editor de cuadernos de Jupyter (a continuación veremos como activarlo)
1. Una ventana de ayuda, que nos brinda rápidamente ayuda del comando que necesitemos usando las teclas `Ctrl+I`.
1. Historial de comandos, etc.

Una configuración útil en `Spyder` es tener la herramienta de los `Jupyter Notebook` en el mismo `Spyder`. Para esto abrir Spyder en `View> Panes` activar Notebooks.

La mayor parte del material de este curso está escrito en formato de de Jupyter Notebooks, y puede ser descargado abierto y modificado mediante usando Spyder y el plugin de Jupyter Notebooks.
