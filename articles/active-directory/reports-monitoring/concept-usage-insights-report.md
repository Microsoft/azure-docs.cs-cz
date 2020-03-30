---
title: Přehled využití a přehledů | Dokumenty společnosti Microsoft
description: Úvod do sestavy využití a přehledů na portálu Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b3db86137207ae726c7befc393f62590fd1456d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008265"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Sestava využití a přehledů na portálu Azure Active Directory

Díky přehledu využití a přehledů můžete získat zobrazení přihlašovacích dat zaměřené na aplikace. Odpovědi na následující otázky naleznete:

*   Jaké jsou nejpoužívaná aplikace v mé organizaci?
*   Které aplikace mají nejvíce neúspěšných přihlášení? 
*   Jaké jsou chyby hlavního přihlášení pro každou aplikaci?

## <a name="prerequisites"></a>Požadavky 

Chcete-li získat přístup k datům z přehledu využití a přehledů, potřebujete:

* Klient Azure AD
* Licence Azure AD premium (P1/P2) pro zobrazení přihlašovacích dat
* Uživatel v roli globálního správce, správce zabezpečení, čtečky zabezpečení nebo čtečky sestav. Kromě toho může každý uživatel (uživatel( uživatel, který není správcem) přistupovat ke svým vlastním přihlášením. 

## <a name="access-the-usage-and-insights-report"></a>Přístup k přehledu využití a přehledů

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Vyberte správný adresář, pak vyberte **Azure Active Directory** a zvolte Podnikové **aplikace**.
3. V části **Aktivita** vyberte **Přehledy využití &** a otevřete sestavu. 

![Sestava přehledů a využití](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Použití sestavy

Sestava využití a přehledy zobrazuje seznam aplikací s jedním nebo více pokusy o přihlášení a umožňuje řadit podle počtu úspěšných přihlášení, neúspěšných přihlášení a úspěšnosti.

Kliknutím na načíst více v dolní části seznamu umožňuje zobrazit další aplikace na stránce. Můžete vybrat rozsah dat pro zobrazení všech aplikací, které byly použity v rozsahu.

Můžete také nastavit fokus na konkrétní aplikaci. Vyberte **aktivitu přihlášení k zobrazení, chcete-li** zobrazit aktivitu přihlášení v průběhu času pro aplikaci, stejně jako hlavní chyby.  

Když vyberete den v grafu využití aplikace, získáte podrobný seznam přihlašovacích aktivit pro aplikaci.  

![Sestava přehledů a využití](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Další kroky

* [Sestava přihlášení](concept-sign-ins.md)