---
title: Vytvoření kontroly přístupu skupin & aplikací – Azure AD
description: Přečtěte si, jak vytvořit kontrolu přístupu členů skupiny nebo přístupu k aplikacím v recenzích přístupu k Azure Active Directory.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/08/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e01951b6147cfc39fe6c46035db822071bda3aa
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984062"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Vytvoření kontroly přístupu skupin a aplikací v recenzích přístupu azure ad

Přístup ke skupinám a aplikacím pro zaměstnance a hosty se v průběhu času mění. Chcete-li snížit riziko spojené s zastaralými přiřazení přístupu, správci můžete použít Azure Active Directory (Azure AD) k vytvoření kontroly přístupu pro členy skupiny nebo přístup k aplikacím. Pokud potřebujete pravidelně kontrolovat přístup, můžete také vytvořit opakované kontroly přístupu. Další informace o těchto scénářích naleznete v [tématech Správa přístupu uživatelů](manage-user-access-with-access-reviews.md) a [Správa přístupu k hostu](manage-guest-access-with-access-reviews.md).

Tento článek popisuje, jak vytvořit jednu nebo více kontrol přístupu pro členy skupiny nebo přístup k aplikaci.

## <a name="prerequisites"></a>Požadavky

- Azure AD Premium P2
- Globální správce nebo správce uživatelů

