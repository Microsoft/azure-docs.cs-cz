---
title: Použití Azure monitoru sešity sestav Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak používat Azure Monitor sešity pro sestavy Azure Active Directory
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
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2c9b3d0ef110fea0629af345a71d0d7b7cce7313
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60287139"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Postup: Azure Monitor sešity použít u sestav Azure Active Directory

Opravdu chcete:

- Pochopení dopadu na vaše [zásady podmíněného přístupu](../conditional-access/overview.md) na přihlašování vašich uživatelů?

- Řešení potíží se selháním přihlášení získáte lepší přehled o stavu přihlášení, vaše organizace také rychlé řešení problémů?

- Vědět, kdo je používá starší verze ověřování se přihlásit k prostředí? Podle [blokování starší verze ověřování](../conditional-access/block-legacy-authentication.md), můžete zlepšit ochranu svého tenanta.


[Azure Monitor sešity](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) text Analytics dotazů, metriky Azure a parametry zkombinovat bohaté interaktivní sestavy. Azure Active Directory poskytuje sešity pro monitorování, které vám umožní najít odpovědi na otázky uvedené výše.

Tento článek:

- Předpokládá, že máte zkušenosti s postupy [vytvářet interaktivní sestavy s Azure Monitor sešity](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Vysvětluje, jak můžete pomocí Azure monitoru sešity o monitorování odpovědět na otázky výše.
 


## <a name="prerequisites"></a>Požadavky

Pokud chcete používat tuto funkci, potřebujete tyto položky:

- Tenantem Azure Active Directory premium (P1/P2) licenci. Zjistěte, jak [nejdřív získat licenci premium](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- A [pracovní prostor Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

## <a name="access-workbooks"></a>Sešity přístup 

Pro přístup k sešity:

1. Přihlaste se k vaší [webu Azure portal](https://portal.azure.com).

2. Na levém navigačním panelu klikněte na tlačítko **Azure Active Directory**.

3. V **monitorování** klikněte na tlačítko **Insights**. 

    ![Insights](./media/howto-use-azure-monitor-workbooks/41.png)

4. Klikněte na šablonu a sestavy, nebo klikněte na tlačítko **otevřít** na panelu nástrojů. 

    ![Galerie](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Analýza přihlášení

Sešit analýzy přihlásit, klikněte na tlačítko **přihlášení** v **využití** oddílu. 

Tento sešit zobrazuje trendy následující přihlášení:

- Všechna přihlášení

- Úspěch

- Čeká se na akci uživatele.

- Selhání

Můžete filtrovat každá trend podle:

- Časové rozmezí

- Aplikace

- Uživatelé

![Galerie](./media/howto-use-azure-monitor-workbooks/43.png)


Pro každý trend získat rozpis podle:

- Location

    ![Galerie](./media/howto-use-azure-monitor-workbooks/45.png)

- Zařízení

    ![Galerie](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Přihlášení pomocí starší verze ověřování 


K přístupu k přihlášení pomocí [starší verze ověřování](../conditional-access/block-legacy-authentication.md) sešitu, klikněte na tlačítko **přihlášení pomocí starší verze ověřování** v **využití** oddílu. 

Tento sešit zobrazuje trendy následující přihlášení:

- Všechna přihlášení

- Úspěch


Můžete filtrovat každá trend podle:

- Časové rozmezí

- Aplikace

- Uživatelé

- Protokoly 

![Galerie](./media/howto-use-azure-monitor-workbooks/47.png)


Pro každý trend získat rozpis podle aplikace a protokolu.

![Galerie](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Přihlášení pomocí podmíněného přístupu 


Pro přístup k přihlášení podle [zásady podmíněného přístupu](../conditional-access/overview.md) sešitu, klikněte na tlačítko **přihlášení pomocí podmíněného přístupu** v **podmíněného přístupu** oddílu. 

Tento sešit zobrazuje trend pro zakázané přihlášení.

Můžete filtrovat každá trend podle:

- Časové rozmezí

- Aplikace

- Uživatelé

![Galerie](./media/howto-use-azure-monitor-workbooks/49.png)


Pro zakázané přihlášení získat rozpis podle stavu podmíněného přístupu.

![Stav podmíněného přístupu](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Přihlášení pomocí udělení ovládacích prvků

Pro přístup k přihlášení podle [udělení ovládacích prvků](../conditional-access/controls.md) sešitu, klikněte na tlačítko **přihlášení podle udělení ovládacích prvků** v **podmíněného přístupu** oddílu. 

Tento sešit zobrazuje následující zakázáno přihlášení trendy:

- Vyžadování MFA
 
- Vyžadovat podmínky použití

- Vyžadovat zásady ochrany osobních údajů

- Ostatní


Můžete filtrovat každá trend podle:

- Časové rozmezí

- Aplikace

- Uživatelé

![Galerie](./media/howto-use-azure-monitor-workbooks/50.png)


Pro každý trend získat rozpis podle aplikace a protokolu.

![Galerie](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analýza selhání přihlášení

Použití **analýza selhání přihlášení** sešit, který chcete řešit potíže s chybami:

- Přihlášení
- Zásady podmíněného přístupu
- Starší verze ověřování. 


Přihlášení pomocí podmíněného přístupu k datům, klikněte na tlačítko **přihlášení pomocí starší verze ověřování** v **Poradce při potížích** oddílu. 

Tento sešit zobrazuje trendy následující přihlášení:

- Všechna přihlášení

- Úspěch

- Čeká se na akci.

- Selhání


Můžete filtrovat každá trend podle:

- Časové rozmezí

- Aplikace

- Uživatelé

![Galerie](./media/howto-use-azure-monitor-workbooks/52.png)


Řešení potíží s přihlášením, získat rozpis podle:

- Hlavní chyby

    ![Galerie](./media/howto-use-azure-monitor-workbooks/53.png)

- Čeká se na akci uživatele přihlášení

    ![Galerie](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Další postup

* [Vytváření interaktivních sestav se sešity Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)