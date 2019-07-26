---
title: Kurz – vytvoření prvního balíčku přístupu ve správě nároků Azure AD (Preview) – Azure Active Directory
description: Podrobný návod, jak vytvořit první balíček přístupu v Azure Active Directory Správa nároků (Preview).
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
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1688651466ba6748e1254c9d33bb24435602868b
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489173"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management-preview"></a>Kurz: Vytvoření prvního balíčku přístupu ve správě nároků ve službě Azure AD (Preview)

> [!IMPORTANT]
> Správa opravňujících k Azure Active Directory (Azure AD) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Správa přístupu ke všem zdrojům prostředků, jako jsou skupiny, aplikace a weby, je důležitou funkcí pro organizace. Chcete zaměstnancům udělit správnou úroveň přístupu, které potřebují k zajištění produktivity, a odebrat svůj přístup, když už ho nepotřebujete.

V tomto kurzu pracujete s Woodgrove bankou jako s správcem IT. Byli jste požádáni o vytvoření balíčku prostředků pro webový projekt, který můžou interní uživatelé požádat o samoobslužný požadavek. Žádosti vyžadují schválení a přístup uživatele vyprší po 30 dnech. Pro účely tohoto kurzu jsou prostředky webového projektu pouze členstvím v jedné skupině, ale může se jednat o kolekci skupin, aplikací nebo webů SharePointu Online.

