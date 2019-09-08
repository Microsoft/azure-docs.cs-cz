---
title: Diagnostika a řešení potíží odpojení pomocí Azure IoT Hub
description: Naučte se diagnostikovat a řešit běžné chyby s připojením zařízení pro Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: 3904c6390cfe8de197bae470c4ae32d22605ae6a
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801423"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Zjištění a řešení potíží odpojení pomocí Azure IoT Hub

Problémy s připojením pro zařízení IoT můžou být obtížné řešit, protože existuje mnoho možných bodů selhání. Všechny problémy s logikou aplikací na straně zařízení, fyzických sítí, protokolů, hardwaru a Azure IoT Hub můžou způsobovat problémy. Tento článek poskytuje doporučení, jak zjistit a řešit potíže s připojením zařízení na straně cloudu (na rozdíl od straně zařízení).

## <a name="get-alerts-and-error-logs"></a>Získat výstrahy a protokoly chyb

Pomocí Azure Monitor získat výstrahy a zapsat protokoly při vyřazení zařízení z provozu.

### <a name="turn-on-diagnostic-logs"></a>Zapnout diagnostické protokoly

Pokud chcete protokolovat události a chyby připojení zařízení, zapněte diagnostiku pro IoT Hub.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Přejděte do služby IoT Hub.

3. Vyberte **nastavení diagnostiky**.

4. Vyberte **zapnout diagnostiku**.

5. Povolte shromažďování protokolů **připojení** .

