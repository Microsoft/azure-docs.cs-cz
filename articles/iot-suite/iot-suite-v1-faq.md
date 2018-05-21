---
title: Azure IoT Suite – nejčastější dotazy | Microsoft Docs
description: Nejčastější dotazy k sadě IoT Suite
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: 4c8ecdae8d84028986b4b3fba94486ce8eb7f1bc
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Nejčastější dotazy k sadě IoT Suite

Viz také konkrétní připojené factory [– nejčastější dotazy](../iot-accelerators/iot-accelerators-faq-cf.md).

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Kde najdu zdrojový kód pro předkonfigurované řešení?

Zdrojový kód je uložena v následující úložišť GitHub:
* [Předkonfigurované řešení vzdáleného monitorování][lnk-remote-monitoring-github]
* [Předkonfigurované řešení prediktivní údržby][lnk-predictive-maintenance-github]
* [Připojené objekt pro vytváření předkonfigurovaného řešení](https://github.com/Azure/azure-iot-connected-factory)

### <a name="how-do-i-update-to-the-latest-version-of-the-remote-monitoring-preconfigured-solution-that-uses-the-iot-hub-device-management-features"></a>Jak aktualizovat na nejnovější verzi předkonfigurovaného řešení vzdáleného monitorování používající funkce správy zařízení IoT Hub?

* Pokud nasadíte předkonfigurované řešení ze https://www.azureiotsuite.com/ lokalitě, vždy nasazování novou instanci třídy nejnovější verzi řešení.
* Pokud nasadíte předkonfigurované řešení pomocí příkazového řádku, můžete aktualizovat existující nasazení s nový kód. V tématu [cloudové nasazení] [ lnk-cloud-deployment] v Githubu [úložiště][lnk-remote-monitoring-github].

### <a name="how-can-i-add-support-for-a-new-device-method-to-the-remote-monitoring-preconfigured-solution"></a>Jak můžete přidat podporu pro nové zařízení metody pro předkonfigurované řešení vzdáleného monitorování?

Najdete v části [přidat podporu pro nové metody pro simulátoru] [ lnk-add-method] v [přizpůsobení předkonfigurovaného řešení] [ lnk-customize] článku.

### <a name="the-simulated-device-is-ignoring-my-desired-property-changes-why"></a>Simulované zařízení provedené změny požadovanou vlastnost, proč ignoruje?
V předkonfigurovaného řešení vzdáleného monitorování, simulované zařízení kód používá jenom **Desired.Config.TemperatureMeanValue** a **Desired.Config.TelemetryInterval** potřeby vlastnosti, které chcete aktualizovat hlášené vlastnosti. Všechny ostatní žádosti o změnu požadovanou vlastnost se ignorují.

### <a name="my-device-does-not-appear-in-the-list-of-devices-in-the-solution-dashboard-why"></a>Moje zařízení nezobrazí v seznamu zařízení na řídicím panelu řešení, proč?

Seznam zařízení na řídicím panelu řešení používá dotaz vrátit seznam zařízení. V současné době dotaz nemůže vrátit více než 10 tisíc zařízení. Pokuste konkrétnější kritéria hledání pro svůj dotaz.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Jaký je rozdíl mezi odstranění skupiny prostředků na portálu Azure a kliknutím na Odstranit v předkonfigurovaném řešení na stránkách azureiotsuite.com?

* Pokud odstraníte předkonfigurované řešení na [azureiotsuite.com][lnk-azureiotsuite], odstranit všechny prostředky, které byly zřízeny při vytváření předkonfigurovaného řešení. Pokud jste do skupiny prostředků přidali další prostředky, tyto prostředky budou také odstraněny. 
* Pokud odstraníte skupinu prostředků [portál Azure][lnk-azure-portal], se odstraní pouze prostředky v příslušné skupině prostředků. Musíte také odstranit aplikaci Azure Active Directory, které jsou přidružené k předkonfigurovanému řešení [portál Azure][lnk-azure-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Kolik instancí služby IoT Hub můžete zřídit v jednom předplatném?

Ve výchozím nastavení můžete zřídit [10 centra IoT na jedno předplatné][link-azuresublimits]. Můžete vytvořit [lístek podpory Azure] [ link-azuresupportticket] chcete tento limit zvýšit. V důsledku toho vzhledem k tomu, že každé předkonfigurované řešení zřídí novou službu IoT Hub, můžete zřídit až 10 předkonfigurovaných řešení v daném předplatném. 

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Kolik instancí Azure Cosmos DB můžete zřídit v jednom předplatném?

Padesát. Můžete vytvořit [lístek podpory Azure] [ link-azuresupportticket] chcete tento limit zvýšit, ale ve výchozím nastavení, můžete zřídit 50 instancí Cosmos DB za předplatné. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Kolik bezplatných rozhraní API Map Bing můžu zřídit v jednom předplatném?

Dvě. Můžete vytvořit pouze dvě vnitřní transakce úroveň 1 mapy Bing pro podnikových plánů v předplatné Azure. Ve výchozím nastavení s plánem vnitřní transakce úrovně 1 se zřídí řešení vzdáleného monitorování. V důsledku toho můžete v daném předplatném zřídit nanejvýš dvě předkonfigurovaná řešení vzdáleného monitorování bez možnosti úprav.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>Mám nasazené řešení vzdáleného monitorování se statickou mapou, jak přidám interaktivní mapu Bing?

1. Získání rozhraní API map Bing pro QueryKey Enterprise z [portál Azure][lnk-azure-portal]: 
   
   1. Přejděte do skupiny prostředků, kde je vaše rozhraní API map Bing pro podniky v [portál Azure][lnk-azure-portal].
   2. Klikněte na tlačítko **všechna nastavení**, pak **Správa klíčů**. 
   3. Zobrazí se dva klíče: **MasterKey** a **QueryKey**. Zkopírujte hodnotu pro **QueryKey**.
      
      > [!NOTE]
      > Nemáte účet rozhraní API Map Bing pro podniky? Vytvořit v [portál Azure] [ lnk-azure-portal] nástrojem kliknutím na + nové, vyhledáte rozhraní API map Bing pro podniky a postupujte podle výzev a vytvořte.
      > 
      > 
2. Stáhněte dolů nejnovější kód [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].
3. Spusťte místní nebo cloudové nasazení podle pokynů v nasazení příkazového řádku ve složce /docs/ v úložišti. 
4. Po spuštění místního nebo cloudového nasazení vyhledejte v kořenové složce soubor *. user.config vytvořený během nasazení. Otevřete tento soubor v textovém editoru. 
5. Změňte následující řádek zahrnout hodnotu, kterou jste zkopírovali z vaší **QueryKey**: 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Můžu vytvořit předkonfigurované řešení, když mám Microsoft Azure pro DreamSpark?

> [!NOTE]
> Microsoft Azure pro DreamSpark se nyní označuje jako Microsoft Imagine pro studenty.

V současné době nelze vytvořit předkonfigurované řešení s [Microsoft Azure pro DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) účtu. Ale můžete vytvořit [Bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) si během několika minut, který vám umožní vytvořit předkonfigurované řešení.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Můžete vytvořit předkonfigurované řešení, pokud mám předplatné Cloud Solution Provider (CSP)?

V současné době nelze vytvořit předkonfigurované řešení s předplatným Cloud Solution Provider (CSP). Ale můžete vytvořit [Bezplatný zkušební účet Azure] [ lnk-30daytrial] si během několika minut, který vám umožní vytvořit předkonfigurované řešení.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Jak se odstraním klienta Azure AD?

Najdete v příspěvku blogu od Erica Golpeho [návod odstranění klient služby Azure AD][lnk-delete-aad-tennant].

### <a name="next-steps"></a>Další postup

Můžete si taky prostudovat některé další funkce a možnosti předkonfigurovaných řešení sady IoT Suite:

* [Přehled řešení předkonfigurované prediktivní údržby][lnk-predictive-overview]
* [Přehled připojené objekt pro vytváření předkonfigurovaného řešení](../iot-accelerators/iot-accelerators-connected-factory-overview.md)
* [Zabezpečení IoT od počátku][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx
[lnk-cloud-deployment]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
[lnk-add-method]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md#add-support-for-a-new-method-to-the-simulator
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-predictive-maintenance-github]: https://github.com/Azure/azure-iot-predictive-maintenance
