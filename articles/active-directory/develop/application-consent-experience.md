---
title: Prostředí pro souhlas s aplikací Azure AD
titleSuffix: Microsoft identity platform
description: Další informace o prostředí chodu souhlasu Azure AD a zjistěte, jak ho můžete používat při správě a vývoji aplikací ve službě Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: ryanwi
ms.reviewer: zachowd
ms.openlocfilehash: c9b449b65a8f8def9dc28a668cd9ee3671124cb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484498"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Prostředí vyjádření souhlasu s aplikací Azure AD

Další informace o uživatelském prostředí aplikace Azure Active Directory (Azure AD) se souhlasem. Takže můžete inteligentně spravovat aplikace pro vaši organizaci a/nebo vyvíjet aplikace s bezproblémovějším prostředím pro souhlas.

## <a name="consent-and-permissions"></a>Souhlas a oprávnění

Souhlas je proces, kdy uživatel udělí oprávnění k aplikaci přístupu k chráněným prostředkům jejich jménem. Správce nebo uživatel může být požádán o souhlas s povolením přístupu k jejich organizaci / individuálním datům.

Skutečné uživatelské prostředí udělení souhlasu se bude lišit v závislosti na zásadách nastavených na klientovi uživatele, rozsahu oprávnění uživatele (nebo roli) a typu [oprávnění](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-permissions-consent) požadovaných klientskou aplikací. To znamená, že vývojáři aplikací a správci tenantů mají určitou kontrolu nad prostředím souhlasu. Správci mají flexibilitu nastavení a zakázání zásad v tenantovi nebo aplikaci pro řízení prostředí souhlasu v jejich tenantovi. Vývojáři aplikací mohou diktovat, jaké typy oprávnění jsou požadovány a zda chtějí uživatele vést tokem souhlasu uživatele nebo tokem souhlasu správce.

- **Tok souhlasu uživatele** je, když vývojář aplikace přesměruje uživatele na koncový bod autorizace s úmyslem zaznamenat souhlas pouze pro aktuálního uživatele.
- **Tok souhlasu správce** je, když vývojář aplikace přesměruje uživatele na koncový bod souhlasu správce s úmyslem zaznamenat souhlas pro celý klient. Chcete-li zajistit, aby tok souhlasu správce `RequiredResourceAccess` fungoval správně, musí vývojáři aplikací uvést všechna oprávnění ve vlastnosti v manifestu aplikace. Další informace naleznete v tématu [Manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest).

## <a name="building-blocks-of-the-consent-prompt"></a>Stavební kameny výzvy k souhlasu

Výzva k souhlasu je navržena tak, aby zajistila, že uživatelé budou mít dostatek informací k určení, zda důvěřují klientské aplikaci pro přístup k chráněným prostředkům jejich jménem. Pochopení stavebních bloků pomůže uživatelům udělujícím souhlas činit informovanější rozhodnutí a pomůže vývojářům vytvářet lepší uživatelské prostředí.

Následující diagram a tabulka poskytují informace o stavebních kamenech výzvy k souhlasu.

![Stavební kameny výzvy k souhlasu](./media/application-consent-experience/consent_prompt.png)

