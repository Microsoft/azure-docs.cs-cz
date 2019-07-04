---
title: Spustit vydavatele OPC – Azure | Dokumentace Microsoftu
description: Spuštění vydavatele OPC
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 0af4fcb6ea87069f3dea73f33828abd4f4bb06f4
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450762"
---
# <a name="run-opc-publisher"></a>Spuštění vydavatele OPC

Tento článek popisuje, jak spustit ladění ad vydavatel OPC. Také řeší důležité informace o výkonu a paměti.

## <a name="command-line-options"></a>Možnosti příkazového řádku

Použití aplikace se zobrazí při použití `--help` možnost příkazového řádku takto:

```sh/cmd
Current directory is: /appdata
Log file is: <hostname>-publisher.log
Log level is: info

OPC Publisher V2.3.0
Informational version: V2.3.0+Branch.develop_hans_methodlog.Sha.0985e54f01a0b0d7f143b1248936022ea5d749f9

Usage: opcpublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]

OPC Edge Publisher to subscribe to configured OPC UA servers and send telemetry to Azure IoT Hub.
To exit the application, just press CTRL-C while it is running.

applicationname: the OPC UA application name to use, required
                  The application name is also used to register the publisher under this name in the
                  IoT Hub device registry.

IoT Hubconnectionstring: the IoT Hub owner connectionstring, optional

There are a couple of environment variables which can be used to control the application:
_HUB_CS: sets the IoT Hub owner connectionstring
_GW_LOGP: sets the filename of the log file to use
_TPC_SP: sets the path to store certificates of trusted stations
_GW_PNFP: sets the filename of the publishing configuration file

Command line arguments overrule environment variable settings.

Options:
      --pf, --publishfile=VALUE
                              the filename to configure the nodes to publish.
                                Default: '/appdata/publishednodes.json'
      --tc, --telemetryconfigfile=VALUE
                              the filename to configure the ingested telemetry
                                Default: ''
  -s, --site=VALUE           the site OPC Publisher is working in. if specified
                                this domain is appended (delimited by a ':' to
                                the 'ApplicationURI' property when telemetry is
                                sent to IoT Hub.
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --ic, --iotcentral     publisher will send OPC UA data in IoTCentral
                                compatible format (DisplayName of a node is used
                                as key, this key is the Field name in IoTCentral)
                                . you need to ensure that all DisplayName's are
                                unique. (Auto enables fetch display name)
                                Default: False
      --sw, --sessionconnectwait=VALUE
                              specify the wait time in seconds publisher is
                                trying to connect to disconnected endpoints and
                                starts monitoring unmonitored items
                                Min: 10
                                Default: 10
      --mq, --monitoreditemqueuecapacity=VALUE
                              specify how many notifications of monitored items
                                can be stored in the internal queue, if the data
                                can not be sent quick enough to IoT Hub
                                Min: 1024
                                Default: 8192
      --di, --diagnosticsinterval=VALUE
                              shows publisher diagnostic info at the specified
                                interval in seconds (need log level info).
                                -1 disables remote diagnostic log and diagnostic
                                output
                                0 disables diagnostic output
                                Default: 0
      --ns, --noshutdown=VALUE
                              same as runforever.
                                Default: False
      --rf, --runforever     publisher can not be stopped by pressing a key on
                                the console, but will run forever.
                                Default: False
      --lf, --logfile=VALUE  the filename of the logfile to use.
                                Default: './<hostname>-publisher.log'
      --lt, --logflushtimespan=VALUE
                              the timespan in seconds when the logfile should be
                                flushed.
                                Default: 00:00:30 sec
      --ll, --loglevel=VALUE the loglevel to use (allowed: fatal, error, warn,
                                info, debug, verbose).
                                Default: info
        --ih, --IoT Hubprotocol=VALUE
                              the protocol to use for communication with IoT Hub (
                                allowed values: Amqp, Http1, Amqp_WebSocket_Only,
                                  Amqp_Tcp_Only, Mqtt, Mqtt_WebSocket_Only, Mqtt_
                                Tcp_Only) or IoT EdgeHub (allowed values: Mqtt_
                                Tcp_Only, Amqp_Tcp_Only).
                                Default for IoT Hub: Mqtt_WebSocket_Only
                                Default for IoT EdgeHub: Amqp_Tcp_Only
      --ms, --IoT Hubmessagesize=VALUE
                              the max size of a message which can be send to
                                IoT Hub. when telemetry of this size is available
                                it will be sent.
                                0 will enforce immediate send when telemetry is
                                available
                                Min: 0
                                Max: 262144
                                Default: 262144
      --si, --IoT Hubsendinterval=VALUE
                              the interval in seconds when telemetry should be
                                send to IoT Hub. If 0, then only the
                                IoT Hubmessagesize parameter controls when
                                telemetry is sent.
                                Default: '10'
      --dc, --deviceconnectionstring=VALUE
                              if publisher is not able to register itself with
                                IoT Hub, you can create a device with name <
                                applicationname> manually and pass in the
                                connectionstring of this device.
                                Default: none
  -c, --connectionstring=VALUE
                              the IoT Hub owner connectionstring.
                                Default: none
      --hb, --heartbeatinterval=VALUE
                              the publisher is using this as default value in
                                seconds for the heartbeat interval setting of
                                nodes without
                                a heartbeat interval setting.
                                Default: 0
      --sf, --skipfirstevent=VALUE
                              the publisher is using this as default value for
                                the skip first event setting of nodes without
                                a skip first event setting.
                                Default: False
      --pn, --portnum=VALUE  the server port of the publisher OPC server
                                endpoint.
                                Default: 62222
      --pa, --path=VALUE     the enpoint URL path part of the publisher OPC
                                server endpoint.
                                Default: '/UA/Publisher'
      --lr, --ldsreginterval=VALUE
                              the LDS(-ME) registration interval in ms. If 0,
                                then the registration is disabled.
                                Default: 0
      --ol, --opcmaxstringlen=VALUE
                              the max length of a string opc can transmit/
                                receive.
                                Default: 131072
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
      --aa, --autoaccept     the publisher trusts all servers it is
                                establishing a connection to.
                                Default: False
      --tm, --trustmyself=VALUE
                              same as trustowncert.
                                Default: False
      --to, --trustowncert   the publisher certificate is put into the trusted
                                certificate store automatically.
                                Default: False
      --fd, --fetchdisplayname=VALUE
                              same as fetchname.
                                Default: False
      --fn, --fetchname      enable to read the display name of a published
                                node from the server. this will increase the
                                runtime.
                                Default: False
      --ss, --suppressedopcstatuscodes=VALUE
                              specifies the OPC UA status codes for which no
                                events should be generated.
                                Default: BadNoCommunication,
                                BadWaitingForInitialData
      --at, --appcertstoretype=VALUE
                              the own application cert store type.
                                (allowed values: Directory, X509Store)
                                Default: 'Directory'
      --ap, --appcertstorepath=VALUE
                              the path where the own application cert should be
                                stored
                                Default (depends on store type):
                                X509Store: 'CurrentUser\UA_MachineDefault'
                                Directory: 'pki/own'
      --tp, --trustedcertstorepath=VALUE
                              the path of the trusted cert store
                                Default: 'pki/trusted'
      --rp, --rejectedcertstorepath=VALUE
                              the path of the rejected cert store
                                Default 'pki/rejected'
      --ip, --issuercertstorepath=VALUE
                              the path of the trusted issuer cert store
                                Default 'pki/issuer'
      --csr                  show data to create a certificate signing request
                                Default 'False'
      --ab, --applicationcertbase64=VALUE
                              update/set this applications certificate with the
                                certificate passed in as bas64 string
      --af, --applicationcertfile=VALUE
                              update/set this applications certificate with the
                                certificate file specified
      --pb, --privatekeybase64=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as base64 string
      --pk, --privatekeyfile=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as file
      --cp, --certpassword=VALUE
                              the optional password for the PEM or PFX or the
                                installed application certificate
      --tb, --addtrustedcertbase64=VALUE
                              adds the certificate to the applications trusted
                                cert store passed in as base64 string (multiple
                                strings supported)
      --tf, --addtrustedcertfile=VALUE
                              adds the certificate file(s) to the applications
                                trusted cert store passed in as base64 string (
                                multiple filenames supported)
      --ib, --addissuercertbase64=VALUE
                              adds the specified issuer certificate to the
                                applications trusted issuer cert store passed in
                                as base64 string (multiple strings supported)
      --if, --addissuercertfile=VALUE
                              adds the specified issuer certificate file(s) to
                                the applications trusted issuer cert store (
                                multiple filenames supported)
      --rb, --updatecrlbase64=VALUE
                              update the CRL passed in as base64 string to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --uc, --updatecrlfile=VALUE
                              update the CRL passed in as file to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --rc, --removecert=VALUE
                              remove cert(s) with the given thumbprint(s) (
                                multiple thumbprints supported)
      --dt, --devicecertstoretype=VALUE
                              the IoT Hub device cert store type.
                                (allowed values: Directory, X509Store)
                                Default: X509Store
      --dp, --devicecertstorepath=VALUE
                              the path of the iot device cert store
                                Default Default (depends on store type):
                                X509Store: 'My'
                                Directory: 'CertificateStores/IoT Hub'
  -i, --install              register OPC Publisher with IoT Hub and then exits.
                                Default:  False
  -h, --help                 show this message and exit
      --st, --opcstacktracemask=VALUE
                              ignored, only supported for backward comaptibility.
      --sd, --shopfloordomain=VALUE
                              same as site option, only there for backward
                                compatibility
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --vc, --verboseconsole=VALUE
                              ignored, only supported for backward comaptibility.
      --as, --autotrustservercerts=VALUE
                              same as autoaccept, only supported for backward
                                cmpatibility.
                                Default: False
      --tt, --trustedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted cert store will always reside in a
                                directory.
      --rt, --rejectedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the rejected cert store will always reside in a
                                directory.
      --it, --issuercertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted issuer cert store will always
                                reside in a directory.
```

