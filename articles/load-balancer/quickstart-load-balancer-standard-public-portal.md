---
title: Quickstart:Create a Standard Load Balancer - Azure portal
titleSuffix: Azure Load Balancer
description: This quickstart shows how to create a Standard Load Balancer by using the Azure portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Standard Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: d15223dfe6d9ce710f2a3d402a49203ef169132e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225196"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Rychlý start: Vytvoření Load Balanceru úrovně Standard pro vyrovnávání zatížení virtuálních počítačů pomocí portálu Azure Portal

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky na více virtuálních počítačů. K vytvoření nástroje pro vyrovnávání zatížení virtuálních počítačů můžete použít web Azure Portal. V tomto rychlém startu se dozvíte, jak vyrovnávat zatížení virtuálních počítačů pomocí Load Balanceru úrovně Standard.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Vytvoření Load Balanceru úrovně Standard

In this section, you create a Standard Load Balancer that helps load balance virtual machines. Load Balancer úrovně Standard podporuje pouze standardní veřejnou IP adresu. Při vytváření Load Balanceru úrovně Standard musíte vytvořit také novou standardní veřejnou IP adresu nakonfigurovanou jako jeho front-end (ve výchozím nastavení má název *LoadBalancerFrontend*). 

1. On the top left-hand side of the screen, select **Create a resource** > **Networking** > **Load Balancer**.
2. In the **Basics** tab of the **Create load balancer** page, enter or select the following information, accept the defaults for the remaining settings, and then select **Review + create**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Select **Create new** and type *myResourceGroupSLB* in the text box.|
    | Name (Název)                   | *myLoadBalancer*                                   |
    | Oblast         | Vyberte **Západní Evropa**.                                        |
    | Typ          | Select **Public**.                                        |
    | Skladová položka           | Select **Standard**.                          |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. |
    | Public IP address name              | Type *myPublicIP* in the text box.   |
    |Availability zone| Select **Zone redundant**.    |
