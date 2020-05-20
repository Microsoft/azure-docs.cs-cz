---
title: Přehledy podmíněného přístupu a sešity vytváření sestav – Azure Active Directory
description: Řešení potíží se zásadami pomocí Azure AD podmíněného přístupu a sešitu vytváření sestav
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c4feeca1cbe7eb88aace811829e4d9c2db5f38e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83641592"
---
# <a name="conditional-access-insights-and-reporting"></a>Přehledy podmíněného přístupu a vytváření sestav

Přehled podmíněného přístupu a sešit vytváření sestav vám umožní pochopit dopad zásad podmíněného přístupu ve vaší organizaci v průběhu času. Během přihlašování se můžou vztahovat jedna nebo víc zásad podmíněného přístupu a udělit přístup v případě, že určité ovládací prvky grantu splní nebo odepře přístup jinak. Vzhledem k tomu, že při každém přihlašování může být vyhodnoceno více zásad podmíněného přístupu, umožňuje sešit přehledů a vytváření sestav zkoumat dopad jednotlivých zásad nebo podmnožiny všech zásad.  

## <a name="prerequisites"></a>Požadavky

Pokud chcete povolit sešit Insights a vytváření sestav, musí mít váš tenant Log Analytics pracovní prostor pro uchovávání dat protokolů přihlášení. Aby mohli uživatelé používat podmíněný přístup, musí mít Azure AD Premium licence P1 nebo P2.

K přehledům a sestavám můžete získat přístup následujícími rolemi:  

- Správce podmíněného přístupu 
- Čtenář zabezpečení 
- Správce zabezpečení 
- Globální čtenář 
- Globální správce 

Uživatelé také potřebují jednu z následujících Log Analytics rolí pracovního prostoru:  

- Přispěvatel  
- Vlastník 

### <a name="stream-sign-in-logs-from-azure-ad-to-azure-monitor-logs"></a>Streamování protokolů přihlášení z Azure AD do protokolů Azure Monitor 

Pokud jste protokoly služby Azure AD neintegroval s protokoly Azure Monitor, bude nutné provést následující kroky předtím, než se sešit načte:  

