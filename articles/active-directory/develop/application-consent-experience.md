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
ms.date: 04/06/2021
ms.author: ryanwi
ms.reviewer: jesakowi, asteen
ms.openlocfilehash: c570fc9f30d69f13546353cf6edab4122ae35142
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105402"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Prostředí vyjádření souhlasu s aplikací Azure AD

Přečtěte si další informace o uživatelském prostředí souhlasu aplikace Azure Active Directory (Azure AD). Takže můžete inteligentně spravovat aplikace pro vaši organizaci nebo vyvíjet aplikace s využitím bezproblémového souhlasu.

## <a name="consent-and-permissions"></a>Souhlas a oprávnění

Souhlas je proces uživatele, který uděluje oprávnění k přístupu k chráněným prostředkům jménem uživatele. Správce nebo uživatel může požádat o souhlas s povolením přístupu k jejich organizaci nebo individuálním datům.

Skutečné uživatelské prostředí udělování souhlasu se bude lišit v závislosti na zásadách nastavených v tenantovi uživatele, rozsahu uživatele autority (nebo roli) a typu [oprávnění](v2-permissions-and-consent.md) , které klientská aplikace požaduje. To znamená, že vývojáři aplikací a Správci klientů mají kontrolu nad tím, jakým způsobem se bude jednat o používání souhlasu. Správci mají flexibilitu nastavení a zakázání zásad v tenantovi nebo aplikaci, aby mohli řídit možnosti souhlasu ve svém tenantovi. Vývojáři aplikací mohou určovat, jaké typy oprávnění jsou požadovány, a pokud chtějí uživatele pořídit pomocí toku souhlasu uživatele nebo toku souhlasu správce.

- **Tok souhlasu uživatele** je v případě, že vývojář aplikace přesměruje uživatele na koncový bod autorizace s cílem zaznamenat souhlas jenom pro aktuálního uživatele.
- **Tok souhlasu správce** je v případě, že vývojář aplikace nasměruje uživatele do koncového bodu souhlasu správce s cílem zaznamenat souhlas celého tenanta. Aby bylo zajištěno, že tok souhlasu správce funguje správně, musí vývojáři aplikací zobrazit seznam všech oprávnění ve `RequiredResourceAccess` vlastnosti v manifestu aplikace. Další informace naleznete v tématu [manifest aplikace](./reference-app-manifest.md).

## <a name="building-blocks-of-the-consent-prompt"></a>Stavební bloky výzvy k vyjádření souhlasu

Výzva k vyjádření souhlasu je navržena tak, aby uživatelé měli dostatek informací, aby zjistili, jestli důvěřují klientské aplikaci za účelem přístupu k chráněným prostředkům jejich jménem. Porozumění stavebním blokům pomůže uživatelům udělit souhlas, dělat podrobnější rozhodnutí a pomůže vývojářům sestavovat lepší uživatelské prostředí.

Následující diagram a tabulka obsahují informace o stavebních blocích výzvy k zadání souhlasu.

:::image type="content" source="./media/application-consent-experience/consent_prompt.png" alt-text="Stavební bloky výzvy k vyjádření souhlasu":::

| # | Součást | Účel |
| ----- | ----- | ----- |
| 1 | Identifikátor uživatele | Tento identifikátor představuje uživatele, který klientská aplikace žádá o přístup k chráněným prostředkům jménem. |
| 2 | Nadpis | Název se změní na základě toho, jestli uživatelé procházejí v toku souhlasu uživatele nebo správce. V toku souhlasu uživatele bude mít název "požadovaná oprávnění", zatímco v toku souhlasu správce bude mít název další řádek "přijmout pro vaši organizaci". |
| 3 | Logo aplikace | Tento obrázek by měl uživatelům pomáhat s vizuálním znázorněním, zda je tato aplikace aplikací, ke které má přístup. Tuto image poskytují vývojáři aplikací a vlastnictví této image se neověřuje. |
| 4 | Název aplikace | Tato hodnota by měla informovat uživatele, které aplikace žádá o přístup ke svým datům. Poznámka: Tento název poskytují vývojáři a vlastnictví tohoto názvu aplikace se neověřuje. |
| 5 | Doména vydavatele | Tato hodnota by měla uživatelům poskytnout doménu, kterou by mohli vyhodnotit důvěryhodnost. Tuto doménu poskytují vývojáři a je ověřeno vlastnictví této domény vydavatele. |
| 6 | Vydavatel byl ověřen. | Modrá "ověřená" visačka znamená, že vydavatel aplikace ověřil svoji identitu pomocí účtu Microsoft Partner Network a dokončil proces ověření.|
| 7 | Informace o vydavateli  | Zobrazuje, zda je aplikace publikována společností Microsoft nebo vaší organizací. |
| 8 | Oprávnění | Tento seznam obsahuje oprávnění požadovaná klientskou aplikací. Uživatelé by měli vždy vyhodnotit typy požadovaných oprávnění, abyste pochopili, jaká data bude klientská aplikace oprávněna k přístupu jménem uživatele, pokud přijmou. Jako vývojář aplikace je nejvhodnější požádat o přístup k oprávněním s nejnižšími oprávněními. |
| 9 | Popis oprávnění | Tuto hodnotu poskytuje služba, která zveřejňuje oprávnění. Chcete-li zobrazit popisy oprávnění, je nutné přepnout dvojitou šipku vedle oprávnění. |
| 10| Výrazy aplikace | Tyto výrazy obsahují odkazy na pravidla služby a prohlášení o zásadách ochrany osobních údajů v aplikaci. Vydavatel je zodpovědný za sbalení svých pravidel podle podmínek služby. Vydavatel je navíc zodpovědný za způsob, jakým uživatel využije a sdílí uživatelská data v prohlášení o zásadách ochrany osobních údajů. Pokud Vydavatel neposkytne odkazy na tyto hodnoty pro víceklientské aplikace, zobrazí se na výzvu k zadání tučné upozornění. |
| 11 | https://myapps.microsoft.com | Toto je odkaz, ve kterém mohou uživatelé zkontrolovat a odebrat všechny aplikace od jiných výrobců, které mají v současnosti přístup k jejich datům. |
| 12 | Sem nahlásit | Tento odkaz slouží k nahlášení podezřelé aplikace, pokud nedůvěřujete aplikaci, pokud se domníváte, že aplikace zosobňuje jinou aplikaci, pokud se domníváte, že aplikace bude vaše data zneužít, nebo z nějakého jiného důvodu. |

