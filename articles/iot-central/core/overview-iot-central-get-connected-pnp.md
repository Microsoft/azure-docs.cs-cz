---
title: Připojení zařízení v Azure IoT Central | Microsoft Docs
description: Tento článek představuje klíčové koncepty týkající se připojení zařízení v Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 5948efe35be9f8015a068134d9eec503e6ef5f48
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991999"
---
# <a name="get-connected-to-azure-iot-central-preview-features"></a>Připojit se k Azure IoT Central (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Tento článek představuje klíčové koncepty týkající se připojení zařízení v Microsoft Azure IoT Central.

Azure IoT Central používá ke správě všech registrací a připojení zařízení [službu azure IoT Hub Device Provisioning Service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) .

Použití DPS umožňuje:

- IoT Central podporovat připojování zařízení a jejich škálování.
- Vygenerujete přihlašovací údaje zařízení a nakonfigurujete zařízení offline bez registrace zařízení prostřednictvím uživatelského rozhraní IoT Central.
- Zařízení, která se mají připojit pomocí sdílených přístupových podpisů (SAS)
- Zařízení, která se mají připojit pomocí standardních certifikátů X. 509
- K registraci zařízení v IoT Central můžete použít vlastní ID zařízení. Použití vlastních ID zařízení zjednodušuje integraci se stávajícími systémy Back-Office.
- Jediný konzistentní způsob připojení zařízení k IoT Central.

Tento článek popisuje následující případy použití:

- [Rychlé připojení jednoho zařízení pomocí SAS](#connect-a-single-device)
- [Připojení zařízení ve velkém měřítku pomocí SAS](#connect-devices-at-scale-using-sas)
- [Připojte zařízení ve velkém měřítku pomocí certifikátů X. 509](#connect-devices-using-x509-certificates) , což je doporučený postup pro produkční prostředí.
- [Připojit bez prvotní registrace zařízení](#connect-without-registering-devices)
- [Připojení zařízení pomocí funkcí technologie Plug and Play IoT](#connect-devices-with-iot-plug-and-play)

## <a name="connect-a-single-device"></a>Připojení jednoho zařízení

Tento přístup je užitečný při experimentování s IoT Central nebo testováním zařízení. K připojení zařízení k aplikaci IoT Central pomocí služby Device Provisioning (DPS) můžete použít informace o připojení zařízení z aplikace IoT Central. Ukázkový kód klienta zařízení DPS najdete v následujících jazycích:

- [C\#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device)
- [Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/tree/master/provisioning/Samples/device)

## <a name="connect-devices-at-scale-using-sas"></a>Připojení zařízení ve velkém měřítku pomocí SAS

Pokud chcete zařízení připojit k IoT Central škálování pomocí SAS, musíte se zaregistrovat a pak nastavit zařízení:

### <a name="register-devices-in-bulk"></a>Hromadně registrovat zařízení

Pokud chcete v aplikaci IoT Central zaregistrovat velký počet zařízení, použijte k [importu ID zařízení a názvů zařízení](howto-manage-devices.md#import-devices)soubor CSV.

Chcete-li načíst informace o připojení pro importovaná zařízení, [exportujte soubor CSV z aplikace IoT Central](howto-manage-devices.md#export-devices).

> [!NOTE]
> Pokud se chcete dozvědět, jak můžete zařízení připojit, aniž byste je nejdřív zaregistrovali v IoT Central, přečtěte si téma [připojit bez první registrace zařízení](#connect-without-registering-devices).

### <a name="set-up-your-devices"></a>Nastavení zařízení

Pomocí informací o připojení ze souboru exportu v kódu zařízení můžete zařízením umožnit připojení a posílání dat do aplikace IoT Central. Další informace o připojení zařízení najdete v části [Další kroky](#next-steps).

## <a name="connect-devices-using-x509-certificates"></a>Připojení zařízení pomocí certifikátů X. 509

V produkčním prostředí je použití certifikátů X. 509 doporučeným mechanismem ověřování zařízení pro IoT Central. Další informace najdete v tématu [ověřování zařízení pomocí certifikátů certifikační autority X. 509](../../iot-hub/iot-hub-x509ca-overview.md).

Následující kroky popisují, jak připojit zařízení k IoT Central pomocí certifikátů X. 509:

1. V aplikaci IoT Central _přidejte a ověřte zprostředkující nebo kořenový certifikát X. 509_ , který používáte k vygenerování certifikátů zařízení:

    - Přejděte do části **správa > připojení zařízení > certifikáty (X. 509)** a přidejte kořenový certifikát X. 509 nebo zprostředkující certifikát, který používáte k vygenerování certifikátů na listovém zařízení.

      ![Nastavení připojení](media/overview-iot-central-get-connected-pnp/connection-settings.png)

      Pokud dojde k narušení zabezpečení nebo pokud je váš primární certifikát nastavený na vypršení platnosti, použijte sekundární certifikát k omezení výpadků. Během aktualizace primárního certifikátu můžete nadále zřizovat zařízení pomocí sekundárního certifikátu.

    - Ověření vlastnictví certifikátu zajišťuje, že odeslání certifikátu pro odeslání certifikátu má privátní klíč certifikátu. Ověření certifikátu:
        - Vyberte tlačítko vedle **ověřovacího kódu** pro vygenerování kódu.
        - Vytvořte ověřovací certifikát X. 509 s ověřovacím kódem, který jste vygenerovali v předchozím kroku. Uložte certifikát jako soubor. cer.
        - Nahrajte podepsaný ověřovací certifikát a vyberte **ověřit**.

          ![Nastavení připojení](media/overview-iot-central-get-connected-pnp/verify-cert.png)

1. K _importu a registraci zařízení_ v aplikaci IoT Central použijte soubor CSV.

1. _Nastavení zařízení_ Vygenerujte listové certifikáty pomocí nahraného kořenového certifikátu. Použijte **ID zařízení** jako hodnotu CNAME v listových certifikátech. ID zařízení by mělo obsahovat malá písmena. Pak zařízení naprogramujte pomocí informací o službě zřizování. Při prvním zapnutí zařízení se načte informace o připojení pro vaši aplikaci IoT Central z DPS.

### <a name="further-reference"></a>Další reference

- Ukázková implementace pro [RaspberryPI.](https://aka.ms/iotcentral-docs-Raspi-releases)

- [Ukázkový klient zařízení v jazyce C.](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>Pouze pro účely testování

Pro účely testování můžete použít tyto nástroje k vygenerování certifikátů certifikační autority a certifikátů zařízení.

- Pokud používáte zařízení DevKit, tento [Nástroj příkazového řádku](https://aka.ms/iotcentral-docs-dicetool) vygeneruje certifikát certifikační autority, který můžete přidat do vaší aplikace IoT Central pro ověření certifikátů.

- Tento [Nástroj příkazového řádku](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) použijte k těmto akcím:
  - Vytvořte řetěz certifikátů. Postupujte podle kroku 2 v článku na GitHubu.
  - Uložte certifikáty jako soubory. cer a nahrajte je do vaší aplikace IoT Central.
  - K vygenerování ověřovacího certifikátu použijte ověřovací kód z aplikace IoT Central. Postupujte podle kroků 3 v článku na GitHubu.
  - Vytvořte listové certifikáty pro vaše zařízení pomocí ID zařízení jako parametru nástroje. Postupujte podle kroku 4 v článku na GitHubu.

## <a name="connect-without-registering-devices"></a>Připojit bez registrace zařízení

Klíčovým scénářem, který IoT Central umožňuje, je, aby výrobci OEM mohli hromadně vyrábět zařízení, která se můžou připojit k aplikaci IoT Central bez prvotní registrace. Výrobce musí vygenerovat vhodné přihlašovací údaje a nakonfigurovat zařízení v továrně. Při prvním zapnutí zařízení se automaticky připojí k IoT Central aplikaci. Operátor IoT Central musí zařízení schválit, aby mohl obstatit odeslání dat.

Tento tok popisuje následující diagram:

![Nastavení připojení](media/overview-iot-central-get-connected-pnp/device-connection-flow1.png)

Následující kroky popisují tento postup podrobněji. Postup se mírně liší v závislosti na tom, zda používáte certifikáty SAS nebo X. 509 pro ověřování zařízení:

1. Konfigurace nastavení připojení:

    - **Certifikáty X. 509:** [přidejte a ověřte kořenový a zprostředkující certifikát](#connect-devices-using-x509-certificates) a použijte ho k vygenerování certifikátů zařízení v následujícím kroku.
    - **SAS:** Zkopírujte primární klíč. Tento klíč je klíčem SAS skupiny pro aplikaci IoT Central. Pomocí klíče vygenerujte klíče SAS zařízení v následujícím kroku.
    ![nastavení připojení SAS](media/overview-iot-central-get-connected-pnp/connection-settings-sas.png)

1. Generování přihlašovacích údajů pro zařízení
    - **Certifikáty X. 509:** Vygenerujte list – certifikáty pro vaše zařízení pomocí kořenového nebo zprostředkujícího certifikátu, který jste přidali do aplikace IoT Central. Ujistěte se, že používáte **ID zařízení** s malým písmenem jako CNAME v listových certifikátech. Pro účely testování použijte tento [Nástroj příkazového řádku](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) k vygenerování certifikátů zařízení.
    - **SAS:** Pomocí tohoto [nástroje příkazového řádku](https://www.npmjs.com/package/dps-keygen) můžete vygenerovat klíče SAS zařízení. Použijte **primární klíč** skupiny z předchozího kroku. ID zařízení musí být malými písmeny.

      Chcete-li nainstalovat [Nástroj Generátor klíčů](https://github.com/Azure/dps-keygen), spusťte následující příkaz:

      ```cmd/sh
      npm i -g dps-keygen
      ```

      Pokud chcete vygenerovat klíč zařízení z primárního klíče skupiny SAS, spusťte následující příkaz:

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Pokud chcete zařízení nastavit, zablikají všechna zařízení s **ID oboru**, **ID zařízení**a **certifikátem zařízení X. 509** nebo **klíčem SAS**.

1. Pak zařízení zapněte, aby se připojovalo k vaší IoT Central aplikaci. Když přepnete zařízení do zařízení, nejprve se připojí k DPS, aby se načetly informace o registraci IoT Central.

1. Připojené zařízení se zpočátku zobrazuje jako **nepřidružená** na stránce **zařízení** . Stav zřizování zařízení je **zaregistrovaný**. **Migrujte** zařízení do příslušné šablony zařízení a schvalte zařízení pro připojení k vaší IoT Central aplikaci. Zařízení pak může načíst připojovací řetězec z IoT Hub a začít odesílat data. Zřizování zařízení je teď hotové a stav zřizování se teď **zřídí**.

## <a name="individual-enrollment-based-device-connectivity"></a>Jednotlivá připojení zařízení založená na registraci

Pro zákazníky, kteří připojují zařízení s ověřovacími přihlašovacími údaji určenými pro jednotlivá zařízení, je možnost. Jednotlivá registrace je záznam pro jedno zařízení, které se může připojit. Jednotlivé registrace můžou jako mechanismus ověřování použít buď certifikáty na list X. 509, nebo tokeny SAS (od fyzického nebo virtuálního čipu TPM). ID zařízení (neboli registrační ID) v individuální registraci je alfanumerické a malé písmeno a může obsahovat spojovníky. Další informace o jednotlivých registracich najdete [tady](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment).

> [!NOTE]
> Když vytvoříte jednotlivou registraci pro zařízení, má přednost před výchozí skupinou ověření identity (SAS, x509) ve vaší aplikaci.

### <a name="creating-individual-enrollments"></a>Vytváření jednotlivých registrací
IoT Central podporuje následující mechanismy ověřování identity

1. **Ověření identity symetrického klíče:** Symetrický ověření identity je jednoduchý přístup k ověřování zařízení pomocí instance služby Device Provisioning. Chcete-li vytvořit jednotlivou registraci pomocí symetrických klíčů; Otevřete dialogové okno připojit, vyberte jednotlivou registraci a mechanismus SAS a zadejte primární a sekundární klíč. Klíče SAS musí být kódované v kódování Base64. Tady je [odkaz](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) na ukázky kódu, které vám pomůžou s psaním kódu zařízení za účelem zřízení zařízení pomocí symetrických klíčů a jednotlivých registrací.
1. **Certifikáty X. 509:** Certifikáty X. 509, jako je navržený název, jsou mechanizmus ověřování na základě certifikátu, což je skvělý způsob, jak škálovat produkci. Pokud chcete vytvořit jednotlivou registraci pomocí symetrických klíčů, vyberte jednotlivou registraci a mechanismus "X. 509" a nahrajte primární a sekundární certifikát a uložte si registraci. Tady je [odkaz](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) na ukázky kódu, které vám pomůžou s psaním kódu zařízení za účelem zřízení zařízení pomocí x509. Certifikáty zařízení používané s [jednotlivou](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment) položkou registrace mají požadavek na to, aby byl název subjektu nastavený na ID zařízení (neboli ID registrace) položky individuální registrace.
1. **Ověření identity čipem TPM:** ČIP TPM představuje modul Trusted Platform Module a je to typ modulu hardwarového zabezpečení (HSM) a je jedním z nejbezpečnější způsobů, jak připojit vaše zařízení.  V tomto článku se předpokládá, že používáte diskrétní, firmware nebo integrovaný čip TPM. Software emulující čipy TPM je vhodný pro vytváření prototypů nebo testování, ale neposkytuje stejnou úroveň zabezpečení jako diskrétní, firmware nebo integrované čipy TPM. Nedoporučujeme používat software čipy TPM v produkčním prostředí. Pokud chcete vytvořit jednotlivou registraci pomocí symetrických klíčů, vyberte jednotlivou registraci a mechanismus TPM a zadáním ověřovacích klíčů vytvořte registraci. Další informace o typech čipy TPM najdete zde. Další informace o ověření identity TPM najdete [tady](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation). Tady je [odkaz](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/TpmSample) na ukázky kódu, které vám pomůžou s psaním kódu zařízení za účelem zřízení zařízení pomocí TPM. Pokud chcete vytvořit ověření na základě čipu TPM, zadejte ověřovací klíč a uložte ho.

## <a name="connect-devices-with-iot-plug-and-play"></a>Připojení zařízení pomocí technologie Plug and Play IoT

Jednou z klíčových funkcí IoT technologie Plug and Play with IoT Central je možnost přidružit šablony zařízení automaticky k připojení zařízení. Společně s přihlašovacími údaji pro zařízení teď můžou zařízení odeslat **CapabilityModelId** jako součást volání registrace zařízení a IoT Central budou vyhledávat a přidružit šablonu zařízení. Proces zjišťování sleduje následující pořadí:

1. Přidruží se k šabloně zařízení, pokud je již publikována v aplikaci IoT Central.
1. Načte se z veřejného úložiště publikovaných a certifikovaných modelů schopností.

Níže je formát dodatečné datové části, kterou zařízení poslalo během volání registrace DPS.

```javascript
'__iot:interfaces': {
              CapabilityModelId: <this is the URN for the capability model>
          }
```

> [!NOTE]
> Mějte na paměti, že možnost automatického schvalování by měla být povolená, aby se zařízení mohla automaticky připojovat, zjistit model a začít odesílat data.

## <a name="device-status"></a>Stav zařízení

Když se reálné zařízení připojí k vaší aplikaci IoT Central, změní se stav zařízení takto:

1. Stav zařízení se **zaregistruje**jako první. Tento stav znamená, že se zařízení vytvoří v IoT Central a má ID zařízení. Zařízení je zaregistrováno v těchto případech:
    - Na stránce **zařízení** se přidá nové reálné zařízení.
    - Na stránce **zařízení** se přidá sada zařízení pomocí **importu** .

1. Stav zařízení se změní na **zřízený** , když se zařízení, které je připojené k vaší aplikaci IoT Central s platnými přihlašovacími údaji, dokončí krok zřizování. V tomto kroku zařízení načte připojovací řetězec z IoT Hub. Zařízení se teď může připojit k IoT Hub a začít odesílat data.

1. Operátor může blokovat zařízení. Když je zařízení blokované, nemůže odesílat data do vaší aplikace IoT Central. Blokovaná zařízení mají stav **blokováno**. Předtím, než bude moct pokračovat v odesílání dat, musí operátor resetovat zařízení. Když operátor odblokuje zařízení, stav se vrátí do předchozí hodnoty, **zaregistrováno** nebo **zřízeno**.

1. Stav zařízení čeká na **schválení** , což znamená, že je možnost **automatického schvalování** zakázaná a vyžaduje, aby se všechna zařízení, která se připojují k IoT Central, explicitně schválila operátorem. Zařízení, která nejsou zaregistrovaná ručně na stránce **zařízení** , ale jsou připojená s platnými přihlašovacími údaji, budou mít stav zařízení **čekající na schválení**. Operátoři můžou tato zařízení schválit ze stránky **zařízení** pomocí tlačítka **schválit** .

1. Stav zařízení není **k** dispozici, což znamená, že zařízení, která se připojují k IoT Central, nemají k nim přidruženou šablonu zařízení. K tomu obvykle dochází v následujících situacích:
    - Sada zařízení se přidá na stránce **zařízení** pomocí **importu** bez zadání šablony zařízení.
    - Zařízení, která nejsou zaregistrovaná ručně na stránce **zařízení** připojená s platnými přihlašovacími údaji, ale bez zadání ID šablony během registrace.  
Operátor může přidružit zařízení k šabloně ze stránky **zařízení** pomocí tlačítka **migrovat** .

## <a name="sdk-support"></a>Podpora sady SDK

Sady SDK pro zařízení Azure nabízí nejjednodušší způsob implementace kódu zařízení. K dispozici jsou následující sady SDK pro zařízení:

- [Sada Azure IoT SDK pro C](https://github.com/azure/azure-iot-sdk-c)
- [Sada Azure IoT SDK pro Python](https://github.com/azure/azure-iot-sdk-python)
- [Sada Azure IoT SDK pro Node. js](https://github.com/azure/azure-iot-sdk-node)
- [Sada Azure IoT SDK pro jazyk Java](https://github.com/azure/azure-iot-sdk-java)
- [Sada Azure IoT SDK pro .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Funkce sady SDK a připojení IoT Hub

Všechna komunikace zařízení s IoT Hub používá následující možnosti připojení IoT Hub:

- [Zasílání zpráv ze zařízení do cloudu](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Vlákna zařízení](../../iot-hub/iot-hub-devguide-device-twins.md)

Následující tabulka shrnuje, jak se funkce IoT Central zařízení Azure namapují k funkcím IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Měření: telemetrie | Zasílání zpráv ze zařízení do cloudu |
| Vlastnosti zařízení | Nedokončené hlášené vlastnosti zařízení |
| Nastavení | Požadované a hlášené vlastnosti vlákna zařízení |

Další informace o používání sad SDK pro zařízení najdete v tématu [připojení zařízení DevDiv Kit k vaší aplikaci Azure IoT Central](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) , například Code.

### <a name="protocols"></a>Protokoly

Sady SDK pro zařízení podporují následující síťové protokoly pro připojení ke službě IoT Hub:

- MQTT
- AMQP
- HTTPS

Informace o těchto rozdílových protokolech a doprovodnéch materiálech k výběru jednoho najdete v tématu [Volba komunikačního protokolu](../../iot-hub/iot-hub-devguide-protocols.md).

Pokud zařízení nemůže používat žádné z podporovaných protokolů, můžete k převodu protokolu použít Azure IoT Edge. IoT Edge podporuje další postupy na základě inteligentních funkcí, které umožňují přesměrování zpracování na hranici z aplikace Azure IoT Central.

## <a name="security"></a>Zabezpečení

Všechna data vyměňované mezi zařízeními a vaším IoT Central Azure jsou šifrovaná. IoT Hub ověří všechny požadavky ze zařízení, které se připojuje k jakémukoli koncovému bodu IoT Hub pro zařízení. Aby se zabránilo výměně přihlašovacích údajů po kabelech, zařízení používá k ověření podepsané tokeny. Další informace najdete v tématu [řízení přístupu k IoT Hub](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili o připojení zařízení v Azure IoT Central, tady jsou doporučené další kroky:

- [Příprava a připojení zařízení DevKit](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- [C SDK: zřízení klientské sady SDK pro zařízení](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
