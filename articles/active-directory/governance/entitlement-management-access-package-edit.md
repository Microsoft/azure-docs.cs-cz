---
title: Úprava a Správa stávajícího balíčku přístupu ve správě nároků ve službě Azure AD (Preview) – Azure Active Directory
description: Naučte se, jak upravit a spravovat existující balíček přístupu v Azure Active Directory Správa nároků (Preview).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: c07f9dbcc21840bec4b4487225dfb8108586865e
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489208"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>Úprava a Správa stávajícího balíčku pro přístup ve správě nároků ve službě Azure AD (Preview)

> [!IMPORTANT]
> Správa opravňujících k Azure Active Directory (Azure AD) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Balíček pro přístup vám umožní jednorázovou instalaci prostředků a zásad, které automaticky spravují přístup po dobu života balíčku přístupu. Jako správce balíčků přístupu můžete kdykoli změnit prostředky v balíčku pro přístup, aniž byste se museli starat o přístup uživatelů k novým prostředkům nebo odebrat jeho přístup z předchozích zdrojů. Zásady je také možné kdykoli aktualizovat, ale změny zásad ovlivňují jenom nové přístupy.

Tento článek popisuje, jak upravit a spravovat existující balíčky přístupu.

## <a name="add-resource-roles"></a>Přidat role prostředků

Role prostředku je kolekce oprávnění přidružených k prostředku. Způsob, jakým zpřístupníte prostředky uživatelům k vyžádání, je přidání rolí prostředků do balíčku pro přístup. Můžete přidat role prostředků pro skupiny, aplikace a weby služby SharePoint.

**Požadovaná role:** Správce uživatele, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. V nabídce vlevo klikněte na **role prostředků**.

