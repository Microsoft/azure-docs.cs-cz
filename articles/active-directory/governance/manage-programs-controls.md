---
title: Správa programů a ovládacích prvků pro kontroly přístupu – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit další programy pro každou zásad správného řízení, řízení rizik a dodržování předpisů iniciativy ve vaší organizaci shromažďování a uspořádání využitím kontrol přístupu Azure Active Directory jako ovládací prvky.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43c0f1c041bfed1b041a9926efd869d167c6f1e9
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577259"
---
# <a name="manage-programs-and-controls-for-azure-ad-access-reviews"></a>Správa programů a ovládacích prvků pro službu Azure AD kontroly přístupu

Azure Active Directory (Azure AD) obsahuje kontrol přístupu členů skupiny a přístup k aplikacím. Příklady ovládacích prvků zajistit dohledu, pro který má přístup k vaší organizaci členství ve skupinách a aplikace. Organizace můžou použít tyto ovládací prvky efektivně vyřešit jejich zásad správného řízení, řízení rizik a požadavky na dodržování předpisů.

## <a name="create-and-manage-programs-and-their-controls"></a>Vytvoření a Správa programů a jejich ovládacích prvků
Jak sledovat a shromažďovat kontroly přístupu pro různé účely jejich uspořádáním do programů může zjednodušit. Každý kontroly přístupu lze propojit do programu. Potom při přípravě sestavy pro auditor můžete soustředit na kontroly přístupu v oboru pro konkrétní iniciativy.  Programy a výsledky kontroly přístupu jsou viditelné pro uživatele v globální správce, Správce uživatelů, správce zabezpečení nebo roli Čtenář zabezpečení.

Pokud chcete zobrazit seznam aplikací, přejděte na [stránku kontrol přístupu](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) a vyberte **programy**.

**Výchozí Program** je vždy k dispozici. Pokud jste globální správce nebo role uživatele správce, můžete vytvořit další programy. Například můžete mít jeden program pro každou dodržování předpisů iniciativy nebo obchodní cíle.

Pokud už nepotřebujete programu a nemá žádné ovládací prvky na sebe, můžete ho odstranit.

## <a name="next-steps"></a>Další postup

- [Vytvoření kontroly přístupu skupiny nebo aplikace](create-access-review.md)
- [Načíst výsledky kontroly přístupu pro skupiny a aplikace](retrieve-access-review.md)
