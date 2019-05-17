---
title: Sestava využití a insights na portálu Azure Active Directory | Dokumentace Microsoftu
description: Úvod do sestavy využití a insights na portálu Azure Active Directory
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
ms.openlocfilehash: 3fe1e72d277bffd4bc9b38ac377e33b341967e17
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806353"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Sestava využití a insights na portálu Azure Active Directory

Sestava využití a insights umožňuje získat aplikace zaměřené zobrazení dat přihlašování. Můžete najít odpovědi na následující otázky:

*   Co jsou horní používané aplikace v mé organizaci?
*   Jaké aplikace mají největší neúspěšných přihlášení? 
*   Co jsou se hlavní chyby přihlášení pro každou aplikaci?

## <a name="prerequisites"></a>Požadavky 

Pro přístup k datům ze sestavy využití a přehledy, budete potřebovat:

* Klient služby Azure AD
* Licenci Azure AD premium (P1/P2) Chcete-li zobrazit data přihlášení
* Uživatel v globální správce, správce zabezpečení, čtenář zabezpečení nebo role čtenáře sestav. Kdokoli (bez oprávnění správce) navíc můžete použít vlastní přihlášení. 

## <a name="access-the-usage-and-insights-report"></a>Přístup k sestavě využití a přehledy

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Vyberte adresář, vpravo a pak vyberte **Azure Active Directory** a zvolte **podnikové aplikace**.
3. Z **aktivity** vyberte **dat o využití a insights** a otevřete tak sestavu. 

![Sestavy využití a přehledů](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Pomocí sestavy

Sestavy využití a insights zobrazí seznam aplikací s minimálně jeden znak na pokus o a umožňuje řazení podle počtu úspěšných přihlášeních, neúspěšných přihlášení a míra úspěšnosti.

Kliknutím na zatížení více v dolní části seznamu můžete zobrazit další aplikace na stránce. Můžete vybrat rozsah, chcete-li zobrazit všechny aplikace, které se používají v rámci rozsahu.

Můžete také nastavit fokus na konkrétní aplikaci. Vyberte **zobrazení aktivit přihlašování** zobrazíte přihlašovací aktivity v čase pro aplikace, stejně jako se hlavní chyby.  

Když vyberete jeden den v grafu využití aplikace, získejte podrobný seznam aktivit přihlašování pro aplikaci.  

![Sestavy využití a přehledů](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Další postup

* [Sestava přihlášení](concept-sign-ins.md)