## <a name="app-requires-a-permission-within-the-users-scope-of-authority"></a>Aplikace vyžaduje oprávnění v oboru uživatele, který je členem autority.

Obvyklým scénářem souhlasu je to, že uživatel přistupuje k aplikaci, která vyžaduje sadu oprávnění, která je v oboru autority daného uživatele. Uživatel se přesměruje na tok souhlasu uživatele.

Správci uvidí další kontrolu na základě obvyklého souhlasu s výzvou, která jim umožní souhlas jménem celého tenanta. Ovládací prvek bude ve výchozím nastavení vypnutý, takže jenom když správci výslovně zaškrtli políčko, bude se mu udělit souhlas pro celého tenanta. Od dnešního dne se toto zaškrtávací políčko zobrazí jenom pro roli globálního správce, takže správce cloudu a Správce aplikací toto políčko neuvidí.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1a.png" alt-text="Výzva k vyjádření souhlasu pro scénář 1a":::

Uživatelům se zobrazí výzva k zadání tradičního souhlasu.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1b.png" alt-text="Snímek obrazovky zobrazující výzvu k tradičnímu souhlasu":::

## <a name="app-requires-a-permission-outside-of-the-users-scope-of-authority"></a>Aplikace vyžaduje oprávnění mimo rozsah autority daného uživatele.

Dalším scénářem společného souhlasu je to, že uživatel přistupuje k aplikaci, která vyžaduje aspoň jedno oprávnění, které je mimo rozsah autority daného uživatele.

Správci uvidí další kontrolu na základě obvyklého souhlasu s výzvou, která jim umožní souhlas jménem celého tenanta.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1a.png" alt-text="Výzva k vyjádření souhlasu pro scénář 1a":::

Uživatelům, kteří nejsou správci, budou zablokováni udělení souhlasu aplikaci a budou jim přizvat, aby požádaly správce o přístup k aplikaci.

:::image type="content" source="./media/application-consent-experience/consent_prompt_2b.png" alt-text="Snímek obrazovky s výzvou k vyjádření souhlasu uživatele informuje, že má správce, aby požádal o přístup k aplikaci.":::

## <a name="user-is-directed-to-the-admin-consent-flow"></a>Uživatel se přesměruje na tok souhlasu správce.

Dalším běžným scénářem je situace, kdy uživatel přejde na tok souhlasu správce nebo se mu přesměruje.

Uživatelům s oprávněními správce se zobrazí výzva k vyjádření souhlasu správce. Název a popis oprávnění se u této výzvy změnily. změny upozorňující na skutečnost, že přijetí této výzvy udělí aplikaci přístup k požadovaným datům jménem celého tenanta.

:::image type="content" source="./media/application-consent-experience/consent_prompt_3a.png" alt-text="Výzva k vyjádření souhlasu pro scénář 3a":::

Uživatelům, kteří nejsou správci, budou zablokováni udělení souhlasu aplikaci a budou jim přizvat, aby požádaly správce o přístup k aplikaci.

:::image type="content" source="./media/application-consent-experience/consent_prompt_2b.png" alt-text="Snímek obrazovky s výzvou k vyjádření souhlasu uživatele informuje, že má správce, aby požádal o přístup k aplikaci.":::

## <a name="next-steps"></a>Další kroky

- Získejte podrobný přehled o [tom, jak rozhraní pro vyjádření souhlasu Azure AD implementuje souhlas](./quickstart-register-app.md).
- Podrobnější informace najdete v článku [o tom, jak aplikace pro více tenantů může používat souhlasu architektury](./howto-convert-app-to-be-multi-tenant.md) k implementaci souhlasu "User" a "admin", který podporuje pokročilejší vzory vícevrstvých aplikací.
- Naučte [se konfigurovat doménu vydavatele aplikace](howto-configure-publisher-domain.md).