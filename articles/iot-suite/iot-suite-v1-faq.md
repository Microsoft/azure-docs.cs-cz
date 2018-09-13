---
title: Nejčastější dotazy k Azure IoT Suite | Dokumentace Microsoftu
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
ms.openlocfilehash: 7e7c4affee64a945900c02b6375ba4df5d085183
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35757800"
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Nejčastější dotazy k sadě IoT Suite

Viz také konkrétní propojené továrny [nejčastější dotazy k](../iot-accelerators/iot-accelerators-faq-cf.md).

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Kde najdu zdrojový kód předkonfigurovaného řešení?

Zdrojový kód je uložena v následujícím úložišti GitHub:
* [Předkonfigurované řešení vzdáleného monitorování][lnk-remote-monitoring-github]
* [Předkonfigurované řešení prediktivní údržby][lnk-predictive-maintenance-github]
* [Předkonfigurované řešení propojené továrny](https://github.com/Azure/azure-iot-connected-factory)

### <a name="how-do-i-update-to-the-latest-version-of-the-remote-monitoring-preconfigured-solution-that-uses-the-iot-hub-device-management-features"></a>Jak můžu aktualizovat na nejnovější verzi předkonfigurovaném řešení vzdáleného monitorování, která používá funkce správy zařízení služby IoT Hub?

* Pokud nasadíte předkonfigurované řešení z https://www.azureiotsuite.com/ lokality, vždy nasadí novou instanci třídy nejnovější verzi řešení.
* Pokud nasadíte předkonfigurované řešení z příkazového řádku, můžete aktualizovat existující nasazení s novým kódem. Zobrazit [cloudové nasazení] [ lnk-cloud-deployment] v Githubu [úložiště][lnk-remote-monitoring-github].

### <a name="how-can-i-add-support-for-a-new-device-method-to-the-remote-monitoring-preconfigured-solution"></a>Jak mohu přidat podporu pro nové metody zařízení k předkonfigurovanému řešení vzdáleného monitorování?

V části [přidat podporu pro novou metodu do simulátoru] [ lnk-add-method] v [přizpůsobení předkonfigurovaného řešení] [ lnk-customize] článku.

### <a name="the-simulated-device-is-ignoring-my-desired-property-changes-why"></a>Simulované zařízení se ignoruje změny požadované vlastnosti proč?
V předkonfigurovaného řešení vzdáleného monitorování, kód simulovaného zařízení využívá pouze **Desired.Config.TemperatureMeanValue** a **Desired.Config.TelemetryInterval** požadované vlastnosti pro aktualizaci ohlášených vlastností. Všechny ostatní žádosti o změnu požadované vlastnosti jsou ignorovány.

### <a name="my-device-does-not-appear-in-the-list-of-devices-in-the-solution-dashboard-why"></a>Moje zařízení nezobrazí v seznamu zařízení na řídicím panelu řešení, proč?

Seznam zařízení na řídicím panelu řešení používá dotaz se seznam zařízení. Dotaz v současné době nelze vrátit více než 10 tisíc zařízení. Pokuste více omezující kritérií vyhledávání pro váš dotaz.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Jaký je rozdíl mezi odstranění skupiny prostředků na portálu Azure a kliknutím na Odstranit v předkonfigurovaném řešení na stránkách azureiotsuite.com?

* Pokud odstraníte předkonfigurované řešení na [azureiotsuite.com][lnk-azureiotsuite], odstraňte všechny prostředky, které byly zřízeny při vytváření předkonfigurovaného řešení. Pokud jste do skupiny prostředků přidali další prostředky, odstraní se také tyto prostředky. 
* Pokud odstraníte skupinu prostředků [webu Azure portal][lnk-azure-portal], můžete odstranit pouze prostředky v příslušné skupině prostředků. Musíte také odstranit aplikaci Azure Active Directory spojené s předkonfigurovaným řešením v [webu Azure portal][lnk-azure-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Kolik instancí služby IoT Hub můžete zřídit v jednom předplatném?

Ve výchozím nastavení můžete zřídit [10 IoT hub na předplatné][link-azuresublimits]. Můžete vytvořit [lístek podpory Azure] [ link-azuresupportticket] chcete tento limit zvýšit. V důsledku toho od každé předkonfigurované řešení zřídí novou službu IoT Hub, můžete zřídit až 10 předkonfigurovaná řešení v rámci daného předplatného. 

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Kolik instancí služby Azure Cosmos DB může zřídit v jednom předplatném?

Padesát. Můžete vytvořit [lístek podpory Azure] [ link-azuresupportticket] chcete tento limit zvýšit, ale ve výchozím nastavení, můžete zřídit 50 instancí Cosmos DB na jedno předplatné. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Kolik bezplatných rozhraní API Map Bing můžu zřídit v jednom předplatném?

Dvě. V předplatném Azure můžete vytvořit pouze dvě vnitřní transakce úrovně 1 mapy Bing pro podnikových plánů. Ve výchozím nastavení vnitřní transakce úrovně 1 plánu se zřídí řešení vzdáleného monitorování. V důsledku toho můžete v daném předplatném zřídit nanejvýš dvě předkonfigurovaná řešení vzdáleného monitorování bez možnosti úprav.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>Mám nasazené řešení vzdáleného monitorování se statickou mapou, jak přidám interaktivní mapu Bing?

1. Získání rozhraní API map Bing pro Enterprise QueryKey [webu Azure portal][lnk-azure-portal]: 
   
   1. Přejděte do skupiny prostředků, ve kterém je vaše rozhraní API map Bing pro podniky v [webu Azure portal][lnk-azure-portal].
   2. Klikněte na tlačítko **všechna nastavení**, pak **Správa klíčů**. 
   3. Zobrazí se dva klíče: **MasterKey** a **QueryKey**. Zkopírujte hodnotu **QueryKey**.
      
      > [!NOTE]
      > Nemáte účet rozhraní API Map Bing pro podniky? Vytvořte si ho [webu Azure portal] [ lnk-azure-portal] tím, že kliknete na + nové, vyhledáte rozhraní API map Bing pro podniky a postupujte podle výzev a vytvořte.
      > 
      > 
2. Stáhněte nejnovější kód ze [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].
3. Spusťte místní nebo cloudové nasazení příkazového řádku nasazování ve složce /docs/ v úložišti. 
4. Po spuštění místního nebo cloudového nasazení vyhledejte v kořenové složce soubor *. user.config vytvořený během nasazení. Otevřete tento soubor v textovém editoru. 
5. Změňte následující řádek zahrnout hodnotu zkopírovanou z vaší **QueryKey**: 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Můžu vytvořit předkonfigurované řešení, když mám Microsoft Azure pro DreamSpark?

> [!NOTE]
> Microsoft Azure pro DreamSpark je teď označuje jako Microsoft Imagine pro studenty.

V současné době nelze vytvořit předkonfigurované řešení se [Microsoft Azure pro DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) účtu. Však můžete vytvořit [Bezplatný zkušební účet pro Azure](https://azure.microsoft.com/free/) během několika minut, která vám umožní vytvořit předkonfigurované řešení.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Můžete vytvořit předkonfigurované řešení, když mám předplatné Cloud Solution Provider (CSP)?

V současné době nelze vytvořit předkonfigurované řešení s předplatným Cloud Solution Provider (CSP). Však můžete vytvořit [Bezplatný zkušební účet pro Azure] [ lnk-30daytrial] během několika minut, která vám umožní vytvořit předkonfigurované řešení.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Jak odstranit tenanta služby Azure AD?

Najdete v příspěvku blogu Eric Golpeho [Walkthrough of Deleting Tenanta služby Azure AD][lnk-delete-aad-tennant].

### <a name="next-steps"></a>Další postup

Můžete si taky prostudovat některé další funkce a možnosti předkonfigurovaných řešení sady IoT Suite:

* [Přehled řešení předkonfigurované prediktivní údržby][lnk-predictive-overview]
* [Přehled řešení propojené továrny v předkonfigurovaném](../iot-accelerators/iot-accelerators-connected-factory-overview.md)
* [Zabezpečení IoT od počátku][lnk-security-groundup]

[lnk-predictive-overview]:../iot-accelerators/iot-accelerators-predictive-overview.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up

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
