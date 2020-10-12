---
title: Prostředí pro vyjádření souhlasu s aplikací Azure AD
titleSuffix: Microsoft identity platform
description: Přečtěte si další informace o prostředí pro vyjádření souhlasu Azure AD a zjistěte, jak ho můžete používat při správě a vývoji aplikací v Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: ryanwi
ms.reviewer: zachowd
ms.openlocfilehash: 6e768c1e938006afd62fc097a80f8ebc3ea0f3e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88115471"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Prostředí vyjádření souhlasu s aplikací Azure AD

Přečtěte si další informace o uživatelském prostředí souhlasu aplikace Azure Active Directory (Azure AD). Takže můžete inteligentně spravovat aplikace pro vaši organizaci nebo vyvíjet aplikace s využitím bezproblémového souhlasu.

## <a name="consent-and-permissions"></a>Souhlas a oprávnění

Souhlas je proces uživatele, který uděluje oprávnění k přístupu k chráněným prostředkům jménem uživatele. Správce nebo uživatel může požádat o souhlas s povolením přístupu k jejich organizaci nebo individuálním datům.

Skutečné uživatelské prostředí udělování souhlasu se bude lišit v závislosti na zásadách nastavených v tenantovi uživatele, rozsahu uživatele autority (nebo roli) a typu [oprávnění](../azuread-dev/v1-permissions-consent.md) , které klientská aplikace požaduje. To znamená, že vývojáři aplikací a Správci klientů mají kontrolu nad tím, jakým způsobem se bude jednat o používání souhlasu. Správci mají flexibilitu nastavení a zakázání zásad v tenantovi nebo aplikaci, aby mohli řídit možnosti souhlasu ve svém tenantovi. Vývojáři aplikací mohou určovat, jaké typy oprávnění jsou požadovány, a pokud chtějí uživatele pořídit pomocí toku souhlasu uživatele nebo toku souhlasu správce.

- **Tok souhlasu uživatele** je v případě, že vývojář aplikace přesměruje uživatele na koncový bod autorizace s cílem zaznamenat souhlas jenom pro aktuálního uživatele.
- **Tok souhlasu správce** je v případě, že vývojář aplikace nasměruje uživatele do koncového bodu souhlasu správce s cílem zaznamenat souhlas celého tenanta. Aby bylo zajištěno, že tok souhlasu správce funguje správně, musí vývojáři aplikací zobrazit seznam všech oprávnění ve `RequiredResourceAccess` vlastnosti v manifestu aplikace. Další informace naleznete v tématu [manifest aplikace](./reference-app-manifest.md).

## <a name="building-blocks-of-the-consent-prompt"></a>Stavební bloky výzvy k vyjádření souhlasu

Výzva k vyjádření souhlasu je navržena tak, aby uživatelé měli dostatek informací, aby zjistili, jestli důvěřují klientské aplikaci za účelem přístupu k chráněným prostředkům jejich jménem. Porozumění stavebním blokům pomůže uživatelům udělit souhlas, dělat podrobnější rozhodnutí a pomůže vývojářům sestavovat lepší uživatelské prostředí.

Následující diagram a tabulka obsahují informace o stavebních blocích výzvy k zadání souhlasu.

![Stavební bloky výzvy k vyjádření souhlasu](./media/application-consent-experience/consent_prompt.png)