1. Kliknutím na **Přidat role prostředků** otevřete stránku přidat role prostředků pro přístup do balíčku.

    ![Přístup k balíčku – přidání rolí prostředků](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. V závislosti na tom, jestli chcete přidat skupinu, aplikaci nebo web služby SharePoint, proveďte kroky v jedné z následujících částí role prostředku.

### <a name="add-a-group-resource-role"></a>Přidat roli prostředku skupiny

Můžete mít správu nároků do skupiny automaticky přidávat uživatele, když se jim přiřadí balíček přístupu. 

- Když je skupina součástí balíčku přístupu a uživatel je přiřazen k tomuto přístupovému balíčku, uživatel se přidá do této skupiny, pokud ještě není k dispozici.
- Po vypršení platnosti přiřazení balíčku přístupu uživatele se ze skupiny odeberou, pokud aktuálně nemají přiřazení k jinému přístupovému balíčku, který obsahuje stejnou skupinu.

Můžete vybrat libovolnou skupinu Office 365 nebo skupinu zabezpečení Azure AD.  Správci můžou do katalogu přidat libovolnou skupinu. Vlastníci katalogu můžou přidat libovolnou skupinu do katalogu, pokud jsou vlastníkem skupiny. Při výběru skupiny mějte na paměti následující omezení Azure AD:

- Když se uživatel, včetně hosta, přidá jako člen do skupiny, uvidí všichni ostatní členové této skupiny.
- Azure AD nemůže změnit členství ve skupině, která byla synchronizovaná ze služby Windows Server Active Directory pomocí Azure AD Connect.  
- Členství dynamických skupin nelze aktualizovat přidáním nebo odebráním člena, takže dynamická členství ve skupině nejsou vhodná pro použití se správou nároků.

1. Na stránce **Přidat role prostředků pro přístup k balíčku** kliknutím na **skupiny** otevřete podokno vybrat skupiny.

1. Vyberte skupiny, které chcete zahrnout do balíčku pro přístup.

    ![Přístup k balíčku – přidání rolí prostředků – výběr skupin](./media/entitlement-management-access-package-edit/group-select.png)

1. Klikněte na tlačítko **vyberte**.

1. V seznamu **role** vyberte možnost **vlastník** nebo **člen**.

    Obvykle vyberete roli člena. Pokud vyberete roli vlastníka, která umožní uživatelům přidat nebo odebrat jiné členy nebo vlastníky.

    ![Přístup k balíčku – přidání role prostředku pro skupinu](./media/entitlement-management-access-package-edit/group-role.png)

1. Klikněte na **Přidat**.

    Všichni uživatelé s existujícími přiřazeními k balíčku pro přístup se při přidání automaticky stanou členy této skupiny.

### <a name="add-an-application-resource-role"></a>Přidat roli prostředku aplikace

Azure AD může automaticky přiřazovat uživatelům přístup k podnikovým aplikacím Azure AD, včetně aplikací SaaS i aplikací vaší organizace, které jsou federované do Azure AD, když je uživateli přiřazený balíček přístupu. Pro aplikace, které se integrují se službou Azure AD prostřednictvím federovaného jednotného přihlašování, Azure AD bude vydávat federační tokeny pro uživatele přiřazené k aplikaci.

Aplikace mohou mít více rolí. Pokud má aplikace při přidávání aplikace do balíčku pro přístup více než jednu roli, budete muset pro tyto uživatele zadat příslušnou roli.  Pokud vyvíjíte aplikace, můžete si přečíst další informace o tom, jak se tyto role poskytují vašim aplikacím v článku o tom, jak [nakonfigurovat deklaraci identity role vydanou v tokenu SAML](../develop/active-directory-enterprise-app-role-management.md).

Jakmile je role aplikace součástí balíčku přístupu:

- Když je uživateli přiřazený balíček přístupu, přidá se do této role aplikace, pokud ještě není k dispozici.
- Po vypršení platnosti přiřazení balíčku přístupu uživatele se z aplikace odeberou jejich přístup, pokud nemají přiřazení k jinému balíčku přístupu, který by tuto roli aplikace zahrnoval.

Zde jsou některé předpoklady při výběru aplikace:

- Aplikace mohou mít také přiřazené skupiny i role.  Můžete se rozhodnout přidat skupinu místo aplikační role v balíčku pro přístup, ale aplikace nebude viditelná pro uživatele jako součást balíčku přístupu na portálu pro přístup.

1. Na stránce **Přidat role prostředků pro přístup k balíčku** kliknutím na **aplikace** otevřete podokno vybrat aplikace.

1. Vyberte aplikace, které chcete zahrnout do balíčku pro přístup.

    ![Přístup k balíčku – přidání rolí prostředků – výběr aplikací](./media/entitlement-management-access-package-edit/application-select.png)

1. Klikněte na tlačítko **vyberte**.

1. V seznamu **role** vyberte roli aplikace.

    ![Přístup k balíčku – přidání role prostředku pro aplikaci](./media/entitlement-management-access-package-edit/application-role.png)

1. Klikněte na **Přidat**.

    Všichni uživatelé s existujícími přiřazeními k balíčku pro přístup budou při přidání automaticky mít k této aplikaci přístup.

### <a name="add-a-sharepoint-site-resource-role"></a>Přidat roli prostředku webu služby SharePoint

Azure AD může automaticky přiřadit uživatele k webu SharePointu Online nebo kolekci webů SharePointu Online, když jim přiřadí balíček přístupu.

1. Na stránce **Přidat role prostředků pro přístup k balíčku** kliknutím na **weby služby SharePoint** otevřete podokno vybrat weby SharePointu Online.

1. Vyberte weby SharePointu Online, které chcete zahrnout do balíčku pro přístup.

    ![Přístup k balíčku – přidání rolí prostředků – výběr webů SharePointu Online](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. Klikněte na tlačítko **vyberte**.

1. V seznamu **role** vyberte roli webu SharePoint Online.

    ![Přístup k balíčku – přidání role prostředku pro web SharePointu Online](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. Klikněte na **Přidat**.

    Všichni uživatelé s existujícími přiřazeními k balíčku pro přístup budou při přidání automaticky mít přístup k tomuto webu SharePointu Online.

## <a name="remove-resource-roles"></a>Odebrání rolí prostředků

**Požadovaná role:** Správce uživatele, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. V nabídce vlevo klikněte na **role prostředků**.

1. V seznamu rolí prostředků Najděte roli prostředků, kterou chcete odebrat.

1. Klikněte na tlačítko se třemi tečkami ( **...** ) a pak klikněte na **Odebrat roli prostředku**.

    Všichni uživatelé s existujícími přiřazeními k balíčku pro přístup budou mít při odebrání do této role prostředků automaticky přístup odvolali.

## <a name="add-a-new-policy"></a>Přidat nové zásady

Způsob, jakým určíte, kdo může požádat o přístup k balíčku, je vytvořit zásadu. Můžete vytvořit více zásad pro jeden balíček přístupu, pokud chcete, aby bylo možné udělit různým sadám uživatelů přiřazení s jiným nastavením schválení a vypršení platnosti. Jednu zásadu nelze použít k přiřazení interních a externích uživatelů ke stejnému balíčku přístupu. V jednom balíčku pro přístup se ale dají vytvořit dvě zásady – jeden pro interní uživatele a jeden pro externí uživatele. Pokud je k dispozici více zásad, které platí pro uživatele, budou v době žádosti vyzváni k výběru zásady, ke kterým se mají přiřadit.

Následující diagram znázorňuje proces vysoké úrovně pro vytvoření zásady pro existující balíček přístupu.

![Vytvoření procesu zásad](./media/entitlement-management-access-package-edit/policy-process.png)

**Požadovaná role:** Správce uživatele, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Klikněte na **zásady** a potom na **Přidat zásadu**.

1. Zadejte název a popis zásady.

    ![Vytvořit zásadu s názvem a popisem](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. Na základě vašeho výběru pro **uživatele, kteří můžou požádat o přístup**, postupujte podle kroků v jedné z následujících částí zásad.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>Úprava existující zásady

Zásadu můžete kdykoli upravit. Pokud změníte datum vypršení platnosti zásady, datum vypršení platnosti požadavků, které jsou již v nedokončeném schválení nebo schváleném stavu, se nezmění.

**Požadovaná role:** Správce uživatele, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Klikněte na **zásady** a potom klikněte na zásadu, kterou chcete upravit.

    V dolní části stránky se otevře podokno **Podrobnosti o zásadách** .

    ![Přístupový balíček – podokno podrobností zásad](./media/entitlement-management-access-package-edit/policy-details.png)

1. Zásadu upravíte kliknutím na **Upravit** .

    ![Přístup k balíčku – upravit zásadu](./media/entitlement-management-access-package-edit/policy-edit.png)

1. Po dokončení klikněte na **aktualizovat**.

## <a name="directly-assign-a-user"></a>Přímo přiřadit uživatele

V některých případech můžete chtít přímo přiřadit konkrétní uživatele k balíčku pro přístup, aby uživatelé nemuseli projít procesem vyžadování balíčku pro přístup. Aby bylo možné přiřazovat uživatele přímo, musí mít přístupový balíček zásadu, která umožňuje přímé přiřazení správců.

**Požadovaná role:** Správce uživatele, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. V nabídce vlevo klikněte na **přiřazení**.

1. Kliknutím na **nové přiřazení** otevřete přidat uživatele pro přístup k balíčku.

    ![Přiřazení – přidat uživatele do balíčku pro přístup](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. Klikněte na **Přidat uživatele** a vyberte uživatele, kterým chcete přiřadit balíček přístupu.

1. V seznamu **Vybrat zásadu** vyberte zásadu, která má nastavení [žádné (pouze přímé přiřazení správců)](#policy-none-administrator-direct-assignments-only) .

    Pokud tento balíček přístupu nemá tento typ zásad, můžete ho přidat kliknutím na **vytvořit novou zásadu** .

1. Nastavte datum a čas, kdy má přiřazení vybraných uživatelů začít a končit. Pokud není zadané koncové datum, použijí se nastavení vypršení platnosti této zásady.

1. Volitelně můžete zadat odůvodnění pro přímé přiřazení záznamu.

1. Kliknutím na tlačítko **Přidat** můžete vybraným uživatelům přiřadit přímý přístup k balíčku přístupu.

    Po chvíli klikněte na **aktualizovat** , aby se uživatelé mohli zobrazit v seznamu přiřazení.

## <a name="view-who-has-an-assignment"></a>Zobrazit, kdo má přiřazení

**Požadovaná role:** Správce uživatele, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Kliknutím na **přiřazení** zobrazíte seznam aktivních přiřazení.

1. Kliknutím na konkrétní přiřazení zobrazíte další podrobnosti.

1. Pokud chcete zobrazit seznam přiřazení, která neobsahovala správně zřízené všechny role prostředků, klikněte na stav filtru a vyberte **doručování**.

    Na stránce **žádosti** můžete zobrazit další podrobnosti o chybách doručení hledáním odpovídající žádosti uživatele.

1. Pokud chcete zobrazit vypršení platnosti, klikněte na stav filtru a vyberte **konec platnosti**.

1. Pokud chcete stáhnout soubor CSV s filtrovaným seznamem, klikněte na **Stáhnout**.

## <a name="view-requests"></a>Zobrazit žádosti

**Požadovaná role:** Správce uživatele, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Klikněte na **požadavky**.

1. Kliknutím na konkrétní požadavek zobrazíte další podrobnosti.

## <a name="view-a-requests-delivery-errors"></a>Zobrazit chyby doručení žádosti

**Požadovaná role:** Správce uživatele, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Klikněte na **požadavky**.

1. Vyberte žádost, kterou chcete zobrazit.

    Pokud v žádosti dojde k žádným chybám doručení, stav žádosti se **nedoručení** a dílčí stav se doručí **částečně**.

    Pokud dojde k chybám doručení, v podokně podrobností žádosti dojde k počtu chyb doručení.

1. Kliknutím na počet zobrazíte všechny chyby doručení žádosti.

## <a name="cancel-a-pending-request"></a>Zrušení žádosti, která čeká na vyřízení

Můžete zrušit pouze nevyřízenou žádost, která ještě nebyla doručena.

**Požadovaná role:** Správce uživatele, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Klikněte na **požadavky**.

1. Klikněte na žádost, kterou chcete zrušit.

1. V podokně podrobností žádosti klikněte na **Zrušit požadavek**.

## <a name="copy-my-access-portal-link"></a>Kopírovat odkaz na portál přístupu

Většina uživatelů v adresáři se může přihlásit k portálu pro přístup a automaticky uvidí seznam balíčků pro přístup, které si můžou vyžádat. U externích partnerských uživatelů, kteří ještě nejsou ve vašem adresáři, je ale budete muset poslat odkaz, který může použít k vyžádání přístupového balíčku. Pokud je pro přístup k externím uživatelům povolený balíček přístupu a máte zásady pro adresář externího uživatele, může externí uživatel požádat o přístup k balíčku s odkazem na portál pro přístup.

**Požadovaná role:** Správce uživatele, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Na stránce Přehled zkopírujte **odkaz Můj portál přístupu**.

    ![Přehled přístupu k balíčku – odkaz na portál pro přístup](./media/entitlement-management-shared/my-access-portal-link.png)

1. Odešlete e-mail nebo pošlete odkaz na vašeho externího obchodního partnera. Můžou sdílet odkaz s uživateli, aby si vyžádali balíček pro přístup.

## <a name="change-the-hidden-setting"></a>Změna skrytého nastavení

Ve výchozím nastavení jsou balíčky pro přístup zjistitelné. To znamená, že pokud zásada umožní uživateli požádat o přístup k balíčku, automaticky uvidí balíček pro přístup uvedený na portálu pro přístup.

**Požadovaná role:** Správce uživatele, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Na stránce Přehled klikněte na **Upravit**.

1. Nastavte **skryté** nastavení.

    Pokud je nastavena na **ne**, bude balíček přístupu uveden na portálu pro přístup uživatele.

    Pokud je nastavená hodnota **Ano**, balíček přístupu nebude uveden na portálu pro přístup uživatele. Jediným způsobem, jak může uživatel zobrazit balíček přístupu, je, že má odkaz na přímý **přístup** do balíčku pro přístup.

## <a name="delete"></a>Odstranění

Balíček přístupu se dá odstranit jenom v případě, že nemá žádná aktivní uživatelská přiřazení.

**Požadovaná role:** Správce uživatele, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. V nabídce vlevo klikněte na **přiřazení** a odeberte přístup pro všechny uživatele.

1. V nabídce vlevo klikněte na **Přehled** a pak klikněte na **Odstranit**.

1. Ve zprávě odstranit, která se zobrazí, klikněte na tlačítko **Ano**.

## <a name="when-are-changes-applied"></a>Kdy se aplikují změny

V rámci správy nároků služba Azure AD zpracuje hromadnou změnu přiřazení a prostředků v balíčcích přístupu několikrát denně. Takže pokud provedete přiřazení nebo změníte role prostředků vašeho balíčku pro přístup, může trvat až 24 hodin, než se tato změna provede v Azure AD, a navíc dobu potřebnou k rozšíření těchto změn na jiné online služby Microsoftu nebo připojené aplikace SaaS. pracují. Pokud vaše změna ovlivní jenom pár objektů, může tato změna trvat jenom pár minut, než se použije ve službě Azure AD, potom ostatní součásti Azure AD tuto změnu odhalí a aktualizují aplikace SaaS. Pokud vaše změna ovlivní tisíce objektů, bude změna trvat déle. Například pokud máte balíček pro přístup se dvěma aplikacemi a 100 přiřazení uživatele a rozhodnete se přidat roli webu služby SharePoint do balíčku pro přístup, může dojít ke zpoždění, dokud všichni uživatelé nebudou součástí této role webu služby SharePoint. Průběh můžete sledovat pomocí protokolu auditu Azure AD, protokolu zřizování Azure AD a protokolů auditu webu služby SharePoint.

## <a name="next-steps"></a>Další postup

- [Přidat vlastníka katalogu nebo správce balíčků přístupu](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [Žádost o proces a e-mailová oznámení](entitlement-management-process.md)
