---
title: Vývoj a nasazení gRPC odvozeného serveru – Azure
description: Tento článek poskytuje pokyny pro vývoj a nasazení gRPC odvozeného serveru.
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: dbf46a26626a4143d76385968d092c4f238729da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105034856"
---
# <a name="how-to-guide--develop-and-deploy-a-grpc-inference-server"></a>Návod – vývoj a nasazení gRPC odvozeného serveru

## <a name="overview"></a>Přehled

V tomto článku se dozvíte, jak můžete zabalit model AI podle vašeho výběru na gRPC odvozený Server, aby ho bylo možné integrovat se službou Live video Analytics (LVA) prostřednictvím rozšíření grafu. 

## <a name="suggested-pre-reading"></a>Navrhované před čtením

* [Rozšíření mediálních grafů](media-graph-extension-concept.md)
* [Protokol rozšíření gRPC](grpc-extension-protocol.md)
* [Odvození schématu metadat](inference-metadata-schema.md)
* [Úvod do gRPC](https://www.grpc.io/docs/what-is-grpc/introduction/)
* [Průvodce jazyky proto3](https://developers.google.com/protocol-buffers/docs/proto3)

## <a name="prerequisites"></a>Požadavky

* Zařízení s platformou X86-64 nebo ARM64, které používá některý z [podporovaných operačních systémů Linux](../../iot-edge/support.md#operating-systems) nebo počítače s Windows.
* [Nainstalujte do počítače Docker](https://docs.docker.com/desktop/#download-and-install) .
* Nainstalujte [modul runtime IoT Edge](../../iot-edge/how-to-install-iot-edge.md?tabs=linux).

## <a name="grpc-implementation-steps"></a>Postup implementace gRPC

Pokud chcete vytvořit odvozený Server gRPC a implementovat ho jako rozšíření s živým analýzou videí, použijí se tyto kroky:

### <a name="setup"></a>Nastavení:

Proveďte potřebné kroky, abyste měli [nasazený modul video Analytics a pracovali na zařízení IoT Edge](deploy-iot-edge-device.md).

### <a name="high-level-implementation-steps"></a>Postup implementace vysoké úrovně:

1. Vyberte jeden z mnoha jazyků, které podporuje gRPC: C#, C++, DART, jít, Java, Node, objektivní-C, PHP, Python, Ruby.
1. Implementujte Server gRPC, který bude komunikovat se službou Live video Analytics pomocí [souborů proto3](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc).

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/inference-srv-container-process.png" alt-text="Server gRPC, který bude komunikovat se službou Live video Analytics pomocí souborů proto3":::

    V rámci této služby:
    1. Pořídí výměnu zpráv s popisem relace mezi serverem a klientem.
    1. Zpracování [ukázkových zpráv o médiích](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) a vracení výsledků.

        1. Vyvolejte modul Inferencing, který používá trained model pro odvození příchozích zpráv.
        1. Přijímají výsledky Inferencing z modulu, zabalí je zpátky jako ukázku média a pomocí souboru [Inferencing.](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/inferencing.proto) deslat se zpátky do živé analýzy videí.

            Případně můžete k ukázce média vyvolat jakoukoli funkci transformace média.
1. Nasaďte implementaci gRPC serveru. Existují dva způsoby:

    1. Nasazení jako modulu IoT společně umístěného v modulu Live video Analytics
    1. Nasaďte jako modul IoT do uzlu přístupného ze sítě (místní nebo v cloudu), který umožňuje výměnu dat pomocí modulu Live video Analytics.
1. Nakonfigurujte topologii grafu živé analýzy videí pomocí modulu Live video Analytics a najeďte na něj na gRPC Server.

### <a name="recommendation"></a>Základě

Pokud Collocating na stejném uzlu, je možné použít sdílenou paměť pro nejlepší výkon. To vyžaduje, abyste používali funkce sdílené paměti pro Linux zveřejněné programovacím jazykem nebo prostředím.

1. Otevřete popisovač sdílené paměti systému Linux.
1. Po přijetí snímku přejděte na posun adresy v rámci sdílené paměti.
1. Potvrďte dokončení zpracování snímků, aby jeho paměť mohla být uvolněna živým analýzou videí.

## <a name="create-a-grpc-inference-server"></a>Vytvoření odvozeného serveru gRPC

Nyní sestavíte modul IoT Edge (externí AI), který přijímá snímky videa ze služby Live video Analytics pomocí zpráv [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) prostřednictvím sdílené paměti, klasifikuje snímky jako "tmavé" nebo "světlé" a vrátí výsledky odvození zpět do jímky zpráv IoT Hub ve službě Live video Analytics pomocí [odvozených schémat metadat](inference-metadata-schema.md).

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/external-ai.png" alt-text="sestavení modulu IoT Edge (externí AI)":::

Tento server odvozený od gRPC je Konzolová aplikace .NET Core, která zpracovává zprávy [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) odeslané mezi živým videem a vlastním AI. Toto je tok zpráv mezi živým video analýzou a serverem odvození gRPC:

1. Live video Analytics pošle popisovač datových proudů médií (viz [extension.](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto)v tomto případě), který definuje informace o mediálním streamu, které budou odesílány pomocí snímků videa na server jako zprávu [Protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) prostřednictvím relace gRPC streamu. 
1. Server ověří a potvrdí popisovač streamu a nastaví požadovanou metodu přenosu dat.
1. Live video Analytics pak začne odesílat soubory MediaSample obsahující snímky videa.
1. Server analyzuje snímky videa při jejich přijetí a spustí jejich zpracování pomocí procesoru imagí definovaného vámi.
1. Server pak vrátí výsledky odvození jako [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) zprávy, jakmile budou k dispozici. 

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/grpc-external-srv.png" alt-text="Vytvoření odvozeného serveru gRPC":::

Snímky videa můžete přenést buď pomocí [sdílené paměti](https://en.wikipedia.org/wiki/Shared_memory#:~:text=In%20computer%20science%2C%20shared%20memory,of%20passing%20data%20between%20programs.) , nebo je můžete vložit do zprávy protobuf. Režim přenosu dat lze nakonfigurovat v topologii grafu LVA a určit tak, jak budou snímky přeneseny. Toho je dosaženo konfigurací prvku **DataTransfer** vlastnosti MediaGraphGrpcExtension, jak je znázorněno níže:

Vložené

```
"dataTransfer": {
              "mode": "Embedded"
            }
```

Sdílená paměť:

```
"dataTransfer": {
              "mode": "sharedMemory",
              "SharedMemorySizeMiB": "20"
            }
```

> [!NOTE]
> Při komunikaci přes sdílenou paměť by měla být hodnota IpcMode nastavena na **sdílení** a v modulu serveru gRPC nastavte hodnotu IpcMode na **container: {CONTAINER_NAME}**. Tato nastavení se mají udělat v souboru manifestu nasazení, který se používá k nasazení modulů do Azure IoT Hub. Níže je ukázka možností kontejneru, které se mají použít při nastavování modulů IoT Edge.

Modul Live video Analytics:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "shareable"
    }
}
```

modul rozšíření gRPC:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "container:lvaEdge"
    }
}
```

> [!NOTE]
> Ujistěte se, že máte přístup ke sdílené paměťové oblasti kontejneru: lvaEdge v rámci grpcExtension.

## <a name="sample-grpc-server"></a>Ukázkový Server gRPC

Abychom porozuměli podrobnostem o vývoji serveru gRPC, Projděte si ukázku kódu.

1. Naklonujte úložiště z odkazu na GitHub [https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) .
1. Spusťte VSCode a přejděte do složky/src/edge/modules/grpcExtension.
1. Pojďme si rychle prodávat soubory:

    1. **Program. cs**: Toto je vstupní bod aplikace. Zodpovídá za inicializaci a správu serveru gRPC, který bude fungovat jako hostitel. V naší ukázce port pro příjem příchozích zpráv gRPC z klienta gRPC (jako je například analýza živých videí) je určen elementem konfigurace grpcBindings v AppConfig.js.
    
        ```json    
        {
          "grpcBinding": "tcp://0.0.0.0:5001"
        }
        ```
    1. **Services\MediaGraphExtensionService.cs**: Tato třída zodpovídá za zpracování zpráv [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) . Načte rámec ve zprávě, vyvolá ImageProcessor a zapíše výsledky odvození.
Teď, když jsme nakonfigurovali a inicializoval připojení portů serveru gRPC, Podívejme se, jak můžeme zpracovat příchozí gRPC zprávy.

        * Po navázání relace gRPC se velmi první zpráva, že server gRPC obdrží od klienta (Live video Analytics), je MediaStreamDescriptor, který je definovaný v souboru [extension.](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto) de. 

            ```
            message MediaStreamDescriptor {
              oneof stream_identifier {
                GraphIdentifier graph_identifier = 1;     // Media Stream graph identifier
                string extension_identifier = 2;          // Media Stream extension identifier
              }
              MediaDescriptor media_descriptor = 5;       // Session media information.
              // Additional data transfer properties. If none is set, it is assumed
              // that all content will be transferred through messages (embedded transfer).
              oneof data_transfer_properties {
                SharedMemoryBufferTransferProperties shared_memory_buffer_transfer_properties = 10;
              }
            }
            ```
        * V naší implementaci serveru metoda `ProcessMediaStreamDescriptor` ověří vlastnost MediaDescriptor MediaStreamDescriptor pro videosoubor a pak nastaví režim přenosu dat (což je buď použití sdílené paměti, nebo použití režimu vloženého přenosu snímků) v závislosti na tom, co zadáte v topologii a použitém souboru šablony nasazení. 
        * Po přijetí zprávy a úspěšném nastavení režimu přenosu dat vrátí server gRPC zprávu MediaStreamDescriptor zpátky do klienta jako potvrzení a tím vytvoří spojení mezi serverem a klientem.    
        * Po úspěšném dokončení služby Live video Analytics se spustí přenos mediálního datového proudu na server gRPC. V naší implementaci serveru metoda `ProcessMediaStream` zpracuje příchozí MediaStreamMessage. MediaStreamMessage je také definováno v [rozšíření. proto](https://github.com/Azure/live-video-analytics/blob/master/contracts/grpc/extension.proto).

            ```
            message MediaStreamMessage {
              uint64 sequence_number = 1;       // Monotonically increasing directional message identifier starting from 1 when the gRPC connection is created
              uint64 ack_sequence_number = 2;   // 0 if this message is not referencing any sent message.
            
            
              // Possible payloads are strongly defined by the contract below
              oneof payload {
                MediaStreamDescriptor media_stream_descriptor = 5;
                MediaSample media_sample = 6;
              }
            }
            ```
        * V závislosti na hodnotě batchSize v Appconfig.jsna serveru bude náš server dál přijímat zprávy a bude ukládat snímky videa do seznamu. Po dosažení limitu batchSize funkce zavolá funkci nebo soubor, který zpracuje obrázek. V našem případě metoda volá soubor s názvem BatchImageProcessor. cs.
    1. **Processors\BatchImageProcessor.cs**: Tato třída zodpovídá za zpracování imagí. V této ukázce jsme použili model klasifikace obrázků. Pro každý obrázek, který se zpracuje, se používá následující algoritmus:

        1. Převeďte obrázek v bajtovém poli ke zpracování. Viz metoda: `GetBytes(Bitmap image)`
        
            Vzorový procesor, který používáme, podporuje jenom obrázek s kódováním JPG a žádný jako pixelový formát. V případě, že vlastní procesor podporuje jiné kódování a/nebo formát, aktualizujte `IsMediaFormatSupported` metodu třídy procesor.
        1. Pomocí [třídy ColorMatrix](/dotnet/api/system.drawing.imaging.colormatrix?preserve-view=true&view=dotnet-plat-ext-3.1)Převeďte obrázek na šedý stupnici. Viz metoda: `ToGrayScale(Image source)` .
        1. Až získáme obrázek se šedým škálováním, vypočítáme průměrnou velikost šedých bajtů.
        1. Pokud průměrná hodnota < 127, klasifikujeme Image jako "tmavě", jinak je klasifikujme jako "Light" s hodnotou spolehlivosti jako 1,0. Viz metoda: `ProcessImage(List<Image> images)` .

    Můžete přidat vlastní logiku procesoru buď úpravou této třídy, nebo přidáním nové třídy a implementací metody:

    ```
    IEnumerable<Inference> ProcessImage(List<Image> images) 
    ```

    Po přidání nové třídy bude nutné aktualizovat MediaGraphExtensionService. cs, aby vytvořila instanci vaší třídy a vyvolala metodu ProcessImage pro spuštění logiky zpracování. 

## <a name="connect-with-live-video-analytics-module"></a>Propojit s modulem Live video Analytics

Teď, když jste vytvořili modul rozšíření gRPC, teď vytvoříme a nasadíme topologii mediálního grafu.

1. Pomocí Visual Studio Code se přihlaste k Docker podle [těchto pokynů](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) .
1. V Visual Studio Code, přejít na src/Edge. Zobrazí se soubor. ENV a některé soubory šablon nasazení.

    Šablona nasazení odkazuje na manifest nasazení hraničního zařízení. Obsahuje některé zástupné hodnoty. Soubor. env obsahuje hodnoty pro tyto proměnné.
    
    Pak vyberte sestavení a nabízená IoT Edge řešení. Pro tento krok použijte src/Edge/deployment.grpc.template.js.
        
    :::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/build-push-iot-edge-solution.png" alt-text="Propojit s modulem Live video Analytics":::
    
    Tato akce vytvoří modul serveru grpc a vloží image do vašeho Azure Container Registry.
    Ověřte, zda máte proměnné prostředí CONTAINER_REGISTRY_USERNAME_myacr a CONTAINER_REGISTRY_PASSWORD_myacr definovány v souboru. env.
1. Přejít do složky src/Cloud-to-Device-Console-App Tady vidíte appsettings.jsv souboru a několik dalších souborů:

    * C2D-Console-App. csproj – soubor projektu pro Visual Studio Code.
    * operations.jsna seznam operací, které má program spustit.
    * Program. cs – ukázkový kód programu Tento kód:

        * Načte nastavení aplikace.
        * Vyvolá přímé metody, které zveřejňuje živá analýza videa v modulu IoT Edge. Pomocí modulu můžete analyzovat živé datové proudy videa vyvoláním jeho [přímých metod](direct-methods.md).
        * Pozastaví, aby bylo možné kontrolovat výstup programu v okně terminálu a prozkoumávat události vygenerované modulem v okně výstup.
        * Vyvolá přímé metody pro vyčištění prostředků.
1. Upravit operations.jsv souboru:

    * Změňte odkaz na topologii grafu:

        * `"topologyUrl" : https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json`
        * V části GraphInstanceSet upravte název topologie grafu tak, aby odpovídala hodnotě z předchozího odkazu:<br/>`"topologyName": "InferencingWithGrpcExtension"`
        * V části GraphTopologyDelete upravte název:<br/>`"name": "InferencingWithGrpcExtension"`

            Topologie (například `https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json` ) musí definovat adresu rozšíření:
    * Parametr adresy rozšíření

        ```
        {
            "name": "grpcExtensionAddress",
            "type": "String",
            "description": "gRPC LVA Extension Address",
            "default": "https://<REPLACE-WITH-IP-OR-CONTAINER-NAME>/score"
        },
        ```
    * Konfigurace

        ```
        {
            "@apiVersion": "1.0",
            "name": "TopologyName",
            "properties": {
            "processors": [
              {
                "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
                "name": "grpcExtension",
                "endpoint": {
                  "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                  "url": "${grpcExtensionAddress}",
                  "credentials": {
                    "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                    "username": "${grpcExtensionUserName}",
                    "password": "${grpcExtensionPassword}"
                  }
                },
                "dataTransfer": {
                        "mode": "sharedMemory",
                        "SharedMemorySizeMiB": "5"
                },
                "image": {
                  "scale": {
                    "mode": "${imageScaleMode}",
                    "width": "${frameWidth}",
                    "height": "${frameHeight}"
                  },
                  "format": {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatEncoded",
                    "encoding": "${imageEncoding}",
                    "quality": "${imageQuality}"
                  }
                }
            ]
          }
        }
        ```
    
## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generování a nasazení manifestu nasazení IoT Edge

Manifest nasazení definuje, které moduly jsou nasazeny do hraničního zařízení a nastavení konfigurace pro tyto moduly. Pomocí těchto kroků vygenerujte manifest ze souboru šablony a potom ho nasaďte do hraničního zařízení.
Tento krok vytvoří manifest nasazení IoT Edge v umístění src/Edge/config/deployment.grpc.amd64.jsna. Klikněte pravým tlačítkem na tento soubor a vyberte **vytvořit nasazení pro jedno zařízení**.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/create-deployment-single-device.png" alt-text="Generování a nasazení manifestu nasazení IoT Edge":::

Dále Visual Studio Code požádá o výběr zařízení IoT Hub. Vyberte zařízení IoT Edge, které by mělo být lva-Sample-Device.
V této fázi se spustilo nasazení hraničních modulů do zařízení IoT Edge. Během přibližně 30 sekund aktualizujte IoT Hub Azure v části vlevo dole v Visual Studio Code. Měl by se zobrazit, že byl nasazen nový modul s názvem lvaExtension.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv-how-to/devices.png" alt-text="Byl nasazen nový modul s názvem lvaExtension.":::

## <a name="next-steps"></a>Další kroky

Postupujte podle kroků **Příprava na monitorování událostí** zmíněných v části [Analýza živého videa pomocí](use-your-model-quickstart.md) rychlého startu modelu a spusťte ukázku a interpretujte výsledky. Podívejte se také na naše ukázkové topologie gRPC: [gRPCExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json), [CVRWithGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-grpcExtension/topology.json), [EVRtoAssetsByGrpcExtension a [EVROnMotionPlusGrpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-with-grpcExtension/topology.json).