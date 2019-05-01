---
title: Kurz – vytvoření vaší první balíčku přístup ve službě Azure AD oprávnění management (Preview) – Azure Active Directory
description: Podrobný návod k vytvoření vaší první přístup k balíčku ve službě Azure Active Directory management oprávnění (Preview).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 04/27/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 354af736d5896214848205f41e429d9bf2c49863
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64873486"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management-preview"></a>Kurz: Vytvoření vaší první přístup k balíčku ve správě služby Azure AD oprávnění (Preview)

> [!IMPORTANT]
> Správa nároků Azure Active Directory (Azure AD) je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Správa přístupu na všechny prostředky zaměstnanců potřebu, jako jsou skupiny, aplikace a weby, je důležité funkce pro organizace. Chcete poskytnout zaměstnancům vhodnou úroveň přístupu, které potřebují k zajištění vysoké produktivity a odebrat jim přístup, pokud už je nepotřebujete.

V tomto kurzu práci pro banku Woodgrove Bank jako správce IT. Že jste byli požádáni o vytvoření balíčku prostředků pro webový projekt, můžete interní uživatele samoobslužná služba požadavek. Požadavky vyžadovat schválení a po 30 dnech vyprší platnost přístupu uživatele. Pro účely tohoto kurzu webových prostředků projektu jsou pouze členství v jedné skupině, ale může to být kolekci skupin, aplikace nebo weby SharePoint Online.

