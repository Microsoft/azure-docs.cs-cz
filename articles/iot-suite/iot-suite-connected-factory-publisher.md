---
title: "Používat vydavatele OPC objekt pro vytváření připojení Azure IoT Suite | Microsoft Docs"
description: "Postup vytvoření a nasazení odkaz implementace připojeného objektu factory OPC vydavatele."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 934f4deb3139c136c871ab0125ba45267e1d1b05
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2017
---
# <a name="opc-publisher-for-azure-iot-edge"></a>OPC vydavatele pro Azure IoT Edge

Tento článek popisuje, jak používat odkaz na implementaci OPC vydavatele. Odkaz na implementaci ukazuje, jak používat Azure IoT Edge na:

- Připojte k existující OPC UA serverům.
- Publikování JSON kódovaný telemetrická data z těchto serverů v OPC UA *Pub nebo Sub* formátu, pomocí datové části JSON do Azure IoT Hub. Můžete používat kterýkoli z přenosové protokoly, které podporuje Azure IoT okraj.

Tento referenční aplikace zahrnuje:

- OPC UA *klienta* pro připojení k existující OPC UA servery ve vaší síti.
- OPC UA *server* naslouchá na portu 62222, který můžete použít ke správě, co je publikována.

Aplikace je implementovaná pomocí .NET Core a můžete spustit na platformách podporovaných aplikací .NET Core.

Vydavatel implementuje logika opakovaných pokusů navazuje připojení ke koncovým bodům. Vydavatel očekává koncové body reagovat během zadaného počtu zachovat aktivní žádosti. Tato logika opakovaných pokusů umožňuje vydavatele ke zjištění stavu třeba při výpadku napájení OPC UA serveru.

V každém odlišné publikování intervalu k serveru OPC UA vytvoří samostatné předplatné, jehož prostřednictvím jsou aktualizovány všechny uzly s Tento interval publikování.

Aby se snížilo zatížení sítě, podporuje vydavatele dávkování data zasílaná do služby IoT Hub. Batch je odeslat do služby IoT Hub, pouze v případě, že je dosaženo velikost nakonfigurované balíčku.

Tato aplikace používá základů OPC OPC UA odkaz zásobníku a proto licenční omezení platí. Navštivte http://opcfoundation.github.io/UA-.NETStandardLibrary/ pro OPC UA dokumentaci a licenční smlouvy.