| # | Součást | Účel |
| ----- | ----- | ----- |
| 1 | Identifikátor uživatele | Tento identifikátor představuje uživatele, který klientská aplikace žádá o přístup k chráněným prostředkům jménem. |
| 2 | Nadpis | Název se změní na základě toho, jestli uživatelé procházejí v toku souhlasu uživatele nebo správce. V toku souhlasu uživatele bude mít název "požadovaná oprávnění", zatímco v toku souhlasu správce bude mít název další řádek "přijmout pro vaši organizaci". |
| 3 | Logo aplikace | Tento obrázek by měl uživatelům pomáhat s vizuálním znázorněním, zda je tato aplikace aplikací, ke které má přístup. Tuto image poskytují vývojáři aplikací a vlastnictví této image se neověřuje. |
| 4 | Název aplikace | Tato hodnota by měla informovat uživatele, které aplikace žádá o přístup ke svým datům. Poznámka: Tento název poskytují vývojáři a vlastnictví tohoto názvu aplikace se neověřuje. |
| 5 | Doména vydavatele | Tato hodnota by měla uživatelům poskytnout doménu, kterou by mohli vyhodnotit důvěryhodnost. Tuto doménu poskytují vývojáři a je ověřeno vlastnictví této domény vydavatele. |
| 6 | Oprávnění | Tento seznam obsahuje oprávnění požadovaná klientskou aplikací. Uživatelé by měli vždy vyhodnotit typy požadovaných oprávnění, abyste pochopili, jaká data bude klientská aplikace oprávněna k přístupu jménem uživatele, pokud přijmou. Jako vývojář aplikace je nejvhodnější požádat o přístup k oprávněním s nejnižšími oprávněními. |
| 7 | Popis oprávnění | Tuto hodnotu poskytuje služba, která zveřejňuje oprávnění. Chcete-li zobrazit popisy oprávnění, je nutné přepnout dvojitou šipku vedle oprávnění. |
| 8 | Výrazy aplikace | Tyto výrazy obsahují odkazy na pravidla služby a prohlášení o zásadách ochrany osobních údajů v aplikaci. Vydavatel je zodpovědný za sbalení svých pravidel podle podmínek služby. Vydavatel je navíc zodpovědný za způsob, jakým uživatel využije a sdílí uživatelská data v prohlášení o zásadách ochrany osobních údajů. Pokud Vydavatel neposkytne odkazy na tyto hodnoty pro víceklientské aplikace, zobrazí se na výzvu k zadání tučné upozornění. |
| 9 | https://myapps.microsoft.com | Toto je odkaz, ve kterém mohou uživatelé zkontrolovat a odebrat všechny aplikace od jiných výrobců, které mají v současnosti přístup k jejich datům. |

## <a name="common-consent-scenarios"></a>Obvyklé scénáře souhlasu

Tady jsou informace o tom, že se uživatel může setkat ve scénářích společného souhlasu:

1. Jednotlivců, kteří přistupují k aplikaci, která je směruje na tok souhlasu uživatele, a přitom vyžadují sadu oprávnění, která je v rámci jejich oboru autority.
    
    1. Správci uvidí další kontrolu na základě obvyklého souhlasu s výzvou, která jim umožní souhlas jménem celého tenanta. Ovládací prvek bude ve výchozím nastavení vypnutý, takže jenom když správci výslovně zaškrtli políčko, bude se mu udělit souhlas pro celého tenanta. Od dnešního dne se toto zaškrtávací políčko zobrazí jenom pro roli globálního správce, takže správce cloudu a Správce aplikací toto políčko neuvidí.

        ![Výzva k vyjádření souhlasu pro scénář 1a](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. Uživatelům se zobrazí výzva k zadání tradičního souhlasu.

        ![Výzva k vyjádření souhlasu pro scénář 1B](./media/application-consent-experience/consent_prompt_1b.png)

2. Jednotlivci, kteří přistupují k aplikaci, která vyžaduje aspoň jedno oprávnění, které je mimo jejich rozsah autority.
    1. Správci uvidí stejnou výzvu jako 1. výše, jak je uvedeno výše.
    2. Uživatelům bude zablokováno udělení souhlasu s aplikací a budou jim přizvat, aby požádali správce o přístup k aplikaci. 
                
        ![Výzva k vyjádření souhlasu pro scénář 1B](./media/application-consent-experience/consent_prompt_2b.png)

3. Jednotlivci, kteří přecházejí nebo jsou přesměrováni na tok souhlasu správce.
    1. Uživatelům s oprávněními správce se zobrazí výzva k vyjádření souhlasu správce. Název a popis oprávnění se u této výzvy změnily. změny upozorňující na skutečnost, že přijetí této výzvy udělí aplikaci přístup k požadovaným datům jménem celého tenanta.
        
        ![Výzva k vyjádření souhlasu pro scénář 1B](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. Uživatelům, kteří nejsou správci, uvidí stejnou obrazovku jako 2. II, jak je uvedeno výše.

## <a name="next-steps"></a>Další kroky
- Získejte podrobný přehled o [tom, jak rozhraní pro vyjádření souhlasu Azure AD implementuje souhlas](./quickstart-register-app.md).
- Podrobnější informace najdete v článku [o tom, jak aplikace pro více tenantů může používat souhlasu architektury](./howto-convert-app-to-be-multi-tenant.md) k implementaci souhlasu "User" a "admin", který podporuje pokročilejší vzory vícevrstvých aplikací.
- Naučte [se konfigurovat doménu vydavatele aplikace](howto-configure-publisher-domain.md).