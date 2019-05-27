---
title: Úprava a Správa existujícího balíčku přístupu v Azure AD oprávnění správy (Preview) – Azure Active Directory
description: Zjistěte, jak upravovat a spravovat existující balíček přístup ve službě Azure Active Directory management oprávnění (Preview).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce51f4df50de50cef06bba699ca7c6f76bc5d166
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833332"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>Úprava a správa přístupu balíček ve správě služby Azure AD oprávnění (Preview)

> [!IMPORTANT]
> Správa nároků Azure Active Directory (Azure AD) je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Balíček přístup můžete to udělat o jednorázové nastavení zásad a prostředků, které automaticky spravuje přístup po dobu životnosti přístup k balíčku. Jako správce přístupu k balíčku můžete změnit prostředky, přístup k balíčku v každém okamžiku bez starostí o zřizování přístup uživatele k nové prostředky a jejich přístup k odebrání předchozí zdroje. Kdykoli můžete také aktualizovat zásady, ale změny zásad ovlivní pouze nové přístupy.

Tento článek popisuje, jak upravovat a spravovat existující balíčky přístup.

## <a name="add-resource-roles"></a>Přidání role prostředků

Role prostředků je kolekce oprávnění přidružené k prostředku. Způsob, jakým prostředky k dispozici pro uživatele k vyžádání je přidáním role prostředků do vašeho přístupu k balíčku. Můžete přidat role prostředků pro skupiny, aplikace a weby služby SharePoint.

**Požadované role:** Správce uživatelů, katalog vlastník nebo správce přístupu k balíčku

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **přístup k balíčkům** a pak otevřete balíček přístup.

1. V nabídce vlevo klikněte na tlačítko **role prostředků**.