![Přehled scénáře](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření balíčku pro přístup se skupinou jako prostředku
> * Určení schvalovatele
> * Ukázka, jak může interní uživatel požádat o přístup k balíčku
> * Schválit žádost o přístup

Pokud nemáte licenci Azure AD Premium P2 nebo Enterprise Mobility + Security E5, vytvořte bezplatnou [zkušební verzi Enterprise mobility + Security E5](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1).

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat správu nároků na Azure AD (Preview), musíte mít jednu z těchto licencí:

- Azure AD Premium P2
- Licence pro Enterprise Mobility + Security (EMS) E5

## <a name="step-1-set-up-users-and-group"></a>Krok 1: Nastavení uživatelů a skupin

Adresář prostředků má jeden nebo více prostředků ke sdílení. V tomto kroku vytvoříte skupinu s názvem **Engineering Group** v adresáři Woodgrove Bank, která je cílovým prostředkem pro správu nároků. Nastavili jste také interního žadatele.

**Požadovaná role:** Globální správce nebo Správce uživatelů

![Vytváření uživatelů a skupin](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce nebo Správce uživatelů.  

1. V levém navigačním panelu klikněte na **Azure Active Directory**.

1. Vytvořte nebo nakonfigurujte následující dva uživatele. Můžete použít tyto názvy nebo jiné názvy. **Admin1** může být uživatel, ke kterému jste aktuálně přihlášení.

    | Name | Role adresáře | Popis |
    | --- | --- | --- |
    | **Uživateli** | Globální správce<br/>-nebo-<br/>Omezený správce (Správce uživatelů) | Správce a schvalovatel |
    | **Requestor1** | Uživatel | Interní žadatel |

    Pro účely tohoto kurzu jsou správcem a schvalovatelem stejná osoba, ale obvykle je jeden nebo více uživatelů, kteří mají být schvalovateli.

1. Vytvořte skupinu zabezpečení Azure AD s názvem **Engineering Group** s přiřazeným typem členství .

    Tato skupina bude cílovým prostředkem pro správu nároků. Skupina by měla být prázdná pro členy, kteří mají být spuštěni.

## <a name="step-2-create-an-access-package"></a>Krok 2: Vytvoření balíčku pro přístup

*Balíček pro přístup* je sada všech prostředků, které uživatel potřebuje k práci na projektu nebo k provedení jejich práce. Balíčky přístupu jsou definované v kontejnerech nazývaných katalogy. V tomto kroku vytvoříte **balíček přístupu k webovému projektu** v katalogu **Obecné** .

**Požadovaná role:** Globální správce nebo Správce uživatelů

![Vytvoření balíčku pro přístup](./media/entitlement-management-access-package-first/elm-access-package.png)

1. V Azure Portal klikněte v levém navigačním panelu na možnost **Azure Active Directory**.

1. V nabídce vlevo klikněte na zásady **správného řízení identity** .

1. V nabídce vlevo klikněte na možnost **přístup k balíčkům**.  Pokud se zobrazí **přístup odepřen**, ujistěte se, že je v tomto adresáři přítomná licence Azure AD Premium P2.

1. Klikněte na **nový balíček pro přístup**.

    ![Správa nároků v Azure Portal](./media/entitlement-management-access-package-first/access-packages-list.png)

1. Na kartě **základy** zadejte název balíčku pro přístup k **webovému projektu** a popis **přístupového balíčku pro technický webový projekt**.

1. Ponechejte rozevírací seznam **katalog** nastavený na **Obecné**.

    ![Karta základy na novém přístupovém balíčku](./media/entitlement-management-access-package-first/basics.png)

1. Kliknutím na **Další** otevřete kartu **role prostředků** .

    Na této kartě můžete vybrat oprávnění, která chcete zahrnout do balíčku přístupu.

1. Klikněte na **skupiny**.

1. V podokně vybrat skupiny vyhledejte a vyberte skupinu **technických skupin** , kterou jste vytvořili dříve.

    Ve výchozím nastavení se zobrazují skupiny uvnitř i vně katalogu **Obecné** . Když vyberete skupinu mimo katalog **Obecné** , přidá se do katalogu pro **Obecné** .

    ![Nový balíček přístupu – karta role prostředků](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Kliknutím na **Vybrat** přidejte skupinu do seznamu.

1. V rozevíracím seznamu **role** vyberte **člen**.

    ![Nový balíček přístupu – karta role prostředků](./media/entitlement-management-access-package-first/resource-roles.png)

1. Kliknutím na **Další** otevřete kartu **zásady** .

1. Nastavte přepínač **vytvořit první zásadu** na **pozdější**.

    Zásadu vytvoříte v další části.

    ![Nový balíček pro přístup – karta Zásady](./media/entitlement-management-access-package-first/policy.png)

1. Kliknutím na **Další** otevřete kartu **Revize + vytvořit** .

    ![Nový balíček přístupu – revize + vytvořit kartu](./media/entitlement-management-access-package-first/review-create.png)

1. Zkontrolujte nastavení balíčku přístupu a pak klikněte na **vytvořit**.

    Může se zobrazit zpráva, že balíček pro přístup nebude viditelný pro uživatele, protože katalog ještě není povolený.

    ![Nový přístupový balíček – neviditelná zpráva](./media/entitlement-management-access-package-first/not-visible.png)

1. Klikněte na **OK**.

    Po chvíli byste měli vidět oznámení, že byl balíček pro přístup úspěšně vytvořen.

## <a name="step-3-create-a-policy"></a>Krok 3: Vytvoření zásady

*Zásady* definují pravidla nebo guardrails pro přístup k balíčku přístupu. V tomto kroku vytvoříte zásadu, která umožňuje určitému uživateli v adresáři prostředků žádat o přístup k balíčku. Také určíte, že žádosti musí být schváleny a kdo bude schvalovatelem.

![Vytvoření zásady balíčku přístupu](./media/entitlement-management-access-package-first/elm-access-package-policy.png)

**Požadovaná role:** Globální správce nebo Správce uživatelů

1. V **balíčku pro přístup k webovému projektu**klikněte v levé nabídce na **zásady**.

    ![Seznam zásad přístupu k balíčku](./media/entitlement-management-access-package-first/policies-list.png)

1. Kliknutím na **Přidat zásadu** otevřete vytvořit zásadu.

1. Zadejte název **interní zásady žadatele** a popis **umožní uživatelům v tomto adresáři požádat o přístup k prostředkům webového projektu**.

1. V části **Uživatelé, kteří můžou požádat o přístup** , klikněte na **uživatele v adresáři**.

    ![Vytvořit zásadu](./media/entitlement-management-access-package-first/policy-create.png)

1. Přejděte dolů do části **Vybrat uživatele a skupiny** a klikněte na **Přidat uživatele a skupiny**.

1. V podokně vybrat uživatele a skupiny vyberte uživatele **Requestor1** , který jste vytvořili dříve, a pak klikněte na **Vybrat**.

1. V části **žádost** nastavte **vyžadovat schválení** na **Ano**.

1. V části **Vybrat schvalovatele** klikněte na **Přidat schvalovatele**.

1. V podokně vybrat schvalovatele vyberte **admin1** , který jste vytvořili dříve, a pak klikněte na **Vybrat**.

    Pro účely tohoto kurzu jsou správcem a schvalovatelem stejná osoba, ale vy můžete jako schvalovatele určit jinou osobu.

1. V části **vypršení platnosti** nastavte u **přístupového balíčku vyprší** **počet dní**.

1. Nastavte **platnost přístupu po** **30** dnech.

1. V nabídce **Povolit zásadu**klikněte na **Ano**.

    ![Vytvořit nastavení zásad](./media/entitlement-management-access-package-first/policy-create-settings.png)

1. Kliknutím na **vytvořit** vytvořte **interní zásadu žadatele**.

1. V levé nabídce balíčku pro přístup k webovému projektu klikněte na **Přehled**.

1. Zkopírujte **odkaz Můj portál přístupu**.

    Tento odkaz použijete pro další krok.

    ![Přehled přístupu k balíčku – odkaz na portál pro přístup](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-4-request-access"></a>Krok 4: Vyžádat si přístup

V tomto kroku provedete kroky jako **interní žadatel** a vyžádáte přístup k balíčku přístupu. Žadatelé odesílají své žádosti pomocí webu s názvem portál přístupu. Portál pro přístup umožňuje žadatelům odesílat požadavky na balíčky přístupu, přečtěte si balíčky přístupu, ke kterým už mají přístup, a zobrazit jejich historii žádostí.

**Požadovaná role:** Interní žadatel

1. Odhlaste se z Azure Portal.

1. V novém okně prohlížeče přejděte na odkaz Můj portál pro přístup, který jste zkopírovali v předchozím kroku.

1. Přihlaste se k portálu pro přístup jako **Requestor1**.

    Měl by se zobrazit **balíček pro přístup k webovému projektu**.

1. V případě potřeby klikněte ve sloupci **Popis** na šipku a zobrazte podrobnosti o balíčku pro přístup.

    ![Portál přístupu – přístupové balíčky](./media/entitlement-management-shared/my-access-access-packages.png)

1. Klikněte na značku zaškrtnutí a vyberte balíček.

1. Kliknutím na **požádat o přístup** otevřete podokno žádosti o přístup.

1. Do pole **obchodní odůvodnění** zadejte odůvodnění **práce na webovém projektu**.

1. **U žádosti o konkrétní období** nastavte přepínač na **Ano**.

1. Nastavte **počáteční datum** na dnešek a **koncové datum** na zítra.

    ![Můj portál přístupu – požádat o přístup](./media/entitlement-management-shared/my-access-request-access.png)

1. Klikněte na **Submit** (Odeslat).

1. V nabídce vlevo klikněte na **historie požadavků** a ověřte, zda byla žádost odeslána.

## <a name="step-5-approve-access-request"></a>Krok 5: Schválit žádost o přístup

V tomto kroku se přihlásíte jako uživatel **schvalovatele** a schválíte žádost o přístup internímu žadateli. Schvalovatelé používají stejný portál pro přístup jako žadatelé, kteří používají k odesílání požadavků. Pomocí portálu pro přístup můžou schvalovatelé zobrazit nevyřízená schválení a schvalovat nebo zamítnout žádosti.

**Požadovaná role:** Schvalovatel

1. Odhlaste se z portálu pro přístup k webu.

1. Přihlaste se k [portálu pro přístup](https://myaccess.microsoft.com) jako **admin1**.

1. V nabídce vlevo klikněte na **schválení**.

1. Na kartě **čekání** vyhledejte **Requestor1**.

    Pokud se žádost od Requestor1 nezobrazuje, počkejte pár minut a zkuste to znovu.

1. Kliknutím na odkaz **Zobrazit** otevřete podokno žádost o přístup.

1. Klikněte na tlačítko **schválit**.

1. Do pole **důvod** zadejte důvod **schválení přístupu k webovému projektu**.

    ![Můj portál pro přístup – žádost o přístup](./media/entitlement-management-shared/my-access-approve-request.png)

1. Kliknutím na **Odeslat** odešlete rozhodnutí.

    Měla by se zobrazit zpráva, že byla úspěšně schválena.

## <a name="step-6-validate-that-access-has-been-assigned"></a>Krok 6: Ověřit přiřazení přístupu

Teď, když jste schválili žádost o přístup, v tomto kroku ověříte, že **interní žadatel** byl přiřazen k balíčku přístupu a že je teď členem skupiny **technických skupin** .

**Požadovaná role:** Globální správce nebo Správce uživatelů

1. Odhlaste se z portálu pro přístup k webu.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako **admin1**.

1. Klikněte na **Azure Active Directory** a pak na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na možnost **přístup k balíčkům**.

1. Vyhledejte a klikněte na **balíček přístupu k webovému projektu**.

1. V nabídce vlevo klikněte na **požadavky**.

    Měli byste vidět Requestor1 a zásady interního žadatele se stavem **doručeno**.

1. Kliknutím na žádost zobrazíte podrobnosti o žádosti.

    ![Přístup k balíčku – Podrobnosti žádosti](./media/entitlement-management-access-package-first/request-details.png)

1. V levém navigačním panelu klikněte na **Azure Active Directory**.

1. Klikněte na **skupiny** a otevřete skupinu **Engineering Group** .

1. Klikněte na tlačítko **Členové**.

    Měla by se zobrazit **Requestor1** uvedená jako člen.

    ![Členové skupiny technických skupin](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-7-clean-up-resources"></a>Krok 7: Vyčištění prostředků

V tomto kroku odeberete změny, které jste provedli, a odstraníte balíček přístupu k **balíčku webového projektu** .

**Požadovaná role:**  Globální správce nebo Správce uživatelů

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. Otevřete **balíček pro přístup k webovému projektu**.

1. Klikněte na **přiřazení**.

1. V případě **Requestor1**klikněte na tlačítko se třemi tečkami ( **...** ) a pak klikněte na **Odebrat přístup**.

    Stav se změní z doručené na konec platnosti.

1. Klikněte na **zásady**.

1. U **zásad interního žadatele**klikněte na tlačítko se třemi tečkami ( **...** ) a pak klikněte na **Odstranit**.

1. Klikněte na **role prostředků**.

1. V části **Technická skupina**klikněte na tři tečky ( **...** ) a pak klikněte na **Odebrat roli prostředku**.

1. Otevřete seznam balíčků přístupu.

1. V případě **projektu přístupu k webu**klikněte na tlačítko se třemi tečkami ( **...** ) a pak klikněte na **Odstranit**.

1. V Azure Active Directory odstraňte všechny uživatele, které jste vytvořili, jako je **Requestor1** a **admin1**.

1. Odstraňte skupinu **technických skupin** .

## <a name="next-steps"></a>Další postup

V dalším článku se dozvíte víc o běžných krocích scénářů v tématu Správa nároků.
> [!div class="nextstepaction"]
> [Obvyklé scénáře](entitlement-management-scenarios.md)
