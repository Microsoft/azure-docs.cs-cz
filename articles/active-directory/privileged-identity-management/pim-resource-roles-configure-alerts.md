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
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 0ad4cde6a59a8867f9ff3b3cb9d9bded06e34256
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699579"
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