Můžete najít zdrojový kód OPC vydavatele v [OPC vydavatele pro Azure IoT Edge](https://github.com/Azure/iot-edge-opc-publisher) úložiště GitHub.

## <a name="prerequisites"></a>Požadavky

Chcete-li sestavit aplikaci, musíte [.NET Core SDK 1.1.](https://docs.microsoft.com/dotnet/core/sdk) pro operační systém.

## <a name="build-the-application"></a>Sestavení aplikace

### <a name="as-native-windows-application"></a>Jako nativní aplikace pro systém Windows

Otevřete `OpcPublisher.sln` projektu s Visual Studio 2017 a sestavte řešení zasažení F7.

### <a name="as-docker-container"></a>Jako kontejner Docker

Chcete-li sestavit aplikaci jako kontejner Windows Docker, použijte `Dockerfile.Windows` konfigurační soubor.

Chcete-li sestavit aplikaci jako kontejner Linux Docker, použijte `Dockerfile` konfigurační soubor.

Na kořenovém adresáři úložiště na vývojovém počítači zadejte následující příkaz v okně konzoly:

`docker build -f <docker-configfile-to-use> -t <your-container-name> .`

`-f` Možnost `docker build` je volitelná a ve výchozím nastavení se použít `Dockerfile` konfigurační soubor.

Docker můžete taky vytvořit přímo z úložiště git. Můžete vytvořit kontejner Linux Docker pomocí následujícího příkazu:

`docker build -t <your-container-name> .https://github.com/Azure/iot-edge-opc-publisher`

## <a name="configure-the-opc-ua-nodes-to-publish"></a>Konfigurace uzlů OPC UA k publikování

Konfigurace uzlů, které OPC UA, je nutné mít jejich hodnoty publikovat do služby Azure IoT Hub, vytvořte soubor JSON formátovaný konfigurace. Výchozí název pro tento konfigurační soubor je `publishednodes.json`. Aplikace, aktualizace a uloží tento konfigurační soubor, když používá OPC UA serveru metody **PublishNode** nebo **UnpublishNode**.

Syntaxe v souboru konfigurace je následující:

```json
[
    {
        // example for an EnpointUrl is: opc.tcp://win10iot:51210/UA/SampleServer
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "OpcNodes": [
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised publishing interval).
            {
                // The identifier specifies the NamespaceUri and the node identifier in XML notation as specified in Part 6 of the OPC UA specification in the XML Mapping section.
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258"
            },
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with an OPC publishing interval of 4 seconds.
            // Publisher will use for each dinstinct publishing interval (of nodes on the same EndpointUrl) a separate subscription. All nodes without a publishing interval setting,
            // will be on the same subscription and the OPC UA stack will publish with the lowest sampling interval of a node.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                "OpcPublishingInterval": 4000
            },
            // Publisher will request the server at EndpointUrl to sample the node with the given sampling interval of 1 second
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised interval).
            // If the OPC publishing interval is set to a lower value, Publisher will adjust the OPC publishing interval of the subscription to the OPC sampling interval value.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                // the OPC sampling interval to use for this node.
                "OpcSamplingInterval": 1000
            }
        ]
    },

    // the format below is only supported for backward compatibility. you need to ensure that the
    // OPC UA server on the configured EndpointUrl has the namespaceindex you expect with your configuration.
    // please use the ExpandedNodeId syntax instead.
    {
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "NodeId": {
            "Identifier": "ns=0;i=2258"
        }
    }
    // please consult the OPC UA specification for details on how OPC monitored node sampling interval and OPC subscription publishing interval settings are handled by the OPC UA stack.
    // the publishing interval of the data to Azure IoT Hub is controlled by the command line settings (or the default: publish data to IoT Hub at least each 1 second).
]
```

## <a name="run-the-application"></a>Spuštění aplikace

### <a name="command-line-options"></a>Možnosti příkazového řádku

Pokud chcete zobrazit úplný využití aplikace, použijte `--help` možnost příkazového řádku. Následující příklad ukazuje strukturu příkazu:

```cmd/sh
OpcPublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]
```

`applicationname`je název aplikace OPC UA používat. Tento parametr je povinný. Název aplikace se také používá k registraci vydavatele v registru zařízení služby IoT Hub.

`IoT Hubconnectionstring`je připojovací řetězec služby IoT Hub vlastníka. Tento parametr je volitelný.

Podporovány jsou následující možnosti:

```cmd/sh
--pf, --publishfile=VALUE
  the filename to configure the nodes to publish.
    Default: './publishednodes.json'
--sd, --shopfloordomain=VALUE
  the domain of the shopfloor. if specified this
    domain is appended (delimited by a ':' to the '
    ApplicationURI' property when telemetry is
    sent to IoT Hub.
    The value must follow the syntactical rules of a
    DNS hostname.
    Default: not set
--sw, --sessionconnectwait=VALUE
  specify the wait time in seconds publisher is
    trying to connect to disconnected endpoints and
    starts monitoring unmonitored items
    Min: 10
    Default: 10
--vc, --verboseconsole=VALUE
  the output of publisher is shown on the console.
    Default: False
--ih, --IoT Hubprotocol=VALUE
  the protocol to use for communication with Azure
    IoT Hub (allowed values: Amqp, Http1, Amqp_
    WebSocket_Only, Amqp_Tcp_Only, Mqtt, Mqtt_
    WebSocket_Only, Mqtt_Tcp_Only).
    Default: Mqtt
--ms, --IoT Hubmessagesize=VALUE
  the max size of a message which can be send to
    IoT Hub. when telemetry of this size is available
    it will be sent.
    0 will enforce immediate send when telemetry is
    available
    Min: 0
    Max: 256 * 1024
    Default: 4096
--si, --IoT Hubsendinterval=VALUE
  the interval in seconds when telemetry should be
    send to IoT Hub. If 0, then only the
    IoT Hubmessagesize parameter controls when
    telemetry is sent.
    Default: '1'
--lf, --logfile=VALUE
  the filename of the logfile to use.
    Default: './Logs/<applicationname>.log.txt'
--pn, --portnum=VALUE
  the server port of the publisher OPC server
    endpoint.
    Default: 62222
--pa, --path=VALUE
  the endpoint URL path part of the publisher OPC
    server endpoint.
    Default: '/UA/Publisher'
--lr, --ldsreginterval=VALUE
  the LDS(-ME) registration interval in ms. If 0,
    then the registration is disabled.
    Default: 0
--ot, --operationtimeout=VALUE
  the operation timeout of the publisher OPC UA
    client in ms.
    Default: 120000
--oi, --opcsamplinginterval=VALUE
  the publisher is using this as default value in
    milliseconds to request the servers to sample
    the nodes with this interval
    this value might be revised by the OPC UA
    servers to a supported sampling interval.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a negative value will set the sampling interval
    to the publishing interval of the subscription
    this node is on.
    0 will configure the OPC UA server to sample in
    the highest possible resolution and should be
    taken with care.
    Default: 1000
--op, --opcpublishinginterval=VALUE
  the publisher is using this as default value in
    milliseconds for the publishing interval setting
    of the subscriptions established to the OPC UA
    servers.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a value less than or equal zero will let the
    server revise the publishing interval.
    Default: 0
--ct, --createsessiontimeout=VALUE
  specify the timeout in seconds used when creating
    a session to an endpoint. On unsuccessful
    connection attemps a backoff up to 5 times the
    specified timeout value is used.
    Min: 1
    Default: 10
--ki, --keepaliveinterval=VALUE
  specify the interval in seconds the publisher is
    sending keep alive messages to the OPC servers
    on the endpoints it is connected to.
    Min: 2
    Default: 2
--kt, --keepalivethreshold=VALUE
  specify the number of keep alive packets a server
    can miss, before the session is disconneced
    Min: 1
    Default: 5
--st, --opcstacktracemask=VALUE
  the trace mask for the OPC stack. See github OPC .
    NET stack for definitions.
    To enable IoT Hub telemetry tracing set it to 711.
    Default: 285  (645)
--as, --autotrustservercerts=VALUE
  the publisher trusts all servers it is
    establishing a connection to.
    Default: False
--tm, --trustmyself=VALUE
  the publisher certificate is put into the trusted
    certificate store automatically.
    Default: True
--fd, --fetchdisplayname=VALUE
  enable to read the display name of a published
    node from the server. this will increase the
    runtime.
    Default: False
--at, --appcertstoretype=VALUE
  the own application cert store type.
    (allowed values: Directory, X509Store)
    Default: 'X509Store'
--ap, --appcertstorepath=VALUE
  the path where the own application cert should be
    stored
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/own'
--tt, --trustedcertstoretype=VALUE
  the trusted cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--tp, --trustedcertstorepath=VALUE
  the path of the trusted cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/trusted'
--rt, --rejectedcertstoretype=VALUE
  the rejected cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--rp, --rejectedcertstorepath=VALUE
  the path of the rejected cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/rejected'
--it, --issuercertstoretype=VALUE
  the trusted issuer cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--ip, --issuercertstorepath=VALUE
  the path of the trusted issuer cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/issuers'
--dt, --devicecertstoretype=VALUE
  the IoT Hub device cert store type.
    (allowed values: Directory, X509Store)
    Default: X509Store
--dp, --devicecertstorepath=VALUE
  the path of the iot device cert store
    Default Default (depends on store type):
    X509Store: 'My'
    Directory: 'CertificateStores/IoT Hub'
-h, --help
  show this message and exit
```

K řízení aplikace můžete použít následující proměnné prostředí:
- `_HUB_CS`: Nastaví připojovací řetězec služby IoT Hub vlastníka
- `_GW_LOGP`: Nastaví název souboru tento soubor protokolu
- `_TPC_SP`: Nastaví cestu k uložení certifikáty důvěryhodné stanice
- `_GW_PNFP`: Nastaví název konfiguračního souboru publikování

Argumenty příkazového řádku zadáte nastavení proměnné prostředí.

Obvykle zadat připojovací řetězec služby IoT Hub vlastníka pouze při prvním spuštění aplikace. Připojovací řetězec je šifrovaný a uložen v úložišti certifikátů platformu.

Na následující volání je připojovací řetězec čtení z úložiště certifikátů platformy a znovu použít. Pokud zadáte připojovacího řetězce při každém spuštění, je odebrat a znovu vytvoří pokaždé, když zařízení v registru zařízení služby IoT Hub.

### <a name="native-on-windows"></a>Nativní v systému Windows

Chcete-li spustit aplikaci nativně v systému Windows, otevřete `OpcPublisher.sln` projektu s Visual Studio 2017, sestavte řešení a publikujete ji. Aplikaci můžete spustit v **cílový adresář** jste publikovali s:

```cmd
dotnet OpcPublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-self-built-container"></a>Použít samoobslužné vytvořený kontejner

Ke spuštění aplikace v automatických integrovaný kontejneru, sestavit a pak spusťte vlastní kontejneru:

```cmd/sh
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-container-from-hubdockercom"></a>Použít kontejner z hub.docker.com

Na DockerHub je k dispozici předem kontejner. Chcete-li jej spustit, spusťte následující příkaz:

```cmd/sh
docker run microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="important-when-using-a-container"></a>Důležité při použití kontejner

#### <a name="access-to-the-publisher-opc-ua-server"></a>Přístup k serveru vydavatele OPC UA

Vydavatel OPC UA serveru ve výchozím nastavení naslouchá na portu 62222. Chcete-li zveřejnit tento příchozí port v kontejneru, budete muset použít `docker run` možnost `-p`:

```cmd/sh
docker run -p 62222:62222 microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="enable-intercontainer-name-resolution"></a>Povolit intercontainer překlad

Chcete-li povolit překlad adres z kontejneru uvnitř jiné kontejnery, postupujte takto:

- Vytvoření sítě most docker definovaný uživatelem.
- Připojení k síti pomocí kontejneru `--network`možnost.
- Přiřadit kontejneru názvu pomocí `--name` možnost.

Následující příklad ukazuje možnosti konfigurace:

```cmd/sh
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Kontejner teď dosažitelný z jiných kontejnerů přes síť pomocí názvu `publisher`.

#### <a name="assign-a-hostname"></a>Přiřadit název hostitele

Vydavatel používá název hostitele počítače, který je spuštěn na pro generování certifikátů a koncového bodu. Docker zvolí náhodný název hostitele, pokud jste nenastavili s `-h` možnost. Zde příklad se nastavit interní název hostitele kontejner, aby `publisher`:

```cmd/sh
docker run -h publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="using-bind-mounts-shared-filesystem"></a>Pomocí vazby připojí (sdílené systému souborů)

V některých scénářích budete chtít přečíst informace o konfiguraci z nebo na hostiteli místo použití systému souborů kontejner zápisu souborů protokolu do umístění. Pro konfiguraci tohoto chování, použijte `-v` možnost `docker run` v režimu připojení vazby. Například:

```cmd/sh
-v //D/docker:/build/out/Logs
-v //D/docker:/build/out/CertificateStores
-v //D/docker:/shared
-v //D/docker:/root/.dotnet/corefx/cryptography/x509stores
```

#### <a name="store-for-x509-certificates"></a>Úložiště pro X509 certifikáty

Ukládání X509 certifikáty nefunguje s připojení zařízení vazby, protože oprávnění cestu k úložišti musí být `rw` pro vlastníka. Místo toho je nutné použít `-v` možnost `docker run` v režimu svazku.

## <a name="debug-the-application"></a>Ladění aplikace

### <a name="native-on-windows"></a>Nativní v systému Windows

Otevřete `OpcPublisher.sln` projektu s Visual Studio 2017 a spuštění ladění aplikace pomocí stále mačkat F5.

### <a name="in-a-docker-container"></a>V kontejneru docker

Visual Studio 2017 podporuje ladění aplikací v kontejner Docker pomocí `docker-compose`. Tato metoda však neumožňuje předat parametry příkazového řádku.

Alternativou ladění možnost, která podporuje VS2017 je ladění přes `ssh`. Můžete použít konfigurační soubor docker sestavení `Dockerfile.ssh` v kořenovém úložišti vytvořit kontejner SSH povoleno:

```cmd/sh
docker build -f .\Dockerfile.ssh -t publisherssh .
```

Nyní můžete spustit kontejner, aby ladění vydavatele:

```cmd/sh
docker run -it publisherssh
```

V kontejneru, musíte spustit **ssh** démon ručně:

```cmd/sh
service ssh start
```

V tomto okamžiku můžete vytvořit **ssh** relace jako uživatel `root` heslem `Passw0rd`.

Příprava na ladění aplikace v kontejneru, proveďte následující kroky:

1. Na straně hostitele, vytvořte `launch.json` souboru:

    ```json
    {
      "version": "0.2.0",
      "adapter": "<path>\\plink.exe",
      "adapterArgs": "root@localhost -pw Passw0rd -batch -T ~/vsdbg/vsdbg --interpreter=vscode",
      "languageMappings": {
        "C#": {
          "languageId": "3F5162F8-07C6-11D3-9053-00C04FA302A1",
          "extensions": [ "*" ]
        }
      },
      "exceptionCategoryMappings": {
        "CLR": "449EC4CC-30D2-4032-9256-EE18EB41B62B",
        "MDA": "6ECE07A9-0EDE-45C4-8296-818D8FC401D4"
      },
      "configurations": [
        {
          "name": ".NET Core Launch",
          "type": "coreclr",
          "cwd": "~/publisher",
          "program": "Opc.Ua.Publisher.dll",
          "args": "<put-the-publisher-command-line-options-here>",

          "request": "launch"
        }
      ]
    }
    ```

1. Sestavte projekt a publikujete ho v adresáři podle vašeho výběru.

1. Pomocí některého nástroje, jako například `WinSCP` zkopírovat publikované soubory do adresáře `/root/publisher` v kontejneru. Pokud chcete použít k jinému adresáři, aktualizovat `cdw` vlastnost `launch.json` souboru.

Nyní můžete začít ladění pomocí následujícího příkazu v příkazovém okně Visual Studio:

```cmd
DebugAdapterHost.Launch /LaunchJson:"<path-to-the-launch.json-file-you-saved>"
```

## <a name="next-steps"></a>Další kroky

Navrhované A dalším krokem je další postup [nasazení brány v systému Windows nebo Linux připojené objekt pro vytváření předkonfigurovaného řešení](iot-suite-connected-factory-gateway-deployment.md).