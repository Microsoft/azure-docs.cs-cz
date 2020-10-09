---
title: Sestava využití a přehledů | Microsoft Docs
description: Seznámení se sestavou využití a přehledů na portálu Azure Active Directory
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74008265"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Sestava využití a přehledů na portálu Azure Active Directory

Pomocí sestavy využití a přehled můžete získat pohled na přihlašovací údaje zaměřené na aplikaci. Můžete najít odpovědi na následující otázky:

*   Jaké jsou nejčastěji používané aplikace v mé organizaci?
*   U kterých aplikací se neúspěšná přihlášení? 
*   Jaké jsou nejčastější chyby při přihlašování u každé aplikace?

## <a name="prerequisites"></a>Požadavky 

Pro přístup k datům ze sestavy využití a přehled potřebujete:

* Tenant Azure AD
* Licence Azure AD Premium (P1/P2) k zobrazení přihlašovacích údajů
* Uživatel v rolích Globální správce, správce zabezpečení, čtenář zabezpečení nebo čtenář sestav. Kromě toho může mít každý uživatel (bez oprávnění správce) přístup ke svým vlastním přihlášením. 

## <a name="access-the-usage-and-insights-report"></a>Přístup k sestavě využití a přehled

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Vyberte správný adresář a pak vyberte **Azure Active Directory** a zvolte **podnikové aplikace**.
3. V části **aktivita** vyberte **využití & přehledy** k otevření sestavy. 

![Sestava přehledů a využití](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Použití sestavy

Sestava využití a přehledů zobrazuje seznam aplikací s jedním nebo více pokusy o přihlášení a umožňuje řadit podle počtu úspěšných přihlášení, neúspěšných přihlášení a míry úspěšnosti.

Kliknutím na načíst další v dolní části seznamu můžete zobrazit další aplikace na stránce. Můžete vybrat rozsah kalendářních dat a zobrazit všechny aplikace, které byly v daném rozsahu použity.

Můžete také nastavit fokus na konkrétní aplikaci. Vyberte **Zobrazit aktivitu přihlašování** , abyste viděli aktivitu přihlášení v průběhu času pro aplikaci i hlavní chyby.  

Když vyberete den v grafu využití aplikací, zobrazí se podrobný seznam aktivit přihlašování pro aplikaci.  

![Sestava přehledů a využití](./media/concept-usage-insights-report/usage-and-insights-report.png)

## <a name="next-steps"></a>Další kroky

* [Sestava přihlášení](concept-sign-ins.md)