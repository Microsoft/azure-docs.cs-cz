---
title: Co je správa nároků Azure AD? (Preview) – Azure Active Directory
description: Získejte přehled o Azure Active Directory správě nároků a o tom, jak ho můžete používat ke správě přístupu ke skupinám, aplikacím a webům SharePointu Online pro interní a externí uživatele.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7a8df09322def77037575b78ddbff3fae374229
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062738"
---
# <a name="what-is-azure-ad-entitlement-management-preview"></a>Co je správa nároků Azure AD? (Preview)

> [!IMPORTANT]
> Správa opravňujících k Azure Active Directory (Azure AD) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Správa nároků na Azure Active Directory (Azure AD) je funkce [zásad správného řízení identity](identity-governance-overview.md) , která organizacím umožňuje spravovat životní cyklus identit a přístupu, a to díky automatizaci pracovních postupů pro přístup k žádostem o přístup, přiřazení přístupu, kontrolám a vypršení platnosti.

Zaměstnanci v organizacích potřebují k provádění svých úloh přístup k různým skupinám, aplikacím a webům. Správa tohoto přístupu je náročná, protože se mění požadavky – přidávají se nové aplikace nebo uživatelé potřebují další přístupová práva.  Tento scénář je při spolupráci s externími organizacemi složitější – nevíte, kdo v jiné organizaci potřebuje přístup k prostředkům vaší organizace, a neinformuje o tom, jaké aplikace, skupiny nebo weby vaše organizace používá.

Správa nároků Azure AD vám může pomáhat efektivněji spravovat přístup ke skupinám, aplikacím a webům SharePointu Online pro interní uživatele a také pro uživatele mimo vaši organizaci, kteří potřebují přístup k těmto prostředkům.

## <a name="why-use-entitlement-management"></a>Proč používat správu nároků?

Podnikové organizace často čelí problémům při správě přístupu zaměstnanců k prostředkům, jako jsou:

- Uživatelé nemusí znát, jaký má mít přístup, a i když mají, můžou mít potíže při hledání příslušných jednotlivců ke schválení přístupu.
- Jakmile uživatelé naleznou a získají přístup k prostředku, můžou mít přístup k delšímu, než je nutné pro obchodní účely.

Tyto problémy jsou složené pro uživatele, kteří potřebují přístup z jiné organizace, jako jsou externí uživatelé, kteří pocházejí z organizací dodavatelských řetězců nebo jiných obchodních partnerů. Například:

- Nikdo z nich nemusí znát všechny konkrétní uživatele v adresářích jiných organizací, aby je mohl pozvat.
- I když byli schopni pozvat tyto uživatele, nikdo z těchto organizací nemusí pamatovat na správu všech přístupů uživatele konzistentně.

