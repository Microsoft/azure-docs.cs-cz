---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 04/29/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: 364d4a11772e6bb72e2e258503f3cce49dc61453
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141662"
---
## <a name="create-one-or-more-access-reviews"></a>Vytvořte jeden nebo více kontroly přístupu

1. Klikněte na tlačítko **nový** k vytvoření nové kontroly přístupu.

1. Název kontroly přístupu. Kontrola volitelně zadejte popis. Název a popis se zobrazí pro revidující.

    ![Vytvoření kontroly přístupu – zkontrolujte název a popis](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Nastavte **počáteční datum**. Ve výchozím nastavení kontroly přístupu akce proběhne jednou, spustí stejné chvíli, kdy je vytvořen a končí na jeden měsíc. Můžete změnit počáteční a koncové datum měli přístup zkontrolujte start v budoucnu a poslední představ počtu dnů.

    ![Vytvoření kontroly přístupu – počáteční a koncové datum](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. Chcete-li opakování kontroly přístupu, změnit **frekvence** nastavení z **jednou** k **týdenní**, **měsíční**,  **Čtvrtletní**, **každoročně**, nebo **částečně annually**. Použití **doba trvání** posuvník nebo textového pole k definování, kolik dní budou každou recenzi řady opakovaných otevřete pro vstup od recenzentů. Maximální doba, po kterou můžete nastavit měsíční kontroly je například 27 dní na překrývající se kontroly.

1. Použití **End** nastavení můžete určit způsob ukončení opakování přístupu zkontrolujte řady. Série můžete ukončit třemi způsoby: běží nepřetržitě spuštění kontrol po neomezenou dobu, dokud k určitému datu nebo po dokončení definovaný počet výskytů. Můžete, jiného uživatele správce nebo jiného globálního správce můžete zastavit řady po vytvoření změnou datum v **nastavení**tak, aby ho k tomuto datu ukončení.

1. V **uživatelé** vyberte jednu nebo víc rolí, které chcete zkontrolovat členství.

    ![Vytvoření kontroly přístupu – uživatelé](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > Výběr více než jednu roli vytvoří více kontroly přístupu. Například výběrem pět rolí vytvoří pět samostatnou přístupovou revize.

    Při vytváření kontroly přístupu Azure AD rolí, následuje příklad seznamu kontroly členství.

    ![Vytvoření kontroly přístupu – Zkontrolujte členství v roli](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Při vytváření kontroly přístupu z role prostředků Azure, ukazuje následující příklad seznamu kontroly členství.

    ![Vytvoření kontroly přístupu – Zkontrolujte členství v roli](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. V **revidující** vyberte jednoho nebo víc uživatelů ke kontrole všech uživatelů. Nebo můžete mít členy kontrolovat svůj vlastní přístup.

    ![Vytvoření kontroly přístupu – kontroloři](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Vybraní uživatelé** – tuto možnost použijte, pokud si nejste jisti, který potřebuje přístup. Pomocí této možnosti můžete přiřadit revize vlastník prostředku nebo správce skupiny pro dokončení.
    - **Členové (vlastní)** – pomocí této možnosti můžete mít uživatele, projděte si svoje vlastní přiřazení rolí.

### <a name="upon-completion-settings"></a>Nastavení činností po dokončení

1. Chcete-li určit, co se stane po se kontrola dokončí, rozbalte **nastavení činností po dokončení** oddílu.

    ![Nastavení činností po dokončení](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Pokud nechcete automaticky odebrat přístup pro uživatele, kteří se zamítla nastavte **automaticky zavést výsledky do prostředku** k **povolit**. Pokud chcete aplikovat ručně výsledky, až se kontrola dokončí, nastavte přepínač na **zakázat**.

1. Použití **by neměly odpovídat kontrolor** seznamu k určení, co se stane, že pro uživatele, kteří nejsou zkontrolované recenzentem během období vyhodnocování. Toto nastavení nemá vliv na uživatele, kteří byly zkontrolovány podle revidující ručně. Pokud poslední revidující rozhodnutí je Deny přístup uživatele se odebere.

    - **Žádná změna** – nechte beze změny přístupu uživatele
    - **Odebrat přístup** – odebrání přístupu uživatele
    - **Schválit přístup** -schválit přístup uživatelů
    - **Přijmout doporučení** – využijte doporučení v systému se zamítnout nebo schválení uživatele je nadále přístup

### <a name="advanced-settings"></a>Upřesnit nastavení

1. K zadání dalších nastavení, rozbalte **upřesňující nastavení** oddílu.

    ![Upřesnit nastavení](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Nastavte **zobrazení doporučení** k **povolit** zobrazíte revidující systému doporučení na základě daného uživatele přístup k informacím.

1. Nastavte **vyžadovat důvod při schválení** k **povolit** chcete požadovat, aby kontrolor odůvodnil schválení.

1. Nastavte **Mail notifications** k **povolit** s Azure AD posílala e-mailová oznámení kontrolorům, když začne kontrola přístupu a správcům, když se kontrola dokončí.

1. Nastavte **připomenutí** k **povolit** bude Azure AD posílala připomenutí kontroly přístupu v průběhu kontrolorům, kteří nedokončili svoji kontrolu.
