---
title: zahrnout soubor
description: zahrnout soubor
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/16/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: e499a0f7bec47e672c599c729a15cc3e3d04a28a
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471609"
---
## <a name="create-a-storage-account-for-event-processor-host"></a>Vytvoření účtu úložiště pro agenta Event Processor Host
Event Processor Host je inteligentní agent, který zjednodušuje přijímání událostí ze služby Event Hubs tím, že spravuje trvalé kontrolní body a paralelní příjmy. K vytváření kontrolních bodů vyžaduje Event Processor Host účet úložiště. Následující příklad ukazuje, jak vytvořit účet úložiště a získat jeho klíče pro přístup:

1. V Azure portal a vyberte **vytvořit prostředek** v levém horním rohu obrazovky.

2. Vyberte **Úložiště** a pak vyberte **Účet úložiště – objekt blob, soubor, tabulka, fronta**.
   
    ![Zvolit účet úložiště](./media/event-hubs-create-storage/create-storage1.png)

3. Na **vytvořit účet úložiště** stránce, proveďte následující kroky: 

    1. Zadejte název účtu úložiště. 
    2. Zvolte předplatné Azure, které obsahuje centra událostí.
    3. Vyberte skupinu prostředků, který má centra událostí.
    4. Vyberte umístění, do kterého se má prostředek vytvořit. 
    5. Pak klikněte na tlačítko **revize + vytvořit**.
   
    ![Vytvořit účet úložiště – stránka](./media/event-hubs-create-storage/create-storage2.png)

4. Na **zkontrolujte + vytvořit** stránky, zkontrolujte hodnoty a vyberte **vytvořit**. 

    ![Zkontrolujte nastavení účtu úložiště a vytvořit](./media/event-hubs-create-storage/review-create-storage-account.png)
5. Po zobrazení **nasazení proběhla úspěšně** zprávu, vyberte **je teď k prostředku** v horní části stránky. Na stránce účtu úložiště můžete také spustit tak, že váš účet úložiště vyberete ze seznamu prostředků.  

    ![Vyberte účet úložiště z nasazení](./media/event-hubs-create-storage/select-storage-deployment.png) 
7. V **Essentials** okně **objekty BLOB**. 

    ![Vyberte službu, objekty BLOB](./media/event-hubs-create-storage/select-blobs-service.png)
1. Vyberte **+ kontejner** v horní části stránky, zadejte **název** pro kontejner a vyberte **OK**. 

    ![Vytvoření kontejneru objektů blob](./media/event-hubs-create-storage/create-blob-container.png)
1. Vyberte **přístupové klíče** v nabídce levé straně a zkopírujte hodnotu **key1**. 

    Uložte tyto hodnoty do poznámkového bloku nebo jiného dočasného umístění.
    - Název účtu úložiště
    - Přístupový klíč pro účet úložiště
    - Název kontejneru