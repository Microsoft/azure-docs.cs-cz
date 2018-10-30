---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226505"
---
1. V modulu Správce clusteru převzetí služeb při selhání rozbalte **role**a pak zvýrazněte vaší skupiny dostupnosti.  

2. Na **prostředky** kartu, klikněte pravým tlačítkem na název naslouchacího procesu a potom klikněte na tlačítko **vlastnosti**.

3. Klikněte na tlačítko **závislosti** kartu. Pokud nejsou uvedeny různé prostředky, ověřte, že IP adresy mají OR, not a závislosti.  

4. Klikněte na **OK**.

5. Klikněte pravým tlačítkem na název naslouchacího procesu a potom klikněte na tlačítko **přepnout do režimu Online**.

6. Po online na naslouchací proces **prostředky** kartu, klikněte pravým tlačítkem na skupinu dostupnosti a pak klikněte na tlačítko **vlastnosti**.
   
    ![Konfigurovat prostředek skupiny dostupnosti](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Vytvoření závislosti na prostředku názvu naslouchací proces (není IP adresa zdroje název) a potom klikněte na tlačítko **OK**.
   
    ![Přidat závislost na název naslouchacího procesu](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Spusťte SQL Server Management Studio a připojte se k primární replice.

9. Přejděte na **AlwaysOn vysokou dostupnost** > **skupin dostupnosti** > **\<AvailabilityGroupName\>**   >  **Naslouchacích procesů skupin dostupnosti**.  
    Naslouchací proces s názvem, který jste vytvořili v modulu Správce clusteru převzetí služeb při selhání má být zobrazena.

10. Klikněte pravým tlačítkem na název naslouchacího procesu a potom klikněte na tlačítko **vlastnosti**.

11. V **Port** pole, zadejte číslo portu pro naslouchací proces skupiny dostupnosti s využitím $EndpointPort, který jste použili dříve (v tomto kurzu, 1433 se výchozí hodnota) a potom klikněte na tlačítko **OK**.

