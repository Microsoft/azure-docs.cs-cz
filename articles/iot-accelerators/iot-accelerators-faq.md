---
title: Azure IoT řešení akcelerátorů – nejčastější dotazy | Microsoft Docs
description: Nejčastější dotazy pro IoT řešení akcelerátorů
services: iot-suite
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
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 6c8c87dc4f7bb91ce227bbfbaf1cabf0c44d2e0b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Nejčastější dotazy pro IoT řešení akcelerátorů

Viz také [– nejčastější dotazy připojené konkrétní objekt pro vytváření](iot-accelerators-faq-cf.md) a [nejčastější dotazy týkající se vzdáleného monitorování specifické pro](iot-accelerators-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Kde najdu zdrojového kódu pro řešení akcelerátorů

Zdrojový kód je uložena v následující úložišť GitHub:

* [Accelerator řešení vzdáleného monitorování (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Accelerator řešení vzdáleného monitorování (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Accelerator řešení prediktivní údržby](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Připojené objekt pro vytváření řešení akcelerátoru](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Jaké sady SDK můžete použít pro vývoj klientů zařízení pro akcelerátorů řešení?

Odkazy na sady SDK zařízení IoT jiným jazykem (C, .NET, Java, Node.js, Python) najdete v [SDK služby Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdks) úložišť GitHub.

Pokud používáte zařízení DevKit, můžete najít prostředky a ukázky v [sady SDK pro IoT DevKit](https://github.com/Microsoft/devkit-sdk) úložiště GitHub.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Je k dispozici pro všechny tři řešení akcelerátorů nové architektury mikroslužeb?

V současné době pouze řešení vzdáleného monitorování používá architekturu mikroslužeb jako pokrývá nejširší scénář.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>V nové aktualizace jaké výhody poskytuje nové open source na základě mikroslužeb architektury?

Za poslední dva roky se výrazně vyvinuly cloudové architektury. Mikroslužeb této služby jako skvělé vzor k dosažení škálování a flexibilitu, aniž by došlo ke ztrátě vývoj rychlostí. Tento vzor architektury se používá v několika služeb Microsoft interně s skvělé spolehlivosti a škálovatelnosti výsledky. Jsme jsou uvedení těchto učení v praxi, aby naše zákazníky těžit z nich.

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Je k dispozici nový akcelerátor řešení ve stejné zeměpisné oblasti jako existující řešení?

Ano, nové vzdálené monitorování je dostupný ve stejné zeměpisné oblasti.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsuitecom"></a>Jaký je rozdíl mezi odstranění skupiny prostředků na portálu Azure a kliknutím na Odstranit v akcelerátor řešení na stránkách azureiotsuite.com?

* Pokud odstraníte akcelerátoru řešení v [azureiotsuite.com](https://www.azureiotsuite.com/), odstranit všechny prostředky, které byly zřízeny při vytváření akcelerátoru řešení. Pokud jste do skupiny prostředků přidali další prostředky, tyto prostředky budou také odstraněny.
* Pokud odstraníte skupinu prostředků [portál Azure](https://portal.azure.com), se odstraní pouze prostředky v příslušné skupině prostředků. Musíte také odstranit aplikaci Azure Active Directory přidruženou akcelerátoru řešení.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Můžete nadále využívat mé existující investice do akcelerátorů řešení Azure IoT?

Ano. Žádné řešení, které existuje ještě dnes nadále fungovat ve vašem předplatném Azure a zdrojový kód zůstává k dispozici na webu GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Kolik instancí služby IoT Hub můžete zřídit v jednom předplatném?

Ve výchozím nastavení můžete zřídit [10 centra IoT na jedno předplatné](../azure-subscription-service-limits.md#iot-hub-limits). Můžete vytvořit [lístek podpory Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) chcete tento limit zvýšit. V důsledku toho od každé řešení akcelerátoru zřizuje novou službu IoT Hub, můžete zřídit až 10 akcelerátorů řešení v daném předplatném.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Kolik instancí Azure Cosmos DB můžete zřídit v jednom předplatném?

Padesát. Můžete vytvořit [lístek podpory Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) chcete tento limit zvýšit, ale ve výchozím nastavení, můžete zřídit 50 instancí Cosmos DB za předplatné.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Kolik bezplatných rozhraní API Map Bing můžu zřídit v jednom předplatném?

Dvě. Můžete vytvořit pouze dvě vnitřní transakce úroveň 1 mapy Bing pro podnikových plánů v předplatné Azure. Ve výchozím nastavení s plánem vnitřní transakce úrovně 1 se zřídí řešení vzdáleného monitorování. V důsledku toho můžete v daném předplatném zřídit nanejvýš dvě předkonfigurovaná řešení vzdáleného monitorování bez možnosti úprav.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Můžete vytvořit akcelerátor řešení, když mám Microsoft Azure pro DreamSpark?

> [!NOTE]
> Microsoft Azure pro DreamSpark se nyní označuje jako Microsoft Imagine pro studenty.

V současné době nelze vytvořit akcelerátor řešení s [Microsoft Azure pro DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) účtu. Ale můžete vytvořit [Bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) si během několika minut, který vám umožní vytvořit akcelerátor řešení.

### <a name="can-i-create-a-solution-accelerator-if-i-have-cloud-solution-provider-csp-subscription"></a>Můžete vytvořit akcelerátor řešení, pokud mám předplatné Cloud Solution Provider (CSP)?

V současné době nelze vytvořit akcelerátor řešení s předplatným Cloud Solution Provider (CSP). Ale můžete vytvořit [Bezplatný zkušební účet Azure](https://azure.microsoft.com/free/) si během několika minut, který vám umožní vytvořit akcelerátor řešení.

### <a name="how-do-i-delete-an-aad-tenant"></a>Jak se odstraním klienta AAD?

Najdete v příspěvku blogu od Erica Golpeho [návod odstranění klient služby Azure AD](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Další postup

Můžete si také prostudovat některé další funkce a možnosti akcelerátorů řešení IoT:

* [Prozkoumejte možnosti vzdáleného monitorování řešení akcelerátoru](iot-accelerators-remote-monitoring-explore.md)
* [Přehled akcelerátorů řešení prediktivní údržby](../iot-suite/iot-suite-predictive-overview.md)
* [Připojené přehled akcelerátoru objekt pro vytváření řešení](iot-accelerators-connected-factory-overview.md)
* [Zabezpečení IoT od základů](../iot-suite/securing-iot-ground-up.md)
