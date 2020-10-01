---
title: Kurz – vytvoření balíčku pro přístup – Správa nároků Azure AD
description: Podrobný návod, jak vytvořit první balíček přístupu v Azure Active Directory správě nároků.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 09/30/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38edbd9e491d4bea469c6b83ad98df48fbce1d4f
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91597508"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management"></a>Kurz: vytvoření prvního balíčku přístupu ve správě nároků Azure AD

Správa přístupu ke všem zdrojům prostředků, jako jsou skupiny, aplikace a weby, je důležitou funkcí pro organizace. Chcete zaměstnancům udělit správnou úroveň přístupu, které potřebují k zajištění produktivity, a odebrat svůj přístup, když už ho nepotřebujete.

V tomto kurzu pracujete s Woodgrove bankou jako s správcem IT. Byli jste požádáni o vytvoření balíčku prostředků pro marketingovou kampaň, které interní uživatelé můžou použít k samoobslužnému požadavku. Žádosti nevyžadují schválení a přístup uživatele vyprší po 30 dnech. Pro účely tohoto kurzu jsou prostředky marketingové kampaně pouze členstvím v jedné skupině, ale může se jednat o kolekci skupin, aplikací nebo webů SharePointu Online.

![Diagram, který zobrazuje přehled scénáře.](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření balíčku pro přístup se skupinou jako prostředku
> * Umožňuje uživateli ve vašem adresáři požádat o přístup.
> * Ukázka, jak může interní uživatel požádat o přístup k balíčku

Podrobný příklad procesu nasazení správy opravňujících Azure Active Directory, včetně vytvoření prvního balíčku pro přístup, najdete v následujícím videu:

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

Balíček pro přístup můžete také vytvořit programově pomocí Microsoft Graph. Kurz, ve kterém se dozvíte, jak vytvořit balíček pro přístup prostřednictvím kódu programu, najdete v tématu [rozhraní API pro správu oprávnění](https://docs.microsoft.com/graph/tutorial-access-package-api?view=graph-rest-beta).

## <a name="prerequisites"></a>Předpoklady

Pokud chcete používat správu nároků služby Azure AD, musíte mít jednu z těchto licencí:

- Azure AD Premium P2
- Licence pro Enterprise Mobility + Security (EMS) E5

Další informace najdete v tématu [licenční požadavky](entitlement-management-overview.md#license-requirements).

## <a name="step-1-set-up-users-and-group"></a>Krok 1: nastavení uživatelů a skupin

Adresář prostředků má jeden nebo více prostředků ke sdílení. V tomto kroku vytvoříte skupinu s názvem **marketingové prostředky** v adresáři Woodgrove Bank, která je cílovým prostředkem pro správu nároků. Nastavili jste také interního žadatele.

**Požadovaná role:** Globální správce nebo Správce uživatelů

![Vytváření uživatelů a skupin](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce nebo Správce uživatelů.  

1. V levém navigačním panelu klikněte na **Azure Active Directory**.

1. Vytvořte nebo nakonfigurujte následující dva uživatele. Můžete použít tyto názvy nebo jiné názvy. **Admin1** může být uživatel, ke kterému jste aktuálně přihlášení.

    | Název | Role adresáře |
    | --- | --- |
    | **Uživateli** | Globální správce<br/>-nebo-<br/>Správce uživatelů |
    | **Requestor1** | Uživatel |

1. Vytvořte skupinu zabezpečení Azure AD s názvem **marketingové zdroje** s **přiřazeným**typem členství.

    Tato skupina bude cílovým prostředkem pro správu nároků. Skupina by měla být prázdná pro členy, kteří mají být spuštěni.

## <a name="step-2-create-an-access-package"></a>Krok 2: vytvoření balíčku pro přístup

*Balíček pro přístup* je sada prostředků, které tým nebo projekt potřebuje a které se řídí zásadami. Balíčky přístupu jsou definované v kontejnerech nazývaných *katalogy*. V tomto kroku vytvoříte balíček pro přístup k **marketingové kampani** v katalogu **Obecné** .

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

![Vytvoření přístupového balíčku](./media/entitlement-management-access-package-first/elm-access-package.png)

1. V Azure Portal klikněte v levém navigačním panelu na možnost **Azure Active Directory**.

2. V nabídce vlevo klikněte na zásady **správného řízení identity** .

3. V nabídce vlevo klikněte na možnost **přístup k balíčkům**.  Pokud se zobrazí **přístup odepřen**, ujistěte se, že je ve vašem adresáři přítomen Azure AD Premium licence P2.

4. Klikněte na **nový balíček pro přístup**.

    ![Správa nároků v Azure Portal](./media/entitlement-management-shared/access-packages-list.png)

5. Na kartě **základy** zadejte název přístupového balíčku **marketingové kampaně** a popis **přístup k prostředkům kampaně**.

6. Ponechejte rozevírací seznam **katalog** nastavený na **Obecné**.

    ![Karta základy na novém přístupovém balíčku](./media/entitlement-management-access-package-first/basics.png)

7. Kliknutím na **Další** otevřete kartu **role prostředků** .

    Na této kartě můžete vybrat prostředky a roli prostředků, které chcete zahrnout do balíčku pro přístup.

8. Klikněte na **skupiny a týmy**.

9. V podokně vybrat skupiny vyhledejte a vyberte skupinu **marketingových zdrojů** , kterou jste vytvořili dříve.

     Ve výchozím nastavení se zobrazují skupiny v katalogu Obecné. Když vyberete skupinu mimo katalog obecné, uvidíte, že zaškrtnutí políčka **Zobrazit vše** bude přidáno do katalogu Obecné.

    ![Snímek obrazovky zobrazující kartu nové přístupové balíčky a role prostředků a okno vybrat skupiny](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

10. Kliknutím na **Vybrat** přidejte skupinu do seznamu.

11. V rozevíracím seznamu **role** vyberte **člen**.

    ![Nový balíček přístupu – karta role prostředků](./media/entitlement-management-access-package-first/resource-roles.png)
    >[!IMPORTANT]
    >Skupiny role, které přiřadíte do balíčku přístupu, budou označeny pomocí podtypu, který **lze přiřadit k rolím**. Další podrobnosti o skupinách, které je možné přiřadit k rolím Azure AD, najdete v tématu [Vytvoření skupiny s přiřazením rolí](../users-groups-roles/roles-groups-create-eligible.md) v Azure Active Directory. Pokud nevidíte skupinu přiřazenou rolí, kterou chcete přidat, nebo ji nemůžete přidat, ujistěte se, že máte požadovanou roli Azure AD a správu oprávnění k provedení této operace. Možná budete muset požádat někoho s požadovanými rolemi přidat prostředek do katalogu. Další informace najdete v tématu [požadované role pro přidání prostředků do katalogu](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

    >[!NOTE]
    > Při použití [dynamických skupin](../users-groups-roles/groups-create-rule.md) se kromě vlastníka nezobrazí žádné další role. Toto chování je úmyslné.
    > ![Přehled scénáře](./media/entitlement-management-access-package-first/dynamic-group-warning.png)
    


12. Kliknutím na **Další** otevřete kartu **žádosti** .

    Na této kartě vytvoříte zásadu žádosti. *Zásady* definují pravidla nebo guardrails pro přístup k balíčku přístupu. Vytvoříte zásadu, která umožňuje určitému uživateli v adresáři prostředků požádat o tento balíček přístupu.

13. V části **Uživatelé, kteří můžou požádat o přístup** , klikněte na **uživatele v adresáři** a pak klikněte na **konkrétní uživatelé a skupiny**.

    ![Nový balíček přístupu – karta požadavky](./media/entitlement-management-access-package-first/requests.png)

14. Klikněte na **Přidat uživatele a skupiny**.

15. V podokně vybrat uživatele a skupiny vyberte uživatele **Requestor1** , kterého jste vytvořili dříve.

    ![Nový přístupový balíček – karta požadavky – vybrat uživatele a skupiny](./media/entitlement-management-access-package-first/requests-select-users-groups.png)

16. Klikněte na **Vybrat**.

17. Přejděte dolů na oddíly **schválení** a **Povolit žádosti** .

18. Nechte položku **vyžadovat schválení** nastavenou na **ne**.

19. Pro **Povolit žádosti**klikněte na **Ano** , aby se tento balíček pro přístup vyžádal, jakmile se vytvoří.

    ![Nový přístupový balíček – vyžádá schválení a povolí žádosti na kartě.](./media/entitlement-management-access-package-first/requests-approval-enable.png)

20. Kliknutím na **Další** otevřete kartu **životní cyklus** .

21. V části **vypršení platnosti** nastavte u **Přiřazení přístupových balíčků** **počet dní**.

22. Nastavte **platnost přiřazení po dobu** **30** dnů.

    ![Nový balíček přístupu – karta životního cyklu](./media/entitlement-management-access-package-first/lifecycle.png)

23. Kliknutím na **Další** otevřete kartu **Revize + vytvořit** .

    ![Nový balíček přístupu – revize + vytvořit kartu](./media/entitlement-management-access-package-first/review-create.png)

    Po chvíli byste měli vidět oznámení, že byl balíček pro přístup úspěšně vytvořen.

24. V nabídce vlevo v balíčku pro přístup k marketingové kampani klikněte na **Přehled**.

25. Zkopírujte **odkaz Můj portál přístupu**.

    Tento odkaz použijete pro další krok.

    ![Přehled přístupu k balíčku – odkaz na portál pro přístup](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-3-request-access"></a>Krok 3: vyžádání přístupu

V tomto kroku provedete kroky jako **interní žadatel** a vyžádáte přístup k balíčku přístupu. Žadatelé odesílají své žádosti pomocí webu s názvem portál přístupu. Portál pro přístup umožňuje žadatelům odesílat požadavky na balíčky přístupu, přečtěte si balíčky přístupu, ke kterým už mají přístup, a zobrazit jejich historii žádostí.

**Požadovaná role:** Interní žadatel

1. Odhlaste se z Azure Portal.

1. V novém okně prohlížeče přejděte na odkaz Můj portál pro přístup, který jste zkopírovali v předchozím kroku.

1. Přihlaste se k portálu pro přístup jako **Requestor1**.

    Měl by se zobrazit balíček pro přístup k **marketingové kampani** .

1. V případě potřeby klikněte ve sloupci **Popis** na šipku a zobrazte podrobnosti o balíčku pro přístup.

    ![Portál přístupu – přístupové balíčky](./media/entitlement-management-shared/my-access-access-packages.png)

1. Klikněte na značku zaškrtnutí a vyberte balíček.

1. Kliknutím na **požádat o přístup** otevřete podokno žádosti o přístup.

    ![Můj portál pro přístup – požádat o přístup k tlačítku](./media/entitlement-management-access-package-first/my-access-request-access-button.png)

1. Do pole **obchodní odůvodnění** zadejte odůvodnění při **práci na nové marketingové kampani**.

    ![Můj portál přístupu – požádat o přístup](./media/entitlement-management-shared/my-access-request-access.png)

1. Klikněte na **Odeslat**.

1. V nabídce vlevo klikněte na **historie požadavků** a ověřte, zda byla žádost odeslána.

## <a name="step-4-validate-that-access-has-been-assigned"></a>Krok 4: ověření, zda byl přiřazen přístup

V tomto kroku ověříte, že **interní žadatel** byl přiřazen k balíčku přístupu a že je teď členem skupiny **marketingových zdrojů** .

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. Odhlaste se z portálu pro přístup k webu.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako **admin1**.

1. Klikněte na **Azure Active Directory** a pak na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na možnost **přístup k balíčkům**.

1. Vyhledejte a klikněte na balíček pro přístup k **marketingové kampani** .

1. V nabídce vlevo klikněte na **požadavky**.

    Měli byste vidět Requestor1 a počáteční zásady se stavem **dodáno**.

1. Kliknutím na žádost zobrazíte podrobnosti o žádosti.

    ![Přístup k balíčku – Podrobnosti žádosti](./media/entitlement-management-access-package-first/request-details.png)

1. V levém navigačním panelu klikněte na **Azure Active Directory**.

1. Klikněte na **skupiny** a otevřete skupinu **marketingových zdrojů** .

1. Klikněte na tlačítko **Členové**.

    Měla by se zobrazit **Requestor1** uvedená jako člen.

    ![Členové marketingových zdrojů](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-5-clean-up-resources"></a>Krok 5: vyčištění prostředků

V tomto kroku odeberete změny, které jste provedli, a odstraníte balíček přístupu k **marketingové kampani** .

**Požadovaná role:**  Globální správce nebo Správce uživatelů

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. Otevřete balíček pro přístup k **marketingové kampani** .

1. Klikněte na **přiřazení**.

1. V případě **Requestor1**klikněte na tlačítko se třemi tečkami (**...**) a pak klikněte na **Odebrat přístup**. Ve zprávě, která se zobrazí, klikněte na tlačítko **Ano**.

    Po chvíli se stav změní ze doručeno na vypršela jeho platnost.

1. Klikněte na **role prostředků**.

1. U **marketingových zdrojů**klikněte na tři tečky (**...**) a pak klikněte na **Odebrat roli prostředku**. Ve zprávě, která se zobrazí, klikněte na tlačítko **Ano**.

1. Otevřete seznam balíčků přístupu.

1. V případě **marketingové kampaně**klikněte na tlačítko se třemi tečkami (**...**) a pak klikněte na **Odstranit**. Ve zprávě, která se zobrazí, klikněte na tlačítko **Ano**.

1. V Azure Active Directory odstraňte všechny uživatele, které jste vytvořili, jako je **Requestor1** a **admin1**.

1. Odstraňte skupinu **marketingových zdrojů** .

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte víc o běžných krocích scénářů v tématu Správa nároků.
> [!div class="nextstepaction"]
> [Typické scénáře](entitlement-management-scenarios.md)
