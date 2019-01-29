---
title: Principy souhlasu s aplikací Azure AD narazí | Dokumentace Microsoftu
description: Další že informace o službě Azure AD udělit souhlas možnosti, jak zobrazit, jak můžete použít při správě a vývoji aplikací v Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2018
ms.author: celested
ms.reviewer: zawad
ms.openlocfilehash: f5ec31cf0f88b9bfdff2e4fa877d56a2689054ae
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55097936"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Principy souhlasu s aplikací Azure AD narazí

Přečtěte si že další informace o aplikaci Azure Active Directory (Azure AD) souhlas činnost koncového uživatele. Proto můžete inteligentně spravovat aplikace pro vaši organizaci nebo vyvíjet aplikace s více bezproblémové prostředí pro vyjádření souhlasu.

## <a name="consent-and-permissions"></a>Oprávnění a souhlas

Souhlas se proces udělení povolení k aplikaci přístup k chráněným prostředkům jejich jménem uživatele. Správce nebo uživatel může vyzváni k zadání souhlasu pro povolení přístupu k datům jejich organizace nebo jednotlivce.

Udělení souhlasu skutečné uživatelské prostředí se budou lišit v závislosti na zásadách nastavených na tenanta uživatele, oboru uživatele úřadu (nebo rolí) a typu [oprávnění](https://docs.microsoft.com/azure/active-directory/develop/active-directory-permissions) žádá klientské aplikace. To znamená, že aplikace vývojáři a správci tenanta mají některé kontrolu nad prostředí pro vyjádření souhlasu. Správci mají možnost nastavení a vypnutím zásad na klienta nebo aplikace pro řízení prostředí pro vyjádření souhlasu v rámci jejich tenanta. Vývojáři aplikací můžete určovat, jaké typy oprávnění jsou požadovány, a pokud chtějí provedou uživatele uživatel vyjádřit souhlas toku nebo tok vyjádření souhlasu správce.

- **Tok vyjádření souhlasu uživatele** je, když vývojář aplikace přesměruje uživatele na koncový bod autorizace se záměrem k záznamu souhlas pro aktuálního uživatele.
- **Tok vyjádření souhlasu správce** je, když vývojář aplikace přesměruje uživatelé do správce souhlasit koncový bod s úmyslem záznamu souhlas pro celého tenanta. Abyste se ujistili, tok vyjádření souhlasu správce funguje správně, musí vývojáři aplikací vypsat všechna oprávnění v `RequiredResourceAccess` vlastnost v manifestu aplikace. Další informace najdete v tématu [manifest aplikace](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest).

## <a name="building-blocks-of-the-consent-prompt"></a>Stavební bloky výzva k povolení spuštění

Výzva k povolení spuštění je navržený tak, aby uživatelé mít dostatek informací k určení, pokud důvěřují klientské aplikaci přístup k chráněným prostředkům jejich jménem. Principy stavební bloky pomohou uživatelům udělení souhlasu zkontrolujte více informovány rozhodnutí a pomohou vývojářům vytvářet lepší uživatelské prostředí.

Následující diagram a tabulka poskytují informace o stavebních bloků výzva k povolení spuštění.

![Stavební bloky výzva k povolení spuštění](./media/application-consent-experience/consent_prompt.png)

| # | Komponenta | Účel |
| ----- | ----- | ----- |
| 1 | Identifikátor uživatele | Tento identifikátor představuje uživatele, který žádá o přístup k chráněným prostředkům jménem klienta aplikace. |
| 2 | Název | Změny názvů založené na tom, jestli uživatelé projít tok vyjádření souhlasu uživatele nebo správce. V toku souhlasu uživatele název bude "Požadováno oprávnění" při v toku souhlasu správce názvu bude mít další řádek "Accept pro vaši organizaci". |
| 3 | Logo aplikace | Tato image by měly pomoci uživatelům mají vizuální upozornění, jestli tato aplikace je aplikace určené pro přístup k. Tento obrázek pochází od vývojáře aplikací a vlastnictví tohoto obrázku není ověřený. |
| 4 | App name (Název aplikace) | Tato hodnota by měla informovat uživatele, který aplikace žádá o přístup ke svým datům. Poznámka: Tento název je k dispozici vývojáři a vlastnictví tento název aplikace není ověřený. |
| 5 | Publisher domain | Tato hodnota by měla poskytnout uživatelům s doménou, které mohou být možné vyhodnotit důvěryhodnost. Tato doména je poskytována vývojáři a ověřit vlastnictví domény vydavatele. |
| 6 | Oprávnění | Tento seznam obsahuje klientská aplikace požaduje oprávnění. Uživatelé by měla vždy posoudíte typy vašich oprávnění žádá chcete pochopit, jaká data aplikace klienta se oprávnění pro přístup k jejich jménem, pokud je přijmou. Jako vývojář aplikací je nejvhodnější pro vyžádání přístupu k oprávnění s nejnižšími oprávněními. |
| 7 | Popis oprávnění | Tato hodnota je poskytována služba zveřejňující oprávnění. Chcete-li zobrazit popis oprávnění, musí přepnout na dvojitou šipku vedle oprávnění. |
| 8 | Podmínky pro aplikaci | Tyto podmínky obsahují odkazy na podmínky služby a o ochraně osobních údajů příkazu aplikace. Vydavatel je zodpovědná za sbalování již nemají svá pravidla v podmínkách služby. Kromě toho je zodpovědná za zveřejnění způsobu použití a sdílení dat uživatele ve své prohlášení o ochraně osobních údajů vydavatele. Pokud vydavatel neobsahuje odkazy na tyto hodnoty pro víceklientské aplikace, bude uveden tučně upozornění na příkazovém řádku souhlas. |
| 9 | https://myapps.microsoft.com | Toto je odkaz, kde můžou uživatelé zkontrolujte a odeberte všechny aplikací od jiných výrobců, které aktuálně máte přístup ke svým datům. |

## <a name="common-consent-scenarios"></a>Běžné scénáře souhlas

Tady jsou souhlasu prostředí, které může uživatel zobrazit v běžných scénářích souhlasu:

1. Jednotlivce přístup k aplikacím, který je nasměruje uživateli souhlas tok při vyžadování sadu oprávnění, která je v rámci jejich rozsah oprávnění.
    
    1. Správci zobrazí další ovládací prvek na příkazovém řádku tradiční souhlasu, která jim umožní souhlas jménem celého tenanta. Ovládací prvek bude změněna na výchozí vypnutý, takže jen když správci explicitně zkontrolovala, že se pole souhlas udělit jménem celého tenanta. Od dnešního dne toto zaškrtávací políčko zobrazí pouze pro roli globálního správce, aby správce cloudu a správu aplikací nezobrazí toto zaškrtávací políčko.

        ![Výzva k povolení spuštění pro scénář 1a](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. Uživatelům se zobrazí výzva tradiční souhlas.

        ![Výzva k povolení spuštění pro scénář 1b](./media/application-consent-experience/consent_prompt_1b.png)

2. Přístup k aplikaci, která vyžaduje aspoň jedno oprávnění, která je mimo jejich rozsah oprávnění jednotlivce.
    1. Správci se zobrazí stejném řádku jako 1.i uvedené výše.
    2. Uživatelé budou mít zablokovaný udělení souhlasu pro aplikaci a bude jim požádat svého správce pro přístup k aplikaci. 
                
        ![Výzva k povolení spuštění pro scénář 1b](./media/application-consent-experience/consent_prompt_2b.png)

3. Jednotlivci, kteří přejděte nebo jsou směrované na správce souhlasit toku.
    1. Správcům se zobrazí výzva k povolení spuštění správce. Název a popis oprávnění změnit na tuto výzvu, zvýraznění změny skutečnost, že přijetím této výzvy, udělí se aplikace přístup k požadovaným datům jménem celého tenanta.
        
        ![Výzva k povolení spuštění pro scénář 1b](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. Uživatelé bez oprávnění správce se zobrazí na stejné obrazovce jako 2. ii uvedené výše.

## <a name="next-steps"></a>Další postup
- Získejte podrobný přehled o [způsob implementace rozhraní Azure AD pro udělování souhlasu souhlasu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework).
- Větší hloubky, přečtěte si [použití rozhraní pro udělování souhlasu víceklientské aplikaci](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) k implementaci "user" a "admin" souhlas, podpora více pokročilé modely vícevrstvých aplikací.
