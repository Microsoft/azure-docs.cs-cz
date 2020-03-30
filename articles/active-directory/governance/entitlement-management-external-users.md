---
title: Řízení přístupu pro externí uživatele ve správě nároků Azure AD – Azure Active Directory
description: Přečtěte si o nastaveních, která můžete zadat pro řízení přístupu pro externí uživatele ve správě nároků služby Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: af0f68ca9520c1715463212da80aaabed48f8269
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128699"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>Řízení přístupu pro externí uživatele ve správě nároků Azure AD

Správa nároků Azure AD využívá [Azure AD business-to-business (B2B)](../b2b/what-is-b2b.md) ke spolupráci s lidmi mimo vaši organizaci v jiném adresáři. S Azure AD B2B externí uživatelé ověřit do svého domovského adresáře, ale mají reprezentaci ve vašem adresáři. Reprezentace v adresáři umožňuje uživateli přiřadit přístup k vašim prostředkům.

Tento článek popisuje nastavení, která můžete zadat pro řízení přístupu pro externí uživatele.

## <a name="how-entitlement-management-can-help"></a>Jak může správa nároků pomoci

Při použití prostředí [pozvání Azure AD B2B,](../b2b/what-is-b2b.md) musíte už znát e-mailové adresy externích uživatelů typu Host, které chcete přenést do adresáře prostředků a pracovat s. To funguje skvěle, když pracujete na menším nebo krátkodobém projektu a již znáte všechny účastníky, ale to je těžší řídit, pokud máte spoustu uživatelů, se kterými chcete pracovat, nebo pokud se účastníci v průběhu času mění.  Můžete například pracovat s jinou organizací a mít jeden kontakt s tou organizací, ale v průběhu času budou přístup potřebovat i další uživatelé z této organizace.

Pomocí správy nároků můžete definovat zásadu, která uživatelům z organizací, které určíte, umožní, aby si mohli sami požádat o přístupový balíček. Můžete určit, zda je vyžadováno schválení a datum vypršení platnosti přístupu. Pokud je vyžadováno schválení, můžete také pozvat jednoho nebo více uživatelů z externí organizace do adresáře a označit je jako schvalovatele , protože pravděpodobně vědí, kteří externí uživatelé z jejich organizace potřebují přístup. Po konfiguraci přístupového balíčku můžete odkaz na přístupový balíček odeslat kontaktní osobě (sponzorovi) v externí organizaci. Tento kontakt může sdílet s ostatními uživateli v externí organizaci a mohou tento odkaz použít k vyžádání přístupového balíčku. Tento odkaz mohou použít také uživatelé z této organizace, kteří již byli pozváni do vašeho adresáře.

Po schválení žádosti správa oprávnění zřídí uživateli potřebný přístup, který může zahrnovat pozvání uživatele, pokud ještě není ve vašem adresáři. Azure AD automaticky vytvoří účet hosta B2B pro ně. Všimněte si, že správce může mít dříve omezené, které organizace jsou povoleny pro spolupráci, nastavením [B2B povolit nebo odepřít seznam](../b2b/allow-deny-list.md) povolit nebo blokovat pozvánky do jiných organizací.  Pokud uživatel není povolen seznamem povolených nebo blokovaných, nebude pozván.

Vzhledem k tomu, že nechcete, aby přístup externího uživatele trval navždy, zadejte datum vypršení platnosti v zásadách, například 180 dní. Po 180 dnech, pokud jejich přístup není prodloužena, správa oprávnění odebere veškerý přístup přidružený k tomuto balíčku přístupu. Ve výchozím nastavení, pokud uživatel, který byl pozván prostřednictvím správy nároků, nemá žádná další přiřazení balíčků přístupu, pak když ztratí své poslední přiřazení, bude jeho účet guest zablokován z přihlášení po dobu 30 dnů a následně odebrán. Tím se zabrání šíření zbytečných účtů. Jak je popsáno v následujících částech, tato nastavení lze konfigurovat.

