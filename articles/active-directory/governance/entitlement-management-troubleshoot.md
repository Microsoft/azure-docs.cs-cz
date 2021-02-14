---
title: Řešení potíží se správou oprávnění – Azure AD
description: Přečtěte si o některých položkách, které byste měli ověřit, abyste vám pomohli vyřešit Azure Active Directory správu nároků.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b01321c2cbb15be5526bbe9ed04f52238390574
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516885"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Řešení potíží se správou nároků Azure AD

Tento článek popisuje některé položky, které byste měli ověřit, abyste mohli řešit problémy se správou oprávnění Azure Active Directory (Azure AD).

## <a name="administration"></a>Správa

* Pokud se při konfiguraci správy opravňují zobrazí zpráva o odepření přístupu a Vy jste globálním správcem, ujistěte se, že má váš adresář [Azure AD Premium licenci P2 (nebo EMS E5)](entitlement-management-overview.md#license-requirements).

* Pokud se při vytváření nebo zobrazování balíčků pro přístup zobrazí zpráva o odepření přístupu a jste členem skupiny Creator Creator, musíte [vytvořit katalog](entitlement-management-catalog-create.md) před vytvořením prvního přístupového balíčku.

## <a name="resources"></a>Zdroje informací

* Role pro aplikace definují samotné aplikace a spravují se v Azure AD. Pokud aplikace nemá žádné role prostředků, Správa nároků přiřadí uživatelům **výchozí roli přístupu** .

    Všimněte si, že Azure Portal může také zobrazit instanční objekty pro služby, které nelze vybrat jako aplikace.  Konkrétně **Exchange Online** a **SharePoint Online** jsou služby, ne aplikace, které mají v adresáři role prostředků, takže je nejde zahrnout do balíčku pro přístup.  Místo toho k zajištění odpovídající licence pro uživatele, který potřebuje přístup k těmto službám, využijte licencování na základě skupin.

* Aby skupina mohla být prostředkem v přístupovém balíčku, musí být upravitelná v Azure AD.  Skupiny, které pocházejí z místní služby Active Directory, není možné přiřadit jako prostředky, protože jejich atributy vlastníka ani člena není možné změnit v Azure AD.   V Azure AD není možné upravit ani skupiny, které pocházejí z distribučních skupin v Exchange Online. 

* Jako prostředky není možné přidat knihovny dokumentů ani jednotlivé dokumenty SharePointu Online.  Místo toho vytvořte [skupinu zabezpečení Azure AD](../fundamentals/active-directory-groups-create-azure-portal.md), zahrňte tuto skupinu a roli lokality do balíčku pro přístup a v SharePointu Online použijte tuto skupinu k řízení přístupu k knihovně dokumentů nebo dokumentu.

* Pokud jsou k prostředku, který chcete spravovat pomocí přístupového balíčku, již přiřazení uživatelé, ujistěte se, že jsou tito uživatelé přiřazení k přístupovému balíčku pomocí odpovídajících zásad. Do přístupového balíčku byste například měli zahrnout skupiny, které již obsahují některé uživatele. Pokud tito uživatelé ve skupině vyžadují nepřetržitý přístup, musí mít odpovídající zásady pro přístupové balíčky, aby neztratili přístup ke skupině. Přístupový balíček můžete přiřadit tak, že požádáte uživatele, aby si vyžádali přístupový balíček obsahující příslušný prostředek, nebo že uživatele přiřadíte k přístupovému balíčku přímo. Další informace najdete v tématu [Nastavení žádostí o změnu a schvalování balíčku pro přístup](entitlement-management-access-package-request-policy.md).

* Když odeberete člena týmu, odeberou se také z Microsoft 365 skupiny. Odebrání z týmového chatu může trvat o něco déle. Další informace najdete v tématu [členství ve skupině](/microsoftteams/office-365-groups#group-membership).


## <a name="access-packages"></a>Přístupové balíčky

* Pokud se pokusíte odstranit balíček nebo zásadu přístupu a zobrazit chybovou zprávu s informacemi o aktivních přiřazeních, Pokud nevidíte žádné uživatele s přiřazením, zkontrolujte, jestli všichni nedávno odstranění uživatelé pořád mají přiřazení. Během 30denního okna po odstranění uživatele se může uživatelský účet obnovit.   

## <a name="external-users"></a>Externí uživatelé

* Když chce externí uživatel požádat o přístup k balíčku přístupu, ujistěte se, že používají **odkaz Můj portál přístupu** pro balíček přístupu. Další informace najdete v tématu [sdílení odkazu pro vyžádání balíčku přístupu](entitlement-management-access-package-settings.md). Pokud externí uživatel navštíví **myAccess.Microsoft.com** a nepoužívá úplný odkaz na portál pro přístup k portálu, zobrazí se jim dostupné balíčky pro přístup ve vlastní organizaci, a ne ve vaší organizaci.

* Pokud externí uživatel nemůže požádat o přístup k balíčku pro přístup nebo k prostředkům nemá přístup, nezapomeňte zkontrolovat [nastavení pro externí uživatele](entitlement-management-external-users.md#settings-for-external-users).

* Pokud nový externí uživatel, který se ještě nepřihlásil k vašemu adresáři, obdrží přístupový balíček zahrnující web SharePointu Online, jeho přístupový balíček se nezobrazí jako zcela doručený, dokud se nezřídí jeho účet v SharePointu Online. Další informace o nastavení sdílení najdete v tématu [Kontrola nastavení externího sdílení na SharePointu Online](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings).

## <a name="requests"></a>Žádosti

* Když chce uživatel požádat o přístup k balíčku přístupu, ujistěte se, že používají **odkaz Můj portál přístupu** pro balíček přístupu. Další informace najdete v tématu [sdílení odkazu pro vyžádání balíčku přístupu](entitlement-management-access-package-settings.md).

* Pokud portál Můj přístup otevřete v prohlížeči v privátním nebo anonymním režimu, může dojít ke konfliktu s přihlášením. Pro přístup k portálu Můj přístup doporučujeme nepoužívat prohlížeč v privátním nebo anonymním režimu.

* Pokud se uživatel, který ještě není ve vašem adresáři, přihlašuje k portálu Můj přístup, aby požádal o přístupový balíček, ujistěte se, že k ověření používá svůj účet organizace. Účtem organizace může být účet v adresáři prostředků nebo v adresáři, který je v některé ze zásad přístupového balíčku. Pokud účet uživatele není účet organizace nebo pokud adresář, ve kterém se ověřuje, není v zásadách, přístupový balíček se uživateli nezobrazí. Další informace najdete v tématu [vyžádání přístupu k balíčku přístupu](entitlement-management-request-access.md).

* Pokud má uživatel zablokované přihlašování k adresáři prostředků, nemůže na portálu Můj přístup požádat o přístup. Aby uživatel mohl požádat o přístup, musíte z jeho profilu odebrat blokování přihlašování. Chcete-li odebrat blok přihlášení, klikněte v Azure Portal na **Azure Active Directory**, klikněte na **Uživatelé**, klikněte na uživatele a pak klikněte na **profil**. Upravte oddíl **Nastavení** a změňte **zablokovat přihlášení** na **ne**. Další informace najdete v tématu [Přidání nebo aktualizace informací o profilu uživatele pomocí Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  Můžete taky zjistit, jestli se uživatel zablokoval kvůli [zásadám ochrany identity](../identity-protection/howto-identity-protection-remediate-unblock.md).

* Pokud je uživatel v portálu pro přístup žadatelem i schvalovatelem, nezobrazí se na stránce **schválení** žádost o přístup k balíčku pro přístup. Toto chování je záměrné – uživatel nemůže schválit vlastní žádost. Ujistěte se, že přístupový balíček, o který žádá, má v zásadách nakonfigurované další schvalovatele. Další informace najdete v tématu [Nastavení žádostí o změnu a schvalování balíčku pro přístup](entitlement-management-access-package-request-policy.md).

### <a name="view-a-requests-delivery-errors"></a>Zobrazit chyby doručení žádosti

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu, přístup správce balíčků nebo správce přiřazení balíčku pro přístup

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Klikněte na **požadavky**.

1. Vyberte žádost, kterou chcete zobrazit.

    Pokud v žádosti dojde k žádným chybám doručení, stav žádosti se **nedoručení** nebo **částečně doručí**.

    Pokud dojde k chybám doručení, zobrazí se v podokně podrobností žádosti počet chyb doručení.

1. Kliknutím na počet zobrazíte všechny chyby doručení žádosti.

### <a name="reprocess-a-request"></a>Znovu zpracovat požadavek

Pokud po aktivaci žádosti o rezpracování balíčku pro přístup dojde k chybě, musíte počkat, až systém žádost znovu zpracuje. Systém se několikrát pokusí znovu zpracovat několik hodin, takže během této doby nebudete moci vynutit opětovné zpracování. 

Můžete zpracovat pouze požadavek se stavem **doručení se nezdařilo** nebo byl **částečně doručen** a datum dokončení je kratší než jeden týden. Tlačítko pro **rezpracování** bude v opačném případě šedé.

![Tlačítko pro znovu zpracovat šedě](./media/entitlement-management-troubleshoot/cancel-reprocess-grayedout.png)

- Pokud je chyba opravena během okna zkušební verze, stav žádosti se změní na **doručení**. Požadavek se znovu zpracuje bez dalších akcí od uživatele.

- Pokud nebyla chyba opravena během okna zkušební verze, může se stát, že se stav žádosti **nezdařil** nebo bylo **doručeno částečně**. Pak můžete použít tlačítko znovu **zpracovat** . Budete mít sedm dní na rezpracování žádosti.

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu, přístup správce balíčků nebo správce přiřazení balíčku pro přístup

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Klikněte na **požadavky**.

1. Klikněte na žádost, kterou chcete znovu zpracovat.

1. V podokně podrobností žádosti klikněte na znovu **zpracovat požadavek**.

    ![Znovu zpracovat neúspěšnou žádost](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Zrušení žádosti, která čeká na vyřízení

Můžete zrušit pouze nevyřízenou žádost, která ještě nebyla doručena, nebo jejíž doručení se nezdařilo. Tlačítko **Zrušit** bude v opačném případě šedé.

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu, přístup správce balíčků nebo správce přiřazení balíčku pro přístup

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Klikněte na **požadavky**.

1. Klikněte na žádost, kterou chcete zrušit.

1. V podokně podrobností žádosti klikněte na **Zrušit požadavek**.

## <a name="multiple-policies"></a>Více zásad

* Správa nároků se řídí minimálně osvědčenými postupy pro oprávnění. Když si uživatel vyžádá přístup k balíčku pro přístup, který má víc zásad, které platí, Správa nároků zahrnuje logiku, která vám pomůžou zajistit, aby byly přísnější nebo konkrétnější zásady Upřednostněné nad obecnými zásadami. Pokud je zásada obecná, Správa nároků nemusí zobrazit zásady žadateli nebo může automaticky vybrat přísnější zásadu.

* Představte si například balíček pro přístup se dvěma zásadami pro interní zaměstnance, u kterých se obě zásady vztahují na žadatele. První zásada je určena pro konkrétní uživatele, kteří obsahují žadatele. Druhá zásada je určena všem uživatelům v adresáři, na kterém je žadatel členem. V tomto scénáři se první zásada vybere automaticky pro žadatele, protože je přísnější. Žadateli není dána možnost výběru druhé zásady.

* Když se uplatní víc zásad, vybere se automaticky vybraná zásada nebo zásady, které se zobrazí žadateli, na základě následující prioritní logiky:

    | Priorita zásad | Obor |
    | --- | --- |
    | P1 | Konkrétní uživatelé a skupiny v adresáři nebo konkrétní připojené organizace |
    | P2 | Všichni členové adresáře (kromě hostů) |
    | P3 | Všichni uživatelé v adresáři (včetně hostů) nebo konkrétní spojené organizace |
    | P4 | Všechny nakonfigurované připojené organizace nebo všichni uživatelé (všechny připojené organizace a všichni noví externí uživatelé) |
    
    Pokud je některá zásada v kategorii s vyšší prioritou, kategorie s nižší prioritou se ignorují. Příklad toho, jak se v žadateli zobrazují různé zásady se stejnou prioritou, najdete v tématu [Výběr zásady](entitlement-management-request-access.md#select-a-policy).

## <a name="next-steps"></a>Další kroky

- [Řízení přístupu pro externí uživatele](entitlement-management-external-users.md)
- [Zobrazit zprávy o tom, jak uživatelé získali přístup ve správě nároků](entitlement-management-reports.md)
