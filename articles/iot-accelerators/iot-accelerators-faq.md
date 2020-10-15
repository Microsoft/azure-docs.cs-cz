---
title: Nejčastější dotazy k akcelerátorům řešení IoT – Azure | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy k akcelerátorům řešení IoT. Obsahuje odkazy na úložiště GitHub.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 40c1fb314a2dea653740e7f87b5caa14c759fffb
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078217"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Nejčastější dotazy k akcelerátorům řešení IoT

Přečtěte si také nejčastější dotazy [k připojeným objektům pro konkrétní továrnu](iot-accelerators-faq-cf.md) a [vzdálené monitorování](iot-accelerators-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Kde můžu najít zdrojový kód pro akcelerátory řešení?

Zdrojový kód je uložený v následujících úložištích GitHub:

* [Akcelerátor řešení vzdáleného monitorování (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Akcelerátor řešení vzdáleného monitorování (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Akcelerátor řešení prediktivní údržby](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Akcelerátor řešení propojené továrny](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Jaké sady SDK můžu použít k vývoji klientů zařízení pro akcelerátory řešení?

Odkazy na sady SDK pro zařízení IoT v různých jazycích (C, .NET, Java, Node.js, Python) najdete v úložištích [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) GitHub.

Pokud používáte zařízení DevKit, můžete najít materiály a ukázky v úložišti GitHub [sady IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) .

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Je k dispozici nová architektura mikroslužeb pro všechny tři akcelerátory řešení?

V současné době pouze řešení vzdáleného monitorování využívá architekturu mikroslužeb, protože se vztahuje na nejširší scénář.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Jaké výhody přináší nová otevřená architektura pro mikroslužby založené na mikroslužbách v nové aktualizaci?

Za poslední dva roky se cloudová architektura významně vyvinula. Mikroslužby se vyvíjejí jako skvělý vzor pro zajištění škálování a flexibility, aniž by došlo k omezení vývoje. Tento model architektury se používá v několika službách Microsoftu interně s velkou spolehlivostí a škálovatelností výsledků. Microsoft vkládá tyto učení do praxe v akcelerátorech řešení, aby na ně zákazníci využili výhody.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Jsem správce služeb a chci změnit mapování adresáře mezi předplatným a konkrétním klientem služby Azure AD. Návody dokončit tuto úlohu?

Další informace najdete v tématu [Přidání existujícího předplatného do adresáře služby Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory) .

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Chci změnit správce služby nebo Co-Administrator, když se přihlásíte pomocí účtu organizace.

[Pokud se přihlásíte pomocí účtu organizace](https://azure.microsoft.com/support/changing-service-admin-and-co-admin), přečtěte si článek o podpoře – Změna správce služby a Co-Administrator.

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Proč se mi zobrazuje tato chyba? "Váš účet nemá správná oprávnění k vytvoření řešení. Obraťte se na správce účtu nebo zkuste použít jiný účet. "

Pokyny najdete v následujícím diagramu:

![Vývojový diagram oprávnění](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Pokud se zobrazí chyba i po ověření, že jste globálním správcem tenanta Azure AD a spolusprávcem předplatného, požádejte správce účtu, aby tento uživatel odebral a znovu přidělil potřebná oprávnění v tomto pořadí. Nejdřív přidejte uživatele jako globálního správce a pak přidejte uživatele jako spolusprávce předplatného Azure. Pokud potíže potrvají, obraťte se na [pomoc s &](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Proč se mi tato chyba zobrazuje i v případě, že mám předplatné Azure? "Předplatné Azure se vyžaduje k vytváření předem nakonfigurovaných řešení. Během několika minut můžete vytvořit bezplatný zkušební účet. "

Pokud jste si jisti, že máte předplatné Azure, ověřte mapování tenanta pro vaše předplatné a zkontrolujte, jestli je v rozevíracím seznamu vybraný správný tenant. Pokud jste ověřili, že je tenant správný, postupujte podle předchozího diagramu a ověřte mapování předplatného a tohoto tenanta Azure AD.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Kde najdu informace o předchozí verzi řešení vzdáleného monitorování?

Předchozí verze akcelerátoru řešení vzdáleného monitorování byla známá jako předkonfigurované řešení vzdáleného monitorování IoT Suite. Archivovanou dokumentaci najdete na adrese [https://docs.microsoft.com/previous-versions/azure/iot-suite/](/previous-versions/azure/iot-suite/) .

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Je nový akcelerátor řešení dostupný ve stejné geografické oblasti jako stávající řešení?

Ano, nové vzdálené monitorování je k dispozici ve stejných geografických oblastech.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Jaký je rozdíl mezi odstraněním skupiny prostředků v Azure Portal a kliknutím na Odstranit v akcelerátoru řešení v azureiotsolutions.com?

* Pokud odstraníte akcelerátor řešení v [azureiotsolutions.com](https://www.azureiotsolutions.com/), odstraníte všechny prostředky, které byly nasazeny při vytváření akcelerátoru řešení. Pokud jste do skupiny prostředků přidali další prostředky, odstraní se i tyto prostředky.
* Pokud odstraníte skupinu prostředků v [Azure Portal](https://portal.azure.com), odstraníte jenom prostředky v této skupině prostředků. Také je nutné odstranit aplikaci Azure Active Directory přidruženou k akcelerátoru řešení.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Můžu dál využívat svoje stávající investice do akcelerátorů řešení Azure IoT?

Yes. Jakékoli řešení, které dnes existuje, bude v předplatném Azure fungovat a zdrojový kód zůstane dostupný na GitHubu.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Kolik instancí IoT Hub můžu zřídit v předplatném?

Ve výchozím nastavení můžete zřídit [10 Center IoT na jedno předplatné](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits). K vyvolání tohoto limitu můžete vytvořit [lístek podpory Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) . Vzhledem k tomu, že každé akcelerátor řešení zřizuje nové IoT Hub, můžete v daném předplatném zřídit jenom 10 akcelerátorů řešení.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Kolik instancí Azure Cosmos DB můžu zřídit v předplatném?

50. Můžete vytvořit [lístek podpory Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro zvýšení tohoto limitu, ale ve výchozím nastavení můžete zřídit jenom 50 Cosmos DB instancí na předplatné.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Kolik bezplatných rozhraní API Map Bing můžu zřídit v jednom předplatném?

Dvě. V rámci předplatného Azure můžete vytvořit pouze dvě interní transakce úrovně 1 mapy Bing pro podnikové plány. Řešení vzdáleného monitorování je ve výchozím nastavení zřízené s plánem interních transakcí úrovně 1. V důsledku toho můžete v předplatném zřídit jenom dvě řešení vzdáleného monitorování bez jakýchkoli úprav.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Můžu vytvořit akcelerátor řešení, pokud mám Microsoft Azure pro DreamSpark?

> [!NOTE]
> Microsoft Azure pro DreamSpark se nyní říká Microsoft Imagine pro studenty.

V současné době nemůžete vytvořit akcelerátor řešení s [Microsoft Azure pro účet DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) . Můžete si ale během několika minut vytvořit [bezplatný zkušební účet pro Azure](https://azure.microsoft.com/free/) , který vám umožní vytvořit akcelerátor řešení.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Návody odstranit tenanta Azure AD?

[Postup odstranění tenanta Azure AD](/archive/blogs/ericgolpe/walkthrough-of-deleting-an-azure-ad-tenant)najdete v příspěvku na blogu pro Eric Golpe.

### <a name="next-steps"></a>Další kroky

Můžete si také prostudovat některé další funkce a možnosti akcelerátorů řešení IoT:

* [Prozkoumejte možnosti akcelerátoru řešení vzdáleného monitorování](quickstart-remote-monitoring-deploy.md)
* [Přehled akcelerátoru řešení prediktivní údržby](./iot-accelerators-predictive-walkthrough.md)
* [Nasadit akcelerátor řešení propojené továrny](quickstart-connected-factory-deploy.md)
* [Zabezpečení IoT od počátku](../iot-fundamentals/iot-security-ground-up.md)