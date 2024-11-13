
# Proyecto Final: Editor de Archivos Encriptados

### Fecha límite de entrega: 22 de noviembre

## Descripción del Proyecto

El reto final consiste en diseñar y desarrollar un editor de archivos de texto plano que permita la creación, edición y visualización de archivos encriptados. El archivo, al ser guardado en disco, debe estar encriptado de modo que su contenido no sea legible sin abrirlo en el editor. Al abrir el archivo en el editor, el contenido debe desencriptarse automáticamente para permitir la edición.

### Objetivos del Proyecto
1. **Encriptación y Desencriptación**: El archivo debe estar encriptado en el disco y debe desencriptarse automáticamente al abrirse en el editor.
2. **Interfaz de Edición**: Proporcionar una interfaz de edición similar a la del editor de texto `vi`, en la cual el usuario pueda ver y modificar el contenido del archivo. Se recomienda el uso de librerías como *Ncurses* en C++ para lograr una experiencia interactiva en la terminal.
3. **Guardado Seguro**: Al finalizar la edición, el archivo debe guardarse encriptado nuevamente.

### Librerías recomendadas (según lenguaje):
- **C++**:
  - **Encriptación**: OpenSSL, Crypto++, Botan
  - **Interfaz de texto**: Ncurses, o una interfaz gráfica con Qt (si se decide extender)
- **Python**:
  - **Encriptación**: PyCryptodome, Fernet (de Cryptography)
  - **Interfaz de texto**: Tkinter, PyQt, o Ncurses para consolas.
- **Java**:
  - **Encriptación**: Bouncy Castle, JCE (Java Cryptography Extension)
  - **Interfaz gráfica**: Swing, JavaFX

### Código Base en C++
```cpp
#include <iostream>
#include <fstream>
#include <string>
#include <crypto++/aes.h>
#include <crypto++/modes.h>
#include <crypto++/filters.h>
#include <ncurses.h>

using namespace CryptoPP;
using namespace std;

// Clave y vector de inicialización (IV) para la encriptación
byte key[AES::DEFAULT_KEYLENGTH] = { /* clave segura */ };
byte iv[AES::BLOCKSIZE] = { /* IV seguro */ };

// Función de encriptación
string encriptar(const string& texto) {
    string cipher;
    CBC_Mode<AES>::Encryption encryptor(key, sizeof(key), iv);
    StringSource s(texto, true, new StreamTransformationFilter(encryptor, new StringSink(cipher)));
    return cipher;
}

// Función de desencriptación
string desencriptar(const string& cipher) {
    string plain;
    CBC_Mode<AES>::Decryption decryptor(key, sizeof(key), iv);
    StringSource s(cipher, true, new StreamTransformationFilter(decryptor, new StringSink(plain)));
    return plain;
}

// Función para guardar texto encriptado
void guardarEncriptado(const string& nombreArchivo, const string& texto) {
    string cipher = encriptar(texto);
    ofstream archivo(nombreArchivo, ios::binary);
    archivo.write(cipher.c_str(), cipher.size());
    archivo.close();
}

// Función para cargar texto desencriptado
string cargarDesencriptado(const string& nombreArchivo) {
    ifstream archivo(nombreArchivo, ios::binary);
    string cipher((istreambuf_iterator<char>(archivo)), istreambuf_iterator<char>());
    archivo.close();
    return desencriptar(cipher);
}

int main() {
    string nombreArchivo = "archivo_encriptado.txt";
    string contenido = cargarDesencriptado(nombreArchivo);

    // Inicializa la biblioteca Ncurses
    initscr();
    printw("Contenido desencriptado del archivo:\n\n%s", contenido.c_str());
    printw("\n\n--- Escribe el nuevo contenido (presiona Ctrl+D para guardar) ---\n");

    string nuevoContenido;
    char ch;
    while ((ch = getch()) != 4) { // Ctrl+D
        nuevoContenido += ch;
        addch(ch);
    }
    contenido += "\n" + nuevoContenido;

    endwin();  // Cierra Ncurses

    // Guarda el contenido completo encriptado
    guardarEncriptado(nombreArchivo, contenido);
    cout << "Archivo guardado exitosamente encriptado.\n";
    return 0;
}
```

### Rúbrica de Evaluación
Como sugerencia esta NCurses, ES OPCIONAL, es como referencia, si usan otra libreria o no usan libreria, la nota no afecta

| Criterio                           | Excelente (5)                                                        | Bueno (4)                                                           | Aceptable (3)                                                    | Insuficiente (1-2)                                                |
|------------------------------------|----------------------------------------------------------------------|---------------------------------------------------------------------|------------------------------------------------------------------|--------------------------------------------------------------------|
| **Encriptación y Desencriptación** | Seguridad y ejecución sin errores.                                   | Correcta, con mínimos errores.                                      | Funciona en la mayoría de casos, aunque con fallas leves.       | No funciona o no es segura.                                       |
| **Interfaz de Usuario (Ncurses)**  | Simula bien la experiencia de edición tipo *vi*                      | Interfaz adecuada, con controles básicos.                           | Interfaz limitada, pero permite edición básica.                 | La interfaz es poco funcional.                                    |
| **Guardado y Carga del Archivo** Opcional   | Guarda y carga encriptado sin fallas.                                | Guarda y carga con errores menores.                                 | Guarda y carga, pero tiene problemas de funcionamiento.         | No guarda correctamente o carga con errores críticos.             |
| **Calidad del Código**             | Bien estructurado y documentado.                                     | Claro, mayormente organizado y documentado.                         | Funcional, aunque desorganizado o sin suficientes comentarios.  | Desorganizado, poco claro y sin documentación suficiente.         |
| **Uso de Librerías (Ncurses)** Opcional    | Uso eficiente de Ncurses y Crypto++.                                 | Uso adecuado de Ncurses y Crypto++.                                 | Uso mínimo de librerías y Ncurses limitado.                     | No usa Ncurses o Crypto++ adecuadamente.                          |

Esta rúbrica mide el cumplimiento de los objetivos y la calidad en la implementación de encriptación, la interfaz de usuario y el guardado de archivos encriptados.
