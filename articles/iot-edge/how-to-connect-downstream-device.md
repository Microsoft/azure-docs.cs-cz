---
title: Připojení zařízení pro příjem dat – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Jak nakonfigurovat zařízení pro příjem dat nebo list pro připojení k zařízením brány Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6ddda38d887cdfe30b449847e2f625ba17f33898
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510800"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Připojení podřízeného zařízení k bráně služby Azure IoT Edge

Tento článek obsahuje pokyny pro navázání důvěryhodného připojení mezi zařízeními pro příjem dat a transparentními branami IoT Edge. Ve scénáři transparentní brány jedno nebo více zařízení můžete předat své zprávy prostřednictvím zařízení s jednou bránou, které udržuje připojení k ioT hubu. Podřízené zařízení může být libovolná aplikace nebo platforma, která má identitu vytvořenou pomocí cloudové služby [Azure IoT Hub.](https://docs.microsoft.com/azure/iot-hub) V mnoha případech tyto aplikace používají [azure iot zařízení SDK](../iot-hub/iot-hub-devguide-sdks.md). Navazující zařízení může být dokonce aplikace spuštěná na samotném zařízení brány IoT Edge.

Existují tři obecné kroky k nastavení úspěšného připojení transparentní brány. Tento článek se zabývá třetím krokem:

1. Zařízení brány se musí bezpečně připojit k zařízením pro příjem dat, přijímat komunikaci z podřazovacích zařízení a směrovat zprávy na správné místo určení. Další informace najdete [v tématu Konfigurace zařízení IoT Edge tak, aby působilo jako transparentní brána](how-to-create-transparent-gateway.md).
2. Zařízení pro příjem dat potřebuje identitu zařízení, aby bylo možné ověřit pomocí služby IoT Hub a vědět, jak komunikovat prostřednictvím zařízení brány. Další informace najdete [v tématu Ověření navazujícího zařízení do služby Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. **Zařízení pro příjem dat se musí bezpečně připojit k zařízení brány.**

Tento článek identifikuje běžné problémy s následnými připojeními zařízení a provede vás při nastavování navazujících zařízení:

* Vysvětlení zabezpečení transportní vrstvy (TLS) a základy certifikátu.
* Vysvětlení, jak knihovny TLS fungují v různých operačních systémech a jak jednotlivé operační systémy napočoují certifikáty.
* Procházení ukázek Azure IoT v několika jazycích, které vám pomůžou začít.

V tomto článku termíny *brány* a *brány IoT Edge* odkazují na zařízení IoT Edge nakonfigurované jako transparentní brána.

## <a name="prerequisites"></a>Požadavky

* Mít soubor certifikátu **azure-iot-test-only.root.ca.cert.pem,** který byl vygenerován v [části Konfigurace zařízení IoT Edge, aby působil jako transparentní brána](how-to-create-transparent-gateway.md) dostupná na zařízení pro příjem dat. Vaše zařízení pro příjem dat používá tento certifikát k ověření identity zařízení brány.
* Mít upravený připojovací řetězec, který odkazuje na zařízení brány, jak je vysvětleno v [Ověření navazující zařízení do služby Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="prepare-a-downstream-device"></a>Příprava následného zařízení

Podřízené zařízení může být libovolná aplikace nebo platforma, která má identitu vytvořenou pomocí cloudové služby [Azure IoT Hub.](https://docs.microsoft.com/azure/iot-hub) V mnoha případech tyto aplikace používají [azure iot zařízení SDK](../iot-hub/iot-hub-devguide-sdks.md). Navazující zařízení může být dokonce aplikace spuštěná na samotném zařízení brány IoT Edge. Jiné zařízení IoT Edge však nemůže být po proudu brány IoT Edge.

>[!NOTE]
>Zařízení IoT, která mají registrované identity v ioT hubu, můžou pomocí [dvojčat modulů](../iot-hub/iot-hub-devguide-module-twins.md) izolovat jiný proces, hardware nebo funkce na jednom zařízení. Brány IoT Edge podporují připojení modulů s využitím symetrického ověřování pomocí klíče, ale ne ověřování certifikátů X.509.

Chcete-li připojit zařízení pro příjem dat k bráně IoT Edge, potřebujete dvě věci:

* Zařízení nebo aplikace, která je nakonfigurovaná pomocí připojovacího řetězce zařízení služby IoT Hub připojeného k informacím pro připojení k bráně.

    Tento krok je [vysvětlenv části Ověření navazujícího zařízení do služby Azure IoT Hub](how-to-authenticate-downstream-device.md).

* Zařízení nebo aplikace musí důvěřovat **kořenovému** certifikátu certifikační autority brány, aby ověřilo připojení TLS k zařízení brány.

    Tento krok je podrobně vysvětlen ve zbytku tohoto článku. Tento krok lze provést jedním ze dvou způsobů: instalací certifikátu certifikační autority do úložiště certifikátů operačního systému nebo (pro určité jazyky) odkazováním na certifikát v rámci aplikací pomocí sad Azure IoT SDK.

## <a name="tls-and-certificate-fundamentals"></a>Základy TLS a certifikátů

Výzva bezpečného připojení navazujících zařízení k IoT Edge je stejně jako jakákoli jiná zabezpečená komunikace mezi klientem a serverem, ke které dochází přes internet. Klient a server bezpečně komunikují přes Internet pomocí [zabezpečení transportní vrstvy (TLS).](https://en.wikipedia.org/wiki/Transport_Layer_Security) TLS je sestaven pomocí standardních infrastruktur [y veřejných klíčů (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) konstrukce zvané certifikáty. TLS je poměrně zapojenspecifikace a řeší širokou škálu témat souvisejících se zabezpečením dvou koncových bodů. Tato část shrnuje koncepty, které jsou pro bezpečné připojení zařízení k bráně IoT Edge důležité.

Když se klient připojí k serveru, server zobrazí řetězec certifikátů nazývaný *řetěz certifikátů serveru*. Řetěz certifikátů obvykle zahrnuje certifikát kořenové certifikační autority (CA), jeden nebo více zprostředkujících certifikátů certifikační autority a nakonec samotný certifikát serveru. Klient vytvoří vztah důvěryhodnosti se serverem kryptograficky ověřením celého řetězce certifikátů serveru. Toto ověření klientského řetězce certifikátů se nazývá *ověření řetězce serveru*. Klient kryptograficky zpochybňuje službu prokázat držení soukromého klíče přidruženého k certifikátu serveru v procesu *nazývaném doklad o vlastnictví*. Kombinace ověřování řetězce serveru a ověření vlastnictví se nazývá *ověřování serveru*. K ověření řetězce certifikátů serveru potřebuje klient kopii kořenového certifikátu certifikační autority, která byla použita k vytvoření (nebo vydání) certifikátu serveru. Obvykle je při připojování k webům prohlížeč předkonfigurován s běžně používanými certifikáty certifikační autority, takže klient má bezproblémový proces.

Když se zařízení připojí k Azure IoT Hub, zařízení je klient a cloudová služba IoT Hub je server. Cloudová služba IoT Hub je podpořena kořenovým certifikátem certifikační autority s názvem **Baltimore CyberTrust Root**, který je veřejně dostupný a široce používaný. Vzhledem k tomu, že certifikát certifikační autority služby IoT Hub je již nainstalován na většině zařízení, mnoho implementací TLS (OpenSSL, Schannel, LibreSSL) jej automaticky používá během ověřování certifikátu serveru. Zařízení, které se může úspěšně připojit k službě IoT Hub, může mít problémy s pokusem o připojení k bráně IoT Edge.

Když se zařízení připojí k bráně IoT Edge, zařízení pro příjem dat je klient a zařízení brány je server. Azure IoT Edge umožňuje operátorům (nebo uživatelům) vytvářet řetězy certifikátů brány, jak uznají za vhodné. Provozovatel se může rozhodnout použít veřejný certifikát certifikační autority, například Baltimore, nebo použít kořenový certifikát certifikační autority podepsaný svým držitelem (nebo interní). Veřejné certifikáty certifikační autority mají často spojené náklady, takže se obvykle používají v produkčních scénářích. Certifikáty certifikační autority podepsané svým držitelem jsou upřednostňovány pro vývoj a testování. Transparentní články nastavení brány uvedené v úvodu používají kořenové certifikáty certifikační autority podepsané svým držitelem.

Pokud pro bránu IoT Edge používáte kořenový certifikát certifikační autority podepsaný svým držitelem, musí být nainstalován na všech podřízených zařízeních, která se pokoušejí připojit k bráně, nebo k němu musí být poskytnut.

![Nastavení certifikátu brány](./media/how-to-create-transparent-gateway/gateway-setup.png)

Další informace o certifikátech IoT Edge a některých produkčních důsledcích najdete [v tématu Podrobnosti o použití certifikátu IoT Edge](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Poskytnutí kořenového certifikátu certifikační autority

K ověření certifikátů zařízení brány potřebuje zařízení pro příjem dat vlastní kopii kořenového certifikátu certifikační autority. Pokud jste k vytvoření testovacích certifikátů použili skripty uvedené v úložišti Git IoT Edge, bude se kořenový certifikát certifikační autority nazývat **azure-iot-test-only.root.ca.cert.pem**. Pokud jste tak ještě neučinili jako součást dalších kroků přípravy nanavazující zařízení, přesuňte tento soubor certifikátu do libovolného adresáře na zařízení pro příjem dat. K přesunutí souboru certifikátu můžete použít službu, jako je [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) nebo funkce, jako je protokol [zabezpečenékopie.](https://www.ssh.com/ssh/scp/)

## <a name="install-certificates-in-the-os"></a>Instalace certifikátů v os

Instalace kořenového certifikátu certifikační autority do úložiště certifikátů operačního systému obecně umožňuje většině aplikací používat kořenový certifikát certifikační autority. Existují některé výjimky, jako jsou aplikace NodeJS, které nepoužívají úložiště certifikátů operačního systému, ale spíše používají interní úložiště certifikátů runde runtime. Pokud se vám nepodaří nainstalovat certifikát na úrovni operačního systému, přeskočte na [použití certifikátů s sadami Azure IoT SDK](#use-certificates-with-azure-iot-sdks).

### <a name="ubuntu"></a>Ubuntu

Následující příkazy jsou příkladem toho, jak nainstalovat certifikát certifikační autority na hostitele Ubuntu. Tento příklad předpokládá, že používáte certifikát **azure-iot-test-only.root.ca.cert.pem** z článků požadavků a že jste certifikát zkopírovali do umístění na zařízení pro příjem dat.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Měli byste vidět zprávu, která říká, "Aktualizace certifikátů v /etc/ssl/certs... 1 přidáno, 0 odstraněno; hotovo."

### <a name="windows"></a>Windows

Následující kroky jsou příkladem instalace certifikátu certifikační autority na hostitele systému Windows. Tento příklad předpokládá, že používáte certifikát **azure-iot-test-only.root.ca.cert.pem** z článků požadavků a že jste certifikát zkopírovali do umístění na zařízení pro příjem dat.

Certifikáty můžete nainstalovat pomocí [certifikátu importu](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) powershellu jako správce:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

Certifikáty můžete nainstalovat také pomocí nástroje **certlm:**

1. V nabídce Start vyhledejte a vyberte **Spravovat certifikáty počítače**. Otevře se nástroj s názvem **certlm.**
2. Přejděte na **certifikáty – důvěryhodné** > **kořenové certifikační úřady**místního počítače .
3. Klepněte pravým tlačítkem myši na **příkaz Certifikáty** a vyberte**možnost Import**ovat **všechny úkoly** > . Měl by být spuštěn Průvodce importem certifikátu.
4. Postupujte podle pokynů a `<path>/azure-iot-test-only.root.ca.cert.pem`importujte soubor certifikátu . Po dokončení by se měla zobrazit zpráva "Úspěšně importováno".

Certifikáty můžete také nainstalovat programově pomocí rozhraní .NET API, jak je znázorněno v ukázce .NET dále v tomto článku.

Aplikace obvykle používají zásobník TLS zadaný systémem Windows s názvem [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) k bezpečnému připojení přes TLS. Schannel *vyžaduje,* aby všechny certifikáty byly nainstalovány v úložišti certifikátů systému Windows před pokusem o navázání připojení TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Použití certifikátů se sadami Azure IoT SDK

Tato část popisuje, jak se sady Azure IoT SDK připojují k zařízení IoT Edge pomocí jednoduchých ukázkových aplikací. Cílem všech ukázek je připojit klienta zařízení a odeslat telemetrické zprávy do brány, pak zavřít připojení a ukončit.

Před použitím vzorků na úrovni aplikace připravte dvě věci:

* Připojovací řetězec služby IoT Hub vašeho následného zařízení upravený tak, aby ukazoval na zařízení brány, a všechny certifikáty potřebné k ověření vašeho zařízení pro příjem dat do služby IoT Hub. Další informace najdete [v tématu Ověření navazujícího zařízení do služby Azure IoT Hub](how-to-authenticate-downstream-device.md).

* Úplná cesta ke kořenovému certifikátu certifikační autority, který jste zkopírovali a uložili někde na zařízení pro příjem dat.

    Například, `<path>/azure-iot-test-only.root.ca.cert.pem`.

### <a name="nodejs"></a>NodeJS

Tato část obsahuje ukázkovou aplikaci pro připojení klienta zařízení Azure IoT NodeJS k bráně IoT Edge. Pro aplikace NodeJS je nutné nainstalovat kořenový certifikát certifikační autority na úrovni aplikace, jak je znázorněno zde. Aplikace NodeJS nepoužívají úložiště certifikátů systému.

1. Získejte ukázku pro **edge_downstream_device js** ze [sady SDK zařízení Azure IoT pro uzd.js ukázky repo](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples).
2. Ujistěte se, že máte všechny předpoklady pro spuštění ukázky kontrolou **readme.md** souboru.
3. V souboru edge_downstream_device.js aktualizujte proměnné **connectionString** a **edge_ca_cert_path.**
4. Pokyny ke spuštění ukázky v zařízení naleznete v dokumentaci k sdk.

Chcete-li porozumět vzorku, který používáte, je následující fragment kódu způsob, jakým klientská sada SDK čte soubor certifikátu a používá jej k navázání zabezpečeného připojení TLS:

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Tato část zavádí ukázkovou aplikaci pro připojení klienta zařízení Azure IoT .NET k bráně IoT Edge. Aplikace .NET však mohou automaticky používat všechny nainstalované certifikáty v úložišti certifikátů systému na hostitelích Linuxu i Windows.

1. Získejte ukázku **edgedownstreamdevice** ze [složky ukázky IoT Edge .NET](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice).
2. Ujistěte se, že máte všechny předpoklady pro spuštění ukázky kontrolou **readme.md** souboru.
3. V souboru **Vlastnosti / launchSettings.json** aktualizujte proměnné **DEVICE_CONNECTION_STRING** a **CA_CERTIFICATE_PATH.** Pokud chcete použít certifikát nainstalovaný v úložišti důvěryhodných certifikátů v hostitelském systému, ponechejte tuto proměnnou prázdnou.
4. Pokyny ke spuštění ukázky v zařízení naleznete v dokumentaci k sdk.

Chcete-li programově nainstalovat důvěryhodný certifikát v úložišti certifikátů prostřednictvím aplikace .NET, podívejte se na funkci **InstallCACert()** v souboru **EdgeDownstreamDevice / Program.cs.** Tato operace je idempotentní, takže lze spustit vícekrát se stejnými hodnotami bez dalšího efektu.

### <a name="c"></a>C

Tato část zavádí ukázkovou aplikaci pro připojení klienta zařízení Azure IoT C k bráně IoT Edge. Sada C SDK může pracovat s mnoha knihovnami TLS, včetně OpenSSL, WolfSSL a Schannel. Další informace najdete v [tématu Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

1. Získejte **iotedge_downstream_device_sample** aplikaci ze [sady Azure IoT device SDK pro ukázky C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples).
2. Ujistěte se, že máte všechny předpoklady pro spuštění ukázky kontrolou **readme.md** souboru.
3. V souboru iotedge_downstream_device_sample.c aktualizujte proměnné **connectionString** a **edge_ca_cert_path.**
4. Pokyny ke spuštění ukázky v zařízení naleznete v dokumentaci k sdk.

Sada Azure IoT device SDK for C poskytuje možnost registrace certifikátu certifikační autority při nastavování klienta. Tato operace nenainstaluje certifikát nikde, ale používá formát řetězce certifikátu v paměti. Uložený certifikát je k dispozici do základního zásobníku TLS při navazování připojení.

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Pokud na hostitelích systému Windows nepoužíváte OpenSSL nebo jinou knihovnu TLS, je ve výchozím nastavení sada SDK používat schannel. Aby schannel fungoval, měl by být kořenový certifikát certifikační autority IoT `IoTHubDeviceClient_SetOption` Edge nainstalován v úložišti certifikátů systému Windows, nikoli nastaven pomocí operace.

### <a name="java"></a>Java

Tato část představuje ukázkovou aplikaci pro připojení klienta zařízení Azure IoT Java k bráně IoT Edge.

1. Získejte ukázku pro **send-event** z [azure iot zařízení SDK pro ukázky Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples).
2. Ujistěte se, že máte všechny předpoklady pro spuštění ukázky kontrolou **readme.md** souboru.
3. Pokyny ke spuštění ukázky v zařízení naleznete v dokumentaci k sdk.

### <a name="python"></a>Python

Tato část zavádí ukázkovou aplikaci pro připojení klienta zařízení Azure IoT Python k bráně IoT Edge.

1. Získejte ukázku pro **send_message** ze [sady Azure IoT device SDK pro ukázky Pythonu](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/advanced-edge-scenarios).
2. Ujistěte se, že jste spuštěni v kontejneru IoT Edge `EdgeHubConnectionString` `EdgeModuleCACertificateFile` nebo ve scénáři ladění, mají proměnné a prostředí nastaveny.
3. Pokyny ke spuštění ukázky v zařízení naleznete v dokumentaci k sdk.

## <a name="test-the-gateway-connection"></a>Testování připojení brány

Pomocí tohoto ukázkového příkazu můžete otestovat, že se vaše zařízení pro příjem dat může připojit k zařízení brány:

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

Tento příkaz testuje připojení přes MQTTS (port 8883). Pokud používáte jiný protokol, upravte příkaz podle potřeby pro AMQPS (5671) nebo HTTPS (433)

Výstup tohoto příkazu může být dlouhý, včetně informací o všech certifikátech v řetězci. Pokud je připojení úspěšné, zobrazí se `Verification: OK` řádek `Verify return code: 0 (ok)`jako nebo .

![Ověření připojení brány](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Poradce při potížích s připojením brány

Pokud má vaše zařízení leaf přerušované připojení k zařízení brány, vyzkoušejte následující kroky pro řešení.

1. Je název hostitele brány v připojovacím řetězci stejný jako hodnota název hostitele v souboru Config.yaml ioT Edge na zařízení brány?
2. Je název hostitele brány resolvable na IP adresu? Přerušovaná připojení můžete vyřešit buď pomocí služby DNS, nebo přidáním položky hostitelského souboru do zařízení list.
3. Jsou komunikační porty otevřené ve vaší bráně firewall? Komunikace založená na použitém protokolu (MQTTS:8883/AMQPS:5671/HTTPS:433) musí být možná mezi zařízením pro příjem dat a průhledným ioT edge.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak může IoT Edge rozšířit [offline funkce](offline-capabilities.md) na zařízení pro příjem dat.
