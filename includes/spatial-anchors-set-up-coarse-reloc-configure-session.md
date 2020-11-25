---
author: msftradford
ms.author: parkerra
ms.date: 11/20/2020
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 41c0a6292bf7369a30fbb1e4bd474e70ffaedcb6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999728"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>Konfigurace relace prostorové kotvy cloudu

V dalším kroku se postará o konfiguraci relace prostorového kotva cloudu. Na prvním řádku nastavíme v relaci poskytovatele senzorů. Od této chvíle budou všechny kotvy, které během relace vytvoříte, přidruženy k sadě čtení snímačů. V dalším kroku vytvoříme pro každé zařízení kritéria hledání a inicializujete je, aby odpovídala požadavkům aplikace. Nakonec připravujeme relaci, aby používala data senzorů při hledání kotev tím, že vytvoří sledovací proces z našich kritérií téměř pro zařízení.