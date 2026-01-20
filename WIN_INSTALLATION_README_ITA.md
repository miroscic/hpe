Scarica exe e installa l'ultima versione di mads:
https://github.com/pbosetti/MADS/releases

Scarica exe da:
https://opencv.org/releases/
Esegui ed estrai "opencv" in "C:"

Scsrica openvino 2023.3 da qua:
https://docs.openvino.ai/2023.3/openvino_docs_install_guides_overview.html?VERSION=v_2023_3_0&OP_SYSTEM=WINDOWS&DISTRIBUTION=ARCHIVE
estrai la cartella e spostala in "C:\Program Files (x86)\Intel". Crea la cartella Intel se non esiste. Rinomina la cartella come "openvino_2023.3".

Aggiungi a "Path" i percorsi di opencv e openvino (controlla che siano giusti):
C:\opencv\build\x64\vc16\bin
C:\Program Files (x86)\Intel\openvino_2023.3\runtime\bin\intel64\Release
C:\Program Files (x86)\Intel\openvino_2023.3\runtime\3rdparty\tbb\bin

Imposta manualmente o con questi comando queste variabili d'ambiente:
setx OpenCV_DIR C:\opencv
setx OpenVINO_DIR "C:\Program Files (x86)\Intel\openvino_2023.3"

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
setx K4A_DIR "C:\Program Files\Azure Kinect SDK v1.4.1"
setx K4ABT_DIR "C:\Program Files\Azure Kinect Body Tracking SDK"

Aggiungi in Path: 
C:\Program Files\Azure Kinect SDK v1.4.1\tools
C:\Program Files\Azure Kinect Body Tracking SDK\tools

Scarica ed esegui EXE di MPI Microsoft da:
https://www.microsoft.com/en-us/download/details.aspx?id=105289
Aggiungi a Path:
C:\Program Files\Microsoft MPI\Bin
Aggiungere anche nelle variabili di sistema se dà errore in compilazione

Scarica pkg-config-lite da:
https://sourceforge.net/projects/pkgconfiglite/files/0.28-1/
Estrai e copia la cartella "pkg-config-lite-0.28-1" in "C:"
Aggiungi a Path: 
C:\pkg-config-lite-0.28-1\bin

Installa cmake scaricandolo da https://cmake.org/download/


Riavvia il PC

Clona la repo di hpe in "C:mirrorworld:hpe" e esegui

cmake -Bbuild -DCMAKE_INSTALL_PREFIX="$(mads -p)"
cmake --build build --config Release
cmake --build build --config Release -t install

Per installare correttamente MONGO seguire questi passi:
- Installare ed avviare docker (non è necessario fare per forza il login)
https://www.docker.com/
- Installare mongo su docker mediante questo comando: 
docker run --name mads-mongo --restart unless-stopped -v ${PWD}/db:/data/db -p27017:27017 -d mongo
- Per fare funzionare il logger c'è bisogno di installare i driver di mongo in locale, andare quindi a questo link:
https://www.mongodb.com/docs/languages/cpp/cpp-driver/current/get-started/ e seguire le istruzioni.

curl -OL https://github.com/mongodb/mongo-cxx-driver/releases/download/r4.1.1/mongo-cxx-driver-r4.1.1.tar.gz
Se la powershell non riconosce il comando -OL scaricare direttamente il file tar dal link (basta andare al link che il download parte in automatico)
tar -xzf mongo-cxx-driver-r4.1.1.tar.gz
cd mongo-cxx-driver-r4.1.1/build

cmake -G "Visual Studio <version> <year>" -A "x64" -DCMAKE_CXX_STANDARD=17 -DCMAKE_INSTALL_PREFIX=C:\mongo-cxx-driver   
Al posto di <version> mettere 17 e al posto di <year> mettere 2022

cmake --build . --config RelWithDebInfo
cmake --build . --target install --config RelWithDebInfo

Aggiungere al path il percorso di installazione:
"C:\mongo-cxx-driver\bin"

--------------------------------------------------------
-------------------------------------------------------
Per aggiornare mads all'ultima versione
Vai sul sito git.new/mads
Scarica release e installa

Ricompila hpe. Basta cancellare tutte le cartelle plugin e pugg in build/_deps
Esegui
cmake -Bbuild -DCMAKE_INSTALL_PREFIX="$(mads -p)"
cmake --build build --config Release -t install


-----------------------------------------------------------
----------------------------------------------------------
Come eseguire il broker, il logger e gli agenti hpe.

Per trovare l'ip del broker da fornire agli agenti che non girano sullo stesso pc esegui:
mads-broker -n list

Ora puoi terminare il broker.
Il comando precedente mostra gli indirizzi ip relativi alla scheda di rete. Scegliere una scheda di rete ed il relativo nome/numero (per esempio "en0" oppure "11") ed esegui di nuovo il broker ma con questo comando (se hai scelto en0):
mads-broker -n en0

Il terminale mostrerà l'indirizzo <IP> e la <PORT> a cui devono collegarsi tutti gli agenti. Se girano sullo stesso pc del broker non è necessario indicare <IP> e <PORT>

Se vuoi salvare i dati in MONGO apri docker e fai partire il container oppure esegui
docker run --name mads-mongo --restart unless-stopped -v ${PWD}/db:/data/db -p27017:27017 -d mongo 

Apri MongoDB Compass e connettiti.
Esegui il logger:
mads-logger

Esegui gli agenti hpe:
mads-source -s tcp://<IP>:<PORT> hpe.plugin 

INSTALLAZIONE DI CUDA
Scaricare CUDA Toolkit 12.8 dal link:
https://developer.nvidia.com/cuda-12-8-0-download-archive?target_os=Windows&target_arch=x86_64&target_version=11&target_type=exe_local

Eseguire l'eseguibile mantenendo tutte le impostazioni standard

Riavviare il sistema e verificare da powershell con il comando:
nvcc --version

Scaricare cuDNN dal link:
https://developer.nvidia.com/cudnn-9-14-0-download-archive?target_os=Windows&target_arch=x86_64&target_version=11&target_type=exe_local

Avviare l'eseguibile ed installare

Copia tutti i file presenti:
da C:\Program Files\NVIDIA\CUDNN\v9.14\include\12.9 a C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.8\include
da C:\Program Files\NVIDIA\CUDNN\v9.14\bin\12.9 a C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.8\bin
da C:\Program Files\NVIDIA\CUDNN\v9.14\lib\12.9\x64 a C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.8\lib\x64