Obvykle je zadat připojovací řetězec služby IoT Hub vlastníka pouze při prvním spuštění aplikace. Připojovací řetězec je zašifrované a uložené v úložišti certifikátů platformy. V pozdější spuštění aplikace načte připojovací řetězec z úložiště certifikátů. Pokud chcete zadat připojovací řetězec při každém běhu, zařízení, který je vytvořen pro aplikaci v registru zařízení služby IoT Hub je odebrat a znovu vytvořit.

## <a name="run-natively-on-windows"></a>Nativně běžet na Windows

Otevřít **opcpublisher.sln** projekt pomocí sady Visual Studio, sestavte řešení a publikujete ji. Aplikaci můžete spustit v **cílový adresář** publikováním následujícím způsobem:

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>Použití sami sestavili kontejneru

Vytvoření vlastní kontejner, spusťte ji následujícím způsobem:

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Použití kontejneru z registru kontejneru Microsoft

V Microsoftu Container Registry je k dispozici předem připravených kontejneru. Spusťte následujícím způsobem:

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Zkontrolujte [Docker Hubu](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) zobrazíte podporované operační systémy a architektury procesoru. Pokud je podporován operační systém a procesor architektury, Docker automaticky vybere ten správný kontejner.

## <a name="run-as-an-azure-iot-edge-module"></a>Spustit jako modul služby Azure IoT Edge

