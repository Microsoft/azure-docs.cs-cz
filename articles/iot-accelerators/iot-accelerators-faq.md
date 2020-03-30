---
title: Akcelerátory řešení IoT nejčastější dotazy – Azure | Dokumenty společnosti Microsoft
description: Tento článek odpovídá na nejčastější dotazy pro akcelerátory řešení IoT. Obsahuje odkazy na úložiště GitHub.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 7a2b167f90b4ec79f5fa515ded1c676ca7085e72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647709"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Nejčastější dotazy k akcelerátorům řešení IoT

Viz také [nejčastější dotazy k připojení k výrobním časem](iot-accelerators-faq-cf.md) a [nejčastější dotazy specifické pro vzdálené monitorování](iot-accelerators-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Kde najdu zdrojový kód akcelerátorů řešení?

Zdrojový kód je uložen v následujících úložištích GitHubu:

* [Akcelerátor řešení vzdáleného monitorování (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Akcelerátor řešení vzdáleného monitorování (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Akcelerátor řešení prediktivní údržby](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Urychlovač řešení připojené továrny](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Jaké sady SDK lze použít k vývoji klientů zařízení pro akcelerátory řešení?

Odkazy na různé jazyky (C, .NET, Java, Node.js, Python) sady SDK zařízení IoT najdete v úložištích GitHub pro microsoft [Azure IoT SDKs.](https://github.com/Azure/azure-iot-sdks)

Pokud používáte zařízení DevKit, najdete prostředky a ukázky v úložišti [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Je nová architektura mikroslužeb k dispozici pro všechny tři akcelerátory řešení?

V současné době pouze řešení vzdáleného monitorování používá architekturu mikroslužeb, protože pokrývá nejširší scénář.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Jaké výhody poskytuje nová architektura založené na mikroslužbách s otevřeným zdrojovým kódem v nové aktualizaci?

Během posledních dvou let se cloudová architektura značně vyvinula. Mikroslužeb se ukázaly jako skvělý vzor pro dosažení škálování a flexibility, aniž by byla obětována rychlost vývoje. Tento architektonický vzor se používá v několika službách společnosti Microsoft interně s velkou spolehlivost í a škálovatelnost výsledků. Společnost Microsoft uvede tyto poznatky do praxe v akcelerátorech řešení, aby z nich zákazníci měli prospěch.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Jsem správce služby a chtěl bych změnit mapování adresářů mezi mým předplatným a konkrétním klientem Azure AD. Jak mohu dokončit tento úkol?

Viz [Přidání existujícího předplatného do adresáře Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Při přihlášení pomocí účtu organizace chci změnit správce služeb nebo spolusprávce

Přečtěte si článek podpory [Změna správce a spolusprávce při přihlášení pomocí účtu organizace](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Proč se zobrazuje tato chyba? "Váš účet nemá správná oprávnění k vytvoření řešení. Obraťte se na správce účtu nebo zkuste jiný účet."

Pokyny naleznete v následujícím diagramu:

![Vývojový diagram oprávnění](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Pokud se chyba zobrazí i po ověření, že jste globálním správcem klienta Azure AD a spolusprávcem předplatného, nechat správce účtu odebrat uživatele a znovu přiřadit potřebná oprávnění v tomto pořadí. Nejprve přidejte uživatele jako globálního správce a pak přidejte uživatele jako spolusprávce předplatného Azure. Pokud problémy přetrvávají, obraťte se [na podporu & podpory .](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Proč se mi tato chyba zobrazuje, když mám předplatné Azure? "K vytvoření předem nakonfigurovaných řešení je potřeba předplatné Azure. Bezplatný zkušební účet si můžete vytvořit během několika minut."

Pokud jste si jisti, že máte předplatné Azure, ověřte mapování klienta pro vaše předplatné a zkontrolujte, že je v rozevíracím programu vybrán správný klient. Pokud jste ověřili, že tenant je správný, postupujte podle předchozího diagramu a ověřte mapování vašeho předplatného a tohoto klienta Azure AD.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Kde najdu informace o předchozí verzi řešení vzdáleného monitorování?

Předchozí verze akcelerátoru řešení vzdáleného monitorování byla známá jako předkonfigurované řešení vzdáleného monitorování sady IoT Suite. Archivoci naleznete na [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/)adrese .

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Je nový akcelerátor řešení k dispozici ve stejné zeměpisné oblasti jako stávající řešení?

Ano, nové vzdálené monitorování je k dispozici ve stejných zeměpisných oblastech.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Jaký je rozdíl mezi odstraněním skupiny prostředků na webu Azure Portal a kliknutím na odstranit na akcelerátoru řešení v azureiotsolutions.com?

* Pokud odstraníte akcelerátor řešení v [azureiotsolutions.com](https://www.azureiotsolutions.com/), odstraníte všechny prostředky, které byly nasazeny při vytváření akcelerátoru řešení. Pokud jste do skupiny prostředků přidali další prostředky, budou tyto prostředky také odstraněny.
* Pokud odstraníte skupinu prostředků na [webu Azure Portal](https://portal.azure.com), odstraníte jenom prostředky v této skupině prostředků. Je také potřeba odstranit aplikaci Azure Active Directory přidruženou k akcelerátoru řešení.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Můžu dál využívat své stávající investice do akcelerátorů řešení Azure IoT?

Ano. Jakékoli řešení, které existuje dnes, pokračuje v práci ve vašem předplatném Azure a zdrojový kód zůstane dostupný na GitHubu.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Kolik instancí služby IoT Hub můžu zřídit v předplatném?

Ve výchozím nastavení můžete zřídit [10 center IoT na jedno předplatné](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits). Můžete vytvořit [lístek podpory Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) zvýšit tento limit. V důsledku toho vzhledem k tomu, že každý akcelerátor řešení zřídí nový IoT Hub, můžete v daném předplatném zřídit až 10 akcelerátorů řešení.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Kolik instancí Azure Cosmos DB můžu zřídit v předplatném?

Padesát. Můžete vytvořit [lístek podpory Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) zvýšit tento limit, ale ve výchozím nastavení můžete pouze zřídit 50 Cosmos DB instance na předplatné.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Kolik bezplatných rozhraní API Map Bing můžu zřídit v jednom předplatném?

Dvě. V rámci předplatného Azure můžete vytvořit jenom dva interní mapy Bing úrovně 1 pro enterprise. Řešení vzdáleného monitorování je ve výchozím nastavení zřízeno s plánem Úroveň interních transakcí 1. V důsledku toho můžete zřídit pouze dvě řešení vzdáleného monitorování v předplatném bez úprav.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Můžu vytvořit akcelerátor řešení, pokud mám Microsoft Azure pro DreamSpark?

> [!NOTE]
> Microsoft Azure pro DreamSpark je teď známý jako Microsoft Imagine pro studenty.

V současné době nelze vytvořit akcelerátor řešení s účtem [Microsoft Azure pro DreamSpark.](https://azure.microsoft.com/pricing/member-offers/imagine/) Můžete však vytvořit [bezplatný zkušební účet pro Azure](https://azure.microsoft.com/free/) během několika minut, který vám umožní vytvořit akcelerátor řešení.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Jak odstraním klienta Azure AD?

Podívejte se na příspěvek blogu Erica [Golpeho Návod k odstranění klienta Azure AD](https://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Další kroky

Můžete si také prostudovat některé další funkce a možnosti akcelerátorů řešení IoT:

* [Seznamte se s možnostmi akcelerátoru řešení vzdáleného monitorování](quickstart-remote-monitoring-deploy.md)
* [Přehled akcelerátorů řešení prediktivní údržby](iot-accelerators-predictive-overview.md)
* [Nasazení akcelerátoru řešení připojené továrny](quickstart-connected-factory-deploy.md)
* [Zabezpečení IoT od počátku](/azure/iot-fundamentals/iot-security-ground-up)
