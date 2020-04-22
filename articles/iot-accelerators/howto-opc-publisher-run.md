---
title: Spuštění vydavatele OPC – Azure | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak spustit a ladit Vydavatel OPC. Také řeší aspekty výkonu a paměti.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: c664d4859a306387b4eafa2f19ab5877ccf6eb1b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686963"
---
# <a name="run-opc-publisher"></a>Spuštění vydavatele OPC

Tento článek popisuje, jak spustit ad ladění OPC Publisher. Také řeší aspekty výkonu a paměti.

## <a name="command-line-options"></a>Možnosti příkazového řádku

Použití aplikace je `--help` zobrazeno pomocí možnosti příkazového řádku takto:

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

Obvykle zadáte připojovací řetězec vlastníka služby IoT Hub pouze při prvním spuštění aplikace. Připojovací řetězec je zašifrován a uložen v úložišti certifikátů platformy. Při pozdějších spuštěních aplikace přečte připojovací řetězec z úložiště certifikátů. Pokud zadáte připojovací řetězec při každém spuštění, zařízení, které je vytvořeno pro aplikaci v registru zařízení služby IoT Hub, se odebere a znovu vytvoří.

## <a name="run-natively-on-windows"></a>Spustit nativně v systému Windows

Otevřete projekt **opcpublisher.sln** pomocí sady Visual Studio, vytvořte řešení a publikujte ho. Aplikaci můžete spustit v **cílovém adresáři,** do který jste publikovali, následujícím způsobem:

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>Použití kontejneru s vlastním stavěním

Vytvořte si vlastní kontejner a začněte jej následujícím způsobem:

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Použití kontejneru z registru microsoft container

V registru kontejnerů Microsoft je k dispozici předem vytvořený kontejner. Začněte takto:

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Zkontrolujte [Docker Hub,](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) abyste viděli podporované operační systémy a architektury procesorů. Pokud je architektura operačního systému a procesoru podporovaná, Docker automaticky vybere správný kontejner.

## <a name="run-as-an-azure-iot-edge-module"></a>Spuštění jako modul Azure IoT Edge

