---
title: Přechod na upravenou spolupráci s Azure Active Directory spolupráce B2B
description: Přejděte k řízení spolupráce s využitím spolupráce Azure AD B2B.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96a671d44133a6f508cc5026e06fc6a56b9e6f20
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/23/2020
ms.locfileid: "97743919"
---
# <a name="transition-to-governed-collaboration-with-azure-active-directory-b2b-collaboration"></a>Přechod na upravenou spolupráci s Azure Active Directory spolupráce B2B 

Získání vaší spolupráce pod kontrolou je klíč pro zabezpečení externího přístupu k vašim prostředkům. Než budete pokračovat v tomto článku, ujistěte se, že máte následující:

* [Bylo zjištěno, že vaše stav zabezpečení](1-secure-access-posture.md)

* [Zjištění aktuálního stavu](2-secure-access-current-state.md)

* [Vytvořili jste plán zabezpečení](3-secure-access-plan.md)

* [Pochopení způsobu spolupráce skupin a zabezpečení](4-secure-access-groups.md)

Až tyto věci dokončíte, budete připraveni přejít na kontrolované spolupráce. Tento článek vám pomůže přesunout veškerou externí spolupráci do [spolupráce Azure Active Directory B2B](../external-identities/what-is-b2b.md) (Azure AD B2B). Azure AD B2B je funkce [externích identit Azure AD](../external-identities/compare-with-b2c.md).

## <a name="control-who-your-organization-collaborates-with"></a>Řízení, s kým vaše organizace spolupracuje

Musíte rozhodnout, jestli chcete omezit, které organizace můžou uživatelé spolupracovat, a kdo v rámci vaší organizace může zahájit spolupráci. Většina organizací přistupuje přístup k tomu, že umožní obchodním jednotkám rozhodnout se, se kterými pracují, a delegovat schválení a dohled podle potřeby. Někteří organizace pro státní správu, vzdělávání a finanční služby například neumožňují otevřenou spolupráci. Můžete chtít použít funkce služby Azure AD k vymezení spolupráce, jak je popsáno ve zbývající části této části.

### <a name="determine-collaboration-partners"></a>Určení partnerů pro spolupráci

Nejdřív se ujistěte, že jste si popsali organizace, se kterými právě spolupracujete, a domény pro uživatele těchto organizací. Jeden partner pro spolupráci může mít více domén. Partner může mít například více obchodních jednotek s oddělenými doménami.

Dále určete, jestli chcete povolit budoucí spolupráci s 

* libovolná doména (nejvíce včetně)

* všechny domény s výjimkou výslovně zamítnutých 

* pouze konkrétní domény (nejvíce omezující)

> [!NOTE]
> Přísnější nastavení spolupráce, což je pravděpodobnější, že vaši uživatelé přecházejí mimo vaše schválené prostředí pro spolupráci. Doporučujeme, abyste povolili nejširší spolupráci, kterou vaše požadavky na zabezpečení umožní, a pečlivě kontrolujte tuto spolupráci, ale nebudete mít k disvelkému omezení. 

Všimněte si také, že omezení na jednu doménu může nechtěně zabránit autorizované spolupráci s organizacemi, které mají jiné nesouvisející domény pro uživatele. Pokud například obchodujete s organizací contoso, může být prvním kontaktním bodem společnosti Contoso jeden ze svých zaměstnanců na bázi USA, kteří mají e-mail s doménou ". com". Pokud ale povolíte jenom doménu ". com", možná nechtěně vynecháte své kanadské zaměstnance, kteří mají doménu ". ca". 

Existují situace, kdy byste chtěli, aby povolovaly jenom konkrétní partnery pro spolupráci. Například systém vysokých škol může chtít, aby měl jenom přístup svého vlastního vyučujícího k tenantovi prostředků. Nebo konglomerát může chtít, aby konkrétní pobočky mohly vzájemně spolupracovat, aby dosáhli souladu s požadovaným rozhraním.

#### <a name="using-allow-and-deny-lists"></a>Použití seznamů povolených a zakázaných

Pomocí seznamu povolených nebo zakázaných seznamů můžete [omezit pozvánky na uživatele B2B](../external-identities/allow-deny-list.md) z konkrétních organizací. Můžete použít pouze seznam povolených nebo odepřených, nikoli obojí.

* [Seznam povolených](../external-identities/allow-deny-list.md) zařízení omezuje spolupráci jenom na ty domény, které jsou uvedené v seznamu. všechny ostatní domény jsou ve skutečnosti v seznamu zakázaných.

