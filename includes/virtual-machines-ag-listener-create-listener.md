---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 394b242ab46da7821f77e8d008836753f4e358e2
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227274"
---
V tomto kroku ručně vytvořit naslouchací proces skupiny dostupnosti v modulu Správce clusteru převzetí služeb při selhání a SQL Server Management Studio.

1. Otevřete Správce clusteru převzetí služeb při selhání z uzlu, který je hostitelem primární repliky.

2. Vyberte **sítě** uzel a Všimněte si název sítě s clustery. Tento název se používá v $ClusterNetworkName proměnné ve skriptu prostředí PowerShell.

3. Rozbalte název clusteru a potom klikněte na tlačítko **role**.

4. V **role** podokně klikněte pravým tlačítkem na název skupiny dostupnosti a pak vyberte **přidat prostředek** > **klientský přístupový bod**.
   
    ![Přidat klientský přístupový bod pro skupinu dostupnosti](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. V **název** pole, vytvoření názvu pro tento nový naslouchací proces, klikněte na tlačítko **Další** dvakrát a potom klikněte na tlačítko **Dokončit**.  
    Není přinášejí naslouchací proces nebo online prostředků v tomto okamžiku.

6. Klikněte na tlačítko **prostředky** kartu a potom rozbalte klientský přístupový bod jste právě vytvořili. 
    Zobrazí se prostředek IP adresy pro každou síť clusteru v clusteru. Pokud to je řešení Azure, zobrazí se pouze jeden prostředek IP adresy.

7. Proveďte jednu z následujících akcí:
   
   * Ke konfiguraci hybridního řešení:
     
        a. Klikněte pravým tlačítkem na prostředek IP adresy, která odpovídá vaší místní podsíti a pak vyberte **vlastnosti**. Poznamenejte si název IP adresy a názvu sítě.
   
        b. Vyberte **statickou IP adresu**, přiřadit nepoužívaná IP adresa a potom klikněte na tlačítko **OK**.
 
   * Konfigurace řešení Azure:

        a. Klikněte pravým tlačítkem na prostředek IP adresy, který odpovídá podsíti služby Azure a pak vyberte **vlastnosti**.
       
       > [!NOTE]
       > Pokud naslouchací proces později selže do režimu online z důvodu konfliktních IP adresa vybraná server DHCP, můžete nakonfigurovat platnou statickou IP adresu v tomto okně Vlastnosti.
       > 
       > 

       b. Ve stejném **IP adresu** okně Vlastnosti, změnit **název IP adresy**.  
        Tento název se používá v proměnné $IPResourceName skript prostředí PowerShell. Pokud vaše řešení obsahuje více virtuálních sítí Azure, můžete tento krok opakujte pro každý prostředek IP.

