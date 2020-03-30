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
ms.openlocfilehash: d791c4ba46587ac5709d72cb31bc76f087118b03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67476254"
---
## <a name="create-one-or-more-access-reviews"></a>Vytvoření jedné nebo více kontrol přístupu

1. Chcete-li vytvořit novou kontrolu přístupu, klepněte na **tlačítko Nový.**

1. Pojmenujte kontrolu přístupu. Volitelně uveďte popis recenze. Recenzentům se zobrazí název a popis.

    ![Vytvoření kontroly přístupu – název a popis kontroly](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Nastavte **počáteční datum**. Ve výchozím nastavení dojde k kontrole přístupu jednou, spustí se ve stejnou dobu, kdy je vytvořena, a končí za jeden měsíc. Můžete změnit počáteční a koncové datum tak, aby kontrola přístupu začala v budoucnu a trvala však mnoho dní, kolik chcete.

    ![Počáteční datum, četnost, doba trvání, konec, počet opakování a koncové datum](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. Chcete-li, aby se kontrola přístupu opakovala, změňte nastavení **Frekvence** z **Jednou** týdně na **Týdenní**, **Měsíční**, **Čtvrtletní**, **Ročně**nebo **Pololetně**. Pomocí posuvníku Nebo textového pole **Doba** trvání definujte, kolik dní bude každá recenze opakované řady otevřena pro vstup od recenzentů. Maximální doba trvání, kterou můžete nastavit pro měsíční kontrolu, je například 27 dní, aby se zabránilo překrývání recenzí.

1. Pomocí nastavení **Konec** určete, jak ukončit řadu opakování kontroly přístupu. Řada může končit třemi způsoby: běží nepřetržitě a spouští recenze neomezeně dlouho, až do určitého data nebo po dokončení definovaného počtu výskytů. Vy, jiný správce uživatele nebo jiný globální správce můžete zastavit řadu po vytvoření změnou data v **nastavení**tak, aby k tomuto datu.

1. V části **Uživatelé** vyberte jednu nebo více rolí, ve kterých chcete zkontrolovat členství.

    ![Rozsah uživatelů pro kontrolu členství v rolích](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > Výběrem více než jedné role vytvoříte více kontrol přístupu. Například výběrem pěti rolí vytvoříte pět samostatných kontrol přístupu.

    Pokud vytváříte kontrolu přístupu rolí Azure AD, následující ukazuje příklad seznamu členství revize.

    ![Zkontrolovat podokno členství se seznamem rolí Azure AD, které můžete vybrat](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Pokud vytváříte kontrolu přístupu rolí prostředků Azure, následující ukazuje příklad seznamu členství revize.

    ![Zkontrolovat podokno členství se seznamem rolí prostředků Azure, které můžete vybrat](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. V části **Recenzenti** vyberte jednoho nebo více lidí, kteří zkontrolují všechny uživatele. Nebo můžete vybrat, zda mají členové zkontrolovat svůj vlastní přístup.

    ![Seznam recenzentů vybraných uživatelů nebo členů (vlastní)](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Vybraní uživatelé** – Tuto možnost použijte, pokud nevíte, kdo potřebuje přístup. Pomocí této možnosti můžete recenzi přiřadit vlastníkovi prostředku nebo správci skupiny, který má být dokončen.
    - **Členové (vlastní)** – Pomocí této možnosti můžete, aby uživatelé zkontrolovali svá přiřazení vlastních rolí.

### <a name="upon-completion-settings"></a>Po dokončení nastavení

1. Chcete-li určit, co se stane po dokončení kontroly, rozbalte část **Po dokončení nastavení.**

    ![Po dokončení nastavení auto použít a měl by zkontrolovat nereaguje](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Chcete-li automaticky odebrat přístup uživatelům, kterým byl odepřen přístup, nastavte **možnost Automaticky použít výsledky na prostředek** na **povolit**. Pokud chcete výsledky po dokončení kontroly použít ručně, nastavte přepínač na **Zakázat**.

1. Pomocí seznamu **By recenzent neodpovídá** určit, co se stane pro uživatele, které nejsou kontrolovány recenzenta v období revize. Toto nastavení nemá vliv na uživatele, kteří byli kontrolováni recenzenty ručně. Pokud je konečné rozhodnutí recenzenta Odepřít, bude přístup uživatele odebrán.

    - **Žádná změna** – Ponechat přístup uživatele beze změny
    - **Odebrat přístup** – odebrání přístupu uživatele
    - **Schválit přístup** – schválení přístupu uživatele
    - **Přijmout doporučení** – přijmout doporučení systému o odepření nebo schválení trvalého přístupu uživatele

### <a name="advanced-settings"></a>Upřesnit nastavení

1. Chcete-li zadat další nastavení, rozbalte oddíl **Upřesnit nastavení.**

    ![Pokročilá nastavení pro zobrazení doporučení, vyžadovat důvod schválení, oznámení pošty a připomenutí](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Nastavte **zobrazit doporučení** **povolit,** chcete-li recenzentům zobrazit systémová doporučení založená na přístupových informacích uživatele.

1. Nastavte **Vyžadovat důvod při schvalování,** **chcete-li povolit,** aby recenzent mohl zadat důvod schválení.

1. Nastavte **oznámení pošty** **na Povolit,** aby Azure AD odesílat e-mailová oznámení recenzentům při spuštění kontroly přístupu a správcům po dokončení kontroly.

1. Nastavte **připomenutí** **na Povolit,** aby Azure AD odesílat připomenutí kontrolpřístupu probíhá recenzentům, kteří nedokončili svou kontrolu.
