---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 7c3887367b6365ea3577bbff6cc19bf34d178ee6
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081376"
---
### <a name="create-a-notification-hub"></a>Vytvoříte centrum oznámení. 

V této části vytvoříte centrum oznámení a nakonfigurujete ověřování pomocí služby **APNs**. Můžete použít certifikát P12 push Certificate nebo ověřování založené na tokenech. Pokud chcete použít Centrum oznámení, které jste už vytvořili, můžete přeskočit na krok 5.

1. Přihlaste se k [Azure](https://portal.azure.com).

1. Klikněte na **vytvořit prostředek**, vyhledejte a vyberte **Centrum oznámení**a pak klikněte na **vytvořit**.

1. Aktualizujte následující pole a pak klikněte na **vytvořit**:

    **ZÁKLADNÍ PODROBNOSTI**  

    **Předplatné:** Z rozevíracího seznamu vyberte cílové **předplatné** .  
    **Skupina prostředků:** Vytvořte novou **skupinu prostředků** (nebo vyberte existující).  

    **PODROBNOSTI OBORU NÁZVŮ**  

    **Obor názvů centra oznámení:** Zadejte globálně jedinečný název oboru názvů **centra oznámení** .  

    > [!NOTE]
    > Zajistěte, aby byla pro toto pole vybrána možnost **vytvořit novou** .

    **PODROBNOSTI CENTRA OZNÁMENÍ**  

    **Centrum oznámení:** Zadejte název **centra oznámení** .  
    **Umístění:** Z rozevíracího seznamu vyberte vhodné umístění.  
    **Cenová úroveň:** Zachovat výchozí možnost **Free**  

    > [!NOTE]
    > Pokud jste nedosáhli maximálního počtu rozbočovačů na úrovni Free.

1. Po zřízení **centra oznámení** přejděte k tomuto prostředku.
1. Přejděte do nového **centra oznámení**.
1. V seznamu vyberte **zásady přístupu** ( **Správa**).
1. Poznamenejte si hodnoty **názvu zásad** spolu s odpovídajícími hodnotami **připojovacího řetězce** .
