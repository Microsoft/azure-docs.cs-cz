---
title: Vytvoření kontroly přístupu rolí Azure AD v PIM – Azure AD | Microsoft Docs
description: Naučte se vytvářet kontrolu přístupu pro role Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 3/16/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 310122177d4bd1603f5f498aa2a51620eeda4a20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592740"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Vytvoření kontroly přístupu rolí Azure AD v Privileged Identity Management

Chcete-li snížit riziko související se zastaralými přiřazeními rolí, měli byste pravidelně kontrolovat přístup. K vytváření kontrol přístupu pro privilegované role Azure AD můžete použít Azure AD Privileged Identity Management (PIM). Můžete také nakonfigurovat opakované kontroly přístupu, ke kterým dochází automaticky.

Tento článek popisuje, jak vytvořit jednu nebo více kontrol přístupu pro privilegované role Azure AD.

## <a name="prerequisites"></a>Požadavky

[Správce privilegovaných rolí](../roles/permissions-reference.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Otevřít recenze přístupů

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s uživatelem, který je členem role správce privilegovaných rolí.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **role Azure AD**.

1. V části Spravovat vyberte kontroly **přístupu** a pak vyberte **Nový**.

    ![Role Azure AD – seznam kontrol přístupu zobrazuje stav všech revizí](./media/pim-how-to-start-security-review/access-reviews.png)

Kliknutím na **Nový** vytvořte novou kontrolu přístupu.

1. Pojmenujte kontrolu přístupu. Volitelně můžete zadat popis revize. Název a popis se zobrazí kontrolorům.

    ![Vytvoření kontroly přístupu – kontrola názvu a popisu](./media/pim-how-to-start-security-review/name-description.png)

1. Nastavte **počáteční datum**. Ve výchozím nastavení proběhne kontrola přístupu jednou, začíná ve stejnou dobu, kdy je vytvořena, a končí v jednom měsíci. Počáteční a koncové datum můžete změnit tak, aby byla kontrola přístupu zahájena v budoucnosti a poslední, ale kolik dní potřebujete.

    ![Počáteční datum, četnost, doba trvání, konec, počet a datum a čas ukončení](./media/pim-how-to-start-security-review/start-end-dates.png)

1. Chcete-li provést opakované kontroly přístupu, změňte nastavení **frekvence** od **jednoho** na **týdně**, **měsíčně**, **čtvrtletně**, **jednou ročně** nebo po měsících. Pomocí posuvníku **Trvání** nebo textového pole můžete definovat, kolik dní bude každá revize opakujících se řad otevřená pro vstup od revidujících. Například maximální doba, kterou můžete nastavit pro měsíční revizi, je 27 dní, aby se předešlo překrývání recenzí.

1. Pomocí nastavení **konec** určete, jak se má ukončit řada kontroly opakovaného přístupu. Série může končit třemi způsoby: průběžně spouští recenze na neomezenou dobu, až do konkrétního data nebo po dokončení definovaného počtu výskytů. Vy, jiný správce nebo jiný globální správce může série po vytvoření zastavit změnou data v **Nastavení**, takže skončí k tomuto datu.

1. V části **Uživatelé** vyberte jednu nebo více rolí, u kterých chcete zkontrolovat členství.

    ![Obor uživatelů pro kontrolu členství v rolích](./media/pim-how-to-start-security-review/users.png)

    > [!NOTE]
    > - Role, které tady vyberete, zahrnují [trvalé i oprávněné role](../privileged-identity-management/pim-how-to-add-role-to-user.md).
    > - Když vyberete víc než jednu roli, vytvoří se víc kontrol přístupu. Když například vyberete pět rolí, vytvoří se pět samostatných revizí přístupu.
    > - Pro role, které mají přiřazené skupiny, se přístup ke každé skupině propojené s rolí v rámci revize bude kontrolovat jako součást kontroly přístupu.
    Pokud vytváříte kontrolu přístupu pro **role Azure AD**, následující příklad uvádí seznam kontrol členství.

    ![V podokně členství zobrazit seznam rolí Azure AD můžete vybrat](./media/pim-how-to-start-security-review/review-membership.png)

    Pokud vytváříte kontrolu přístupu pro **role prostředků Azure**, zobrazí se na následujícím obrázku příklad seznamu členů kontroly.

    ![V podokně členství zobrazit seznam rolí prostředků Azure, které můžete vybrat](./media/pim-how-to-start-security-review/review-membership-azure-resource-roles.png)

1. V části **revidujícíi** vyberte jednu nebo více lidí pro kontrolu všech uživatelů. Nebo můžete vybrat, aby členové zkontrolovali svůj vlastní přístup.

    ![Seznam revidujících vybraných uživatelů nebo členů (osoba)](./media/pim-how-to-start-security-review/reviewers.png)

    - **Vybraní uživatelé** – tuto možnost použijte, Pokud nevíte, kdo potřebuje přístup. Pomocí této možnosti můžete k dokončení této revize přiřadit vlastníka prostředku nebo správce skupin.
    - **Členové (vlastní)** – tuto možnost použijte, pokud chcete, aby uživatelé zkontrolovali vlastní přiřazení rolí. Skupiny přiřazené k roli nebudou součástí kontroly, pokud je tato možnost vybraná.
    - **Správce** – tuto možnost použijte, pokud chcete, aby nadřízený uživatel zkontroloval přiřazení role. Po výběru manažera budete mít také možnost zadat záložního kontrolora. Záložní revidující budou požádáni, aby zkontrolovali uživatele, když v adresáři není zadaný žádný správce. Skupiny přiřazené k roli budou přezkoumány záložním kontrolorem, pokud je vybraný. 

### <a name="upon-completion-settings"></a>Nastavení po dokončení

1. Chcete-li určit, co se stane po dokončení kontroly, rozbalte část **nastavení po dokončení** .

    ![Automatické použití nastavení po dokončení a kontrola, že nereagují](./media/pim-how-to-start-security-review/upon-completion-settings.png)

1. Pokud chcete automaticky odebrat přístup pro uživatele, kterým bylo odepřeno, nastavte **automatické použití výsledků na prostředek** , aby **bylo možné povolit**. Pokud chcete výsledky použít ručně po dokončení kontroly, nastavte přepínač na **Zakázat**.

1. Seznam **by měl kontrolor bez odpovědi** použít k určení toho, co se stane pro uživatele, kteří kontrolor v rámci období revize nekontroloval. Toto nastavení nemá vliv na uživatele, kteří byli zkontrolováni ručně. Pokud je posledním rozhodnutím kontrolora zamítnutí, bude přístup uživatele odebrán.

    - **Žádná změna** – opuštění přístupu uživatele nezměněné
    - **Odebrání přístupu** – odebrání přístupu uživatele
    - **Schválit přístup** – schválení přístupu uživatele
    - **Využijte doporučení** – Vezměte v úvahu doporučení systému při odepření nebo schvalování trvalého přístupu uživatele.

### <a name="advanced-settings"></a>Rozšířená nastavení

1. Chcete-li zadat další nastavení, rozbalte oddíl **Upřesnit nastavení** .

    ![Rozšířená nastavení pro možnost zobrazit doporučení, vyžadovat důvod při schválení, e-mailová oznámení a připomenutí](./media/pim-how-to-start-security-review/advanced-settings.png)

1. Nastavením **Zobrazit doporučení** **umožníte, aby se** recenzenti zobrazovala doporučení k systému na základě informací o přístupu uživatele.

1. Nastavte **vyžadovat důvod schválení** , aby mohl uživatel vyžadovat **, aby kontrolor** zadal důvod schválení.

1. Nastavte e- **mailová oznámení** , která **umožní** , aby služba Azure AD odesílala e-mailová oznámení kontrolorům při zahájení kontroly přístupu a správcům, když se kontrola dokončí.

1. Nastavením **připomenutí** **umožníte** , aby služba Azure AD odesílala připomenutí kontrol přístupu, která nedokončila jejich kontrolu.
1. Obsah e-mailu odeslaného revidujícím se automaticky vygeneruje na základě podrobností o kontrole, jako je například název revize, název prostředku, datum splatnosti atd. Pokud potřebujete způsob, jak sdělit další informace, jako jsou například další pokyny nebo kontaktní údaje, můžete tyto podrobnosti zadat v **dalším obsahu pro e-mail kontrolora** , který bude zahrnut v e-mailech pozvánky a připomenutí odeslaných přiřazeným kontrolorům. Zvýrazněná část je místo, kde se tyto informace zobrazí.

    ![Obsah e-mailu odeslaného revidujícím s nejzajímavější](./media/pim-how-to-start-security-review/email-info.png)

## <a name="start-the-access-review"></a>Spustit kontrolu přístupu

Po zadání nastavení pro kontrolu přístupu vyberte **Spustit**. Kontrola přístupu se zobrazí v seznamu s indikátorem jeho stavu.

![Seznam kontrol přístupu zobrazuje stav spuštěných revizí](./media/pim-how-to-start-security-review/access-reviews-list.png)

Ve výchozím nastavení Azure AD pošle e-mail kontrolorům krátce po zahájení kontroly. Pokud se rozhodnete Neodesílat e-maily Azure AD, nezapomeňte informovat kontrolory, které čekají na dokončení kontroly přístupu. Můžete jim Ukázat pokyny, jak [zkontrolovat přístup k rolím Azure AD](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Správa kontroly přístupu

Průběh můžete sledovat, když kontroloři dokončí revize na stránce **Přehled** kontroly přístupu. V adresáři se nezměnila žádná přístupová práva, dokud se [Kontrola nedokončí](pim-how-to-complete-review.md).

![Stránka s přehledem kontroly přístupu, která zobrazuje podrobnosti o kontrole](./media/pim-how-to-start-security-review/access-review-overview.png)

Pokud se jedná o jednorázovou revizi, potom po uplynutí doby kontroly přístupu nebo když správce zastaví kontrolu přístupu, postupujte podle kroků v části [dokončení kontroly přístupu rolí Azure AD](pim-how-to-complete-review.md) , které vám umožní zobrazit a použít výsledky.  

Pokud chcete spravovat řadu kontrol přístupu, přejděte na kontrolu přístupu a v části naplánované recenze Najděte nadcházející výskyty a upravte koncové datum nebo přidejte nebo odeberte kontrolory odpovídajícím způsobem.

Na základě vašich výběrů v **nastavení po dokončení** se spustí automatické použití po koncovém datu revize nebo při ručním zastavení kontroly. Stav kontroly se změní ze **dokončených** do mezistavů, jako je **použití** a nakonec na stav **použito**. Měli byste očekávat, že v několika minutách se v případě potřeby odeberou Zakázaní uživatelé z rolí.

## <a name="next-steps"></a>Další kroky

- [Kontrola přístupu k rolím Azure AD](pim-how-to-perform-security-review.md)
- [Dokončení kontroly přístupu pro role Azure AD](pim-how-to-complete-review.md)
- [Vytvoření kontroly přístupu pro role prostředků Azure](pim-resource-roles-start-access-review.md)
