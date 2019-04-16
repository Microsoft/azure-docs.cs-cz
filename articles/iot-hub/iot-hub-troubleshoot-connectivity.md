---
title: Diagnostika a řešení potíží odpojí službou Azure IoT Hub
description: Naučte se diagnostikovat a řešit běžné chyby s připojením zařízení pro službu Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: a107689796c58b17c445e7a9cf7c6f0402ef6005
ms.sourcegitcommit: e89b9a75e3710559a9d2c705801c306c4e3de16c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2019
ms.locfileid: "59571048"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Zjišťování a řešení potíží s odpojí službou Azure IoT Hub

Problémy s připojením zařízení IoT může být obtížné vyřešit, protože existuje mnoho možných bodů selhání. Logika aplikace na straně zařízení, fyzické sítě, protokoly, hardwaru a Azure IoT Hub může způsobit všechny problémy. Tento článek obsahuje doporučení o tom, jak detekovat a řešit problémy s připojením zařízení ze strany cloudu (na rozdíl od zařízení strana).

## <a name="get-alerts-and-error-logs"></a>Získání výstrah a protokoly chyb

Pomocí Azure monitoru upozornění a zapisují protokoly při připojení zařízení vyřadit.

### <a name="turn-on-diagnostic-logs"></a>Zapnout diagnostické protokoly

Do protokolu událostí připojení zařízení a chyb, zapněte diagnostiku pro službu IoT Hub.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Přejděte do služby IoT hub.

3. Vyberte **nastavení diagnostiky**.

4. Vyberte **zapnout diagnostiku**.

5. Povolit **připojení** protokoly, které se mají shromažďovat.

