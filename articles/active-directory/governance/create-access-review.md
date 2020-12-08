---
title: Vytvoření kontroly přístupu skupin & aplikací – Azure AD
description: Naučte se, jak vytvořit kontrolu přístupu pro členy skupiny nebo přístup k aplikacím v Azure Active Directory kontroly přístupu.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/07/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: b12eb95a7840bdbb902701fc644eee30ffe9900f
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96778533"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Vytvoření kontroly přístupu skupin a aplikací v prohlídekch Azure AD Access

Přístup k skupinám a aplikacím pro zaměstnance a hosty se v průběhu času mění. Aby se snížilo riziko spojené s nezastaralým přiřazením přístupu, můžou správci pomocí služby Azure Active Directory (Azure AD) vytvářet kontroly přístupu pro členy skupiny nebo přístup k aplikacím. Pokud potřebujete rutinní kontrolu přístupu, můžete také vytvořit opakované recenze přístupu. Další informace o těchto scénářích najdete v tématech [Správa přístupu uživatelů](manage-user-access-with-access-reviews.md) a [Správa přístupu hostů](manage-guest-access-with-access-reviews.md).

Můžete se podívat na rychlé video s přehledem o povolení kontrol přístupu:

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

Tento článek popisuje, jak vytvořit jednu nebo více kontrol přístupu pro členy skupiny nebo přístup k aplikaci.

## <a name="prerequisites"></a>Předpoklady

- Azure AD Premium P2
- Globální správce nebo Správce uživatelů

