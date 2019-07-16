# BGAssetUnity

Init inicial: Asset de Blended Games Framework unity

Al diseñar nuevos videojuegos se puede utilizar esta herramienta para administrar nuevos datos de sensores de forma sencilla basándose en la comunicación por WebSocket. A continuación, se describen los pasos para poder utilizar el Asset de Unity.

Los requerimientos iniciales antes de utilizar el asset son:
\begin{itemize}
    \item Instalar Aplicación de escritorio.
    \item Descargar plugin que se desea utilizar y ubicar en carpeta Pluggins.
    \item Para trabajar con fuentes de datos externas es necesario desplegar los microservicios.
\end{itemize}

\section{Agregando Asset: script y prefab}
Para comenzar a usar Blended Games Framework en Unity inicialmente hay que descargar el Asset desde el repositorio de git: (Referencia). Luego de descargar hay que ubicar la carpeta BG en la carpeta Assets dentro del proyecto de Unity en el que se esta trabajando:

IMAGEN DE PROYECTO NUEVO CON LA CARPETA BG EN ASSETS

Ahora con los archivos dispuestos dentro del proyecto, se accede a la carpeta BG/Data donde se encuentra el script BGWebSocket, el cual debe ser arrastrado hacia un Scene para que se ejecute, al mismo tiempo hay que arrastrar el Prefab que se encuentra en la carpeta BG/SocketIO/Prefabs hasta los GameObjects del proyecto.

IMAGEN DE ARRASTRAR SCRIPT Y ARRASTRAR PREFAB

Por último hay que modificar el Url del servidor donde tenemos nuestro WebSocket.

IMAGEN DE CAMBIO DE URL

Ahora esta todo listo para utilizar los métodos necesarios y los objetos de atributos para obtener y manejar datos de diversas fuentes de información.

\section{Implementación básica de uso}
Habiendo completado los pasos anteriores, nos dirigirnos al Script que deseemos para modificar los eventos del WebSocket ante la entrada de Atributos, tanto como de Resumen de atributos (Categorías), como de atributos simples (Procesados o en Streaming).

\begin{lstlisting}[caption={ Ejemplo de Uso de Blended Games Framework. \newline Fuente: Elaboración propia, 2019.},captionpos=b, label = {BGFrame:1}]
//Agregar biblioteca SocketIO
using SocketIO;
//Declarar una variable para el uso del atributo y del atributo categoría o resumen
private BGobjects.AttributePlayer attAux;
private BGobjects.AttributeResPlayer attAuxRes;
//Agregar al inicio del script 
void Start()
{
    //Para obtener los sensores que están disponibles en el socket se utiliza :
    BDWebSocket.instance.GetAllSensors()
    //Revisando la lista de Strings en APIRestClient.instance.AllSensor o viendo el mensaje impreso en pantalla, se obtienen los sensores disponibles.
    //Ahora nos conectamos a alguno de los sensores disponibles, de la siguiente manera:
    BDWebSocket.instance.ConnectToSensor("NombreObtenidoEnAllsensors", "NombreDelJuegoNuevo");
    // Se específica las funciones OnSmessage y OnRmessage para manejar el evento de la llegada de un atributo, para facilitar el uso de los datos apenas estén disponibles.
    BDWebSocket.instance.socket.On("Smessage",OnSmessage);
    BDWebSocket.instance.socket.On("Rmessage",OnRmessage);
}
//El siguiente código solo se ejecutara cuando el WebSocket se comunique enviándonos los atributos el resumen de una categoría de atributo.
private void OnSmessage(SocketIOEvent socketIOevent)
{
//Guardamos el atributo que nos llega en el objeto que posee los campos correspondientes al Atributo esperado, y luego podemos usar el dato.
    attAux = BDWebSocket.instance.JSONstrToAttribute(socketIOevent.data);
    string data = attAux.Dato.ToString();
}

private void OnRmessage(SocketIOEvent socketIOevent)
{
//De la misma manera para el resumen de una categoría de atributo se transforma en su objeto correspondiente
    attAuxRes = BDWebSocket.instance.JSONstrToAttribute(socketIOevent.data);
    string data = attAuxRes.Dato.ToString();
}

\end{lstlisting}
