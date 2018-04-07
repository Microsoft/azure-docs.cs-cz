---
title: Privileged Identity Management pro prostředky Azure - výstrahy zabezpečení | Microsoft Docs
description: Popisuje výstrahy zabezpečení PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 86c9a0f12b2598ffbd02810a11622b13b0363a1f
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---alerts"></a>Privileged Identity Management - role prostředků - výstrahy
PIM pro prostředky Azure generuje výstrahy, když je aktivita podezřelého nebo unsafe ve vašem prostředí. Když se spustí, se objeví na stránce výstrahy. 

![](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>Zkontrolujte výstrahy
Vyberte výstrahu, pokud chcete zobrazit sestavu obsahující seznam uživatelů nebo rolí, které spustí výstrahu a Rady, jak nápravu.
![](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>Výstrahy
| Výstrahy | Závažnost | Trigger | Doporučení |
| --- | --- | --- | --- |
| **Příliš mnoho vlastníky přiřazené k prostředku** |Střednědobé používání |Příliš mnoho uživatelů mít roli vlastníka. |Zkontrolujte uživatele v seznamu a znovu přiřadit některé méně privilegované role. |
| **Příliš mnoho trvalé vlastníky přiřazené k prostředku** |Střednědobé používání |Příliš mnoho uživatelů jsou trvale přiřazené k roli. |Zkontrolujte uživatele v seznamu a znovu přiřadit některé tak, aby vyžadovala aktivace pro roli použití. |
| **Duplicitní role vytvořen** |Střednědobé používání |Několik rolí obsahuje stejných kritérií. |Použijte pouze jednu z těchto rolí. |


### <a name="severity"></a>Závažnost
* **Vysoká**: vyžaduje okamžitý zásah kvůli porušení zásad. 
* **Střední**: nevyžaduje okamžitý zásah, ale signály potenciálních porušení zásad.
* **Nízká**: nevyžaduje okamžitý zásah, ale doporučuje změny preferrable zásad.

## <a name="configure-security-alert-settings"></a>Konfigurace nastavení výstrah zabezpečení
Na stránce výstrahy přejděte k nastavení.
![](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

Přizpůsobit nastavení na jinou výstrahy pro práci s prostředím a cíle zabezpečení.
![](media/azure-pim-resource-rbac/rbac-alert-settings.png)