Správa nároků Azure AD může tyto výzvy vyřešit.  Pokud chcete získat další informace o tom, jak zákazníci používají správu nároků služby Azure AD, můžete si přečíst [případovou studii Avanade](https://aka.ms/AvanadeELMCase) a [případovou studii na střed](https://aka.ms/CentricaELMCase).  Toto video poskytuje přehled o správě nároků a jeho hodnotě:

>[!VIDEO https://www.youtube.com/embed/_Lss6bFrnQ8]

## <a name="what-can-i-do-with-entitlement-management"></a>Co se dá dělat se správou nároků?

Tady jsou některé možnosti správy nároků:

- Delegovat na nesprávce možnost vytvářet balíčky přístupu. Tyto balíčky pro přístup obsahují prostředky, které si uživatelé můžou vyžádat, a správci balíčků s delegovaným přístupem můžou definovat zásady s pravidly, která si uživatelé můžou vyžádat, kdo musí schválit jejich přístup a když vyprší platnost přístupu.
- Vyberte připojené organizace, jejichž uživatelé můžou požádat o přístup.  Když uživatel, který ještě není ve vašem adresáři, žádá o přístup a schválí se, automaticky se přihlásí do vašeho adresáře a přiřadí se mu přístup.  Pokud jejich platnost vyprší, pokud nemají žádné jiné přiřazení balíčků přístupu, může se jejich účet B2B v adresáři automaticky odebrat.

Můžete začít s naším [kurzem a vytvořit svůj první balíček přístupu](entitlement-management-access-package-first.md). Můžete si také přečíst [běžné scénáře](entitlement-management-scenarios.md)nebo sledovat videa, včetně

- [Jak nasadit správu nároků Azure AD ve vaší organizaci](https://www.youtube.com/watch?v=zaaKvaaYwI4)
- [Jak monitorovat a škálovat používání správy nároků Azure AD](https://www.youtube.com/watch?v=omtNJ7ySjS0)
- [Jak delegovat správu nároků](https://www.youtube.com/watch?v=Fmp1eBxzrqw)

## <a name="what-are-access-packages-and-what-resources-can-i-manage-with-them"></a>Co jsou balíčky přístupu a které prostředky mohu s nimi spravovat?

Správa nároků zavádí do Azure AD koncept *balíčku pro přístup*. Balíček pro přístup je sada všech prostředků s přístupem, které uživatel potřebuje k práci na projektu, nebo k provedení úkolu. Přístupové balíčky slouží k řízení přístupu k vašim interním zaměstnancům a také uživatelům mimo vaši organizaci.

 Tady jsou typy prostředků, ke kterým můžete spravovat přístup uživatelů pomocí správy nároků:

- Členství ve skupinách zabezpečení služby Azure AD
- Členství skupin a týmů Office 365
- Přiřazení k podnikovým aplikacím Azure AD, včetně aplikací SaaS a aplikací integrovaných s vlastními, které podporují federaci, jednotné přihlašování a/nebo zřizování
- Členství na webech SharePointu Online

Můžete také řídit přístup k dalším prostředkům, které spoléhají na skupiny zabezpečení Azure AD nebo skupiny Office 365.  Například:

- Uživatelům můžete udělit licence pro systém Microsoft Office 365 pomocí skupiny zabezpečení Azure AD v balíčku přístupu a konfigurací [licencování na základě skupin](../users-groups-roles/licensing-groups-assign.md) pro tuto skupinu.
- Uživatelům můžete udělit přístup ke správě prostředků Azure pomocí skupiny zabezpečení Azure AD v balíčku pro přístup a vytvoření [přiřazení role Azure](../../role-based-access-control/role-assignments-portal.md) pro tuto skupinu.

## <a name="how-do-i-control-who-gets-access"></a>Návody ovládací prvek, který získá přístup?

Pomocí balíčku pro přístup správce nebo delegovaný přístup správce balíčků vypíše prostředky (skupiny, aplikace a weby) a role, které uživatelé potřebují pro tyto prostředky.

Balíčky pro přístup také obsahují jednu nebo více *zásad*. Zásady definují pravidla nebo guardrails pro přiřazení pro přístup k balíčku. Každá zásada se dá použít k zajištění toho, že bude moct požádat o přístup jenom příslušné uživatele, že pro svůj požadavek existují schvalovatelé a že jejich přístup k těmto prostředkům bude omezený na čas a vyprší, pokud se neobnoví.

![Přístup k balíčkům a zásadám](./media/entitlement-management-overview/elm-overview-access-package.png)

V rámci každé zásady definuje správce nebo správce balíčků pro přístup

- Buď už stávající uživatelé (obvykle zaměstnanci nebo už pozvaní hosté), nebo partnerské organizace externích uživatelů, kteří mají nárok na přístup
- Proces schvalování a uživatelé, kteří mohou schvalovat nebo odmítat přístup
- Doba trvání přiřazení přístupu uživatele po schválení, před vypršením platnosti přiřazení

Následující diagram znázorňuje příklad různých prvků ve správě nároků. Zobrazuje jeden katalog se dvěma vzorovými balíčky přístupu.

- **Přístup k balíčku 1** zahrnuje jednu skupinu jako prostředek. Přístup je definován pomocí zásady, která umožňuje sadě uživatelů v adresáři požádat o přístup.
- **Přístup k balíčku 2** obsahuje skupinu, aplikaci a web SharePointu Online jako prostředky. Přístup je definován pomocí dvou různých zásad. První zásada umožňuje sadě uživatelů v adresáři požádat o přístup. Druhá zásada umožňuje uživatelům v externím adresáři požádat o přístup.

![Přehled správy nároků](./media/entitlement-management-overview/elm-overview.png)

## <a name="when-should-i-use-access-packages"></a>Kdy mám použít balíčky přístupu?

Přístupové balíčky nenahrazují jiné mechanismy pro přiřazení přístupu.  Jsou nejvhodnější v situacích, jako jsou například tyto:

- Zaměstnanci potřebují časově omezený přístup pro konkrétní úkol.  Například můžete použít licencování na základě skupin a dynamickou skupinu, abyste zajistili, že všichni zaměstnanci mají poštovní schránku Exchange Online, a pak použijete přístupové balíčky pro situace, kdy zaměstnanci potřebují další přístup, jako je například čtení prostředků oddělení z jiného. Ministerstvo.
- Přístup musí schválit Manažer zaměstnance nebo jiní určení jednotlivci.
- Oddělení, která chtějí spravovat zásady přístupu ke svým prostředkům bez zapojení do IT.  
- V projektu spolupracuje dvě nebo víc organizací a výsledkem je, že pro přístup k prostředkům jiné organizace bude potřeba, aby se do služby Azure AD B2B napravilo více uživatelů z jedné organizace.

## <a name="how-do-i-delegate-access"></a>Návody přístup delegáta?

 Balíčky přístupu jsou definované v kontejnerech nazývaných *katalogy*.  Můžete mít jeden katalog pro všechny balíčky pro přístup, nebo můžete určit jednotlivce pro vytváření a vlastní své vlastní katalogy. Správce může přidávat prostředky do libovolného katalogu, ale bez oprávnění správce může přidat do katalogu pouze prostředky, které vlastní. Vlastník katalogu může přidat další uživatele jako spolupracovníky katalogu nebo jako správce balíčků přístupu.  Tyto scénáře jsou podrobněji popsány v článku [delegování a role v Azure AD – Správa nároků](entitlement-management-delegate.md).

## <a name="summary-of-terminology"></a>Souhrn terminologie

Chcete-li lépe pochopit správu nároků a její dokumentaci, můžete se vrátit k následujícímu seznamu podmínek.

| Doba účinnosti | Popis |
| --- | --- |
| přístup k balíčku | Sada prostředků, které tým nebo projekt potřebuje a řídí se zásadami. Přístupový balíček je vždycky obsažený v katalogu. Vytvořili jste nový balíček pro přístup pro scénář, ve kterém uživatelé potřebují požádat o přístup.  |
| žádost o přístup | Žádost o přístup k prostředkům v balíčku pro přístup Požadavek obvykle prochází schvalovacím pracovním postupem.  V případě schválení obdrží žádající uživatel přiřazení balíčku přístupu. |
| přiřazení | Přiřazení balíčku přístupu uživateli zajistí, že uživatel bude mít všechny role prostředků tohoto balíčku přístupu.  Přístup k přiřazením balíčků má obvykle časový limit před vypršením platnosti. |
| zařazení | Kontejner souvisejících prostředků a přístupových balíčků.  Katalogy se používají pro delegování, aby nesprávci mohli vytvářet vlastní balíčky přístupu. Vlastníci katalogu můžou přidat prostředky, které vlastní, do katalogu. |
| Tvůrce katalogu | Kolekce uživatelů, kteří mají oprávnění vytvořit nové katalogy.  Když uživatel bez oprávnění správce, který je autorizován jako tvůrce katalogu, vytvoří nový katalog, stane se automaticky vlastníkem tohoto katalogu. |
| připojená organizace | Externí adresář nebo doména služby Azure AD, se kterou máte relaci. Uživatelé z připojené organizace můžou být v zásadách uvedeni tak, aby si vyžádali přístup. |
| policy | Sada pravidel definujících životní cyklus přístupu, například způsob, jakým uživatelé získávají přístup, kteří můžou schvalovat a jak dlouho mají uživatelé přístup prostřednictvím přiřazení. Zásada je propojená s balíčkem přístupu. Přístupový balíček může například mít dvě zásady – jeden pro zaměstnance, kteří požadují přístup a druhý pro externí uživatele, aby si vyžádali přístup. |
| Partner | Asset, jako je například skupina Office, skupina zabezpečení, aplikace nebo web SharePointu Online, s rolí, pro kterou může uživatel udělit oprávnění. |
| Adresář prostředků | Adresář, který obsahuje jeden nebo více prostředků ke sdílení. |
| role prostředku | Kolekce oprávnění přidružených k prostředku a definovanému prostředkem. Skupina má dvě role – člena a vlastníka. Weby SharePointu mají obvykle 3 role, ale mohou mít další vlastní role. Aplikace mohou mít vlastní role. |


## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Specializované cloudy, například Azure Government, Azure Německo a Azure Čína 21Vianet, nejsou v tuto chvíli k dispozici pro použití v této verzi Preview.

### <a name="which-users-must-have-licenses"></a>Kteří uživatelé musí mít licence?

Aby váš tenant měl aktivní uživatele ve správě nároků, musí mít aspoň tolik licencí Azure AD Premium P2. Mezi aktivní členské uživatele v řízení nároků patří:

- Uživatel, který iniciuje nebo schválí požadavek na balíček pro přístup
- Uživatel, kterému byl přiřazen balíček přístupu.
- Uživatel, který spravuje přístupové balíčky.

V rámci licencí pro členské uživatele můžete také uživatelům dovolit, aby mohli pracovat s řízením nároků na řadu uživatelů typu Host. Informace o tom, jak vypočítat počet uživatelů typu Host, které můžete zahrnout, najdete v tématu [Azure Active Directory doprovodné materiály k licencování B2B pro spolupráci](../b2b/licensing-guidance.md).

Informace o tom, jak přiřadit licence uživatelům, najdete v tématu [přiřazení nebo odebrání licencí pomocí portálu Azure Active Directory](../fundamentals/license-users-groups.md). Všimněte si, že správa nároků aktuálně neuplatňuje přiřazování licencí pro uživatele.

## <a name="next-steps"></a>Další kroky

- [Kurz: vytvoření prvního balíčku pro přístup](entitlement-management-access-package-first.md)
- [Obvyklé scénáře](entitlement-management-scenarios.md)