* [Seznam pro odepření](../external-identities/allow-deny-list.md) umožňuje spolupráci s jakoukoli doménou, která není v seznamu odepřených.

> [!IMPORTANT]
> Tyto seznamy se nevztahují na uživatele, kteří jsou již ve vašem adresáři. Nevztahují se také na OneDrive pro firmy a SharePoint povolují neoddělené seznamy zakázaných seznamů.

Některé organizace používají seznam známých domén typu "špatných actor" poskytovaných spravovaným poskytovatelem zabezpečení pro seznam zamítnutých. Například pokud organizace má legitimní podnikání se společností Contoso a pomocí domény. com, může se jednat o nesouvisející organizaci, která používá doménu contoso. org a pokusit se o útok útoku phishing na zosobnění zaměstnanců společnosti Contoso. 

## <a name="control-how-external-users-gain-access"></a>Řízení, jak externí uživatelé získávají přístup

Existuje mnoho způsobů, jak spolupracovat s externími partnery pomocí Azure AD B2B. Chcete-li zahájit spolupráci, pozvěte nebo jinak umožněte vašemu partnerovi přístup k prostředkům. Uživatelé mohou získat přístup tak, že reagují na:

* Uplatnění [pozvánky odeslané e-mailem](../external-identities/redemption-experience.md)nebo [přímý odkaz na sdílení](../external-identities/redemption-experience.md) prostředku.

* Žádost o přístup [prostřednictvím aplikace](../external-identities/self-service-sign-up-overview.md) , kterou vytvoříte

* Vyžadování přístupu prostřednictvím portálu pro [přístup](../governance/entitlement-management-request-access.md)

Když povolíte Azure AD B2B, umožníte ve výchozím nastavení pozvat uživatele typu Host přes přímé odkazy a pozvánky e-mailu. Pozvánky prostřednictvím e-mailu jednorázového hesla a samoobslužného portálu jsou momentálně ve verzi Preview a musí být povolené v rámci externích identit. | Nastavení externí spolupráce na portálu Azure AD.

### <a name="control-who-can-invite-guest-users"></a>Řízení, kdo může pozvat uživatele typu Host

Určete, kdo může pozvat uživatele typu Host pro přístup k prostředkům.

* Nejpřísnějším nastavením je umožnit správcům přizvat pouze správce a těmto uživatelům, [kteří mají oprávnění pozvat hosty](../external-identities/delegate-invitations.md) .

* Pokud vaše požadavky na zabezpečení povolují, doporučujeme, aby všichni uživatelé s cílem pozvat hosty.

* Určete, jestli chcete, aby uživatelé s pozvaním oprávněním hosta, který je výchozím typem účtu pro uživatele Azure AD B2B, mohli pozvat další hosty. 

![Snímek obrazovky s nastavením pozvánky hosta](media/secure-external-access/5-guest-invite-settings.png)

 

### <a name="collect-additional-information-about-external-users"></a>Shromažďování dalších informací o externích uživatelích

Pokud používáte správu nároků služby Azure AD, můžete nakonfigurovat otázky pro externí uživatele na odpověď. Otázky se pak zobrazí schvalovatelům, aby jim pomohli udělat rozhodnutí. Pro každou [zásadu balíčku přístupu](../governance/entitlement-management-access-package-approval-policy.md) můžete nakonfigurovat různé sady otázek, aby schvalovatelé mohli mít příslušné informace pro přístup, který schválí. Pokud je například jeden balíček pro přístup určen pro přístup od dodavatele, žadatel může požádat o číslo smlouvy dodavatele. Jiný přístupový balíček určený pro dodavatele se může zeptat na jejich zemi původu.

