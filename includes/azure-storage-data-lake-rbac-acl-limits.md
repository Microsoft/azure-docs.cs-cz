---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 97b47b48a183951002d04cd58b08d5e2b9051eda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017664"
---
| Mechanismus | Obor |Omezení | Podporovaná úroveň oprávnění |
|---|---|---|---|
| Azure RBAC | Účty úložiště, kontejnery. <br>Přiřazení rolí Azure mezi prostředky na úrovni předplatného nebo skupiny prostředků. | 2000 přiřazení rolí Azure v předplatném | Role Azure (předdefinované nebo vlastní) |
| SEZNAMU ACL| Adresář, soubor |32 položek seznamu ACL (efektivně 28 položek seznamu ACL) na soubor a pro každý adresář. Přístup a výchozí seznamy ACL mají svůj vlastní limit počtu položek seznamu řízení přístupu (ACL) 32. |Oprávnění seznamu řízení přístupu|
