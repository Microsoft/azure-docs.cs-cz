---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 7ac98f608cb1ca5bab590714623bc78036d46b81
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093136"
---
## <a name="configure-the-cloud-anchor-session"></a>Konfigurace relace kotvy do cloudu

V dalším kroku se postará o konfiguraci relace kotvy do cloudu. Na prvním řádku nastavíme v relaci poskytovatele senzorů. Od této chvíle budou všechny kotvy, které během relace vytvoříte, přidruženy k sadě čtení snímačů. V dalším kroku vytvoříme pro každé zařízení kritéria hledání a inicializujete je, aby odpovídala požadavkům aplikace. Nakonec připravujeme relaci, aby používala data senzorů při hledání kotev tím, že vytvoří sledovací proces z našich kritérií téměř pro zařízení.