| # | Komponenta | Účel |
| ----- | ----- | ----- |
| 1 | Identifikátor uživatele | Tento identifikátor představuje uživatele, kterého klientská aplikace požaduje pro přístup k chráněným prostředkům jménem. |
| 2 | Nadpis | Název se změní na základě toho, zda uživatelé procházejí tokem souhlasu uživatele nebo správce. V toku souhlasu uživatele bude název "Požadovaná oprávnění", zatímco v toku souhlasu správce bude mít titul další řádek "Přijmout pro vaši organizaci". |
| 3 | Logo aplikace | Tento obrázek by měl pomoci uživatelům mít vizuální podnět, zda tato aplikace je aplikace, které mají v úmyslu získat přístup. Tento obrázek je poskytován vývojáři aplikací a vlastnictví této bitové kopie není ověřeno. |
| 4 | App name (Název aplikace) | Tato hodnota by měla informovat uživatele, která aplikace požaduje přístup ke svým datům. Všimněte si, že tento název je poskytován vývojáři a vlastnictví tohoto názvu aplikace není ověřeno. |
| 5 | Doména vydavatele | Tato hodnota by měla poskytnout uživatelům doménu, kterou mohou být schopni vyhodnotit z důvodu důvěryhodnosti. Tato doména je poskytována vývojáři a vlastnictví této domény vydavatele je ověřeno. |
| 6 | Oprávnění | Tento seznam obsahuje oprávnění požadovaná klientskou aplikací. Uživatelé by měli vždy vyhodnotit typy oprávnění, které jsou požadovány pochopit, jaká data klientská aplikace bude oprávněna k přístupu jejich jménem, pokud přijmou. Jako vývojář aplikace je nejlepší požádat o přístup, k oprávněním s nejmenšími oprávněními. |
| 7 | Popis oprávnění | Tato hodnota je poskytována službou, která vydává oprávnění. Chcete-li zobrazit popisy oprávnění, je nutné přepnout dvojitou šipku vedle oprávnění. |
| 8 | Podmínky aplikace | Tyto podmínky obsahují odkazy na smluvní podmínky a prohlášení o zásadách ochrany osobních údajů aplikace. Vydavatel je zodpovědný za nastínění svých pravidel v jejich podmínkách služby. Kromě toho je vydavatel odpovědný za zveřejnění způsobu, jakým používá a sdílí uživatelská data ve svém prohlášení o zásadách ochrany osobních údajů. Pokud vydavatel neposkytuje odkazy na tyto hodnoty pro víceklientské aplikace, bude na výzvu souhlasu upozornění tučným písmem. |
| 9 | https://myapps.microsoft.com | Toto je odkaz, kde mohou uživatelé zkontrolovat a odebrat všechny aplikace jiných výrobců, které mají aktuálně přístup ke svým datům. |

## <a name="common-consent-scenarios"></a>Scénáře společného souhlasu

Zde jsou prostředí souhlasu, které může uživatel zobrazit ve scénářích společného souhlasu:

1. Jednotlivci, kteří přistupují k aplikaci, která je přesměruje na tok souhlasu uživatele, a zároveň vyžadují sadu oprávnění, která spadá do jejich působnosti.
    
    1. Správci uvidí další ovládací prvek na výzvu tradiční souhlas, který jim umožní souhlas jménem celého klienta. Ovládací prvek bude výchozí vypnutí, takže pouze v případě, že správci explicitně zaškrtnout políčko bude souhlas udělen jménem celého klienta. Od dnešního dne se toto zaškrtávací políčko zobrazí jenom pro roli globálního správce, takže cloudový správce a správce aplikací toto políčko neuvidí.

        ![Výzva k souhlasu pro scénář 1a](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. Uživatelům se zobrazí výzva k tradičnímu souhlasu.

        ![Výzva k souhlasu pro scénář 1b](./media/application-consent-experience/consent_prompt_1b.png)

2. Jednotlivci přistupující k aplikaci, která vyžaduje alespoň jedno oprávnění, které je mimo jejich rozsah oprávnění.
    1. Správci uvidí stejnou výzvu jako 1.i je uvedeno výše.
    2. Uživatelům bude zablokováno udělení souhlasu s aplikací a bude jim řečeno, aby požádali svého správce o přístup k aplikaci. 
                
        ![Výzva k souhlasu pro scénář 1b](./media/application-consent-experience/consent_prompt_2b.png)

3. Jednotlivci, kteří navigují nebo jsou přesměrováni na tok souhlasu správce.
    1. Uživatelé správce uvidí výzvu k souhlasu správce. Název a popisy oprávnění se změnily na této výzvu, změny zdůrazňují skutečnost, že přijetí této výzvy udělí aplikaci přístup k požadovaným datům jménem celého klienta.
        
        ![Výzva k souhlasu pro scénář 1b](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. Uživatelé, kteří nejsou správci, uvidí stejnou obrazovku jako 2.ii zobrazenou výše.

## <a name="next-steps"></a>Další kroky
- Získejte podrobný přehled o tom, [jak architektura souhlasu Azure AD implementuje souhlas](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Pro větší hloubku se dozvíte, [jak víceklientská aplikace můžete použít rozhraní souhlasu](active-directory-devhowto-multi-tenant-overview.md) k implementaci souhlasu "uživatel" a "správce", podpora pokročilejší vícevrstvé aplikace vzory.
- Přečtěte [si, jak nakonfigurovat doménu vydavatele aplikace](howto-configure-publisher-domain.md).
