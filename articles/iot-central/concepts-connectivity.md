---
title: Připojení zařízení v Azure IoT Central | Microsoft Docs
description: Tento článek představuje klíčové koncepty týkající se připojení zařízení v Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: bb7711eea927212042ed2299bae74130867c1692
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067644"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Připojení zařízení v Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Tento článek představuje klíčové koncepty týkající se připojení zařízení v Microsoft Azure IoT Central.

Azure IoT Central používá ke správě všech registrací a připojení zařízení [službu azure IoT Hub Device Provisioning Service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) .

Použití DPS umožňuje:

- IoT Central podporovat připojování zařízení a jejich škálování.
- Vygenerujete přihlašovací údaje zařízení a nakonfigurujete zařízení offline bez registrace zařízení prostřednictvím uživatelského rozhraní IoT Central.
- Zařízení, která se mají připojit pomocí sdílených přístupových podpisů (SAS)
- Zařízení, která se mají připojit pomocí standardních certifikátů X. 509
- K registraci zařízení v IoT Central můžete použít vlastní ID zařízení. Použití vlastních ID zařízení zjednodušuje integraci se stávajícími systémy Back-Office.
- Jediný konzistentní způsob připojení zařízení k IoT Central.

Tento článek popisuje následující čtyři případy použití:

- [Rychlé připojení jednoho zařízení pomocí SAS](#connect-a-single-device)
- [Připojení zařízení ve velkém měřítku pomocí SAS](#connect-devices-at-scale-using-sas)
- [Připojte zařízení ve velkém měřítku pomocí certifikátů X. 509](#connect-devices-using-x509-certificates) , což je doporučený postup pro produkční prostředí.
- [Připojit bez prvotní registrace zařízení](#connect-without-registering-devices)

## <a name="connect-a-single-device"></a>Připojení jednoho zařízení

Tento přístup je užitečný při experimentování s IoT Central nebo testováním zařízení. K připojení zařízení k aplikaci IoT Central pomocí služby Device Provisioning (DPS) můžete použít informace o připojení zařízení z aplikace IoT Central. Ukázkový kód klienta zařízení DPS najdete v následujících jazycích:

- [C\#](./howto-connect-raspberry-pi-csharp.md)
- [Node.js](./howto-connect-nodejs.md)

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

V produkčním prostředí je použití certifikátů X. 509 doporučeným mechanismem ověřování zařízení pro IoT Central. Další informace najdete v tématu [ověřování zařízení pomocí certifikátů certifikační autority X. 509](../iot-hub/iot-hub-x509ca-overview.md).

Následující kroky popisují, jak připojit zařízení k IoT Central pomocí certifikátů X. 509:

1. V aplikaci IoT Central _přidejte a ověřte zprostředkující nebo kořenový certifikát X. 509_ , který používáte k vygenerování certifikátů zařízení:

    - Přejděte do části **správa > připojení zařízení > certifikáty (X. 509)** a přidejte kořenový certifikát X. 509 nebo zprostředkující certifikát, který používáte k vygenerování certifikátů na listovém zařízení.

      ![Nastavení připojení](media/concepts-connectivity/connection-settings.png)

      Pokud dojde k narušení zabezpečení nebo pokud je váš primární certifikát nastavený na vypršení platnosti, použijte sekundární certifikát k omezení výpadků. Během aktualizace primárního certifikátu můžete nadále zřizovat zařízení pomocí sekundárního certifikátu.

    - Ověření vlastnictví certifikátu zajišťuje, že odeslání certifikátu pro odeslání certifikátu má privátní klíč certifikátu. Ověření certifikátu:
        - Vyberte tlačítko vedle ověřovacího **kódu** pro vygenerování kódu.
        - Vytvořte ověřovací certifikát X. 509 s ověřovacím kódem, který jste vygenerovali v předchozím kroku. Uložte certifikát jako soubor. cer.
        - Nahrajte podepsaný ověřovací certifikát a vyberte **ověřit**.

          ![Nastavení připojení](media/concepts-connectivity/verify-cert.png)

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

![Nastavení připojení](media/concepts-connectivity/device-connection-flow1.png)

Následující kroky popisují tento postup podrobněji. Postup se mírně liší v závislosti na tom, zda používáte certifikáty SAS nebo X. 509 pro ověřování zařízení:

1. Konfigurace nastavení připojení:

    - **Certifikáty X. 509:** [Přidejte a ověřte kořenový a zprostředkující certifikát](#connect-devices-using-x509-certificates) a použijte ho k vygenerování certifikátů zařízení v následujícím kroku.
    - **VEDE** Zkopírujte primární klíč. Tento klíč je klíčem SAS skupiny pro aplikaci IoT Central. Pomocí klíče vygenerujte klíče SAS zařízení v následujícím kroku.
    ![Nastavení připojení SAS](media/concepts-connectivity/connection-settings-sas.png)

1. Generování přihlašovacích údajů pro zařízení
    - **Certifikáty X. 509:** Vygenerujte list – certifikáty pro vaše zařízení pomocí kořenového nebo zprostředkujícího certifikátu, který jste přidali do aplikace IoT Central. Ujistěte se, že používáte **ID zařízení** s malým písmenem jako CNAME v listových certifikátech. Pro účely testování použijte tento [Nástroj příkazového řádku](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) k vygenerování certifikátů zařízení.
    - **VEDE** Pomocí tohoto [nástroje příkazového řádku](https://www.npmjs.com/package/dps-keygen) můžete vygenerovat klíče SAS zařízení. Použijte **primární klíč** skupiny z předchozího kroku. ID zařízení musí být malými písmeny.

      Chcete-li nainstalovat [Nástroj Generátor klíčů](https://github.com/Azure/dps-keygen), spusťte následující příkaz:

      ```cmd/sh
      npm i -g dps-keygen
      ```

      Pokud chcete vygenerovat klíč zařízení z primárního klíče skupiny SAS, spusťte následující příkaz:

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Pokud chcete zařízení nastavit, zablikají všechna zařízení s **ID oboru**, **ID zařízení**a certifikátem **zařízení X. 509** nebo **klíčem SAS**.

1. Pak zařízení zapněte, aby se připojovalo k vaší IoT Central aplikaci. Když přepnete zařízení do zařízení, nejprve se připojí k DPS, aby se načetly informace o registraci IoT Central.

1. Připojené zařízení se zpočátku zobrazuje na stránce **Device Explorer** jako **nepřidružené zařízení** . Stav zřizování zařízení je zaregistrovaný. **Přidružte** zařízení k příslušné šabloně zařízení a schvalte zařízení pro připojení k vaší aplikaci IoT Central. Zařízení pak může načíst připojovací řetězec z IoT Hub a začít odesílat data. Zřizování zařízení je teď hotové a stav zřizování se teď **zřídí**.

## <a name="provisioning-status"></a>Stav zřizování

Když se reálné zařízení připojí k vaší IoT Central aplikaci, jeho stav zřizování se změní takto:

1. Stav zřizování zařízení je nejprve **zaregistrován**. Tento stav znamená, že se zařízení vytvoří v IoT Central a má ID zařízení. Zařízení je zaregistrováno v těchto případech:
    - Na stránce **Device Explorer** se přidá nové reálné zařízení.
    - Pomocí importování na stránce **Device Explorer** se přidá sada zařízení.
    - Zařízení se na stránce **Device Explorer** nezaregistrovalo ručně, ale je propojené s platnými přihlašovacími údaji a na stránce **Device Explorer** je viditelné jako nepřidružené zařízení.

1. Stav zřizování zařízení se změní na **zřízený** , když se zařízení, které je připojené k vaší aplikaci IoT Central s platnými přihlašovacími údaji, dokončí krok zřizování. V tomto kroku zařízení načte připojovací řetězec z IoT Hub. Zařízení se teď může připojit k IoT Hub a začít odesílat data.

1. Operátor může blokovat zařízení. Když je zařízení blokované, nemůže odesílat data do vaší aplikace IoT Central. Blokovaná zařízení mají stav zřizování blokováno. Předtím, než bude moct pokračovat v odesílání dat, musí operátor resetovat zařízení. Když operátor odblokuje zařízení, stav zřizování se vrátí k předchozí hodnotě, zaregistrováno nebo **zřízeno**.

## <a name="sdk-support"></a>Podpora sady SDK

Sady SDK pro zařízení Azure nabízí nejjednodušší způsob implementace kódu zařízení. K dispozici jsou následující sady SDK pro zařízení:

- [Sada Azure IoT SDK pro C](https://github.com/azure/azure-iot-sdk-c)
- [Sada Azure IoT SDK pro Python](https://github.com/azure/azure-iot-sdk-python)
- [Sada Azure IoT SDK pro Node. js](https://github.com/azure/azure-iot-sdk-node)
- [Sada Azure IoT SDK pro jazyk Java](https://github.com/azure/azure-iot-sdk-java)
- [Sada Azure IoT SDK pro .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Funkce sady SDK a připojení IoT Hub

Všechna komunikace zařízení s IoT Hub používá následující možnosti připojení IoT Hub:

- [Zasílání zpráv ze zařízení do cloudu](../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Vlákna zařízení](../iot-hub/iot-hub-devguide-device-twins.md)

Následující tabulka shrnuje, jak se funkce IoT Central zařízení Azure namapují k funkcím IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Číselnou Telemetrie | Zasílání zpráv ze zařízení do cloudu |
| Vlastnosti zařízení | Nedokončené hlášené vlastnosti zařízení |
| Nastavení | Požadované a hlášené vlastnosti vlákna zařízení |

Další informace o používání sad SDK pro zařízení najdete v jednom z následujících článků příklad kódu:

- [Připojení obecného klienta Node.js k aplikaci Azure IoT Central](howto-connect-nodejs.md)
- [Připojení zařízení malinu pi k aplikaci Azure IoT Central](howto-connect-raspberry-pi-python.md)
- [Připojte zařízení DevDiv Kit ke svojí aplikaci Azure IoT Central](howto-connect-devkit.md).

### <a name="protocols"></a>Protokoly

Sady SDK pro zařízení podporují následující síťové protokoly pro připojení ke službě IoT Hub:

- MQTT
- AMQP
- HTTPS

Informace o těchto rozdílových protokolech a doprovodnéch materiálech k výběru jednoho najdete v tématu [Volba komunikačního protokolu](../iot-hub/iot-hub-devguide-protocols.md).

Pokud zařízení nemůže používat žádné z podporovaných protokolů, můžete k převodu protokolu použít Azure IoT Edge. IoT Edge podporuje další postupy na základě inteligentních funkcí, které umožňují přesměrování zpracování na hranici z aplikace Azure IoT Central.

## <a name="security"></a>Zabezpečení

Všechna data vyměňované mezi zařízeními a vaším IoT Central Azure jsou šifrovaná. IoT Hub ověří všechny požadavky ze zařízení, které se připojuje k jakémukoli koncovému bodu IoT Hub pro zařízení. Aby se zabránilo výměně přihlašovacích údajů po kabelech, zařízení používá k ověření podepsané tokeny. Další informace najdete v tématu [řízení přístupu k IoT Hub](../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili o připojení zařízení v Azure IoT Central, tady jsou doporučené další kroky:

- [Příprava a připojení zařízení DevKit](howto-connect-devkit.md)
- [Příprava a připojení Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Připojení obecného klienta Node.js k aplikaci Azure IoT Central](howto-connect-nodejs.md)
- [SADA C SDK: Zřizování klientské sady SDK pro zařízení](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
