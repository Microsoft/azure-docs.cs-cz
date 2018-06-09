---
title: Spravovat výstrahy zabezpečení u prostředků Azure pomocí Privileged Identity managementu | Microsoft Docs
description: Popisuje výstrahy zabezpečení PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 5d65201c8940e63e3ba514ae0a2dc8a247d7b859
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233508"
---
# <a name="manage-security-alerts-for-azure-resources-by-using-privileged-identity-management"></a>Spravovat výstrahy zabezpečení u prostředků Azure pomocí Privileged Identity Management
Privileged Identity Management (PIM) pro prostředky Azure generuje výstrahy, když je aktivita podezřelého nebo unsafe ve vašem prostředí. Když se spustí, se objeví na stránce výstrahy. 

![stránka výstrah](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>Zkontrolujte výstrahy
Vyberte výstrahu, pokud chcete zobrazit sestavu obsahující seznam uživatelů nebo rolí, které spustí výstrahu, společně s nápravy Rady.

![Sestava výstrahy](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>Výstrahy
| Výstrahy | Severity | Trigger | Doporučení |
| --- | --- | --- | --- |
| **Příliš mnoho vlastníky přiřazené k prostředku** |Střednědobé používání |Příliš mnoho uživatelů mít roli vlastníka. |Zkontrolujte uživatele v seznamu a přiřadit některé méně privilegované role. |
| **Příliš mnoho trvalé vlastníky přiřazené k prostředku** |Střednědobé používání |Příliš mnoho uživatelů jsou trvale přiřazené k roli. |Zkontrolujte uživatele v seznamu a znovu přiřadit některé tak, aby vyžadovala aktivace pro roli použití. |
| **Duplicitní role vytvořen** |Střednědobé používání |Několik rolí obsahuje stejných kritérií. |Použijte pouze jednu z těchto rolí. |


### <a name="severity"></a>Severity
* **Vysoká**: vyžaduje okamžitý zásah kvůli porušení zásad. 
* **Střední**: nevyžaduje okamžitý zásah, ale signály potenciálních porušení zásad.
* **Nízká**: nevyžaduje okamžitý zásah, ale doporučuje změnu upřednostňované zásady.

## <a name="configure-security-alert-settings"></a>Konfigurace nastavení výstrah zabezpečení
Na stránce výstrahy, přejděte na **nastavení**.
![Nastavení](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

Přizpůsobit nastavení na jinou výstrahy pro práci s prostředím a cíle zabezpečení.
![Přizpůsobení nastavení](media/azure-pim-resource-rbac/rbac-alert-settings.png)