Další informace naleznete v tématu [Licenční požadavky](access-reviews-overview.md#license-requirements).

## <a name="create-one-or-more-access-reviews"></a>Vytvoření jedné nebo více kontrol přístupu

1. Přihlaste se k portálu Azure a otevřete [stránku Zásad správného řízení identit](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. V levé nabídce klikněte na **Access reviews**.

1. Kliknutím na **Nová kontrola přístupu** vytvořte novou kontrolu přístupu.

    ![Podokno Kontroly přístupu v řízení identity](./media/create-access-review/access-reviews.png)

1. Pojmenujte kontrolu přístupu. Volitelně uveďte popis recenze. Recenzentům se zobrazí název a popis.

    ![Vytvoření kontroly přístupu – název a popis kontroly](./media/create-access-review/name-description.png)

1. Nastavte **počáteční datum**. Ve výchozím nastavení dojde k kontrole přístupu jednou, spustí se ve stejnou dobu, kdy je vytvořena, a končí za jeden měsíc. Můžete změnit počáteční a koncové datum tak, aby kontrola přístupu začala v budoucnu a trvala však mnoho dní, kolik chcete.

    ![Vytvoření kontroly přístupu – počáteční a koncové datum](./media/create-access-review/start-end-dates.png)

1. Chcete-li, aby se kontrola přístupu opakovala, změňte nastavení **Frekvence** z **Jednou** týdně na **Týdenní**, **Měsíční**, **Čtvrtletní**, **Pololetně**nebo **Ročně**. Pomocí posuvníku Nebo textového pole **Doba** trvání definujte, kolik dní bude každá recenze opakované řady otevřena pro vstup od recenzentů. Maximální doba trvání, kterou můžete nastavit pro měsíční kontrolu, je například 27 dní, aby se zabránilo překrývání recenzí.

1. Pomocí nastavení **Konec** určete, jak ukončit řadu opakování kontroly přístupu. Řada může končit třemi způsoby: běží nepřetržitě a spouští recenze neomezeně dlouho, až do určitého data nebo po dokončení definovaného počtu výskytů. Vy, jiný správce uživatele nebo jiný globální správce můžete zastavit řadu po vytvoření změnou data v **nastavení**tak, aby k tomuto datu.

1. V části **Uživatelé** zadejte uživatele, na které se vztahuje kontrola přístupu. Kontroly přístupu mohou být pro členy skupiny nebo pro uživatele, kteří byli přiřazeni k aplikaci. Můžete dále obor kontroly přístupu zkontrolovat pouze uživatele typu Host, kteří jsou členy (nebo přiřazena k aplikaci), spíše než kontrolu všech uživatelů, kteří jsou členy nebo kteří mají přístup k aplikaci.

    ![Vytvoření kontroly přístupu – uživatelé](./media/create-access-review/users.png)

1. V části **Skupina** vyberte jednu nebo více skupin, ve kterých chcete členství zkontrolovat.

    > [!NOTE]
    > Výběrem více než jedné skupiny vytvoříte více kontrol přístupu. Například výběrem pěti skupin vytvoříte pět samostatných kontrol přístupu.
    
    ![Vytvoření kontroly přístupu – vybrat skupinu](./media/create-access-review/select-group.png)

1. V části **Aplikace** (pokud jste v kroku 8 vybrali **možnost Přiřazeno k aplikaci)** vyberte aplikace, ke kterým chcete zkontrolovat přístup.

    > [!NOTE]
    > Výběrem více než jedné aplikace vytvoříte více kontrol přístupu. Například výběrem pěti aplikací vytvoříte pět samostatných kontrol přístupu.
    
    ![Vytvoření kontroly přístupu – výběr aplikace](./media/create-access-review/select-application.png)

1. V části **Recenzenti** vyberte jednoho nebo více lidí, kteří zkontrolují všechny uživatele v oboru. Nebo můžete vybrat, zda mají členové zkontrolovat svůj vlastní přístup. Pokud se jedná o skupinu, můžete požádat vlastníky skupiny, aby je zkontrolovali. Můžete také požadovat, aby recenzenti poskytli důvod, když schválí přístup.

    ![Vytvoření kontroly přístupu – recenzenti](./media/create-access-review/reviewers.png)

1. V části **Programy** vyberte program, který chcete použít. **Výchozí program** je vždy k dispozici.

    ![Vytvoření kontroly přístupu – programy](./media/create-access-review/programs.png)

    Můžete zjednodušit, jak sledovat a shromažďovat recenze přístupu pro různé účely jejich uspořádáním do programů. Každá kontrola přístupu může být propojena s programem. Poté, co připravujete zprávy pro auditora, můžete se zaměřit na kontroly přístupu v rozsahu pro konkrétní iniciativu. Programy a výsledky kontroly přístupu jsou viditelné pro uživatele v roli globálního správce, správce uživatele, správce zabezpečení nebo čtečky zabezpečení.

    Seznam programů zobrazíte tak, že přejdete na stránku kontroly přístupu a vyberte **programy**. Pokud jste v roli globálního správce nebo správce uživatele, můžete vytvořit další programy. Můžete například zvolit, zda chcete mít jeden program pro každou iniciativu nebo obchodní cíl dodržování předpisů. Pokud již program nepotřebujete a program s ním není propojen, můžete jej odstranit.

### <a name="upon-completion-settings"></a>Po dokončení nastavení

1. Chcete-li určit, co se stane po dokončení kontroly, rozbalte část **Po dokončení nastavení.**

    ![Vytvoření kontroly přístupu – po dokončení nastavení](./media/create-access-review/upon-completion-settings.png)

1. Chcete-li automaticky odebrat přístup pro uživatele, kteří byli odepřeni, nastavte **automatické použití výsledků na prostředek** na **povolit**. Pokud chcete výsledky po dokončení kontroly použít ručně, nastavte přepínač na **Zakázat**.

1. Pomocí seznamu **By recenzent neodpovídá** určit, co se stane pro uživatele, které nejsou kontrolovány recenzenta v období revize. Toto nastavení nemá vliv na uživatele, kteří byli kontrolováni recenzenty ručně. Pokud je konečné rozhodnutí recenzenta Odepřít, bude přístup uživatele odebrán.

    - **Žádná změna** – Ponechat přístup uživatele beze změny
    - **Odebrat přístup** – odebrání přístupu uživatele
    - **Schválit přístup** – schválení přístupu uživatele
    - **Přijmout doporučení** – přijmout doporučení systému o odepření nebo schválení trvalého přístupu uživatele

### <a name="advanced-settings"></a>Upřesnit nastavení

1. Chcete-li zadat další nastavení, rozbalte oddíl **Upřesnit nastavení.**

    ![Vytvoření kontroly přístupu – upřesňující nastavení](./media/create-access-review/advanced-settings.png)

1. Nastavte **zobrazit doporučení** **povolit,** chcete-li recenzentům zobrazit systémová doporučení založená na přístupových informacích uživatele.

1. Nastavte **Vyžadovat důvod při schvalování,** **chcete-li povolit,** aby recenzent mohl zadat důvod schválení.

1. Nastavte **oznámení pošty** **na Povolit,** aby Azure AD odesílat e-mailová oznámení recenzentům při spuštění kontroly přístupu a správcům po dokončení kontroly.

1. Nastavte **připomenutí** **na Povolit,** aby Azure AD odesílat připomenutí kontrolpřístupu probíhá recenzentům, kteří nedokončili svou kontrolu.

    Ve výchozím nastavení Azure AD automaticky pošle připomenutí revidujícím, kteří ještě neodpověděli, po uplynutí poloviny času.

## <a name="start-the-access-review"></a>Spuštění kontroly přístupu

Po zadání nastavení kontroly přístupu klepněte na tlačítko **Start**. Kontrola přístupu se zobrazí v seznamu s indikátorem jeho stavu.

![Seznam kontrol přístupu a jejich stav](./media/create-access-review/access-reviews-list.png)

Ve výchozím nastavení Azure AD odešle e-mail recenzentům krátce po spuštění kontroly. Pokud se rozhodnete, že nemáte Azure AD odeslat e-mail, nezapomeňte informovat recenzenty, že kontrola přístupu čeká na jejich dokončení. Můžete jim zobrazit pokyny, jak [zkontrolovat přístup ke skupinám nebo aplikacím](perform-access-review.md). Pokud je vaše recenze na hosta, aby si zkontrolovali vlastní přístup, ukažte jim pokyny, jak [si sami zkontrolovat přístup ke skupinám nebo aplikacím](review-your-access.md).

Pokud jste hosté přiřadili jako recenzenty a oni pozvánku nepřijali, neobdrží e-mail z kontrol přístupu, protože musí pozvánku nejprve přijmout před kontrolou.

## <a name="access-review-status-table"></a>Tabulka stavu kontroly přístupu

| Status | Definice |
|--------|------------|
|Notstarted | Byla vytvořena kontrola, zjišťování uživatele čeká na spuštění. |
|Initializing   | Probíhá zjišťování uživatelů k identifikaci všech uživatelů, kteří jsou součástí kontroly. |
|Spouštění | Začíná kontrola. Pokud jsou povolena e-mailová oznámení, jsou recenzentům odesílány e-maily. |
|Probíhá | Kontrola byla zahájena. Pokud jsou e-mailová oznámení povolena, byly recenzentům odeslány e-maily. Recenzenti mohou odesílat rozhodnutí až do data splatnosti. |
|Dokončení | Kontrola se dokončuje a vlastníkovi recenze se zasílají e-maily. |
|Automatické recenzování | Recenze je ve fázi revize systému. Systém zaznamenává rozhodnutí pro uživatele, kteří nebyli zkontrolováni na základě doporučení nebo předem nakonfigurovaných rozhodnutí. |
|Automaticky recenzováno | Systém zaznamenal rozhodnutí pro všechny uživatele, kteří nebyli přezkoumáni. Kontrola je připravena přejít na **použít,** pokud je povoleno automatické použití. |
|Použití | Nedojde k žádné změně v přístupu pro uživatele, kteří byli schváleni. |
|Použito | Pokud existuje počet zakázaných uživatelů, byli odebráni ze zdroje nebo adresáře. |

## <a name="create-reviews-via-apis"></a>Vytváření recenzí pomocí api

Můžete také vytvořit kontroly přístupu pomocí api. Co děláte pro správu kontrol přístupu skupin a uživatelů aplikací na webu Azure Portal, se dá taky dělat pomocí rozhraní API Microsoft Graphu. Další informace najdete v [tématu Azure AD kontroly přístupu k rozhraní API odkaz](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta). Ukázka kódu najdete v [tématu Příklad načítání kontrol přístupu azure ad prostřednictvím Microsoft Graphu](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Další kroky

- [Kontrola přístupu ke skupinám nebo aplikacím](perform-access-review.md)
- [Zkontrolujte si přístup ke skupinám nebo aplikacím](review-your-access.md)
- [Vyplnění kontroly přístupu skupin nebo aplikací](complete-access-review.md)
