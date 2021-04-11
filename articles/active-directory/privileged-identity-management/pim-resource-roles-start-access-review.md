---
title: Vytvoření kontroly přístupu pro role prostředků Azure v PIM – Azure AD | Microsoft Docs
description: Naučte se vytvářet kontrolu přístupu k rolím prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 04/05/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87c0ce72348f67c22759915a3a15c69193ad2f60
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552783"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Vytvoření kontroly přístupu pro role prostředků Azure v Privileged Identity Management

Potřeba přístup k privilegovaným rolím prostředků Azure, které zaměstnanci mění v čase. Chcete-li snížit riziko související se zastaralými přiřazeními rolí, měli byste pravidelně kontrolovat přístup. K vytvoření kontrol přístupu pro privilegovaný přístup k rolím prostředků Azure můžete použít Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Můžete také nakonfigurovat opakované kontroly přístupu, ke kterým dochází automaticky. Tento článek popisuje, jak vytvořit jednu nebo více kontrol přístupu.

## <a name="prerequisite-license"></a>Požadovaná licence

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]. Další informace o licencích pro PIM najdete v tématu [licenční požadavky pro použití Privileged Identity Management](subscription-requirements.md).

> [!Note]
>  V současné době můžete určit rozsah kontroly přístupu u instančních objektů s přístupem k Azure AD a k rolím prostředků Azure (Preview) s aktivní verzí služby Azure Active Directory Premium P2 ve vašem tenantovi. Pro účely obecné dostupnosti této funkce bude finalizace licenční model pro instanční objekty a může vyžadovat další licence.