## <a name="how-access-works-for-external-users"></a>Jak funguje přístup pro externí uživatele

Následující diagram a kroky poskytují přehled o tom, jak jsou externím uživatelům udělen přístup k balíčku přístupu.

![Diagram znázorňující životní cyklus externích uživatelů](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. Přidáte [připojenou organizaci](entitlement-management-organization.md) pro adresář nebo doménu Azure AD, se kterou chcete spolupracovat.

1. V adresáři vytvoříte přístupový balíček, který obsahuje zásadu [Pro uživatele, kteří nejsou ve vašem adresáři](entitlement-management-access-package-create.md#for-users-not-in-your-directory).

1. Odkaz [na portál Služby Přístup](entitlement-management-access-package-settings.md) odešlete kontaktu v externí organizaci, který mohou sdílet se svými uživateli a požádat o přístupový balíček.

1. Externí uživatel (**Žadatel A** v tomto příkladu) používá odkaz portálu Můj přístup k [vyžádání přístupu](entitlement-management-request-access.md) k přístupu k balíčku přístupu. Způsob přihlášení uživatele závisí na typu ověřování adresáře nebo domény definované v připojené organizaci.

1. Schvalovatel [žádost schválí](entitlement-management-request-approve.md) (nebo je žádost automaticky schválena).

1. Požadavek přejde do [stavu doručení](entitlement-management-process.md).

1. Pomocí procesu pozvání B2B je ve vašem adresáři vytvořen uživatelský účet hosta (**Žadatel A (Host)** v tomto příkladu). Pokud je definován [seznam povolených](../b2b/allow-deny-list.md) nebo odepřít, bude použito nastavení seznamu.

1. Uživateli typu Host je přiřazen přístup ke všem prostředkům v balíčku přístupu. Může chvíli trvat, než se změny ve službě Azure AD a v jiných službách Microsoft Online Services nebo připojených aplikacích SaaS promění. Další informace naleznete v [tématu Při použití změn](entitlement-management-access-package-resources.md#when-changes-are-applied).

1. Externí uživatel obdrží e-mail s oznámením, že byl [doručen](entitlement-management-process.md)jeho přístup .

1. Pro přístup k prostředkům může externí uživatel klepnout na odkaz v e-mailu nebo se pokusit o přístup k libovolnému adresářovému prostředkům přímo k dokončení procesu pozvání.

1. V závislosti na nastavení zásad, jak plyne čas, vyprší platnost přiřazení balíčku přístupu pro externího uživatele a přístup externího uživatele je odebrán.

1. V závislosti na životním cyklu nastavení externích uživatelů, pokud externí uživatel již nemá žádné přiřazení balíčků přístupu, je externíuživatel blokován od přihlášení a uživatelský účet hosta je odebrán z adresáře.

## <a name="settings-for-external-users"></a>Nastavení pro externí uživatele

Chcete-li zajistit, aby uživatelé mimo vaši organizaci mohli požadovat přístupové balíčky a získat přístup k prostředkům v těchto přístupových balíčcích, je třeba ověřit některá nastavení, která byste měli ověřit, že jsou správně nakonfigurována.

### <a name="enable-catalog-for-external-users"></a>Povolení katalogu pro externí uživatele

- Ve výchozím nastavení je při vytváření [nového katalogu](entitlement-management-catalog-create.md)povoleno povolit externím uživatelům požadovat přístupové balíčky v katalogu. Ujistěte **se, že je pro externí uživatele** nastavena možnost Povoleno na **ano**.

    ![Upravit nastavení katalogu](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Konfigurace nastavení externí spolupráce Azure AD B2B

- Povolení hostům pozvat do adresáře další hosty znamená, že pozvánky hostů mohou probíhat mimo správu nároků. Doporučujeme nastavit **Hosté mohou pozvat** na **ne,** aby povolili pouze správně řízené pozvánky.
- Pokud používáte seznam povolených B2B, musíte se ujistit, že do seznamu je přidána jakákoli doména, se kterou chcete spolupracovat pomocí správy nároků. Případně pokud používáte seznam zamítnutí B2B, musíte se ujistit, že do seznamu není přidána žádná doména, se kterou chcete spolupracovat.
- Pokud vytvoříte zásadu správy nároků pro **všechny uživatele** (všechny připojené organizace + všichni noví externí uživatelé), všechna nastavení seznamu B2B povolit nebo odepřít, které máte, budou mít přednost. Proto nezapomeňte zahrnout domény, které chcete zahrnout do této zásady, do seznamu povolených položek, pokud jej používáte, a vyloučit je ze seznamu zamítnutí, pokud používáte seznam zamítnutí.
- Pokud chcete vytvořit zásadu správy nároků, která zahrnuje **všechny uživatele** (všechny připojené organizace + všechny nové externí uživatele), musíte nejprve povolit ověření kódu e-mailem pro váš adresář. Další informace najdete v [tématu E-mail s jednorázovým ověřováním přístupového kódu (preview).](../b2b/one-time-passcode.md#opting-in-to-the-preview)
- Další informace o nastavení externí spolupráce Azure AD B2B najdete [v tématu Povolení externí spolupráce B2B a správa toho, kdo může pozvat hosty](../b2b/delegate-invitations.md).

    ![Nastavení externí spolupráce Azure AD](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>Kontrola zásad podmíněného přístupu

- Nezapomeňte vyloučit hosty ze všech zásad podmíněného přístupu, které noví uživatelé typu Host nebudou moci splnit, protože jim to zabrání v přihlášení k vašemu adresáři. Například hosté pravděpodobně nemají registrované zařízení, nejsou ve známém umístění a nechtějí se znovu zaregistrovat pro vícefaktorové ověřování (MFA), takže přidání těchto požadavků do zásad podmíněného přístupu zablokuje hostům používání oprávnění Správa. Další informace najdete v tématu [Co jsou podmínky v podmíněném přístupu služby Azure Active Directory?](../conditional-access/concept-conditional-access-conditions.md).

    ![Zásady podmíněného přístupu Azure AD vylučují nastavení](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>Kontrola nastavení externího sdílení SharePointu Online

- Pokud chcete do přístupových balíčků pro externí uživatele zahrnout weby SharePointu Online, ujistěte se, že je nastavení externího sdílení na úrovni organizace nastavené na **Kdokoli** (uživatelé nevyžadují přihlášení) nebo **Noví a stávající hosté** (hosté se musí přihlásit nebo zadat ověřovací kód). Další informace naleznete v [tématu Zapnutí nebo vypnutí externího sdílení](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Pokud chcete omezit jakékoli externí sdílení mimo správu oprávnění, můžete nastavit nastavení externího sdílení na **existující hosty**. Přístup k těmto webům pak budou moci získat pouze noví uživatelé, kteří jsou pozváni prostřednictvím správy nároků. Další informace naleznete v [tématu Zapnutí nebo vypnutí externího sdílení](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Ujistěte se, že nastavení na úrovni webu umožňují přístup hosta (stejné možnosti výběry, jak je uvedeno v dříve). Další informace naleznete v [tématu Zapnutí nebo vypnutí externího sdílení webu](https://docs.microsoft.com/sharepoint/change-external-sharing-site).

### <a name="review-your-office-365-group-sharing-settings"></a>Kontrola nastavení sdílení skupin office 365

- Pokud chcete do přístupových balíčků pro externí uživatele zahrnout skupiny Office 365, ujistěte se, že je **možnost Umožnit uživatelům přidávat nové hosty do organizace** nastavenou na **Zapnuto,** aby byl povolen přístup hosta. Další informace najdete [v tématu Správa přístupu hosta ke skupinám Office 365](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide#manage-groups-guest-access).

- Pokud chcete, aby externí uživatelé měli přístup k webu SharePointu Online a k prostředkům přidruženým ke skupině Office 365, nezapomeňte zapnout externí sdílení SharePointu Online. Další informace naleznete v [tématu Zapnutí nebo vypnutí externího sdílení](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Informace o nastavení zásad hosta pro skupiny Office 365 na úrovni adresáře v Prostředí PowerShell najdete v [tématu Příklad: Konfigurace zásad hosta pro skupiny na úrovni adresáře](../users-groups-roles/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level).

### <a name="review-your-teams-sharing-settings"></a>Kontrola nastavení sdílení teams

- Pokud chcete zahrnout Teams do přístupových balíčků pro externí uživatele, ujistěte se, že **je možnost Povolit přístup hosta v Microsoft Teams** nastavená na **Zapnuto,** aby byl povolen přístup hosta. Další informace najdete [v tématu Konfigurace přístupu pro hosty v Centru pro správu Microsoft Teams](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center).

## <a name="manage-the-lifecycle-of-external-users"></a>Správa životního cyklu externích uživatelů

Můžete vybrat, co se stane, když externí uživatel, který byl pozván do vašeho adresáře prostřednictvím žádosti o přístup balíček je schválen, již nemá žádné přiřazení balíčků přístupu. K tomu může dojít, pokud se uživatel vzdá všech přiřazení balíčků přístupu nebo jejich poslední přiřazení balíčku přístupu vyprší. Ve výchozím nastavení, pokud externí uživatel již nemá žádné přiřazení balíčků přístupu, je mu zablokováno přihlášení do adresáře. Po 30 dnech bude z vašeho adresáře odebrán uživatelský účet guest.

**Předpokladová role:** Globální správce nebo správce uživatelů

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte v části **Správa nároků** na **Nastavení**.

1. Klikněte na **Upravit**.

    ![Nastavení pro správu životního cyklu externích uživatelů](./media/entitlement-management-external-users/settings-external-users.png)

1. V části **Spravovat životní cyklus externích uživatelů** vyberte různá nastavení pro externí uživatele.

1. Jakmile externí uživatel ztratí své poslední přiřazení k libovolným přístupovým balíčkům, pokud mu chcete zabránit v přihlášení k tomuto adresáři, nastavte **možnost Blokovat externího uživatele, aby se k tomuto adresáři nepřihlašoval,** na **ano**.

    > [!NOTE]
    > Pokud je uživateli zablokováno přihlášení k tomuto adresáři, nebude moci znovu požádat o přístupový balíček nebo požádat o další přístup v tomto adresáři. Nenakonfigurujte blokování jejich přihlášení, pokud budou následně muset požádat o přístup k jiným přístupovým balíčkům.

1. Jakmile externí uživatel ztratí své poslední přiřazení k libovolným přístupovým balíčkům, pokud chcete odebrat jeho uživatelský účet hosta v tomto adresáři, nastavte **odebrat externího uživatele** na **Ano**.

    > [!NOTE]
    > Správa nároků odebere pouze účty, které byly pozvány prostřednictvím správy oprávnění. Všimněte si také, že uživatel bude blokován z přihlášení a odebrán z tohoto adresáře i v případě, že tento uživatel byl přidán do prostředků v tomto adresáři, které nebyly přístup k přiřazení balíčků. Pokud host byl přítomen v tomto adresáři před přijetím přiřazení balíčku přístupu, zůstanou. Pokud však byl host pozván prostřednictvím přiřazení balíčku přístupu a po pozvání byl také přiřazen k webu OneDrive pro firmy nebo SharePoint online, bude odebrán.

1. Chcete-li odebrat uživatelský účet hosta v tomto adresáři, můžete nastavit počet dní před jeho odebráním. Pokud chcete odebrat uživatelský účet hosta, jakmile ztratí své poslední přiřazení k libovolným přístupovým balíčkům, nastavte **počet dní před odebráním externího uživatele z tohoto adresáře** na **0**.

1. Klikněte na **Uložit**.

## <a name="next-steps"></a>Další kroky

- [Přidání připojené organizace](entitlement-management-organization.md)
- [Pro uživatele, kteří nejsou ve vašem adresáři](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Řešení potíží](entitlement-management-troubleshoot.md)
