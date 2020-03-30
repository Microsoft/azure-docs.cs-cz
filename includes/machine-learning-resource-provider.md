---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75893970"
---
Při vytváření pracovního prostoru Azure Machine Learning nebo prostředku používaného v pracovním prostoru se může zobrazit chyba podobná následujícím zprávám:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

Většina poskytovatelů prostředků je automaticky registrována, ale ne všichni. Pokud se zobrazí tato zpráva, je třeba zaregistrovat uvedeného zprostředkovatele.

Informace o registraci zprostředkovatelů prostředků naleznete v tématu [Řešení chyb při registraci zprostředkovatele prostředků](../articles/azure-resource-manager/templates/error-register-resource-provider.md).