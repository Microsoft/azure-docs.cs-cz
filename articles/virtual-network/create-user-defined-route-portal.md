---
title: "Vytvoření uživatelem definované trasy pro směrování síťového provozu prostřednictvím virtuálního zařízení sítě - portálu Azure | Microsoft Docs"
description: "Naučte se vytvářet trasy definované uživatelem přepsat výchozí Azure směrování podle směrování síťového provozu přes virtuální síťové zařízení."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: 0319029277091611673f15c94604604850cbfcbe
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2017
---
# <a name="create-a-user-defined-route---azure-portal"></a>Vytvoření trasy definované uživatelem – portál Azure

V tomto kurzu, Naučte se vytvářet trasy definované uživatelem směrovat provoz mezi dvěma [virtuální sítě](virtual-networks-overview.md) podsítě prostřednictvím sítě virtuálního zařízení. Virtuální zařízení sítě je virtuální počítač, který spouští síťové aplikace, jako je například Brána firewall. Další informace o předem nakonfigurovaná síťová virtuální zařízení, které můžete nasadit v virtuální síť Azure, najdete v článku [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

Když vytvoříte podsítě ve virtuální síti, Azure vytvoří výchozí [systémové trasy](virtual-networks-udr-overview.md#system-routes) prostředky ve všech podsítích ke komunikaci mezi sebou, které umožňují, jak je znázorněno na následujícím obrázku:

![Výchozí trasy](./media/create-user-defined-route/default-routes.png)

V tomto kurzu vytvoříte virtuální síť s veřejné, privátní a podsítě DMZ, jak je znázorněno na obrázku, který následuje dále. Obvykle je možno nasadit webové servery do veřejného podsítě a podsíť privátní možno nasadit aplikace nebo databázový server. Vytvoření virtuálního počítače tak, aby fungoval jako virtuální zařízení sítě v podsíti DMZ a volitelně můžete vytvořit virtuální počítač v každé podsíti, které komunikují prostřednictvím sítě virtuální zařízení. Veškerý provoz mezi podsítěmi veřejné a privátní je směrován přes zařízení, jak je znázorněno na následujícím obrázku:

![Trasy definované uživatelem](./media/create-user-defined-route/user-defined-routes.png)

Tento článek obsahuje kroky k vytvoření trasy definované uživatelem prostřednictvím modelu nasazení Resource Manager, který je model nasazení, které vám doporučujeme používat při vytváření trasy definované uživatelem. Pokud potřebujete vytvořit trasy definované uživatelem (klasické), přečtěte si téma [vytvořit trasy definované uživatelem (klasické)](virtual-network-create-udr-classic-ps.md). Pokud si nejste obeznámeni s modelech nasazení Azure, najdete v části [modelech nasazení Azure pochopit](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Další informace o trasy definované uživatelem, najdete v části [trasy definované uživatelem přehled](virtual-networks-udr-overview.md#user-defined).

## <a name="create-routes-and-network-virtual-appliance"></a>Vytvoření trasy a virtuální síťové zařízení

1. **Požadovaný**: vytvoření virtuální sítě se dvěma podsítěmi pomocí kroků v [vytvořit virtuální síť](#create-a-virtual-network).
2. Po vytvoření virtuální sítě v internetovém prohlížeči přejděte na [portál Azure](https://portal.azure.com). Přihlaste se pomocí vaší [účet Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account).
3. Na **vyhledávání prostředků** pole v horní části portálu, zadejte *myResourceGroup*. Klikněte na tlačítko **myResourceGroup** při zobrazí ve výsledcích hledání. Skupina prostředků byla vytvořena v rámci požadované součásti.
4. Klikněte na tlačítko **myVnet**, jak je znázorněno na následujícím obrázku:

    ![Nastavení síťového rozhraní](./media/create-user-defined-route/virtual-network.png)

5. Vytvořte podsíť pro virtuální síťové zařízení:
 
    - V části **myVnet**, klikněte na tlačítko **podsítě** pod **nastavení** na levé straně.
    - Klikněte na tlačítko **+ podsítě**, jak je znázorněno na následujícím obrázku:

        ![Podsítě](./media/create-user-defined-route/subnets.png) 
    - Zadejte následující hodnoty v části **přidat podsíť**, pak klikněte na tlačítko **OK**:

        |Nastavení|Hodnota|
        |-----|-----|
        |Name (Název)|DMZ|
        |Rozsah adres (blok CIDR)|10.0.2.0/24|

6. Vytvoření virtuálního počítače virtuální zařízení sítě:

    - Na levé straně na portálu, klikněte na tlačítko **+ nový**, pak klikněte na tlačítko **výpočetní**a potom klikněte na **Windows Server 2016 Datacenter** nebo **Ubuntu Server 16.04 LTS**.
    - Zadejte následující hodnoty **Základy** okno, které se zobrazí, pak klikněte na tlačítko **OK**.

        |Nastavení|Hodnota|
        |---|---|
        |Name (Název)|Můjvp hodnocení chyb zabezpečení|
        |Uživatelské jméno|azureuser|
        |Heslo a potvrzení hesla|Heslo dle vlastního výběru|
        |Předplatné|Vyberte předplatné|
        |Skupina prostředků|Klikněte na tlačítko **použít existující**, pak vyberte **myResourceGroup**|
        |Umístění|Východ USA|
    - Na **zvolte velikost** okno, které se zobrazí, klikněte na tlačítko **DS1_V2 standardní**, klikněte **vyberte**.
    - Na **nastavení** okno, které se zobrazí, klikněte na tlačítko **virtuální síť**. Klikněte na tlačítko **myVnet** v **zvolte virtuální síť** okno, které se zobrazí.
    - Na **nastavení** okně klikněte na tlačítko **podsítě**. Klikněte na tlačítko **DMZ** na **zvolte podsíť** okno, které se zobrazí. 
    - Ponechte výchozí nastavení pro zbývající nastavení a klikněte na tlačítko **OK**.
    - Klikněte na tlačítko **vytvořit** na **vytvořit** okno, které se zobrazí. Nasazení virtuálního počítače trvá několik minut.

    > [!NOTE]
    > Kromě vytvoření virtuálního počítače, na portálu Azure vytvoří veřejnou IP adresu a přiřadí ji k virtuálnímu počítači, ve výchozím nastavení. Pokud byly nasazení virtuálního počítače v produkčním prostředí, můžete se rozhodnout nechcete přiřadit veřejnou IP adresu virtuálního počítače. Virtuální zařízení sítě může místo toho přístup z jiných virtuálních počítačů v rámci virtuální sítě. Další informace o veřejné IP adresy, najdete v části [spravovat veřejnou IP adresu](virtual-network-public-ip-address.md).

7. Přiřadit statickou privátní IP adresou a povolení předávání protokolu IP pro síťové rozhraní na portálu vytvořené v předchozím kroku. 
    - Na **vyhledávání prostředků** pole v horní části stránky, zadejte *Můjvp hodnocení chyb zabezpečení*.
    - Klikněte na tlačítko **Můjvp hodnocení chyb zabezpečení** při zobrazí ve výsledcích hledání.
    - Klikněte na tlačítko **sítě** pod **nastavení** na levé straně.
    - Klikněte na název síťového rozhraní v části **Můjvp hodnocení chyb zabezpečení – síťová rozhraní**. Název je **můjvp hodnocení chyb zabezpečení***X*, kde *X* je číslo přiřazené portálem.
    - Klikněte na tlačítko **konfigurace protokolu IP** pod **nastavení** pro síťové rozhraní, jak je znázorněno na následujícím obrázku:

        ![Nastavení síťového rozhraní](./media/create-user-defined-route/network-interface-settings.png)
        
    - Klikněte na tlačítko **povoleno** pro **předávání IP** nastavení, pak klikněte na tlačítko **Uložit**. Předávání IP musí být povolen pro každé síťové rozhraní, který přijímá provoz není adresovaný přiřazené k němu IP adresy. Povolení předávání IP zakáže kontrola zdroj nebo cíl Azure pro síťové rozhraní.
    - Klikněte na tlačítko **ipconfig1** v seznamu konfigurace protokolu IP.
    - Klikněte na tlačítko **statické** pro **přiřazení** privátní IP adresy v rámci **ipconfig1**, jak je znázorněno na následujícím obrázku:

        ![Konfigurace protokolu IP](./media/create-user-defined-route/ip-configuration.png)
    - Jak je vidět na předchozím obrázku, zadejte *10.0.2.4* pod **IP adresu** v **nastavení privátní IP adresy**. Přiřazení statické IP adresy pro síťové rozhraní zajistí, že adresa nemění po celou dobu životnosti virtuálnímu počítači, který síťové rozhraní je připojen k. Zadaná adresa není aktuálně přiřazen jiný prostředek v podsíti DMZ, která síťové rozhraní je v. 
    - Chcete-li uložit konfiguraci, klikněte na tlačítko **Uložit** pod **ipconfig1**. Neuzavírejte pole ipconfig1, dokud byste obdržet oznámení na portálu, který je uložený v síťové rozhraní.
 
8. Vytvořte dva směrovací tabulky. Směrovací tabulky obsahovat nula nebo víc tras:

    - Na levé straně na portálu, klikněte na tlačítko **+ nový** > **sítě** > **směrovací tabulku**.
    - Zadejte následující hodnoty v části **vytvořit směrovací tabulku**a potom klikněte na **vytvořit**:

        |Nastavení|Hodnota|
        |---|---|
        |Name (Název)|Veřejné myRouteTable|
        |Předplatné|Vyberte předplatné|
        |Skupina prostředků|Vyberte **použít existující**, pak vyberte **myResourceGroup**|
        |Umístění|Východ USA|
    
    - Dokončení předchozí dílčích kroků kroku 8 znovu, ale název směrovací tabulka *myRouteTable privátní*.
9. Přidat trasy k *myRouteTable veřejné* směrovací tabulky a přidružit směrovací tabulku, aby *veřejné* podsítě:

    - Na **vyhledávání prostředků** pole v horní části portálu, zadejte *myRouteTable veřejné*. Klikněte na tlačítko **myRouteTable veřejné** při zobrazí ve výsledcích hledání.
    - V části **myRouteTable veřejné**, klikněte na tlačítko **trasy** v seznamu **nastavení**.
    - Klikněte na tlačítko **+ přidat** pod **myRouteTable veřejný - trasy**.
    -  Ve výchozím nastavení Azure směrování provozu mezi všech podsítí v rámci virtuální sítě. Vytvořte trasu k změnit výchozí nastavení Azure a směrování, která provoz z *veřejné* podsíť směrován přes hodnocení chyb zabezpečení, místo přímo na *privátní* podsítě. Zadejte následující hodnoty **přidat trasy** okno, které se zobrazí a pak klikněte na tlačítko **OK**:

        |Nastavení|Hodnota|Vysvětlení|
        |---|---|---|
        |Název trasy|ToPrivateSubnet|Tato trasa přesměruje přenosy na privátní podsítě přes virtuální síťové zařízení.|
        |Předpona adresy|10.0.1.0/24| Všechny přenosy odesílané do všechny adresy v rámci této předpona adresy (10.0.1.0 – 10.0.1.254) posílá virtuální síťové zařízení.|
        |Typ dalšího segmentu| Vyberte **virtuální zařízení**||
        |Adresa dalšího segmentu|10.0.2.4| Statickou privátní IP adresa síťového rozhraní připojené k síti virtuální zařízení. Je pouze typ přechodu, můžete zadat adresu pro **virtuální zařízení**.|

    - V části **myRouteTable veřejné**, klikněte na tlačítko **podsítě** pod **nastavení**. 
    - Klikněte na tlačítko **+ přidružit** pod **myRouteTable veřejný - podsítě**.
    - Klikněte na tlačítko **virtuální síť** pod **přidružení podsítě**, pak klikněte na tlačítko **myVnet**.
    - Klikněte na tlačítko **podsíť** v části **přidružení podsítě**, pak klikněte na tlačítko **veřejné** pod **zvolte podsíť**. 
    - Chcete-li uložit konfiguraci, klikněte na tlačítko **OK** pod **přidružení podsítě**. Podsíť může mít žádnou nebo jednu směrovací tabulku přidružené k němu.
10. Dokončení kroku 9 znovu, hledání **myRouteTable privátní**, vytváření trasu s následujícím nastavením a pak přiřazení směrovací tabulku, aby **privátní** podsíť **myVnet** virtuální sítě:

    |Nastavení|Hodnota|Vysvětlení|
    |---|---|---|
    |Název trasy|ToPublicSubnet|Tato trasa přesměruje přenosy na veřejné podsíť přes virtuální síťové zařízení.|
    |Předpona adresy|10.0.0.0/24| Všechny přenosy odesílané do všechny adresy v rámci této předpona adresy (10.0.0.0 – 10.0.1.254) posílá virtuální síťové zařízení.|
    |Typ dalšího segmentu| Vyberte **virtuální zařízení**||
    |Adresa dalšího segmentu|10.0.2.4||

    Síťový provoz mezi všechny prostředky v privátní a veřejné podsítě prochází virtuální síťové zařízení. 
11. **Volitelné:** vytvoření virtuálního počítače ve veřejných a privátních podsítích a ověřte, že komunikace mezi virtuálními počítači je směrován přes virtuální síťové zařízení pomocí kroků v [ověření směrování](#validate-routing). 
12. **Volitelné**: Odstraňte prostředky, které vytvoříte v tomto kurzu, pomocí kroků v [odstranit prostředky](#delete-resources).

## <a name="validate-routing"></a>Ověření směrování

1. Pokud jste to ještě neudělali, proveďte kroky v [vytvořit trasy a virtuální síťové zařízení](#create-routes-and-network-virtual-appliance).
2. Klikněte **vyzkoušet** tlačítko pole, která následuje, otevře se prostředí cloudové služby Azure. Pokud budete vyzváni, přihlaste se k Azure pomocí vaší [účet Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Pokud účet Azure nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/offers/ms-azr-0044p). Prostředí cloudové služby Azure je prostředí bash volné pomocí rozhraní příkazového řádku Azure předinstalována. 

    Vytvořte dva virtuální počítače, jeden v následujících skriptů *veřejné* podsítě a po jednom v *privátní* podsíť. Skripty povolit taky předávání protokolu IP pro rozhraní sítě v rámci operačního systému hodnocení chyb zabezpečení, aby operační systém pro směrování provozu přes rozhraní sítě. Provozní hodnocení chyb zabezpečení sítě obvykle zkontroluje provoz před směrování, ale v tomto kurzu jednoduché hodnocení chyb zabezpečení právě směruje provoz bez kontroly ho. 

    Klikněte na tlačítko **kopie** v tlačítko **Linux** nebo **Windows** skripty, které se řídí a vložte obsah skriptu do textového editoru. Změňte heslo pro *adminPassword* proměnné a potom vložte skript do prostředí cloudu Azure. Spusťte skript pro operační systém, který jste vybrali, když jste vytvořili v kroku 6 v virtuální zařízení sítě [vytvořit trasy a virtuální síťové zařízení](#create-routes-and-network-virtual-appliance). 

    **Linux**

    ```azurecli-interactive
    #!/bin/bash

    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.    
    az vm extension set \
      --resource-group $rgName \
      --vm-name myVm-Nva \
      --name customScript \
      --publisher Microsoft.Azure.Extensions \
      --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
    ```

    **Windows**

    ```azurecli-interactive

    #!/bin/bash
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Public \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Private \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1"}'

    # Restart the NVA virtual machine.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Restart-Computer -ComputerName myVm-Nva -Force"}'
    ```

3. Ověření komunikace mezi virtuálními počítači ve veřejných a privátních podsítích. 

    - Otevřete [SSH](../virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Linux) nebo [vzdálené plochy](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) připojení (Windows) na veřejnou IP adresu *Můjvp veřejné* virtuálního počítače.
    - Z příkazového řádku na *Můjvp veřejné* virtuální počítač, zadejte `ping myVm-Private`. Obdržíte odpovědi, protože hodnocení chyb zabezpečení směruje provoz z veřejnosti privátní podsítě.
    - Z *Můjvp veřejné* virtuálního počítače, spusťte trasování cesty mezi virtuálními počítači v veřejné a privátní podsítě. Zadejte příslušný příkaz, který následuje, podle toho, jaký operační systém nainstalovaný v virtuální počítače ve veřejných a privátních podsítě:
        - **Windows**: Z příkazového řádku, spusťte `tracert myvm-private` příkaz.
        - **Ubuntu**: spuštění `tracepath myvm-private` příkaz.
      Provoz prochází přes 10.0.2.4 (hodnocení chyb zabezpečení) dříve, než dorazila 10.0.1.4 (virtuální počítač v privátní podsítě). 
    - Dokončit předchozí kroky připojením k *Můjvp privátní* virtuální počítač a otestováním *Můjvp veřejné* virtuálního počítače. Trasování cesty ukazuje komunikace cestě prostřednictvím 10.0.2.4 dříve, než dorazila 10.0.0.4 (virtuálního počítače v podsíti veřejný).

      > [!NOTE]
      > Předchozí kroky umožňují potvrďte směrování mezi Azure privátních IP adres. Pokud chcete předat dál nebo proxy server, provoz na veřejné IP adresy prostřednictvím sítě virtuálního zařízení:
      > - Zařízení musí poskytovat překlad síťových adres nebo funkce proxy serveru. Pokud překlad síťových adres, zařízení musí překládat zdrojové IP adresy do svého vlastního a pak je odeslat tuto žádost o veřejnou IP adresu. Jestli má zařízení Síťová adresa přeložit zdrojovou adresu, nebo je připojení přes proxy server, Azure překládá virtuální zařízení sítě privátní IP adresu na veřejnou IP adresu. Další informace o různých metodách Azure používá překládali soukromé IP adresy na veřejné IP adresy najdete v tématu [pochopení odchozí připojení](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
      > - Další trasu ve směrovací tabulce, například předpona: 0.0.0.0/0, typ dalšího směrování VirtualAppliance a další směrování IP adres 10.0.2.4 (v předchozím příkladu skriptu).
      >
    - **Volitelně**: K ověření dalšího směrování mezi dvěma virtuálními počítači v rámci Azure, použijte další směrování schopnosti produktu sledovací proces sítě Azure. Před použitím sledovací proces sítě, musíte nejdřív [vytvoření instance sledovací proces sítě Azure](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro oblast, kterou chcete použít v. V tomto kurzu se používá oblasti USA – východ. Po povolení instance sledovací proces sítě pro oblast, zadejte následující příkaz, který zobrazit další informace o směrování mezi virtuálními počítači v veřejné a privátní podsítě:
     
        ```azurecli-interactive
        az network watcher show-next-hop --resource-group myResourceGroup --vm myVm-Public --source-ip 10.0.0.4 --dest-ip 10.0.1.4
        ```

       Vrátí *10.0.2.4* jako **nextHopIpAddress** a *VirtualAppliance* jako **nextHopType**. 

> [!NOTE]
> K objasnění konceptů v tomto kurzu, veřejné IP adresy přiřazené k virtuálních počítačů v veřejné a privátní podsítě a všechny port přístup k síti je povolena v rámci Azure pro virtuální počítače. Při vytváření virtuálních počítačů pro použití v provozním prostředí, nemusí k nim přiřadíte veřejné IP adresy a filtrovat síťový provoz do privátní podsítě virtuální síťové zařízení před jeho nasazením nebo přiřazením skupinu zabezpečení sítě k podsítím, síťové rozhraní, nebo obojí. Další informace o skupinách zabezpečení sítě najdete v tématu [skupin zabezpečení sítě](virtual-networks-nsg.md).

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Tento kurz vyžaduje existující virtuální síť se dvěma podsítěmi. Klikněte **vyzkoušet** tlačítka na pole, která odpovídá rychle vytvořit virtuální síť. Kliknutím **vyzkoušet** tlačítko otevře [prostředí cloudu Azure](../cloud-shell/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). I když cloudové prostředí běží PowerShell nebo prostředí Bash v této části, prostředí Bash se používá k vytvoření virtuální sítě. Prostředí Bash má nainstalované rozhraní příkazového řádku Azure. Pokud se zobrazí výzva cloudové prostředí, přihlaste se k Azure pomocí vaší [účet Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Pokud účet Azure nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/offers/ms-azr-0044p). Chcete-li vytvořit virtuální síť použít v tomto kurzu, klikněte na tlačítko **kopie** tlačítko do následujícího okna a potom vložte skript do prostředí cloudu Azure:

```azurecli-interactive
#!/bin/bash

#Set variables used in the script.
rgName="myResourceGroup"
location="eastus"

# Create a resource group.
az group create \
  --name $rgName \
  --location $location

# Create a virtual network with one subnet named Public.
az network vnet create \
  --name myVnet \
  --resource-group $rgName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24

# Create an additional subnet named Private in the virtual network.
az network vnet subnet create \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --vnet-name myVnet \
  --resource-group $rgName
```

Další informace o tom, jak vytvořit virtuální síť pomocí portálu, prostředí PowerShell nebo šablonu Azure Resource Manager najdete v tématu [vytvořit virtuální síť](virtual-networks-create-vnet-arm-pportal.md).

## <a name="delete-resources"></a>Odstraňte prostředky

Po dokončení tohoto kurzu můžete chtít odstranit prostředky, které jste vytvořili, tak, aby vám zbytečně nenabíhaly poplatky za používání. Odstranění skupiny prostředků se také odstraní všechny prostředky, které jsou ve skupině prostředků. V portálu otevřené proveďte následující kroky:

1. V dialogovém okně hledání portálu zadejte **myResourceGroup**. Ve výsledcích hledání klikněte na tlačítko **myResourceGroup**.
2. Na **myResourceGroup** okně klikněte **odstranit** ikonu.
3. Potvrďte odstranění, v **název skupiny prostředků typu** zadejte **myResourceGroup**a potom klikněte na **odstranit**.

## <a name="next-steps"></a>Další kroky

- Vytvoření [vysoce dostupném síťovém virtuální zařízení](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuální síťová zařízení mají často víc síťových rozhraní a IP adresy, které jsou jim přiřazeny. Zjistěte, jak [síťových rozhraní přidat do existujícího virtuálního počítače](virtual-network-network-interface-vm.md#vm-add-nic) a [přidání IP adres do existujícího síťového rozhraní](virtual-network-network-interface-addresses.md#add-ip-addresses). Ačkoli všechny velikosti virtuálních počítačů může mít aspoň dvě rozhraní sítě připojené k nim, každý velikost virtuálního počítače podporuje maximální počet síťových rozhraní. Informace o tom, kolik síťových rozhraní se každý virtuální počítač velikost podporuje, najdete v části [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) a [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) velikostí virtuálních počítačů. 
- Vytvořit uživatelem definované trasy k vynucení tunelu provoz místní prostřednictvím [připojení site-to-site VPN](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
