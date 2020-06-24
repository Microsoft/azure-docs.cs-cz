---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 5c3277394350036b8863185e83b7cfcd4975be1c
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081348"
---
Umožňuje registraci a zrušení registrace z centra oznámení prostřednictvím back-end služby, kterou jste vytvořili. 

Výstraha se zobrazí, když je zadána akce a aplikace je v popředí. V opačném případě se oznámení zobrazí v centru oznámení.

> [!NOTE]
> Během příslušného bodu životního cyklu aplikace (nebo jako součást prvního spouštěného prostředí) byste obvykle prováděli akce registrace (a zrušení registrace) bez explicitního přihlášení uživatele nebo zrušení registrace vstupů. Tento příklad však bude vyžadovat explicitní uživatelský vstup, aby bylo možné tuto funkci prozkoumat a snadněji otestovat.
