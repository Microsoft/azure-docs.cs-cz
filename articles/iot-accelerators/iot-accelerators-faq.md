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
ms.openlocfilehash: 61993df77b0831926f16339a741a2553e80c2a0d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713835"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Nejčastější dotazy k akcelerátorům řešení IoT

Přečtěte si také [Nejčastější dotazy k připojeným objektům pro konkrétní továrny](iot-accelerators-faq-cf.md).

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Kde můžu najít zdrojový kód pro akcelerátory řešení?

Zdrojový kód je uložený v následujících úložištích GitHub:

* [Akcelerátor řešení propojené továrny](https://github.com/Azure/azure-iot-connected-factory)
* [Akcelerátor řešení pro simulaci zařízení](https://github.com/Azure/azure-iot-pcs-device-simulation)

### <a name="where-can-i-find-the-remote-monitoring-and-predictive-maintenance-solution-accelerators"></a>Kde najdu akcelerátory řešení pro vzdálené monitorování a prediktivní údržbu?

Od 10. prosince 2020 byly odebrány akcelerátory vzdáleného monitorování a prediktivní údržby z webu [akcelerátorů řešení Azure IoT](https://www.azureiotsolutions.com/Accelerators) a již nejsou k dispozici pro nová nasazení. Úložiště GitHubu pro oba akcelerátory byly archivovány. Kód je stále k dispozici pro kohokoli, kdo má přístup, ale v úložištích nejsou žádné nové příspěvky.

### <a name="what-happens-to-my-existing-remote-monitoring-and-predictive-maintenance-deployments"></a>Co se stane s existujícím nasazením vzdáleného monitorování a prediktivní údržby?

Stávající nasazení nejsou ovlivněná odebráním akcelerátorů řešení vzdáleného monitorování a prediktivní údržby a budou fungovat i nadále. Zároveň nejsou ovlivněna rozvětvená úložiště. Hlavní úložiště v GitHubu byla archivována.

### <a name="how-do-i-deploy-device-simulation-solution-accelerator"></a>Návody nasadit akcelerátor řešení pro simulaci zařízení?

Pokud chcete nasadit akcelerátor řešení pro simulaci zařízení, přečtěte si téma úložiště GitHub pro [simulaci zařízení](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) .

### <a name="where-can-i-find-information-about-the-removed-solution-accelerators"></a>Kde najdu informace o odebraných akcelerátorech řešení?

Podívejte se na následující stránky na webu předchozí verze:

* [Vzdálené monitorování](/previous-versions/azure/iot-accelerators/about-iot-accelerators)
* [Prediktivní údržba](/previous-versions/azure/iot-accelerators/about-iot-accelerators)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Jaké sady SDK můžu použít k vývoji klientů zařízení pro akcelerátory řešení?

Odkazy na sady SDK pro zařízení IoT v různých jazycích (C, .NET, Java, Node.js, Python) najdete v úložištích [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) GitHub.

Pokud používáte zařízení DevKit, můžete najít materiály a ukázky v úložišti GitHub [sady IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) .

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

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Jaký je rozdíl mezi odstraněním skupiny prostředků v Azure Portal a kliknutím na Odstranit v akcelerátoru řešení v azureiotsolutions.com?

* Pokud odstraníte akcelerátor řešení v [azureiotsolutions.com](https://www.azureiotsolutions.com/), odstraníte všechny prostředky, které byly nasazeny při vytváření akcelerátoru řešení. Pokud jste do skupiny prostředků přidali další prostředky, odstraní se i tyto prostředky.
* Pokud odstraníte skupinu prostředků v [Azure Portal](https://portal.azure.com), odstraníte jenom prostředky v této skupině prostředků. Také je nutné odstranit aplikaci Azure Active Directory přidruženou k akcelerátoru řešení.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Můžu dál využívat svoje stávající investice do akcelerátorů řešení Azure IoT?

Ano. Jakékoli řešení, které dnes existuje, bude v předplatném Azure fungovat a zdrojový kód zůstane dostupný na GitHubu.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Kolik instancí IoT Hub můžu zřídit v předplatném?

Ve výchozím nastavení můžete zřídit [10 Center IoT na jedno předplatné](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits). K vyvolání tohoto limitu můžete vytvořit [lístek podpory Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) . Vzhledem k tomu, že každé akcelerátor řešení zřizuje nové IoT Hub, můžete v daném předplatném zřídit jenom 10 akcelerátorů řešení.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Kolik instancí Azure Cosmos DB můžu zřídit v předplatném?

50. Můžete vytvořit [lístek podpory Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro zvýšení tohoto limitu, ale ve výchozím nastavení můžete zřídit jenom 50 Cosmos DB instancí na předplatné.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Můžu vytvořit akcelerátor řešení, pokud mám Microsoft Azure pro DreamSpark?

> [!NOTE]
> Microsoft Azure pro DreamSpark se nyní říká Microsoft Imagine pro studenty.

V současné době nemůžete vytvořit akcelerátor řešení s [Microsoft Azure pro účet DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) . Můžete si ale během několika minut vytvořit [bezplatný zkušební účet pro Azure](https://azure.microsoft.com/free/) , který vám umožní vytvořit akcelerátor řešení.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Návody odstranit tenanta Azure AD?

[Postup odstranění tenanta Azure AD](/archive/blogs/ericgolpe/walkthrough-of-deleting-an-azure-ad-tenant)najdete v příspěvku na blogu pro Eric Golpe.

### <a name="next-steps"></a>Další kroky

Můžete si také prostudovat některé další funkce a možnosti akcelerátorů řešení IoT:

* [Nasadit akcelerátor řešení propojené továrny](quickstart-connected-factory-deploy.md)
* [Zabezpečení IoT od počátku](../iot-fundamentals/iot-security-ground-up.md)