---
title: Poradce při potížích se správou nároků – Azure AD
description: Přečtěte si o některých položkách, které byste měli zkontrolovat, abyste měli potíže se správou nároků služby Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c38e1a61827da547bb39a699a0e92043e63466c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128480"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Poradce při potížích se správou nároků Azure AD

Tento článek popisuje některé položky, které byste měli zkontrolovat, abyste pomohli s řešením potíží se správou nároků služby Azure Active Directory (Azure AD).

## <a name="administration"></a>Správa

* Pokud se při konfiguraci správy nároků zobrazí zpráva o odepření přístupu a jste globální správce, ujistěte se, že váš adresář má [licenci Azure AD Premium P2 (nebo EMS E5).](entitlement-management-overview.md#license-requirements)

* Pokud se při vytváření nebo zobrazování přístupových balíčků zobrazí zpráva o odepření přístupu a jste členem skupiny pro autory katalogu, musíte před vytvořením prvního balíčku pro přístup [vytvořit katalog.](entitlement-management-catalog-create.md)

## <a name="resources"></a>Prostředky

* Role pro aplikace definují samotné aplikace a spravují se v Azure AD. Pokud aplikace nemá žádné role prostředků, správa nároků přiřadí uživatele k **roli výchozí ho přístupu.**

    Všimněte si, že portál Azure může také zobrazit instanční objekty pro služby, které nelze vybrat jako aplikace.  **Zejména Exchange Online** a **SharePoint Online** jsou služby, nikoli aplikace, které mají role prostředků v adresáři, takže je nelze zahrnout do balíčku pro přístup.  Místo toho k zajištění odpovídající licence pro uživatele, který potřebuje přístup k těmto službám, využijte licencování na základě skupin.

* Aby skupina mohla být prostředkem v přístupovém balíčku, musí být upravitelná v Azure AD.  Skupiny, které pocházejí z místní služby Active Directory, není možné přiřadit jako prostředky, protože jejich atributy vlastníka ani člena není možné změnit v Azure AD.   V Azure AD není možné upravit ani skupiny, které pocházejí z distribučních skupin v Exchange Online. 

* Jako prostředky není možné přidat knihovny dokumentů ani jednotlivé dokumenty SharePointu Online.  Místo toho vytvořte [skupinu zabezpečení Azure AD](../fundamentals/active-directory-groups-create-azure-portal.md), zahrňte tuto skupinu a roli webu do balíčku přístupu a v SharePointu Online použijte tuto skupinu k řízení přístupu ke knihovně dokumentů nebo dokumentu.

* Pokud jsou k prostředku, který chcete spravovat pomocí přístupového balíčku, již přiřazení uživatelé, ujistěte se, že jsou tito uživatelé přiřazení k přístupovému balíčku pomocí odpovídajících zásad. Do přístupového balíčku byste například měli zahrnout skupiny, které již obsahují některé uživatele. Pokud tito uživatelé ve skupině vyžadují nepřetržitý přístup, musí mít odpovídající zásady pro přístupové balíčky, aby neztratili přístup ke skupině. Přístupový balíček můžete přiřadit tak, že požádáte uživatele, aby si vyžádali přístupový balíček obsahující příslušný prostředek, nebo že uživatele přiřadíte k přístupovému balíčku přímo. Další informace naleznete v [tématu Změna nastavení žádosti a schválení pro přístupový balíček](entitlement-management-access-package-request-policy.md).

* Když odeberete člena týmu, odebere se i ze skupiny Office 365. Odebrání z týmového chatu může trvat o něco déle. Další informace naleznete v tématu [Členství ve skupině](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership).

* Ujistěte se, že váš adresář není nakonfigurovaný pro více geografických umístění. Správa nároků v současné době nepodporuje více geografických umístění pro SharePoint Online. Weby SharePointu Online musí být ve výchozím geografickém umístění, aby se mohly řídit pomocí správy nároků. Další informace najdete [v tématu Multi-Geo capabilities in OneDrive a SharePoint Online](https://docs.microsoft.com/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365).

## <a name="access-packages"></a>Přístupové balíčky

* Pokud se pokusíte odstranit přístupový balíček nebo zásadu a zobrazí se chybová zpráva, že existují aktivní přiřazení, pokud nevidíte žádné uživatele s přiřazeními, zkontrolujte, zda některá nedávno odstraněná uživatelé stále mají přiřazení. Během 30denního okna po odstranění uživatele lze uživatelský účet obnovit.   

## <a name="external-users"></a>Externí uživatelé

* Pokud chce externí uživatel požádat o přístup k přístupovému balíčku, ujistěte se, že pro přístupový balíček používá **odkaz Portál u aplikace Můj přístup.** Další informace naleznete v [tématu Odkaz na sdílení a požádejte o přístupový balíček](entitlement-management-access-package-settings.md). Pokud externí uživatel navštíví **myaccess.microsoft.com** a nepoužije úplný odkaz na portál Služby Přístup, zobrazí se mu přístupové balíčky, které mají k dispozici v jejich vlastní organizaci, nikoli ve vaší organizaci.

* Pokud externí uživatel nemůže požádat o přístup k přístupu k balíčku nebo nemůže získat přístup k prostředkům, zkontrolujte [nastavení pro externí uživatele](entitlement-management-external-users.md#settings-for-external-users).

* Pokud nový externí uživatel, který se ještě nepřihlásil k vašemu adresáři, obdrží přístupový balíček zahrnující web SharePointu Online, jeho přístupový balíček se nezobrazí jako zcela doručený, dokud se nezřídí jeho účet v SharePointu Online. Další informace o nastavení sdílení najdete v [tématu Kontrola nastavení externího sdílení SharePointu Online](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings).

## <a name="requests"></a>Žádosti

* Pokud chce uživatel požádat o přístup k přístupovému balíčku, ujistěte se, že pro přístupový balíček používá **odkaz portálu Můj přístup.** Další informace naleznete v [tématu Odkaz na sdílení a požádejte o přístupový balíček](entitlement-management-access-package-settings.md).

* Pokud portál Můj přístup otevřete v prohlížeči v privátním nebo anonymním režimu, může dojít ke konfliktu s přihlášením. Pro přístup k portálu Můj přístup doporučujeme nepoužívat prohlížeč v privátním nebo anonymním režimu.

* Pokud se uživatel, který ještě není ve vašem adresáři, přihlašuje k portálu Můj přístup, aby požádal o přístupový balíček, ujistěte se, že k ověření používá svůj účet organizace. Účtem organizace může být účet v adresáři prostředků nebo v adresáři, který je v některé ze zásad přístupového balíčku. Pokud účet uživatele není účet organizace nebo pokud adresář, ve kterém se ověřuje, není v zásadách, přístupový balíček se uživateli nezobrazí. Další informace naleznete v [tématu Žádost o přístup k přístupu k balíčku](entitlement-management-request-access.md).

* Pokud má uživatel zablokované přihlašování k adresáři prostředků, nemůže na portálu Můj přístup požádat o přístup. Aby uživatel mohl požádat o přístup, musíte z jeho profilu odebrat blokování přihlašování. Pokud chcete přihlašovací blok odebrat, klikněte na webu Azure Portal na **Azure Active Directory**, klikněte na **Uživatelé**, klikněte na něj a potom na **Profil**. Upravte oddíl **Nastavení** a změňte **možnost Přihlásit se** k bloku na **Ne**. Další informace najdete [v tématu Přidání nebo aktualizace informací o profilu uživatele pomocí služby Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  Můžete také zkontrolovat, zda byl uživatel zablokován z důvodu [zásad ochrany identity](../identity-protection/howto-unblock-user.md).

* Pokud je uživatel na portálu Můj přístup žadatel i schvalovatel, nezobrazí se mu žádost o přístupový balíček na stránce **Schválení.** Toto chování je záměrné – uživatel nemůže schválit vlastní žádost. Ujistěte se, že přístupový balíček, o který žádá, má v zásadách nakonfigurované další schvalovatele. Další informace naleznete v [tématu Změna nastavení žádosti a schválení pro přístupový balíček](entitlement-management-access-package-request-policy.md).

### <a name="view-a-requests-delivery-errors"></a>Zobrazení chyb doručení požadavku

**Předpokladová role:** Globální správce, správce uživatele, vlastník katalogu nebo správce balíčků aplikace Access

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **accessové balíčky** a potom otevřete přístupový balíček.

1. Klepněte na **položku Požadavky**.

1. Vyberte požadavek, který chcete zobrazit.

    Pokud má požadavek nějaké chyby doručení, stav požadavku bude **nedoručennebo** **částečně doručen**.

    Pokud se objeví chyby doručení, bude v podokně podrobností požadavku počet chyb doručení.

1. Kliknutím na počet zobrazíte všechny chyby doručení žádosti.

### <a name="reprocess-a-request"></a>Opětovné zpracování požadavku

Pokud požadavek narazí na chybu, můžete požadavek znovu zpracovat a zopakovat jej. Požadavek, který má stav Doručení **se nezdařilo** nebo **částečně doručeno** a dokončení kratší než jeden týden, lze znovu zpracovat pouze.

**Předpokladová role:** Globální správce, správce uživatele, vlastník katalogu nebo správce balíčků aplikace Access

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **accessové balíčky** a potom otevřete přístupový balíček.

1. Klepněte na **položku Požadavky**.

1. Klikněte na požadavek, který chcete znovu zpracovat.

1. V podokně podrobností požadavku klepněte na **tlačítko Znovu zpracovat požadavek**.

    ![Opětovné zpracování neúspěšného požadavku](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Zrušení nevyřízené žádosti

Čekající požadavek, který ještě nebyl doručen nebo jehož doručení se nezdařilo, můžete zrušit pouze.

**Předpokladová role:** Globální správce, správce uživatele, vlastník katalogu nebo správce balíčků aplikace Access

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **accessové balíčky** a potom otevřete přístupový balíček.

1. Klepněte na **položku Požadavky**.

1. Klikněte na požadavek, který chcete zrušit.

1. V podokně podrobností požadavku klepněte na tlačítko **Storno požadavek**.

## <a name="multiple-policies"></a>Více zásad

* Správa oprávnění se řídí osvědčenými postupy pro nejnižší oprávnění. Když uživatel požaduje přístup k přístupový balíček, který má více zásad, které platí, správa oprávnění zahrnuje logiku, která pomáhá zajistit přísnější nebo konkrétnější zásady jsou upřednostňovány nad obecné zásady. Pokud je zásada obecná, správa oprávnění nemusí zobrazit zásadu žadateli nebo může automaticky vybrat přísnější zásady.

* Zvažte například balíček přístupu se dvěma zásadami pro interní zaměstnance, ve kterých se obě zásady vztahují na osičujícího. První zásada je pro konkrétní uživatele, kteří zahrnují žadatele. Druhá zásada je pro všechny uživatele v adresáři, jehož je žadatel členem. V tomto scénáři je automaticky vybrána první zásada pro ozdaní, protože je přísnější. Žadatel nemá možnost vybrat druhou zásadu.

* Pokud platí více zásad, zásady, které jsou automaticky vybrány nebo zásady, které jsou zobrazeny žadateli je založena na následující logiku priority:

    | Priorita zásad | Rozsah |
    | --- | --- |
    | P1 | Konkrétní uživatelé a skupiny ve vašem adresáři NEBO Specifické připojené organizace |
    | P2 | Všichni členové ve vašem adresáři (kromě hostů) |
    | P3 | Všichni uživatelé ve vašem adresáři (včetně hostů) NEBO Specifické připojené organizace |
    | P4 | Všechny připojené organizace NEBO Všichni uživatelé (všichni připojení organizace + noví externí uživatelé) |
    
    Pokud je nějaká zásada v kategorii s vyšší prioritou, budou kategorie s nižší prioritou ignorovány. Příklad zobrazení více zásad se stejnou prioritou žadateli naleznete v [tématu Výběr zásady](entitlement-management-request-access.md#select-a-policy).

## <a name="next-steps"></a>Další kroky

- [Řízení přístupu pro externí uživatele](entitlement-management-external-users.md)
- [Zobrazení sestav o tom, jak uživatelé získali přístup při správě nároků](entitlement-management-reports.md)
