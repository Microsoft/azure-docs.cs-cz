---
title: 'Rychlý Start: vytvoření Standard Load Balancer-Azure Portal'
titlesuffix: Azure Load Balancer
description: V tomto rychlém startu se dozvíte, jak vytvořit Standard Load Balancer pomocí Azure Portal.
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
ms.openlocfilehash: c8df0daac25a79bbbd67577c30b0a2da62d037da
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273832"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Rychlý start: Vytvoření Load Balanceru úrovně Standard pro vyrovnávání zatížení virtuálních počítačů pomocí webu Azure Portal

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky na více virtuálních počítačů. K vytvoření nástroje pro vyrovnávání zatížení virtuálních počítačů můžete použít web Azure Portal. V tomto rychlém startu se dozvíte, jak vyrovnávat zatížení virtuálních počítačů pomocí Load Balanceru úrovně Standard.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Vytvoření Load Balanceru úrovně Standard

V této části vytvoříte Standard Load Balancer, která pomáhá vyrovnávat zatížení virtuálních počítačů. Load Balancer úrovně Standard podporuje pouze standardní veřejnou IP adresu. Při vytváření Load Balanceru úrovně Standard musíte vytvořit také novou standardní veřejnou IP adresu nakonfigurovanou jako jeho front-end (ve výchozím nastavení má název *LoadBalancerFrontend*). 

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek** > **síť** > **Load Balancer**.
2. Na kartě **základy** na stránce vytvořit službu Vyrovnávání **zatížení** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Subscription               | Vyberte své předplatné.    |    
    | Resource group         | Vyberte **vytvořit nový** a do textového pole zadejte *myResourceGroupSLB* .|
    | Name                   | *myLoadBalancer*                                   |
    | Oblast         | Vyberte **Západní Evropa**.                                        |
    | type          | Vyberte možnost **veřejné**.                                        |
    | SKU           | Vyberte **Standard**.                          |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. |
    | Název veřejné IP adresy              | Do textového pole zadejte *myPublicIP* .   |
    |Zóna dostupnosti| Vyberte **zóna redundantní**.    |