## <a name="prerequisite-role"></a>Požadovaná role

 Chcete-li vytvořit kontroly přístupu, musíte být přiřazeni k roli [vlastníka](../../role-based-access-control/built-in-roles.md#owner) nebo [Správce přístupu uživatele](../../role-based-access-control/built-in-roles.md#user-access-administrator) k roli Azure pro daný prostředek.

## <a name="open-access-reviews"></a>Otevřít recenze přístupů

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s uživatelem, který je přiřazený k jedné z požadovaných rolí.

1. Vybrat zásady **správného řízení identity**
 
1. V nabídce vlevo vyberte **prostředky Azure** v části **Azure AD Privileged Identity Management**.

1. Vyberte prostředek, který chcete spravovat, například předplatné.

1. V části Spravovat vyberte **recenze přístupů**.

    ![Prostředky Azure – seznam kontrol přístupu zobrazuje stav všech revizí](./media/pim-resource-roles-start-access-review/access-reviews.png)

1. Kliknutím na **Nový** vytvořte novou kontrolu přístupu.

1. Pojmenujte kontrolu přístupu. Volitelně můžete zadat popis revize. Název a popis se zobrazí kontrolorům.

    ![Vytvoření kontroly přístupu – kontrola názvu a popisu](./media/pim-resource-roles-start-access-review/name-description.png)

1. Nastavte **počáteční datum**. Ve výchozím nastavení proběhne kontrola přístupu jednou, začíná ve stejnou dobu, kdy je vytvořena, a končí v jednom měsíci. Počáteční a koncové datum můžete změnit tak, aby byla kontrola přístupu zahájena v budoucnosti a poslední, ale kolik dní potřebujete.

    ![Počáteční datum, četnost, doba trvání, konec, počet a datum a čas ukončení](./media/pim-resource-roles-start-access-review/start-end-dates.png)

1. Chcete-li provést opakované kontroly přístupu, změňte nastavení **frekvence** od **jednoho** na **týdně**, **měsíčně**, **čtvrtletně**, **jednou ročně** nebo po měsících. Pomocí posuvníku **Trvání** nebo textového pole můžete definovat, kolik dní bude každá revize opakujících se řad otevřená pro vstup od revidujících. Například maximální doba, kterou můžete nastavit pro měsíční revizi, je 27 dní, aby se předešlo překrývání recenzí.

1. Pomocí nastavení **konec** určete, jak se má ukončit řada kontroly opakovaného přístupu. Série může končit třemi způsoby: průběžně spouští recenze na neomezenou dobu, až do konkrétního data nebo po dokončení definovaného počtu výskytů. Vy, jiný správce nebo jiný globální správce může série po vytvoření zastavit změnou data v **Nastavení**, takže skončí k tomuto datu.

1. V části **Uživatelé** vyberte rozsah revize. Chcete-li zkontrolovat uživatele, vyberte možnost **Uživatelé nebo vyberte (Preview) instanční objekty** pro kontrolu účtů počítačů s přístupem k roli Azure.   

    ![Obor uživatelů pro kontrolu členství v rolích](./media/pim-resource-roles-start-access-review/users.png)


1. V části **Revize členství v roli** vyberte privilegované role Azure, které chcete zkontrolovat. 

    > [!NOTE]
    > - Role, které tady vyberete, zahrnují [trvalé i oprávněné role](../privileged-identity-management/pim-how-to-add-role-to-user.md).
    > - Když vyberete víc než jednu roli, vytvoří se víc kontrol přístupu. Když například vyberete pět rolí, vytvoří se pět samostatných revizí přístupu.
    Pokud vytváříte kontrolu přístupu pro **role Azure AD**, následující příklad uvádí seznam kontrol členství.

    ![V podokně členství zobrazit seznam rolí Azure AD můžete vybrat](./media/pim-resource-roles-start-access-review/review-membership.png)

    Pokud vytváříte kontrolu přístupu pro **role prostředků Azure**, zobrazí se na následujícím obrázku příklad seznamu členů kontroly.

    ![V podokně členství zobrazit seznam rolí prostředků Azure, které můžete vybrat](./media/pim-resource-roles-start-access-review/review-membership-azure-resource-roles.png)

1. V části **revidujícíi** vyberte jednu nebo více lidí pro kontrolu všech uživatelů. Nebo můžete vybrat, aby členové zkontrolovali svůj vlastní přístup.

    ![Seznam revidujících vybraných uživatelů nebo členů (osoba)](./media/pim-resource-roles-start-access-review/reviewers.png)

    - **Vybraní uživatelé** – tuto možnost použijte k určení konkrétního uživatele k dokončení kontroly. Tato možnost je k dispozici bez ohledu na rozsah kontroly a vybrané revidující mohou kontrolovat uživatele a instanční objekty. 
    - **Členové (vlastní)** – tuto možnost použijte, pokud chcete, aby uživatelé zkontrolovali vlastní přiřazení rolí. Tato možnost je dostupná jenom v případě, že je revize vymezená na **uživatele**.
    - **Správce** – tuto možnost použijte, pokud chcete, aby nadřízený uživatel zkontroloval přiřazení role. Tato možnost je dostupná jenom v případě, že je revize vymezená na **uživatele**. Po výběru manažera budete mít také možnost zadat záložního kontrolora. Záložní revidující budou požádáni, aby zkontrolovali uživatele, když v adresáři není zadaný žádný správce. 

### <a name="upon-completion-settings"></a>Nastavení po dokončení

1. Chcete-li určit, co se stane po dokončení kontroly, rozbalte část **nastavení po dokončení** .

    ![Automatické použití nastavení po dokončení a kontrola, že nereagují](./media/pim-resource-roles-start-access-review/upon-completion-settings.png)

1. Pokud chcete automaticky odebrat přístup pro uživatele, kterým bylo odepřeno, nastavte **automatické použití výsledků na prostředek** , aby **bylo možné povolit**. Pokud chcete výsledky použít ručně po dokončení kontroly, nastavte přepínač na **Zakázat**.

1. Seznam **by měl kontrolor bez odpovědi** použít k určení toho, co se stane pro uživatele, kteří kontrolor v rámci období revize nekontroloval. Toto nastavení nemá vliv na uživatele, kteří byli zkontrolováni ručně. Pokud je posledním rozhodnutím kontrolora zamítnutí, bude přístup uživatele odebrán.

    - **Žádná změna** – opuštění přístupu uživatele nezměněné
    - **Odebrání přístupu** – odebrání přístupu uživatele
    - **Schválit přístup** – schválení přístupu uživatele
    - **Využijte doporučení** – Vezměte v úvahu doporučení systému při odepření nebo schvalování trvalého přístupu uživatele.

### <a name="advanced-settings"></a>Rozšířená nastavení

1. Chcete-li zadat další nastavení, rozbalte oddíl **Upřesnit nastavení** .

    ![Rozšířená nastavení pro možnost zobrazit doporučení, vyžadovat důvod při schválení, e-mailová oznámení a připomenutí](./media/pim-resource-roles-start-access-review/advanced-settings.png)

1. Nastavením **Zobrazit doporučení** **umožníte, aby se** recenzenti zobrazovala doporučení k systému na základě informací o přístupu uživatele.

1. Nastavte **vyžadovat důvod schválení** , aby mohl uživatel vyžadovat **, aby kontrolor** zadal důvod schválení.

1. Nastavte e- **mailová oznámení** , která **umožní** , aby služba Azure AD odesílala e-mailová oznámení kontrolorům při zahájení kontroly přístupu a správcům, když se kontrola dokončí.

1. Nastavením **připomenutí** **umožníte** , aby služba Azure AD odesílala připomenutí kontrol přístupu, která nedokončila jejich kontrolu.
1. Obsah e-mailu odeslaného revidujícím se automaticky vygeneruje na základě podrobností o kontrole, jako je například název revize, název prostředku, datum splatnosti atd. Pokud potřebujete způsob, jak sdělit další informace, jako jsou například další pokyny nebo kontaktní údaje, můžete tyto podrobnosti zadat v **dalším obsahu pro e-mail kontrolora** , který bude zahrnut v e-mailech pozvánky a připomenutí odeslaných přiřazeným kontrolorům. Zvýrazněná část je místo, kde se tyto informace zobrazí.

    ![Obsah e-mailu odeslaného revidujícím s nejzajímavější](./media/pim-resource-roles-start-access-review/email-info.png)

## <a name="start-the-access-review"></a>Spustit kontrolu přístupu

Po zadání nastavení pro kontrolu přístupu klikněte na **Spustit**. Kontrola přístupu se zobrazí v seznamu s indikátorem jeho stavu.

![Seznam kontrol přístupu zobrazuje stav zahájené Revize](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Ve výchozím nastavení Azure AD pošle e-mail kontrolorům krátce po zahájení kontroly. Pokud se rozhodnete Neodesílat e-maily Azure AD, nezapomeňte informovat kontrolory, které čekají na dokončení kontroly přístupu. Můžete jim Ukázat pokyny, jak [zkontrolovat přístup k rolím prostředků Azure](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Správa kontroly přístupu

Průběh můžete sledovat, když kontroloři dokončí revize na stránce **Přehled** kontroly přístupu. V adresáři se nezměnila žádná přístupová práva, dokud se [Kontrola nedokončí](pim-resource-roles-complete-access-review.md).

![Stránka s přehledem kontroly přístupu, která zobrazuje podrobnosti o kontrole](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Pokud se jedná o jednorázovou revizi, potom po uplynutí doby kontroly přístupu nebo když správce zastaví kontrolu přístupu, postupujte podle kroků v části [dokončení kontroly přístupu k rolím prostředků Azure](pim-resource-roles-complete-access-review.md) , aby bylo možné zobrazit a použít výsledky.  

Pokud chcete spravovat řadu kontrol přístupu, přejděte na kontrolu přístupu a v části naplánované recenze Najděte nadcházející výskyty a upravte koncové datum nebo přidejte nebo odeberte kontrolory odpovídajícím způsobem.

Na základě vašich výběrů v **nastavení po dokončení** se spustí automatické použití po koncovém datu revize nebo při ručním zastavení kontroly. Stav kontroly se změní ze **dokončených** do mezistavů, jako je **použití** a nakonec na stav **použito**. Měli byste očekávat, že v několika minutách se v případě potřeby odeberou Zakázaní uživatelé z rolí.

## <a name="next-steps"></a>Další kroky

- [Kontrola přístupu k rolím prostředků Azure](pim-resource-roles-perform-access-review.md)
- [Dokončení kontroly přístupu pro role prostředků Azure](pim-resource-roles-complete-access-review.md)
- [Vytvoření kontroly přístupu pro role Azure AD](pim-how-to-start-security-review.md)
