---
title: Připojit zařízení příjem dat – Azure IoT Edge | Dokumentace Microsoftu
description: Jak nakonfigurovat směru server-klient nebo zařízení typu list pro připojení prostřednictvím zařízení brány Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: d41ec0bc959eb264564d49ae6ac31aa30b3be98a
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55492755"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Připojte zařízení za příjem dat k bráně Azure IoT Edge

Azure IoT Edge umožňuje transparentní brána scénářů, ve kterých jedno nebo více zařízení můžete předat své zprávy prostřednictvím jedné brány zařízení, která udržuje připojení ke službě IoT Hub. Jakmile budete mít nakonfigurované zařízení brány, musíte vědět, jak bezpečně připojit podřízené zařízení. 

Tento článek identifikuje běžné problémy s připojením zařízení příjem dat a provede vás v nastavení zařízení příjem dat podle: 

* Kde vysvětluje zabezpečení transportní vrstvy (TLS) a základní informace o certifikátu. 
* Vysvětlení fungování TLS knihovny v různých operačních systémech a jak se má každý operační systém zacházet s certifikáty.
* Procházení Azure IoT ukázky v několika jazycích, které vám pomůžou začít. 

V tomto článku se podmínky *brány* a *brána IoT Edge* odkazovat na zařízení IoT Edge nakonfigurovaná jako transparentní brána. 

## <a name="prerequisites"></a>Požadavky

Před provedením kroků v tomto článku, byste měli mít dvě zařízení připravené k použití:

1. Nastavit jako transparentní brána zařízení IoT Edge. 
    [Konfigurace zařízení tak, aby fungoval jako transparentní brána IoT Edge](how-to-create-transparent-gateway.md)

    Jakmile budete mít nakonfigurované vaše zařízení brány, zkopírujte **azure-iot-test-only.root.ca.cert.pem** certifikátu ze brána a budete ho mít dostupný kdekoli na příjem dat zařízení. 

