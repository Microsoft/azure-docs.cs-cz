---
title: Konfigurovat Azure AD Privileged Identity Management | Microsoft Docs
description: Téma, které vysvětluje, co je Azure AD Privileged Identity managementu a jak používat PIM k zlepšují zabezpečení vašeho cloudu.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 03/07/2018
ms.author: curtand
ms.custom: pim
ms.openlocfilehash: b8df65c969a37eb22589c297ecb9b0d0b840a52c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Co je Azure AD Privileged Identity Management?

Pomocí aplikace Azure Active Directory (AD) Privileged Identity Management můžete spravovat, řídit a sledovat přístup v rámci organizace. Sem patří přístup k prostředkům ve službě Azure AD, prostředky Azure (Preview), a dalších služeb Microsoft Online Services, jako jsou Office 365 nebo Microsoft Intune.

> [!NOTE]
> Když povolíte Privileged Identity Management pro tenanta, platný P2 Azure AD Premium nebo Enterprise Mobility + Security E5 placené nebo zkušební licence se vyžaduje pro každého uživatele, který komunikuje se službou nebo dávku obdrží od služby. Mezi příklady patří uživatelé/v > skupinu, která jsou:
>
>- Přiřazeny k roli správce privilegovaných rolí 
>- Přiřadit jako vhodné pro jiné role directory spravovat pomocí PIM 
>- Možnost schválit nebo zamítnout požadavky v PIM 
>- Přiřazení k roli prostředků Azure s pouze v čas nebo přímé přiřazení (na základě čas)  
>- Přiřazené kontrola přístupu
>
>Další informace najdete v článku [Edice služby Azure Active Directory](active-directory-editions.md).

Organizace chcete minimalizovat počet lidí, kteří mají přístup zabezpečit informace nebo prostředky, protože, sníží se tím možnost uživatel se zlými úmysly získávání tento přístup nebo oprávněný uživatel nechtěně, které mají vliv citlivých prostředků.  Ale uživatelé stále muset provádět privilegované operace ve službě Azure AD, Azure, Office 365 nebo SaaS aplikace. Organizace může poskytnout privilegovaného přístupu uživatelů k prostředků Azure stejně jako odběry a Azure AD. Je třeba pro dohledu pro tyto činnosti uživatelů s jejich oprávněními správce. Azure AD Privileged Identity Management pomáhá zmírnit riziko nadměrné, nepotřebné nebo došlo ke zneužití přístupová práva.

Azure AD Privileged Identity Management pomáhá vaší organizaci:

- Zjistit, kteří uživatelé jsou přiřazeny privilegovaných rolí ke správě prostředků Azure (Preview), a kteří uživatelé budou přiřazení rolí správce ve službě Azure AD
- Povolit "právě v čase" pro správu přístup k Microsoft Online Services, jako je Office 365 a Intune a k prostředků Azure (Preview) odběry, skupiny prostředků a jednotlivé prostředky, jako jsou virtuální počítače na vyžádání 
-   Zobrazit historii správce aktivace, včetně správci změny provedené prostředky Azure (Preview)
- Dostávat upozornění na změny v přiřazení správců
- Vyžadovat schválení k aktivaci rolí správce Azure AD privilegovaný (Preview) 
- Zkontrolujte členství v rolích pro správu a vyžaduje, aby uživatel uveďte její odůvodnění pro nepřetržitou členství

Ve službě Azure AD, Azure AD Privileged Identity Management můžete spravovat uživatele přiřazené do vestavěné organizační role Azure AD, jako je například globálního správce. V Azure Azure AD Privileged Identity Management spravovat uživatele a skupiny přiřazené prostřednictvím role Azure RBAC, včetně vlastníka nebo přispěvatele.

## <a name="just-in-time-administrator-access"></a>Právě v přístup správce čas

V minulosti může přiřadit uživatele k roli správce prostřednictvím portálu Azure, dalších portálech služeb Microsoft Online Services nebo rutiny služby Azure AD v prostředí Windows PowerShell. V důsledku toho stane **trvalé správce**, vždy aktivní v přiřazenou roli. Azure AD Privileged Identity Management zavádí koncepci **oprávněné správce**. Oprávněné admins by měl být, že uživatelům, kteří potřebují privilegovaný přístup nyní nebo později, ale není celodenní každý den. Role je neaktivní, dokud uživatel potřebuje přístup, pak se dokončit proces aktivace a stane aktivní správce po předem určenou dobu. Více organizace se rozhodnete tuto metodu použijte pro zmenšení nebo odstranění "stojící správce přístup" privilegované role.

## <a name="enable-privileged-identity-management-for-your-directory"></a>Povolit Privileged Identity Management pro svůj adresář