1. Klikněte na tlačítko **přidat prostředek role** otevřete přidat prostředek role pro přístup k balíčku stránky.

    ![Přístup k balíčku – přidání role prostředků](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. V závislosti na tom, jestli chcete přidat skupiny, aplikace nebo webu služby SharePoint proveďte kroky v jednom z následujících částí role prostředků.

### <a name="add-a-group-resource-role"></a>Přidání role skupiny prostředků

Můžete mít nárok management automaticky přidat uživatele do skupiny, jakmile budou přiřazena přístupu k balíčku. 

- Když skupiny je součástí balíčku přístup a uživatel se přiřadí přístup k balíčku, uživatel se přidá do této skupiny, pokud není již k dispozici.
- Když vyprší platnost přiřazení balíčku přístupu uživatele, jsou odebrány ze skupiny, pokud nemají aktuálně přiřazení k jinému balíčku přístup, který zahrnuje tu samou skupinu.

Můžete vybrat všechny skupiny Office 365 nebo Azure AD.  Správci mohou přidat libovolnou skupinu do katalogu; Vlastníci katalogu můžete přidat libovolnou skupinu do katalogu, pokud je vlastníkem skupiny. Při výběru skupiny, mít na paměti následující omezení služby Azure AD:

- Když uživatele, včetně typu Host, se přidá jako člen do skupiny, uvidí všichni ostatní členové této skupiny.
- Azure AD nelze změnit členství ve skupině, která se synchronizují ze služby Windows Server Active Directory pomocí služby Azure AD Connect.  
- Členství v dynamické skupiny nelze aktualizovat přidáním nebo odebráním člen, takže nejsou vhodné pro použití se službou Správa nároků dynamické členství ve skupinách.

1. Na **přidat prostředek role pro přístup k balíčku** klikněte na **skupiny** a otevřete tak podokno vybrané skupiny.

1. Vyberte skupiny, které chcete zahrnout do balíčku přístup.

    ![Přístup k balíčku – přidání role prostředků – vybrané skupiny](./media/entitlement-management-access-package-edit/group-select.png)

1. Klikněte na **Vybrat**.

1. V **Role** seznamu vyberte **vlastníka** nebo **člen**.

    Obvykle vyberete roli člena. Pokud vyberete role vlastníka, který uživatelům umožňuje přidat nebo odebrat ostatní členy nebo vlastníci.

    ![Přístup k balíčku – přidání role prostředků pro skupinu](./media/entitlement-management-access-package-edit/group-role.png)

1. Klikněte na tlačítko **Add** (Přidat).

    Každý uživatel s existující přiřazení přístupu k balíčku se automaticky se stanou členy této skupiny umožňuje při přidání.

### <a name="add-an-application-resource-role"></a>Přidat roli prostředků aplikace

Můžete použít Azure AD automaticky přidělit uživatelům přístup k podnikové aplikaci Azure AD, včetně aplikací SaaS a aplikací vaší organizace Federovaná do služby Azure AD, když je uživatel přiřazenou přístupu k balíčku. Pro aplikace, které se integrují s Azure AD prostřednictvím federovaného jednotného přihlašování Azure AD bude vystavovat tokeny federace pro uživatele s přiřazenými k aplikaci.

Aplikace může mít několik rolí. Při přidávání aplikace pro balíček s přístup, pokud tato aplikace má více než jednu roli, musíte zadat příslušné role pro uživatele.  Pokud vyvíjíte aplikace, můžete si přečíst více o tom, jak tyto role jsou k dispozici pro vaše aplikace v článku o tom, jak [konfigurace deklarace role vystavených v tokenu SAML](../develop/active-directory-enterprise-app-role-management.md).

Jakmile se aplikační role je součástí balíčku přístupu:

- Když se uživatel je přiřazený přístup k balíčku, uživatel se přidá do této role aplikace, pokud již nejsou přítomny.
- Když vyprší platnost přiřazení balíčku přístupu uživatele, jejich přístup se odebere z aplikace, pokud nemají přiřazení k jinému balíčku přístup, který obsahuje tento aplikační role.

Tady jsou některé aspekty při výběru aplikace:

- Aplikace také může mít přiřazené k jejich rolím i skupiny.  Můžete však přidat skupinu namísto aplikační role, přístup k balíčku, potom aplikace nebudou viditelné pro uživatele jako součást balíčku přístupu na portálu Moje přístup.

1. Na **přidat prostředek role pro přístup k balíčku** klikněte na **aplikací** a otevřete tak podokno výběr aplikace.

1. Vyberte aplikace, které chcete zahrnout do balíčku přístup.

    ![Přístup k balíčku – přidání role prostředků – výběr aplikací](./media/entitlement-management-access-package-edit/application-select.png)

1. Klikněte na **Vybrat**.

1. V **Role** vyberte roli aplikace.

    ![Přístup k balíčku – přidání prostředku role pro aplikaci](./media/entitlement-management-access-package-edit/application-role.png)

1. Klikněte na tlačítko **Add** (Přidat).

    Každý uživatel s existující přiřazení přístupu k balíčku automaticky dostanou přístup k této aplikaci umožňuje při přidání.

### <a name="add-a-sharepoint-site-resource-role"></a>Přidat prostředek role lokality služby SharePoint

Azure AD může automaticky přidělit uživatelům přístup k webu Sharepointu Online nebo SharePoint Online kolekci webů Jakmile budou přiřazena přístupu k balíčku.

1. Na **přidat prostředek role pro přístup k balíčku** klikněte na **Sharepointové weby** a otevřete tak podokno výběr Sharepointu Online servery.

1. Vyberte na weby SharePoint Online, které chcete zahrnout do balíčku přístup.

    ![Přístup k balíčku – přidání role prostředků – výběr Sharepointových webů](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. Klikněte na **Vybrat**.

1. V **Role** vyberte roli webu Sharepointu Online.

    ![Přístup k balíčku – přidání prostředku role webu Sharepointu Online](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. Klikněte na tlačítko **Add** (Přidat).

    Každý uživatel s existující přiřazení přístupu k balíčku bude automaticky mít přístup k této Sharepointový web umožňuje při přidání.

## <a name="remove-resource-roles"></a>Odebrání role prostředků

**Požadované role:** Správce uživatelů, katalog vlastník nebo správce přístupu k balíčku

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **přístup k balíčkům** a pak otevřete balíček přístup.

1. V nabídce vlevo klikněte na tlačítko **role prostředků**.

1. V seznamu prostředků rolí najdete prostředků roli, kterou chcete odebrat.

1. Klikněte na tlačítko se třemi tečkami (**...** ) a potom klikněte na tlačítko **odebrat prostředek role**.

    Každý uživatel s existující přiřazení k balíčku přístupu bude mít automaticky přístup odvolat tento prostředek roli. odebere se současně.

## <a name="add-a-new-policy"></a>Přidat nové zásady

Způsob, jak určit, kdo žádosti přístup k balíčku je můžete vytvořit zásadu. Pokud chcete povolit různé skupiny uživatelů, kterým se má udělit přiřazení s jinou schválení a nastavení vypršení platnosti, můžete vytvořit několik zásad pro přístup pomocí jednotného balíček. Interní a externí uživatele přiřadit stejný přístup k balíčku nelze použít jedna zásada. Však můžete vytvořit dvě zásady, které v balíčku stejný přístup – jednu pro interní uživatele a jednu pro externí uživatele. Pokud existuje více zásad, které se vztahují na uživatele, uživatelé budou vyzváni k v době jejich požadavek na vyberte zásadu, že mají být přiřazená.

Následující diagram znázorňuje proces vysoké úrovně můžete vytvořit zásadu pro existující balíček pro přístup.

![Vytvořit proces zásad](./media/entitlement-management-access-package-edit/policy-process.png)

**Požadované role:** Správce uživatelů, katalog vlastník nebo správce přístupu k balíčku

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **přístup k balíčkům** a pak otevřete balíček přístup.

1. Klikněte na tlačítko **zásady** a potom **přidat zásady**.

1. Zadejte název a popis zásady.

    ![Vytvoření zásady se název a popis](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. Na základě vašeho výběru pro **uživatelé, kteří můžou požádat o přístup**, proveďte kroky v jednom z následujících částí zásad.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>Upravit existující zásadu

Kdykoli můžete upravit zásady. Pokud změníte datum vypršení platnosti pro zásady, datum vypršení platnosti pro požadavky, které jsou již čeká na schválení nebo schválení stav se nezmění.

**Požadované role:** Správce uživatelů, katalog vlastník nebo správce přístupu k balíčku

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **přístup k balíčkům** a pak otevřete balíček přístup.

1. Klikněte na tlačítko **zásady** a pak klikněte na zásadu, kterou chcete upravit.

    **Podrobnosti zásad** v dolní části stránky se otevře podokno.

    ![Přístup k balíčku - zásady v podokně podrobností](./media/entitlement-management-access-package-edit/policy-details.png)

1. Klikněte na tlačítko **upravit** pro editaci zásady.

    ![Přístup k balíčku - upravit zásadu](./media/entitlement-management-access-package-edit/policy-edit.png)

1. Až budete hotovi, klikněte na tlačítko **aktualizace**.

## <a name="directly-assign-a-user"></a>Přímo přiřadit uživatele

V některých případech můžete chtít přímo přiřadit konkrétní uživatele pro balíček s přístup tak, aby uživatelé nemuseli projít procesem požadovat přístup k balíčku. Přímo přiřadit uživatelům, musí mít přístup k balíčku zásadu, která umožňuje přímé přiřazení správce.

**Požadované role:** Správce uživatelů, katalog vlastník nebo správce přístupu k balíčku

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **přístup k balíčkům** a pak otevřete balíček přístup.

1. V nabídce vlevo klikněte na tlačítko **přiřazení**.

1. Klikněte na tlačítko **nové přiřazení** otevřete přidat uživatele k přístupu k balíčku.

    ![Přiřazení - přidat uživatele k přístupu k balíčku](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. Klikněte na tlačítko **přidat uživatele** vybraným uživatelům, kterým chcete přiřadit přístup k balíčku.

1. V **vyberte zásadu** vyberte zásadu, která má [None (pouze správce přímých přiřazení)](#policy-none-administrator-direct-assignments-only) nastavení.

    Pokud tento balíček přístup nemá tento typ zásad, můžete kliknout na **vytvořit nové zásady** aby vám ho přidal.

1. Nastavte datum a čas, chcete, aby vybraní uživatelé přiřazení k zahájení a ukončení. Pokud není k dispozici koncové datum, použijí se nastavení zásad vypršení platnosti.

1. Volitelně zadejte odůvodnění pro přímé přiřazení pro zachování záznamů.

1. Klikněte na tlačítko **přidat** přímo přiřadit vybraným uživatelům přístup k balíčku.

    Po chvíli, klikněte na tlačítko **aktualizovat** zobrazíte uživatele v seznamu přiřazení.

## <a name="view-who-has-an-assignment"></a>Zobrazení, kdo má přiřazení

**Požadované role:** Správce uživatelů, katalog vlastník nebo správce přístupu k balíčku

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **přístup k balíčkům** a pak otevřete balíček přístup.

1. Klikněte na tlačítko **přiřazení** zobrazíte seznam aktivních přiřazení.

1. Kliknutím na konkrétní přiřazení zobrazíte další podrobnosti.

1. Pokud chcete zobrazit seznam přiřazení, která nemá všechny role prostředků správně zřízený, klikněte na tlačítko filtru stavu a vyberte **doručování**.

    Můžete zobrazit další podrobnosti o doručení chyby vyhledáním odpovídající žádost uživatele na **požadavky** stránky.

1. Vypršela platnost přiřazení, klikněte na tlačítko filtru stavu a vyberte **neplatné**.

1. Chcete-li stáhnout soubor CSV filtrovaný seznam, klikněte na tlačítko **Stáhnout**.

## <a name="view-requests"></a>Zobrazit požadavky

**Požadované role:** Správce uživatelů, katalog vlastník nebo správce přístupu k balíčku

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **přístup k balíčkům** a pak otevřete balíček přístup.

1. Klikněte na tlačítko **požadavky**.

1. Kliknutím zobrazíte další podrobnosti o konkrétní požadavek.

## <a name="view-a-requests-delivery-errors"></a>Zobrazit chyby doručování požadavku

**Požadované role:** Správce uživatelů, katalog vlastník nebo správce přístupu k balíčku

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **přístup k balíčkům** a pak otevřete balíček přístup.

1. Klikněte na tlačítko **požadavky**.

1. Vyberte žádosti, které chcete zobrazit.

    Pokud požadavek má nějaké chyby doručování, stav žádosti bude **Undelivered** a dílčího stavu bude **částečně doručit**.

    Pokud nejsou žádné chyby doručování, v podokně Podrobnosti žádosti, bude počet doručení chyby.

1. Kliknutím na počet zobrazíte všechny žádosti doručení chyby.

## <a name="cancel-a-pending-request"></a>Zrušení čekající žádosti o

Můžete pouze zrušit čekající na vyřízení žádosti, která dosud nebyla doručena.

**Požadované role:** Správce uživatelů, katalog vlastník nebo správce přístupu k balíčku

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **přístup k balíčkům** a pak otevřete balíček přístup.

1. Klikněte na tlačítko **požadavky**.

1. Klikněte na žádost, u které chcete akci zrušit

1. V podokně podrobností žádosti, klikněte na tlačítko **požadavek na zrušení**.

## <a name="copy-my-access-portal-link"></a>Zkopírujte odkaz pro tento přístup k portálu

Většina uživatelů ve vašem adresáři můžou přihlásit k portálu Můj přístup a automaticky zobrazí seznam balíčků přístupu, které můžou požádat o. Pro externí obchodních partnerů, které ještě nejsou ve vašem adresáři, musíte však poslat odkaz, který můžete použít k žádosti přístup k balíčku. Přístup k balíčku je povolená pro externí uživatele a pokud máte zásadu pro adresář externí uživatele, můžete použít externího uživatele na portálu odkaz Mé přístup požádat o přístup k balíčku.

**Požadované role:** Správce uživatelů, katalog vlastník nebo správce přístupu k balíčku

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **přístup k balíčkům** a pak otevřete balíček přístup.

1. Na stránce s přehledem, zkopírujte **Moje propojte portálu**.

    ![Přehled přístupu k balíčku - Moje propojte portálu](./media/entitlement-management-shared/my-access-portal-link.png)

1. E-mailu nebo pošlete tento odkaz externím obchodním partnerům. Propojení můžou sdílet se svým uživatelům požádat o přístup k balíčku.

## <a name="change-the-hidden-setting"></a>Změnit nastavení skryté

Zjistitelný ve výchozím nastavení jsou balíčky přístup. To znamená, že pokud zásada umožňuje uživatelům požádat o přístup k balíčku, automaticky zobrazí balíček přístup uvedené na portálu Moje přístup.

**Požadované role:** Správce uživatelů, katalog vlastník nebo správce přístupu k balíčku

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **přístup k balíčkům** a pak otevřete balíček přístup.

1. Na stránce Přehled klikněte na tlačítko **upravit**.

1. Nastavte **skryté** nastavení.

    Pokud nastavena na **č**, zobrazí se balíček přístup na portálu Moje přístupu uživatele.

    Pokud hodnotu **Ano**, přístup k balíčku nebudou uvedené na portálu Moje přístup daného uživatele. Jediný způsob, jak může uživatel zobrazit balíček přístupu je, pokud mají přímou **Moje propojte portálu** k přístupu k balíčku.

## <a name="delete"></a>Odstranit

Balíček přístup můžete odstranit pouze, pokud nemá žádná přiřazení aktivního uživatele.

**Požadované role:** Správce uživatelů, katalog vlastník nebo správce přístupu k balíčku

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **přístup k balíčkům** a pak otevřete balíček přístup.

1. V nabídce vlevo klikněte na tlačítko **přiřazení** a odebrat přístup pro všechny uživatele.

1. V nabídce vlevo klikněte na tlačítko **přehled** a potom klikněte na tlačítko **odstranit**.

1. Ve zprávě delete, který se zobrazí, klikněte na tlačítko **Ano**.

## <a name="when-are-changes-applied"></a>Když se použijí změny

Ve správě oprávnění Azure AD zpracuje hromadných změn pro přiřazení a prostředky ve vašich balíčcích přístup několikrát za den. Proto pokud provedete přiřazení nebo změna role prostředků balíčku přístup, může trvat až 24 hodin pro tuto změnu chcete provést ve službě Azure AD, plus množství času se trvá přenést tyto změny do jiných Microsoft Online Services nebo připojené aplikaci SaaS s. Pokud vaše změna ovlivní pouze několik objektů, změna pravděpodobně zabere jenom pár minut a můžete použít ve službě Azure AD, po jejímž uplynutí další součásti služby Azure AD pak zjistí se změní a aktualizovat aplikace SaaS. Pokud vaše změna ovlivní tisíce objektů, tato změna bude trvat déle. Například pokud máte balíček přístup s 2 aplikacemi a 100 přiřazení uživatelských a rozhodnete se přidat roli serveru služby SharePoint k přístupu k balíčku, může docházet k prodlevám dokud všichni uživatelé jsou součástí této role webu služby SharePoint. Můžete sledovat průběh pomocí protokolů auditu Azure AD, zřizování protokolu Azure AD a protokoly auditu webu služby SharePoint.

## <a name="next-steps"></a>Další postup

- [Požádat o procesu a e-mailových oznámení](entitlement-management-process.md)
