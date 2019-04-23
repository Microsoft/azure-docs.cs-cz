---
title: Vytvoření kontroly přístupu skupiny nebo aplikace – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit kontrolu přístupu členů skupiny nebo přístupu k aplikacím v kontrol přístupu Azure Active Directory.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/01/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 804efa6e0a39e009e18bbb9dec5ad1638a163597
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60247144"
---
# <a name="create-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Vytvoření kontroly přístupu skupin nebo kontrol přístupu aplikací ve službě Azure AD

Přístup k skupin a aplikací pro zaměstnance a hosté mění v průběhu času. Chcete-li snížit riziko spojené s přiřazení zastaralé přístupu, mohou správci služby Azure Active Directory (Azure AD) k vytvoření kontroly přístupu pro členy skupiny nebo přístupu k aplikacím. Pokud je potřeba pravidelně kontrolujte přístup, můžete také vytvořit opakované kontroly přístupu. Další informace o těchto scénářích najdete v tématu [spravovat přístup uživatelů](manage-user-access-with-access-reviews.md) a [spravovat přístup hosta](manage-guest-access-with-access-reviews.md).

Tento článek popisuje, jak vytvořit jeden nebo více kontroly přístupu pro členy skupiny nebo přístupu k aplikacím.

## <a name="prerequisites"></a>Požadavky

- [Kontroly přístupu povolena](access-reviews-overview.md)
- Globální správce nebo Správce uživatelů

## <a name="create-one-or-more-access-reviews"></a>Vytvořte jeden nebo více kontroly přístupu