1. [Vytvoří Log Analytics pracovní prostor v Azure monitor](../../azure-monitor/learn/quick-create-workspace.md).
1. [Integrujte protokoly služby Azure AD s protokoly Azure monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

## <a name="how-it-works"></a>Jak to funguje 

Přístup k sešitu Insights a vytváření sestav:  

1. Přihlaste se k **portálu Azure Portal**.
1. Přejděte na **Azure Active Directory**  >  **zabezpečení**  >  a přehled**podmíněného přístupu**  >  **a vytváření sestav**.

### <a name="get-started-select-parameters"></a>Začínáme: Výběr parametrů 

Řídicí panel přehledů a vytváření sestav vám umožní zobrazit vliv jedné nebo více zásad podmíněného přístupu v zadaném období. Začněte nastavením jednotlivých parametrů v horní části sešitu. 

![Přehledy podmíněného přístupu a řídicí panel vytváření sestav v Azure Portal](./media/howto-conditional-access-insights-reporting/conditional-access-insights-and-reporting-dashboard.png)

**Zásada podmíněného přístupu**: vyberte jednu nebo víc zásad podmíněného přístupu, abyste viděli jejich kombinovaný dopad. Zásady jsou rozdělené do dvou skupin: povoleno a zásady pouze pro sestavy. Ve výchozím nastavení jsou vybrány všechny povolené zásady. Tyto povolené zásady jsou aktuálně vydané zásady ve vašem tenantovi.  

**Časový rozsah**: vyberte časový rozsah od 4 hodiny po dobu až 90 dní. Pokud vyberete časový rozsah více než při integraci protokolů služby Azure AD s Azure Monitor, zobrazí se po dokončení integrace pouze přihlášení.  

**Uživatel**: ve výchozím nastavení se na řídicím panelu zobrazuje dopad vybraných zásad pro všechny uživatele. Chcete-li filtrovat podle jednotlivého uživatele, zadejte jméno uživatele do textového pole. Chcete-li filtrovat všechny uživatele, zadejte do textového pole text "Všichni uživatelé" nebo ponechte parametr prázdný. 

**Aplikace**: ve výchozím nastavení se na řídicím panelu zobrazuje dopad vybraných zásad pro všechny aplikace. Pokud chcete filtrovat podle individuální aplikace, zadejte název aplikace do textového pole. Pokud chcete filtrovat podle všech aplikací, zadejte do textového pole všechny aplikace nebo ponechte parametr prázdný. 

**Zobrazení dat**: vyberte, jestli chcete, aby se na řídicím panelu zobrazovaly výsledky z hlediska počtu uživatelů nebo počtu přihlášení. Jednotliví uživatelé můžou mít stovky přihlášení k mnoha aplikacím s velkým množstvím různých výsledků během daného časového období. Pokud vyberete zobrazení dat pro uživatele, může být uživatel zahrnut do počtu úspěchů i při selhání (například pokud je 10 uživatelů. 8 z nich by mohlo vést k úspěchu v posledních 30 dnech a 9 z nich by mohlo vést k chybě v posledních 30 dnech).

## <a name="impact-summary"></a>Souhrn dopadů 

Po nastavení parametrů se souhrn dopadu načte. V souhrnu se zobrazuje počet uživatelů nebo přihlášení během časového rozsahu, který je výsledkem "úspěch", "Chyba", "je vyžadována akce uživatele" nebo "nepoužit" při vyhodnocování vybraných zásad.  

![Souhrn dopadu v sešitu podmíněného přístupu](./media/howto-conditional-access-insights-reporting/workbook-impact-summary.png)

**Total (celkem**): počet uživatelů nebo přihlášení během časového období, kdy se vyhodnotila aspoň jedna z vybraných zásad.

**Úspěch**: počet uživatelů nebo přihlášení během období, kdy se kombinovaný výsledek vybraných zásad stal "úspěchem" nebo "pouze sestava: úspěch".

**Selhání**: počet uživatelů nebo přihlášení během časového období, kdy byl výsledek nejméně jedné z vybraných zásad "Chyba" nebo "pouze sestava: Chyba".

**Požaduje se akce uživatele**: počet uživatelů nebo přihlášení během časového období, ve kterém byl kombinovaný výsledek vybraných zásad "pouze sestava: je vyžadována akce uživatele". Akce uživatele se vyžaduje v případě, že ovládací prvek interaktivního udělení, jako je vícefaktorové ověřování, je vyžadován zásadami podmíněného přístupu pouze pro sestavu. Vzhledem k tomu, že ovládací prvky interaktivního udělení nejsou vynutily zásady pouze pro sestavy, nelze určit úspěch nebo neúspěch.  

**Nepoužito**: počet uživatelů nebo přihlášení během časového období, kdy se nepoužívá žádná z vybraných zásad.

### <a name="understanding-the-impact"></a>Porozumění dopadu 

![Rozpis sešitu na podmínku a stav](./media/howto-conditional-access-insights-reporting/workbook-breakdown-condition-and-status.png)

Zobrazení rozpisu uživatelů nebo přihlášení ke každé z těchto podmínek. Přihlášení určitého výsledku (například úspěch nebo neúspěch) můžete filtrovat tak, že vyberete souhrnné dlaždice v horní části sešitu. Můžete si prohlédnout rozpis přihlášení pro každou podmínku podmíněného přístupu: stav zařízení, platforma zařízení, klientská aplikace, umístění, aplikace a riziko přihlášení.  

## <a name="sign-in-details"></a>Podrobnosti o přihlášení 

![Přihlašovací údaje sešitu](./media/howto-conditional-access-insights-reporting/workbook-sign-in-details.png)

Přihlášení konkrétního uživatele můžete prozkoumat také tak, že v dolní části řídicího panelu vyhledáte přihlášení. Dotaz na levé straně zobrazuje nejčastěji uživatele. Když se uživatel vybere, bude dotaz filtrovat vpravo.  

## <a name="troubleshooting"></a>Řešení potíží

### <a name="why-is-the-workbook-taking-a-long-time-to-load"></a>Proč je sešit trvá dlouhou dobu, než se načte?  

V závislosti na vybraném časovém rozsahu a velikosti vašeho tenanta může sešit vyhodnotit neobyčejně velký počet přihlašovacích událostí. U rozsáhlých tenantů může objem přihlášení překročit kapacitu dotazu Log Analytics. Zkuste zkrátit časový rozsah na 4 hodiny, aby se zobrazilo, jestli se sešit načítá.  

### <a name="after-loading-for-a-few-minutes-why-is-the-workbook-returning-zero-results"></a>Proč se po načtení několik minut vrátí sešit s nulovými výsledky? 

Pokud svazek přihlášení překračuje kapacitu dotazu Log Analytics, vrátí sešit nula výsledků. Zkuste zkrátit časový rozsah na 4 hodiny, aby se zobrazilo, jestli se sešit načítá.  

### <a name="can-i-save-my-parameter-selections"></a>Můžu uložit výběry parametrů?  

Výběry parametrů můžete uložit v horní části sešitu tak, že na **Azure Active Directory**  >  **sešity**vydáte  >  **přehledy podmíněného přístupu a vytváření sestav**. Tady najdete šablonu sešitu, kde můžete sešit upravovat a ukládat do svého pracovního prostoru kopii, včetně výběrů parametrů, v **mých sestavách** nebo ve **sdílených sestavách**. 

### <a name="can-i-edit-and-customize-the-workbook-with-additional-queries"></a>Můžu sešit upravovat a přizpůsobovat dalším dotazům? 

Sešit můžete upravit a přizpůsobit tak, že kliknete na **Azure Active Directory**  >  **sešity**  >  **podmíněného přístupu a vytváření sestav**. Tady najdete šablonu sešitu, kde můžete sešit upravovat a ukládat do svého pracovního prostoru kopii, včetně výběrů parametrů, v **mých sestavách** nebo ve **sdílených sestavách**. Pokud chcete začít upravovat dotazy, klikněte v horní části sešitu na **Upravit** .  
 
## <a name="next-steps"></a>Další kroky

[Režim pouze sestavy podmíněného přístupu](concept-conditional-access-report-only.md)
