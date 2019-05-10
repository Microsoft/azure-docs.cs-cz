---
title: Použití Azure monitoru sešity sestav Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak používat Azure Monitor sešity pro sestavy Azure Active Directory.
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
ms.openlocfilehash: 6ae14ec152975717af5d55780bcc39aa87c4b01a
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406599"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Jak používat Azure Monitor sešity sestav Azure Active Directory

Opravdu chcete:

- Pochopení dopadu funkcí vaše [zásady podmíněného přístupu](../conditional-access/overview.md) na vaši uživatelé přihlašovací prostředí?

- Řešení potíží s neúspěšných přihlášení získáte lepší přehled o vaší organizaci přihlásit stavu a rychlé řešení problémů?

- Vědět, kdo je používá starší verze ověřování pro přihlášení k prostředí? (Podle [blokování starší verze ověřování](../conditional-access/block-legacy-authentication.md), můžete zlepšit ochranu svého tenanta.)

Pomůžou vám tyto otázky, poskytuje služba Active Directory sešity pro monitorování. [Azure Monitor sešity](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) sloučení textu, analytické dotazy, metriky a parametry do bohaté interaktivní sestavy. 

Tento článek:

- Seznamte se s postupy se předpokládá [vytvořit interaktivní sestavy s použitím monitorování sešity](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Vysvětluje, jak použít monitorování sešity k pochopení účinku zásad podmíněného přístupu, řešení potíží s neúspěšných přihlášení a k identifikaci starší verze ověřování.
 


## <a name="prerequisites"></a>Požadavky

K monitorování sešity použít, budete potřebovat:

- Tenanta služby Active Directory premium (plán P1 nebo P2) licenci. Zjistěte, jak [nejdřív získat licenci premium](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- A [pracovní prostor Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

## <a name="workbook-access"></a>Přístup k sešitu 

Pro přístup k sešity:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. V levém navigačním podokně vyberte **Azure Active Directory**.

3. V **monitorování** vyberte **Insights**. 

    ![Select Insights](./media/howto-use-azure-monitor-workbooks/41.png)

4. Vyberte šablonu a sestavy nebo na panelu nástrojů vyberte **otevřít**. 

    ![Vyberte Otevřít](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Analýza přihlášení

Pro přístup k sešit analýzy přihlášení v **využití** vyberte **přihlášení**. 

Tento sešit zobrazuje trendy následující přihlášení:

- Všechna přihlášení

- Úspěch

- Čeká se na akci uživatele.

- Chyba

Každý trendů můžete filtrovat podle těchto kategorií:

- Časové rozmezí

- Aplikace

- Uživatelé

![Analýza přihlášení](./media/howto-use-azure-monitor-workbooks/43.png)


Pro každý trend získat rozpis podle těchto kategorií:

- Location

    ![Přihlášení podle umístění](./media/howto-use-azure-monitor-workbooks/45.png)

- Zařízení

    ![Přihlášení podle zařízení](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Přihlášení pomocí starší verze ověřování 


Pro přístup k sešitu pro přihlášení, které používají [starší verze ověřování](../conditional-access/block-legacy-authentication.md)v **využití** vyberte **přihlášení pomocí starší verze ověřování**. 

Tento sešit zobrazuje trendy následující přihlášení:

- Všechna přihlášení

- Úspěch


Každý trendů můžete filtrovat podle těchto kategorií:

- Časové rozmezí

- Aplikace

- Uživatelé

- Protokoly

![Přihlášení pomocí starší verze ověřování](./media/howto-use-azure-monitor-workbooks/47.png)


Pro každý trend získat rozpis podle aplikace a protokolu.

![Starší verze ověřování přihlášení aplikace a protokol](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Přihlášení pomocí podmíněného přístupu 


Pro přístup k sešitu pro přihlášení podle [zásady podmíněného přístupu](../conditional-access/overview.md)v **podmíněného přístupu** vyberte **přihlášení pomocí podmíněného přístupu**. 

Tento sešit zobrazuje trendy pro zakázané přihlášení. Každý trendů můžete filtrovat podle těchto kategorií:

- Časové rozmezí

- Aplikace

- Uživatelé

![Přihlášení pomocí podmíněného přístupu](./media/howto-use-azure-monitor-workbooks/49.png)


Pro zakázané přihlášení získat rozpis podle stavu podmíněného přístupu.

![Stav podmíněného přístupu](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Přihlášení pomocí udělení ovládacích prvků

Pro přístup k sešitu pro přihlášení podle [udělení ovládacích prvků](../conditional-access/controls.md)v **podmíněného přístupu** vyberte **přihlášení podle udělení ovládacích prvků**. 

Tento sešit zobrazuje následující zakázáno přihlášení trendy:

- Vyžadování MFA
 
- Vyžadovat podmínky použití

- Vyžadovat zásady ochrany osobních údajů

- Další


Každý trendů můžete filtrovat podle těchto kategorií:

- Časové rozmezí

- Aplikace

- Uživatelé

![Přihlášení pomocí udělení ovládacích prvků](./media/howto-use-azure-monitor-workbooks/50.png)


Pro každý trend získat rozpis podle aplikace a protokolu.

![Rozpis poslední přihlášení](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analýza selhání přihlášení

Použití **analýza selhání přihlášení** sešit k řešení potíží s chybami následujícím kódem:

- Přihlášení
- Zásady podmíněného přístupu
- Starší verze ověřování 


Pro přístup k přihlášení pomocí podmíněný přístup k datům v **Poradce při potížích** vyberte **přihlášení pomocí starší verze ověřování**. 

Tento sešit zobrazuje trendy následující přihlášení:

- Všechna přihlášení

- Úspěch

- Čeká se na akci.

- Chyba


Každý trendů můžete filtrovat podle těchto kategorií:

- Časové rozmezí

- Aplikace

- Uživatelé

![Řešení potíží s přihlášením](./media/howto-use-azure-monitor-workbooks/52.png)


Při řešení potíží s přihlášením, Azure Monitor je rozpis podle těchto kategorií:

- Hlavní chyby

    ![Souhrnné informace o hlavní chyby](./media/howto-use-azure-monitor-workbooks/53.png)

- Čeká se na akci uživatele přihlášení

    ![Souhrnné informace o přihlášeních čeká na akci uživatele](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Další postup

[Vytváření interaktivních sestav pomocí monitorování sešity](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).