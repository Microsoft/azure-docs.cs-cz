---
title: Akcelerátory řešení Azure IoT – nejčastější dotazy | Dokumentace Microsoftu
description: Nejčastější dotazy k akcelerátorům řešení IoT
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 154d82ab5e309631df0bfa40df7fe14902327f46
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884271"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Nejčastější dotazy k akcelerátorům řešení IoT

Viz také [propojená specifické pro objekt pro vytváření – nejčastější dotazy](iot-accelerators-faq-cf.md) a [vzdálené – nejčastější dotazy pro monitorování specifických](iot-accelerators-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Kde lze najít zdrojový kód pro akcelerátory řešení?

Zdrojový kód je uložena v následujícím úložišti GitHub:

* [Akcelerátor řešení vzdálené monitorování (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Akcelerátor řešení vzdálené monitorování (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Akcelerátor řešení prediktivní údržby](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Akcelerátor řešení propojené továrny](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Jaké sady SDK můžete použít k vývoji klientů zařízení pro akcelerátory řešení?

Odkazy na sady SDK zařízení IoT jiný jazyk (C, .NET, Java, Node.js, Python) najdete v [sadami SDK služby Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdks) úložištích GitHub.

Pokud používáte DevKit zařízení, najdete prostředky a ukázky [sadou SDK IoT DevKit](https://github.com/Microsoft/devkit-sdk) úložiště GitHub.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Je k dispozici pro všechny tři řešení akcelerátory nové architektury mikroslužeb?

V současné době pouze řešení vzdáleného monitorování využívá architekturu mikroslužeb obsahuje nejširší scénář.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Jaké výhody nové open source založených na mikroslužbách architektury poskytuje v nové aktualizaci?

Za poslední dva roky se výrazně vyvinula cloudové architektury. Mikroslužeb mají umístila jako skvělé vzor k dosažení škálování a flexibility, aniž byste museli obětovat rychlost vývoje. Tento Architektonický model se používá v různými službami společnosti Microsoft interně skvělé spolehlivosti a škálovatelnosti výsledky. Jsme jsou uvedení tyto materiály v praxi, takže naši zákazníci využívat.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Kde najdu informace o předchozí verzi řešení vzdáleného monitorování

Předchozí verze akcelerátor řešení vzdálené monitorování se označuje jako řešení vzdáleného monitorování IoT Suite předkonfigurované. Můžete najít Archivovaná dokumentace na [ https://docs.microsoft.com/previous-versions/azure/iot-suite/ ](https://docs.microsoft.com/previous-versions/azure/iot-suite/).

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Je k dispozici nový akcelerátor řešení ve stejné zeměpisné oblasti jako existující řešení?

Ano, nové vzdálené monitorování je k dispozici ve stejné zeměpisné oblasti.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsuitecom"></a>Jaký je rozdíl mezi odstranění skupiny prostředků na webu Azure Portal a kliknutím na Odstranit v akcelerátoru řešení na stránkách azureiotsuite.com?

* Pokud odstraníte akcelerátor řešení v [azureiotsuite.com](https://www.azureiotsolutions.com/), odstraňte všechny prostředky, které byly zřízeny při vytvoření akcelerátor řešení. Pokud jste do skupiny prostředků přidali další prostředky, odstraní se také tyto prostředky.
* Pokud odstraníte skupinu prostředků [webu Azure portal](https://portal.azure.com), můžete odstranit pouze prostředky v příslušné skupině prostředků. Musíte také odstranit aplikaci Azure Active Directory přidružené k akcelerátoru řešení.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>I nadále využívat své existující investice do akcelerátory řešení Azure IoT?

Ano. Jakékoli řešení, které dnes existuje i nadále fungovat ve vašem předplatném Azure a zdrojový kód zůstane k dispozici na Githubu.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Kolik instancí služby IoT Hub můžete zřídit v jednom předplatném?

Ve výchozím nastavení můžete zřídit [10 IoT hub na předplatné](../azure-subscription-service-limits.md#iot-hub-limits). Můžete vytvořit [lístek podpory Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) chcete tento limit zvýšit. V důsledku toho od každé ustanovení akcelerátor řešení novou službu IoT Hub, můžete zřídit až 10 akcelerátory řešení v rámci daného předplatného.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Kolik instancí služby Azure Cosmos DB může zřídit v jednom předplatném?

Padesát. Můžete vytvořit [lístek podpory Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) chcete tento limit zvýšit, ale ve výchozím nastavení, můžete zřídit 50 instancí Cosmos DB na jedno předplatné.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Kolik bezplatných rozhraní API Map Bing můžu zřídit v jednom předplatném?

Dvě. V předplatném Azure můžete vytvořit pouze dvě vnitřní transakce úrovně 1 mapy Bing pro podnikových plánů. Ve výchozím nastavení s plánem vnitřní transakce úrovně 1 zřídí řešení vzdáleného monitorování. V důsledku toho můžete zřídit až dvě vzdálené monitorování řešení v rámci předplatného bez možnosti úprav.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Můžete vytvořit akcelerátoru řešení, když mám Microsoft Azure pro DreamSpark?

> [!NOTE]
> Microsoft Azure pro DreamSpark je teď označuje jako Microsoft Imagine pro studenty.

V současné době nelze vytvořit akcelerátoru řešení se [Microsoft Azure pro DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) účtu. Však můžete vytvořit [Bezplatný zkušební účet pro Azure](https://azure.microsoft.com/free/) během několika minut, která vám umožní vytvořit akcelerátoru řešení.

### <a name="how-do-i-delete-an-aad-tenant"></a>Jak se odstraním klienta AAD?

Najdete v příspěvku blogu Eric Golpeho [Walkthrough of Deleting Tenanta služby Azure AD](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Další postup

Můžete si také prostudovat některé další funkce a možnosti akcelerátorů řešení IoT:

* [Prozkoumejte možnosti akcelerátor řešení vzdálené monitorování](quickstart-remote-monitoring-deploy.md)
* [Přehled akcelerátorů řešení prediktivní údržby](iot-accelerators-predictive-overview.md)
* [Nasazení akcelerátoru řešení připojená továrna](quickstart-connected-factory-deploy.md)
* [Zabezpečení IoT od počátku](/azure/iot-fundamentals/iot-security-ground-up)
