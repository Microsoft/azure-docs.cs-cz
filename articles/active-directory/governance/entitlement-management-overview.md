---
title: Co je správa nároků? - Azure AD
description: Získejte přehled o správě nároků služby Azure Active Directory a o tom, jak ji můžete použít ke správě přístupu k webům skupin, aplikací a SharePointu Online pro interní i externí uživatele.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3deb87fec8241ad6126314f3f6ce5fb9600ad1fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128551"
---
# <a name="what-is-azure-ad-entitlement-management"></a>Co je správa nároků Azure AD?

Správa oprávnění Azure Active Directory (Azure AD) je funkce [zásad správného řízení identit,](identity-governance-overview.md) která organizacím umožňuje spravovat identitu a přístup k životnímu cyklu ve velkém měřítku automatizací pracovních postupů žádostí o přístup, přiřazení přístupu, recenzí a vypršení platnosti.

Zaměstnanci v organizacích potřebují přístup k různým skupinám, aplikacím a webům, aby mohly vykonávat svou práci. Správa tohoto přístupu je náročná, protože požadavky se mění – přidávají se nové aplikace nebo uživatelé potřebují další přístupová práva.  Tento scénář se zkomplikuje při spolupráci s externími organizacemi – možná nevíte, kdo v jiné organizaci potřebuje přístup k prostředkům vaší organizace, a nebudou vědět, jaké aplikace, skupiny nebo weby vaše organizace používá.

Správa nároků Azure AD vám může pomoci efektivněji spravovat přístup k webům skupin, aplikací a SharePointu Online pro interní uživatele a také pro uživatele mimo vaši organizaci, kteří potřebují přístup k těmto prostředkům.

## <a name="why-use-entitlement-management"></a>Proč používat správu nároků?

Podnikové organizace se často potýkají s problémy při správě přístupu zaměstnanců k prostředkům, jako jsou:

- Uživatelé nemusí vědět, jaký přístup by měli mít, a i když ano, mohou mít potíže s nalezením správných osob pro schválení jejich přístupu
- Jakmile uživatelé vyhledáa přístup k prostředku a získají k němu přístup, mohou mít přístup delší, než je požadováno pro obchodní účely.

Tyto problémy jsou umocněny pro uživatele, kteří potřebují přístup z jiné organizace, jako jsou například externí uživatelé, kteří jsou z organizací dodavatelského řetězce nebo jiných obchodních partnerů. Například:

- Nikdo nemusí znát všechny konkrétní osoby v adresářích jiné organizace, aby je mohl pozvat
- I kdyby byli schopni tyto uživatele pozvat, nikdo v této organizaci si možná nepamatuje, že by měl spravovat veškerý přístup uživatele konzistentně.

