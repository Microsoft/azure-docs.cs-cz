---
title: Sešity Azure Monitoru pro sestavy | Dokumenty společnosti Microsoft
description: Přečtěte si, jak používat sešity Azure Monitoru pro sestavy Azure Active Directory.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2e94d9f56a865999f9169650f621a6af892c27ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014366"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Jak používat sešity Azure Monitoru pro sestavy Služby Azure Active Directory

> [!IMPORTANT]
> Chcete-li optimalizovat základní dotazy v tomto sešitu, klikněte na tlačítko "Upravit", klikněte na ikonu Nastavení a vyberte pracovní prostor, ve kterém chcete tyto dotazy spustit. Sešity ve výchozím nastavení vybere všechny pracovní prostory, ve kterých směrujete protokoly Azure AD. 

Chcete:

- Rozumíte vlivu [zásad podmíněného přístupu](../conditional-access/overview.md) na prostředí přihlašování uživatelů?

- Poradce při potížích s přihlášením, chcete-li získat lepší přehled o stavu přihlášení vaší organizace a rychle vyřešit problémy?

- Víte, kdo používá starší ověřování k přihlášení do vašeho prostředí? [(Blokováním staršíverze ověřování](../conditional-access/block-legacy-authentication.md)můžete zlepšit ochranu klienta.)

- Potřebujete pochopit dopad zásad podmíněného přístupu ve vašem tenantovi?

- Chcete možnost zkontrolovat: dotazy protokolu přihlášení, sešit hlásí, kolik uživatelů byl udělen nebo odepřen přístup, a kolik uživatelů obejít zásady podmíněného přístupu při přístupu k prostředkům?

- Máte zájem o hlubší pochopení: podrobnosti sešitu podle podmínky tak, aby dopad zásady lze kontextualizovat podle podmínky, včetně platformy zařízení, stavu zařízení, klientské aplikace, rizika přihlášení, umístění a aplikace?

- Získejte hlubší přehled o dotazech protokolu přihlášení, sešit hlásí, kolik uživatelů byl udělen nebo odepřen přístup, a také kolik uživatelů obejít zásady podmíněného přístupu při přístupu k prostředkům.

- Služba Active Directory poskytuje sešity pro monitorování, které vám pomohou tyto otázky řešit. [Sešity Azure Monitoru kombinují](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) text, analytické dotazy, metriky a parametry do bohatých interaktivních sestav.



Tento článek:

- Předpokládá, že jste obeznámeni s tím, jak [vytvářet interaktivní sestavy pomocí sešitů Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Vysvětluje, jak pomocí sešitů monitorování porozumět efektu zásad podmíněného přístupu, řešit chyby přihlášení a identifikovat starší ověřování.
 


## <a name="prerequisites"></a>Požadavky

Chcete-li používat sešity Monitor, potřebujete:

- Tenant služby Active Directory s prémiovou licencí (P1 nebo P2). Přečtěte si, jak [získat prémiovou licenci](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- [Pracovní prostor Analýzy protokolů](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

- [Přístup k](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) pracovnímu prostoru analýzy protokolů
- Následující role ve službě Azure Active Directory (pokud přistupujete k Log Analytics prostřednictvím portálu Azure Active Directory)
    - Správce zabezpečení
    - Čtenář zabezpečení
    - Čtečka sestav
    - Globální správce

## <a name="roles"></a>Role
Chcete-li spravovat sešity, musíte být v jedné z následujících rolí a mít přístup k podkladovému pracovnímu prostoru [Log Analytics:](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions)
-   Globální správce
-   Správce zabezpečení
-   Čtenář zabezpečení
-   Čtečka sestav
-   Správce aplikace

## <a name="workbook-access"></a>Přístup k sešitu 

Přístup k sešitům:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Přejděte do**sešitů****monitorování** >  **služby Azure Active Directory** > . 

1. Vyberte sestavu nebo šablonu nebo na panelu nástrojů vyberte **Otevřít**. 

![Vyhledání sešitů Azure Monitoru ve Službě Azure AD](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>Analýza přihlášení

Chcete-li získat přístup k sešitu analýzy přihlášení, vyberte v části **Využití** **přihlášení**. 

Tento sešit zobrazuje následující trendy přihlášení:

- Všechna přihlášení

- Úspěch

- Čekající akce uživatele

- Selhání

Každý trend můžete filtrovat podle následujících kategorií:

- Časové rozmezí

- Aplikace

- Uživatelé

![Analýza přihlášení](./media/howto-use-azure-monitor-workbooks/43.png)


U každého trendu získáte rozdělení podle následujících kategorií:

- Umístění

    ![Přihlášení podle umístění](./media/howto-use-azure-monitor-workbooks/45.png)

- Zařízení

    ![Přihlášení podle zařízení](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Přihlášení pomocí staršího ověřování 


Pokud chcete získat přístup k sešitu pro přihlášení, která používají [starší verze ověřování](../conditional-access/block-legacy-authentication.md), vyberte v části **Využití** přihlášení **pomocí staršího ověřování**. 

Tento sešit zobrazuje následující trendy přihlášení:

- Všechna přihlášení

- Úspěch


Každý trend můžete filtrovat podle následujících kategorií:

- Časové rozmezí

- Aplikace

- Uživatelé

- Protokoly

![Přihlášení podle staršíverze ověřování](./media/howto-use-azure-monitor-workbooks/47.png)


U každého trendu získáte rozdělení podle aplikace a protokolu.

![Přihlášení ke staršímu ověřování podle aplikace a protokolu](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Přihlášení podle podmíněného přístupu 


Chcete-li získat přístup k sešitu pro přihlášení pomocí [zásad podmíněného přístupu](../conditional-access/overview.md), vyberte v části **Podmíněný přístup** možnost Přihlášení **podle podmíněného přístupu**. 

Tento sešit zobrazuje trendy pro zakázané přihlášení. Každý trend můžete filtrovat podle následujících kategorií:

- Časové rozmezí

- Aplikace

- Uživatelé

![Přihlášení pomocí podmíněného přístupu](./media/howto-use-azure-monitor-workbooks/49.png)


U zakázaných přihlášení se zobrazuje rozdělení podle stavu podmíněného přístupu.

![Stav podmíněného přístupu](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>Přehled podmíněného přístupu

### <a name="overview"></a>Přehled

Sešity obsahují dotazy protokolu přihlášení, které mohou správcům IT pomoci sledovat dopad zásad podmíněného přístupu v jejich tenantovi. Máte možnost hlásit, kolik uživatelů by byl udělen nebo odepřen přístup. Sešit obsahuje přehled o tom, kolik uživatelů by obejít zásady podmíněného přístupu na základě atributů těchto uživatelů v době přihlášení. Obsahuje podrobnosti podle podmínky tak, aby dopad zásady lze kontextualizovat podle podmínky, včetně platformy zařízení, stavu zařízení, klientské aplikace, rizika přihlášení, umístění a aplikace.

### <a name="instructions"></a>Pokyny 
Chcete-li získat přístup k sešitu pro přehledy podmíněného přístupu, vyberte sešit **Přehledy podmíněného přístupu** v části Podmíněný přístup. Tento sešit zobrazuje očekávaný dopad jednotlivých zásad podmíněného přístupu ve vašem tenantovi. Vrozenou jednu nebo více zásad podmíněného přístupu z rozevíracího seznamu a zúžit rozsah sešitu pomocí následujících filtrů: 

- **Časový rozsah**

- **Uživatel**

- **Aplikace**

- **Zobrazení dat**

![Stav podmíněného přístupu](./media/howto-use-azure-monitor-workbooks/access-insights.png)


Souhrn dopadu zobrazuje počet uživatelů nebo přihlášení, pro které vybrané zásady měly konkrétní výsledek. Součet je počet uživatelů nebo přihlášení, pro které byly vybrané zásady vyhodnoceny ve vybraném časovém rozsahu. Kliknutím na dlaždici můžete filtrovat data v sešitu podle tohoto typu výsledku. 

![Stav podmíněného přístupu](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

Tento sešit také zobrazuje dopad vybraných zásad rozdělených podle každé ze šesti podmínek: 
- **Stav zařízení**
- **Platforma zařízení**
- **Klientské aplikace**
- **Riziko přihlášení**
- **Umístění**
- **Aplikace**

![Stav podmíněného přístupu](./media/howto-use-azure-monitor-workbooks/device-platform.png)

Můžete také prozkoumat jednotlivá přihlášení filtrovaná podle parametrů vybraných v sešitu. Vyhledejte jednotlivé uživatele seřazené podle četnosti přihlášení a zobrazte jejich odpovídající události přihlášení. 

![Stav podmíněného přístupu](./media/howto-use-azure-monitor-workbooks/filtered.png)





## <a name="sign-ins-by-grant-controls"></a>Přihlášení podle ovládacích prvků grantu

Chcete-li získat přístup k sešitu pro přihlášení podle [ovládacích prvků udělení](../conditional-access/controls.md), vyberte v části **Podmíněný přístup** možnost Přihlášení **podle ovládacích prvků grantu**. 

Tento sešit zobrazuje následující zakázané trendy přihlášení:

- Vyžadování MFA
 
- Vyžadování podmínek použití

- Vyžadovat prohlášení o zásadách ochrany osobních údajů

- Ostatní


Každý trend můžete filtrovat podle následujících kategorií:

- Časové rozmezí

- Aplikace

- Uživatelé

![Přihlášení podle ovládacích prvků grantu](./media/howto-use-azure-monitor-workbooks/50.png)


U každého trendu získáte rozdělení podle aplikace a protokolu.

![Rozdělení nedávných přihlášení](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analýza selhání přihlášení

Pomocí sešitu **analýzy selhání přihlášení** můžete řešit chyby pomocí:

- Přihlášení
- Zásady podmíněného přístupu
- Starší verze ověřování 


Chcete-li získat přístup k přihlašovacím údajům podle dat podmíněného přístupu, vyberte v části **Poradce při potížích** **přihlášení pomocí staršího ověřování**. 

Tento sešit zobrazuje následující trendy přihlášení:

- Všechna přihlášení

- Úspěch

- Nevyřízená akce

- Selhání


Každý trend můžete filtrovat podle následujících kategorií:

- Časové rozmezí

- Aplikace

- Uživatelé

![Poradce při potížích s přihlášením](./media/howto-use-azure-monitor-workbooks/52.png)


Azure Monitor vám pomůže při řešení potíží s přihlášením a rozpisem podle následujících kategorií:

- Nejlepší chyby

    ![Souhrn hlavních chyb](./media/howto-use-azure-monitor-workbooks/53.png)

- Přihlášení čekající na akci uživatele

    ![Souhrn přihlášení čekajících na akci uživatele](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Další kroky

[Vytvářejte interaktivní sestavy pomocí sešitů Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).
