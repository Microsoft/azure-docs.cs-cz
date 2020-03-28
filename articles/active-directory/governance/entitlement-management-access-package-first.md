---
title: Kurz – vytvoření balíčku přístupu – správa oprávnění Azure AD
description: Podrobný kurz, jak vytvořit první balíček přístupu ve správě nároků Služby Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 10/22/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd4feeb83acc3842874e7a2e4bbd32dacabcc00d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75422660"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management"></a>Kurz: Vytvoření prvního balíčku přístupu ve správě nároků Azure AD

Správa přístupu ke všem prostředkům, které zaměstnanci potřebují, jako jsou skupiny, aplikace a weby, je důležitou funkcí organizací. Chcete zaměstnancům poskytnout správnou úroveň přístupu, kterou potřebují k tomu, aby byli produktivní, a odebrat jim přístup, když již není potřeba.

V tomto kurzu pracujete pro Woodgrove Bank jako správce IT. Byli jste požádáni o vytvoření balíčku zdrojů pro marketingovou kampaň, kterou mohou interní uživatelé samoobslužné a požadovat. Požadavky nevyžadují schválení a přístup uživatele vyprší po 30 dnech. V tomto kurzu jsou prostředky marketingové kampaně pouze členstvím v jedné skupině, ale může se jedná o kolekci skupin, aplikací nebo webů SharePointu Online.

