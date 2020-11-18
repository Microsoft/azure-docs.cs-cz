---
title: Připojení zařízení pro příjem dat – Azure IoT Edge | Microsoft Docs
description: Jak nakonfigurovat podřízená nebo koncová zařízení pro připojení k zařízením Azure IoT Edge brány
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: 78600b7b57a7c30fc609434a700f13fa21e079ce
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659637"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Připojení podřízeného zařízení k bráně služby Azure IoT Edge

Tento článek poskytuje pokyny k navázání důvěryhodného připojení mezi podřízenými zařízeními a IoT Edge transparentními branami. V případě transparentní brány může jedno nebo více zařízení předat zprávy pomocí jediného zařízení brány, které udržuje připojení k IoT Hub.

Existují tři obecné kroky k nastavení úspěšného transparentního připojení brány. Tento článek se věnuje třetímu kroku:

1. Nakonfigurujte zařízení brány jako server tak, aby se k nim mohli připojit podřízená zařízení bezpečně. Nastavte bránu tak, aby přijímala zprávy ze zařízení pro příjem dat, a směrovat je do správného umístění. Postup najdete v tématu [Konfigurace zařízení IoT Edge pro fungování jako transparentní brány](how-to-create-transparent-gateway.md).
2. Vytvořte identitu zařízení pro zařízení pro příjem dat, aby se mohla ověřit pomocí IoT Hub. Nakonfigurujte zařízení pro příjem zpráv, aby odesílala zprávy přes zařízení brány. Postup najdete v tématu [ověření zařízení pro příjem dat do Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. **Připojte zařízení pro příjem dat k zařízení brány a začněte odesílat zprávy.**

Tento článek pojednává o základních konceptech připojení k zařízením pro příjem dat a provede vás nastavením těchto zařízení:

* Vysvětlení protokolu TLS (Transport Layer Security) a základů certifikátů.
* Vysvětlení fungování knihoven TLS v různých operačních systémech a způsobu, jakým jednotlivé operační systémy pracují s certifikáty.
* Projděte si ukázky Azure IoT v několika jazycích, které vám pomůžou začít.

V tomto článku se pojmem *brána brány* a brána *IoT Edge* odkazují na IoT Edge zařízení nakonfigurované jako transparentní brána.

## <a name="prerequisites"></a>Požadavky

* K vygenerování certifikátu certifikační autority zařízení v části [konfigurace IoT Edgeho zařízení, které budou fungovat jako transparentní brána](how-to-create-transparent-gateway.md) na vašem zařízení pro příjem dat, je třeba použít kořenový soubor certifikátu certifikační autority. Vaše zařízení pro příjem dat používá tento certifikát k ověření identity zařízení brány. Pokud jste použili ukázkové certifikáty, kořenový certifikát certifikační autority se nazývá **Azure-IoT-test-Only. root. ca. CERT. pem**.
* Přidaný připojovací řetězec odkazuje na zařízení brány, jak je vysvětleno v tématu [ověření zařízení pro příjem dat do Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="prepare-a-downstream-device"></a>Příprava zařízení pro příjem dat

Pro zařízení pro příjem dat může být libovolná aplikace nebo platforma, která má vytvořenou identitu pomocí cloudové služby Azure IoT Hub. V mnoha případech tyto aplikace používají [sadu SDK pro zařízení Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). Navazující zařízení by mohlo být i aplikace běžící na samotném zařízení IoT Edge brány. Další IoT Edge zařízení ale nemůže být podřízená bráně IoT Edge.

>[!NOTE]
>Zařízení IoT registrovaná pomocí IoT Hub můžou používat [vlákna modulu](../iot-hub/iot-hub-devguide-module-twins.md) k izolaci různých procesů, hardwaru nebo funkcí na jednom zařízení. IoT Edge brány podporují připojení k navazujícím modulům pomocí ověřování pomocí symetrického klíče, ale ne pomocí ověřování pomocí certifikátu X. 509.

Pokud chcete připojit zařízení pro příjem dat k bráně IoT Edge, potřebujete dvě věci:

* Zařízení nebo aplikace, která je nakonfigurovaná s připojovacím řetězcem IoT Hub zařízení připojená s informacemi, aby se připojila k bráně.

    Tento krok byl dokončen v předchozím článku [ověření zařízení pro příjem dat do Azure IoT Hub](how-to-authenticate-downstream-device.md#retrieve-and-modify-connection-string).

* Zařízení nebo aplikace musí důvěřovat **certifikátu kořenové certifikační autority** brány, aby ověřili připojení TLS (Transport Layer Security) k zařízení brány.

    Tento krok je podrobněji vysvětlen ve zbývající části tohoto článku. Tento krok lze provést jedním ze dvou způsobů: instalací certifikátu certifikační autority do úložiště certifikátů operačního systému nebo (u některých jazyků) odkazem na certifikát v aplikacích, které používají sady SDK Azure IoT.

## <a name="tls-and-certificate-fundamentals"></a>Základy TLS a certifikátů

Výzvou k bezpečnému propojení navazujících zařízení s IoT Edge je stejně jako jakákoli jiná zabezpečená komunikace mezi klientem a serverem, ke které dochází přes Internet. Klient a server zabezpečeně komunikují přes Internet pomocí [protokolu TLS (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Protokol TLS je sestaven pomocí standardních konstrukcí [infrastruktury veřejných klíčů (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) nazývaných certifikáty. TLS je poměrně spojená specifikace a řeší široké spektrum témat souvisejících s zabezpečením dvou koncových bodů. V této části najdete přehled konceptů, které jsou důležité pro bezpečné připojení zařízení k bráně IoT Edge.

Když se klient připojí k serveru, server prezentuje řetěz certifikátů nazývaný *řetěz certifikátů serveru*. Řetěz certifikátů obvykle zahrnuje certifikát kořenové certifikační autority (CA), jeden nebo více zprostředkujících certifikátů certifikační autority a nakonec samotný certifikát serveru. Klient vytvoří vztah důvěryhodnosti se serverem tím, že kryptograficky ověří celý řetěz certifikátů serveru. Toto ověření klienta řetězu certifikátů serveru se nazývá *ověření řetězu serveru*. Klient vyzve Server k důkazu vlastnictví privátního klíče přidruženého k certifikátu serveru v procesu, který se nazývá *důkaz o vlastnictví*. Kombinace ověřování řetězu serveru a ověření vlastnictví se nazývá *ověřování serveru*. K ověření řetězu certifikátů serveru potřebuje klient kopii certifikátu kořenové certifikační autority, který se použil k vytvoření (nebo vydání) certifikátu serveru. V případě, že se připojujete k webům, je prohlížeč předem nakonfigurovaný s běžně používanými certifikáty certifikační autority, takže klient bude mít bezproblémové procesy.

Když se zařízení připojí k Azure IoT Hub, je to klient a cloudová služba IoT Hub je serverem. Cloudová služba IoT Hub se zálohuje certifikátem kořenové certifikační autority s názvem **Baltimore CyberTrust Root**, který je veřejně dostupný a široce používaný. Vzhledem k tomu, že IoT Hub certifikát certifikační autority je již na většině zařízení nainstalován, mnoho implementací TLS (OpenSSL, Schannel, LibreSSL) je automaticky používá během ověřování certifikátů serveru. Zařízení, které se úspěšně připojuje k IoT Hub může ale mít problémy se pokusem o připojení k IoT Edge bráně.

Když se zařízení připojí k bráně IoT Edge, jedná se o klienta a toto zařízení je server brány. Azure IoT Edge umožňuje sestavovat řetězy certifikátů brány, které se jim budou zobrazovat podle potřeby. Můžete použít veřejný certifikát certifikační autority, například Baltimore, nebo použít certifikát kořenové certifikační autority podepsaný svým držitelem (nebo interní). Certifikáty veřejné certifikační autority mají často náklady spojené s nimi, takže se obvykle používají v produkčních scénářích. Certifikáty certifikační autority podepsané svým držitelem jsou upřednostňovány pro vývoj a testování. Pokud používáte ukázkové certifikáty, jsou certifikáty s certifikátem kořenové certifikační autority podepsané svým držitelem.

Pokud pro bránu IoT Edge používáte certifikát od kořenové certifikační autority podepsaný svým držitelem, musí se nainstalovat nebo poskytnout všem zařízením, která se pokoušejí připojit k bráně.

![Nastavení certifikátu brány](./media/how-to-create-transparent-gateway/gateway-setup.png)

Další informace o IoT Edgech certifikátů a některých dopadech na produkční prostředí najdete v tématu [IoT Edge informace o využití certifikátu](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Zadejte certifikát kořenové certifikační autority.

Pro ověření certifikátů zařízení brány musí zařízení pro příjem dat vyžadovat vlastní kopii kořenového certifikátu certifikační autority. Pokud jste pro vytváření testovacích certifikátů použili skripty poskytované v úložišti Git IoT Edge, pak se certifikát kořenové certifikační autority nazývá **Azure-IoT-test-Only. root. ca. CERT. pem**. Pokud jste to ještě neudělali jako součást dalších kroků přípravy na zařízení, přesuňte tento soubor certifikátu do libovolného adresáře v zařízení pro příjem dat. K přesunutí souboru certifikátu můžete použít službu, jako je [Azure Key Vault](../key-vault/index.yml) , nebo funkci, jako je [protokol Secure Copy](https://www.ssh.com/ssh/scp/) .

## <a name="install-certificates-in-the-os"></a>Instalace certifikátů v operačním systému

Jakmile je certifikát kořenové certifikační autority na zařízení pro příjem dat, je nutné zajistit, aby aplikace, které jsou připojeny k bráně, měly přístup k certifikátu.

Instalace kořenového certifikátu certifikační autority v úložišti certifikátů operačního systému obecně umožňuje, aby většina aplikací používala certifikát kořenové certifikační autority. Existují nějaké výjimky, jako NodeJS aplikace, které nepoužívají úložiště certifikátů operačního systému, ale používají interní úložiště certifikátů modulu runtime uzlu. Pokud nemůžete nainstalovat certifikát na úrovni operačního systému, přeskočte k [používání certifikátů se sadami SDK služby Azure IoT](#use-certificates-with-azure-iot-sdks).

### <a name="ubuntu"></a>Ubuntu

Následující příkazy jsou příkladem instalace certifikátu certifikační autority do hostitele Ubuntu. V tomto příkladu se předpokládá, že používáte certifikát **Azure-IoT-test-Only. root. ca. CERT. pem** z článků s požadavky a že jste certifikát zkopírovali do umístění na zařízení pro příjem dat.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Měla by se zobrazit zpráva s informacemi o aktualizaci certifikátů v/etc/SSL/certs.... 1 Přidání, 0 odebraných; Hotovo. "

### <a name="windows"></a>Windows

Následující postup ukazuje příklad instalace certifikátu certifikační autority na hostiteli s Windows. V tomto příkladu se předpokládá, že používáte certifikát **Azure-IoT-test-Only. root. ca. CERT. pem** z článků s požadavky a že jste certifikát zkopírovali do umístění na zařízení pro příjem dat.

Certifikáty můžete nainstalovat pomocí rutiny [Import-Certificate](/powershell/module/pkiclient/import-certificate?view=win10-ps) prostředí PowerShell jako správce:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

Certifikáty můžete nainstalovat také pomocí nástroje **Certlm** :

1. V nabídce Start vyhledejte a vyberte **spravovat certifikáty počítače**. Otevře se nástroj s názvem **Certlm** .
2. Přejděte na **certifikáty –**  >  **Důvěryhodné kořenové certifikační autority** místního počítače.
3. Klikněte pravým tlačítkem na **certifikáty** a vyberte **všechny úkoly**  >  **importovat**. Průvodce importem certifikátu by se měl spustit.
4. Postupujte podle pokynů jako směrovaný a importujte soubor certifikátu `<path>/azure-iot-test-only.root.ca.cert.pem` . Po dokončení by se měla zobrazit zpráva "úspěšně importováno".

Certifikáty můžete nainstalovat také programově pomocí rozhraní .NET API, jak je znázorněno v ukázce .NET dále v tomto článku.

Obvykle aplikace používají k zabezpečenému připojení přes TLS sadu protokolů TLS s názvem [Schannel](/windows/desktop/com/schannel) . Před pokusem o navázání připojení TLS *vyžaduje* Schannel instalaci všech certifikátů v úložišti certifikátů Windows.

## <a name="use-certificates-with-azure-iot-sdks"></a>Použití certifikátů se sadami SDK služby Azure IoT

Tato část popisuje, jak se sady SDK Azure IoT připojují k zařízení IoT Edge pomocí jednoduchých ukázkových aplikací. Cílem všech ukázek je připojení klienta zařízení a odeslání zpráv telemetrie do brány, následné ukončení připojení a ukončení.

Před použitím ukázek na úrovni aplikace jsou připravené dvě věci:

* IoT Hub připojovacího řetězce pro vaše zařízení, který se změnil tak, aby odkazoval na zařízení brány, a všechny certifikáty potřebné k ověření vašeho zařízení pro příjem dat IoT Hub. Další informace najdete v tématu [ověření zařízení pro příjem dat do Azure IoT Hub](how-to-authenticate-downstream-device.md).

* Úplná cesta k certifikátu kořenové certifikační autority, kterou jste zkopírovali a uložili někde na vašem zařízení pro příjem dat.

    Například, `<path>/azure-iot-test-only.root.ca.cert.pem`.

### <a name="nodejs"></a>NodeJS

V této části najdete ukázkovou aplikaci pro připojení klienta zařízení Azure IoT NodeJS k bráně IoT Edge. V případě aplikací NodeJS je nutné na úrovni aplikace nainstalovat certifikát kořenové certifikační autority, jak je znázorněno zde. NodeJS aplikace nepoužívají úložiště certifikátů systému.

1. Získejte ukázku pro **edge_downstream_device.js** ze [sady SDK pro zařízení Azure IoT pro Node.js ukázkové úložiště](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples).
2. Ujistěte se, že máte všechny požadavky na spuštění ukázky, a to kontrolou souboru **Readme.MD** .
3. V souboru edge_downstream_device.js aktualizujte proměnné **ConnectionString** a **edge_ca_cert_path** .
4. Pokyny, jak spustit ukázku na vašem zařízení, najdete v dokumentaci k sadě SDK.

Pro pochopení ukázky, kterou používáte, je následující fragment kódu způsob, jakým klientská sada SDK čte soubor certifikátu a používá ho k navázání zabezpečeného připojení TLS:

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

V této části se seznámíte s ukázkovou aplikací pro připojení klienta zařízení Azure IoT .NET k bráně IoT Edge. Aplikace .NET ale v úložišti certifikátů systému v systémech Linux i Windows automaticky můžou používat všechny nainstalované certifikáty.

1. Získejte ukázku pro **EdgeDownstreamDevice** ze [složky ukázek rozhraní .NET IoT Edge](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice).
2. Ujistěte se, že máte všechny požadavky na spuštění ukázky, a to kontrolou souboru **Readme.MD** .
3. V okně **vlastnosti/launchSettings.jsv** souboru aktualizujte proměnné **DEVICE_CONNECTION_STRING** a **CA_CERTIFICATE_PATH** . Pokud chcete použít certifikát nainstalovaný v úložišti důvěryhodných certifikátů v hostitelském systému, ponechte tuto proměnnou prázdnou.
4. Pokyny, jak spustit ukázku na vašem zařízení, najdete v dokumentaci k sadě SDK.

Chcete-li programově nainstalovat důvěryhodný certifikát do úložiště certifikátů prostřednictvím aplikace .NET, přečtěte si funkci **InstallCACert ()** v souboru **EdgeDownstreamDevice/program.cs** . Tato operace je idempotentní, takže ji můžete spustit vícekrát se stejnými hodnotami, a to bez dalšího efektu.

### <a name="c"></a>C

V této části se seznámíte s ukázkovou aplikací pro připojení klienta zařízení Azure IoT C k bráně IoT Edge. Sada C SDK může pracovat s mnoha knihovnami TLS, včetně OpenSSL, WolfSSL a Schannel. Další informace najdete v tématu [sada SDK pro Azure IoT C](https://github.com/Azure/azure-iot-sdk-c).

1. Získejte aplikaci **iotedge_downstream_device_sample** ze [sady SDK pro zařízení Azure IoT pro C Samples](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples).
2. Ujistěte se, že máte všechny požadavky na spuštění ukázky, a to kontrolou souboru **Readme.MD** .
3. V souboru iotedge_downstream_device_sample. c aktualizujte proměnné **ConnectionString** a **edge_ca_cert_path** .
4. Pokyny, jak spustit ukázku na vašem zařízení, najdete v dokumentaci k sadě SDK.


Sada SDK pro zařízení Azure IoT pro jazyk C poskytuje možnost Registrovat certifikát certifikační autority při nastavování klienta. Tato operace neinstaluje certifikát kdekoli, ale místo toho používá formát řetězce certifikátu v paměti. Při navazování připojení se uložený certifikát poskytne základnímu zásobníku TLS.

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

>[!NOTE]
> Metoda registrace certifikátu certifikační autority při nastavování klienta se může změnit, pokud se používá [spravovaný](https://github.com/Azure/azure-iot-sdk-c#packages-and-libraries) balíček nebo knihovna. Například [knihovna založená na IDE Arduino](https://github.com/azure/azure-iot-arduino) bude vyžadovat přidání certifikátu certifikační autority do pole certifikátů definovaného v souboru Global certs [. c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) namísto použití `IoTHubDeviceClient_LL_SetOption` operace.  

Pokud v hostitelích se systémem Windows nepoužíváte OpenSSL nebo jinou knihovnu TLS, použije sada SDK ve výchozím nastavení zprostředkovatele Schannel. Aby mohla zprostředkovatel Schannel fungovat, musí být v úložišti certifikátů Windows nainstalovaný certifikát certifikační autority IoT Edge root, který není nastavený pomocí této `IoTHubDeviceClient_SetOption` operace.

### <a name="java"></a>Java

V této části se seznámíte s ukázkovou aplikací pro připojení klienta zařízení s Azure IoT Java k bráně IoT Edge.

1. Ukázka pro **odeslání události** ze [sady SDK pro zařízení Azure IoT pro Java Samples](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)
2. Ujistěte se, že máte všechny požadavky na spuštění ukázky, a to kontrolou souboru **Readme.MD** .
3. Pokyny, jak spustit ukázku na vašem zařízení, najdete v dokumentaci k sadě SDK.

### <a name="python"></a>Python

Tato část zavádí ukázkovou aplikaci pro připojení klienta zařízení Azure IoT Python k bráně IoT Edge.

1. Získejte ukázku pro **send_message_downstream** ze [sady Azure IoT SDK pro zařízení pro Python Samples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios).
2. Nastavte `IOTHUB_DEVICE_CONNECTION_STRING` `IOTEDGE_ROOT_CA_CERT_PATH` proměnné prostředí a, jak je uvedeno v komentářích ke skriptům Pythonu.
3. Další pokyny ke spuštění ukázky na zařízení najdete v dokumentaci k sadě SDK.

## <a name="test-the-gateway-connection"></a>Test připojení brány

Pomocí tohoto ukázkového příkazu na zařízení pro příjem dat otestujte, jestli se může připojit k zařízení brány:

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

Tento příkaz testuje připojení přes MQTTS (port 8883). Pokud používáte jiný protokol, upravte příkaz podle potřeby pro AMQPS (5671) nebo HTTPS (433).

Výstup tohoto příkazu může být dlouhý, včetně informací o všech certifikátech v řetězu. Pokud je připojení úspěšné, zobrazí se řádek, například `Verification: OK` nebo `Verify return code: 0 (ok)` .

![Ověření připojení brány](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Řešení potíží s připojením brány

Pokud má vaše listové zařízení přerušované připojení k zařízení brány, zkuste k řešení použít následující postup.

1. Je název hostitele brány v připojovacím řetězci stejný jako hodnota názvu hostitele v souboru IoT Edge config. yaml na zařízení brány?
2. Je název hostitele brány přeložitelný na IP adresu? Přerušovaná připojení můžete vyřešit buď pomocí DNS, nebo přidáním položky souboru hostitele na listovém zařízení.
3. Jsou v bráně firewall otevřené komunikační porty? Komunikace založená na použitém protokolu (MQTTS: 8883/AMQPS: 5671/HTTPS: 433) musí být mezi podřízenými zařízeními a transparentním IoT Edge.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak IoT Edge můžou do zařízení pro příjem dat nastavovat [Možnosti offline](offline-capabilities.md) .