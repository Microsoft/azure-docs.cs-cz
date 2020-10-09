---
title: 'Kurz: Vyrovnávání zatížení internetového provozu na virtuálních počítačích – Azure Portal'
titleSuffix: Azure Load Balancer
description: Tento kurz ukazuje, jak vytvořit a spravovat Standard Load Balancer pomocí webu Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create and Standard Load Balancer so that I can load balance internet traffic to VMs and add and remove VMs from the load-balanced set.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 8961a50490bdbf8b456e87e1c00577c2c8afd050
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80240368"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Kurz: Vyrovnávání zatížení internetového provozu do virtuálních počítačů pomocí Azure Portal

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky na více virtuálních počítačů. V tomto kurzu se dozvíte o různých součástech Standard Load Balancer Azure, které distribuují internetový provoz do virtuálních počítačů a poskytují vysokou dostupnost. Získáte informace o těchto tématech:


> [!div class="checklist"]
> * Vytvoření Azure Load Balancer
> * Vytvoření prostředků Load Balancer
> * Vytvoření virtuálních počítačů a instalace serveru IIS
> * Zobrazit Load Balancer v akci
> * Přidání a odebrání virtuálních počítačů z Load Balancer

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Vytvoření Load Balanceru úrovně Standard

V této části vytvoříte Standard Load Balancer, která pomáhá vyrovnávat zatížení virtuálních počítačů. Load Balancer úrovně Standard podporuje pouze standardní veřejnou IP adresu. Při vytváření Standard Load Balanceru musíte vytvořit také novou standardní veřejnou IP adresu nakonfigurovanou jako jeho front-end (ve výchozím nastavení má název *LoadBalancerFrontend*). 

1. V levém horním rohu obrazovky klikněte na **vytvořit prostředek**  >  **síť**  >  **Load Balancer**.
2. Na kartě **základy** na stránce **vytvořit službu Vyrovnávání zatížení** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **vytvořit nový** a do textového pole zadejte *myResourceGroupSLB* .|
    | Název                   | *myLoadBalancer*                                   |
    | Oblast         | Vyberte **Západní Evropa**.                                        |
    | Typ          | Vyberte **Veřejný**.                                        |
    | Skladová jednotka (SKU)           | Vyberte **Standard**.                          |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. |
    | Název veřejné IP adresy              | Do textového pole zadejte *myPublicIP* .   |
    |Zóna dostupnosti| Vyberte **zóna redundantní**.    |