Správa nároků Azure AD může pomoci řešit tyto problémy.  Další informace o tom, jak zákazníci používají správu oprávnění Azure AD, si můžete přečíst [případovou studii Avanade](https://aka.ms/AvanadeELMCase) a [případovou studii Centrica](https://aka.ms/CentricaELMCase).  Toto video poskytuje přehled správy nároků a jeho hodnoty:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>Co mohu dělat se správou nároků?

Zde jsou některé z možností správy nároků:

- Delegujte na jiné než správce možnost vytvářet přístupové balíčky. Tyto přístupové balíčky obsahují prostředky, které mohou uživatelé požadovat, a správci balíčků delegovaného přístupu mohou definovat zásady s pravidly, pro která mohou uživatelé požadovat, kdo musí schválit jejich přístup a kdy vyprší platnost přístupu.
- Vyberte připojené organizace, jejichž uživatelé mohou požádat o přístup.  Pokud uživatel, který ještě není ve vašem adresáři, požádá o přístup a je schválen, je automaticky pozván do vašeho adresáře a je mu přiřazen přístup.  Když jejich přístup vyprší, pokud nemají žádné jiné přiřazení balíčků přístupu, jejich účet B2B ve vašem adresáři lze automaticky odebrat.

Můžete začít s [naším tutorial vytvořit svůj první přístup ový balíček](entitlement-management-access-package-first.md). Můžete si také přečíst [běžné scénáře](entitlement-management-scenarios.md)nebo sledovat videa, včetně

- [Jak nasadit správu nároků Azure AD ve vaší organizaci](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Jak sledovat a škálovat vaše používání správy oprávnění Azure AD](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Jak delegovat ve správě nároků](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Co jsou přístupové balíčky a jaké prostředky s nimi mohu spravovat?

Správa oprávnění zavádí do služby Azure AD koncept *balíčku přístupu*. Přístupový balíček je sada všech prostředků s přístupem, který uživatel potřebuje k práci na projektu nebo k provedení svého úkolu. Přístupové balíčky se používají k řízení přístupu pro interní zaměstnance a také uživatele mimo vaši organizaci.

 Zde jsou typy prostředků, ke kterým můžete spravovat přístup uživatele pomocí správy nároků:

- Členství ve skupinách zabezpečení Azure AD
- Členství ve skupinách a týmech Office 365
- Přiřazení k podnikovým aplikacím Azure AD, včetně aplikací SaaS a vlastních integrovaných aplikací, které podporují federační/jednotné přihlašování a zřizování
- Členství na webech SharePointu Online

Můžete také řídit přístup k dalším prostředkům, které jsou závislé na skupinách zabezpečení Azure AD nebo skupinách Office 365.  Například:

- Uživatelům můžete udělit licence pro Microsoft Office 365 pomocí skupiny zabezpečení Azure AD v balíčku přístupu a konfigurací [licencí založených na skupinách](../users-groups-roles/licensing-groups-assign.md) pro tuto skupinu.
- Uživatelům můžete udělit přístup ke správě prostředků Azure pomocí skupiny zabezpečení Azure AD v balíčku přístupu a vytvořením [přiřazení role Azure](../../role-based-access-control/role-assignments-portal.md) pro tuto skupinu.

## <a name="how-do-i-control-who-gets-access"></a>Jak můžu kontrolovat, kdo získá přístup?

Pomocí balíčku pro přístup správce nebo delegovaný správce balíčků přístupu uvádí prostředky (skupiny, aplikace a weby) a role, které uživatelé pro tyto prostředky potřebují.

Balíčky aplikace Access také obsahují jednu nebo více *zásad*. Zásada definuje pravidla nebo svodidla pro přiřazení pro přístup k balíčku. Každá zásada může být použita k zajištění, že pouze příslušní uživatelé mohou požádat o přístup, že pro jejich žádost existují schvalovatelé a že jejich přístup k těmto prostředkům je časově omezený a vyprší, pokud nebude obnoven.

![Přístup balíček a zásady](./media/entitlement-management-overview/elm-overview-access-package.png)

V rámci každé zásady definuje správce nebo správce balíčků přístupu

- Buď již existující uživatelé (obvykle zaměstnanci nebo již pozvaní hosté) nebo partnerské organizace externích uživatelů, kteří jsou způsobilí požádat o přístup
- Proces schvalování a uživatelé, kteří mohou schválit nebo odepřít přístup
- Doba trvání přiřazení přístupu uživatele po schválení před vypršením platnosti přiřazení

Následující diagram znázorňuje příklad různých prvků správy nároků. Zobrazuje jeden katalog se dvěma ukázkovými balíčky přístupu.

- **Balíček access 1** obsahuje jednu skupinu jako prostředek. Aplikace Access je definována pomocí zásad, která umožňuje sadě uživatelů v adresáři požádat o přístup.
- **Balíček Access 2** zahrnuje jako prostředky skupinu, aplikaci a web SharePointu Online. Přístup je definován dvěma různými zásadami. První zásada umožňuje sadě uživatelů v adresáři požádat o přístup. Druhá zásada umožňuje uživatelům v externím adresáři požádat o přístup.

![Přehled správy nároků](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Kdy mám používat přístupové balíčky?

Balíčky přístupu nenahrazují jiné mechanismy pro přiřazení přístupu.  Jsou nejvhodnější v situacích, jako jsou následující:

- Zaměstnanci potřebují časově omezený přístup pro konkrétní úkol.  Můžete například použít skupinové licencování a dynamickou skupinu, abyste zajistili, že všichni zaměstnanci mají poštovní schránku Exchange Online, a pak použít přístupové balíčky pro situace, ve kterých zaměstnanci potřebují další přístup, například ke čtení prostředků oddělení z jiného Oddělení.
- Přístup musí být schválen manažerem zaměstnance nebo jinými určenými osobami.
- Oddělení chtějí spravovat své vlastní přístupové politiky pro své zdroje bez zapojení IT.  
- Dvě nebo více organizací spolupracují na projektu a v důsledku toho bude potřeba přinést více uživatelů z jedné organizace prostřednictvím Azure AD B2B, aby měli přístup k prostředkům jiné organizace.

## <a name="how-do-i-delegate-access"></a>Jak delegovat přístup?

 Přístupové balíčky jsou definovány v kontejnerech *nazývaných katalogy*.  Můžete mít jeden katalog pro všechny vaše přístupové balíčky nebo můžete určit jednotlivce, kteří budou vytvářet a vlastnit vlastní katalogy. Správce může přidávat prostředky do libovolného katalogu, ale nesprávce může do katalogu přidat pouze prostředky, které vlastní. Vlastník katalogu můžete přidat další uživatele jako spoluvlastníky katalogu nebo jako správce balíčků přístupu.  Tyto scénáře jsou popsány dále v legování článku [a role ve správě oprávnění Azure AD](entitlement-management-delegate.md).

## <a name="summary-of-terminology"></a>Shrnutí terminologie

Chcete-li lépe porozumět správě nároků a jeho dokumentaci, můžete se vrátit k následujícímu seznamu termínů.

| Označení | Popis |
| --- | --- |
| přístupový balíček | Sada zdrojů, které tým nebo projekt potřebuje a řídí se zásadami. Přístupový balíček je vždy obsažen v katalogu. Vytvořili byste nový přístupový balíček pro scénář, ve kterém uživatelé potřebují požádat o přístup.  |
| žádost o přístup | Požadavek na přístup k prostředkům v balíčku přístupu. Požadavek obvykle prochází pracovním postupem schválení.  Pokud je schválen, žádající uživatel obdrží přiřazení balíčku přístupu. |
| Přiřazení | Přiřazení přístupového balíčku uživateli zajišťuje, že uživatel má všechny role prostředků tohoto balíčku pro přístup.  Přiřazení balíčků aplikace Access mají obvykle časový limit před vypršením jejich platnosti. |
| Katalogu | Kontejner souvisejících prostředků a přístup k balíčkům.  Katalogy se používají pro delegování, takže nesprávci mohou vytvářet vlastní přístupové balíčky. Vlastníci katalogu mohou do katalogu přidávat prostředky, které vlastní. |
| katalog tvůrce | Kolekce uživatelů, kteří jsou oprávněni vytvářet nové katalogy.  Pokud uživatel bez oprávnění správce, který je oprávněn být tvůrcem katalogu, vytvoří nový katalog, automaticky se stane vlastníkem tohoto katalogu. |
| připojená organizace | Externí adresář nebo doména Azure AD, se kterou máte vztah. Uživatelé z připojené organizace mohou být v zásadě určeni jako uživatelé, kteří mohou požádat o přístup. |
| policy | Sada pravidel, která definuje životní cyklus přístupu, například jak uživatelé získají přístup, kdo může schválit a jak dlouho mají uživatelé přístup prostřednictvím přiřazení. Zásada je propojena s balíčkem přístupu. Balíček přístupu může mít například dvě zásady – jednu pro zaměstnance, která požaduje přístup, a druhou pro externí uživatele, kteří mohou požádat o přístup. |
| prostředek | Prostředek, například skupina Office, skupina zabezpečení, aplikace nebo web SharePointu Online, s rolí, ke které může být uživateli udělena oprávnění. |
| adresář prostředků | Adresář, který má jeden nebo více prostředků ke sdílení. |
| role zdroje | Kolekce oprávnění přidružených a definovaných prostředkem. Skupina má dvě role - člen a vlastník. Weby služby SharePoint mají obvykle 3 role, ale mohou mít další vlastní role. Aplikace mohou mít vlastní role. |


## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Specializované cloudy, jako je Azure Government, Azure Germany a Azure China 21Vianet, nejsou momentálně dostupné pro použití.

### <a name="how-many-licenses-must-you-have"></a>Kolik licencí musíte mít?

Ujistěte se, že váš adresář má alespoň tolik licencí Azure AD Premium P2, jako máte zaměstnance, kteří budou provádět následující úkoly:

- Uživatelé, kteří **mohou** požádat o přístupový balíček.
- Uživatelé a uživatelé typu Host, kteří požadují přístupový balíček.
- Uživatelé a uživatelé typu Host, kteří schvalují žádosti o přístupový balíček.

Licence Azure AD Premium P2 **nejsou** vyžadovány pro následující úkoly:

- Pro uživatele s rolí globálního správce, kteří nastavují počáteční katalogy, přístupové balíčky a zásady a delegují úkoly správy na ostatní uživatele, nejsou vyžadovány žádné licence.
- Pro uživatele, kterým byly delegovány úkoly správy, jako je například tvůrce katalogu, vlastník katalogu a správce balíčků přístupu, nejsou vyžadovány žádné licence.
- Pro hosty, kteří **mohou požádat** o přístupové balíčky, nejsou vyžadovány žádné licence, ale **nepožadují** přístupový balíček.

Pro každou placenou licenci Azure AD Premium P2, kterou zakoupíte pro členské uživatele (zaměstnance), můžete pomocí Azure AD B2B pozvat až 5 uživatelů typu Host. Tito uživatelé typu Host můžou taky používat funkce Azure AD Premium P2. Další informace najdete [v tématu Azure AD B2B pokyny k licencování spolupráce](../b2b/licensing-guidance.md).

Další informace o licencích najdete v tématu [Přiřazení nebo odebrání licencí pomocí portálu Azure Active Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Příklady scénářů licencí

Zde je několik příkladů licenčních scénářů, které vám pomohou určit počet licencí, které musíte mít.

| Scénář | Výpočet | Počet licencí |
| --- | --- | --- |
| Globální správce ve Woodgrove Bank vytvoří počáteční katalogy a deleguje úkoly správy na 6 dalších uživatelů. Jedna ze zásad určuje, že **všichni zaměstnanci** (2 000 zaměstnanců) mohou požadovat určitou sadu přístupových balíčků. Přístupové balíčky požaduje 150 zaměstnanců. | 2 000 zaměstnanců, kteří **mohou** požádat o přístupové balíčky | 2 000 |
| Globální správce ve Woodgrove Bank vytvoří počáteční katalogy a deleguje úkoly správy na 6 dalších uživatelů. Jedna ze zásad určuje, že **všichni zaměstnanci** (2 000 zaměstnanců) mohou požadovat určitou sadu přístupových balíčků. Jiné zásady určují, že někteří **uživatelé z uživatelů z partnera Contoso** (hosté) mohou požádat o stejné přístupové balíčky podléhající schválení. Contoso má 30 000 uživatelů. Přístupové balíčky požaduje 150 zaměstnanců a přístup 10 500 uživatelů od společnosti Contoso požaduje přístup. | 2 000 zaměstnanců + 500 hostujících uživatelů společnosti Contoso, kteří překračují poměr 1:5 (10 500 - (2 000 * 5)) | 2,500 |

## <a name="next-steps"></a>Další kroky

- [Kurz: Vytvoření prvního přístupového balíčku](entitlement-management-access-package-first.md)
- [Obvyklé scénáře](entitlement-management-scenarios.md)