Můžete začít používat Azure AD Privileged Identity Management [portál Azure](https://portal.azure.com/).

> [!NOTE]
> Musí být globální správce s účtem organizace (například @yourdomain.com), nikoli účet Microsoft (například @outlook.com), aby pro adresář Azure AD Privileged Identity Management.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce adresáře.
2. Pokud má vaše organizace víc než jeden adresář, vyberte své uživatelské jméno v pravém horním rohu webu Azure Portal. Vyberte adresář, kde budete používat Azure AD Privileged Identity Management.
3. Vyberte **Všechny služby** a pomocí textového pole Filtr najděte **Azure AD Privileged Identity Management**.
4. Zaškrtněte **Připnout na řídicí panel** a potom klikněte na **Vytvořit**. Aplikace Privileged Identity Management se otevře.

Pokud jste první, kdo použít Azure AD Privileged Identity Management ve vašem adresáři a přejděte do role adresář Azure AD a přejděte do role adresář Azure AD, [Průvodce zabezpečení](active-directory-privileged-identity-management-security-wizard.md) vás provede počáteční přiřazení prostředí. Následně se automaticky stane první **správce zabezpečení** a **správce privilegovaných rolí** adresáře.

Pro role se službou Azure AD můžete spravovat jenom uživatel, který je v roli správce privilegovaných rolí přiřazení pro ostatní správce v Azure AD PIM. Můžete [ostatním uživatelům předat, schopnost spravovat adresář role v PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md). Globální správci, správce zabezpečení a zabezpečení čtečky můžete zobrazit přiřazení role se službou Azure AD v Azure AD PIM.
Pro role Azure RBAC můžete spravovat pouze správce předplatného, vlastník prostředku nebo správce přístupu uživatelů prostředků přiřazení pro ostatní správce v Azure AD PIM.  Uživatelé, kteří jsou správci privilegované Role, správce zabezpečení nebo čtečky zabezpečení není ve výchozím nastavení mají přístup k zobrazení přiřazení rolí Azure RBAC v Azure AD PIM.

## <a name="privileged-identity-management-overview-entry-point"></a>Správa privilegovaných identit (vstupního bodu) – přehled

Azure AD Privileged Identity Management podporuje správu rolí adresáře Azure AD a rolí pro prostředky Azure (Preview). Funkce rolí pro prostředky Azure se liší od správní role ve službě Azure AD. Role prostředků Azure poskytují podrobné oprávnění k prostředku, na které jsou přiřazeny a všechny podřízené prostředky v hierarchii prostředků (označované jako dědičnosti). [Další informace o RBAC, hierarchie prostředků a dědičnost](../role-based-access-control/role-assignments-portal.md). PIM pro obě role adresář Azure AD a prostředky Azure (Preview) můžete spravovat přístup k na příslušný odkaz v části Správa levé navigační nabídce PIM přehled vstupní bod.

PIM poskytuje pohodlné přístup k aktivaci rolí, zobrazení čekajících aktivací nebo požadavků, čeká na schválení (pro role adresář Azure AD) a čeká na vaši odpověď z části úlohy levé navigační nabídce na.

Při přístupu k některou z položek nabídky úlohy ze vstupního bodu přehled, výsledné zobrazení obsahuje výsledky pro role adresář Azure AD i role prostředků Azure (Preview).

![Rychlý start](./media/active-directory-privileged-identity-management-configure/quick-start.png)

Moje rolí obsahuje seznam přiřazení role active a vhodné role adresář Azure AD, a rolí prostředků Azure (Preview). [Další informace o aktivaci role vhodné přiřazení](active-directory-privileged-identity-management-how-to-activate-role.md).

Aktivace role pro prostředky Azure (Preview) přináší nové prostředí, které umožňuje vhodné členy role k plánování aktivace pro budoucí datum a čas a vyberte dobu trvání konkrétní aktivace v rámci maximální povolenou správci.

![](./media/active-directory-privileged-identity-management-configure/activations.png)

V případě, že naplánované aktivace se už nevyžaduje, uživatelé mohou zrušit jejich nevyřízené žádosti přechodem do nabídky žádosti čekající na vyřízení v levé navigační nabídce a kliknutím na tlačítko Storno tlačítko v řádku s tímto požadavkem.

![Žádosti čekající na vyřízení](./media/active-directory-privileged-identity-management-configure/pending-requests.png)

## <a name="privileged-identity-management-admin-dashboard"></a>Privilegované řídicí panel Správce Identity Management

Azure AD Privileged Identity Manager poskytuje správce řídicího panelu, který poskytuje důležité informace, jako:

* Výstrahy, které odkazují na příležitosti pro zlepšení zabezpečení
* Počet uživatelů, kteří jsou přiřazeni k každé privilegované role  
* Počet oprávněných a trvalých správců
* Graf aktivací privilegované role ve vašem adresáři
*   Počet těsně za běhu, časově vázaných a trvalé přiřazení rolí prostředků Azure (Preview)
*   Uživatelé a skupiny s nové přiřazení role v posledních 30 dní (role prostředků Azure)


![Řídicí panel PIM – snímek obrazovky][2]

## <a name="privileged-role-management"></a>Správa privilegovaných rolí

S Azure AD Privileged Identity Management můžete spravovat správce přidáním nebo odebráním trvalé nebo oprávněné správce ke každé roli pro role adresář Azure AD. S PIM pro prostředky Azure (Preview) vlastníky, správci přístupu uživatele a globální správci, které umožňují správu předplatných v jejich klienta lze přiřadit uživatele nebo skupiny prostředků Azure role jako způsobilých (přístup pouze za běhu), nebo časově vázaných ( není vyžadována aktivace) přístup s počáteční a koncové datum a čas nebo trvalé (Pokud je povoleno v nastavení role).

![Správci přidat nebo odebrat PIM – snímek obrazovky][3]

## <a name="configure-the-role-activation-settings"></a>Konfigurace nastavení aktivace role

Pomocí [nastavení role](active-directory-privileged-identity-management-how-to-change-default-settings.md) můžete konfigurovat vlastnosti aktivace role vhodné pro role adresář Azure AD, včetně:

* Doba trvání v období aktivace role
* Oznámení o aktivaci role
* Informace, které uživatel musí poskytnout během procesu aktivace role
* Číslo služby lístků nebo incidenty.
* [Požadavky na pracovní postup schválení – náhled](./privileged-identity-management/azure-ad-pim-approval-workflow.md)

![Snímek obrazovky nastavení – správce aktivace – PIM][4]

Všimněte si, že se na obrázku tlačítka pro **Multi-Factor Authentication** jsou zakázány. Pro některé, vysoce privilegované role, jsme vícefaktorové ověřování vyžadovat pro zvýšenou ochranu.

Nastavení role pro role prostředků Azure (Preview) umožňuje správcům nakonfigurovat pouze za běhu a přímé přiřazení nastavení, včetně:

- Schopnost přidělit skupin uživatelů nebo rolí bez datum/čas ukončení (trvalého přiřazení)
- Výchozí doba přiřazení (když není trvalé)
- Doba trvání maximální aktivace (když člena vhodné role aktivuje)
- Informace, uživatel musí poskytnout při aktivaci role (pouze v době přiřazení) nebo procesu přiřazení (přímé přiřazení)

![](./media/active-directory-privileged-identity-management-configure/role-settings-details.png)

## <a name="role-activation"></a>Aktivace role

K [aktivovat roli](active-directory-privileged-identity-management-how-to-activate-role.md), oprávněné správce požadavky časově vázaných "aktivace" pro roli. Aktivace může být požadováno pomocí **aktivovat Moje role** možnost v Azure AD Privileged Identity Management.

Kdo chce aktivovat roli správce musí inicializovat Azure AD Privileged Identity Management na portálu Azure.

Aktivaci role je přizpůsobitelná. V nastavení PIM, můžete zjistit délku aktivace a co informace nutné poskytnout při aktivaci role správce.

![Aktivace role PIM správce žádost – snímek obrazovky][5]

## <a name="review-role-activity"></a>Kontrolní aktivita role

Existují dva způsoby, jak sledovat, jak zaměstnancům a správci používají privilegované role. První možností je použití [historie auditu role Directory](active-directory-privileged-identity-management-how-to-use-audit-log.md). Historie auditu protokoly sledovat změny v přiřazení privilegované role, historie aktivace role, a a změny nastavení rolí prostředků Azure (Preview). 

![Historie aktivace PIM – snímek obrazovky][6]

Druhou možností je nastavit regular [přístup recenze](active-directory-privileged-identity-management-how-to-start-security-review.md). Tyto recenze přístup může být provádí a přiřazení kontrolor (jako je team manager) nebo zaměstnanci můžete zkontrolovat sami. Toto je nejlepší způsob, jak monitorovat kdo stále vyžaduje přístup a kteří už nepodporuje.

## <a name="azure-ad-pim-at-subscription-expiration"></a>Azure AD PIM na vypršení platnosti odběru

Klient musí mít Azure AD Premium P2 (nebo EMS E5) zkušební nebo placené předplatné v jejich klientovi před použitím Azure AD PIM.  Licence musí být navíc přiřazovány Správci klienta.  Konkrétně musí být přiřazená licence k správci ve službě Azure AD role spravované přes Azure AD PIM, správci v Azure RBAC role spravovat prostřednictvím Azure AD PIM a uživatelé bez oprávnění správce, kteří provést recenze přístup.
Pokud vaše organizace neobnovuje Azure AD Premium P2 nebo vypršení zkušební doby, funkce Azure AD PIM už nebude k dispozici ve vašem klientovi, přiřazení oprávněné rolí budou odebrány a uživatelé nebudou moci uživatel aktivovat role. Další informace v [požadavků předplatné Azure AD PIM](./privileged-identity-management/subscription-requirements.md)

## <a name="next-steps"></a>Další postup

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Admin_Overview.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_Settings_w_Approval_Disabled.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png