![Přehled scénáře](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření přístupového balíčku se skupinou jako zdrojem
> * Povolení přístupu uživateli ve vašem adresáři
> * Předveďte, jak může interní uživatel požádat o přístupový balíček

Podrobné ukázky procesu nasazování správy nároků služby Azure Active Directory, včetně vytvoření prvního balíčku přístupu, naznejte následující video:

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

## <a name="prerequisites"></a>Požadavky

Chcete-li používat správu oprávnění Azure AD, musíte mít jednu z následujících licencí:

- Azure AD Premium P2
- Licence Enterprise Mobility + Security (EMS) E5

Další informace naleznete v tématu [Licenční požadavky](entitlement-management-overview.md#license-requirements).

## <a name="step-1-set-up-users-and-group"></a>Krok 1: Nastavení uživatelů a skupin

Adresář prostředků má jeden nebo více prostředků ke sdílení. V tomto kroku vytvoříte skupinu s názvem **Marketingové zdroje** v adresáři Woodgrove Bank, která je cílovým zdrojem pro správu nároků. Můžete také nastavit interní žadatel.

**Předpokladová role:** Globální správce nebo správce uživatelů

![Vytváření uživatelů a skupin](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako globální správce nebo správce uživatelů.  

1. V levém navigačním panelu klikněte na **Azure Active Directory**.

1. Vytvořte nebo nakonfigurujte následující dva uživatele. Tyto názvy nebo různé názvy můžete použít. **Admin1** může být uživatel, ke které jste aktuálně přihlášeni.

    | Name (Název) | Role adresáře |
    | --- | --- |
    | **Správce1** | Globální správce<br/>-nebo-<br/>Správce uživatele |
    | **Žadatel** | Uživatel |

1. Vytvořte skupinu zabezpečení Azure AD s názvem **Marketingové prostředky** s typem **členství Přiřazeno**.

    Tato skupina bude cílovým zdrojem pro správu nároků. Skupina by měla být prázdná členů pro spuštění.

## <a name="step-2-create-an-access-package"></a>Krok 2: Vytvoření přístupového balíčku

Přístupový *balíček* je sada prostředků, které tým nebo projekt potřebuje a řídí se zásadami. Přístupové balíčky jsou definovány v kontejnerech *nazývaných katalogy*. V tomto kroku vytvoříte balíček přístupu k **marketingové kampani** v **obecném** katalogu.

**Předpokladová role:** Globální správce, správce uživatele, vlastník katalogu nebo správce balíčků aplikace Access

![Vytvoření přístupového balíčku](./media/entitlement-management-access-package-first/elm-access-package.png)

1. Na webu Azure Portal klikněte v levém navigačním panelu na **Položku Azure Active Directory**.

1. V levé nabídce klikněte na **Zásady správného řízení identity.**

1. V levé nabídce klepněte na **položku Přístup k balíčkům**.  Pokud se vám **přístup odepřen**, ujistěte se, že licence Azure AD Premium P2 je k dispozici ve vašem adresáři.

1. Klepněte na **položku Nový přístupový balíček**.

    ![Správa nároků na webu Azure Portal](./media/entitlement-management-shared/access-packages-list.png)

1. Na kartě **Základy** zadejte název Balíček přístupu k **marketingové kampani** a popis **Přístup k prostředkům kampaně**.

1. Rozevírací seznam **Katalog** ponechejte nastavený na **obecné**.

    ![Nový přístupový balíček – karta Základy](./media/entitlement-management-access-package-first/basics.png)

1. Kliknutím na **Další** otevřete kartu **Role prostředků.**

    Na této kartě vyberete prostředky a roli prostředku, které chcete zahrnout do balíčku přístupu.

1. Klikněte na **Skupiny a týmy**.

1. V podokně Vybrat skupiny vyhledejte a vyberte skupinu **marketingových zdrojů,** kterou jste vytvořili dříve.

    Ve výchozím nastavení se zobrazí skupiny uvnitř a vně **obecného** katalogu. Vyberete-li skupinu mimo **obecný** katalog, bude přidána do **obecného** katalogu.

    ![Nový přístupový balíček – karta Role prostředků](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Kliknutím na **Vybrat** přidáte skupinu do seznamu.

1. V rozevíracím seznamu **Role** vyberte **Člen**.

    ![Nový přístupový balíček – karta Role prostředků](./media/entitlement-management-access-package-first/resource-roles.png)

1. Kliknutím na **Další** otevřete kartu **Požadavky.**

    Na této kartě vytvoříte zásadu požadavku. *Zásada* definuje pravidla nebo svodidla pro přístup k balíčku přístupu. Vytvoření zásady, která umožňuje určitému uživateli v adresáři prostředků požadovat tento přístupový balíček.

1. V části **Uživatelé, kteří mohou požádat o přístup,** klikněte **v adresáři** na možnost Pro uživatele a potom klikněte na **položku Konkrétní uživatelé a skupiny**.

    ![Nový přístupový balíček – karta Požadavky](./media/entitlement-management-access-package-first/requests.png)

1. Klikněte na **Přidat uživatele a skupiny**.

1. V podokně Vybrat uživatele a skupiny vyberte dříve vytvořeného uživatele **Requestor1.**

    ![Nový přístupový balíček – karta Požadavky – výběr uživatelů a skupin](./media/entitlement-management-access-package-first/requests-select-users-groups.png)

1. Klepněte na **tlačítko Vybrat**.

1. Posuňte se dolů do oddílů **Schválení** a **Povolit požadavky.**

1. Ponechat **ponechat Po schválení ponechat** nastaveno na **ne**.

1. V **případě povolení požadavků**povolte klepnutím na tlačítko **Ano,** chcete-li povolit požadavek na tento přístupový balíček ihned po jeho vytvoření.

    ![Nový přístupový balíček – karta Požadavky – schválení a povolení požadavků](./media/entitlement-management-access-package-first/requests-approval-enable.png)

1. Kliknutím na **Další** otevřete kartu **Životní cyklus.**

1. V části **Vypršení platnosti** nastavte **počet přiřazení balíčků aplikace Access** na Počet **dní**.

1. Nastavit **Přiřazení vyprší po** **30** dnech.

    ![Nový přístupový balíček – karta Životní cyklus](./media/entitlement-management-access-package-first/lifecycle.png)

1. Kliknutím na **Další** otevřete kartu **Revize + Vytvořit.**

    ![Nový přístupový balíček – karta Revize + Vytvoření](./media/entitlement-management-access-package-first/review-create.png)

    Po několika okamžicích byste měli vidět oznámení, že balíček přístupu byl úspěšně vytvořen.

1. V levé nabídce balíčku Přístup k marketingové kampani klepněte na tlačítko **Přehled**.

1. Zkopírujte **odkaz Na portál u aplikace Můj Přístup**.

    Tento odkaz použijete k dalšímu kroku.

    ![Přehled přístupových balíčků – odkaz na portál Můj Přístup](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-3-request-access"></a>Krok 3: Žádost o přístup

V tomto kroku provedete kroky jako **interní žadatel** a požádat o přístup k balíčku přístupu. Žadatelé odešlou své požadavky pomocí webu nazvaného Portál přístupu. Portál Můj přístup umožňuje žadatelům odesílat žádosti o přístupové balíčky, zobrazit přístupové balíčky, ke kterým již mají přístup, a zobrazit jejich historii požadavků.

**Předpokladová role:** Interní žadatel

1. Odhlaste se z webu Azure Portal.

1. V novém okně prohlížeče přejděte na odkaz Můj přístup ový portál, který jste zkopírovali v předchozím kroku.

1. Přihlaste se k portálu Můj přístup jako **Requestor1**.

    Měli byste vidět balíček přístupu **k marketingové kampani.**

1. V případě potřeby klikněte ve sloupci **Popis** na šipku a zobrazte podrobnosti o přístupovém balíčku.

    ![Můj přístupový portál – přístupové balíčky](./media/entitlement-management-shared/my-access-access-packages.png)

1. Kliknutím na zaškrtnutí vyberte balíček.

1. Kliknutím na **Požádat o přístup** otevřete podokno Požádat o přístup.

    ![Můj přístupový portál – tlačítko Požádat o přístup](./media/entitlement-management-access-package-first/my-access-request-access-button.png)

1. Do pole **Obchodní odůvodnění** zadejte odůvodnění, na kterém pracuji na nové **marketingové kampani**.

    ![Portál Můj přístup – žádost o přístup](./media/entitlement-management-shared/my-access-request-access.png)

1. Klepněte na **tlačítko Odeslat**.

1. V levé nabídce klikněte na **Možnost vyžádat historii** a ověřte, že váš požadavek byl odeslán.

## <a name="step-4-validate-that-access-has-been-assigned"></a>Krok 4: Ověření, zda byl přiřazen přístup

V tomto kroku potvrdíte, že **internímu žadateli** byl přiřazen přístupový balíček a že je nyní členem skupiny **Marketingové prostředky.**

**Předpokladová role:** Globální správce, správce uživatele, vlastník katalogu nebo správce balíčků aplikace Access

1. Odhlaste se z portálu Můj přístup.

1. Přihlaste se na [portál Azure](https://portal.azure.com) jako **Správce1**.

1. Klikněte na **Azure Active Directory** a potom klikněte na **Zásadsprávné řízení identit .**

1. V levé nabídce klepněte na **položku Přístup k balíčkům**.

1. Najděte a klikněte na balíček přístupu k **marketingové kampani.**

1. V levé nabídce klikněte na **Požadavky**.

    Měli byste vidět Requestor1 a počáteční zásady se stavem **Delivered**.

1. Kliknutím na požadavek zobrazíte podrobnosti žádosti.

    ![Přístupový balíček – podrobnosti požadavku](./media/entitlement-management-access-package-first/request-details.png)

1. V levém navigačním panelu klikněte na **Azure Active Directory**.

1. Klikněte na **Skupiny** a otevřete skupinu **Marketingové zdroje.**

1. Klepněte na **položku Členové**.

    Měli byste vidět **Requestor1** uvedeny jako člen.

    ![Členové marketingových zdrojů](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-5-clean-up-resources"></a>Krok 5: Vyčištění prostředků

V tomto kroku odeberete provedené změny a odstraníte balíček přístupu k **marketingové kampani.**

**Předpokladová role:**  Globální správce nebo správce uživatelů

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. Otevřete balíček přístupu k **marketingové kampani.**

1. Klepněte na **položku Přiřazení**.

1. V **části Requestor1**klepněte na tři tečky (**...**) a potom klepněte na tlačítko **Odebrat přístup**. Ve zprávě, která se zobrazí, klepněte na tlačítko **Ano**.

    Po několika okamžicích se stav změní z Delivered na Delivered na Expired.

1. Klepněte na **položku Role prostředků**.

1. V **části Marketingové zdroje**klepněte na tři tečky (**...**) a potom klepněte na tlačítko **Odebrat roli zdroje**. Ve zprávě, která se zobrazí, klepněte na tlačítko **Ano**.

1. Otevřete seznam přístupových balíčků.

1. V **části Marketingová kampaň**klepněte na tři tečky (**...**) a potom klepněte na tlačítko **Odstranit**. Ve zprávě, která se zobrazí, klepněte na tlačítko **Ano**.

1. Ve službě Azure Active Directory odstraňte všechny uživatele, které jste vytvořili, například **Requestor1** a **Admin1**.

1. Odstraňte skupinu **Marketingové zdroje.**

## <a name="next-steps"></a>Další kroky

Přejdete k dalšímu článku a dozvíte se o běžných krocích scénáře při správě nároků.
> [!div class="nextstepaction"]
> [Obvyklé scénáře](entitlement-management-scenarios.md)