3. In the **Review + create** tab, select **Create**.   

    ![Vytvoření Load Balanceru úrovně Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Create Load Balancer resources

In this section, you configure Load Balancer settings for a backend address pool, a health probe, and specify a balancer rule.

### <a name="create-a-backend-address-pool"></a>Vytvoření fondu back-endových adres

To distribute traffic to the VMs, a backend address pool contains the IP addresses of the virtual (NICs) connected to the Load Balancer. Create the backend address pool *myBackendPool* to include virtual machines for load-balancing internet traffic.

1. Select **All services** in the left-hand menu, select **All resources**, and then select **myLoadBalancer** from the resources list.
2. Under **Settings**, select **Backend pools**, then select **Add**.
3. On the **Add a backend pool** page, for name, type *myBackendPool*, as the name for your backend pool, and then select **Add**.

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

To allow the Load Balancer to monitor the status of your app, you use a health probe. The health probe dynamically adds or removes VMs from the Load Balancer rotation based on their response to health checks. Vytvořte sondu stavu *myHealthProbe* pro monitorování stavu virtuálních počítačů.

1. Select **All services** in the left-hand menu, select **All resources**, and then select **myLoadBalancer** from the resources list.
2. Under **Settings**, select **Health probes**, then select **Add**.
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Enter *myHealthProbe*. |
    | Protocol (Protokol) | Select **HTTP**. |
    | Port | Enter *80*.|
    | Interval | Enter *15* for number of **Interval** in seconds between probe attempts. |
    | Unhealthy threshold | Select **2** for number of **Unhealthy threshold** or consecutive probe failures that must occur before a VM is considered unhealthy.|
    | | |
4. Vyberte **OK**.

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla Load Balanceru
Pravidlo Load Balanceru slouží k definování způsobu distribuce provozu do virtuálních počítačů. Nadefinujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Create a Load Balancer rule *myLoadBalancerRuleWeb* for listening to port 80 in the frontend *FrontendLoadBalancer* and sending load-balanced network traffic to the backend address pool *myBackEndPool* also using port 80. 

1. Select **All services** in the left-hand menu, select **All resources**, and then select **myLoadBalancer** from the resources list.
2. Under **Settings**, select **Load balancing rules**, then select **Add**.
3. Ke konfiguraci pravidla vyrovnávání zatížení použijte tyto hodnoty:
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Enter *myHTTPRule*. |
    | Protocol (Protokol) | Select **TCP**. |
    | Port | Enter *80*.|
    | Backend port | Enter *80*. |
    | Backend pool | Select *myBackendPool*.|
    | Sonda stavu | Select *myHealthProbe*. |
4. Leave the rest of the defaults and then select **OK**.


## <a name="create-backend-servers"></a>Vytvoření serverů back-end

In this section, you create a virtual network, create three virtual machines for the backend pool of the Load Balancer, and then install IIS on the virtual machines to help test the Load Balancer.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
1. On the upper-left side of the screen, select **Create a resource** > **Networking** > **Virtual network**.

1. In **Create virtual network**, enter or select this information:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte *myVNet*. |
    | Adresní prostor | Enter *10.1.0.0/16*. |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Select existing resource - *myResourceGroupSLB*. |
    | Umístění | Vyberte **Západní Evropa**.|
    | Subnet - Name | Zadejte *myBackendSubnet*. |
    | Podsíť – Rozsah adres | Enter *10.1.0.0/24*. |
1. Leave the rest of the defaults and select **Create**.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů
Standard Load Balancer only supports VMs with Standard IP addresses in the backend pool. In this section, you will create three VMs (*myVM1*, *myVM2* and *myVM3*) with a Standard public IP address in three different zones (*Zone 1*, *Zone 2*, and *Zone 3*) that are later added to the backend pool of the Standard Load Balancer that was created earlier.

1. On the upper-left side of the portal, select **Create a resource** > **Compute** > **Windows Server 2019 Datacenter**. 
   
1. In **Create a virtual machine**, type or select the following values in the **Basics** tab:
   - **Subscription** > **Resource Group**: Select **myResourceGroupSLB**.
   - **Instance Details** > **Virtual machine name**: Type *myVM1*.
   - **Instance Details** > **Region** > select **West Europe**.
   - **Instance Details** > **Availability Options** > Select **Availability zones**. 
   - **Instance Details** > **Availability zone** > Select **1**.
   - **Administrator account**> Enter the **Username**, **Password** and **Confirm password** information.
   - Select the **Networking** tab, or select **Next: Disks**, then **Next: Networking**.
  
1. In the **Networking** tab make sure the following are selected:
   - **Virtual network**: *myVnet*
   - **Subnet**: *myBackendSubnet*
   - **Public IP** > select **Create new**, and in the **Create public IP address** window, for **SKU**, select **Standard**, and for **Availability zone**, select **Zone-redundant**, and then select **OK**.
   - To create a new network security group (NSG), a type of firewall, under **Network Security Group**, select **Advanced**. 
       1. In the **Configure network security group** field, select **Create new**. 
       1. Type *myNetworkSecurityGroup*, and select **OK**.
   - To make the VM a part of the Load Balancer's backend pool, complete the following steps:
        - In **Load Balancing**, for **Place this virtual machine behind an existing load balancing solution?** , select **Yes**.
        - In **Load balancing settings**, for **Load balancing options**, select **Azure load balancer**.
        - For **Select a load balancer**, *myLoadBalancer*.
        - Select the **Management** tab, or select **Next** > **Management**.
2. In the **Management** tab, under **Monitoring**, set **Boot diagnostics** to **Off**. 
1. Vyberte **Zkontrolovat a vytvořit**.   
1. Review the settings, and then select **Create**.
1. Follow the steps 2 to 6 to create two additional VMs with the following values and all the other settings the same as *myVM1*:

    | Nastavení | VM 2| VM 3|
    | ------- | ----- |---|
    | Name (Název) |  *myVM2* |*myVM3*|
    | Availability zone | 2 |3|
    |Veřejná IP adresa| **Standard** SKU|**Standard** SKU|
    | Public IP - Availability zone| **Zone redundant** |**Zone redundant**|
    | Skupina zabezpečení sítě | Select the existing *myNetworkSecurity Group*| Select the existing *myNetworkSecurity Group*|

 ### <a name="create-nsg-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

In this section, you create a network security group rule to allow inbound connections using HTTP.

1. Select **All services** in the left-hand menu, select **All resources**, and then from the resources list select **myNetworkSecurityGroup** that is located in the **myResourceGroupSLB** resource group.
2. V části **Nastavení** vyberte **Příchozí pravidla zabezpečení** a potom vyberte **Přidat**.
3. Zadáním následujících hodnot pro příchozí pravidlo zabezpečení *myHTTPRule* povolte příchozí připojení HTTP na portu 80:
    - Jako **Zdroj** zadejte *Značka služby*.
    - Jako **Značka zdrojové služby** zadejte *Internet*.
    - Jako **Rozsahy cílových portů** zadejte *80*.
    - Jako **Protokol** zadejte *TCP*.
    - Jako **Akce** zadejte *Povolit*.
    - Jako **Priorita** zadejte *100*.
    - Jako název zadejte *myHTTPRule*.
    - Jako popis zadejte *Povolení protokolu HTTP*.
4. Vyberte **Přidat**.
 
### <a name="install-iis"></a>Instalace služby IIS

1. Select **All services** in the left-hand menu, select **All resources**, and then from the resources list, select **myVM1** that is located in the *myResourceGroupSLB* resource group.
2. Na stránce **Přehled** vyberte **Připojit** a připojte se přes RDP k virtuálnímu počítači.
5. Přihlaste se k virtuálnímu počítači pomocí přihlašovacích údajů, které jste zadali při vytváření tohoto virtuálního počítače. Tím se otevře relace vzdálené plochy s virtuálním počítačem *myVM1*.
6. Na ploše serveru přejděte do části **Nástroje pro správu Windows**>**Windows PowerShell**.
7. V okně PowerShellu spuštěním následujících příkazů nainstalujte server služby IIS, odeberte výchozí soubor iisstart.htm a pak přidejte nový soubor iisstart.htm, který zobrazuje název virtuálního počítače:

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Ukončete relaci RDP s *myVM1*.
7. Opakováním kroků 1 až 6 nainstalujte službu IIS a aktualizovaný soubor iisstart.htm na *myVM2* a *myVM3*.

## <a name="test-the-load-balancer"></a>Test the Load Balancer
1. Na obrazovce **Přehled** vyhledejte veřejnou IP adresu Load Balanceru. Select **All services** in the left-hand menu, select **All resources**, and then select **myPublicIP**.

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

   ![Webový server služby IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

To see the Load Balancer distribute traffic across all three VMs, you can customize the default page of each VM's IIS Web server and then force-refresh your web browser from the client machine.

## <a name="clean-up-resources"></a>Vyčištění prostředků

When no longer needed, delete the resource group, Load Balancer, and all related resources. To do so, select the resource group (*myResourceGroupSLB*) that contains the Load Balancer, and then select **Delete**.

## <a name="next-steps"></a>Další kroky

In this quickstart, you created a Standard Load Balancer, attached VMs to it, configured the Load Balancer traffic rule, health probe, and then tested the Load Balancer. Chcete-li zjistit další informace o službě Azure Load Balancer, přejděte ke kurzům pro Azure Load Balancer.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
