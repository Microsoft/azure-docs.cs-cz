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
ms.openlocfilehash: 5bd66e3cb3902665aab9245a524a2bec6f57dc8c
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "42054628"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Zjišťování a řešení potíží s odpojí službou Azure IoT Hub

Problémy s připojením zařízení IoT může být obtížné vyřešit, protože existuje mnoho možných bodů selhání. Logika aplikace na straně zařízení, fyzické sítě, protokoly, hardwaru a Azure IoT Hub může způsobit všechny problémy. Tento dokument obsahuje doporučení o tom, jak detekovat a řešit problémy s připojením zařízení ze strany cloudu (na rozdíl od zařízení straně).

## <a name="get-alerts-and-error-logs"></a>Získání výstrah a protokoly chyb

Pomocí Azure monitoru upozornění a zapisují protokoly při připojení zařízení vyřadit.

### <a name="turn-on-diagnostic-logs"></a>Zapnout diagnostické protokoly 

Do protokolu událostí připojení zařízení a chyb, zapněte diagnostiku pro službu IoT Hub. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte do svého centra IoT.
1. Vyberte **nastavení diagnostiky**.
1. Potom vyberte **zapnout diagnostiku**.
1. Ujistěte se, že povolíte **připojení** protokoly, které se mají shromažďovat. 
1. Pro usnadnění analýzy, měli byste zapnout **odesílat do Log Analytics** ([podívejte se na cenách](https://azure.microsoft.com/pricing/details/log-analytics/)). Příklad dále v tomto článku pomocí Log Analytics.

   ![Doporučená nastavení][2]

Další informace najdete v tématu [monitorování stavu služby Azure IoT Hub a rychlá Diagnostika potíží](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-the-connected-devices-count-metric"></a>Nastavení upozornění pro metriku počet připojených zařízení

Zasílání výstrah v případě odpojení zařízení, konfigurace výstrah pro *připojená zařízení* metriku. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte do služby IoT Hub.
1. Vyberte **upozornění (klasická)**.
1. Klikněte na tlačítko **přidat upozornění metriky (klasické)**.
1. Vyplňte formulář a vyberte **OK**. 

   ![Doporučené upozornění metriky][3]

Další informace najdete v tématu [co jsou upozornění v Microsoft Azure classic?](../monitoring-and-diagnostics/monitoring-overview-alerts.md).

## <a name="resolve-connectivity-errors"></a>Řešení chyb připojení

Pokud výstrahy pro připojených zařízení a diagnostické protokoly jsou zapnuté, získáváte upozornění, když něco selže. Tato část popisuje, jak vyřešit běžné problémy, když obdržíte výstrahu. Následující postup předpokládá, že jste nastavili Log Analytics pro diagnostické protokoly. 

1. Získat pracovní prostor **Log Analytics** na webu Azure portal.
1. Klikněte na tlačítko **prohledávání protokolů**.
1. Protokoly chyb připojení izolace pro službu IoT Hub, zadejte tento dotaz do pole, stiskněte klávesu **spustit**.

    ```
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. Pokud neexistují výsledky, vyhledejte `OperationName`, `ResultType` (kód chyby) a `ResultDescription` (chybová zpráva), abyste získali více podrobností o chybě.

   ![Příklad v protokolu chyb][4]

1. Tato tabulka slouží k pochopení a vyřešení běžných chyb.

    | Chyba | Původní příčina | Řešení |
    |---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 404104 DeviceConnectionClosedRemotely | Připojení bylo ukončeno. zařízení, ale služby IoT Hub nebude vědět, proč. Mezi běžné příčiny patří protokol MQTT nebo AMQP časový limit a internet ztrátě připojení. | Ujistěte se, že se zařízení může připojit ke službě IoT Hub pomocí [testování připojení](tutorial-connectivity.md). Pokud připojení je v pořádku, ale zařízení se přerušovaně, ujistěte se, že implementovat logiku správné keep alive zařízení podle vaší volby protokolu (protokol MQTT nebo AMPQ). |
    | 401003 IoTHubUnauthorized | IoT Hub se nepovedlo ověřit připojení. | Ujistěte se, že nevyprší platnost SAS nebo další token zabezpečení, které používáte. [Sady SDK Azure IoT](iot-hub-devguide-sdks.md) automaticky generovat tokeny bez nutnosti zvláštní konfiguraci. |
    | 409002 LinkCreationConflict | Existuje více než jeden připojení pro stejné zařízení. Jakmile novou žádost o připojení pro zařízení, IoT Hub ukončí předchozímu s touto chybou. | V případě nejběžnějších zařízení k odpojení zjistí a pokusí se znovu vytvořit připojení, ale služby IoT Hub není považováno za že odpojené ještě, tak, aby bylo předchozí připojení se zavře a protokoly k této chybě. Tato chyba se obvykle zobrazí jako vedlejší účinek různých přechodný problém, proto vyhledejte jiné chyby v protokolech, chcete-li pokračovat v řešení potíží. Jinak Ujistěte se, že vydat novou žádost o připojení jenom v případě, že připojení. |
    | 500001 ServerError | IoT Hub narazili na problém na straně serveru. Problém je pravděpodobně přechodná. Zatímco funguje týmu služby IoT Hub obtížné spravovat [smlouvy SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/), malé podmnožin uzlů služby IoT Hub může občas docházet k přechodným chybám. Pokud vaše zařízení se pokusí připojit k uzlu, který je s problémy, se zobrazí tato chyba. | Ke zmírnění přechodných chyb, vydejte opakování ze zařízení. K [automaticky, můžete opakování spravovat](iot-hub-reliability-features-in-sdks.md#connection-and-retry), ujistěte se, že používáte nejnovější verzi [sad SDK Azure IoT](iot-hub-devguide-sdks.md).<br><br>Osvědčeným postupem na zpracování přechodných chyb a opakování, naleznete v tématu [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).  <br><br>Pokud se problém nevyřeší počet pokusů, zkontrolujte [Resource Health](iot-hub-monitor-resource-health.md#use-azure-resource-health) a [stav Azure](https://azure.microsoft.com/status/history/) zobrazíte, pokud služby IoT Hub je známý problém. Pokud neexistuje žádné známé problémy a potíže přetrvávají, [obraťte se na podporu](https://azure.microsoft.com/support/options/) o pomoc. |
    | 500008 GenericTimeout | IoT Hub nelze dokončit žádost o připojení, než vyprší časový limit. Podobně jako 500001 ServerError tato chyba je pravděpodobně přechodná. | Použijte postup řešení potíží pro 500001 ServerError na hlavní příčina a řešení této chyby.|

## <a name="other-steps-to-try"></a>Další kroky k vyzkoušení

Pokud vám výše uvedené kroky nepomohly tady je pár dalších věcí vyzkoušet:

* Pokud máte přístup k problematické zařízení fyzicky nebo vzdáleně (jako jsou SSH), postupujte [Průvodce odstraňováním potíží na straně zařízení](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) odstraňování potíží.
* Ověřte, že jsou vaše zařízení **povoleno** na webu Azure Portal > služby IoT Hub > zařízení IoT.
* Získejte pomoc od [fórum pro Azure IoT Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub), nebo [podpory Azure](https://azure.microsoft.com/support/options/).

Ke zlepšování dokumentace pro všechny uživatele, napište komentář dole, pokud vám tato příručka nepomohly.

## <a name="next-steps"></a>Další postup

* Další informace o řešení přechodné problémy, najdete v článku [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).
* Další informace o sadě SDK Azure IoT a při správě opakovaných pokusů, naleznete v tématu [Správa připojení a spolehlivé zasílání zpráv pomocí sady SDK pro zařízení Azure IoT Hub](iot-hub-reliability-features-in-sdks.md#connection-and-retry).

<!-- Images -->
[1]: ../../includes/media/iot-hub-diagnostics-settings/turnondiagnostics.png
[2]: ./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png
[3]: ./media/iot-hub-troubleshoot-connectivity/metric-alert.png
[4]: ./media/iot-hub-troubleshoot-connectivity/diag-logs.png
