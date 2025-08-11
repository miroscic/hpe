Scarica exe e installa mads (dalla versione 1.3.2):
https://github.com/pbosetti/MADS/releases

Scarica exe da:
https://opencv.org/releases/
Esegui ed estrai "opencv" in "C:"

Scsrica openvino 2023.3 da qua:
https://docs.openvino.ai/2023.3/openvino_docs_install_guides_overview.html?VERSION=v_2023_3_0&OP_SYSTEM=WINDOWS&DISTRIBUTION=ARCHIVE
estrai la cartella e spostala in "C:\Program Files (x86)\Intel". Rinomina la cartella come "openvino_2023.3".

Aggiungi a "Path" i percorsi di openc e openvino (controlla che siano giusti):
C:\opencv\build\x64\vc16\bin
C:\Program Files (x86)\Intel\openvino_2023.3\runtime\bin\intel64\Release
C:\Program Files (x86)\Intel\openvino_2023.3\runtime\3rdparty\tbb\bin

Imposta manualmente o con questi comando queste variabili d'ambiente:
setx OpenCV_DIR C:\opencv
setx OpenVINO_DIR 'C:\Program Files (x86)\Intel\openvino_2023.3'

Installa PCL scaricando la v1.14.1 da:
https://github.com/PointCloudLibrary/pcl/releases
Scarica exe ed esegui (accetta tutto trane icone su desktop)

Aggiungi percorsi a Path nelle variabili d'ambiente
C:\Program Files\PCL 1.14.1
C:\Program Files\PCL 1.14.1\bin
C:\Program Files\PCL 1.14.1\include
C:\Program Files\PCL 1.14.1\3rdParty
C:\Program Files\PCL 1.14.1\3rdParty\VTK\bin

Scarica Azure Kinect SDK 1.4.1 da:
https://www.microsoft.com/en-us/download/details.aspx?id=101454
Esegui exe

Scarica Azure Kinect Body Tracking SDK v1.1.2 da:
https://www.microsoft.com/en-us/download/details.aspx?id=104221
Esegui msi

Aggiungi variabili d'ambiente
K4A_DIR "C:\Program Files\Azure Kinect SDK v1.4.1"
K4ABT_DIR "C:\Program Files\Azure Kinect Body Tracking SDK"

Aggiungi in Path: 
C:\Program Files\Azure Kinect SDK v1.4.1\tools
C:\Program Files\Azure Kinect Body Tracking SDK\tools

Scarica ed esegui msi di MPI Microsoft da:
https://www.microsoft.com/en-us/download/details.aspx?id=105289
Aggiungi a Path "C:\Program Files\Microsoft MPI\Bin"

Scarica pkg-config-lite da:
https://sourceforge.net/projects/pkgconfiglite/files/0.28-1/
Estrai e copia la cartella "pkg-config-lite-0.28-1" in "C:"
Aggiungi a Path: "C:\pkg-config-lite-0.28-1\bin"

Riavvia il PC

Clona la repo di hpe in "C:mirrorworld:hpe" e esegui

cmake -Bbuild -DCMAKE_INSTALL_PREFIX="$(mads -p)"
cmake --build build --config Release
cmake --build build --config Release -t install