Pokud používáte Samoobslužný portál, můžete k shromažďování dalších atributů uživatelů při registraci použít [konektory rozhraní API](../external-identities/api-connectors-overview.md) . Pak můžete tyto atributy použít k přiřazení přístupu. Například pokud během procesu registrace shromáždíte své ID dodavatele, můžete použít tento atribut k dynamickému přiřazování do skupiny nebo přístupu k balíčku pro daného dodavatele. V Azure Portal můžete vytvořit vlastní atributy a použít je v uživatelských tocích samoobslužné registrace. Tyto atributy můžete také číst a zapisovat pomocí [rozhraní Microsoft Graph API](https://docs.microsoft.com/azure/active-directory-b2c/manage-user-accounts-graph-api). 

### <a name="troubleshoot-invitation-redemption-to-azure-ad-users"></a>Řešení potíží s uplatněním pozvánky pro uživatele Azure AD

Existují tři instance, pokud pozvaní uživatelé typu Host od partnera pro spolupráci s využitím služby Azure AD budou mít problémy s uplatněním pozvánky.

* Pokud se používá seznam povolených a doména uživatele není zahrnutá do seznamu povolených.

* Pokud má domovský tenant partnera pro spolupráci omezení tenanta, která zabraňují spolupráci s externími uživateli.

* Pokud uživatel není součástí tenanta Azure AD partnera. Například uživatelé mají na contoso.com, kteří jsou pouze ve službě Active Directory (nebo jiné místní IdP), budou moci uplatnit pozvánky pouze prostřednictvím procesu e-mailového hesla. Další informace najdete v tématu [tok uplatnění pozvánky](../external-identities/redemption-experience.md).

## <a name="control-what-external-users-can-access"></a>Určení, ke kterým externím uživatelům mají přístup

Většina organizací není monolitické. To znamená, že existují některé prostředky, které jsou pro sdílení s externími uživateli dobré a některé z nich nebudete mít přístup k externím uživatelům. Proto musíte určit, ke kterým externím uživatelům máte přístup. Zvažte možnost použití [oprávnění pro správu a přístup k řízení přístupu](6-secure-access-entitlement-managment.md) ke konkrétním prostředkům.

Ve výchozím nastavení mohou uživatelé typu Host zobrazovat informace a atributy o členech tenanta a dalších partnerech, včetně členství ve skupinách. Zvažte, jestli vaše požadavky na zabezpečení volají omezení přístupu externích uživatelů k těmto informacím.

![Snímek obrazovky konfigurace nastavení externí spolupráce](media/secure-external-access/5-external-collaboration-settings.png)

Pro uživatele typu Host doporučujeme následující omezení.

* **Omezte přístup hosta na skupiny procházení a další vlastnosti v adresáři.**

   * Pomocí nastavení externí spolupráce můžete omezit schopnost hosta číst skupiny, které nejsou členy.

* **Zablokovat přístup k aplikacím jenom pro zaměstnance**.

   * Vytvořte zásady podmíněného přístupu, které zablokují přístup k aplikacím integrovaným v Azure AD, které jsou vhodné jenom pro uživatele, kteří nejsou hostem. 

* **Zablokuje přístup k Azure Portal. Je možné provádět vzácná nezbytná výjimka**. 

   * Vytvořte zásady podmíněného přístupu, které budou zahrnovat všechny uživatele typu Host a externí uživatelé a pak [implementují zásady pro blokování přístupu](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management).

 

## <a name="remove-users-who-no-longer-need-access"></a>Odebrat uživatele, kteří už nepotřebují přístup

Vyhodnoťte aktuální přístup, abyste mohli [zkontrolovat a odebrat uživatele, kteří už nepotřebují přístup](../governance/access-reviews-external-users.md). Zahrnutí externích uživatelů do tenanta jako hostů a těch, které obsahují členské účty. 

Některé organizace přidávají externí uživatele, jako jsou například dodavatelé, partneři a dodavatelé, jako členové. Tito členové mohou mít konkrétní atribut nebo uživatelská jména, která začínají, například

* v – pro dodavatele

* p-pro partnery

* c – pro dodavatele

Vyhodnoťte všechny externí uživatele pomocí členských účtů, abyste zjistili, jestli stále potřebují přístup. Pokud ano, převeďte tyto uživatele na službu Azure AD B2B, jak je popsáno v následující části.

Můžete mít také uživatele typu Host, kteří nebyli pozváni prostřednictvím správy nároků nebo Azure AD B2B.

Pro vyhledání těchto uživatelů můžete:

* [Najde uživatele typu Host, kteří nejsou pozváni prostřednictvím správy nároků](../governance/access-reviews-external-users.md).

   * Poskytujeme [vzorový skript PowerShellu.](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)

Převeďte tyto uživatele na uživatele Azure AD B2B, jak je popsáno v následující části.

## <a name="transition-your-current-external-users-to-b2b"></a>Převod aktuálních externích uživatelů na B2B

Pokud jste nepoužívali službu Azure AD B2B, pravděpodobně máte v tenantovi uživatele, kteří nejsou zaměstnanci. Doporučujeme, abyste tyto účty přešli na účty externích uživatelů B2B Azure AD a pak změnili své UserType na Host. Díky tomu můžete využívat mnoho způsobů, jak Azure AD, tak Microsoft 365 vám umožní zacházet se externími uživateli odlišně. Mezi tyto způsoby patří:

* Snadné zahrnutí nebo vyloučení uživatelů typu Host v zásadách podmíněného přístupu

* Snadné zahrnutí nebo vyloučení uživatelů typu Host v balíčcích přístupu a prohlídek přístupu

* Snadné zahrnutí nebo vyloučení externího přístupu k týmům, SharePointu a dalším prostředkům.

Informace o tom, jak tyto interní uživatele přecházet při zachování aktuálního přístupu, UPN a členství ve skupinách, najdete v tématu [pozvání externích uživatelů ke spolupráci B2B](../external-identities/invite-internal-users.md).

## <a name="decommission-undesired-collaboration-methods"></a>Vyřadit z provozu nežádoucí metody spolupráce

Pro dokončení přechodu na řízení spolupráce byste měli vyřadit z provozu nežádoucí metody spolupráce. Vyřazení z provozu vychází z rozsahu řízení, který chcete využít při spolupráci, a stav zabezpečení. Informace o tom, jak se nachází v rámci ovládacího prvku pro koncové uživatele, najdete v tématu [určení stav zabezpečení pro externí přístup](1-secure-access-posture.md).

Níže jsou uvedené prostředky pro spolupráci, které byste mohli chtít vyhodnotit.

### <a name="direct-invitation-through-microsoft-teams"></a>Přímá Pozvánka prostřednictvím Microsoft Teams

Ve výchozím nastavení týmy umožňují externí přístup, což znamená, že organizace může komunikovat se všemi externími doménami. Pokud chcete omezit nebo povolit konkrétní domény jenom pro týmy, můžete to udělat na [portálu pro správu týmů](https://admin.teams.microsoft.com/company-wide-settings/external-communications). 


### <a name="direct-sharing-through-sharepoint-and-onedrive"></a>Přímé sdílení prostřednictvím SharePointu a OneDrivu

Přímé sdílení prostřednictvím SharePointu a OneDrivu může přidat uživatele mimo proces správy nároků. Podrobný pohled na tyto konfigurace najdete v tématu [Správa přístupu pomocí Microsoft teams, SharePointu a OneDrivu pro firmy](9-secure-access-teams-sharepoint.md) . v případě potřeby můžete také [zablokovat používání osobního OneDrivu uživatele](https://docs.microsoft.com/office365/troubleshoot/group-policy/block-onedrive-use-from-office) .

### <a name="sending-documents-through-email"></a>Posílání dokumentů e-mailem

Vaši uživatelé budou posílat dokumenty prostřednictvím e-mailu externím uživatelům. Vezměte v úvahu, jak chcete omezit a šifrovat přístup k těmto dokumentům pomocí popisků citlivosti. Další informace najdete v tématu Správa přístupu s popisky citlivosti.

### <a name="unsanctioned-collaboration-tools"></a>Nástroje pro spolupráci neschválené

Na šířku nástrojů pro spolupráci je velká část. Vaši uživatelé nejspíš využívají spoustu neoficiálních povinností, včetně platforem, jako je Google Docs, DropBox, časová rezerva nebo přiblížení. Je možné blokovat použití takových nástrojů z podnikové sítě na úrovni brány firewall a pomocí správy mobilních aplikací pro zařízení spravovaná organizací. Tím se ale taky zablokuje všechny schválené instance těchto platforem a neblokuje se přístup z nespravovaných zařízení. V případě potřeby můžete blokovat platformy, které nechcete používat, a vytvářet obchodní zásady pro žádné neschválené použití pro platformy, které potřebujete použít. 

Další informace o správě neschválených aplikací najdete v těchto tématech:

* [Řízení připojených aplikací](https://docs.microsoft.com/cloud-app-security/governance-actions)

* [Schválení a zrušení schválení aplikace.](https://docs.microsoft.com/cloud-app-security/governance-discovery)

 
### <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích o zabezpečení externího přístupu k prostředkům. Doporučujeme, abyste provedli akce v uvedeném pořadí.

1. [Určení stav zabezpečení pro externí přístup](1-secure-access-posture.md)

2. [Zjistit aktuální stav](2-secure-access-current-state.md)

3. [Vytvoření plánu zásad správného řízení](3-secure-access-plan.md)

4. [Použití skupin pro zabezpečení](4-secure-access-groups.md)

5. [Přechod do Azure AD B2B](5-secure-access-b2b.md) (jste tady.)

6. [Zabezpečený přístup se správou oprávnění](6-secure-access-entitlement-managment.md)

7. [Zabezpečený přístup pomocí zásad podmíněného přístupu](7-secure-access-conditional-access.md)

8. [Zabezpečený přístup s popisky citlivosti](8-secure-access-sensitivity-labels.md)

9. [Zabezpečený přístup k Microsoft teams, OneDrivu a SharePointu](9-secure-access-teams-sharepoint.md)