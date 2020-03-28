---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 574003a150ef294aa6a2ebc035fe48cf877dec66
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76545196"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>Konfigurace relace prostorové kotvy cloudu

Postaráme se o konfiguraci relace prostorové kotvy v cloudu. Na prvním řádku jsme nastavili poskytovatele senzorů na relaci. Od této chvíle bude každá kotva, kterou vytvoříme během relace, spojena se sadou údajů ze senzorů. Dále instanci je hledání kritérií v blízkosti zařízení a inicializovat tak, aby odpovídaly požadavkům aplikace. Nakonec jsme pokyn relace používat data ze senzorů při hledání kotvy vytvořením pozorovatele z našich kritérií blízkého zařízení.