---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 8b793a28c31df185555b9a548df52e4beeccf305
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131628"
---
| Mechanismus | Obor |Omezení | Podporovaná úroveň oprávnění |
|---|---|---|---|
| RBAC | Účty úložiště, kontejnery. <br>Přiřazení role RBAC mezi prostředky na úrovni předplatného nebo skupiny prostředků. | přiřazení rolí RBAC v předplatném 2000 | Role RBAC (předdefinované nebo vlastní) |
| SEZNAMU ACL| Adresář, soubor |32 položek seznamu ACL (efektivně 28 položek seznamu ACL) na soubor a pro každý adresář. Přístup a výchozí seznamy ACL mají svůj vlastní limit počtu položek seznamu řízení přístupu (ACL) 32. |Oprávnění seznamu řízení přístupu|
