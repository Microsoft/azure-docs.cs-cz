---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 394b242ab46da7821f77e8d008836753f4e358e2
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "67174956"
---
V tomto kroku ručně vytvoříte naslouchací proces skupiny dostupnosti v Správce clusteru s podporou převzetí služeb při selhání a SQL Server Management Studio.

1. Otevřete Správce clusteru s podporou převzetí služeb při selhání z uzlu, který je hostitelem primární repliky.

2. Vyberte uzel **sítě** a potom si poznamenejte název sítě s clustery. Tento název se používá ve $ClusterNetworkName proměnné ve skriptu PowerShellu.

3. Rozbalte název clusteru a klikněte na **role**.

4. V podokně **role** klikněte pravým tlačítkem myši na název skupiny dostupnosti a pak vyberte **Přidat prostředek** > **Klientský přístupový bod**.
   
    ![Přidání klientského přístupového bodu pro skupinu dostupnosti](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. V poli **název** vytvořte název tohoto nového naslouchacího procesu, klikněte dvakrát na **Další** a potom klikněte na **Dokončit**.  
    V tuto chvíli nepřineste naslouchací proces nebo prostředek online.

6. Klikněte na kartu **prostředky** a potom rozbalte Klientský přístupový bod, který jste právě vytvořili. 
    Zobrazí se prostředek IP adresy pro každou síť s clustery v clusteru. Pokud se jedná o řešení jenom Azure, zobrazí se jenom jeden prostředek IP adresy.

7. Proveďte jednu z následujících akcí:
   
   * Konfigurace hybridního řešení:
     
        a. Klikněte pravým tlačítkem na prostředek IP adresy, který odpovídá vaší místní podsíti, a pak vyberte **vlastnosti**. Poznamenejte si název IP adresy a název sítě.
   
        b. Vyberte možnost **statická IP adresa**, přiřaďte nepoužitou IP adresu a klikněte na tlačítko **OK**.
 
   * Konfigurace řešení jenom pro Azure:

        a. Klikněte pravým tlačítkem na prostředek IP adresy, který odpovídá vaší podsíti Azure, a pak vyberte **vlastnosti**.
       
       > [!NOTE]
       > Pokud se naslouchací proces později nepovede do online režimu kvůli konfliktní IP adrese, kterou vybere služba DHCP, můžete v tomto okně vlastností nakonfigurovat platnou statickou IP adresu.
       > 
       > 

       b. V okně se stejnou vlastností **IP adresy** změňte **název IP adresy**.  
        Tento název se používá v proměnné $IPResourceName skriptu PowerShellu. Pokud vaše řešení zahrnuje více virtuálních sítí Azure, opakujte tento krok u každého prostředku IP.

