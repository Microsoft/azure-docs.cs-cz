---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75893970"
---
Při vytváření pracovního prostoru Azure Machine Learning nebo prostředku, který používá pracovní prostor, se může zobrazit chyba podobná následující zprávě:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

Většina poskytovatelů prostředků se registruje automaticky, ale ne všechny. Pokud se zobrazí tato zpráva, je nutné zaregistrovat zmíněného poskytovatele.

Informace o registraci poskytovatelů prostředků najdete v tématu [řešení chyb pro registraci poskytovatele prostředků](../articles/azure-resource-manager/templates/error-register-resource-provider.md).