Další informace najdete v tématu [licenční požadavky](access-reviews-overview.md#license-requirements).

## <a name="create-one-or-more-access-reviews"></a>Vytvoření jedné nebo více kontrol přístupu

1. Přihlaste se k Azure Portal a otevřete [stránku zásad správného řízení identity](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

2. V nabídce vlevo klikněte na **recenze přístupů**.

3. Kliknutím na **Nová kontrola přístupu** vytvořte novou kontrolu přístupu.

    ![Podokno kontroly přístupu v nástroji pro řízení identit](./media/create-access-review/access-reviews.png)

4. V **kroku 1: vyberte, co chcete zkontrolovat** vyberte prostředek, který chcete zkontrolovat.

    ![Vytvoření kontroly přístupu – kontrola názvu a popisu](./media/create-access-review/select-what-review.png)

5. Pokud jste v kroku 1 vybrali **týmy a skupiny** , máte dvě možnosti v kroku 2.
   - **Všechny Microsoft 365 skupiny s uživateli typu Host.** Tuto možnost vyberte, pokud chcete vytvářet opakované Recenze všech uživatelů typu Host napříč všemi týmy Microsoft a M365 skupinami ve vaší organizaci. Některé skupiny můžete vyloučit kliknutím na vybrat skupiny, které se mají vyloučit.
   - **Vyberte týmy + skupiny.** Tuto možnost vyberte, pokud chcete určit konečnou skupinu týmů nebo skupin, které chcete zkontrolovat. Po kliknutí na tuto možnost se zobrazí seznam skupin vpravo, ze kterých můžete vybírat.

     ![Týmy a skupiny](./media/create-access-review/teams-groups.png)

     ![Týmy a skupiny zvolené v uživatelském rozhraní](./media/create-access-review/teams-groups-detailed.png)

6. Pokud jste v kroku 1 vybrali **aplikace** , můžete v kroku 2 vybrat jednu nebo více aplikací.

    >[!NOTE]
    > Výběr více skupin nebo aplikací bude mít za následek vytvoření více revizí přístupu. Pokud například vyberete 5 skupin, které se mají zkontrolovat, bude výsledkem 5 samostatných revizí přístupu.

   ![Rozhraní, které se zobrazí, pokud jste zvolili aplikace místo skupin](./media/create-access-review/select-application-detailed.png)

7. Dále v kroku 3 můžete vybrat rozsah revize. Vaše možnosti jsou
   - **Jenom uživatelé typu Host.** Výběrem této možnosti omezíte kontrolu přístupu jenom na uživatele typu Host Azure AD B2B ve vašem adresáři.
   - **Všemi.** Výběrem této možnosti zaberete u všech uživatelských objektů přidružených k danému prostředku kontrolu přístupu.

    >[!NOTE]
    > Pokud jste v kroku 2 vybrali možnost všechny Microsoft 365 skupiny s uživateli typu Host, pak je jediným krokem kontrola uživatelů typu Host v kroku 3.

8. Klikněte na další: recenze.
9. V části **Vybrat revidující** vyberte jednu nebo více uživatelů, kteří mají provádět kontroly přístupu. Na výběr máte tyto:
    - **Vlastník skupiny** (k dispozici pouze při provádění kontroly pro tým nebo skupinu)
    - **Vybraní uživatelé nebo skupiny**
    - **Uživatelé kontrolují vlastní přístup**
    - **Tisk Manažeři uživatelů.**
    Pokud zvolíte buď **Správce uživatelů** , nebo **Vlastníci skupiny**  , máte také možnost zadat záložního kontrolora. Záložní revidující jsou vyzváni, aby provedli kontrolu v případě, že uživatel nemá žádného správce určeného v adresáři, nebo skupina nemá vlastníka.

    ![Nová kontrola přístupu](./media/create-access-review/new-access-review.png)

10. V části **zadat opakování recenze** můžete zadat četnost, jako je například **týdně, měsíčně, čtvrtletně, jednou ročně**. Pak zadáte **dobu trvání**, která určuje, jak dlouho bude kontrola otevřená pro vstup od revidujících. Například maximální doba, kterou můžete nastavit pro měsíční revizi, je 27 dní, aby se předešlo překrývání recenzí. Možná budete chtít zkrátit dobu trvání, abyste měli jistotu, že se váš vstup revidujícího používal dříve. Dále můžete vybrat **počáteční** a **koncové datum**.

    ![Vyberte, jak často se má kontrola provádět.](./media/create-access-review/frequency.png)

11. Klikněte na tlačítko **Další: nastavení** v dolní části stránky.
12. V **nastavení po dokončení** můžete určit, co se stane po dokončení kontroly.

    ![Vytvoření kontroly přístupu s nastavením při dokončování](./media/create-access-review/upon-completion-settings-new.png)

Pokud chcete automaticky odebrat přístup pro zamítnuté uživatele, nastavte automatické použití výsledků na prostředek, aby bylo možné povolit. Pokud chcete výsledky použít ručně po dokončení kontroly, nastavte přepínač na zakázat.
Použijte seznam Pokud revidující nereagují k určení toho, co se stane pro uživatele, kteří kontrolor v rámci období revize nekontroloval. Toto nastavení nemá vliv na uživatele, kteří byli zkontrolováni ručně. Pokud je posledním rozhodnutím kontrolora zamítnutí, bude přístup uživatele odebrán.

- **Žádná změna** – opuštění přístupu uživatele nezměněné
- **Odebrání přístupu** – odebrání přístupu uživatele
- **Schválit přístup** – schválení přístupu uživatele
- **Využijte doporučení** – Vezměte v úvahu doporučení systému při odepření nebo schvalování trvalého přístupu uživatele.

    ![Možnosti nastavení po dokončení](./media/create-access-review/upon-completion-settings-new.png)

Použijte akci, která se má použít na zamítnutých uživatelích **typu Host** a určení toho, co se stane uživatelům typu Host, pokud jsou odepřeni.
- Odebráním členství uživatele z prostředku dojde k odebrání odepřeného přístupu uživatele ke skupině nebo aplikaci, která je právě revidována, a stále se budou moci přihlásit k tenantovi.
- Zablokuje uživatelům přihlášení po dobu 30 dnů a pak odebrání uživatele z klienta zablokuje odepřeným uživatelům přihlášení k tenantovi bez ohledu na to, jestli mají přístup k jiným prostředkům. Pokud došlo k chybě nebo pokud se správce rozhodne znovu povolit přístup k jednomu z nich, může to provést do 30 dnů od zakázání uživatele. Pokud se u zakázaných uživatelů neprovede žádná akce, odstraní se z tenanta.

Další informace o osvědčených postupech pro odebrání uživatelů typu Host, kteří už nemají přístup k prostředkům ve vaší organizaci, najdete v článku [s názvem použití Azure AD identity governance ke kontrole a odebírání externích uživatelů, kteří už nemají přístup](access-reviews-external-users.md) k prostředkům.

   >[!NOTE]
   >Akce, která se má použít u zamítnutých uživatelů typu Host, se nedá nakonfigurovat u revizí, které jsou v rozsahu pro uživatele typu Host. Pro recenze **všech M365 skupin s uživateli typu Host** se taky nedá konfigurovat. Pokud není možné konfigurovat, použije se výchozí možnost odebrání členství uživatele z prostředku u zakázaných uživatelů.

13. V části **Povolit kontrolu rozhodnutí pro rozhodování** si rozhodněte, jestli chcete, aby váš kontrolor přijímal doporučení během procesu revize.

    ![Povolit možnosti pomocníka s rozhodnutím](./media/create-access-review/helpers.png)

14. V části **Upřesnit nastavení** můžete vybrat následující možnosti:
    - Nastavte **odůvodnění** , které je potřeba, aby **bylo možné povolit** , aby kontrolor zadal důvod ke schválení.
    - Nastavte **e-mailová oznámení** , která **umožní** , aby služba Azure AD odesílala e-mailová oznámení kontrolorům při zahájení kontroly přístupu a správcům, když se kontrola dokončí.
    - Nastavením **připomenutí** **umožníte** , aby služba Azure AD odesílala připomenutí kontrol přístupu, která nedokončila jejich kontrolu. Tato připomenutí budou po celou dobu trvání revize samostatná.
    - Obsah e-mailu odeslaného revidujícím se automaticky vygeneruje na základě podrobností o kontrole, jako je například název revize, název prostředku, datum splatnosti atd. Pokud potřebujete způsob, jak sdělit další informace, jako jsou například další pokyny nebo kontaktní údaje, můžete tyto podrobnosti zadat v části **Další obsah pro e-maily kontrolora** . Zadané informace jsou součástí e-mailů pozvánky a připomenutí odeslaných přiřazeným kontrolorům. Část zvýrazněná na obrázku níže ukazuje, kde se tyto informace zobrazují.


      ![Další obsah pro kontrolora](./media/create-access-review/additional-content-reviewer.png)

15. Klikněte na **Další: zkontrolovat + vytvořit** pro přechod na další stránku.
16. Pojmenujte kontrolu přístupu. Volitelně můžete zadat popis revize. Název a popis se zobrazí kontrolorům.
17. Zkontrolujte informace a vyberte **vytvořit** .

       ![vytvořit revizi obrazovky](./media/create-access-review/create-review.png)

## <a name="start-the-access-review"></a>Spustit kontrolu přístupu

Po zadání nastavení pro kontrolu přístupu klikněte na **Spustit**. Kontrola přístupu se zobrazí v seznamu s indikátorem jeho stavu.

![Seznam revizí přístupu a jejich stav](./media/create-access-review/access-reviews-list.png)

Ve výchozím nastavení Azure AD pošle e-mail kontrolorům krátce po zahájení kontroly. Pokud se rozhodnete Neodesílat e-maily Azure AD, nezapomeňte informovat kontrolory, které čekají na dokončení kontroly přístupu. Můžete jim Ukázat pokyny, jak [zkontrolovat přístup ke skupinám nebo aplikacím](perform-access-review.md). Pokud je vaše kontrola pro hosty, aby zkontrolovala svůj vlastní přístup, zobrazte si pokyny, jak [kontrolovat přístup pro vlastní skupiny nebo aplikace](review-your-access.md).

Pokud jste přidělili hosty jako kontroloři a nepřijali pozvánku, neobdrží e-mail z kontroly přístupu, protože před přezkoumáním musí nejdřív pozvání přijmout.

## <a name="access-review-status-table"></a>Tabulka stavů kontroly přístupu

| Status | Definice |
|--------|------------|
|NotStarted | Byla vytvořena recenze, zjišťování uživatelů čeká na spuštění. |
|Initializing   | Zjišťování uživatelů probíhá při identifikaci všech uživatelů, kteří jsou součástí kontroly. |
|Spouštění | Probíhá spuštění kontroly. Pokud jsou povolená e-mailová oznámení, posílá recenzentům e-maily. |
|InProgress | Kontrola byla spuštěna. Pokud se e-mailová oznámení povolí recenzentům, pošlou jim e-mail. Recenzenti mohou odesílat rozhodnutí do data splatnosti. |
|Absolv | Kontrola je dokončena a e-maily jsou odesílány vlastníkovi revize. |
|Automatické přezkoumání | Revize je ve fázi kontroly systému. Systém zaznamenává rozhodnutí pro uživatele, kteří nebyli prověřeni na základě doporučení nebo předem nakonfigurovaných rozhodnutí. |
|Automaticky zkontrolované | Systém zaznamenal rozhodnutí pro všechny uživatele, kteří nebyli zkontrolováni. Kontrola je připravena pokračovat v **použití** , pokud je povoleno automatické použití. |
|Použije | Pro uživatele, kteří se schválili, nebude přístup nijak změněn. |
|Použito | Zamítnutí uživatelé (pokud existují) byly odebrány z prostředku nebo adresáře. |
|Neúspěšný | Kontrola neprobíhala. Tato chyba může souviset s odstraněním tenanta, změnou licencí nebo jinými interními změnami klienta. |

## <a name="create-reviews-via-apis"></a>Vytváření recenzí prostřednictvím rozhraní API

Můžete také vytvořit kontroly přístupu pomocí rozhraní API. K tomu, jak spravovat kontroly přístupu skupin a uživatelů aplikací v Azure Portal lze také použít rozhraní API Microsoft Graph. Další informace najdete v referenčních informacích k [rozhraní API kontroly přístupu Azure AD](/graph/api/resources/accessreviews-root?view=graph-rest-beta). Ukázku kódu najdete v tématu [příklad načtení kontrol přístupu služby Azure AD prostřednictvím Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Další kroky

- [Kontrola přístupu ke skupinám nebo aplikacím](perform-access-review.md)
- [Kontrola přístupu pro vlastní skupiny nebo aplikace](review-your-access.md)
- [Dokončení kontroly přístupu skupin nebo aplikací](complete-access-review.md)