3. Na kartě **Revize + vytvořit** klikněte na **vytvořit**.

   ![Vytvoření Load Balanceru úrovně Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Vytvoření prostředků Load Balancer

V této části nakonfigurujete Load Balancer nastavení pro fond back-end adres, sondu stavu a zadáváte pravidlo pro vyrovnávání zatížení.

### <a name="create-a-backend-address-pool"></a>Vytvoření fondu back-endových adres

Aby bylo možné distribuovat provoz do virtuálních počítačů, fond adres back-endu obsahuje IP adresy virtuálních (síťových rozhraní) připojených k Load Balancer. Vytvořte fond back-end adres *myBackendPool* , který bude zahrnovat virtuální počítače pro internetovou komunikaci s vyrovnáváním zatížení.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků klikněte na **myLoadBalancer** .
2. V části **Nastavení** klikněte na **Back-endové fondy** a pak klikněte na **Přidat**.
3. Do pole název na stránce **Přidat fond back-end** serveru zadejte *myBackendPool*jako název vašeho back-end fondu a pak vyberte **Přidat**.

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Pokud chcete Load Balancer, aby mohl monitorovat stav vaší aplikace, použijte sondu stavu. Sonda stavu dynamicky přidává nebo odebírá virtuální počítače z Load Balancer rotace na základě jejich reakcí na kontroly stavu. Vytvořte sondu stavu *myHealthProbe* pro monitorování stavu virtuálních počítačů.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků klikněte na **myLoadBalancer** .
2. V části **Nastavení** klikněte na **Sondy stavu** a pak klikněte na **Přidat**.
3. K vytvoření sondy stavu použijte tyto hodnoty:
     
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte *myHealthProbe*. |
    | Protokol | Vyberte **http**. |
    | Port | Zadejte *80*.|
    | Interval | Zadejte hodnotu *15* pro **interval** mezi pokusy o sondu v sekundách. |
    | Prahová hodnota pro poškozený stav | Vyberte *2* pro počet chybných **prahových hodnot** nebo po sobě jdoucích selhání sondy, ke kterým musí dojít, aby se virtuální počítač považoval za poškozený.|
    
4. Vyberte **OK**.

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla Load Balanceru

Pravidlo Load Balanceru slouží k definování způsobu distribuce provozu do virtuálních počítačů. Nadefinujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Vytvořte pravidlo Load Balancer *myLoadBalancerRuleWeb* pro naslouchání na portu 80 ve front-endu *FrontendLoadBalancer* a odesílání síťového provozu s vyrovnáváním zatížení do fondu back-end adres *myBackEndPool* také pomocí portu 80.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků klikněte na **myLoadBalancer** .
2. V části **Nastavení** klikněte na **Pravidla vyrovnávání zatížení** a pak klikněte na **Přidat**.
3. Pomocí těchto hodnot můžete nakonfigurovat pravidlo vyrovnávání zatížení:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte *myHTTPRule*. |
    | Protokol | Vyberte **TCP**. |
    | Port | Zadejte *80*.|
    | Back-endový port | Zadejte *80*. |
    | Back-endový fond | Vyberte *myBackendPool*.|
    | Sonda stavu | Vyberte *myHealthProbe*. |
    
4. Pro ostatní nastavení nechte zvolené výchozí hodnoty a vyberte **OK**.

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části vytvoříte virtuální síť, vytvoříte tři virtuální počítače pro back-end fond Load Balancer a pak na virtuální počítače nainstalujete službu IIS, která vám pomůžou otestovat Load Balancer.

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

V této části budete muset v krocích níže nahradit následující parametry:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupSLB (vyberte existující skupinu prostředků) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | West Europe      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Standard Load Balancer podporuje jenom virtuální počítače se standardními IP adresami ve fondu back-end. V této části vytvoříte tři virtuální počítače (*myVM1*, *myVM2*a *MYVM3*) se standardní veřejnou IP adresou ve třech různých zónách (*zóna 1*, *zóna 2*a *zóna 3*), které se přidají do fondu back-end Standard Load Balancer, který jste vytvořili dříve.

1. V levé horní části portálu vyberte **vytvořit prostředek**  >  **COMPUTE**  >  **Windows Server 2016 Datacenter**. 
   
1. V části **vytvořit virtuální počítač**zadejte nebo vyberte následující hodnoty na kartě **základy** :
   - **Předplatné**  >  **Skupina prostředků**: vyberte **myResourceGroupSLB**.
   - **Podrobnosti instance**  >  **Název virtuálního počítače**: zadejte *myVM1*.
   - **Podrobnosti instance**  >  **Oblast** > vyberte **západní Evropa**.
   - **Podrobnosti instance**  >  **Možnosti dostupnosti** > vybrat **zóny dostupnosti**. 
   - **Podrobnosti instance**  >  **Zóna dostupnosti** > vybrat **1**.
  
1. Vyberte kartu **síť** nebo vyberte **Další: disky**a **Další: síť**. 
   
   - Ujistěte se, že jsou vybrané následující:
       - **Virtuální síť**: **myVnet**
       - **Podsíť**: **myBackendSubnet**
       - **Veřejná IP** adresa > vyberte **vytvořit novou**a v okně **vytvořit veřejnou IP adresu** pro položku **SKU**vyberte možnost **Standard**a pro **zónu dostupnosti**vyberte **zóna – redundantní** .
      
   - Pokud chcete vytvořit novou skupinu zabezpečení sítě (NSG), typ brány firewall, v části **Skupina zabezpečení sítě**vyberte **Upřesnit**. 
       1. V poli **Konfigurovat skupinu zabezpečení sítě** vyberte **vytvořit novou**. 
       1. Zadejte *myNetworkSecurityGroup*a vyberte **OK**.

   - Pokud chcete virtuálnímu počítači udělat součást back-endu fondu Load Balancer, proveďte následující kroky:
        - V případě **Vyrovnávání zatížení** **umístěte tento virtuální počítač za existující řešení vyrovnávání zatížení**a vyberte **Ano**.
        - V **nastavení vyrovnávání zatížení**v možnosti vyrovnávání **zatížení**vyberte **Azure Load Balancer**.
        - Pro **Vyberte nástroj pro vyrovnávání zatížení** *myLoadBalancer*. 
1. Vyberte kartu **Správa** nebo vyberte možnost **Další**  >  **Správa**. V části **monitorování**nastavte **diagnostiku spouštění** na **vypnuto**. 
1. Vyberte **Zkontrolovat a vytvořit**.   
1. Zkontrolujte nastavení a pak vyberte **vytvořit**.
1. Použijte postup vytvoření dvou dalších virtuálních počítačů – *myVM2* a *MYVM3*s veřejnou IP adresou standardní SKU v **zóně dostupnosti** **2** a **3** a všechna ostatní nastavení stejná jako *myVM1*.  

### <a name="create-network-security-group-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

V této části vytvoříte pravidlo skupiny zabezpečení sítě, které povolí příchozí připojení pomocí protokolu HTTP.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom ze seznamu prostředky klikněte na **myNetworkSecurityGroup** , která je umístěná ve skupině prostředků **myResourceGroupSLB** .
2. V části **Nastavení** klikněte na **Příchozí pravidla zabezpečení** a pak klikněte na **Přidat**.
3. Zadáním následujících hodnot pro příchozí pravidlo zabezpečení *myHTTPRule* povolte příchozí připojení HTTP na portu 80:
    - Jako **Zdroj** zadejte *Značka služby*.
    - Jako **Značka zdrojové služby** zadejte *Internet*.
    - Jako **Rozsahy cílových portů** zadejte *80*.
    - Jako **Protokol** zadejte *TCP*.
    - Jako **Akce** zadejte *Povolit*.
    - Jako **Priorita** zadejte *100*.
    - *myHTTPRule* pro název
    - Popis *Povolení protokolu HTTP*
4. Vyberte **Přidat**.

### <a name="install-iis-on-vms"></a>Instalace služby IIS na virtuální počítače

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom ze seznamu prostředky klikněte na **myVM1** , která je umístěná ve skupině prostředků *myResourceGroupSLB* .
2. Na stránce **Přehled** klikněte na **Připojit** a připojte se přes RDP k virtuálnímu počítači.
3. V automaticky otevíraném okně **Připojení k virtuálnímu počítači** vyberte **Stáhnout soubor RDP** a pak otevřete stažený soubor RDP.
4. V okně **Připojení ke vzdálené ploše** klikněte na **Připojit**.
5. Přihlaste se k virtuálnímu počítači pomocí přihlašovacích údajů, které jste zadali při vytváření tohoto virtuálního počítače. Tím se otevře relace vzdálené plochy s virtuálním počítačem *myVM1*.
6. Na ploše serveru přejděte do části **Nástroje pro správu Windows** > **Windows PowerShell**.
7. V okně PowerShellu spuštěním následujících příkazů nainstalujte server služby IIS, odeberte výchozí soubor iisstart.htm a pak přidejte nový soubor iisstart.htm, který zobrazuje název virtuálního počítače:

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Zavřete relaci RDP pomocí *myVM1*.
7. Opakováním kroků 1 až 6 nainstalujte službu IIS a aktualizovaný soubor iisstart.htm na *myVM2* a *myVM3*.

## <a name="test-the-load-balancer"></a>Otestování Load Balancer
1. Na obrazovce **Přehled** vyhledejte veřejnou IP adresu Load Balanceru. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a pak klikněte na **myPublicIP**.

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

      ![Webový server služby IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Pokud chcete zobrazit Load Balancer distribuci provozu mezi třemi virtuálními počítači, na kterých běží vaše aplikace, můžete vynutit aktualizaci webového prohlížeče.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Odeberte nebo přidejte virtuální počítače z back-endového fondu.
Na virtuálních počítačích, na kterých je vaše aplikace spuštěná, možná budete potřebovat provést údržbu, například nainstalovat aktualizace operačního systému. Abyste si poradili se zvýšením provozu do vaší aplikace, možná budete muset přidat další virtuální počítače. V této části se dozvíte, jak odebrat nebo přidat virtuální počítač (*myVM1*) z Load Balancer.

### <a name="remove-vm-from-a-backend-pool"></a>Odebrat virtuální počítač ze back-endu fondu
Pokud chcete odebrat *myVM1* z back-end fondu, proveďte následující kroky:

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků klikněte na **myLoadBalancer** .
2. V části **Nastavení** klikněte na **Back-endové fondy** a v seznamu back-endového fondu klikněte na **myBackendPool**.
3. Na stránce **myBackendPool** pro odebrání *VM1* na konci řádku, který zobrazuje *myVM1*, vyberte ikonu Odstranit a pak klikněte na **Uložit**.

Když už *myVM1* není v back-endovém fondu adres, můžete na *myVM1* provádět všechny úlohy údržby, jako jsou třeba instalace aktualizací softwaru. V případě absence *VM1*se zatížení teď vyrovnává napříč *myVM2* a *myVM3*. 

### <a name="add-vm-to-a-backend-pool"></a>Přidání virtuálního počítače do back-endového fondu
Pokud chcete přidat *myVM1* zpátky do back-end fondu, proveďte následující kroky:

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myVM1** .
2. Na stránce **VM1** v části **Nastavení**vyberte **sítě**.
3. Na stránce **sítě** vyberte kartu **Vyrovnávání zatížení** a pak vyberte **Přidat vyrovnávání zatížení**.
4. Na stránce **Přidat vyrovnávání zatížení** udělejte toto:
   1. V **možnostech vyrovnávání zatížení**vyberte **Azure Load Balancer**.
   2. V případě **Vyberte nástroj pro vyrovnávání zatížení**vyberte *myLoadBalancer*.
   3. V případě **Vyberte fond back-end**vyberte možnost *myBackendPool*. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, Load Balancer a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků *myResouceGroupSLB* , která obsahuje Load Balancer, a pak vyberete  **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili Standard Load Balancer připojené virtuální počítače, nakonfigurovali jste pravidlo Load Balancer přenosů dat, sondu stavu a pak jste otestovali Load Balancer. Také jste odebrali virtuální počítač ze skupiny s vyrovnáváním zatížení a přidali jste virtuální počítač zpátky do fondu back-endových adres. Chcete-li zjistit další informace o službě Azure Load Balancer, přejděte ke kurzům pro Azure Load Balancer.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