Vydavatel OPC je připravená k použití jako [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge) modulu. Při použití vydavatele OPC jako modul IoT Edge jediný podporovaný přenosové protokoly jsou **Amqp_Tcp_Only** a **Mqtt_Tcp_Only**.

Přidáte modul vydavatele OPC pro vaše nasazení IoT Edge přejděte na nastavení služby IoT Hub na webu Azure Portal a proveďte následující kroky:

1. Přejděte na **IoT Edge** a vytvořte nebo vyberte zařízení IoT Edge.
1. Vyberte **Nastavit moduly**.
1. Vyberte **přidat** pod **moduly nasazení** a potom **modul IoT Edge**.
1. V **název** zadejte **vydavatele**.
1. V **identifikátor URI Image** zadejte `mcr.microsoft.com/iotedge/opc-publisher:<tag>`
1. Dostupné značky můžete najít na [Docker Hubu](https://hub.docker.com/_/microsoft-iotedge-opc-publisher)
1. Vložte následující kód JSON do **možnosti vytvoření kontejneru** pole:

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    Tato konfigurace nakonfiguruje IoT Edge a spusťte kontejner volá **vydavatele** použití vydavatele OPC image. Název hostitele systému kontejneru je nastavena na **vydavatele**. Vydavatel OPC je volána s argumentem příkazového řádku následující: `--aa`. Pomocí této možnosti důvěřuje vydavatel OPC certifikáty serverů OPC UA, který se připojuje ke službě. Můžete použít všechny parametry příkazového řádku vydavatel OPC. Jediným omezením je velikost **možnosti vytvoření kontejneru** podporuje IoT Edge.

1. Ostatní nastavení ponechte beze změny a vyberte **Uložit**.
1. Pokud chcete zpracovat výstup vydavatele OPC místně se jiný modul IoT Edge, přejděte zpátky k **nastavit moduly** stránky. Pak přejděte **určení tras** karta a přidat novou trasu vypadá podobně jako následující kód JSON:

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. Zpátky **nastavit moduly** stránce **Další**, dokud se nedostanete na poslední stránku konfigurace.
1. Vyberte **odeslat** odeslat konfiguraci na hraničních zařízeních IoT.
1. Pokud jste začali IoT Edge na hraniční zařízení a kontejneru dockeru **vydavatele** běží, si můžete prohlédnout výstup protokolu vydavatel OPC s použitím `docker logs -f publisher` nebo když Nahlédnete souboru protokolu. V předchozím příkladu se soubor protokolu je vyšší než `d:\iiotegde\publisher-publisher.log`. Můžete také použít [iot-edge – opc vydavatel diagnostický nástroj, který](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics).

### <a name="make-the-configuration-files-accessible-on-the-host"></a>Ujistěte se, konfigurační soubory dostupné na hostiteli

Chcete-li IoT Edge module konfiguračních souborů v hostitelském systému souborů k dispozici, použijte následující **možnosti vytvoření kontejneru**. V následujícím příkladu je nasazení pomocí Linuxu kontejnery pro Windows:

```json
{
    "Hostname": "publisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "d:/iiotedge:/appdata"
        ]
    }
}
```

S těmito možnostmi vydavatel OPC načte uzly by měli zveřejnit ze souboru `./pn.json` kontejneru pracovní adresář nastaven na `/appdata` při spuštění. S těmito nastaveními vydavatel OPC načte soubor `/appdata/pn.json` z kontejneru zobrazíte její konfiguraci. Bez `--pf` možnost vydavatele OPC se pokusí přečíst výchozí konfigurační soubor `./publishednodes.json`.

Soubor protokolu s použitím výchozí název `publisher-publisher.log`, jsou zapsána do `/appdata` a `CertificateStores` adresář se vytvoří taky v tomto adresáři.

Chcete-li tyto soubory k dispozici do hostitelského souborového systému, konfigurace kontejneru vyžaduje svazek připojení vazby. `d://iiotedge:/appdata` Vazby mapuje adresář `/appdata`, což je aktuální pracovní adresář při spuštění kontejneru do hostitele adresáře `d://iiotedge`. Bez této možnosti se ukládají data bez souboru při dalším spuštění kontejneru.

Pokud používáte kontejnery Windows a pak syntaxe `Binds` parametru se liší. Při spuštění kontejneru pracovní adresář nastaven `c:\appdata`. Do konfiguračního souboru v adresáři `d:\iiotedge`na hostitele, zadejte následující mapování v `HostConfig` části:

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Pokud používáte Linuxové kontejnery v Linuxu, syntaxe `Binds` parametru se liší znovu. Při spuštění kontejneru pracovní adresář nastaven `/appdata`. Do konfiguračního souboru v adresáři `/iiotedge` na hostitele, zadejte následující mapování v `HostConfig` části:

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>Informace týkající se použití kontejneru

Následující části uvádějí některé co je potřeba vzít v úvahu při použití kontejneru:

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>Přístup k serveru OPC vydavatele OPC UA

Ve výchozím nastavení server OPC vydavatele OPC UA naslouchá na portu 62222. Pokud chcete zpřístupnit tento příchozí port v kontejneru, použijte následující příkaz:

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>Povolit intercontainer překlad

Pokud chcete povolit překlad z v rámci kontejneru do jiných kontejnerů, vytvořte uživatele definování síťového mostu docker a připojte se k této síti pomocí kontejneru `--network` možnost. Také přiřadit kontejneru názvem pomocí `--name` možnost následujícím způsobem:

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Kontejner je teď dostupný prostřednictvím názvu `publisher` podle dalších kontejnerů ve stejné síti.

### <a name="access-other-systems-from-within-the-container"></a>Přístup k jiné systémy z v rámci kontejneru

Jiné kontejnery lze dosáhnout pomocí parametrů, které jsou uvedené v předchozí části. Pokud operační systém, který je hostitelem Dockeru je server DNS, nastanou při přístupu k všechny systémy, které jsou známé DNS funguje.

V sítích využívajících službu překladu názvů NetBIOS, povolení přístupu k jiným systémům spuštěním váš kontejner `--add-host` možnost. Tato možnost efektivně přidá položku do kontejneru soubor hostitele:

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>Přiřaďte název hostitele

Vydavatel OPC používá název hostitele počítače, který je spuštěn na pro generování certifikátů a koncový bod. Docker zvolí náhodný název hostitele, pokud jeden není nastaven `-h` možnost. Následující příklad ukazuje, jak nastavit interní název hostitele kontejneru `publisher`:

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>Použít připojení bind (sdílený systém souborů)

Namísto použití systému souborů kontejneru, můžete si zvolit hostitele systému souborů k ukládání informací o konfiguraci a soubory protokolu. Chcete-li tuto možnost nakonfigurujte, použijte `-v` možnost `docker run` v režimu připojení vazby.

## <a name="opc-ua-x509-certificates"></a>Certifikáty OPC UA X.509

OPC UA používá certifikáty X.509, ověření klienta OPC UA a serveru při navázání připojení a k šifrování komunikace mezi nimi. Vydavatel OPC používá ke správě všech certifikátů úložišť certifikátů udržované sady OPC UA. Při spuštění vydavatel OPC zkontroluje, jestli existuje certifikát pro sebe sama. Pokud neexistuje žádný certifikát v úložišti certifikátů a jeden není jednou předané v příkazovém řádku, vydavatel OPC vytvoří certifikát podepsaný svým držitelem. Další informace najdete v tématu **InitApplicationSecurityAsync** metoda ve `OpcApplicationConfigurationSecurity.cs`.

Certifikáty podepsané svým držitelem neposkytují zabezpečení, protože nejsou podepsány důvěryhodné certifikační Autority.

Vydavatel OPC poskytuje možností příkazového řádku:

- Načtěte informace o žádosti o podepsání certifikátu z aktuální aplikace certifikát používaný službou vydavatel OPC.
- Vydavatel OPC zřízení se certifikační Autorita certifikát podepsaný.
- Certifikát podepsaný zřízení vydavatele OPC se nový pár klíčů a odpovídající certifikační Autority.
- Přidáte certifikáty důvěryhodné partnera nebo úložiště certifikátů důvěryhodných vystavitelů.
- Přidejte seznamu odvolaných certifikátů.
- Odebrat certifikát od důvěryhodné partnera nebo úložiště certifikátů důvěryhodných vystavitelů.

Všechny tyto možnosti umožňují předat parametry s využitím soubory nebo řetězce s kódováním base64.

Výchozí typ úložiště pro všechna úložiště certifikátů je systém souborů, které můžete změnit pomocí možnosti příkazového řádku. Protože kontejner neposkytuje trvalého úložiště v jeho systému souborů, je nutné vybrat typ jiném úložišti. Použití Dockeru `-v` možností zachovat certifikát uloží do hostitelského souborového systému nebo na svazku Dockeru. Pokud používáte Docker svazku, můžete předat certifikáty pomocí řetězce s kódováním base64.

Běhové prostředí ovlivňuje, jak jsou trvalé certifikáty. Vyhněte se vytváření nových úložišť certifikátů při každém spuštění aplikace:

- Běží nativně na Windows, nelze použít certifikát úložiště aplikací typu `Directory` vzhledem k tomu, že přístup k privátnímu klíči se nezdaří. V takovém případě použijte možnost `--at X509Store`.
- Spuštění jako kontejner dockeru pro Linux, můžete namapovat úložiště certifikátů do hostitelského souborového systému s dockerem možnost Spustit `-v <hostdirectory>:/appdata`. Tato možnost umožňuje certifikát trvalé během spuštění aplikace.
- Spuštění jako Linux kontejneru dockeru a chcete použít x X509 úložiště pro certifikát aplikace, použijte možnost spustit dockeru `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` a možností aplikace `--at X509Store`

## <a name="performance-and-memory-considerations"></a>Aspekty týkající se výkonu a paměti

Tato část popisuje možnosti pro správu paměti a výkonu:

### <a name="command-line-parameters-to-control-performance-and-memory"></a>Parametry příkazového řádku k řízení výkonu a paměti

Při spuštění vydavatele OPC, musíte znát vaše požadavky na výkon a paměťové prostředky k dispozici na hostiteli.

Výkon a paměť jsou nezávislé a obě závisí na konfiguraci kolik uzlů nakonfigurovaná pro publikování. Ujistěte se, že tyto parametry splňují vaše požadavky:

- IoT Hub odesílá intervalu: `--si`
- Velikost zprávy služby IoT Hub (výchozí `1`): `--ms`
- Monitorovaných položek fronty kapacity: `--mq`

`--mq` Parametr určuje horní mez kapacity vnitřní fronty, který ukládá do vyrovnávací paměti všech oznamování změn hodnotu uzlu OPC. Pokud vydavatel OPC nemůže odesílat zprávy do služby IoT Hub rychlý dostatečně, této fronty vyrovnávací paměti oznámení. Tento parametr nastaví počet oznámení, která můžete do vyrovnávací paměti. Pokud se zobrazí počet položek v této frontě zvyšovat v testovacích běhů, pak aby se zabránilo ztrátě zpráv byste měli:

- Zkracovat interval odeslání služby IoT Hub
- Zvětšete velikost zpráv služby IoT Hub

`--si` Vynutí parametr vydavatele OPC pro odesílání zpráv do služby IoT Hub v zadaném intervalu. Vydavatel OPC odešle zprávu jako velikost zprávy určené `--ms` parametr je dosaženo, nebo co nejdřív intervalu určeném `--si` parametr je dosaženo. Chcete-li zakázat možnost velikost zprávy, použijte `--ms 0`. V tomto případě vydavatel OPC používá největší velikost možné služby IoT Hub zprávy 256 kB k datům služby batch.

`--ms` Umožňuje parametr dávkové zprávy odeslané do služby IoT Hub. Protokol, který používáte Určuje, zda nároky na odesílání zprávy do služby IoT Hub vysokou ve srovnání s skutečný čas odeslat datové části. Pokud váš scénář umožňuje latence při dat přijatý službou IoT Hub, nakonfigurujte vydavatele OPC použít největší velikost zprávy 256 kB.

Před použitím vydavatel OPC v produkčních scénářích testování výkonu a využití paměti za podmínek produkčního prostředí. Můžete použít `--di` parametr zadejte interval v sekundách, po kterou vydavatel OPC zapisuje diagnostické informace.

### <a name="test-measurements"></a>Měření testu

Následující příklad diagnostics zobrazit měření s různými hodnotami parametru `--si` a `--ms` parametry publikování 500 uzly OPC publikování intervalu 1 sekundu.  Test použil sestavení pro ladění vydavatel OPC ve Windows 10 nativně po dobu 120 sekund. Protokol služby IoT Hub se výchozí protokolu MQTT.

#### <a name="default-configuration---si-10---ms-262144"></a>Výchozí konfigurace (--si – 10ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:33:05 (started @ 26.10.2017 15:31:09)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54363
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54363
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:33:04
bytes sent to IoT Hub: 12709429
avg msg size: 116600
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 10
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Výchozí konfigurace odesílá data do služby IoT Hub každých 10 sekund nebo 256 kB dat je k dispozici pro službu IoT Hub pro ingestování. Tato konfigurace přidá střední latenci asi 10 sekund, ale má nejnižší pravděpodobnost ztrátě dat z důvodu velikostí velkých zpráv. Diagnostický výstup ukazuje nejsou žádné aktualizace ke ztrátě uzlu OPC: `monitored item notifications enqueue failure: 0`.

#### <a name="constant-send-interval---si-1---ms-0"></a>Konstantní odeslat interval (--si 1 ms - 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:35:59 (started @ 26.10.2017 15:34:03)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54243
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54243
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:35:59
bytes sent to IoT Hub: 12683836
avg msg size: 116365
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 1
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Pokud velikost zprávy je nastavena na hodnotu 0 vydavatel OPC interně dávek dat s využitím největší podporované služby IoT Hub velikost zprávy, které je 256 kB. Diagnostický výstup ukazuje Průměrná velikost 115,019 bajtů. V této konfiguraci vydavatel OPC nedojde ke ztrátě všech uzlů OPC hodnotu aktualizace a ve srovnání s výchozí má nižší latenci.

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>Poslat každou aktualizaci hodnotu uzlu OPC (--si 0 – ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:39:33 (started @ 26.10.2017 15:37:37)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 8184
monitored item notifications enqueued: 54232
monitored item notifications enqueue failure: 44624
monitored item notifications dequeued: 1424
---------------------------------
messages sent to IoT Hub: 1423
last successful msg sent @: 26.10.2017 15:39:33
bytes sent to IoT Hub: 333046
avg msg size: 234
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 96
--si setting: 0
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Tato konfigurace odešle pro každou hodnotu uzlu OPC změnit zprávy do služby IoT Hub. Zobrazit diagnostiku Průměrná velikost je 234 bajtů, což je malý. Výhodou téhle konfigurace je, že vydavatel OPC nepřidává žádné latence. Počet ztrátou určitých aktualizací hodnotu uzlu OPC (`monitored item notifications enqueue failure: 44624`) je vysoké, což Ujistěte se, tato konfigurace vhodná pro scénáře s velké objemy telemetrických dat pro publikování.

### <a name="maximum-batching---si-0---ms-262144"></a>Maximální dávkové zpracování (--si 0 – ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:42:55 (started @ 26.10.2017 15:41:00)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54137
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54137
---------------------------------
messages sent to IoT Hub: 48
last successful msg sent @: 26.10.2017 15:42:55
bytes sent to IoT Hub: 12565544
avg msg size: 261782
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 0
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Tato konfigurace dávek tolik aktualizací hodnotu uzlu OPC nejvíce. Maximální velikost zprávy služby IoT Hub je 256 kB, která je tady nakonfigurovaná. Neexistuje žádný interval odeslat požadovaný, což znamená, že objem dat pro službu IoT Hub pro ingestování určuje latence. Tato konfigurace má nejnižší pravděpodobnost ztráty všechny hodnoty uzlu OPC a je vhodný pro publikování vysoký počet uzlů. Při použití této konfigurace, ujistěte se, že váš scénář nemá podmínky, kde je zavedená vysokou latencí, není-li velikost zprávy 256 kB.

## <a name="debug-the-application"></a>Ladění aplikace

Chcete-li ladit aplikaci, otevřete **opcpublisher.sln** řešení pomocí sady Visual Studio a nástroje pro ladění sady Visual Studio.

Pokud potřebujete přístup k serveru OPC UA v vydavatele OPC, ujistěte se, že brána firewall povoluje přístup k portu server naslouchá. Výchozí port je: 62222.

## <a name="control-the-application-remotely"></a>Vzdálené řízení aplikace

Konfigurace uzlů pro publikování můžete udělat pomocí služby IoT Hub přímé metody.

Vydavatel OPC implementuje několik dalších volání přímé metody služby IoT Hub, ke čtení:

- Obecné informace.
- Diagnostické informace o relacích, předplatných a monitorované položky OPC.
- Diagnostické informace o zpráv ve službě IoT Hub a události.
- Při spuštění protokolu.
- Posledních 100 řádků v protokolu.
- Vypněte aplikaci.

Následující úložiště GitHub obsahují nástroje [konfigurace uzlů pro publikování](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) a [diagnostické informace](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics). Oba nástroje jsou také dostupné jako kontejnery v Docker Hubu.

## <a name="use-a-sample-opc-ua-server"></a>Pomocí ukázky serveru OPC UA

Pokud nemáte k dispozici skutečný server OPC UA, můžete použít [ukázkový OPC UA PLC](https://github.com/Azure-Samples/iot-edge-opc-plc) začít. Tato ukázka PLC je také k dispozici v Docker Hubu.

Implementuje několik značek, které generují náhodná data a značky s anomálie. Ukázku můžete rozšířit, pokud chcete simulovat hodnoty další značky.

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak spustit vydavatele OPC, doporučených dalších kroků jsou další informace o [OPC Dvojčete](overview-opc-twin.md) a [OPC trezor](overview-opc-vault.md).
