---
title: Konfigurace aplikace Azure AD Privileged Identity Management | Microsoft Docs
description: Téma, které popisuje, co je Azure AD Privileged Identity Management a jak pomocí PIM zlepšit zabezpečení cloudu.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: protection
ms.topic: overview
ms.date: 03/07/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 9e5039623b1f9b53c636ebaeb1e956d852185577
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952048"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Co je Azure AD Privileged Identity Management?

Pomocí aplikace Azure Active Directory (AD) Privileged Identity Management můžete spravovat, řídit a sledovat přístup v rámci organizace. To zahrnuje přístup k prostředkům v Azure AD, prostředků Azure (Preview) a dalším online službách Microsoftu jako Office 365 nebo Microsoft Intune.

> [!NOTE]
> Když pro svého tenanta povolíte Privileged Identity Management, každý uživatel, který se službou pracuje nebo kterému služba přináší výhody, potřebuje platnou placenou nebo zkušební licenci Azure AD Premium P2 nebo Enterprise Mobility + Security E5. Příkladem můžou být uživatelé a uživatelé ve skupině, kteří:
>
>- Jsou přiřazeni k roli Správce privilegovaných rolí. 
>- Jsou přiřazeni jako způsobilí k ostatním rolím adresáře umožňujícím správu prostřednictvím PIM. 
>- Mají možnost schvalovat a zamítat požadavky v PIM. 
>- Jsou přiřazeni k roli prostředku Azure pomocí přiřazení za běhu nebo přímého (časově omezeného) přiřazení.  
>- Jsou přiřazeni ke kontrole přístupu.
>
>Další informace najdete v článku [Edice služby Azure Active Directory](fundamentals/active-directory-whatis.md).

Organizace chtějí minimalizovat počet lidí, kteří mají přístup k zabezpečeným informacím nebo prostředkům, protože se tím snižuje riziko, že přístup získá uživatel se zlými úmysly nebo že autorizovaný uživatel nevratně ovlivní citlivý prostředek.  Uživatelé však i nadále potřebují provádět privilegované operace v Azure AD, Azure, Office 365 nebo aplikacích SaaS. Organizace můžou uživatelům udělit privilegovaný přístup k prostředkům Azure, jako jsou předplatná a Azure AD. Je potřeba zajistit dohled nad činností těchto uživatelů s oprávněními správce. Azure AD Privileged Identity Management pomáhá zmírnit riziko udělení nadměrných, nepotřebných nebo nesprávně použitých přístupových práv.

Azure AD Privileged Identity Management vaší organizaci pomůže:

- Zjistit, kteří uživatelé mají přiřazené privilegované role pro správu prostředků Azure (Preview) a kteří uživatelé mají přiřazené role pro správu v Azure AD.
- Povolit za běhu a na vyžádání přístup pro správu k online službám Microsoftu jako Office 365 a Intune a k prostředkům Azure (Preview), mezi které patří předplatná, skupiny prostředků a jednotlivé prostředky, jako jsou například virtuální počítače. 
-   Zobrazit historii aktivací správcem, včetně změn, které správci provedli s prostředky Azure (Preview).
- Dostávat upozornění na změny v přiřazeních správců.
- Vyžadovat schválení k aktivaci privilegovaných rolí správců Azure AD (Preview). 
- Kontrolovat členství v rolích pro správu a vyžadovat od uživatelů odůvodnění pokračujícího členství.

V Azure AD může Azure AD Privileged Identity Management spravovat uživatele přiřazené k předdefinovaným organizačním rolím Azure AD, jako je globální správce. V Azure může Azure AD Privileged Identity Management spravovat uživatele a skupiny přiřazené prostřednictvím rolí Azure RBAC, včetně vlastníka a přispěvatele.

## <a name="just-in-time-administrator-access"></a>Přístup správce za běhu

V minulosti jste mohli uživatele přiřadit k roli správce přes web Azure Portal, portály jiných online služeb Microsoftu nebo rutiny Azure AD ve Windows PowerShellu. Takový uživatel se pak stane **trvalým správcem**, který je v přiřazené roli pořád aktivní. Azure AD Privileged Identity Management zavádí koncept **oprávněného správce**. Oprávnění správci by měli být uživatelé, kteří čas od času potřebují privilegovaný přístup, ale ne každý den po celý den. Dokud uživatel nepotřebuje přístup, je tato role neaktivní. Jakmile uživatel bude přístup potřebovat, dokončí proces aktivace a na předem určenou dobu se stane aktivním správcem. Stále více organizací volí tento přístup za účelem omezení nebo eliminace trvalého přístupu správců u privilegovaných rolí.

## <a name="enable-privileged-identity-management-for-your-directory"></a>Povolení aplikace Privileged Identity Management pro adresář

