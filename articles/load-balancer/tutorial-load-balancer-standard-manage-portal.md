---
title: 'Kurz: Zatížení vyrovnávat přenosy z Internetu k virtuálním počítačům – Azure portal'
titlesuffix: Azure Load Balancer
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
ms.openlocfilehash: 22d0e56a77036c551b6006f43997c92fcce07499
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273359"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Kurz: Zatížení vyrovnávat přenosy z Internetu k virtuálním počítačům pomocí webu Azure portal

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky na více virtuálních počítačů. V tomto kurzu se dozvíte o různých komponentách Azure Standard Load Balancer, které distribuují přenosy z Internetu k virtuálním počítačům a zajištění vysoké dostupnosti. Získáte informace o těchto tématech:


> [!div class="checklist"]
> * Vytvoření služby Azure Load Balancer
> * Vytvoření prostředků nástroje pro vyrovnávání zatížení
> * Vytvoření virtuálních počítačů a instalace serveru IIS
> * Nástroj pro vyrovnávání zatížení zobrazení v akci
> * Přidání a odebrání virtuálních počítačů z nástroje pro vyrovnávání zatížení

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Vytvoření Load Balanceru úrovně Standard

V této části vytvoříte Load balanceru úrovně Standard, který pomáhá vyrovnávat zatížení virtuálních počítačů. Load Balancer úrovně Standard podporuje pouze standardní veřejnou IP adresu. Při vytváření Standard Load Balanceru musíte vytvořit také novou standardní veřejnou IP adresu nakonfigurovanou jako jeho front-end (ve výchozím nastavení má název *LoadBalancerFrontend*). 

1. V levém horním rohu obrazovky klikněte na **Vytvořit prostředek** > **Sítě** > **Load Balancer**.
2. V **Základy** karty **vytvořit nástroj pro vyrovnávání zatížení** stránky, zadejte nebo vyberte následující informace, přijměte výchozí hodnoty pro zbývající nastavení a pak vyberte **revize + vytvořit**:

    | Nastavení                 | Value                                              |
    | ---                     | ---                                                |
    | Subscription               | Vyberte své předplatné.    |    
    | Resource group         | Vyberte **vytvořit nový** a typ *myResourceGroupSLB* v textovém poli.|
    | Name                   | *myLoadBalancer*                                   |
    | Oblast         | Vyberte **Západní Evropa**.                                        |
    | type          | Vyberte **veřejné**.                                        |
    | SKU           | Vyberte **standardní**.                          |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. |
    | Název veřejné IP adresy              | Typ *myPublicIP* v textovém poli.   |
    |Zóna dostupnosti| Vyberte **Zónově redundantní**.    |

