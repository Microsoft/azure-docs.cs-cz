---
title: Řešení potíží se správou oprávnění – Azure AD
description: Přečtěte si o některých položkách, které byste měli ověřit, abyste vám pomohli vyřešit Azure Active Directory správu nároků.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: e291a032c1aac45ebc783126e69b524e1d0af95b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422494"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Řešení potíží se správou nároků Azure AD

Tento článek popisuje některé položky, které byste měli ověřit, abyste mohli řešit problémy se správou oprávnění Azure Active Directory (Azure AD).

## <a name="administration"></a>Správa

* Pokud se při konfiguraci správy opravňují zobrazí zpráva o odepření přístupu a Vy jste globálním správcem, ujistěte se, že má váš adresář [Azure AD Premium licenci P2 (nebo EMS E5)](entitlement-management-overview.md#license-requirements).

* Pokud se při vytváření nebo zobrazování balíčků pro přístup zobrazí zpráva o odepření přístupu a jste členem skupiny Creator Creator, musíte [vytvořit katalog](entitlement-management-catalog-create.md) před vytvořením prvního přístupového balíčku.

## <a name="resources"></a>Materiály

* Role pro aplikace jsou definované aplikací a jsou spravované v Azure AD. Pokud aplikace nemá žádné role prostředků, Správa nároků přiřadí uživatelům **výchozí roli přístupu** .

    Všimněte si, že Azure Portal může také zobrazit instanční objekty pro služby, které nelze vybrat jako aplikace.  Konkrétně **Exchange Online** a **SharePoint Online** jsou služby, ne aplikace, které mají v adresáři role prostředků, takže je nejde zahrnout do balíčku pro přístup.  Místo toho použijte licencování na základě skupin k navázání vhodné licence pro uživatele, který potřebuje přístup k těmto službám.

* Aby skupina mohla být prostředkem v přístupovém balíčku, musí být schopna být upravitelná v Azure AD.  Skupiny, které pocházejí z místní služby Active Directory, se nedají přiřadit jako prostředky, protože jejich vlastník nebo atributy členů nejde změnit v Azure AD.   Skupiny, které pocházejí z Exchange Online jako distribuční skupiny, se nedají ve službě Azure AD upravovat ani v nich. 

* Knihovny dokumentů SharePointu Online a jednotlivé dokumenty nelze přidat jako prostředky.  Místo toho vytvořte [skupinu zabezpečení Azure AD](../fundamentals/active-directory-groups-create-azure-portal.md), zahrňte tuto skupinu a roli lokality do balíčku pro přístup a v SharePointu Online použijte tuto skupinu k řízení přístupu k knihovně dokumentů nebo dokumentu.

* Pokud jsou uživatelé, kteří už byli přiřazeni k prostředku, který chcete spravovat pomocí balíčku pro přístup, ujistěte se, že jsou uživatelé přiřazeni k balíčku přístupu s odpovídajícími zásadami. Například můžete chtít zahrnout skupinu do balíčku pro přístup, který již má uživatele ve skupině. Pokud tito uživatelé ve skupině potřebují pokračovat v přístupu, musí mít odpovídající zásady pro přístupové balíčky, aby neztratily přístup ke skupině. Balíček pro přístup můžete přiřadit tak, že požádáte uživatele, aby si vyžádali balíček pro přístup, který tento prostředek obsahuje, nebo ho přímo přiřadí do balíčku pro přístup. Další informace najdete v tématu [Nastavení žádostí o změnu a schvalování balíčku pro přístup](entitlement-management-access-package-request-policy.md).

* Když odeberete člena týmu, odstraní se i ze skupiny Office 365. Odebrání funkcí chatu týmu může být zpožděné. Další informace najdete v tématu [členství ve skupině](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership).

* Ujistěte se, že váš adresář není nakonfigurovaný pro více geografických umístění. Správa nároků aktuálně nepodporuje více geografických umístění pro SharePoint Online. Weby SharePointu Online musí být ve výchozím geografickém umístění, které se má řídit správou oprávnění. Další informace najdete v tématu [s více geografickými možnostmi na OneDrivu a SharePointu Online](https://docs.microsoft.com/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365).

## <a name="external-users"></a>Externí uživatelé

* Když chce externí uživatel požádat o přístup k balíčku přístupu, ujistěte se, že používají **odkaz Můj portál přístupu** pro balíček přístupu. Další informace najdete v tématu [sdílení odkazu pro vyžádání balíčku přístupu](entitlement-management-access-package-settings.md). Pokud externí uživatel navštíví **myAccess.Microsoft.com** a nepoužívá úplný odkaz na portál pro přístup k portálu, zobrazí se jim dostupné balíčky pro přístup ve vlastní organizaci, a ne ve vaší organizaci.

* Pokud externí uživatel nemůže požádat o přístup k balíčku pro přístup nebo k prostředkům nemá přístup, nezapomeňte zkontrolovat [nastavení pro externí uživatele](entitlement-management-external-users.md#settings-for-external-users).

* Pokud nový externí uživatel, který se dřív nepodepsal v adresáři, obdrží balíček pro přístup, včetně webu SharePointu Online, zobrazí se jeho balíček pro přístup jako neúplně doručené, dokud se jejich účet nezřídí v SharePointu Online. Další informace o nastavení sdílení najdete v tématu [Kontrola nastavení externího sdílení na SharePointu Online](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings).

## <a name="requests"></a>Požadavky

* Když chce uživatel požádat o přístup k balíčku přístupu, ujistěte se, že používají **odkaz Můj portál přístupu** pro balíček přístupu. Další informace najdete v tématu [sdílení odkazu pro vyžádání balíčku přístupu](entitlement-management-access-package-settings.md).

* Pokud otevřete portál pro přístup s prohlížečem nastaveným na privátní nebo anonymním režim, může to být v konfliktu s chováním při přihlašování. Při návštěvě portálu pro přístup doporučujeme, abyste nepoužívali privátní nebo anonymním režim pro váš prohlížeč.

* Když se uživatel, který ještě není ve vašem adresáři, přihlásí na portál My Access, aby si vyžádal balíček pro přístup, ujistěte se, že se ověřuje pomocí účtu organizace. Účet organizace může být buď účet v adresáři prostředků, nebo v adresáři, který je zahrnutý v jedné ze zásad balíčku přístupu. Pokud účet uživatele není účet organizace nebo pokud se v zásadě nezahrne adresář, ve kterém se ověřuje, uživatel neuvidí balíček pro přístup. Další informace najdete v tématu [vyžádání přístupu k balíčku přístupu](entitlement-management-request-access.md).

* Pokud se uživateli zablokuje přihlášení k adresáři prostředků, nebude moct požádat o přístup na portálu pro přístup. Než bude moct uživatel požádat o přístup, musíte z profilu uživatele odebrat blok přihlášení. Chcete-li odebrat blok přihlášení, klikněte v Azure Portal na **Azure Active Directory**, klikněte na **Uživatelé**, klikněte na uživatele a pak klikněte na **profil**. Upravte oddíl **Nastavení** a změňte **zablokovat přihlášení** na **ne**. Další informace najdete v tématu [Přidání nebo aktualizace informací o profilu uživatele pomocí Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  Můžete taky zjistit, jestli se uživatel zablokoval kvůli [zásadám ochrany identity](../identity-protection/howto-unblock-user.md).

* Pokud je uživatel v portálu pro přístup žadatelem i schvalovatelem, nezobrazí se na stránce **schválení** žádost o přístup k balíčku pro přístup. Toto chování je záměrné – uživatel nemůže schvalovat svoji vlastní žádost. Ujistěte se, že přístupový balíček, který požaduje, má pro tuto zásadu nakonfigurované další schvalovatele. Další informace najdete v tématu [Nastavení žádostí o změnu a schvalování balíčku pro přístup](entitlement-management-access-package-request-policy.md).

### <a name="view-a-requests-delivery-errors"></a>Zobrazit chyby doručení žádosti

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Klikněte na **požadavky**.

1. Vyberte žádost, kterou chcete zobrazit.

    Pokud v žádosti dojde k žádným chybám doručení, stav žádosti se **nedoručení** nebo **částečně doručí**.

    Pokud dojde k chybám doručení, v podokně podrobností žádosti dojde k počtu chyb doručení.

1. Kliknutím na počet zobrazíte všechny chyby doručení žádosti.

### <a name="reprocess-a-request"></a>Znovu zpracovat požadavek

Pokud v požadavku dojde k chybě, můžete žádost znovu zpracovat a znovu ji vyzkoušet. Můžete zpracovat pouze požadavek se stavem **doručení se nezdařilo** nebo byl **částečně doručen** a datum dokončení je kratší než jeden týden.

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Klikněte na **požadavky**.

1. Klikněte na žádost, kterou chcete znovu zpracovat.

1. V podokně podrobností žádosti klikněte na znovu **zpracovat požadavek**.

    ![Znovu zpracovat neúspěšnou žádost](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Zrušení žádosti, která čeká na vyřízení

Můžete zrušit pouze nevyřízenou žádost, která ještě nebyla doručena, nebo jejíž doručení se nezdařilo.

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Klikněte na **požadavky**.

1. Klikněte na žádost, kterou chcete zrušit.

1. V podokně podrobností žádosti klikněte na **Zrušit požadavek**.

## <a name="multiple-policies"></a>Více zásad

* Správa nároků se řídí minimálně osvědčenými postupy pro oprávnění. Když si uživatel vyžádá přístup k balíčku pro přístup, který má víc zásad, které platí, Správa nároků zahrnuje logiku, která vám pomůžou zajistit, aby byly přísnější nebo konkrétnější zásady Upřednostněné nad obecnými zásadami. Pokud je zásada obecná, Správa nároků nemusí zobrazit zásady žadateli nebo může automaticky vybrat přísnější zásadu.

* Představte si například balíček pro přístup se dvěma zásadami pro interní zaměstnance, u kterých se obě zásady vztahují na žadatele. První zásada je určena pro konkrétní uživatele, kteří obsahují žadatele. Druhá zásada je určena všem uživatelům v adresáři, na kterém je žadatel členem. V tomto scénáři se první zásada vybere automaticky pro žadatele, protože je přísnější. Žadateli není dána možnost výběru druhé zásady.

* Když se uplatní víc zásad, vybere se automaticky vybraná zásada nebo zásady, které se zobrazí žadateli, na základě následující prioritní logiky:

    | Priorita zásad | Rozsah |
    | --- | --- |
    | P1 | Konkrétní uživatelé a skupiny v adresáři nebo konkrétní připojené organizace |
    | P2 | Všichni členové adresáře (kromě hostů) |
    | P3 | Všichni uživatelé v adresáři (včetně hostů) nebo konkrétní spojené organizace |
    | P4 | Všechny připojené organizace nebo všichni uživatelé (všechny připojené organizace a všichni noví externí uživatelé) |
    
    Pokud je některá zásada v kategorii s vyšší prioritou, kategorie s nižší prioritou se ignorují. Příklad toho, jak se v žadateli zobrazují různé zásady se stejnou prioritou, najdete v tématu [Výběr zásady](entitlement-management-request-access.md#select-a-policy).

## <a name="next-steps"></a>Další kroky

- [Řízení přístupu pro externí uživatele](entitlement-management-external-users.md)
- [Zobrazit zprávy o tom, jak uživatelé získali přístup ve správě nároků](entitlement-management-reports.md)