6. Pro jednodušší analýzu, zapněte **odesílat do Log Analytics** ([podívejte se na cenách](https://azure.microsoft.com/pricing/details/log-analytics/)). Podívejte se na příklad v části [řešení chyb připojení](#resolve-connectivity-errors).

   ![Doporučená nastavení](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Další informace najdete v tématu [monitorování stavu služby Azure IoT Hub a rychlá Diagnostika potíží](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-the-connected-devices-count-metric"></a>Nastavení upozornění pro _připojená zařízení_ počet metrik

Zasílání výstrah v případě odpojení zařízení, konfigurace výstrah pro **připojené zařízení (preview)** metriku.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Přejděte do služby IoT hub.

3. Vyberte **výstrahy**.

4. Vyberte **nové pravidlo upozornění**.

5. Vyberte **přidat podmínku**, pak vyberte "Připojené zařízení (preview)".

6. Dokončení instalace požadované prahové hodnoty a výstrahy možnosti podle následujících pokynů.

Další informace najdete v tématu [co jsou upozornění v Microsoft Azure classic?](../azure-monitor/platform/alerts-overview.md).

## <a name="resolve-connectivity-errors"></a>Řešení chyb připojení

Když zapnete diagnostické protokoly a upozornění na připojených zařízení, získáte výstrahy, když dojde k chybám. Tato část popisuje, jak vyřešit běžné problémy, když obdržíte výstrahu. Následující postup předpokládá, že jste nastavili protokoly Azure monitoru pro diagnostické protokoly.

1. Získat pracovní prostor **Log Analytics** na webu Azure Portal.

2. Vyberte **prohledávání protokolů**.

3. Protokoly chyb připojení izolace pro službu IoT Hub, zadejte následující dotaz a pak vyberte **spustit**:

    ```
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. Pokud neexistují výsledky, vyhledejte `OperationName`, `ResultType` (kód chyby) a `ResultDescription` (chybová zpráva), abyste získali více podrobností o chybě.

   ![Příklad v protokolu chyb](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

2. Tato tabulka slouží k pochopení a vyřešení běžných chyb.

    | Chyba | Původní příčina | Řešení |
    |-------|------------|------------|
    | 404104 DeviceConnectionClosedRemotely | Připojení bylo ukončeno. zařízení, ale služby IoT Hub nebude vědět, proč. Mezi běžné příčiny patří protokol MQTT nebo AMQP časový limit a internet ztrátě připojení. | Ujistěte se, že se zařízení může připojit ke službě IoT Hub pomocí [testování připojení](tutorial-connectivity.md). Pokud připojení je v pořádku, ale zařízení se přerušovaně, ujistěte se, že implementovat logiku správné keep alive zařízení podle vaší volby protokolu (protokol MQTT nebo AMPQ). |
    | 401003 IoTHubUnauthorized | IoT Hub se nepovedlo ověřit připojení. | Ujistěte se, že nevyprší platnost SAS nebo další token zabezpečení, které používáte. [Sady SDK Azure IoT](iot-hub-devguide-sdks.md) automaticky generovat tokeny bez nutnosti zvláštní konfiguraci. |
    | 409002 LinkCreationConflict | Zařízení obsahuje víc než jedno připojení. Jakmile novou žádost o připojení pro zařízení, IoT Hub ukončí předchozímu s touto chybou. | V případě nejběžnějších zařízení k odpojení zjistí a pokusí se znovu vytvořit připojení, ale stále považuje připojené zařízení služby IoT Hub. IoT Hub ukončí předchozí připojení a protokoly k této chybě. Tato chyba se obvykle zobrazí jako vedlejší účinek různých, přechodný problém, proto vyhledejte jiné chyby v protokolech, chcete-li pokračovat v řešení potíží. Jinak Ujistěte se, že vydat novou žádost o připojení jenom v případě, že připojení. |
    | 500001 ServerError | IoT Hub narazili na problém na straně serveru. Problém je pravděpodobně přechodná. Zatímco funguje týmu služby IoT Hub obtížné spravovat [smlouvy SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/), malé podmnožin uzlů služby IoT Hub může občas docházet k přechodným chybám. Pokud vaše zařízení se pokusí připojit k uzlu, který je s problémy, se zobrazí tato chyba. | Ke zmírnění přechodných chyb, vydejte opakování ze zařízení. K [automaticky, můžete opakování spravovat](iot-hub-reliability-features-in-sdks.md#connection-and-retry), ujistěte se, že používáte nejnovější verzi [sad SDK Azure IoT](iot-hub-devguide-sdks.md).<br><br>Osvědčeným postupem na zpracování přechodných chyb a opakování, naleznete v tématu [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).  <br><br>Pokud se problém nevyřeší počet pokusů, zkontrolujte [Resource Health](iot-hub-monitor-resource-health.md#use-azure-resource-health) a [stav Azure](https://azure.microsoft.com/status/history/) zobrazíte, pokud služby IoT Hub je známý problém. Pokud neexistuje žádné známé problémy a potíže přetrvávají, [obraťte se na podporu](https://azure.microsoft.com/support/options/) o pomoc. |
    | 500008 GenericTimeout | IoT Hub nelze dokončit žádost o připojení, než vyprší časový limit. Podobně jako 500001 ServerError tato chyba je pravděpodobně přechodná. | Použijte postup řešení potíží pro 500001 ServerError na hlavní příčinu a tuto chybu vyřešit.|

## <a name="other-steps-to-try"></a>Další kroky k vyzkoušení

Pokud vám předchozí kroky nepomohly můžete vyzkoušet:

* Pokud máte přístup k problematické zařízení fyzicky nebo vzdáleně (jako jsou SSH), postupujte [Průvodce odstraňováním potíží na straně zařízení](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) odstraňování potíží.

* Ověřte, že jsou vaše zařízení **povoleno** na webu Azure Portal > služby IoT hub > zařízení IoT.

* Získejte pomoc od [fórum pro Azure IoT Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub), nebo [podpory Azure](https://azure.microsoft.com/support/options/).

Abychom pomohli vylepšit dokumentaci pro všechny uživatele, napište komentář ve níže v části zpětnou vazbu, pokud vám tato příručka nepomohly.

## <a name="next-steps"></a>Další postup

* Další informace o řešení přechodné problémy, najdete v článku [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).

* Další informace o sadě SDK Azure IoT a při správě opakovaných pokusů, naleznete v tématu [Správa připojení a spolehlivé zasílání zpráv pomocí sady SDK pro zařízení Azure IoT Hub](iot-hub-reliability-features-in-sdks.md#connection-and-retry).