![Přehled scénáře](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření balíčku přístup ke skupině jako prostředek
> * Určit schvalovatele
> * Ukazují, jak se interní uživatel může požádat o přístup k balíčku
> * Schválit žádost o přístup

Pokud nemáte Azure AD Premium P2 nebo Enterprise Mobility + Security E5 licenci, vytvářet bezplatného [Enterprise Mobility + Security E5 zkušební](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1).

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat správu oprávnění Azure AD (Preview), musí mít jeden z těchto licencí:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 licence

## <a name="step-1-set-up-users-and-group"></a>Krok 1: Nastavit uživatele a skupiny

Adresář prostředků má jeden nebo více prostředků sdílet. V tomto kroku vytvoříte skupinu s názvem **skupiny Technici** v adresáři společnosti Woodgrove Bank, který je cílový prostředek pro správu oprávnění. Můžete také nastavit interní žadatele.

**Požadované role:** Globální správce nebo Správce uživatelů

![Vytvoření uživatelů a skupin](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako globální správce nebo Správce uživatelů.  

1. V levém navigačním panelu klikněte na tlačítko **Azure Active Directory**.

1. Vytvořit nebo konfigurovat následující dva uživatele. Můžete použít tyto názvy nebo jiné názvy. **Admin1** může být aktuálně jste přihlášeni jako uživatel.

    | Název | Role adresáře | Popis |
    | --- | --- | --- |
    | **admin1** | Globální správce<br/>-nebo-<br/>Správce s omezením (Správce uživatelů) | Správce a schvalovatele |
    | **Requestor1** | Uživatel | Interní žadatele |

    Pro účely tohoto kurzu správce a schvalovatele jsou stejné osobě, ale obvykle určit jeden nebo více uživatelů bude schvalovatele.

1. Zabezpečení služby Azure AD vytvořit skupina s názvem **skupiny Technici** s typem členství **přiřazeno**.

    Tato skupina bude cílový prostředek pro správu oprávnění. Skupina by měla být prázdná členů, kterou chcete spustit.

## <a name="step-2-create-an-access-package"></a>Krok 2: Vytvořte si balíček pro přístup

*Přístup k balíčku* je sada všech prostředků, které uživatel potřebuje k práci na projektu nebo provádění své práce. Přístup balíčky jsou definovány v kontejnerech volá *katalogy*. V tomto kroku vytvoříte **webového projektu přístup k balíčku** v **Obecné** katalogu.

**Požadované role:** Globální správce nebo Správce uživatelů

![Vytvořte si balíček pro přístup](./media/entitlement-management-access-package-first/elm-access-package.png)

1. Na webu Azure Portal, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory**.

1. V nabídce vlevo klikněte na tlačítko **zásady správného řízení identit**

1. V nabídce vlevo klikněte na tlačítko **přístup k balíčkům**.  Pokud se zobrazí **přístup byl odepřen**, ujistěte se, že se nachází v tomto adresáři licenci Azure AD Premium P2.

1. Klikněte na tlačítko **nový balíček pro přístup k**.

    ![Správa nároků na webu Azure Portal](./media/entitlement-management-access-package-first/access-packages-list.png)

1. Na **Základy** kartu, zadejte název **webového projektu přístup k balíčku** a popis **přístup k balíčku pro webový projekt Engineering**.

1. Nechte **katalogu** rozevíracího seznamu nastavte na **Obecné**.

    ![Nový balíček přístup – základní informace o kartě](./media/entitlement-management-access-package-first/basics.png)

1. Klikněte na tlačítko **Další** otevřít **role prostředků** kartu.

    Na této kartě vyberte oprávnění k přístupu k balíčku.

1. Klikněte na tlačítko **skupiny**.

1. V podokně vyberte skupiny, vyhledejte a vyberte **skupiny Technici** skupiny, které jste vytvořili dříve.

    Ve výchozím nastavení, se zobrazí skupiny zevnitř i zvenčí **Obecné** katalogu. Když vyberete skupinu mimo **Obecné** katalog, přidá se do **Obecné** katalogu.

    ![Nový balíček přístup - kartu role prostředků](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Klikněte na tlačítko **vyberte** přidání skupiny do seznamu.

1. V **Role** rozevíracího seznamu vyberte **člen**.

    ![Nový balíček přístup - kartu role prostředků](./media/entitlement-management-access-package-first/resource-roles.png)

1. Klikněte na tlačítko **Další** otevřít **zásady** kartu.

1. Nastavte **vytvořte první zásady** přepnutím **později**.

    Zásada se vytvoří v další části.

    ![Nový balíček přístup – karta zásad](./media/entitlement-management-access-package-first/policy.png)

1. Klikněte na tlačítko **Další** otevřít **revize + vytvořit** kartu.

    ![Nový balíček pro přístup – zkontrolovat a vytvořit kartu](./media/entitlement-management-access-package-first/review-create.png)

1. Projděte si nastavení přístupu k balíčku a potom klikněte na tlačítko **vytvořit**.

    Může zobrazit zpráva, že přístup k balíčku nebude viditelné pro uživatele, protože zatím není povolená v katalogu.

    ![Nový balíček přístup – nejsou viditelné v rámci zprávy](./media/entitlement-management-access-package-first/not-visible.png)

1. Klikněte na **OK**.

    Po chvíli byste měli vidět oznámení, že přístup k balíčku byl úspěšně vytvořen.

## <a name="step-3-create-a-policy"></a>Krok 3: Vytvořit zásadu

A *zásady* definuje pravidla nebo ochranného zábradlí pro přístup k balíčku přístup. V tomto kroku vytvoříte zásadu, která umožňuje konkrétního uživatele v adresáři zdrojů požádat o přístup k balíčku. Zadáte také, že požadavky musí být schválen a kdo bude schvalovatele.

![Vytvoření zásad přístupu k balíčku](./media/entitlement-management-access-package-first/elm-access-package-policy.png)

**Požadované role:** Globální správce nebo Správce uživatelů

1. V **webového projektu přístup k balíčku**, v nabídce vlevo klikněte na tlačítko **zásady**.

    ![Seznam zásad přístupu balíčku](./media/entitlement-management-access-package-first/policies-list.png)

1. Klikněte na tlačítko **přidat zásady** otevřete vytvořit zásadu.

1. Zadejte název **interní žadatele zásad** a popis **umožňuje uživatelům v tomto adresáři požádat o přístup k webovým prostředkům projektu**.

1. V **uživatelé, kteří můžou požádat o přístup** klikněte na tlačítko **pro uživatele ve vašem adresáři**.

    ![Vytvoření zásad](./media/entitlement-management-access-package-first/policy-create.png)

1. Přejděte dolů k položce **výběr uživatelů a skupin** části a klikněte na tlačítko **přidávat uživatele a skupiny**.

1. Vybrat uživatele a skupiny, vyberte **Requestor1** uživatele, které jste vytvořili dříve a pak klikněte na tlačítko **vyberte**.

1. V **žádosti** nastavte **vyžadovat schválení** k **Ano**.

1. V **vybrat schvalovatele** klikněte na tlačítko **přidat schvalovatele**.

1. V podokně vyberte schvalovatelé, vyberte **Admin1** vytvořené dříve a pak klikněte na tlačítko **vyberte**.

    Pro účely tohoto kurzu správce a schvalovatele jsou stejné osobě, ale můžete určit jinou osobu jako schvalovatele.

1. V **vypršení platnosti** nastavte **vyprší platnost přístupu k balíčku** k **počet dnů**.

1. Nastavte **po vypršení platnosti přístupu** k **30** dnů.

1. Pro **povolit zásady**, klikněte na tlačítko **Ano**.

    ![Vytvořte nastavení zásad](./media/entitlement-management-access-package-first/policy-create-settings.png)

1. Klikněte na tlačítko **vytvořit** vytvořit **interní žadatele zásad**.

1. V levé nabídce webového projektu přístup k balíčku, klikněte na tlačítko **přehled**.

1. Kopírovat **Moje portálu propojte**.

    Použijete tento odkaz na další krok.

    ![Přehled přístupu k balíčku - Moje propojte portálu](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-4-request-access"></a>Krok 4: Vyžádat si přístup

V tomto kroku provedete kroky jako **interní žadatele** a žádat o přístup k přístupu k balíčku. Jejich požadavky pomocí web s názvem portal tento přístup můžete odesílat žadatele. Na portálu Moje přístup umožňuje žadatele k odeslání žádosti o přístup k balíčkům naleznete v tématu přístup balíčky jsou už mít přístup k a zobrazit historii svých žádostí.

**Požadované role:** Interní žadatele

1. Odhlaste se z webu Azure portal.

1. V novém okně prohlížeče přejděte na Moje přístup portálu odkaz, který jste si zkopírovali v předchozím kroku.

1. Přihlaste se k portálu Můj přístup jako **Requestor1**.

    Měli byste vidět **webového projektu přístup k balíčku**.

1. V případě potřeby v **popis** sloupce, klikněte na šipku zobrazte podrobné informace o přístupu k balíčku.

    ![Moje portál přístup – přístup k balíčkům](./media/entitlement-management-shared/my-access-access-packages.png)

1. Kliknutím na značku zaškrtnutí k vybrání balíčku.

1. Klikněte na tlačítko **žádat o přístup** a otevřete tak podokno žádost o přístup.

1. V **obchodní odůvodnění** zadejte odůvodnění **pracující na webový projekt**.

1. Nastavte **vyžádat pro konkrétní období** přepnutím **Ano**.

1. Nastavte **počáteční datum** do informací o dnešku a **koncové datum** na zítra.

    ![Moje přístup k portálu – žádost o přístup](./media/entitlement-management-shared/my-access-request-access.png)

1. Klikněte na **Submit** (Odeslat).

1. V nabídce vlevo klikněte na tlačítko **historie žádostí** k ověření, že vaše žádost byla odeslána.

## <a name="step-5-approve-access-request"></a>Krok 5: Schválit žádost o přístup

V tomto kroku jste přihlášení jako **schvalovatel** uživatele a schválit žádost o přístup pro interní žadatele. Schvalovatelů použít na stejném portálu Můj přístup žadatele k používání k odesílání žádostí o. Na portálu Moje přístup schvalovatele mohli zobrazení čekajících na schválení a schválit nebo odmítnout požadavky.

**Požadované role:** Schvalovatel

1. Odhlaste se z portálu Můj přístup.

1. Přihlaste se k [portál Můj přístupu](https://myaccess.microsoft.com) jako **Admin1**.

1. V nabídce vlevo klikněte na tlačítko **schválení**.

1. Na **čekající** kartu, Najít **Requestor1**.

    Pokud nevidíte žádost od Requestor1, počkejte několik minut a zkuste to znovu.

1. Klikněte na tlačítko **zobrazení** odkaz a otevřete tak podokno žádost o přístup.

1. Klikněte na tlačítko **schválit**.

1. V **důvod** zadejte důvod **schválení přístupu pro webový projekt**.

    ![Moje přístup k portálu – žádost o přístup](./media/entitlement-management-shared/my-access-approve-request.png)

1. Klikněte na tlačítko **odeslat** odeslat svoje rozhodnutí.

    Měli byste vidět zprávu, že byla položka úspěšně schválena.

## <a name="step-6-validate-that-access-has-been-assigned"></a>Krok 6: Ověřte, že má přiřazenou přístup

Teď, když jste schválili žádost o přístup, v tomto kroku potvrdíte, že **interní žadatele** byl přiřazen přístup k balíčku a, že jsou teď členem **skupiny Technici** skupiny.

**Požadované role:** Globální správce nebo Správce uživatelů

1. Odhlaste se z portálu Můj přístup.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako **Admin1**.

1. Klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. V nabídce vlevo klikněte na tlačítko **přístup k balíčkům**.

1. Vyhledejte a klikněte na tlačítko **webového projektu přístup k balíčku**.

1. V nabídce vlevo klikněte na tlačítko **požadavky**.

    Měli byste vidět Requestor1 a interní žadatele zásad se stavem **dodáno**.

1. Klikněte na požádat o zobrazíte podrobnosti o žádosti.

    ![Přístup k balíčku – podrobnosti o žádosti](./media/entitlement-management-access-package-first/request-details.png)

1. V levém navigačním panelu klikněte na tlačítko **Azure Active Directory**.

1. Klikněte na tlačítko **skupiny** a otevřete **skupiny Technici** skupiny.

1. Klikněte na tlačítko **členy**.

    Měli byste vidět **Requestor1** uvedená jako člena.

    ![Členové skupiny inženýrství](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-7-clean-up-resources"></a>Krok 7: Vyčištění prostředků

V tomto kroku odeberete provedené změny a odstranění **webového projektu přístup k balíčku** přístup k balíčku.

**Požadované role:**  Globální správce nebo Správce uživatelů

1. Na webu Azure Portal, klikněte na tlačítko **Azure Active Directory** a potom klikněte na tlačítko **zásady správného řízení identit**.

1. Otevřít **webového projektu přístup k balíčku**.

1. Klikněte na tlačítko **přiřazení**.

1. Pro **Requestor1**, klikněte na tlačítko se třemi tečkami (**...** ) a potom klikněte na tlačítko **odebrat přístup**.

    Stav se změní z dodáno na platnost vypršela.

1. Klikněte na tlačítko **zásady**.

1. Pro **interní žadatele zásad**, klikněte na tlačítko se třemi tečkami (**...** ) a potom klikněte na tlačítko **odstranit**.

1. Klikněte na tlačítko **role prostředků**.

1. Pro **skupiny Technici**, klikněte na tlačítko se třemi tečkami (**...** ) a potom klikněte na tlačítko **odebrat prostředek role**.

1. Otevře se seznam balíčků přístup.

1. Pro **webového projektu přístup k projektu**, klikněte na tlačítko se třemi tečkami (**...** ) a potom klikněte na tlačítko **odstranit**.

1. V Azure Active Directory, odstraňte všechny uživatele, jako jste vytvořili **Requestor1** a **Admin1**.

1. Odstranit **skupiny Technici** skupiny.

## <a name="next-steps"></a>Další postup

Přejděte k dalším článku se dozvíte o běžných kroky scénáře ve správě oprávnění.
> [!div class="nextstepaction"]
> [Obvyklé scénáře](entitlement-management-scenarios.md)