2. Příjem dat zařízení, která má identitu zařízení ze služby IoT Hub. 
    Zařízení IoT Edge nelze použít jako podřízené zařízení. Místo toho použijte zařízení registrovaná jako regulární zařízení IoT ve službě IoT Hub. Na portálu, můžete zaregistrovat nové zařízení na **zařízení IoT** oddílu. Nebo můžete použít rozhraní příkazového řádku Azure pro [zaregistrovat zařízení](../iot-hub/quickstart-send-telemetry-c.md#register-a-device). Zkopírujte připojovací řetězec a budete ho mít dostupný pro použití v dalších částech. 

    V současné době pouze podřízené zařízení se ověřování pomocí symetrického klíče se můžou připojit prostřednictvím brány IoT Edge. X.509 certifikačních autorit a certifikátů X.509 podepsaných svým držitelem nejsou aktuálně podporované. 

## <a name="prepare-a-downstream-device"></a>Příprava příjem dat zařízení

Příjem dat zařízení může být jakékoli aplikaci nebo platforma, která má identitu vytvořené pomocí [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) cloudovou službu. V mnoha případech se tyto aplikace používat [zařízení Azure IoT SDK](../iot-hub/iot-hub-devguide-sdks.md). Pro všechny praktické účely může být podřízený zařízení i aplikace běžící na samotném zařízení brány IoT Edge. 

Připojit zařízení příjem dat na hraničních zařízeních IoT gateway, je třeba dvě věci:

1. Zařízení nebo aplikaci, která je nakonfigurována s řetězcem připojení zařízení IoT Hub s informace pro připojení k bráně. 

    Připojovací řetězec je ve formátu, jako jsou: `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;`. Připojte **GatewayHostName** vlastnost s názvem hostitele na konec připojovací řetězec zařízení brány. Hodnota **GatewayHostName** by měl odpovídat hodnotě **hostname** v souboru config.yaml zařízení brány. 

    Konečný řetězec vypadá jako: `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com`.

2. Zařízení nebo aplikace musí důvěřovat brány **kořenová certifikační Autorita** nebo **vlastníka certifikační Autority** certifikát pro ověření připojení TLS k zařízení brány. 

    Tento krok složitější je podrobně popsána ve zbývající části tohoto článku. Tento krok lze provádět jednu ze dvou způsobů: instalací certifikátu certifikační Autority v úložišti certifikátů operačního systému nebo (pro určité jazyky) odkazující na tento certifikát do aplikací pomocí sady SDK Azure IoT.

## <a name="tls-and-certificate-fundamentals"></a>Základy TLS a certifikátů

Výzvu se bezpečně připojuje podřízené zařízení k IoT Edge je stejně jako jinou klientem a serverem zabezpečené komunikaci, která nastane v Internetu. Klient a server zabezpečeně komunikovat přes internet pomocí [Transport layer security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Protokol TLS je sestavena pomocí standardní [infrastruktury veřejných klíčů (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) konstrukce nazývané certifikáty. Protokol TLS je poměrně používané specifikace a široké škály témat souvisejících s zabezpečení dva koncové body, ale následující části stručně a výstižně popisuje, co je potřeba pro zabezpečené připojení zařízení k brány IoT Edge.

Když se klient připojí k serveru, na serveru představuje řetěz certifikátů, volá se, *řetěz certifikátů serveru*. Řetěz certifikátů obvykle zahrnuje kořenový certifikát certifikační autority (CA), jeden nebo více zprostředkujících certifikátů CA a nakonec vlastní certifikát serveru. Klient vytváří vztah důvěryhodnosti se serverem pomocí kryptograficky ověření řetězu certifikátů celý server. Tento klient ověřování řetězu certifikátů serveru se nazývá *ověřování serveru*. Pokud chcete ověřit řetěz certifikátů serveru, klient potřebuje kopii certifikátu kořenové certifikační Autority, která byla použita k vytvoření (nebo vydat) na server certifikát. Obvykle při připojování k webům, prohlížeč vybavená předem nakonfigurovaným rozhraním běžně používaných certifikátů certifikačních Autorit, klient získá bezproblémový procesu. 

Když se zařízení připojí ke službě Azure IoT Hub, klient je zařízení a cloudové služby IoT Hub je na serveru. Cloudové služby IoT Hub je založená na certifikátu kořenové certifikační Autority volá **Baltimore CyberTrust Root**, což je veřejně dostupné a široce používaným. Protože certifikát služby IoT Hub certifikační Autority je již nainstalován na většině zařízení, mnoho implementací protokolu TLS (OpenSSL, Schannel, LibreSSL) automaticky použije při ověřování certifikátu serveru. Zařízení, která se může úspěšně připojit ke službě IoT Hub může mít problémy při pokusu o připojení k bráně IoT Edge.

Když se zařízení připojí k bráně IoT Edge, klient je podřízený zařízení a zařízení brány je na serveru. Azure IoT Edge umožňuje k vytváření řetězů certifikátů brány, ale jsou vhodná pro operátory (nebo uživatelé). Operátor, který můžete použít veřejný certifikát certifikační Autority, jako je Baltimore, nebo podepsaný svým držitelem (nebo interní) kořenový certifikát certifikační Autority. Veřejné certifikáty certifikační Autority často mají náklady spojené s nimi, proto se obvykle používají v produkčních scénářích. Certifikáty certifikační Autority podepsané svým držitelem jsou upřednostňované pro vývoj a testování. V článcích nastavení transparentní brána uvedených v části požadavky používá certifikáty podepsané svým držitelem kořenové certifikační Autority. 

Pokud používáte certifikát podepsaný svým držitelem kořenové certifikační Autority pro brány IoT Edge, musí být nainstalovaný na nebo k dispozici na všech zařízeních podřízené pokusu o připojení k bráně. 

Další informace o certifikátech pro IoT Edge a některé důsledky produkčního prostředí, najdete v článku [podrobnosti o použití IoT Edge certifikát](iot-edge-certs.md).

## <a name="install-certificates-using-the-os"></a>Nainstalujte certifikáty pomocí operačního systému

Tento článek používá *vlastníka certifikační Autority* odkazovat na certifikát kořenové certifikační Autority, protože to je termín používaný v skripty v článku požadavků brány. 

Instalace certifikátu certifikační Autority vlastníka v úložišti certifikátů operačního systému obecně umožňuje většinu aplikací používat vlastníka certifikát certifikační Autority. Existují některé výjimky, jako jsou aplikace Node.js, která není použití úložiště certifikátů operačního systému, ale místo toho použít úložiště vnitřní certifikát uzel modulu runtime. Pokud nemůžete nainstalovat certifikát na úrovni operačního systému, podívejte se na konkrétní jazyk příklady dále v tomto článku pro použití certifikátu se sadou SDK Azure IoT v aplikacích. 

### <a name="ubuntu"></a>Ubuntu

Následující příkazy jsou příkladem toho, jak nainstalovat certifikát certifikační Autority na hostiteli se systémem Ubuntu. Tento příklad předpokládá, že používáte **azure-iot-test-only.root.ca.cert.pem** certifikát z článcích požadavky a že jste zkopírovali certifikát do umístění na příjem dat zařízení.  

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Zobrazí se zpráva, že "aktualizace certifikáty v /etc/ssl/certs... 1 přidání, odebrání 0; Hotovo."

### <a name="windows"></a>Windows

Následující postup je příklad toho, jak nainstalovat certifikát certifikační Autority na hostitele Windows. Tento příklad předpokládá, že používáte **azure-iot-test-only.root.ca.cert.pem** certifikát z článcích požadavky a že jste zkopírovali certifikát do umístění na příjem dat zařízení.  

1. V nabídce Start, vyhledejte a vyberte **spravovat certifikáty počítače**. Nástroj, který volá **certlm** otevře.
2. Přejděte do **certifikáty - místní počítač** > **důvěryhodné kořenové certifikační autority**.
3. Klikněte pravým tlačítkem na **certifikáty** a vyberte **všechny úkoly** > **Import**. Průvodce importem certifikátu by měl spustit. 
4. Postupujte podle pokynů a importovat soubor certifikátu `<path>/azure-iot-test-only.root.ca.cert.pem`. Po dokončení, měli byste vidět zprávu "Úspěšně naimportoval". 

Můžete také nainstalovat certifikáty programově pomocí rozhraní API pro .NET, jak je znázorněno v ukázce .NET dále v tomto článku. 

Obvykle aplikace používat Windows, pokud volá TLS zásobníku [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) pro zabezpečené připojení přes protokol TLS. Schannel *vyžaduje* nainstalovat všechny certifikáty v úložišti certifikátů Windows před pokusem o připojení protokolu TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Používání certifikátů s sad SDK Azure IoT

Tento článek odkazuje na certifikátu kořenové certifikační Autority, jako *vlastníka certifikační Autority* vzhledem k tomu, který je termín používaný skripty, které vygenerovat certifikát podepsaný svým držitelem v článcích požadavky. 

Tato část popisuje, jak připojit Azure IoT SDK pro zařízení IoT Edge pomocí jednoduchých ukázkových aplikací. Cílem všechny ukázky je připojení klienta zařízení a odesílání telemetrických zpráv k bráně, pak ukončete připojení a ukončení. 

### <a name="common-concepts-across-all-azure-iot-sdks"></a>Běžné koncepty přes všechny sady SDK Azure IoT

Máte připravené před použitím ukázek úrovni aplikace splňovat dvě podmínky:

1. Připojovací řetězec služby IoT Hub podřízené zařízení změnit pro odkazování na zařízení brány.

    Připojovací řetězec je ve formátu, jako jsou: `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;`. Připojte **GatewayHostName** vlastnost s názvem hostitele na konec připojovací řetězec zařízení brány. Hodnota **GatewayHostName** by měl odpovídat hodnotě **hostname** v souboru config.yaml zařízení brány. 

    Konečný řetězec vypadá jako: `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com`.

2. Úplná cesta k certifikátu kořenové certifikační Autority, který jste zkopírovali a někam uložit na příjem dat zařízení.

    Například, `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJS

Tato část poskytuje ukázkové aplikace pro připojení klienta Azure IoT NodeJS zařízení brány IoT Edge. Pro hostitele se systémy Linux a Windows musíte nainstalovat certifikát kořenové certifikační Autority na úrovni aplikace jak je vidět, jak aplikace NodeJS nepoužívejte úložiště certifikátů v systému. 

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

1. Získat ukázky pro **edge_downstream_client** z [zařízení Azure IoT SDK pro Python – ukázky](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples). 
2. Ujistěte se, že máte vše potřebné pro spuštění ukázky kontrolou **readme.md** souboru. 
3. V souboru edge_downstream_client.py aktualizujte **CONNECTION_STRING** a **TRUSTED_ROOT_CA_CERTIFICATE_PATH** proměnné. 
4. Naleznete v dokumentaci sady SDK pro pokyny o tom, jak ukázku spustit ve vašem zařízení. 


## <a name="test-the-gateway-connection"></a>Otestovat připojení brány

Toto je ukázkový příkaz, jaké testy, které všechno, co bylo nastavit správně. Měli byste vidět zprávu "OK ověřit".

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

## <a name="next-steps"></a>Další postup

Zjistěte, jak můžete rozšířit IoT Edge [offline možnosti](offline-capabilities.md) pro příjem dat zařízení. 