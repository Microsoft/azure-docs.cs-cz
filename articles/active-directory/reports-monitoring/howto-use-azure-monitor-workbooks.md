---
title: Azure Monitor sešitů pro sestavy | Microsoft Docs
description: Naučte se používat Azure Monitor sešity pro sestavy Azure Active Directory.
services: active-directory
author: cawrites
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.openlocfilehash: 5e498dcb39f62fc870bc7efa989b91caeac0dedc
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819716"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Použití Azure Monitorch sešitů pro sestavy Azure Active Directory

Chcete:

- Seznámení se s vlivem [zásad podmíněného přístupu](../conditional-access/overview.md) na přihlašovací prostředí vašich uživatelů?

- Řešení potíží s přihlašováním kvůli lepšímu zobrazení stavu přihlášení vaší organizace a rychlé řešení problémů?

- Víte, kdo pro přihlášení k prostředí používá starší verze ověřování? ( [Blokováním staršího ověřování](../conditional-access/block-legacy-authentication.md)můžete zlepšit ochranu vašeho klienta.)

Aby bylo možné vyřešit tyto otázky, služba Active Directory poskytuje sešity pro monitorování. [Azure monitor sešity](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) kombinují text, analytické dotazy, metriky a parametry do propracovaných interaktivních sestav. 

Tento článek:

- Předpokládá, že máte zkušenosti s [vytvářením interaktivních sestav pomocí monitorování sešitů](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Vysvětluje, jak pomocí monitorovat sešity pochopit účinek zásad podmíněného přístupu, vyřešit problémy s přihlášením a identifikovat starší verze ověřování.
 


## <a name="prerequisites"></a>Předpoklady

Pokud chcete používat monitorované sešity, potřebujete:

- Tenant služby Active Directory s licencí Premium (P1 nebo P2). Přečtěte si, jak [získat licenci](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)na úrovni Premium.

- [Log Analytics pracovní prostor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

- [Přístup](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) k pracovnímu prostoru Log Analytics
- Následující role v Azure Active Directory (Pokud přistupujete Log Analytics prostřednictvím Azure Active Directoryového portálu)
    - Správce zabezpečení
    - Čtecí modul zabezpečení
    - Čtečka sestav
    - Globální správce

## <a name="roles"></a>Role
Musíte být v jedné z následujících rolí a mít [přístup k podkladovým Log Analytics](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) pracovním prostorům pro správu sešitů:
-   Globální správce
-   Správce zabezpečení
-   Čtecí modul zabezpečení
-   Čtečka sestav
-   Správce aplikace


## <a name="workbook-access"></a>Přístup k sešitu 

Přístup k sešitům:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

2. V levém navigačním podokně vyberte **Azure Active Directory**.

3. V části **monitorování** vyberte **sešity**. 

    ![Vybrat přehledy](./media/howto-use-azure-monitor-workbooks/41.png)

4. Vyberte sestavu nebo šablonu nebo na panelu nástrojů vyberte **otevřít**. 

    ![Vyberte otevřít.](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Analýza přihlášení

Chcete-li získat přístup k sešitu analýzy přihlašování, vyberte v části **využití** možnost **přihlášení**. 

Tento sešit obsahuje následující trendy při přihlašování:

- Všechna přihlášení

- Úspěch

- Nedokončená akce uživatele

- Selhání

Jednotlivé trendy můžete filtrovat podle následujících kategorií:

- Časové rozmezí

- Aplikace

- Uživatelé

![Analýza přihlášení](./media/howto-use-azure-monitor-workbooks/43.png)


Pro každý trend získáte rozpis podle následujících kategorií:

- Umístění

    ![Přihlášení podle umístění](./media/howto-use-azure-monitor-workbooks/45.png)

- Zařízení

    ![Přihlášení podle zařízení](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Přihlášení pomocí starší verze ověřování 


Pokud chcete získat přístup k sešitu pro přihlášení, která používají [starší verze ověřování](../conditional-access/block-legacy-authentication.md), v části **využití** vyberte **přihlášení pomocí starší verze ověřování**. 

Tento sešit obsahuje následující trendy při přihlašování:

- Všechna přihlášení

- Úspěch


Jednotlivé trendy můžete filtrovat podle následujících kategorií:

- Časové rozmezí

- Aplikace

- Uživatelé

- Protokoly

![Přihlášení pomocí starší verze ověřování](./media/howto-use-azure-monitor-workbooks/47.png)


Pro každý trend získáte rozpis podle aplikace a protokolu.

![Starší verze ověřování – přihlášení podle aplikace a protokolu](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Přihlášení podle podmíněného přístupu 


Chcete-li získat přístup k sešitu pro přihlášení podle [zásad podmíněného](../conditional-access/overview.md)přístupu, vyberte v části **podmíněný přístup** možnost **přihlášení podle podmíněného přístupu**. 

Tento sešit zobrazuje trendy pro zakázané přihlášení. Jednotlivé trendy můžete filtrovat podle následujících kategorií:

- Časové rozmezí

- Aplikace

- Uživatelé

![Přihlášení pomocí podmíněného přístupu](./media/howto-use-azure-monitor-workbooks/49.png)


Pro zakázaná přihlášení získáte rozpis stavu podmíněného přístupu.

![Stav podmíněného přístupu](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Přihlášení pomocí ovládacích prvků grant

Chcete-li získat přístup k sešitu pro přihlášení prostřednictvím [udělení ovládacích prvků](../conditional-access/controls.md), v části **podmíněný přístup** vyberte možnost **přihlášení pomocí udělení ovládacích prvků**. 

Tento sešit obsahuje následující zakázané trendy přihlašování:

- Vyžadování MFA
 
- Vyžadování podmínek použití

- Vyžadovat prohlášení o zásadách ochrany osobních údajů

- Jiné


Jednotlivé trendy můžete filtrovat podle následujících kategorií:

- Časové rozmezí

- Aplikace

- Uživatelé

![Přihlášení pomocí ovládacích prvků grant](./media/howto-use-azure-monitor-workbooks/50.png)


Pro každý trend získáte rozpis podle aplikace a protokolu.

![Rozpis nedávných přihlášení](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analýza selhání přihlášení

Pomocí sešitu **analýzy selhání přihlášení** můžete řešit chyby s následujícím:

- Přihlášení
- Zásady podmíněného přístupu
- Starší verze ověřování 


Chcete-li získat přístup k přihlášení podle dat podmíněného přístupu, vyberte v části **Poradce při potížích** možnost **přihlášení pomocí starší verze ověřování**. 

Tento sešit obsahuje následující trendy při přihlašování:

- Všechna přihlášení

- Úspěch

- Akce čeká na vyřízení

- Selhání


Jednotlivé trendy můžete filtrovat podle následujících kategorií:

- Časové rozmezí

- Aplikace

- Uživatelé

![Řešení potíží s přihlášením](./media/howto-use-azure-monitor-workbooks/52.png)


Při řešení potíží s přihlášením vám Azure Monitor poskytuje rozpis podle následujících kategorií:

- Hlavní chyby

    ![Shrnutí hlavních chyb](./media/howto-use-azure-monitor-workbooks/53.png)

- Přihlášení čekající na akci uživatele

    ![Shrnutí přihlášení čekajících na akci uživatele](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Další kroky

[Vytváření interaktivních sestav pomocí sledování sešitů](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).
