---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 97b47b48a183951002d04cd58b08d5e2b9051eda
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017664"
---
| Mechanismus | Rozsah |Omezení | Podporovaná úroveň oprávnění |
|---|---|---|---|
| Azure RBAC | Účty úložiště, kontejnery. <br>Přiřazení rolí Azure mezi prostředky na úrovni předplatného nebo skupiny prostředků. | 2000 přiřazení rolí Azure v předplatném | Role Azure (předdefinované nebo vlastní) |
| SEZNAMU ACL| Adresář, soubor |32 položek seznamu ACL (efektivně 28 položek seznamu ACL) na soubor a pro každý adresář. Přístup a výchozí seznamy ACL mají svůj vlastní limit počtu položek seznamu řízení přístupu (ACL) 32. |Oprávnění seznamu řízení přístupu|
