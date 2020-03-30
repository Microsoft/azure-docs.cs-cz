---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 394b242ab46da7821f77e8d008836753f4e358e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174956"
---
V tomto kroku ručně vytvoříte naslouchací proces skupiny dostupnosti ve Správci clusterů s podporou převzetí služeb při selhání a v aplikaci SQL Server Management Studio.

1. Otevřete Správce clusteru s podporou převzetí služeb při selhání z uzlu, který je hostitelem primární repliky.

2. Vyberte uzel **Sítě** a poznamenejte si název sítě clusteru. Tento název se používá v proměnné $ClusterNetworkName ve skriptu prostředí PowerShell.

3. Rozbalte název clusteru a klikněte na **Role**.

4. V podokně **Role** klikněte pravým tlačítkem myši na název skupiny dostupnosti a potom vyberte přidat**klientský přístupový bod** **prostředků** > .
   
    ![Přidat klientský přístupový bod pro skupinu dostupnosti](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. V poli **Název** vytvořte název pro tento nový naslouchací proces, dvakrát klepněte na **tlačítko Další** a potom klepněte na tlačítko **Dokončit**.  
    Nepřenesete naslouchací proces nebo prostředek do režimu online v tomto okamžiku.

6. Klikněte na kartu **Prostředky** a rozbalte klientský přístupový bod, který jste právě vytvořili. 
    Zobrazí se prostředek adresy IP pro každou síť clusteru v clusteru. Pokud se jedná o řešení pouze pro Azure, zobrazí se pouze jeden prostředek IP adresy.

7. Proveďte jednu z následujících akcí:
   
   * Konfigurace hybridního řešení:
     
        a. Klepněte pravým tlačítkem myši na prostředek adresy IP, který odpovídá místní podsíti, a potom vyberte **příkaz Vlastnosti**. Poznamenejte si název adresy IP a název sítě.
   
        b. Vyberte **statická adresa IP**, přiřaďte nepoužitou adresu IP a klepněte na tlačítko **OK**.
 
   * Konfigurace řešení pouze pro Azure:

        a. Klikněte pravým tlačítkem myši na prostředek IP adresy, který odpovídá vaší podsíti Azure, a pak vyberte **Vlastnosti**.
       
       > [!NOTE]
       > Pokud se naslouchací proces později nepodaří přepnout do režimu online z důvodu konfliktní adresy IP vybrané službou DHCP, můžete v tomto okně vlastností nakonfigurovat platnou statickou adresu IP.
       > 
       > 

       b. Ve stejném okně vlastností **adresy IP** změňte název **adresy IP**.  
        Tento název se používá v proměnné $IPResourceName skriptu prostředí PowerShell. Pokud vaše řešení zahrnuje více virtuálních sítí Azure, opakujte tento krok pro každý prostředek IP.

