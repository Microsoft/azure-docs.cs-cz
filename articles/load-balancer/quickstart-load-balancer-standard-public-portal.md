---
title: 'Rychlý start: vytvoření Load balanceru úrovně Standard – webu Azure portal'
titlesuffix: Azure Load Balancer
description: Tento rychlý start ukazuje, jak vytvořit Load balanceru úrovně Standard s využitím webu Azure portal.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: f4f54273262f60dc01f78f4bb5828c8fdd2b97a9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707245"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Rychlý start: Vytvoření Load Balanceru úrovně Standard pro vyrovnávání zatížení virtuálních počítačů pomocí webu Azure Portal

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky na více virtuálních počítačů. K vytvoření nástroje pro vyrovnávání zatížení virtuálních počítačů můžete použít web Azure Portal. V tomto rychlém startu se dozvíte, jak vyrovnávat zatížení virtuálních počítačů pomocí Load Balanceru úrovně Standard.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Vytvoření Load Balanceru úrovně Standard

V této části vytvoříte Load balanceru úrovně Standard, který pomáhá vyrovnávat zatížení virtuálních počítačů. Load Balancer úrovně Standard podporuje pouze standardní veřejnou IP adresu. Při vytváření Load Balanceru úrovně Standard musíte vytvořit také novou standardní veřejnou IP adresu nakonfigurovanou jako jeho front-end (ve výchozím nastavení má název *LoadBalancerFrontend*). 

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek** > **sítě** > **nástroje pro vyrovnávání zatížení**.
2. V **Základy** karty **vytvořit nástroj pro vyrovnávání zatížení** stránky, zadejte nebo vyberte následující informace, přijměte výchozí hodnoty pro zbývající nastavení a pak vyberte **revize + vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **vytvořit nový** a typ *myResourceGroupSLB* v textovém poli.|
    | Název                   | *myLoadBalancer*                                   |
    | Oblast         | Vyberte **USA – východ**.                                        |
    | Type          | Vyberte **veřejné**.                                        |
    | Skladová jednotka (SKU)           | Vyberte **standardní**.                          |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. |
    | Název veřejné IP adresy              | Typ *myPublicIP* v textovém poli.   |
    |Zóna dostupnosti| Vyberte **Zónově redundantní**.    |