1. Přihlaste se k Azure portal a otevřete [stránku kontrol přístupu](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. V nabídce vlevo klikněte na tlačítko **kontrol přístupu**.

1. Klikněte na tlačítko **nová kontrola přístupu** k vytvoření nové kontroly přístupu.

    ![Kontroly přístupu – ovládací prvky](./media/create-access-review/access-reviews.png)

1. Název kontroly přístupu. Kontrola volitelně zadejte popis. Název a popis se zobrazí pro revidující.

    ![Vytvoření kontroly přístupu – zkontrolujte název a popis](./media/create-access-review/name-description.png)

1. Nastavte **počáteční datum**. Ve výchozím nastavení kontroly přístupu akce proběhne jednou, spustí stejné chvíli, kdy je vytvořen a končí na jeden měsíc. Můžete změnit počáteční a koncové datum měli přístup zkontrolujte start v budoucnu a poslední představ počtu dnů.

    ![Vytvoření kontroly přístupu – počáteční a koncové datum](./media/create-access-review/start-end-dates.png)

1. Chcete-li opakování kontroly přístupu, změnit **frekvence** nastavení z **jednou** k **týdenní**, **měsíční**,  **Čtvrtletní** nebo **každoročně**. Použití **doba trvání** posuvník nebo textového pole k definování, kolik dní budou každou recenzi řady opakovaných otevřete pro vstup od recenzentů. Maximální doba, po kterou můžete nastavit měsíční kontroly je například 27 dní na překrývající se kontroly.

1. Použití **End** nastavení můžete určit způsob ukončení opakování přístupu zkontrolujte řady. Série můžete ukončit třemi způsoby: běží nepřetržitě spuštění kontrol po neomezenou dobu, dokud k určitému datu nebo po dokončení definovaný počet výskytů. Můžete, jiného uživatele správce nebo jiného globálního správce můžete zastavit řady po vytvoření změnou datum v **nastavení**tak, aby ho k tomuto datu ukončení.

1. V **uživatelé** části, zadejte uživatele, pro které platí kontroly přístupu. Kontroly přístupu lze pro členy skupiny nebo uživatelé, kteří byly přiřazeny k aplikaci. Můžete dále obor přístupu pouze kontroly kontrola uživatelů typu Host kteří jsou členy (nebo přiřazené k aplikaci), namísto kontroly všech uživatelů, kteří jsou členy nebo kteří mají přístup k aplikaci.

    ![Vytvoření kontroly přístupu – uživatelé](./media/create-access-review/users.png)

1. V **skupiny** vyberte jednu nebo více skupin, které chcete zkontrolovat členství.

    > [!NOTE]
    > Výběr více než jedné skupině vytvoří více kontroly přístupu. Například výběrem pět skupin vytvoří pět samostatnou přístupovou revize.
    
    ![Vytvoření kontroly přístupu – vyberte skupinu](./media/create-access-review/select-group.png)

1. V **aplikací** části (Pokud jste vybrali **přidružených k aplikaci** v kroku 8), vyberte aplikace, které chcete kontrolovat přístup k.

    > [!NOTE]
    > Výběr více než jednu aplikaci vytvoří více kontroly přístupu. Například výběrem pět aplikace vytvoří pět samostatnou přístupovou revize.
    
    ![Vytvoření kontroly přístupu - výběr aplikace](./media/create-access-review/select-application.png)

1. V **revidující** vyberte jeden nebo více lidem zkontrolovat všechny uživatele v oboru. Nebo můžete mít členy kontrolovat svůj vlastní přístup. Pokud prostředek je skupina, můžete požádat vlastníci skupiny ke kontrole. Také můžete vyžadovat, aby revidující zadejte důvod, proč při jejich schválit přístup.

    ![Vytvoření kontroly přístupu – kontroloři](./media/create-access-review/reviewers.png)

1. V **programy** vyberte program, který chcete použít. Jak sledovat a shromažďovat kontroly přístupu pro různé účely jejich uspořádáním do programů může zjednodušit. **Výchozí Program** je vždy k dispozici, nebo můžete vytvořit v jiné aplikaci. Například můžete mít jeden program pro každou dodržování předpisů iniciativy nebo obchodní cíle.

    ![Vytvoření kontroly přístupu – programy](./media/create-access-review/programs.png)

### <a name="upon-completion-settings"></a>Nastavení činností po dokončení

1. Chcete-li určit, co se stane po se kontrola dokončí, rozbalte **nastavení činností po dokončení** oddílu.

    ![Nastavení činností po dokončení](./media/create-access-review/upon-completion-settings.png)

1. Pokud nechcete automaticky odebrat přístup pro uživatele, kteří se zamítla nastavte **automaticky zavést výsledky do prostředku** k **povolit**. Pokud chcete aplikovat ručně výsledky, až se kontrola dokončí, nastavte přepínač na **zakázat**.

1. Použití **by neměly odpovídat kontrolor** seznamu k určení, co se stane, že pro uživatele, kteří nejsou zkontrolované recenzentem během období vyhodnocování. Toto nastavení nemá vliv na uživatele, kteří byly zkontrolovány podle revidující ručně. Pokud poslední revidující rozhodnutí je Deny přístup uživatele se odebere.

    - **Žádná změna** – nechte beze změny přístupu uživatele
    - **Odebrat přístup** – odebrání přístupu uživatele
    - **Schválit přístup** -schválit přístup uživatelů
    - **Přijmout doporučení** – využijte doporučení v systému se zamítnout nebo schválení uživatele je nadále přístup

### <a name="advanced-settings"></a>Upřesnit nastavení

1. K zadání dalších nastavení, rozbalte **upřesňující nastavení** oddílu.

    ![Upřesnit nastavení](./media/create-access-review/advanced-settings.png)

1. Nastavte **zobrazení doporučení** k **povolit** zobrazíte revidující systému doporučení na základě daného uživatele přístup k informacím.

1. Nastavte **vyžadovat důvod při schválení** k **povolit** chcete požadovat, aby kontrolor odůvodnil schválení.

1. Nastavte **Mail notifications** k **povolit** s Azure AD posílala e-mailová oznámení kontrolorům, když začne kontrola přístupu a správcům, když se kontrola dokončí.

1. Nastavte **připomenutí** k **povolit** bude Azure AD posílala připomenutí kontroly přístupu v průběhu kontrolorům, kteří nedokončili svoji kontrolu.

## <a name="start-the-access-review"></a>Zahájení kontroly přístupu

Po zadání nastavení pro kontroly přístupu, klikněte na tlačítko **Start**. Kontroly přístupu se zobrazí v seznamu se indikátor stavu.

![Seznam kontrol přístupu](./media/create-access-review/access-reviews-list.png)

Ve výchozím nastavení Azure AD pošle e-mail revidující krátce po spuštění kontroly. Pokud se rozhodnete odeslat e-mailu se službou Azure AD, nezapomeňte informovat revidující, které čeká na všesměrově jejich dokončení kontroly přístupu. Je možné zobrazit pokyny k [kontrolovat přístup skupinám nebo aplikacím](perform-access-review.md). Pokud kontrolu pro hosty kontrolovat svůj vlastní přístup, je zobrazit pokyny k [zkontrolujte přístup skupinám nebo aplikacím](review-your-access.md).

Pokud jsou některé z revidující hosté, hosté upozorněni prostřednictvím e-mailu jenom v případě, že jste již přijetí svou pozvánku.

## <a name="manage-the-access-review"></a>Správa kontroly přístupu.

Průběh můžete sledovat, jak recenzenti dokončí své recenze **přehled** stránky kontroly přístupu. Žádné přístupová práva jsou změněny v adresáři, dokud [se kontrola dokončí](complete-access-review.md).

![Průběh kontroly přístupu](./media/create-access-review/overview-progress.png)

Pokud je to jednorázové kontroly, po období kontroly přístupu je nad nebo správce zastavení kontroly přístupu, postupujte podle pokynů v [dokončení kontroly přístupu skupinám nebo aplikacím](complete-access-review.md) chcete zobrazit a použít výsledky.  

Ke správě řadu přístup kontroly, přejděte na kontrolu přístupu a budete najít připravované výskyty v naplánované kontroly a upravit koncové datum nebo přidání nebo odebrání revidujících odpovídajícím způsobem.

Na základě vašeho výběru v **nastavení činností po dokončení**, se automaticky použít se spustí po koncové datum kontrola nebo když ručně zastavte kontrolu. Stav kontroly se změní z **dokončeno** prostřednictvím průběžných stavů například **použití** a nakonec do stavu **použito**. Měli byste očekávat zobrazíte zamítnutým uživatelům, pokud existuje, odebírán z přiřazení skupiny členství nebo aplikaci za několik minut.

## <a name="create-reviews-via-apis"></a>Vytvoření kontroly prostřednictvím rozhraní API

Můžete také vytvořit kontrolu přístupu pomocí rozhraní API. K čemu ke správě přístupu zkontroluje skupin a uživatelů aplikace na webu Azure Portal je možné provést pomocí rozhraní Microsoft Graph API. Další informace najdete v tématu [kontroly přístupu Azure AD, reference k rozhraní API](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta). Ukázku kódu naleznete v tématu [kontroly příklad načítání přístup služby Azure AD prostřednictvím Microsoft Graphu](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Další postup

- [Zkontrolovat přístup do skupiny nebo aplikace](perform-access-review.md)
- [Zkontrolovat přístup do skupiny nebo aplikace](review-your-access.md)
- [Dokončení kontroly přístupu skupiny nebo aplikace](complete-access-review.md)
