---
title: Spuštění vydavatele OPC – Azure | Microsoft Docs
description: Tento článek popisuje, jak spustit a ladit OPC Publisher. Také řeší požadavky na výkon a paměť.
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
ms.openlocfilehash: 6a4b65195488f101d36aaf73956f1422bfccbbf9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91282132"
---
# <a name="run-opc-publisher"></a>Spuštění vydavatele OPC

> [!IMPORTANT]
> I když aktualizujeme Tento článek, přečtěte si nejaktuálnější obsah v tématu [Azure Data IoT](https://azure.github.io/Industrial-IoT/) .

Tento článek popisuje, jak spustit vydavatele služby AD Debug OPC Publisher. Také řeší požadavky na výkon a paměť.

## <a name="command-line-options"></a>Možnosti příkazového řádku

Použití aplikace se zobrazuje pomocí `--help` Možnosti příkazového řádku následujícím způsobem:

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

Obvykle je třeba zadat připojovací řetězec IoT Hub Owner pouze při prvním spuštění aplikace. Připojovací řetězec je zašifrovaný a uložený v úložišti certifikátů platformy. Při pozdějším spuštění aplikace přečte připojovací řetězec z úložiště certifikátů. Pokud při každém spuštění zadáte připojovací řetězec, zařízení, které je vytvořeno pro aplikaci v registru IoT Hub zařízení, se odebere a znovu vytvoří.

## <a name="run-natively-on-windows"></a>Spouštění nativně ve Windows

Otevřete projekt **opcpublisher. sln** se sadou Visual Studio, sestavte řešení a publikujte ho. Aplikaci můžete spustit v **cílovém adresáři** , do kterého jste publikovali následujícím způsobem:

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>Použití samostatně vytvořeného kontejneru

Sestavte vlastní kontejner a spusťte ho následujícím způsobem:

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Použití kontejneru z Microsoft Container Registry

V Microsoft Container Registry je k dispozici předem vytvořený kontejner. Spusťte ji následujícím způsobem:

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Zkontrolujte [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) a podívejte se na podporované operační systémy a architektury procesorů. Pokud je podporován operační systém a architektura procesoru, Docker automaticky vybere správný kontejner.

## <a name="run-as-an-azure-iot-edge-module"></a>Spustit jako modul Azure IoT Edge

Vydavatel OPC je připravený k použití jako modul [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge) . Pokud používáte OPC Publisher jako modul IoT Edge, jediné podporované přenosové protokoly jsou **Amqp_Tcp_Only** a **Mqtt_Tcp_Only**.

Pokud chcete přidat vydavatele OPC jako modul do nasazení IoT Edge, v Azure Portal klikněte na nastavení IoT Hub a proveďte následující kroky:

1. Přejít na **IoT Edge** a vytvořit nebo vybrat IoT Edge zařízení.
1. Vyberte možnost **nastavit moduly**.
1. V části **moduly nasazení** vyberte **přidat** a pak **IoT Edge modul**.
1. Do pole **název** zadejte **Publisher**.
1. Do pole **identifikátor URI image** zadejte `mcr.microsoft.com/iotedge/opc-publisher:<tag>`
1. Dostupné značky najdete v [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) .
1. Vložte následující JSON do pole **možnosti vytvoření kontejneru** :

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    Tato konfigurace nakonfiguruje IoT Edge ke spuštění kontejneru s názvem **Publisher** pomocí Image vydavatele OPC. Název hostitele systému kontejneru je nastaven na hodnotu **Publisher**. Vydavatel OPC se volá s následujícím argumentem příkazového řádku: `--aa` . Pomocí této možnosti Vydavatel OPC důvěřuje certifikátům serverů OPC UA, ke kterým se připojuje. Můžete použít jakékoli možnosti příkazového řádku pro vydavatele OPC. Jediným omezením je velikost **kontejneru pro vytváření kontejnerů** , které podporuje IoT Edge.

1. Ostatní nastavení ponechte beze změny a vyberte **Uložit**.
1. Pokud chcete zpracovat výstup vydavatele OPC lokálně s jiným modulem IoT Edge, vraťte se na stránku **nastavit moduly** . Pak přejít na kartu **zadat trasy** a přidejte novou trasu, která bude vypadat jako následující JSON:

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. Zpátky na stránce **nastavit moduly** vyberte **Další**, dokud nedosáhnete poslední stránky konfigurace.
1. Vyberte **Odeslat** pro odeslání konfigurace do IoT Edge.
1. Po spuštění IoT Edge na hraničním zařízení a v případě, že je spuštěný **Vydavatel** kontejneru Docker, můžete rezervovat výstup protokolu OPC Publisher buď pomocí `docker logs -f publisher` , nebo kontrolou souboru protokolu. V předchozím příkladu je soubor protokolu výše `d:\iiotegde\publisher-publisher.log` . Můžete také použít [Nástroj IoT-Edge-OPC-Publish-Diagnostics](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics).

### <a name="make-the-configuration-files-accessible-on-the-host"></a>Zpřístupněte konfigurační soubory na hostiteli.

Chcete-li zpřístupnit konfigurační soubory modulu IoT Edge v hostitelském systému souborů, použijte následující **možnosti vytvoření kontejneru**. Následující příklad je nasazení pomocí kontejnerů Linux pro systém Windows:

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

Pomocí těchto možností Vydavatel OPC přečte uzly, které by měly publikovat ze souboru `./pn.json` , a pracovní adresář kontejneru je nastaven na `/appdata` při spuštění. Pomocí těchto nastavení OPC Publisher přečte soubor `/appdata/pn.json` z kontejneru, aby získal jeho konfiguraci. Bez `--pf` Možnosti se Vydavatel OPC pokusí přečíst výchozí konfigurační soubor `./publishednodes.json` .

Soubor protokolu s použitím výchozího názvu `publisher-publisher.log` je napsán do `/appdata` a v `CertificateStores` tomto adresáři je také vytvořen adresář.

Aby všechny tyto soubory byly k dispozici v hostitelském systému souborů, vyžaduje konfigurace kontejneru přípojný svazek vazby. `d://iiotedge:/appdata`Vazba mapuje adresář `/appdata` , což je aktuální pracovní adresář při spuštění kontejneru, do adresáře hostitelů `d://iiotedge` . Bez této možnosti nejsou při příštím spuštění kontejneru uchována žádná data souborů.

Pokud používáte kontejnery Windows, pak se syntaxe `Binds` parametru liší. Při spuštění kontejneru je pracovní adresář `c:\appdata` . Chcete-li uložit konfigurační soubor do adresáře `d:\iiotedge` na hostiteli, zadejte následující mapování v `HostConfig` části:

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Pokud používáte kontejnery Linux v systému Linux, syntaxe `Binds` parametru je znovu odlišná. Při spuštění kontejneru je pracovní adresář `/appdata` . Chcete-li uložit konfigurační soubor do adresáře `/iiotedge` na hostiteli, zadejte následující mapování v `HostConfig` části:

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>Předpoklady při použití kontejneru

V následujících částech jsou uvedeny některé věci, které je potřeba vzít v úvahu při používání kontejneru:

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>Přístup k serveru OPC vydavatele OPC UA

Ve výchozím nastavení naslouchá OPC Publisher OPC UA serveru na portu 62222. K vystavení tohoto příchozího portu v kontejneru použijte následující příkaz:

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>Povolit rozlišení názvů pro zahrnutí

Pokud chcete povolit překlad názvů z kontejneru do jiných kontejnerů, vytvořte uživatele definující síť Docker mostu a připojte kontejner k této síti pomocí `--network` Možnosti. Také přiřaďte kontejner název pomocí možnosti následujícím `--name` způsobem:

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Kontejner je nyní dosažitelný pomocí názvů `publisher` jiných kontejnerů ve stejné síti.

### <a name="access-other-systems-from-within-the-container"></a>Přístup k jiným systémům v rámci kontejneru

K ostatním kontejnerům se dá získat pomocí parametrů popsaných v předchozí části. Pokud je v operačním systému, na kterém je Docker hostovaný, povolený DNS, pak přístup ke všem systémům, které služba DNS zná, funguje.

V sítích, které používají překlad IP adres (NetBIOS), povolte přístup k jiným systémům spuštěním kontejneru s `--add-host` možností. Tato možnost efektivně přidá položku do hostitelského souboru kontejneru:

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>Přiřadit název hostitele

Vydavatel OPC používá název hostitele počítače, na kterém je spuštěný, pro certifikáty a generování koncových bodů. Docker zvolí náhodný název hostitele, pokud jeden není nastaven `-h` parametrem. Následující příklad ukazuje, jak nastavit interní název hostitele kontejneru na `publisher` :

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>Použít připojení BIND (sdílený systém souborů)

Místo použití systému souborů kontejnerů můžete zvolit systém souborů hostitele k ukládání informací o konfiguraci a souborů protokolu. Pokud chcete tuto možnost nakonfigurovat, použijte `-v` možnost `docker run` v režimu připojení BIND.

## <a name="opc-ua-x509-certificates"></a>Certifikáty OPC UA X. 509

OPC UA používá certifikáty X. 509 k ověřování klienta a serveru OPC UA při navazování připojení a zašifrování komunikace mezi nimi. OPC Publisher používá úložiště certifikátů udržované zásobníkem OPC UA ke správě všech certifikátů. Při spuštění OPC Publisher kontroluje, jestli existuje certifikát pro sebe sama. Pokud v úložišti certifikátů není žádný certifikát a jeden z nich není předaný na příkazovém řádku, vytvoří Vydavatel OPC certifikát podepsaný svým držitelem. Další informace naleznete v části Metoda **InitApplicationSecurityAsync** v tématu `OpcApplicationConfigurationSecurity.cs` .

Certifikáty podepsané svým držitelem neposkytují žádné zabezpečení, protože nejsou podepsány důvěryhodnou certifikační autoritou.

Vydavatel OPC poskytuje možnosti příkazového řádku pro:

- Načte informace CSR pro aktuální certifikát aplikace používaný vydavatelem OPC.
- Zřídit vydavatele OPC s certifikátem podepsaným certifikační autoritou.
- Zřídit OPC Publisher s novou dvojicí klíčů a porovnávacím certifikátem podepsaným certifikační autoritou.
- Přidejte certifikáty do úložiště certifikátů důvěryhodného partnera nebo důvěryhodného vystavitele.
- Přidejte seznam odvolaných certifikátů.
- Odeberte certifikát z úložiště certifikátů důvěryhodného partnera nebo důvěryhodného vystavitele.

Všechny tyto možnosti umožňují předávat parametry pomocí souborů nebo řetězců kódovaných v kódování Base64.

Výchozím typem úložiště pro všechna úložiště certifikátů je systém souborů, který můžete změnit pomocí možností příkazového řádku. Vzhledem k tomu, že kontejner neposkytuje trvalé úložiště v systému souborů, musíte zvolit jiný typ úložiště. Pomocí možnosti Docker `-v` zachovejte úložiště certifikátů v systému souborů hostitele nebo na svazku Docker. Pokud používáte svazek Docker, můžete certifikáty předat pomocí řetězců kódovaných v kódování Base64.

Běhové prostředí má vliv na to, jak jsou certifikáty trvalé. Vyhněte se vytváření nových úložišť certifikátů při každém spuštění aplikace:

- Spouštění nativně v systému Windows nemůžete použít úložiště certifikátů aplikace typu, `Directory` protože přístup k privátnímu klíči se nezdařil. V takovém případě použijte možnost `--at X509Store` .
- Běží jako kontejner Docker pro Linux. úložiště certifikátů můžete namapovat na systém souborů hostitele pomocí možnosti Spustit jako pro Docker `-v <hostdirectory>:/appdata` . Tato možnost způsobí, že se certifikát v rámci aplikace bude trvalý.
- Běží jako kontejner Docker pro Linux a pro certifikát aplikace chcete použít úložiště x509, použijte možnost Spustit jako Docker `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` a možnost aplikace. `--at X509Store`

## <a name="performance-and-memory-considerations"></a>Požadavky na výkon a paměť

Tato část popisuje možnosti správy paměti a výkonu:

### <a name="command-line-parameters-to-control-performance-and-memory"></a>Parametry příkazového řádku pro řízení výkonu a paměti

Když spustíte OPC Publisher, budete muset znát požadavky na výkon a paměťové prostředky, které jsou k dispozici na hostiteli.

Výkon a výkon jsou závislé na konfiguraci, kolik uzlů nakonfigurujete pro publikování. Zajistěte, aby splňovaly vaše požadavky následující parametry:

- Interval odesílání IoT Hub: `--si`
- Velikost zprávy IoT Hub (výchozí `1` ): `--ms`
- Kapacita fronty monitorovaných položek: `--mq`

`--mq`Parametr určuje horní mez kapacity interní fronty, která ukládá do vyrovnávací paměti všechna oznámení o změně hodnot uzlu OPC. Pokud Vydavatel OPC nemůže odesílat zprávy, aby IoT Hub dostatečně rychle, tato fronta ukládá oznámení do vyrovnávací paměti. Parametr nastaví počet oznámení, která lze ukládat do vyrovnávací paměti. Pokud vidíte, že počet položek v této frontě roste v testovacích běhůch, pak se vyhnete ztrátě zpráv, které byste měli:

- Omezení IoT Hubho intervalu odesílání
- Zvětšit IoT Hub velikost zprávy

`--si`Parametr vynutí, aby Vydavatel OPC odesílal zprávy do IoT Hub v zadaném intervalu. Vydavatel OPC pošle zprávu hned po dosažení velikosti zprávy určené `--ms` parametrem nebo po dosažení intervalu zadaného `--si` parametrem. Chcete-li zakázat možnost velikosti zprávy, použijte `--ms 0` . V tomto případě používá Vydavatel OPC největší možnou IoT Hub velikost zprávy 256 kB až po dávková data.

`--ms`Parametr umožňuje dávkovat zprávy odeslané do IoT Hub. Protokol, který používáte, určuje, zda režie odeslání zprávy do IoT Hub je ve srovnání se skutečným časem odeslání datové části vysoká. Pokud váš scénář umožňuje latenci, když se data ingestují IoT Hub, nakonfiguruje se Vydavatel OPC, aby používal největší velikost zprávy 256 kB.

Než použijete vydavatele OPC v produkčních scénářích, otestujte využití výkonu a paměti v rámci produkčních podmínek. Parametr můžete použít `--di` k určení intervalu v sekundách, který OPC Publisher zapisuje diagnostické informace.

### <a name="test-measurements"></a>Měření testů

Následující příklad diagnostiky zobrazuje měření s různými hodnotami `--si` parametrů a `--ms` publikování 500 uzlů s intervalem publikování OPC 1 sekundou.  Test použil OPC sestavení pro ladění v systému Windows 10 nativně po dobu 120 sekund. Protokol IoT Hub byl výchozí protokol MQTT.

#### <a name="default-configuration---si-10---ms-262144"></a>Výchozí konfigurace (--si 10--MS 262144)

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

Výchozí konfigurace odesílá data IoT Hub každých 10 sekund, nebo pokud je IoT Hub k ingestování k dispozici 256 kB dat. Tato konfigurace přidává střední latenci zhruba po dobu 10 sekund, ale má nejnižší pravděpodobnost ztráty dat z důvodu velké velikosti zprávy. Výstup diagnostiky zobrazuje žádné ztracené aktualizace uzlů OPC: `monitored item notifications enqueue failure: 0` .

#### <a name="constant-send-interval---si-1---ms-0"></a>Interval konstantního odesílání (--si 1--MS 0)

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

Když je velikost zprávy nastavená na 0, OPC Publisher interně dávkuje data s využitím největší podporované velikosti IoT Hub zpráv, což je 256 kB. Výstup diagnostiky zobrazuje průměrnou velikost zprávy je 115 019 bajtů. V této konfiguraci OPC Publisher neztratí žádné aktualizace hodnot uzlu OPC a ve srovnání s výchozím nastavením má nižší latenci.

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>Poslat každou aktualizaci hodnoty uzlu OPC (--si 0--MS 0)

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

Tato konfigurace posílá pro každou hodnotu uzlu OPC změnu zprávy na IoT Hub. Diagnostika zobrazuje průměrnou velikost zprávy je 234 bajtů, což je malé. Výhodou této konfigurace je, že vydavatel OPC nepřidá žádnou latenci. Počet ztracených aktualizací OPC uzlů `monitored item notifications enqueue failure: 44624` je vysoký, takže tato konfigurace není vhodná pro scénáře s velkým objemem telemetrie, která se má publikovat.

### <a name="maximum-batching---si-0---ms-262144"></a>Maximální dávkování (--si 0--MS 262144)

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

Tato konfigurace dávkuje tolik OPC hodnot, kolik jich je možné aktualizovat. Maximální velikost zprávy IoT Hub je 256 kB, která je konfigurována zde. Nepožaduje se žádný časový interval pro odesílání, což znamená, že množství dat IoT Hub k ingestování určuje latenci. Tato konfigurace má nejnižší pravděpodobnost ztráty všech hodnot uzlu OPC a je vhodná pro publikování vysokého počtu uzlů. Při použití této konfigurace se ujistěte, že váš scénář nemá podmínky, kde je zavedena vysoká latence, pokud není dosažena velikost zprávy 256 kB.

## <a name="debug-the-application"></a>Ladění aplikace

Chcete-li ladit aplikaci, otevřete soubor řešení **opcpublisher. sln** pomocí sady Visual Studio a použijte ladicí nástroje sady Visual Studio.

Pokud potřebujete přístup k serveru OPC UA v OPC vydavateli, ujistěte se, že brána firewall umožňuje přístup k portu, na kterém server naslouchá. Výchozí port je: 62222.

## <a name="control-the-application-remotely"></a>Řízení aplikace vzdáleně

Konfigurace uzlů pro publikování se dá provádět pomocí IoT Hub přímých metod.

OPC Publisher implementuje několik dalších volání přímých metod IoT Hub pro čtení:

- Obecné informace.
- Diagnostické informace o OPC relacích, předplatných a monitorovaných položkách.
- Diagnostické informace o IoT Hubch zprávách a událostech.
- Protokol spuštění.
- Poslední 100 řádky protokolu
- Ukončete aplikaci.

Následující úložiště GitHub obsahují nástroje pro [konfiguraci uzlů pro publikování](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) a [čtení diagnostických informací](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics). Oba nástroje jsou také k dispozici jako kontejnery v Docker Hub.

## <a name="use-a-sample-opc-ua-server"></a>Použití ukázkového serveru OPC UA

Pokud nemáte skutečný server OPC UA, můžete začít pomocí [ukázkového OPC UA PLC](https://github.com/Azure-Samples/iot-edge-opc-plc) . Tato ukázková PLC je dostupná taky v Docker Hub.

Implementuje několik značek, které generují náhodná data a značky se anomáliemi. Ukázku můžete roztáhnout, pokud potřebujete simulovat další hodnoty značek.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak spustit OPC Publisher, doporučujeme další postup, jak se dozvědět o [OPC](overview-opc-twin.md) a [OPC trezoru](overview-opc-vault.md).