3. V **revize + vytvořit** klikněte na tlačítko **vytvořit**.

   ![Vytvoření Load Balanceru úrovně Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení

V této části nakonfigurujte nastavení nástroje pro vyrovnávání zatížení pro back-endový fond adres sondu stavu a zadat pravidlo nástroje pro vyrovnávání.

### <a name="create-a-backend-address-pool"></a>Vytvoření fondu back-endových adres

Back-endový fond adres pro distribuci provozu do virtuálních počítačů, obsahuje IP adresy virtuální (NIC) připojené k nástroji pro vyrovnávání zatížení. Vytvoření back-endový fond adres *myBackendPool* zahrnout virtuální počítače pro vyrovnávání zatížení přenosy z Internetu.

1. Vyberte **všechny služby** v nabídce vlevo vyberte **všechny prostředky**a potom klikněte na tlačítko **myLoadBalancer** v seznamu prostředků.
2. V části **Nastavení** klikněte na **Back-endové fondy** a pak klikněte na **Přidat**.
3. Na **přidat back-endový fond** stránky pro název, typ *myBackendPool*, jako název back-endového fondu a pak vyberte **přidat**.

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Povolit nástroje pro vyrovnávání zatížení pro monitorování stavu vaší aplikace, použijte sondu stavu. Sonda stavu dynamicky přidá nebo odebere virtuální počítače z oběhu nástroje pro vyrovnávání zatížení, na základě jejich reakce na kontroly stavu. Vytvořte sondu stavu *myHealthProbe* pro monitorování stavu virtuálních počítačů.

1. Vyberte **všechny služby** v nabídce vlevo vyberte **všechny prostředky**a potom klikněte na tlačítko **myLoadBalancer** v seznamu prostředků.
2. V části **Nastavení** klikněte na **Sondy stavu** a pak klikněte na **Přidat**.
3. K vytvoření sondy stavu použijte tyto hodnoty:
     
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name | Zadejte *myHealthProbe*. |
    | Protocol | Vyberte **HTTP**. |
    | Port | Zadejte *80*.|
    | Interval | Zadejte *15* počet **Interval** v sekundách mezi pokusy o testování. |
    | Prahová hodnota špatného stavu | Vyberte *2* počet **prahová hodnota špatného stavu** nebo selhání po sobě jdoucích testu, které se musí vyskytovat před virtuální počítač považoval za poškozený.|
    | Sonda stavu | Vyberte *myHealthProbe*. |
    
4. Vyberte **OK**.

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla Load Balanceru

Pravidlo Load Balanceru slouží k definování způsobu distribuce provozu do virtuálních počítačů. Definujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Vytvořit pravidlo služby Load balancer úrovně *myLoadBalancerRuleWeb* pro naslouchání na portu 80 ve front-endu *FrontendLoadBalancer* a odesílání síťového provozu s vyrovnáváním zatížení do back-endový fond adres *myBackEndPool* rovněž na portu 80.

1. Vyberte **všechny služby** v nabídce vlevo vyberte **všechny prostředky**a potom klikněte na tlačítko **myLoadBalancer** v seznamu prostředků.
2. V části **Nastavení** klikněte na **Pravidla vyrovnávání zatížení** a pak klikněte na **Přidat**.
3. A tím nakonfigurovat pravidlo Vyrovnávání zatížení použijte tyto hodnoty:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name | Zadejte *myHTTPRule*. |
    | Protocol | Vyberte **TCP**. |
    | Port | Zadejte *80*.|
    | Back-endový port | Zadejte *80*. |
    | Back-endový fond | Vyberte *myBackendPool*.|
    | Sonda stavu | Vyberte *myHealthProbe*. |
    
4. Pro ostatní nastavení nechte zvolené výchozí hodnoty a vyberte **OK**.

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části vytvořit virtuální síť, vytvořte tři virtuální počítače pro back-endového fondu nástroje pro vyrovnávání zatížení a potom nainstalovat službu IIS na virtuální počítače, která vám pomůže nástroj pro vyrovnávání zatížení otestovat.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Na straně levého horního rohu obrazovky vyberte **vytvořit prostředek** > **sítě** > **virtuální síť**.
2. V **vytvořit virtuální síť**, zadejte nebo vyberte tyto informace:

    | Nastavení | Value |
    | ------- | ----- |
    | Name | Zadejte *myVNet*. |
    | Adresní prostor | Zadejte *10.1.0.0/16*. |
    | Subscription | Vyberte své předplatné.|
    | Resource group | Vyberte existující prostředek - *myResourceGroupSLB*. |
    | Location | Vyberte **Západní Evropa**.|
    | Podsíť - Name | Zadejte *myBackendSubnet*. |
    | Podsíť – Rozsah adres | Zadejte *10.1.0.0/24*. |
    
3. Ponechejte zbývající výchozí hodnoty a vyberte **vytvořit**.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Load balancer úrovně standard podporuje pouze virtuální počítače se standardní IP adresami v back-endový fond. V této části vytvoříte tři virtuální počítače (*myVM1*, *myVM2*, a *myVM3*) s standardní veřejné IP adresy ve třech různých zónách (*zóny 1*, *Zónu 2*, a *zóna 3*), které se přidají do back-endový fond Standard pro vyrovnávání zatížení, který jste vytvořili dříve.

1. Na straně levého horního rohu portálu, vyberte **vytvořit prostředek** > **Compute** > **systému Windows Server 2016 Datacenter**. 
   
1. V **vytvoření virtuálního počítače**, zadejte nebo vyberte následující hodnoty **Základy** kartu:
   - **Předplatné** > **skupiny prostředků**: Vyberte **myResourceGroupSLB**.
   - **Podrobnosti o instanci** > **název virtuálního počítače**: Typ *myVM1*.
   - **Podrobnosti o instanci** > **oblasti** > vyberte **západní Evropa**.
   - **Podrobnosti o instanci** > **možností dostupnosti** > vyberte **zóny dostupnosti**. 
   - **Podrobnosti o instanci** > **zóna dostupnosti** > vyberte **1**.
  
1. Vyberte **sítě** kartě nebo vyberte **Další: Disky**, pak **Další: Sítě**. 
   
   - Ujistěte se, že jsou vybrány následující:
       - **Virtuální síť**: **myVnet**
       - **Podsíť**: **myBackendSubnet**
       - **Veřejná IP adresa** > vyberte **vytvořit nový**a v **vytvoření veřejné IP adresy** okně pro **SKU**vyberte **standardní**, a pro **zóna dostupnosti**vyberte **zónově redundantní**
      
   - Chcete-li vytvořit novou skupinu zabezpečení sítě (NSG), typ brány firewall, v části **skupinu zabezpečení sítě**vyberte **Upřesnit**. 
       1. V **konfigurovat skupinu zabezpečení sítě** pole, vyberte **vytvořit nový**. 
       1. Typ *myNetworkSecurityGroup*a vyberte **OK**.

   - Chcete-li virtuální počítač součástí back-endového fondu nástroje pro vyrovnávání zatížení, proveďte následující kroky:
        - V **Vyrovnávání zatížení**, pro **umístit virtuální počítač za existující řešení vyrovnávání zatížení?** vyberte **Ano**.
        - V **nastavení služby Vyrovnávání zatížení**, pro **možnosti vyrovnávání zatížení**vyberte **nástroji Azure load balancer**.
        - Pro **vyberte nástroj pro vyrovnávání zatížení**, *myLoadBalancer*. 
1. Vyberte **správu** kartě nebo vyberte **Další** > **správu**. V části **monitorování**, nastavte **Diagnostika spouštění** k **vypnout**. 
1. Vyberte **Zkontrolovat a vytvořit**.   
1. Zkontrolujte nastavení a pak vyberte **vytvořit**.
1. Podle pokynů vytvořte dva další virtuální počítače – *myVM2* a *myVM3*, s veřejnou IP adresu standardních SKU v **zóna dostupnosti** **2** a **3** a všechna ostatní nastavení stejný jako *myVM1*.  

### <a name="create-network-security-group-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

V této části vytvoříte pravidlo skupiny zabezpečení sítě, které povolí příchozí připojení pomocí protokolu HTTP.

1. Vyberte **všechny služby** v nabídce vlevo vyberte **všechny prostředky**a pak v seznamu prostředků klikněte na tlačítko **myNetworkSecurityGroup** , který je umístěný v **myResourceGroupSLB** skupinu prostředků.
2. V části **Nastavení** klikněte na **Příchozí pravidla zabezpečení** a pak klikněte na **Přidat**.
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

### <a name="install-iis-on-vms"></a>Instalace služby IIS na virtuální počítače

1. Vyberte **všechny služby** v nabídce vlevo vyberte **všechny prostředky**a pak v seznamu prostředků klikněte na tlačítko **myVM1** , který je umístěný v  *myResourceGroupSLB* skupinu prostředků.
2. Na stránce **Přehled** klikněte na **Připojit** a připojte se přes RDP k virtuálnímu počítači.
3. V automaticky otevíraném okně **Připojení k virtuálnímu počítači** vyberte **Stáhnout soubor RDP** a pak otevřete stažený soubor RDP.
4. V okně **Připojení ke vzdálené ploše** klikněte na **Připojit**.
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

## <a name="test-the-load-balancer"></a>Otestování nástroje pro vyrovnávání zatížení
1. Na obrazovce **Přehled** vyhledejte veřejnou IP adresu Load Balanceru. Vyberte **všechny služby** v nabídce vlevo vyberte **všechny prostředky**a potom klikněte na tlačítko **myPublicIP**.

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

      ![Webový server služby IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Chcete-li zobrazit nástroje pro vyrovnávání zatížení distribuuje provoz mezi tři virtuální počítače používající vaši aplikaci, můžete můžete vynutit aktualizaci webového prohlížeče.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Odeberte nebo přidejte virtuální počítače z back-endového fondu.
Na virtuálních počítačích, na kterých je vaše aplikace spuštěná, možná budete potřebovat provést údržbu, například nainstalovat aktualizace operačního systému. Abyste si poradili se zvýšením provozu do vaší aplikace, možná budete muset přidat další virtuální počítače. V této části se dozvíte, jak odebrat nebo přidat virtuální počítač (*myVM1*) z nástroje pro vyrovnávání zatížení.

### <a name="remove-vm-from-a-backend-pool"></a>Odeberte virtuální počítač z back-endový fond
Chcete-li odebrat *myVM1* z back-endový fond, proveďte následující kroky:

1. Vyberte **všechny služby** v nabídce vlevo vyberte **všechny prostředky**a potom klikněte na tlačítko **myLoadBalancer** v seznamu prostředků.
2. V části **Nastavení** klikněte na **Back-endové fondy** a v seznamu back-endového fondu klikněte na **myBackendPool**.
3. Na **myBackendPool** stránky, chcete-li odebrat *VM1* vyberte ikonu Odstranit na konci řádku, který zobrazí *myVM1*a potom klikněte na tlačítko **Uložit**.

Když už *myVM1* není v back-endovém fondu adres, můžete na *myVM1* provádět všechny úlohy údržby, jako jsou třeba instalace aktualizací softwaru. Chybí *VM1*, je nyní s vyrovnáváním zatížení napříč *myVM2* a *myVM3*. 

### <a name="add-vm-to-a-backend-pool"></a>Přidání virtuálních počítačů do back-endový fond
Chcete-li přidat *myVM1* zpět do back-endový fond, proveďte následující kroky:

1. Vyberte **všechny služby** v nabídce vlevo vyberte **všechny prostředky**a pak vyberte **myVM1** v seznamu prostředků.
2. V **VM1** stránce v části **nastavení**vyberte **sítě**.
3. V **sítě** stránky, vyberte **Vyrovnávání zatížení** kartu a potom vyberte **přidání služby Vyrovnávání zatížení**.
4. V **přidání služby Vyrovnávání zatížení** stránce, postupujte takto:
   1. Pro **možnosti vyrovnávání zatížení**vyberte **nástroji Azure load balancer**.
   2. Pro **vyberte nástroj pro vyrovnávání zatížení**vyberte *myLoadBalancer*.
   3. Pro **vyberte back-endový fond**vyberte *myBackendPool*. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, nástroje pro vyrovnávání zatížení a všech souvisejících prostředků. Chcete-li to provést, vyberte *myResouceGroupSLB* skupinu prostředků, která obsahuje nástroje pro vyrovnávání zatížení a potom vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili Load balanceru úrovně Standard, připojené virtuální počítače, nakonfigurovali pravidlo provozu nástroje pro vyrovnávání zatížení a sondu stavu a pak jste nástroj pro vyrovnávání zatížení otestovali. Také jste odebrali virtuální počítač ze skupiny s vyrovnáváním zatížení a přidali jste virtuální počítač zpátky do fondu back-endových adres. Další informace o službě Azure Load Balancer najdete v kurzech týkajících se služby Azure Load Balancer.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
