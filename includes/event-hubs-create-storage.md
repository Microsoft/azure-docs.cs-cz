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
ms.openlocfilehash: b84b0a8e09bf739ce62dee167ff751b491765c66
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66154553"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Vytvoření účtu úložiště pro agenta Event Processor Host
Event Processor Host je inteligentní agent, který zjednodušuje přijímání událostí ze služby Event Hubs tím, že spravuje trvalé kontrolní body a paralelní příjmy. K vytváření kontrolních bodů vyžaduje Event Processor Host účet úložiště. Následující příklad ukazuje, jak vytvořit účet úložiště a získat jeho klíče pro přístup:

1. Na webu Azure Portal v levém horním rohu obrazovky vyberte **Vytvořit prostředek**.

2. Vyberte **Úložiště** a pak vyberte **Účet úložiště – objekt blob, soubor, tabulka, fronta**.
   
    ![Výběr účtu úložiště](./media/event-hubs-create-storage/create-storage1.png)

3. Na stránce **Vytvořit účet úložiště** proveďte následující kroky: 

   1. Zadejte název účtu úložiště. 
   2. Zvolte předplatné Azure obsahující centrum událostí.
   3. Vyberte skupinu prostředků obsahující centrum událostí.
   4. Vyberte umístění, ve kterém se má prostředek vytvořit. 
   5. Pak klikněte na **Zkontrolovat a vytvořit**.
   
      ![Stránka Vytvořit účet úložiště](./media/event-hubs-create-storage/create-storage2.png)

4. Na stránce **Zkontrolovat a vytvořit** zkontrolujte hodnoty a vyberte **Vytvořit**. 

    ![Kontrola nastavení účtu úložiště a jeho vytvoření](./media/event-hubs-create-storage/review-create-storage-account.png)
5. Jakmile se zobrazí **nasazení proběhla úspěšně** zprávu, vyberte **přejít k prostředku** v horní části stránky. Na stránce účtu úložiště můžete také spustit tak, že váš účet úložiště vyberete ze seznamu prostředků.  

    ![Výběr účtu úložiště z nasazení](./media/event-hubs-create-storage/select-storage-deployment.png) 
7. V okně **Základy** vyberte **Objekty blob**. 

    ![Vyberte službu, objekty BLOB](./media/event-hubs-create-storage/select-blobs-service.png)
1. Vyberte **+ kontejner** v horní části stránky, zadejte **název** pro kontejner a vyberte **OK**. 

    ![Vytvoření kontejneru objektů blob](./media/event-hubs-create-storage/create-blob-container.png)
1. Vyberte **přístupové klíče** v nabídce levé straně a zkopírujte hodnotu **key1**. 

    Uložte tyto hodnoty do poznámkového bloku nebo jiného dočasného umístění.
    - Název účtu úložiště
    - Přístupový klíč pro účet úložiště
    - Název kontejneru
