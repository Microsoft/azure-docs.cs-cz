---
title: 'Kurz: načtení vyrovnávat přenosy z Internetu k virtuálním počítačům – Azure portal | Dokumentace Microsoftu'
description: Tento kurz ukazuje, jak vytvořit a spravovat Standard Load Balancer pomocí webu Azure Portal.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create and Standard Load balancer so that I can load balance internet traffic to VMs and add and remove VMs from the load-balanced set.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/18
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: ef021a89cb1cba5a3240ade5ba67141940413cdc
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51687300"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Kurz: Zatížení vyrovnávat přenosy z Internetu k virtuálním počítačům pomocí webu Azure portal

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky na více virtuálních počítačů. V tomto kurzu se dozvíte o různých komponentách Azure Standard Load Balancer, které distribuují přenosy z Internetu k virtuálním počítačům a zajištění vysoké dostupnosti. Získáte informace o těchto tématech:


> [!div class="checklist"]
> * Vytvoření nástroje pro vyrovnávání zatížení Azure
> * Vytvoření virtuálních počítačů a instalace serveru IIS
> * Vytvoření prostředků nástroje pro vyrovnávání zatížení
> * Zobrazení nástroje pro vyrovnávání zatížení v akci
> * Přidání virtuálních počítačů do nástroje pro vyrovnávání zatížení a jejich odebrání

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu Azure Portal na adrese [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Vytvoření Load Balanceru úrovně Standard

V této části vytvoříte veřejný nástroj pro vyrovnávání zatížení, který pomáhá vyrovnávat zatížení virtuálních počítačů. Load Balancer úrovně Standard podporuje pouze standardní veřejnou IP adresu. Při vytváření Standard Load Balanceru musíte vytvořit také novou standardní veřejnou IP adresu nakonfigurovanou jako jeho front-end (ve výchozím nastavení má název *LoadBalancerFrontend*). 

1. V levém horním rohu obrazovky klikněte na **Vytvořit prostředek** > **Sítě** > **Load Balancer**.
2. Na stránce **Vytvořit nástroj pro vyrovnávání zatížení** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:
    
    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Název                   | *myLoadBalancer*                                   |
    | Typ          | Public                                        |
    | Skladová jednotka (SKU)           | Standard                          |
    | Veřejná IP adresa | Vyberte **Vytvořit novou** a do textového pole zadejte *myPublicIP*. Ve výchozím nastavení je pro veřejnou IP adresu vybraná skladová položka Standard. V poli **Zóna dostupnosti** vyberte **Zónově redundantní**. |
    | Předplatné               | Vyberte své předplatné.    |
    |Skupina prostředků | Vyberte **Vytvořit novou** a zadejte *myResourceGroupSLB*.    |
    | Umístění           | Vyberte **Západní Evropa**.                          |
    

![Vytvoření nástroje pro vyrovnávání zatížení](./media/load-balancer-standard-public-portal/create-load-balancer.png)
   
## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části vytvoříte virtuální síť a tři virtuální počítače pro back-endový fond vašeho nástroje pro vyrovnávání zatížení a pak na tyto virtuální počítače nainstalujete službu IIS, která vám pomůže nástroj pro vyrovnávání zatížení otestovat.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek** > **Sítě** > **Virtuální síť** a zadejte pro svou virtuální síť tyto hodnoty:
    |Nastavení|Hodnota|
    |---|---|
    |Název|Zadejte *myVNet*.|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků| Vyberte **Použít existující** a pak vyberte *myResourceGroupSLB*.|
    |Název podsítě| Zadejte *myBackendSubnet*.|
    
2. Kliknutím na **Vytvořit** vytvořte virtuální síť.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek** > **Compute** > **Windows Server 2016 Datacenter** a zadejte pro virtuální počítač tyto hodnoty:
    1. Jako název virtuálního počítače zadejte *myVM1*.        
    2. V části **Skupina prostředků** vyberte **Použít existující** a pak vyberte *myResourceGroupSLB*.
2. Klikněte na **OK**.
3. Vyberte velikost virtuálního počítače **DS1_V2** a klikněte na **Vybrat**.
4. Zadejte následující hodnoty nastavení virtuálního počítače:
    1. Zkontrolujte, že je vybraná virtuální síť *myVNet* a podsíť *myBackendSubnet*.
    2. V části **Veřejná IP adresa** v podokně **Vytvořit veřejnou IP adresu** vyberte **Standard** a pak vyberte **OK**.
    3. V části **Skupina zabezpečení sítě** vyberte **Upřesnit** a pak postupujte následovně:
        1. Vyberte Skupina zabezpečení sítě (brána firewall) a na stránce **Vyberte skupinu zabezpečení sítě** vyberte **Vytvořit novou**. 
        2. Na stránce **Vyberte skupinu zabezpečení sítě** jako **Název** nové skupiny zabezpečení sítě zadejte *myNetworkSecurityGroup* a pak vyberte **OK**.
5. Kliknutím na **Zakázáno** zakažte diagnostiku spouštění.
6. Klikněte na **OK**, na stránce souhrnu zkontrolujte nastavení a pak klikněte na **Vytvořit**.
7. Podle pokynů v krocích 1 až 6 vytvořte další dva virtuální počítače s názvy *VM2* a *VM3*, s virtuální sítí *myVnet*, podsítí *myBackendSubnet* a skupinou zabezpečení sítě *myNetworkSecurityGroup*. 

### <a name="create-network-security-group-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

V této části vytvoříte pravidlo skupiny zabezpečení sítě, které povolí příchozí připojení pomocí protokolu HTTP.

1. V levé nabídce klikněte na **Všechny prostředky** a pak v seznamu prostředků klikněte na **myNetworkSecurityGroup** ve skupině prostředků **myResourceGroupSLB**.
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

1. V nabídce nalevo klikněte na **Všechny prostředky** a pak v seznamu prostředků klikněte na **myVM1** ve skupině prostředků *myResourceGroupSLB*.
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

## <a name="create-load-balancer-resources"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení

V této části nakonfigurujete nastavení nástroje pro vyrovnávání zatížení pro fond back-endových adres a sondu stavu a zadáte pravidlo nástroje pro vyrovnávání zatížení.

### <a name="create-a-backend-address-pool"></a>Vytvoření fondu back-endových adres

Za účelem distribuce provozu do virtuálních počítačů obsahuje fond back-endových adres IP adresy virtuálních síťových karet připojených k nástroji pro vyrovnávání zatížení. Vytvořte fond back-endových adres *myBackendPool*, který zahrnuje virtuální počítače *VM1* a *VM2*.

1. V levé nabídce klikněte na **Všechny prostředky** a pak v seznamu prostředků klikněte na **myLoadBalancer**.
2. V části **Nastavení** klikněte na **Back-endové fondy** a pak klikněte na **Přidat**.
3. Na stránce **Přidat back-endový fond** postupujte následovně:
   - Jako název back-endového fondu zadejte *myBackendPool*.
   - Jako **Virtuální síť** vyberte *myVNet*.
   - V části **Virtuální počítač** přidejte virtuální počítače *myVM1*, *myVM2* a *my VM3* spolu s odpovídajícími IP adresami a pak vyberte **Přidat**.
4. Zkontrolujte, že se v nastavení back-endového fondu vašeho nástroje pro vyrovnávání zatížení zobrazují všechny tři virtuální počítače (*myVM1*, *myVM2* a *myVM3*) a pak klikněte na **OK**.

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Pokud chcete nástroji pro vyrovnávání zatížení povolit monitorování stavu vaší aplikace, použijte sondu stavu. Sonda stavu dynamicky přidává virtuální počítače do oběhu nástroje pro vyrovnávání zatížení nebo je z něj odebírá na základě jejich reakce na kontroly stavu. Vytvořte sondu stavu *myHealthProbe* pro monitorování stavu virtuálních počítačů.

1. V levé nabídce klikněte na **Všechny prostředky** a pak v seznamu prostředků klikněte na **myLoadBalancer**.
2. V části **Nastavení** klikněte na **Sondy stavu** a pak klikněte na **Přidat**.
3. K vytvoření sondy stavu použijte tyto hodnoty:
    - *myHealthProbe* – název sondy stavu.
    - **HTTP** – typ protokolu.
    - *80* – číslo portu.
    - *15* – **Interval** mezi pokusy o testování v sekundách.
    - *2* – **Prahová hodnota špatného stavu** neboli počet po sobě jdoucích selhání sondy, ke kterým musí dojít, aby se virtuální počítač považoval za poškozený.
4. Klikněte na **OK**.

   ![Přidání testu](./media/load-balancer-standard-public-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce provozu do virtuálních počítačů. Definujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Vytvořte pravidlo nástroje pro vyrovnávání zatížení *myLoadBalancerRuleWeb* pro naslouchání na portu 80 ve front-endu *FrontendLoadBalancer* a odesílání síťového provozu s vyrovnáváním zatížení do back-endového fondu adres *myBackEndPool* rovněž na portu 80. 

1. V levé nabídce klikněte na **Všechny prostředky** a pak v seznamu prostředků klikněte na **myLoadBalancer**.
2. V části **Nastavení** klikněte na **Pravidla vyrovnávání zatížení** a pak klikněte na **Přidat**.
3. Ke konfiguraci pravidla vyrovnávání zatížení použijte tyto hodnoty:
    - *myHTTPRule* – název pravidla vyrovnávání zatížení.
    - **TCP** – typ protokolu.
    - *80* – číslo portu.
    - *80* – back-endový port.
    - *myBackendPool* – název backendového fondu.
    - *myHealthProbe* – název sondy stavu.
4. Klikněte na **OK**.

## <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení
1. Na obrazovce **Přehled** vyhledejte veřejnou IP adresu Load Balanceru. Klikněte na **Všechny prostředky** a pak klikněte na **myPublicIP**.

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

      ![Webový server služby IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Pokud chcete zobrazit distribuci provozu nástrojem pro vyrovnávání zatížení mezi všechny tři virtuální počítače, na kterých je vaše aplikace spuštěná, můžete vynutit aktualizaci webového prohlížeče.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Odeberte nebo přidejte virtuální počítače z back-endového fondu.
Na virtuálních počítačích, na kterých je vaše aplikace spuštěná, možná budete potřebovat provést údržbu, například nainstalovat aktualizace operačního systému. Abyste si poradili se zvýšením provozu do vaší aplikace, možná budete muset přidat další virtuální počítače. V této části se dozvíte, jak z nástroje pro vyrovnávání zatížení odebrat virtuální počítač nebo ho do něj přidat.

1. V levé nabídce klikněte na **Všechny prostředky** a pak v seznamu prostředků klikněte na **myLoadBalancer**.
2. V části **Nastavení** klikněte na **Back-endové fondy** a v seznamu back-endového fondu klikněte na **myBackendPool**.
3. Pokud chcete odebrat *VM1* z back-endu, klikněte na stránce **myBackendPool** v části **Target network IP configurations** (Cílové konfigurace protokolu IP sítě) na ikonu pro odstranění vedle položky **Virtual machine:myVM1** (Virtuální počítač:myVM1).

Když už *myVM1* není v back-endovém fondu adres, můžete na *myVM1* provádět všechny úlohy údržby, jako jsou třeba instalace aktualizací softwaru. Protože teď není dostupný *VM1**, vyrovnává se zatížení s použitím *myVM2* a *myVM3*. 

Když budete chtít přidat *myVM1* zpátky do back-endového fondu, postupujte podle pokynů v části *Přidání virtuálních počítačů do back-endového fondu* v tomto článku.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, nástroj pro vyrovnávání zatížení a všechny další související prostředky. Provedete to výběrem skupiny prostředků, která obsahuje nástroj pro vyrovnávání zatížení, a kliknutím na **Odstranit**.

## <a name="next-steps"></a>Další postup

V rámci tohoto kurzu jste vytvořili službu Standard Load Balancer, připojili jste k ní virtuální počítače, nakonfigurovali jste pravidlo pro přenosy u nástroje pro vyrovnávání zatížení a sondu stavu a pak jste nástroj pro vyrovnávání zatížení otestovali. Také jste odebrali virtuální počítač ze skupiny s vyrovnáváním zatížení a přidali jste virtuální počítač zpátky do fondu back-endových adres. Další informace o službě Azure Load Balancer najdete v kurzech týkajících se služby Azure Load Balancer.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
