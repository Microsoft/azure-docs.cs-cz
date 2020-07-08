---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "67174958"
---
1. V Správce clusteru s podporou převzetí služeb při selhání rozbalte **role**a pak Zvýrazněte skupinu dostupnosti.  

2. Na kartě **prostředky** klikněte pravým tlačítkem myši na název naslouchacího procesu a potom klikněte na příkaz **vlastnosti**.

3. Klikněte na kartu **závislosti** . Pokud je v seznamu uvedeno více prostředků, ověřte, zda IP adresa obsahuje nebo, nikoli a, závislosti.  

4. Klikněte na **OK**.

5. Klikněte pravým tlačítkem na název naslouchacího procesu a pak klikněte na **převést do režimu online**.

6. Po online spuštění naslouchacího procesu na kartě **prostředky** klikněte pravým tlačítkem na skupinu dostupnosti a pak klikněte na **vlastnosti**.
   
    ![Konfigurace prostředku skupiny dostupnosti](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Vytvořte závislost na prostředku názvu naslouchacího procesu (nejedná se o název prostředků IP adresy) a pak klikněte na **OK**.
   
    ![Přidat závislost na název naslouchacího procesu](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Spusťte SQL Server Management Studio a pak se připojte k primární replice.

9. Přejít na **AlwaysOn High Availability**  >  **Availability Groups**  >  **\<AvailabilityGroupName\>**  >  **naslouchací procesy**skupin dostupnosti AlwaysOn vysoké dostupnosti  
    Měl by se zobrazit název naslouchacího procesu, který jste vytvořili v Správce clusteru s podporou převzetí služeb při selhání.

10. Klikněte pravým tlačítkem myši na název naslouchacího procesu a pak klikněte na **vlastnosti**.

11. Do pole **port** zadejte číslo portu pro naslouchací proces skupiny dostupnosti pomocí $EndpointPort, který jste použili dříve (v tomto kurzu byl standardně 1433) a pak klikněte na **OK**.

