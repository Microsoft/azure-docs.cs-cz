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
ms.openlocfilehash: bf107da82fb3f772a341e70ce472f08ea674a450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75692528"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Vytvoření účtu úložiště pro agenta Event Processor Host
Event Processor Host je inteligentní agent, který zjednodušuje přijímání událostí ze služby Event Hubs tím, že spravuje trvalé kontrolní body a paralelní příjmy. K vytváření kontrolních bodů vyžaduje Event Processor Host účet úložiště. Následující příklad ukazuje, jak vytvořit účet úložiště a získat jeho klíče pro přístup:

1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**.

    ![Vytvoření položky nabídky prostředků, portál Microsoft Azure](./media/event-hubs-create-storage/create-resource.png)

2. Vyberte**účet úložiště**. **Storage** > 
   
    ![Vyberte účet úložiště, portál Microsoft Azure.](./media/event-hubs-create-storage/select-storage-account.png)

3. Na stránce **Vytvořit účet úložiště** proveďte následující kroky: 

   1. Zadejte **název účtu úložiště**.
   2. Zvolte **předplatné** Azure, které obsahuje centrum událostí.
   3. Zvolte nebo vytvořte **skupinu prostředků,** která má centrum událostí.
   4. Vyberte **umístění,** ve kterém chcete zdroj vytvořit. 
   5. Vyberte **Zkontrolovat a vytvořit**.
   
        ![Recenze + vytvoření, vytvoření účtu úložiště, portál Microsoft Azure](./media/event-hubs-create-storage/review-create.png)

4. Na stránce **Zkontrolovat a vytvořit** zkontrolujte hodnoty a vyberte **Vytvořit**. 

    ![Kontrola nastavení účtu úložiště a vytvoření portálu Microsoft Azure](./media/event-hubs-create-storage/create-storage-account.png)
5. Až se v oznámeních zobrazí zpráva **Deployments Succeeded,** vyberte **přejít na prostředek** a otevřete stránku Účet úložiště. Případně můžete rozbalit **podrobnosti o nasazení** a pak vybrat nový prostředek ze seznamu prostředků.  

    ![Přejděte na prostředek, nasazení účtu úložiště, portál Microsoft Azure](./media/event-hubs-create-storage/go-to-resource.png) 
6. Vyberte **kontejnery**.

    ![Vyberte službu kontejnerů objektů Blob, účty úložiště, portál Microsoft Azure](./media/event-hubs-create-storage/select-blob-container-service.png)
7. Nahoře vyberte **+ Kontejner,** zadejte **název** kontejneru a vyberte **OK**. 

    ![Vytvoření nového kontejneru objektů blob, účtů úložiště, portálmicrosoft Azure](./media/event-hubs-create-storage/create-new-blob-container.png)
8. Z nabídky stránky **Účtu úložiště** zvolte **Přístupové klávesy** a zkopírujte hodnotu **key1**.

    Uložte následující hodnoty do poznámkového bloku nebo do jiného dočasného umístění.
    - Název účtu úložiště
    - Přístupový klíč pro účet úložiště
    - Název kontejneru