3. V **revize + vytvořit** kartu, vyberte možnost **vytvořit**.   

    ![Vytvoření Load Balanceru úrovně Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení

V této části nakonfigurujte nastavení nástroje pro vyrovnávání zatížení pro back-endový fond adres sondu stavu a zadat pravidlo nástroje pro vyrovnávání.

### <a name="create-a-backend-address-pool"></a>Vytvoření fondu back-endových adres

Back-endový fond adres pro distribuci provozu do virtuálních počítačů, obsahuje IP adresy virtuální (NIC) připojené k nástroji pro vyrovnávání zatížení. Vytvoření back-endový fond adres *myBackendPool* zahrnout virtuální počítače pro vyrovnávání zatížení přenosy z Internetu.

1. Vyberte **všechny služby** v nabídce vlevo vyberte **všechny prostředky**a pak vyberte **myLoadBalancer** v seznamu prostředků.
2. V části **nastavení**vyberte **back-endové fondy**a pak vyberte **přidat**.
3. Na **přidat back-endový fond** stránky pro název, typ *myBackendPool*, jako název back-endového fondu a pak vyberte **přidat**.

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Povolit nástroje pro vyrovnávání zatížení pro monitorování stavu vaší aplikace, použijte sondu stavu. Sonda stavu dynamicky přidá nebo odebere virtuální počítače z oběhu nástroje pro vyrovnávání zatížení, na základě jejich reakce na kontroly stavu. Vytvořte sondu stavu *myHealthProbe* pro monitorování stavu virtuálních počítačů.

1. Vyberte **všechny služby** v nabídce vlevo vyberte **všechny prostředky**a pak vyberte **myLoadBalancer** v seznamu prostředků.
2. V části **nastavení**vyberte **sondy stavu**a pak vyberte **přidat**.
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte *myHealthProbe*. |
    | Protocol (Protokol) | Vyberte **HTTP**. |
    | Port | Zadejte *80*.|
    | Interval | Zadejte *15* počet **Interval** v sekundách mezi pokusy o testování. |
    | Prahová hodnota pro poškozený stav | Vyberte **2** počet **prahová hodnota špatného stavu** nebo selhání po sobě jdoucích testu, které se musí vyskytovat před virtuální počítač považoval za poškozený.|
    | | |
4. Vyberte **OK**.

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla Load Balanceru
Pravidlo Load Balanceru slouží k definování způsobu distribuce provozu do virtuálních počítačů. Definujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Vytvořit pravidlo služby Load balancer úrovně *myLoadBalancerRuleWeb* pro naslouchání na portu 80 ve front-endu *FrontendLoadBalancer* a odesílání síťového provozu s vyrovnáváním zatížení do back-endový fond adres *myBackEndPool* rovněž na portu 80. 

1. Vyberte **všechny služby** v nabídce vlevo vyberte **všechny prostředky**a pak vyberte **myLoadBalancer** v seznamu prostředků.
2. V části **nastavení**vyberte **pravidla Vyrovnávání zatížení**a pak vyberte **přidat**.
3. Ke konfiguraci pravidla vyrovnávání zatížení použijte tyto hodnoty:
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Enter *myHTTPRule*. |
    | Protocol (Protokol) | Vyberte **TCP**. |
    | Port | Zadejte *80*.|
    | Back-endový port | Zadejte *80*. |
    | Back-endový fond | Vyberte *myBackendPool*.|
    | Sonda stavu | Vyberte *myHealthProbe*. |
4. Ponechejte zbývající výchozí hodnoty a pak vyberte **OK**.


## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části vytvořit virtuální síť, vytvořte tři virtuální počítače pro back-endového fondu nástroje pro vyrovnávání zatížení a potom nainstalovat službu IIS na virtuální počítače, která vám pomůže nástroj pro vyrovnávání zatížení otestovat.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
1. Na straně levého horního rohu obrazovky vyberte **vytvořit prostředek** > **sítě** > **virtuální síť**.

1. V **vytvořit virtuální síť**, zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte *myVNet*. |
    | Adresní prostor | Zadejte *10.1.0.0/16*. |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte existující prostředek - *myResourceGroupSLB*. |
    | Location | Vyberte **Západní Evropa**.|
    | Podsíť - Name | Zadejte *myBackendSubnet*. |
    | Podsíť – Rozsah adres | Zadejte *10.1.0.0/24*. |
1. Ponechejte zbývající výchozí hodnoty a vyberte **vytvořit**.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů
Load balancer úrovně standard podporuje pouze virtuální počítače se standardní IP adresami v back-endový fond. V této části vytvoříte tři virtuální počítače (*myVM1*, *myVM2* a *myVM3*) s standardní veřejné IP adresy ve třech různých zónách (*zóny 1*, *Zónu 2*, a *zóna 3*), který později se přidají do back-endový fond Standard pro vyrovnávání zatížení, který jste vytvořili dříve.

1. Na straně levého horního rohu portálu, vyberte **vytvořit prostředek** > **Compute** > **systému Windows Server. 2019 Datacenter**. 
   
1. V **vytvoření virtuálního počítače**, zadejte nebo vyberte následující hodnoty **Základy** kartu:
   - **Předplatné** > **skupiny prostředků**: Select **myResourceGroupSLB**.
   - **Podrobnosti o instanci** > **název virtuálního počítače**: Typ *myVM1*.
   - **Podrobnosti o instanci** > **oblasti** > vyberte **západní Evropa**.
   - **Podrobnosti o instanci** > **možností dostupnosti** > vyberte **zóny dostupnosti**. 
   - **Podrobnosti o instanci** > **zóna dostupnosti** > vyberte **1**.
   - **Účet správce**> Enter **uživatelské jméno**, **heslo** a **potvrzení hesla** informace.
   - Vyberte **sítě** kartě nebo vyberte **Další: Disky**, pak **Další: Sítě**.
  
1. V **sítě** Ujistěte se, že jsou vybrány následující karty:
   - **Virtuální síť**: *myVnet*
   - **Podsíť**: *myBackendSubnet*
   - **Veřejná IP adresa** > vyberte **vytvořit nový**a v **vytvoření veřejné IP adresy** okně pro **SKU**vyberte **standardní**, a pro **zóna dostupnosti**vyberte **zónově redundantní**a pak vyberte **OK**.
   - Chcete-li vytvořit novou skupinu zabezpečení sítě (NSG), typ brány firewall, v části **skupinu zabezpečení sítě**vyberte **Upřesnit**. 
       1. V **konfigurovat skupinu zabezpečení sítě** pole, vyberte **vytvořit nový**. 
       1. Typ *myNetworkSecurityGroup*a vyberte **OK**.
   - Chcete-li virtuální počítač součástí back-endového fondu nástroje pro vyrovnávání zatížení, proveďte následující kroky:
        - V **Vyrovnávání zatížení**, pro **umístit virtuální počítač za existující řešení vyrovnávání zatížení?** vyberte **Ano**.
        - V **nastavení služby Vyrovnávání zatížení**, pro **možnosti vyrovnávání zatížení**vyberte **nástroji Azure load balancer**.
        - Pro **vyberte nástroj pro vyrovnávání zatížení**, *myLoadBalancer*.
        - Vyberte **správu** kartě nebo vyberte **Další** > **správu**.
2. V **správu** ve skupině **monitorování**, nastavte **Diagnostika spouštění** k **vypnout**. 
1. Vyberte **Zkontrolovat a vytvořit**.   
1. Zkontrolujte nastavení a pak vyberte **vytvořit**.
1. Postupujte podle kroků 2 až 6 vytvořte dva další virtuální počítače s následující hodnoty a všechna ostatní nastavení stejný jako *myVM1*:

    | Nastavení | VM 2| VM 3|
    | ------- | ----- |---|
    | Název |  *myVM1* |*myVM3*|
    | Zóna dostupnosti | 2 |3|
    |Veřejná IP adresa| **Standardní** SKU|**Standardní** SKU|
    | Veřejná IP adresa – zóna dostupnosti| Zónově redundantní |
    | Skupina zabezpečení sítě | Vyberte existující *myNetworkSecurity skupiny*| Vyberte existující *myNetworkSecurity skupiny*|

 ### <a name="create-nsg-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

V této části vytvoříte pravidlo skupiny zabezpečení sítě, které povolí příchozí připojení pomocí protokolu HTTP.

1. Vyberte **všechny služby** v nabídce vlevo vyberte **všechny prostředky**a potom z prostředků seznamu vyberte **myNetworkSecurityGroup** , který je umístěný v **myResourceGroupSLB** skupinu prostředků.
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

1. Vyberte **všechny služby** v nabídce vlevo vyberte **všechny prostředky**a pak v seznamu prostředků vyberte **myVM1** , který je umístěný v  *myResourceGroupSLB* skupinu prostředků.
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

## <a name="test-the-load-balancer"></a>Otestování nástroje pro vyrovnávání zatížení
1. Na obrazovce **Přehled** vyhledejte veřejnou IP adresu Load Balanceru. Vyberte **všechny služby** v nabídce vlevo vyberte **všechny prostředky**a pak vyberte **myPublicIP**.

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

   ![Webový server služby IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Chcete-li zobrazit nástroje pro vyrovnávání zatížení distribuuje provoz mezi všechny tři virtuální počítače, můžete přizpůsobit výchozí stránka každý virtuální počítač webového serveru služby IIS a pak vynutit aktualizaci webového prohlížeče z klientského počítače.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, nástroje pro vyrovnávání zatížení a všech souvisejících prostředků. Uděláte to tak, vyberte skupinu prostředků (*myResourceGroupSLB*), který obsahuje nástroje pro vyrovnávání zatížení a pak vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili Load balanceru úrovně Standard, připojené virtuální počítače, nakonfigurovali pravidlo provozu nástroje pro vyrovnávání zatížení a sondu stavu a pak jste nástroj pro vyrovnávání zatížení otestovali. Další informace o službě Azure Load Balancer najdete v kurzech týkajících se služby Azure Load Balancer.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
