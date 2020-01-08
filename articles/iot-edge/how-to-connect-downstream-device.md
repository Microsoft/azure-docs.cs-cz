---
title: Připojit zařízení příjem dat – Azure IoT Edge | Dokumentace Microsoftu
description: Jak nakonfigurovat podřízená nebo koncová zařízení pro připojení k zařízením Azure IoT Edge brány
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 20de7bc55a62a44d1fa852d86705e7596e1776d6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434439"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Připojte zařízení za příjem dat k bráně Azure IoT Edge

Tento článek poskytuje pokyny k navázání důvěryhodného připojení mezi podřízenými zařízeními a IoT Edge transparentními branami. V případě transparentní brány může jedno nebo více zařízení předat zprávy pomocí jediného zařízení brány, které udržuje připojení k IoT Hub. Příjem dat zařízení může být jakékoli aplikaci nebo platforma, která má identitu vytvořené pomocí [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) cloudovou službu. V mnoha případech se tyto aplikace používat [zařízení Azure IoT SDK](../iot-hub/iot-hub-devguide-sdks.md). Navazující zařízení by mohlo být i aplikace běžící na samotném zařízení IoT Edge brány. 

Existují tři obecné kroky k nastavení úspěšného transparentního připojení brány. Tento článek se věnuje třetímu kroku:

1. Zařízení brány musí bezpečně připojit se k zařízením pro příjem dat, přijímat komunikaci ze zařízení pro příjem dat a směrovat zprávy do správného umístění. Další informace najdete v tématu [Konfigurace zařízení IoT Edge pro fungování jako transparentní brány](how-to-create-transparent-gateway.md).
2. Zařízení pro příjem dat potřebuje identitu zařízení, aby se mohla ověřit pomocí IoT Hub a ví, jak komunikovat přes jeho zařízení brány. Další informace najdete v tématu [ověření zařízení pro příjem dat do Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. **Zařízení pro příjem dat se musí bezpečně připojit k zařízení brány.**

Tento článek identifikuje běžné problémy s připojením zařízení příjem dat a provede vás v nastavení zařízení příjem dat podle: 

* Kde vysvětluje zabezpečení transportní vrstvy (TLS) a základní informace o certifikátu. 
* Vysvětlení fungování TLS knihovny v různých operačních systémech a jak se má každý operační systém zacházet s certifikáty.
* Procházení Azure IoT ukázky v několika jazycích, které vám pomůžou začít. 

V tomto článku se podmínky *brány* a *brána IoT Edge* odkazovat na zařízení IoT Edge nakonfigurovaná jako transparentní brána. 

## <a name="prerequisites"></a>Požadavky 

* K dispozici je soubor certifikátu **Azure-IoT-test-Only. root. ca. CERT. pem** , který se vygeneroval v části [Konfigurace zařízení IoT Edge tak, aby fungoval jako transparentní brána](how-to-create-transparent-gateway.md) , která je dostupná na vašem zařízení pro příjem dat. Vaše zařízení pro příjem dat používá tento certifikát k ověření identity zařízení brány. 
* Přidaný připojovací řetězec odkazuje na zařízení brány, jak je vysvětleno v tématu [ověření zařízení pro příjem dat do Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="prepare-a-downstream-device"></a>Příprava příjem dat zařízení

Příjem dat zařízení může být jakékoli aplikaci nebo platforma, která má identitu vytvořené pomocí [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) cloudovou službu. V mnoha případech se tyto aplikace používat [zařízení Azure IoT SDK](../iot-hub/iot-hub-devguide-sdks.md). Navazující zařízení by mohlo být i aplikace běžící na samotném zařízení IoT Edge brány. Další IoT Edge zařízení ale nemůže být podřízená bráně IoT Edge. 

>[!NOTE]
>Zařízení IoT, která mají identity registrovaná v IoT Hub můžou používat [vlákna modulu](../iot-hub/iot-hub-devguide-module-twins.md) k izolaci různých procesů, hardwaru nebo funkcí na jednom zařízení. IoT Edge brány podporují připojení k navazujícím modulům pomocí ověřování pomocí symetrického klíče, ale ne pomocí ověřování pomocí certifikátu X. 509. 

Připojit zařízení příjem dat na hraničních zařízeních IoT gateway, je třeba dvě věci:

* Zařízení nebo aplikaci, která je nakonfigurována s řetězcem připojení zařízení IoT Hub s informace pro připojení k bráně. 

    Tento krok je vysvětlen v tématu [ověření zařízení pro příjem dat do Azure IoT Hub](how-to-authenticate-downstream-device.md).

* Zařízení nebo aplikace musí důvěřovat certifikátu **kořenové certifikační autority** brány, aby ověřili připojení TLS k zařízení brány. 

    Tento krok je podrobněji vysvětlen ve zbývající části tohoto článku. Tento krok lze provést jedním ze dvou způsobů: instalací certifikátu certifikační autority do úložiště certifikátů operačního systému nebo (u některých jazyků) odkazem na certifikát v aplikacích, které používají sady SDK Azure IoT.

## <a name="tls-and-certificate-fundamentals"></a>Základy TLS a certifikátů

Výzvu se bezpečně připojuje podřízené zařízení k IoT Edge je stejně jako jinou klientem a serverem zabezpečené komunikaci, která nastane v Internetu. Klient a server zabezpečeně komunikovat přes internet pomocí [Transport layer security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Protokol TLS je sestavena pomocí standardní [infrastruktury veřejných klíčů (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) konstrukce nazývané certifikáty. TLS je poměrně spojená specifikace a řeší široké spektrum témat souvisejících s zabezpečením dvou koncových bodů. V této části najdete přehled konceptů, které jsou důležité pro bezpečné připojení zařízení k bráně IoT Edge.

Když se klient připojí k serveru, na serveru představuje řetěz certifikátů, volá se, *řetěz certifikátů serveru*. Řetěz certifikátů obvykle zahrnuje kořenový certifikát certifikační autority (CA), jeden nebo více zprostředkujících certifikátů CA a nakonec vlastní certifikát serveru. Klient vytváří vztah důvěryhodnosti se serverem pomocí kryptograficky ověření řetězu certifikátů celý server. Toto ověření klienta řetězu certifikátů serveru se nazývá *ověření řetězu serveru*. Klient kryptograficky vystavuje službu, aby prokázal vlastnictví privátního klíče přidruženého k certifikátu serveru v procesu, který se nazývá *důkaz o vlastnictví*. Kombinace ověřování řetězu serveru a ověření vlastnictví se nazývá *ověřování serveru*. Pokud chcete ověřit řetěz certifikátů serveru, klient potřebuje kopii certifikátu kořenové certifikační Autority, která byla použita k vytvoření (nebo vydat) na server certifikát. Obvykle při připojování k webům, prohlížeč vybavená předem nakonfigurovaným rozhraním běžně používaných certifikátů certifikačních Autorit, klient získá bezproblémový procesu. 

Když se zařízení připojí ke službě Azure IoT Hub, klient je zařízení a cloudové služby IoT Hub je na serveru. Cloudové služby IoT Hub je založená na certifikátu kořenové certifikační Autority volá **Baltimore CyberTrust Root**, což je veřejně dostupné a široce používaným. Protože certifikát služby IoT Hub certifikační Autority je již nainstalován na většině zařízení, mnoho implementací protokolu TLS (OpenSSL, Schannel, LibreSSL) automaticky použije při ověřování certifikátu serveru. Zařízení, která se může úspěšně připojit ke službě IoT Hub může mít problémy při pokusu o připojení k bráně IoT Edge.

Když se zařízení připojí k bráně IoT Edge, klient je podřízený zařízení a zařízení brány je na serveru. Azure IoT Edge umožňuje k vytváření řetězů certifikátů brány, ale jsou vhodná pro operátory (nebo uživatelé). Operátor, který můžete použít veřejný certifikát certifikační Autority, jako je Baltimore, nebo podepsaný svým držitelem (nebo interní) kořenový certifikát certifikační Autority. Veřejné certifikáty certifikační Autority často mají náklady spojené s nimi, proto se obvykle používají v produkčních scénářích. Certifikáty certifikační Autority podepsané svým držitelem jsou upřednostňované pro vývoj a testování. Články s nastavením transparentní brány uvedené v úvodu používají certifikáty kořenové certifikační autority podepsané svým držitelem. 

Pokud používáte certifikát podepsaný svým držitelem kořenové certifikační Autority pro brány IoT Edge, musí být nainstalovaný na nebo k dispozici na všech zařízeních podřízené pokusu o připojení k bráně. 

![Instalace certifikátu brány](./media/how-to-create-transparent-gateway/gateway-setup.png)

Další informace o certifikátech pro IoT Edge a některé důsledky produkčního prostředí, najdete v článku [podrobnosti o použití IoT Edge certifikát](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Zadejte certifikát kořenové certifikační autority.

Pro ověření certifikátů zařízení brány musí zařízení pro příjem dat vyžadovat vlastní kopii kořenového certifikátu certifikační autority. Pokud jste pro vytváření testovacích certifikátů použili skripty poskytované v úložišti Git IoT Edge, pak se certifikát kořenové certifikační autority nazývá **Azure-IoT-test-Only. root. ca. CERT. pem**. Pokud jste to ještě neudělali jako součást dalších kroků přípravy na zařízení, přesuňte tento soubor certifikátu do libovolného adresáře v zařízení pro příjem dat. K přesunutí souboru certifikátu můžete použít službu, jako je [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) , nebo funkci, jako je [protokol Secure Copy](https://www.ssh.com/ssh/scp/) .

## <a name="install-certificates-in-the-os"></a>Instalace certifikátů v operačním systému

Instalace kořenového certifikátu certifikační autority v úložišti certifikátů operačního systému obecně umožňuje, aby většina aplikací používala certifikát kořenové certifikační autority. Existují nějaké výjimky, jako NodeJS aplikace, které nepoužívají úložiště certifikátů operačního systému, ale používají interní úložiště certifikátů modulu runtime uzlu. Pokud nemůžete nainstalovat certifikát na úrovni operačního systému, přeskočte k [používání certifikátů se sadami SDK služby Azure IoT](#use-certificates-with-azure-iot-sdks). 

### <a name="ubuntu"></a>Ubuntu

Následující příkazy jsou příkladem toho, jak nainstalovat certifikát certifikační Autority na hostiteli se systémem Ubuntu. V tomto příkladu se předpokládá, že používáte certifikát **Azure-IoT-test-Only. root. ca. CERT. pem** z článků s požadavky a že jste certifikát zkopírovali do umístění na zařízení pro příjem dat.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Měla by se zobrazit zpráva s informacemi o aktualizaci certifikátů v/etc/SSL/certs.... 1 Přidání, 0 odebraných; Hotovo. "

### <a name="windows"></a>Windows

Následující postup je příklad toho, jak nainstalovat certifikát certifikační Autority na hostitele Windows. V tomto příkladu se předpokládá, že používáte certifikát **Azure-IoT-test-Only. root. ca. CERT. pem** z článků s požadavky a že jste certifikát zkopírovali do umístění na zařízení pro příjem dat.

Certifikáty můžete nainstalovat pomocí rutiny [Import-Certificate](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) prostředí PowerShell jako správce:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

Certifikáty můžete nainstalovat také pomocí nástroje **Certlm** : 

1. V nabídce Start, vyhledejte a vyberte **spravovat certifikáty počítače**. Nástroj, který volá **certlm** otevře.
2. Přejděte do **certifikáty - místní počítač** > **důvěryhodné kořenové certifikační autority**.
3. Klikněte pravým tlačítkem na **certifikáty** a vyberte **všechny úkoly** > **Import**. Průvodce importem certifikátu by měl spustit. 
4. Postupujte podle pokynů a importovat soubor certifikátu `<path>/azure-iot-test-only.root.ca.cert.pem`. Po dokončení, měli byste vidět zprávu "Úspěšně naimportoval". 

Můžete také nainstalovat certifikáty programově pomocí rozhraní API pro .NET, jak je znázorněno v ukázce .NET dále v tomto článku. 

Obvykle aplikace používat Windows, pokud volá TLS zásobníku [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) pro zabezpečené připojení přes protokol TLS. Schannel *vyžaduje* nainstalovat všechny certifikáty v úložišti certifikátů Windows před pokusem o připojení protokolu TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Používání certifikátů s sad SDK Azure IoT

Tato část popisuje, jak připojit Azure IoT SDK pro zařízení IoT Edge pomocí jednoduchých ukázkových aplikací. Cílem všechny ukázky je připojení klienta zařízení a odesílání telemetrických zpráv k bráně, pak ukončete připojení a ukončení. 

Máte připravené před použitím ukázek úrovni aplikace splňovat dvě podmínky:

* IoT Hub připojovacího řetězce pro vaše zařízení, který se změnil tak, aby odkazoval na zařízení brány, a všechny certifikáty potřebné k ověření vašeho zařízení pro příjem dat IoT Hub. Další informace najdete v tématu [ověření zařízení pro příjem dat do Azure IoT Hub](how-to-authenticate-downstream-device.md).

* Úplná cesta k certifikátu kořenové certifikační Autority, který jste zkopírovali a někam uložit na příjem dat zařízení.

    Například, `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJS

Tato část poskytuje ukázkové aplikace pro připojení klienta Azure IoT NodeJS zařízení brány IoT Edge. V případě aplikací NodeJS je nutné na úrovni aplikace nainstalovat certifikát kořenové certifikační autority, jak je znázorněno zde. NodeJS aplikace nepoužívají úložiště certifikátů systému. 

1. Získat ukázky pro **edge_downstream_device.js** z [zařízení Azure IoT SDK pro Node.js – ukázky úložiště](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples). 
2. Ujistěte se, že máte vše potřebné pro spuštění ukázky kontrolou **readme.md** souboru. 
3. V souboru edge_downstream_device.js aktualizujte **connectionString** a **edge_ca_cert_path** proměnné. 
4. Naleznete v dokumentaci sady SDK pro pokyny o tom, jak ukázku spustit ve vašem zařízení. 

Informace o tom ukázku, kterou používáte, následující fragment kódu je jak Klientská sada SDK čte soubor certifikátu a použije ho k navázat zabezpečené připojení TLS: 

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Tato část představuje ukázkovou aplikaci pro připojení klienta Azure IoT .NET zařízení brány IoT Edge. Možnost automaticky používat všechny nainstalované certifikáty v úložišti certifikátů systému na hostitele systému Linux a Windows jsou však aplikace .NET.

1. Získat ukázky pro **EdgeDownstreamDevice** z [složce samples IoT Edge .NET](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice). 
2. Ujistěte se, že máte vše potřebné pro spuštění ukázky kontrolou **readme.md** souboru. 
3. V **vlastnosti / launchSettings.json** soubor, aktualizovat **DEVICE_CONNECTION_STRING** a **CA_CERTIFICATE_PATH** proměnné. Pokud chcete použít certifikát nainstalován v úložišti důvěryhodných certifikátů v hostitelském systému, ponechte tato proměnná prázdná. 
4. Naleznete v dokumentaci sady SDK pro pokyny o tom, jak ukázku spustit ve vašem zařízení. 

Pokud chcete prostřednictvím kódu programu nainstalovat důvěryhodný certifikát v úložišti certifikátů prostřednictvím aplikace .NET, přečtěte si **InstallCACert()** fungovat v **EdgeDownstreamDevice / Program.cs** souboru. Tato operace je idempotentní, proto můžete spustit více než jednou pomocí stejných hodnot s další neplatí. 

### <a name="c"></a>C

Tato část představuje ukázkovou aplikaci pro připojení klienta Azure IoT C zařízení brány IoT Edge. SDK pro jazyk C mohou pracovat s mnoha TLS knihoven, včetně OpenSSL, WolfSSL a zprostředkovatele Schannel. Další informace najdete v tématu [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). 

1. Získejte **iotedge_downstream_device_sample** aplikace z [zařízení Azure IoT SDK pro C ukázky](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples). 
2. Ujistěte se, že máte vše potřebné pro spuštění ukázky kontrolou **readme.md** souboru. 
3. V souboru iotedge_downstream_device_sample.c aktualizujte **connectionString** a **edge_ca_cert_path** proměnné. 
4. Naleznete v dokumentaci sady SDK pro pokyny o tom, jak ukázku spustit ve vašem zařízení. 

Zařízení Azure IoT SDK pro jazyk C poskytuje možnost zaregistrovat certifikát certifikační Autority při nastavování klienta. Tato operace není nainstalován certifikát kdekoli, ale místo toho používá formát řetězce certifikátu v paměti. Uložený certifikát získáte základní TLS zásobníku při navazování připojení. 

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Na hostitelích s Windows Pokud nepoužíváte OpenSSL nebo jiné knihovny TLS, sady SDK ve výchozím nastavení pomocí zprostředkovatele Schannel. Zprostředkovatele Schannel pro práci, je třeba nainstalovat certifikát kořenové certifikační Autority IoT Edge v úložiště certifikátů Windows, není nastavený pomocí `IoTHubDeviceClient_SetOption` operace. 

### <a name="java"></a>Java

Tato část představuje ukázkovou aplikaci pro připojení klienta zařízení Azure IoT Java na hraničních zařízeních IoT gateway. 

1. Získat ukázky pro **událost odeslání** z [zařízení Azure IoT SDK pro ukázky v Javě](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples). 
2. Ujistěte se, že máte vše potřebné pro spuštění ukázky kontrolou **readme.md** souboru. 
3. Naleznete v dokumentaci sady SDK pro pokyny o tom, jak ukázku spustit ve vašem zařízení.

### <a name="python"></a>Python

Tato část představuje ukázkovou aplikaci pro připojení Azure IoT Pythonového klienta zařízení brány IoT Edge. 

1. Získejte ukázku pro **send_message** ze [sady Azure IoT SDK pro zařízení pro Python Samples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/advanced-edge-scenarios). 
2. Ujistěte se, že jste buď spustili v kontejneru IoT Edge, nebo ve scénáři ladění, které mají nastavené proměnné prostředí `EdgeHubConnectionString` a `EdgeModuleCACertificateFile`.
3. Naleznete v dokumentaci sady SDK pro pokyny o tom, jak ukázku spustit ve vašem zařízení. 


## <a name="test-the-gateway-connection"></a>Otestovat připojení brány

Pomocí tohoto ukázkového příkazu otestujte, jestli se vaše zařízení pro příjem dat může připojit k zařízení brány: 

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

Tento příkaz testuje připojení přes MQTTS (port 8883). Pokud používáte jiný protokol, upravte příkaz podle potřeby pro AMQPS (5671) nebo HTTPS (433).

Výstup tohoto příkazu může být dlouhý, včetně informací o všech certifikátech v řetězu. Pokud je připojení úspěšné, zobrazí se řádek, například `Verification: OK` nebo `Verify return code: 0 (ok)`.

![Ověření připojení brány](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Řešení potíží s připojením brány

Pokud má vaše listové zařízení přerušované připojení k zařízení brány, zkuste k řešení použít následující postup. 

1. Je název hostitele brány v připojovacím řetězci stejný jako hodnota názvu hostitele v souboru IoT Edge config. yaml na zařízení brány?
2. Je název hostitele brány přeložitelný na IP adresu? Přerušovaná připojení můžete vyřešit buď pomocí DNS, nebo přidáním položky souboru hostitele na listovém zařízení.
3. Jsou v bráně firewall otevřené komunikační porty? Komunikace založená na použitém protokolu (MQTTS: 8883/AMQPS: 5671/HTTPS: 433) musí být mezi podřízenými zařízeními a transparentním IoT Edge.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak můžete rozšířit IoT Edge [offline možnosti](offline-capabilities.md) pro příjem dat zařízení. 
