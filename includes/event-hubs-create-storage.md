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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "75692528"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Vytvoření účtu úložiště pro agenta Event Processor Host
Event Processor Host je inteligentní agent, který zjednodušuje přijímání událostí ze služby Event Hubs tím, že spravuje trvalé kontrolní body a paralelní příjmy. K vytváření kontrolních bodů vyžaduje Event Processor Host účet úložiště. Následující příklad ukazuje, jak vytvořit účet úložiště a získat jeho klíče pro přístup:

1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**.

    ![Vytvoření položky nabídky prostředků, portál Microsoft Azure](./media/event-hubs-create-storage/create-resource.png)

2. Vyberte **Úložiště** > **Účet úložiště**.
   
    ![Vyberte účet úložiště, portál Microsoft Azure](./media/event-hubs-create-storage/select-storage-account.png)

3. Na stránce **Vytvořit účet úložiště** proveďte následující kroky: 

   1. Zadejte **název účtu úložiště**.
   2. Vyberte **předplatné** Azure, které obsahuje centrum událostí.
   3. Vyberte nebo vytvořte **skupinu prostředků** , která má centrum událostí.
   4. Vyberte **umístění** , ve kterém se má prostředek vytvořit. 
   5. Vyberte **Zkontrolovat a vytvořit**.
   
        ![Projděte si téma Vytvoření, vytvoření účtu úložiště, portál Microsoft Azure](./media/event-hubs-create-storage/review-create.png)

4. Na stránce **Zkontrolovat a vytvořit** zkontrolujte hodnoty a vyberte **Vytvořit**. 

    ![Zkontrolujte nastavení účtu úložiště a vytvořte portál Microsoft Azure](./media/event-hubs-create-storage/create-storage-account.png)
5. Po zobrazení zprávy o **úspěšném nasazení** v oznámeních vyberte **Přejít k prostředku** a otevřete stránku účtu úložiště. Případně můžete rozbalit **Podrobnosti nasazení** a pak vybrat nový prostředek ze seznamu prostředků.  

    ![Přejít na prostředek, nasazení účtu úložiště, portál Microsoft Azure](./media/event-hubs-create-storage/go-to-resource.png) 
6. Vyberte **kontejnery**.

    ![Vyberte službu kontejnerů objektů blob, účty úložiště, portál Microsoft Azure](./media/event-hubs-create-storage/select-blob-container-service.png)
7. V horní části vyberte **+ kontejner** , zadejte **název** kontejneru a vyberte **OK**. 

    ![Vytvořte nový kontejner objektů blob, účty úložiště, portál Microsoft Azure](./media/event-hubs-create-storage/create-new-blob-container.png)
8. V nabídce Stránka **účtu úložiště** vyberte **přístupové klíče** a zkopírujte hodnotu **klíč1**.

    Následující hodnoty uložte do poznámkového bloku nebo jiného dočasného umístění.
    - Název účtu úložiště
    - Přístupový klíč pro účet úložiště
    - Název kontejneru