3. Na kartě **Revize + vytvořit** vyberte **vytvořit**.   

    ![Vytvoření Load Balanceru úrovně Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Vytvoření prostředků Load Balancer

V této části nakonfigurujete Load Balancer nastavení pro fond back-end adres, sondu stavu a zadáváte pravidlo pro vyrovnávání zatížení.

### <a name="create-a-backend-address-pool"></a>Vytvoření fondu back-endových adres

Aby bylo možné distribuovat provoz do virtuálních počítačů, fond adres back-endu obsahuje IP adresy virtuálních (síťových rozhraní) připojených k Load Balancer. Vytvořte fond back-end adres *myBackendPool* , který bude zahrnovat virtuální počítače pro internetovou komunikaci s vyrovnáváním zatížení.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myLoadBalancer** .
2. V části **Nastavení**vyberte **back-end fondy**a pak vyberte **Přidat**.
3. Do pole název na stránce **Přidat fond back-end** serveru zadejte *myBackendPool*jako název vašeho back-end fondu a pak vyberte **Přidat**.

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Pokud chcete Load Balancer, aby mohl monitorovat stav vaší aplikace, použijte sondu stavu. Sonda stavu dynamicky přidává nebo odebírá virtuální počítače z Load Balancer rotace na základě jejich reakcí na kontroly stavu. Vytvořte sondu stavu *myHealthProbe* pro monitorování stavu virtuálních počítačů.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myLoadBalancer** .
2. V části **Nastavení**vyberte **sondy stavu**a pak vyberte **Přidat**.
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name | Zadejte *myHealthProbe*. |
    | Protocol | Vyberte **http**. |
    | Port | Zadejte *80*.|
    | Interval | Zadejte hodnotu *15* pro **interval** mezi pokusy o sondu v sekundách. |
    | Prahová hodnota špatného stavu | Vyberte **2** pro počet chybných **prahových hodnot** nebo po sobě jdoucích selhání sondy, ke kterým musí dojít, aby se virtuální počítač považoval za poškozený.|
    | | |
4. Vyberte **OK**.

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla Load Balanceru
Pravidlo Load Balanceru slouží k definování způsobu distribuce provozu do virtuálních počítačů. Definujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Vytvořte pravidlo Load Balancer *myLoadBalancerRuleWeb* pro naslouchání na portu 80 ve front-endu *FrontendLoadBalancer* a odesílání síťového provozu s vyrovnáváním zatížení do fondu back-end adres *myBackEndPool* také pomocí portu 80. 

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myLoadBalancer** .
2. V části **Nastavení**vyberte **pravidla vyrovnávání zatížení**a pak vyberte **Přidat**.
3. Ke konfiguraci pravidla vyrovnávání zatížení použijte tyto hodnoty:
    
    | Nastavení | Value |
    | ------- | ----- |
    | Name | Zadejte *myHTTPRule*. |
    | Protocol | Vyberte **TCP**. |
    | Port | Zadejte *80*.|
    | Port back-endu | Zadejte *80*. |
    | Back-end fond | Vyberte *myBackendPool*.|
    | Sonda stavu | Vyberte *myHealthProbe*. |
4. Ponechte zbytek výchozích hodnot a pak vyberte **OK**.


## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části vytvoříte virtuální síť, vytvoříte tři virtuální počítače pro back-end fond Load Balancer a pak na virtuální počítače nainstalujete službu IIS, která vám pomůžou otestovat Load Balancer.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
1. V levé horní části obrazovky vyberte **vytvořit prostředek** > **síť** > **virtuální síť**.

1. V nástroji **vytvořit virtuální síť**zadejte nebo vyberte tyto informace:

    | Nastavení | Value |
    | ------- | ----- |
    | Name | Zadejte *myVNet*. |
    | Adresní prostor | Zadejte *10.1.0.0/16*. |
    | Subscription | Vyberte své předplatné.|
    | Resource group | Vyberte existující prostředek – *myResourceGroupSLB*. |
    | Location | Vyberte **Západní Evropa**.|
    | Název podsítě | Zadejte *myBackendSubnet*. |
    | Podsíť – Rozsah adres | Zadejte *10.1.0.0/24*. |
1. Ponechte zbytek výchozích hodnot a vyberte **vytvořit**.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů
Standard Load Balancer podporuje jenom virtuální počítače se standardními IP adresami ve fondu back-end. V této části vytvoříte tři virtuální počítače (*myVM1*, *myVM2* a *MYVM3*) se standardní veřejnou IP adresou ve třech různých zónách (*zóna 1*, *zóna 2*a *zóna 3*), které jsou později přidané do back-endu fondu Standard Load Balancer, která byla vytvořena dříve.

1. V levé horní části portálu vyberte **vytvořit prostředek** > **COMPUTE** > **Windows Server 2019 Datacenter**. 
   
1. V **vytvoření virtuálního počítače**, zadejte nebo vyberte následující hodnoty **Základy** kartu:
   -  > **Skupina prostředků**předplatného: Vyberte **myResourceGroupSLB**.
   - Podrobnosti > instance**název virtuálního počítače**: Zadejte *myVM1*.
   -  > **Oblast** podrobností instance > vybrat **západní Evropa**.
   -  > **Možnosti dostupnosti** podrobností instance > vybrat **zóny dostupnosti**. 
   -  > **Zóna dostupnosti** podrobností instance > vybrat **1**.
   - **Účet správce**> zadejte **uživatelské jméno**, **heslo** a **potvrzení** informací o hesle.
   - Vyberte kartu **síť** nebo vyberte **další: Disky**a potom **další: Sítě**.
  
1. Na kartě **sítě** zkontrolujte, že jsou vybrané následující:
   - **Virtuální síť**: *myVnet*
   - **Podsíť**: *myBackendSubnet*
   - **Veřejná IP** adresa > vyberte **vytvořit novou**a v okně **vytvořit veřejnou IP adresu** pro položku **SKU**vyberte možnost **Standard**a v části **zóna dostupnosti**vyberte **zóna – redundantní**a pak vyberte **OK**.
   - Chcete-li vytvořit novou skupinu zabezpečení sítě (NSG), typ brány firewall, v části **skupinu zabezpečení sítě**vyberte **Upřesnit**. 
       1. V **konfigurovat skupinu zabezpečení sítě** pole, vyberte **vytvořit nový**. 
       1. Zadejte *myNetworkSecurityGroup*a vyberte **OK**.
   - Pokud chcete virtuálnímu počítači udělat součást back-endu fondu Load Balancer, proveďte následující kroky:
        - V případě vyrovnávání **zatížení** **umístěte tento virtuální počítač za existující řešení vyrovnávání zatížení**a vyberte **Ano**.
        - V **nastavení vyrovnávání zatížení**v možnosti vyrovnávání **zatížení**vyberte **Azure Load Balancer**.
        - Pro **Vyberte nástroj**pro vyrovnávání zatížení *myLoadBalancer*.
        - Vyberte **správu** kartě nebo vyberte **Další** > **správu**.
2. Na kartě **Správa** v části **monitorování**nastavte diagnostiku **spouštění** na **vypnuto**. 
1. Vyberte **Zkontrolovat a vytvořit**.   
1. Zkontrolujte nastavení a pak vyberte **vytvořit**.
1. Postupujte podle kroků 2 až 6 a vytvořte dva další virtuální počítače s následujícími hodnotami a všechna ostatní nastavení, která jsou stejná jako *myVM1*:

    | Nastavení | VM 2| VM 3|
    | ------- | ----- |---|
    | Name |  *myVM2* |*myVM3*|
    | Zóna dostupnosti | 2 |3|
    |Veřejná IP adresa| **Standardní** SKLADOVÉ|**Standardní** SKLADOVÉ|
    | Veřejná IP adresa – zóna dostupnosti| **Zóna redundantní** |**Zóna redundantní**|
    | Skupina zabezpečení sítě | Vybrat existující *skupinu myNetworkSecurity*| Vybrat existující *skupinu myNetworkSecurity*|

 ### <a name="create-nsg-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

V této části vytvoříte pravidlo skupiny zabezpečení sítě, které povolí příchozí připojení pomocí protokolu HTTP.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom ze seznamu prostředky vyberte **myNetworkSecurityGroup** , která je umístěná ve skupině prostředků **myResourceGroupSLB** .
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

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředky vyberte **myVM1** , která je umístěná ve skupině prostředků *myResourceGroupSLB* .
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

## <a name="test-the-load-balancer"></a>Otestování Load Balancer
1. Na obrazovce **Přehled** vyhledejte veřejnou IP adresu Load Balanceru. V nabídce na levé straně vyberte **všechny služby** a vyberte **všechny prostředky**a pak vyberte **myPublicIP**.

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

   ![Webový server služby IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Pokud chcete zobrazit Load Balancer distribuci provozu napříč všemi třemi virtuálními počítači, můžete přizpůsobit výchozí stránku webového serveru IIS každého virtuálního počítače a potom vynutit aktualizaci webového prohlížeče z klientského počítače.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, Load Balancer a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků (*myResourceGroupSLB*) obsahující Load Balancer a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili Standard Load Balancer k němu připojené virtuální počítače, nakonfigurovali jste pravidlo Load Balancer přenosů dat, sondu stavu a pak jste otestovali Load Balancer. Další informace o službě Azure Load Balancer najdete v kurzech týkajících se služby Azure Load Balancer.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