6. Pro snazší analýzu zapněte **odeslání do Log Analytics** ([Viz ceny](https://azure.microsoft.com/pricing/details/log-analytics/)). Podívejte se na příklad v části [řešení chyb připojení](#resolve-connectivity-errors).

   ![Doporučená nastavení](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Další informace najdete v tématu [monitorování stavu Azure IoT Hub a rychlé diagnostikování problémů](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-the-_connected-devices_-count-metric"></a>Nastavte výstrahy pro metriku počtu _připojených zařízení_ .

Pokud chcete dostávat upozornění, když se zařízení odpojí, nakonfigurujte upozornění na metrikě **připojená zařízení (Preview)** .

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Přejděte do služby IoT Hub.

3. Vyberte **výstrahy**.

4. Vyberte **nové pravidlo výstrahy**.

5. Vyberte **Přidat podmínku**a pak vyberte připojená zařízení (Preview).

6. Dokončete nastavení požadovaných prahových hodnot a možností upozorňování podle pokynů.

Další informace najdete v tématu [co jsou klasické výstrahy v Microsoft Azure?](../azure-monitor/platform/alerts-overview.md).

## <a name="resolve-connectivity-errors"></a>Řešení chyb připojení

Když zapnete diagnostické protokoly a výstrahy pro připojená zařízení, zobrazí se upozornění, když dojde k chybám. Tato část popisuje, jak vyřešit běžné problémy, když obdržíte výstrahu. Následující postup předpokládá, že jste nastavili protokoly Azure Monitor pro diagnostické protokoly.

1. Pro **Log Analytics** v Azure Portal si Projděte svůj pracovní prostor.

2. Vyberte **prohledávání protokolu**.

3. Pokud chcete izolovat protokoly chyb připojení pro IoT Hub, zadejte následující dotaz a pak vyberte **Spustit**:

    ```kusto
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. Pokud jsou k dispozici výsledky, `OperationName`vyhledejte `ResultType` , (kód chyby) a `ResultDescription` (chybová zpráva), abyste získali více podrobností o chybě.

   ![Příklad protokolu chyb](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

2. Pomocí této tabulky můžete pochopit a řešit běžné chyby.

    | Chyba | Původní příčina | Řešení |
    |-------|------------|------------|
    | 404104 DeviceConnectionClosedRemotely | Připojení bylo zavřeno zařízením, ale IoT Hub neví, proč. Mezi běžné příčiny patří časový limit MQTT/AMQP a ztráta připojení k Internetu. | Ujistěte se, že se zařízení může připojit k IoT Hub [otestováním připojení](tutorial-connectivity.md). Pokud je připojení v pořádku, ale zařízení se odpojí, ujistěte se, že jste implementovali správnou logiku zařízení Keep Alive pro zvolený protokol (MQTT/AMPQ). |
    | 401003 IoTHubUnauthorized | IoT Hub nemohl ověřit připojení. | Ujistěte se, že vypršela platnost SAS nebo jiného tokenu zabezpečení, který používáte. Sady [SDK Azure IoT](iot-hub-devguide-sdks.md) automaticky generují tokeny bez nutnosti speciální konfigurace. |
    | 409002 LinkCreationConflict | Zařízení má více než jedno připojení. Když se pro zařízení dostane nový požadavek na připojení, IoT Hub se s touto chybou zavře předchozí. | V nejběžnějším případě zařízení zjistí odpojení a pokusí se znovu vytvořit připojení, ale IoT Hub se i nadále považovat za připojené zařízení. IoT Hub zavře předchozí připojení a zaznamená tuto chybu. Tato chyba se obvykle zobrazuje v důsledku vedlejšího efektu jiného, přechodného problému, takže se můžete podívat na další chyby v protokolech k dalšímu řešení potíží. V opačném případě se ujistěte, že chcete vydat novou žádost o připojení pouze v případě, že se připojení uvolní. |
    | 500001 ServerError | IoT Hub došlo k problému na straně serveru. Problém je pravděpodobně přechodný. I když tým IoT Hub zachovává [smlouvu SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/), malé podmnožiny IoT Hub uzlů můžou občas docházet k přechodným chybám. Když se vaše zařízení pokusí připojit k uzlu, kde dochází k problémům, zobrazí se tato chyba. | Chcete-li zmírnit přechodnou chybu, vydejte na zařízení Opakovaný pokus. Pokud chcete [automaticky spravovat opakované pokusy](iot-hub-reliability-features-in-sdks.md#connection-and-retry), ujistěte se, že používáte nejnovější verzi [sad SDK služby Azure IoT](iot-hub-devguide-sdks.md).<br><br>Osvědčené postupy při zpracování přechodných chyb a opakování najdete v tématu [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).  <br><br>Pokud potíže potrvají i po opakování, zkontrolujte [Resource Health](iot-hub-monitor-resource-health.md#use-azure-resource-health) a [Azure status](https://azure.microsoft.com/status/history/) a zjistěte, zda IoT Hub má známý problém. Pokud neexistují žádné známé problémy a problém přetrvává, obraťte se na [podporu a požádejte](https://azure.microsoft.com/support/options/) o další šetření. |
    | 500008 GenericTimeout | IoT Hub nemohl dokončit požadavek na připojení před vypršením časového limitu. Podobně jako v 500001 ServerError se tato chyba může dočasná. | Postupujte podle kroků pro řešení potíží, které se 500001 ServerError, do hlavní příčiny a vyřešte tuto chybu.|

## <a name="other-steps-to-try"></a>Další kroky k vyzkoušení

Pokud předchozí kroky nemohly být užitečné, můžete zkusit:

* Pokud máte přístup k problematickým zařízením buď fyzicky, nebo vzdáleně (jako je SSH), postupujte podle pokynů v [Průvodci odstraňováním potíží na straně zařízení](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) a pokračujte v řešení potíží.

* Ověřte, jestli jsou zařízení **povolená** v Azure Portal > vaše zařízení IoT ve službě iot hub >.

* Získejte pomoc od služby [azure IoT Hub Fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)nebo [podpory Azure](https://azure.microsoft.com/support/options/).

Pokud vám tato příručka nepomohly vylepšit dokumentaci pro všechny uživatele, ponechte v části zpětná vazba komentář.

## <a name="next-steps"></a>Další kroky

* Další informace o řešení přechodných problémů naleznete v tématu [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).

* Další informace o sadě Azure IoT SDK a správě opakování najdete v tématu [Správa připojení a spolehlivého zasílání zpráv pomocí sad SDK pro zařízení azure IoT Hub](iot-hub-reliability-features-in-sdks.md#connection-and-retry).
