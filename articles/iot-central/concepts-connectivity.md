---
title: Připojení zařízení v Azure IoT Central | Dokumentace Microsoftu
description: Tento článek představuje klíčové koncepty týkající se připojení zařízení v Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: f11d1c6b727799f2cc67c6f5012dc4d117556d2e
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467815"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Připojení zařízení v Azure IoT Central

Tento článek představuje klíčové koncepty týkající se připojení zařízení v Microsoft Azure IoT Central.

Používá Azure IoT Central [Azure IoT Hub Device Provisioning service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) spravovat všechny registrace zařízení a připojení.

Použití distribučních bodů umožňuje:

- IoT Central pro podporu registrace a připojování zařízení ve velkém měřítku.
- Vygenerování zařízení přihlašovací údaje a nakonfigurujte zařízení offline bez registrace zařízení pomocí IoT Central uživatelského rozhraní.
- Zařízení a připojte se pomocí sdílených přístupových podpisů (SAS).
- Zařízení a připojte se pomocí standardních certifikátů X.509.
- Abyste je mohli používat vlastní ID zařízení k registraci zařízení v IoT Central. Integrace s existujícími systémy administrativní použití vlastní ID zařízení zjednodušuje.
- Jednoduchý a konzistentní způsob připojení zařízení k IoT Central.

Tento článek popisuje následující čtyři svědectví:

1. [Spojte se rychle jedno zařízení, pomocí SAS](#connect-a-single-device)
1. [Připojení zařízení ve velkém měřítku pomocí SAS](#connect-devices-at-scale-using-sas)
1. [Připojení zařízení ve velkém měřítku pomocí certifikátů X.509](#connect-devices-using-x509-certificates) Toto je doporučený postup pro produkční prostředí.
1. [Připojit bez první registrace zařízení](#connect-without-registering-devices)

## <a name="connect-a-single-device"></a>Připojení jedno zařízení

Tento přístup je užitečný při experimentování s IoT Central nebo testování zařízení. Informace o připojení zařízení z aplikace IoT Central můžete použít k vygenerování připojovacího řetězce pro zařízení. Podrobné pokyny najdete v článku [jak vygenerovat připojovací řetězec zařízení pro připojení k aplikaci Azure IoT Central](howto-generate-connection-string.md).

## <a name="connect-devices-at-scale-using-sas"></a>Připojení zařízení ve velkém měřítku pomocí SAS

Připojení zařízení k IoT Central ve velkém měřítku pomocí SAS, budete muset zaregistrovat a pak nastavit zařízení:

### <a name="register-devices-in-bulk"></a>Hromadné registrace zařízení

Zaregistrovat velký počet zařízení s vaší aplikací IoT Central, použijte soubor CSV a [importovat ID zařízení a názvy zařízení](howto-manage-devices.md#import-devices).

K načtení informací o připojení pro importovaná zařízení [vyexportujete soubor CSV z vaší aplikace IoT Central](howto-manage-devices.md#export-devices).

> [!NOTE]
> Informace o tom, jak připojit zařízení i bez jejich první registrace v IoT Central, naleznete v tématu [připojit bez registrace zařízení první](#connect-without-registering-devices).

### <a name="set-up-your-devices"></a>Nastavit vaše zařízení

Pomocí informací o připojení ze souboru exportu ve vašem kódu zařízení umožní vašim zařízením připojit a odesílat data IoT a aplikace IoT Central. Další informace o připojení zařízení najdete v tématu [další kroky](#next-steps).

## <a name="connect-devices-using-x509-certificates"></a>Připojení zařízení pomocí certifikátů X.509

V produkčním prostředí pomocí certifikátů X.509 je ověřovací mechanismus doporučené zařízení IoT Central. Další informace najdete v tématu [zařízení ověřování pomocí certifikátů X.509 certifikační Autority](../iot-hub/iot-hub-x509ca-overview.md).

Následující kroky popisují, jak připojit zařízení k IoT Central pomocí certifikátů X.509:

1. V aplikaci IoT Central _přidat a ověřit dočasná nebo kořenový certifikát X.509_ používáte k vytvoření certifikátů zařízení:

    - Přejděte do **správy > připojení zařízení > Certifikáty (X.509)** a přidejte X.509 kořenový nebo zprostředkující certifikát, které používáte k vytvoření certifikátů zařízení typu list.

      ![Nastavení připojení](media/concepts-connectivity/connection-settings.png)

      Pokud v případě porušení zabezpečení nebo nastavení primárního certifikátu vyprší, použijte k omezení prostojů sekundární certifikát. Můžete pokračovat ke zřízení zařízení pomocí sekundární certifikát aktualizovat primární certifikát.

    - Ověření vlastnictví certifikátu se zajistí, že odeslání certifikát nemá privátní klíč certifikátu. Chcete-li ověřit certifikát:
        - Vyberte tlačítko vedle **ověřovací kód** generovat kód.
        - Vytvořte certifikát pro ověření X.509 s ověřovacím kódem, který jste vygenerovali v předchozím kroku. Uložte si certifikát jako soubor .cer.
        - Nahrát podepsaný ověřovací certifikát a vyberte **ověřte**.

          ![Nastavení připojení](media/concepts-connectivity/verify-cert.png)

1. Soubor CSV a použít _importovat a registraci zařízení_ aplikace IoT Central.

1. _Nastavte vaše zařízení._ Generovat listové certifikáty pomocí nahrané kořenový certifikát. Použití **ID zařízení** jako hodnota CNAME v listové certifikáty. ID zařízení by měl obsahovat jenom malá písmena. Pak naprogramovat svá zařízení pomocí zřizovací služby. Když je zařízení zapnuté pro prvních, načte její informace o připojení pro vaše aplikace IoT Central z distribučních bodů.

### <a name="further-reference"></a>Odkaz na další

- Ukázková implementace pro [raspberrypi nebo.](https://aka.ms/iotcentral-docs-Raspi-releases)

- [Ukázky klienta zařízení v jazyce C.](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>Pouze pro testovací účely

Pouze pro testování, můžete použít tyto nástroje pro generování certifikátů certifikačních Autorit a certifikátů zařízení.

- Pokud používáte zařízení s DevKit to [nástroj příkazového řádku](https://aka.ms/iotcentral-docs-dicetool) vygeneruje certifikát certifikační Autority, můžete přidat do vaší aplikace IoT Central k ověření certifikátů.

- Použijte tento [nástroj příkazového řádku](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) na:
  - Vytvořte řetěz certifikátů. Postupujte podle kroku 2 v článku Githubu věnovaném.
  - Certifikáty uložte jako soubory .cer k nahrání do aplikace IoT Central.
  - Použijte ověřovací kód z aplikace IoT Central generovat ověřovací certifikát. Postupujte podle kroku 3 v článku Githubu věnovaném.
  - Vytvoření certifikátů typu list pro zařízení s použitím ID vašeho zařízení jako parametr do nástroje. Postupujte podle kroku 4 v článku Githubu věnovaném.

## <a name="connect-without-registering-devices"></a>Připojit i bez registrace zařízení

Klíčovým scénářem, IoT Central umožňuje, je pro výrobce OEM pro výrobu velké množství zařízení, která se může připojit k aplikaci IoT Central, aniž byste nejdřív registrována. Výrobce, musíte vygenerovat vhodný přihlašovací údaje a konfigurace zařízení v objektu pro vytváření. Když zařízení zapne poprvé, připojí se automaticky do aplikace IoT Central. IoT Central operátor musí schválit zařízení před svým stat – odesílající data.

Následující diagram popisuje tento tok:

![Nastavení připojení](media/concepts-connectivity/device-connection-flow1.png)

Následující kroky popisují postup podrobněji. Kroky se mírně liší v závislosti na tom, jestli používáte SAS nebo X.509 certifikáty pro ověřování zařízení:

1. Konfigurace nastavení připojení:

    - **Certifikáty X.509:** [Přidat a ověřit kořenový nebo zprostředkující certifikát](#connect-devices-using-x509-certificates) a použít ho ke generování certifikátů zařízení v následujícím kroku.
    - **SAS:** Zkopírujte primární klíč. Tento klíč je klíč SAS pro skupinu pro aplikace IoT Central. Používá se k vygenerování klíče SAS zařízení v následujícím kroku.
    ![Nastavení připojení SAS](media/concepts-connectivity/connection-settings-sas.png)

1. Vytvoření přihlašovacích údajů pro zařízení
    - **Certifikáty X.509:** Generovat listové certifikáty pro zařízení s použitím kořenový nebo zprostředkující certifikát, který jste přidali do vaší aplikace IoT Central. Ujistěte se, že používáte malé **ID zařízení** jako záznam CNAME v listové certifikáty. Pro účely testování, použijte ho [nástroj příkazového řádku](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) ke generování certifikátů zařízení.
    - **SAS:** Použijte tento [nástroj příkazového řádku](https://www.npmjs.com/package/dps-keygen) generovat klíče SAS zařízení. Použití skupiny **primární klíč** z předchozího kroku. ID zařízení musí být malá.

      K instalaci [klíče nástroj Generátor](https://github.com/Azure/dps-keygen), spusťte následující příkaz:

      ```cmd/sh
      npm i -g dps-keygen
      ```

      Ke generování klíče zařízení ze skupiny primární klíč SAS, spusťte následující příkaz:

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Chcete-li nastavit vaše zařízení, flash každému zařízení **ID oboru**, **ID zařízení**, a **certifikátu zařízení X.509** nebo **klíč SAS**.

1. Tato zařízení k přihlášení do aplikace IoT Central. Po přepnutí na zařízení poprvé připojí ke DPS načíst registrační informace IoT Central.

1. Zpočátku zobrazí připojené zařízení jako **nepřidružené zařízení** na **Device Explorer** stránky. Zařízení, Stav zřizování je **registrované**. **Přidružit** zařízení k šabloně příslušné zařízení a schválit zařízení pro připojení k vaší aplikaci IoT Central. Zařízení můžete získat připojovací řetězec ze služby IoT Hub a začít odesílat data. Zřizování zařízení je teď dokončený a Stav zřizování je nyní **zřízená**.

## <a name="provisioning-status"></a>Stav zřizování

Když skutečné zařízení se připojí k vaší aplikace IoT Central, jeho zřizování změny stavu následujícím způsobem:

1. Zařízení, Stav zřizování je první **registrované**. Tento stav znamená, že zařízení je vytvořené v IoT Central a má ID zařízení. Je zařízení registrováno při:
    - Jsou přidávány nové skutečné zařízení **Device Explorer** stránky.
    - Skupiny zařízení přidána pomocí **Import** na **Device Explorer** stránky.
    - Zařízení nebyl zaregistrován ručně na **Device Explorer** stránce, ale spojení s platné přihlašovací údaje a zobrazuje jako **Unassociated** zařízení na **Device Explorer**stránky.

1. Stav zřizování zařízení změní na **zřízená** po dokončení kroku zřizování zařízení, které jsou připojené k vaší aplikace IoT Central pomocí platných přihlašovacích údajů. V tomto kroku zařízení načte připojovací řetězec ze služby IoT Hub. Zařízení teď můžete připojit ke službě IoT Hub a začít odesílat data.

1. Operátor může blokovat zařízení. Když dojde k zablokování zařízení, kterou nelze odesílat data do vaší aplikace IoT Central. Blokováno zařízení jsou ve stavu zřizování **blokováno**. Předtím, než můžete pokračovat v odesílání dat, musí operátor resetování zařízení. Když operátor odblokuje zařízení vrátí do stavu zřizování na původní hodnotu, **registrované** nebo **zřízená**.

## <a name="sdk-support"></a>Podpora v sadě SDK

Nabídka sady SDK pro zařízení Azure nejjednodušší můžete implementovat kód vašeho zařízení. Sady SDK pro následující zařízení jsou k dispozici:

- [Sada Azure IoT SDK pro jazyk C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK pro Python](https://github.com/azure/azure-iot-sdk-python)
- [Sada Azure IoT SDK pro Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Sada Azure IoT SDK pro Javu](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK pro .NET](https://github.com/azure/azure-iot-sdk-csharp)

Každé zařízení připojí pomocí jedinečných připojovací řetězec identifikující zařízení. Zařízení lze připojit pouze ke službě IoT hub, ve kterém je zaregistrovaný. Při vytváření skutečných zařízení v aplikaci Azure IoT Central aplikace generuje informace, které potřebujete k vytvoření připojovací řetězec pomocí `dps-keygen`.

### <a name="sdk-features-and-iot-hub-connectivity"></a>Funkce sady SDK a připojení k službě IoT Hub

Veškerá komunikace zařízení pomocí služby IoT Hub používá následující možnosti připojení k službě IoT Hub:

- [Zasílání zpráv typu zařízení cloud](../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Dvojčata zařízení](../iot-hub/iot-hub-devguide-device-twins.md)

Následující tabulka shrnuje, jak Azure IoT Central zařízení funkce mapují k funkcích ve službě IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Měření: Telemetrická data | Zasílání zpráv typu zařízení cloud |
| Vlastnosti zařízení | Ohlášené vlastnosti dvojčete zařízení |
| Nastavení | Dvojče zařízení požadovaného a ohlášené vlastnosti |

Další informace o použití sady SDK pro zařízení, najdete v jednom z následujících článků příklad kódu:

- [Připojení obecného klienta Node.js k aplikaci Azure IoT Central](howto-connect-nodejs.md)
- [Připojte Raspberry Pi zařízení do aplikace Azure IoT Central](howto-connect-raspberry-pi-python.md)
- [Připojte zařízení za kit DevDiv do aplikace Azure IoT Central](howto-connect-devkit.md).

### <a name="protocols"></a>Protokoly

Sady SDK pro zařízení se podporují následující síťové protokoly pro připojení do služby IoT hub:

- MQTT
- AMQP
- HTTPS

Informace o těchto protokolů rozdíl a pokyny o volbě jeden najdete v tématu [volba komunikačního protokolu](../iot-hub/iot-hub-devguide-protocols.md).

Pokud vaše zařízení nemohou použít žádný z podporovaných protokolů, můžete použít Azure IoT Edge na protokol převodu. IoT Edge podporuje další scénáře intelligence na the-edge pro snižování zátěže zpracování do hraničních zařízení z aplikace v Azure IoT Central.

## <a name="security"></a>Zabezpečení

Všechna data se vyměňují mezi zařízeními a vaše Azure IoT Central je zašifrovaná. IoT Hub provádí ověřování každý požadavek ze zařízení, která se připojuje k některé z koncových bodů určených pro zařízení služby IoT Hub. Zařízení se pokud chcete vyhnout, výměnu přihlašovacích údajů při přenosu, používá k ověření podepsané tokeny. Další informace najdete v tématu, [řízení přístupu ke službě IoT Hub](../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Další postup

Teď, když jste se dozvěděli o připojení zařízení v Azure IoT Central, tady jsou další navrhované kroky:

- [Příprava a připojte zařízení za DevKit](howto-connect-devkit.md)
- [Příprava a připojení Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Připojení obecného klienta Node.js k aplikaci Azure IoT Central](howto-connect-nodejs.md)
- [SDK PRO JAZYK C: Klientská sada SDK zařízení pro zřizování](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