S používáním aplikace Azure AD Privileged Identity Management můžete začít na webu [Azure Portal](https://portal.azure.com/).

> [!NOTE]
> Pokud chcete pro adresář povolit Azure AD Privileged Identity Management, musíte být globální správce s účtem organizace (například @yourdomain.com), a ne s účtem Microsoft (například @outlook.com).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce adresáře.
2. Pokud má vaše organizace víc než jeden adresář, vyberte své uživatelské jméno v pravém horním rohu webu Azure Portal. Vyberte adresář, ve kterém budete Azure AD Privileged Identity Management používat.
3. Vyberte **Všechny služby** a pomocí textového pole Filtr najděte **Azure AD Privileged Identity Management**.
4. Zaškrtněte **Připnout na řídicí panel** a potom klikněte na **Vytvořit**. Aplikace Privileged Identity Management se otevře.

Pokud jste první, kdo bude ve vašem adresáři používat aplikaci Azure AD Privileged Identity Management, a přejdete do rolí adresáře Azure AD, [průvodce zabezpečením](active-directory-privileged-identity-management-security-wizard.md) vás provede počátečním přiřazením. Automaticky se pak stanete prvním **správcem zabezpečení** a **správcem privilegovaných rolí** adresáře.

V případě rolí Azure AD může přiřazení ostatních správců v Azure AD PIM spravovat pouze uživatel, který je v roli správce privilegovaných rolí. [Ostatním uživatelům můžete udělit možnost spravovat role adresáře v PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md). Globální správci, správci zabezpečení a čtenáři zabezpečení můžou v Azure AD PIM zobrazit přiřazení k rolím Azure AD.
V případě rolí Azure RBAC může přiřazení ostatních správců v Azure AD PIM spravovat pouze správce předplatného, vlastník prostředku nebo správce uživatelského přístupu k prostředku.  Uživatelé, kteří jsou správci privilegovaných rolí, správci zabezpečení nebo čtenáři zabezpečení, nemají ve výchozím nastavení přístup k zobrazení přiřazení k rolím Azure RBAC v Azure AD PIM.

## <a name="privileged-identity-management-overview-entry-point"></a>Přehled aplikace Privileged Identity Management (vstupní bod)

Azure AD Privileged Identity Management podporuje správu rolí adresáře Azure AD a rolí pro prostředky Azure (Preview). Funkce rolí u prostředků Azure se od rolí pro správu v Azure AD liší. Role prostředků Azure poskytují různě odstupňovaná oprávnění k prostředku, ke kterému jsou přiřazené, a všem podřízeným prostředkům v hierarchii prostředků (označuje se jako dědičnost). [Další informace o RBAC, hierarchii prostředků a dědičnosti](../role-based-access-control/role-assignments-portal.md) PIM pro role adresáře Azure AD i prostředky Azure (Preview) můžete spravovat tak, že přejdete na odpovídající odkaz v levé navigační nabídce v části Správa vstupního bodu Přehled PIM.

PIM v části Úlohy v levé navigační nabídce poskytuje pohodlný přístup k aktivaci rolí, zobrazení aktivací a žádostí čekajících na vyřízení, schválení čekajících na vyřízení (v případě rolí adresáře Azure AD) a kontrolám čekajícím na vaši odpověď.

Když ze vstupního bodu Přehled přejdete na některou z položek nabídky Úlohy, bude výsledné zobrazení obsahovat výsledky pro role adresáře Azure AD i role prostředků Azure (Preview).

![Rychlý start](./media/active-directory-privileged-identity-management-configure/quick-start.png)

Část Moje role obsahuje seznam aktivních a oprávněných přiřazení rolí pro role adresáře Azure AD a role prostředků Azure (Preview). [Další informace o aktivaci oprávněných přiřazení rolí](active-directory-privileged-identity-management-how-to-activate-role.md)

Aktivace rolí pro prostředky Azure (Preview) zavádí nové prostředí, které umožňuje oprávněným členům role naplánovat aktivaci na budoucí datum a čas a vybrat konkrétní dobu trvání aktivace až do maximální doby povolené správci.

![](./media/active-directory-privileged-identity-management-configure/activations.png)

V případě, že už naplánovaná aktivace není potřeba, může uživatel svou žádost čekající na vyřízení zrušit tím, že v levé navigační nabídce přejde k čekajícím žádostem a klikne na tlačítko Zrušit na řádku příslušné žádosti.

![Žádosti čekající na vyřízení](./media/active-directory-privileged-identity-management-configure/pending-requests.png)

## <a name="privileged-identity-management-admin-dashboard"></a>Řídicí panel pro správu aplikace Privileged Identity Management

Privileged Identity Management nabízí řídicí panel pro správu, který poskytuje důležité informace, například:

* Upozornění na příležitosti ke zlepšení zabezpečení
* Počet uživatelů přiřazených k jednotlivým privilegovaným rolím  
* Počet oprávněných a trvalých správců
* Graf aktivací privilegovaných rolí ve vašem adresáři
*   Počet přiřazení za běhu, časově omezených a trvalých přiřazení pro role prostředků Azure (Preview)
*   Uživatelé a skupiny s novými přiřazeními rolí za posledních 30 dnů (role prostředků Azure)


![Řídicí panel PIM – snímek obrazovky](./media/active-directory-privileged-identity-management-configure/PIM_Admin_Overview.png)

## <a name="privileged-role-management"></a>Správa privilegovaných rolí

V případě rolí adresáře Azure AD můžete pomocí aplikace Azure AD Privileged Identity Management spravovat správce prostřednictvím přidávání nebo odebírání trvalých nebo oprávněných správců k jednotlivým rolím. Pomocí PIM pro prostředky Azure (Preview) můžou vlastníci, správci uživatelského přístupu a globální správci, kteří v tenantovi povolují správu předplatných, přiřazovat uživatele nebo skupiny k rolím prostředků Azure s oprávněným přístupem (přístup za běhu), časově omezeným přístupem (nevyžaduje se aktivace) s počátečním a koncovým datem a časem nebo trvalým přístupem (pokud je povolený v nastavení role).

![Přidávání a odebírání správců v PIM – snímek obrazovky](./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png)

## <a name="configure-the-role-activation-settings"></a>Konfigurace nastavení aktivace role

Prostřednictvím [nastavení role](active-directory-privileged-identity-management-how-to-change-default-settings.md) můžete nakonfigurovat vlastnosti aktivace oprávněné role pro role adresáře Azure AD, včetně následujících:

* Doba trvání období aktivace role
* Oznámení o aktivaci role
* Informace, které uživatel musí zadat během procesu aktivace role
* Lístek služby nebo číslo incidentu
* [Požadavky na pracovní postup schválení – Preview](./privileged-identity-management/azure-ad-pim-approval-workflow.md)

![Nastavení PIM – aktivace správce – snímek obrazovky](./media/active-directory-privileged-identity-management-configure/PIM_Settings_w_Approval_Disabled.png)

Na obrázku si všimněte neaktivních tlačítek pro službu **Multi-Factor Authentication**. U určitých vysoce privilegovaných rolí vyžadujeme kvůli zvýšené ochraně službu MFA.

Nastavení role pro role prostředků Azure (Preview) umožňuje správcům konfigurovat nastavení přiřazení za běhu a přímého přiřazení, včetně následujících:

- Možnost přiřadit uživatele nebo skupiny k rolím bez koncového data a času (trvalé přiřazení)
- Výchozí doba trvání přiřazení (pokud není trvalé)
- Maximální doba trvání aktivace (pokud tuto možnost oprávněný člen role aktivuje)
- Informace, které uživatel musí zadat během procesu aktivace role (přiřazení za běhu) nebo přiřazení (přímá přiřazení)

![](./media/active-directory-privileged-identity-management-configure/role-settings-details.png)

## <a name="role-activation"></a>Aktivace role

Oprávněný správce [aktivuje roli](active-directory-privileged-identity-management-how-to-activate-role.md) tak, že požádá o časově omezenou aktivaci role. O aktivaci je možné požádat pomocí možnosti **Aktivovat moji roli** v aplikaci Azure AD Privileged Identity Management.

Správce, který chce aktivovat roli, musí na webu Azure Portal inicializovat aplikaci Azure AD Privileged Identity Management.

Aktivaci role je možné upravit. V nastavení PIM můžete určit délku aktivace a informace, které pro aktivaci role musí správce zadat.

![Žádost správce o aktivaci role v PIM – snímek obrazovky](./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png)

## <a name="review-role-activity"></a>Kontrola aktivity role

Existují dva způsoby, jak můžete sledovat, jak vaši zaměstnanci a správci používají privilegované role. První možností je použít [historii auditu rolí adresáře](active-directory-privileged-identity-management-how-to-use-audit-log.md). Protokoly historie auditu sledují změny přiřazení privilegovaných rolí, historii aktivací rolí a změny nastavení pro role prostředků Azure (Preview). 

![Historie aktivací v PIM – snímek obrazovky](./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png)

Druhou možností je nastavit pravidelné [kontroly přístupu](active-directory-privileged-identity-management-how-to-start-security-review.md). Tyto kontroly přístupu může provádět přiřazený revidující (například vedoucí týmu) nebo je můžou provádět sami zaměstnanci. Toto je nejlepší způsob, jak monitorovat, kdo stále požaduje přístup, a kdo už ne.

## <a name="azure-ad-pim-at-subscription-expiration"></a>Azure AD PIM při vypršení platnosti předplatného

Před použitím Azure AD PIM musí mít tenant zkušební nebo placené předplatné Azure AD Premium P2 (nebo EMS E5).  Správci tenanta navíc musí mít přiřazené licence.  Konkrétně musí mít licence přiřazené správci v rolích Azure AD spravovaných přes Azure AD PIM, správci v rolích Azure RBAC spravovaných přes Azure AD PIM a všichni uživatelé bez oprávnění správce, kteří provádějí kontroly přístupu.
Pokud vaše organizace předplatné Azure AD Premium P2 neprodlouží nebo vyprší platnost vaší zkušební verze, funkce Azure AD PIM přestanou být ve vašem tenantovi dostupné, oprávněná přiřazení rolí se odeberou a uživatelé ztratí možnost aktivovat role. Další informace najdete v tématu [Požadavky na předplatné Azure AD PIM](./privileged-identity-management/subscription-requirements.md).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
