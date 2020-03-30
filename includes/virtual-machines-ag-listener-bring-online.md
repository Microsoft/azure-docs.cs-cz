---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174958"
---
1. Ve Správci clusterů s podporou převzetí služeb při selhání rozbalte **položku Role**a zvýrazněte skupinu dostupnosti.  

2. Na kartě **Zdroje** klikněte pravým tlačítkem myši na název posluchače a potom klikněte na **příkaz Vlastnosti**.

3. Klikněte na kartu **Závislosti.** Pokud je uvedeno více prostředků, ověřte, zda adresy IP mají závislosti NEBO, nikoli AND.  

4. Klikněte na tlačítko **OK**.

5. Klikněte pravým tlačítkem myši na název posluchače a potom klikněte na **příkaz Převést do režimu online**.

6. Po naslouchací proces je online, na **kartě Zdroje** klikněte pravým tlačítkem myši na skupinu dostupnosti a potom klepněte na příkaz **Vlastnosti**.
   
    ![Konfigurace prostředku skupiny dostupnosti](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Vytvořte závislost na prostředku názvu naslouchací procesu (nikoli na název prostředků adresy IP) a klepněte na tlačítko **OK**.
   
    ![Přidání závislosti na názvu posluchače](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Spusťte sql server management studio a potom se připojte k primární replice.

9. Přejít **na AlwaysOn dostupnost** > **skupiny** > **\<DostupnostSkupinaSkupina\>** > Dostupnost Skupina**naslouchací procesy**.  
    Měl by být zobrazen název posluchače, který jste vytvořili ve Správci clusterů s podporou převzetí služeb při selhání.

10. Klepněte pravým tlačítkem myši na název posluchače a potom klepněte na příkaz **Vlastnosti**.

11. V poli **Port** zadejte číslo portu pro naslouchací proces skupiny dostupnosti pomocí $EndpointPort, který jste použili dříve (v tomto kurzu 1433 byl výchozí) a klepněte na tlačítko **OK**.