OPC Publisher je připravený k použití jako modul [Azure IoT Edge.](https://docs.microsoft.com/azure/iot-edge) Při použití OPC Publisher jako modul IoT Edge, pouze podporované přenosové protokoly jsou **Amqp_Tcp_Only** a **Mqtt_Tcp_Only**.

Pokud chcete přidat Vydavatel OPC jako modul do nasazení IoT Edge, přejděte na nastavení ioT hubu na webu Azure Portal a proveďte následující kroky:

1. Přejděte na **IoT Edge** a vytvořte nebo vyberte zařízení IoT Edge.
1. Vyberte **možnost Nastavit moduly**.
1. V části **Moduly nasazení vyberte** **Přidat** a potom **modul IoT Edge**.
1. Do pole **Název** zadejte **vydavatele**.
1. Do pole **Identifikátor URI obrázku** zadejte`mcr.microsoft.com/iotedge/opc-publisher:<tag>`
1. Dostupné značky najdete v [Docker Hubu](https://hub.docker.com/_/microsoft-iotedge-opc-publisher)
1. Do pole **Možnosti vytvoření kontejneru** vložte následující pole JSON:

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    Tato konfigurace konfiguruje IoT Edge tak, aby spustil kontejner s názvem **vydavatel** pomocí bitové kopie VydavatelE OPC. Název hostitele systému kontejneru je nastaven na **vydavatele**. Vydavatel OPC je volán s následujícím `--aa`argumentem příkazového řádku: . Pomocí této možnosti aplikace OPC Publisher důvěřuje certifikátům serverů OPC UA, ke kterým se připojuje. Můžete použít libovolné možnosti příkazového řádku Aplikace OPC. Jediným omezením je velikost **možností vytvoření kontejneru podporovaných** ioT Edge.

1. Ostatní nastavení ponechte beze změny a vyberte **Uložit**.
1. Pokud chcete zpracovat výstup Vydavatele OPC místně s jiným modulem IoT Edge, vraťte se na stránku **Nastavit moduly.** Potom přejděte na kartu **Zadat trasy** a přidejte novou trasu, která vypadá jako následující JSON:

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. Zpět na stránce **Nastavit moduly** vyberte **Další**, dokud se nedostanete na poslední stránku konfigurace.
1. Vyberte **Odeslat,** chcete-li odeslat konfiguraci do IoT Edge.
1. Když jste spustili IoT Edge na hraničním zařízení a **vydavatel** kontejneru dockeru běží, můžete `docker logs -f publisher` rezervovat výstup protokolu OPC Publisher buď pomocí nebo kontrolou souboru protokolu. V předchozím příkladu je soubor `d:\iiotegde\publisher-publisher.log`protokolu výše . Můžete také použít [nástroj iot-edge-opc-publisher-diagnostics](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics).

### <a name="make-the-configuration-files-accessible-on-the-host"></a>Zpřístupnění konfiguračních souborů na hostiteli

Chcete-li zpřístupnit konfigurační soubory modulu IoT Edge v hostitelském systému souborů, použijte následující **možnosti vytvoření kontejneru**. Následující příklad je nasazení pomocí Linux kontejnery pro Windows:

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

S těmito možnostmi, OPC Vydavatel přečte `./pn.json` uzly, které by měl `/appdata` publikovat ze souboru a pracovní adresář kontejneru je nastavena na při spuštění. S těmito nastaveními OPC `/appdata/pn.json` Publisher přečte soubor z kontejneru získat jeho konfiguraci. Bez `--pf` možnosti se Aplikace OPC Publisher `./publishednodes.json`pokusí přečíst výchozí konfigurační soubor .

Soubor protokolu pomocí výchozího `publisher-publisher.log`názvu je `/appdata` zapsán `CertificateStores` a adresář je také vytvořen v tomto adresáři.

Chcete-li zpřístupnit všechny tyto soubory v hostitelském systému souborů, vyžaduje konfigurace kontejneru svazek připojení vazby. Vazba `d://iiotedge:/appdata` mapuje `/appdata`adresář , který je aktuálním pracovním adresářem při spuštění kontejneru, do adresáře `d://iiotedge`hostitele . Bez této možnosti žádná data souboru je zachována při příštím spuštění kontejneru.

Pokud používáte kontejnery systému Windows, `Binds` pak syntaxe parametru se liší. Při spuštění kontejneru je `c:\appdata`pracovní adresář . Chcete-li umístit konfigurační soubor do adresáře `d:\iiotedge` `HostConfig` na hostiteli, zadejte v části následující mapování:

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Pokud používáte linuxové kontejnery na Linuxu, syntaxe parametru `Binds` je opět odlišná. Při spuštění kontejneru je `/appdata`pracovní adresář . Chcete-li umístit konfigurační soubor do adresáře `/iiotedge` `HostConfig` na hostiteli, zadejte v části následující mapování:

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>Důležité informace při použití kontejneru

V následujících částech jsou uvedeny některé věci, které je třeba mít na paměti při použití kontejneru:

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>Přístup k serveru OPC Publisher UA

Ve výchozím nastavení naslouchá server OPC OPC UA na portu 62222. Chcete-li tento vstupní port zpřístupnit v kontejneru, použijte následující příkaz:

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>Povolit překlad názvů mezi kontejnery

Chcete-li povolit překlad názvů z kontejneru do jiných kontejnerů, vytvořte uživatelsky `--network` definující síť docker bridge a připojte kontejner k této síti pomocí této možnosti. Také přiřadit kontejneru název `--name` pomocí možnosti takto:

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Kontejner je nyní dostupný pomocí `publisher` názvu jinými kontejnery ve stejné síti.

### <a name="access-other-systems-from-within-the-container"></a>Přístup k dalším systémům z kontejneru

Ostatní kontejnery lze dosáhnout pomocí parametrů popsaných v předchozí části. Pokud je povolen operační systém, ve kterém je Docker hostován, funguje přístup ke všem systémům, které jsou službě DNS známy.

V sítích, které používají překlad názvů NetBIOS, povolte `--add-host` přístup k jiným systémům spuštěním kontejneru s možností. Tato možnost efektivně přidá položku do hostitelského souboru kontejneru:

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>Přiřazení názvu hostitele

OPC Publisher používá název hostitele počítače, ve které běží, pro generování certifikátu a koncového bodu. Docker zvolí náhodný název hostitele, pokud není `-h` nastaven a možnost. Následující příklad ukazuje, jak nastavit název interního `publisher`hostitele kontejneru na :

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>Použití vazebových připojení (sdílený souborový systém)

Namísto použití systému souborů kontejneru můžete zvolit hostitelský systém souborů pro ukládání informací o konfiguraci a souborů protokolu. Chcete-li tuto možnost `-v` nakonfigurovat, použijte možnost `docker run` v režimu připojení vazby.

## <a name="opc-ua-x509-certificates"></a>Certifikáty OPC UA X.509

OPC UA používá certifikáty X.509 k ověření klienta a serveru OPC UA při navázání připojení a šifrování komunikace mezi nimi. Aplikace OPC Publisher používá ke správě všech certifikátů úložiště certifikátů spravovaná zásobníkem OPC UA. Při spuštění Aplikace OPC Publisher zkontroluje, zda existuje certifikát pro sebe. Pokud v úložišti certifikátů není žádný certifikát a jeden není předaný na příkazovém řádku, OPC Publisher vytvoří certifikát podepsaný svým držitelem. Další informace naleznete v metodě **InitApplicationSecurityAsync** v aplikaci `OpcApplicationConfigurationSecurity.cs`.

Certifikáty podepsané svým držitelem neposkytují žádné zabezpečení, protože nejsou podepsány důvěryhodnou certifikační autoritou.

Aplikace OPC Publisher nabízí možnosti příkazového řádku pro:

- Načtěte informace o csr aktuálního certifikátu aplikace používaného vydavatelem OPC.
- Zřízení vydavatele OPC s certifikátem podepsaným certifikační autoritou
- Zřizte vydavatele OPC s novým párem klíčů a odpovídajícím certifikátem podepsaným certifikační autoritou.
- Přidejte certifikáty do úložiště certifikátů důvěryhodných rovnocenných nebo důvěryhodných vystavitelů.
- Přidejte seznam crl.
- Odeberte certifikát z úložiště certifikátů důvěryhodných rovnocenných nebo důvěryhodných vystavitelů.

Všechny tyto možnosti umožňují předat parametry pomocí souborů nebo base64 kódovaných řetězců.

Výchozím typem úložiště pro všechna úložiště certifikátů je systém souborů, který můžete změnit pomocí možností příkazového řádku. Vzhledem k tomu, že kontejner neposkytuje trvalé úložiště v systému souborů, musíte zvolit jiný typ úložiště. Pomocí možnosti Dockeru `-v` můžete zachovat úložiště certifikátů v hostitelském systému souborů nebo na svazku Dockeru. Pokud používáte svazek Dockeru, můžete předat certifikáty pomocí řetězců kódovaných base64.

Prostředí runtime ovlivňuje, jak jsou certifikáty trvalé. Při každém spuštění aplikace se vyhněte vytváření nových úložišť certifikátů:

- Nativně spuštěné v systému Windows nelze použít `Directory` úložiště certifikátů aplikace typu, protože přístup k soukromému klíči se nezdaří. V takovém případě použijte `--at X509Store`možnost .
- Spuštění jako linuxový docker kontejner, můžete mapovat úložiště certifikátů `-v <hostdirectory>:/appdata`na systém souborů hostitele s možností spuštění dockeru . Tato možnost umožňuje trvalý certifikát napříč spuštěním aplikace.
- Běží jako linuxový docker kontejner a chcete použít úložiště X509 pro `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` certifikát aplikace, použijte možnost spuštění dockeru a možnost aplikace`--at X509Store`

## <a name="performance-and-memory-considerations"></a>Důležité informace o výkonu a paměti

Tato část popisuje možnosti správy paměti a výkonu:

### <a name="command-line-parameters-to-control-performance-and-memory"></a>Parametry příkazového řádku pro řízení výkonu a paměti

Při spuštění OPC Publisher, musíte být vědomi požadavků na výkon a paměťové prostředky, které jsou k dispozici na vašem hostiteli.

Paměť a výkon jsou vzájemně závislé a oba závisí na konfiguraci, kolik uzlů nakonfigurujete publikovat. Ujistěte se, že následující parametry splňují vaše požadavky:

- IoT Hub odesílá interval:`--si`
- Velikost zprávy centra IoT Hub (výchozí): `1``--ms`
- Kapacita fronty monitorovaných položek:`--mq`

Parametr `--mq` řídí horní mez kapacity vnitřní fronty, která uvěčňuje všechna oznámení o změně hodnoty uzlu OPC. Pokud Vydavatel OPC nemůže odesílat zprávy do služby IoT Hub dostatečně rychle, tato fronta uvažuje oznámení. Parametr nastaví počet oznámení, která mohou být uložena do vyrovnávací paměti. Pokud se počet položek v této frontě zvyšuje v testovacích běhů, pak, aby se zabránilo ztrátě zpráv, měli byste:

- Snížení intervalu odesílání centra IoT Hub
- Zvětšení velikosti zpráv služby IoT Hub

Parametr `--si` vynutí, aby Vydavatel OPC odesílá zprávy do služby IoT Hub v zadaném intervalu. OPC Publisher odešle zprávu, jakmile je `--ms` dosaženo velikosti zprávy určené parametrem, `--si` nebo jakmile je dosaženo intervalu určeného parametrem. Chcete-li zakázat možnost `--ms 0`velikosti zprávy, použijte . V tomto případě OPC Vydavatel používá největší možnou velikost zprávy IoT Hub 256 kB na dávková data.

Parametr `--ms` umožňuje dávkové zprávy odeslané do ioT hubu. Protokol, který používáte, určuje, zda je režie odesílání zprávy do služby IoT Hub vysoká ve srovnání se skutečným časem odeslání datové části. Pokud váš scénář umožňuje latenci při pohlcování dat pomocí služby IoT Hub, nakonfigurujte Vydavatel OPC tak, aby používal největší velikost zprávy 256 kB.

Před použitím Aplikace OPC publisher v produkčních scénářích otestujte výkon a využití paměti v produkčních podmínkách. `--di` Parametr můžete použít k určení intervalu v sekundách, který Vydavatel OPC zapisuje diagnostické informace.

### <a name="test-measurements"></a>Zkušební měření

Následující příklad diagnostiky ukazují měření `--si` s `--ms` různými hodnotami pro a parametry publikování 500 uzlů s intervalem publikování OPC 1 sekundy.  Test používá OPC Publisher ladění sestavení na Windows 10 nativně po dobu 120 sekund. Protokol IoT Hub byl výchozím protokolem MQTT.

#### <a name="default-configuration---si-10---ms-262144"></a>Výchozí konfigurace (--si 10 --ms 262144)

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

Výchozí konfigurace odesílá data do služby IoT Hub každých 10 sekund nebo když je k dispozici 256 kB dat pro službu IoT Hub k ingestování. Tato konfigurace přidá střední latence asi 10 sekund, ale má nejnižší pravděpodobnost ztráty dat z důvodu velké velikosti zprávy. Výstup diagnostiky ukazuje, že nejsou žádné ztracené `monitored item notifications enqueue failure: 0`aktualizace uzlů OPC: .

#### <a name="constant-send-interval---si-1---ms-0"></a>Interval konstantního odesílání (--si 1 --ms 0)

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

Pokud je velikost zprávy nastavena na 0, pak OPC Publisher interně dávky dat pomocí největší podporované velikosti zprávy Služby IoT Hub, což je 256 kB. Diagnostický výstup ukazuje, že průměrná velikost zprávy je 115 019 bajtů. V této konfiguraci OPC Vydavatel neztrácí žádné aktualizace hodnot uzlu OPC a ve srovnání s výchozí má nižší latenci.

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>Odeslat každou aktualizaci hodnoty uzlu OPC (--si 0 --ms 0)

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

Tato konfigurace odešle pro každou hodnotu uzlu OPC změnit zprávu do služby IoT Hub. Diagnostika ukazují, že průměrná velikost zprávy je 234 bajtů, což je malé. Výhodou této konfigurace je, že Vydavatel OPC nepřidává žádnou latenci. Počet ztracených aktualizací hodnoty uzlu`monitored item notifications enqueue failure: 44624`OPC ( ) je vysoký, což činí tuto konfiguraci nevhodnou pro scénáře s vysokými objemy telemetrie, které mají být publikovány.

### <a name="maximum-batching---si-0---ms-262144"></a>Maximální dávkování (--si 0 --ms 262144)

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

Tato konfigurace dávky co nejvíce aktualizací hodnoty uzlu OPC, jak je to možné. Maximální velikost zprávy služby IoT Hub je 256 kB, která je nakonfigurována zde. Není požadován žádný interval odesílání, což znamená, že množství dat pro ioT hub k ingestování určuje latenci. Tato konfigurace má nejmenší pravděpodobnost ztráty hodnot uzlů OPC a je vhodná pro publikování vysokého počtu uzlů. Při použití této konfigurace, ujistěte se, že váš scénář nemá podmínky, kde je zavedena vysoká latence, pokud není dosaženo velikosti zprávy 256 kB.

## <a name="debug-the-application"></a>Ladění aplikace

Chcete-li ladit aplikaci, otevřete soubor řešení **opcpublisher.sln** pomocí sady Visual Studio a použijte nástroje pro ladění sady Visual Studio.

Pokud potřebujete získat přístup k serveru OPC UA v vydavateli OPC, ujistěte se, že brána firewall umožňuje přístup k portu, na který server naslouchá. Výchozí port je: 62222.

## <a name="control-the-application-remotely"></a>Dálkové ovládání aplikace

Konfigurace uzlů pro publikování lze provést pomocí přímých metod služby IoT Hub.

Vydavatel OPC implementuje několik dalších volání přímé metody služby IoT Hub ke čtení:

- Obecné informace.
- Diagnostické informace o opsa, předplatných a sledovaných položkách.
- Diagnostické informace o zprávách a událostech centra IoT Hub.
- Spouštěcí protokol.
- Posledních 100 řádků protokolu.
- Vypněte aplikaci.

Následující úložiště GitHub obsahují nástroje pro [konfiguraci uzlů pro publikování](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) a [čtení diagnostických informací](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics). Oba nástroje jsou také k dispozici jako kontejnery v Docker Hub.

## <a name="use-a-sample-opc-ua-server"></a>Použití ukázkového serveru OPC UA

Pokud nemáte skutečný OPC UA server, můžete použít [ukázku OPC UA PLC,](https://github.com/Azure-Samples/iot-edge-opc-plc) abyste mohli začít. Tato ukázková PLC je k dispozici také na Docker Hubu.

Implementuje řadu značek, které generují náhodná data a značky s anomáliemi. Vzorek můžete rozšířit, pokud potřebujete simulovat další hodnoty značek.

## <a name="next-steps"></a>Další kroky

Nyní, když jste se naučili, jak spustit OPC Publisher, doporučené další kroky jsou dozvědět se o [OPC Twin](overview-opc-twin.md) a [OPC Vault](overview-opc-vault.md).
