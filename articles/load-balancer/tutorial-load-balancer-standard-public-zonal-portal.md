---
title: 'Kurz: Použití Load Balanceru pro virtuální počítače v rámci zóny – Azure Portal | Microsoft Docs'
description: Tento kurz ukazuje, jak vytvořit Load Balancer úrovně Standard se zónovým front-endem, který bude vyrovnávat zatížení virtuálních počítačů v rámci zóny dostupnosti pomocí webu Azure Portal.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 9067ea350997ed0c4fc5c65dccb72f403adfa774
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="tutorialload-balance-vms-within-an-availability-zone-with-a-standard-load-balancer-using-the-azure-portal"></a>Kurz: Vyrovnávání zatížení virtuálních počítačů v rámci zóny dostupnosti pomocí Load Balanceru úrovně Standard na webu Azure Portal

Tento kurz popisuje kroky při vytváření veřejného [Load Balanceru úrovně Standard](https://aka.ms/azureloadbalancerstandard) se zónovým front-endem se standardní veřejnou IP adresou na webu Azure Portal. V tomto scénáři zadáte určitou zónu pro instance front-endu a back-endu, aby se cesta k datům a prostředky spojily s konkrétní zónou. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření Azure Load Balanceru úrovně Standard se zónovým front-endem
> * Vytvoření skupin zabezpečení sítě pro definování pravidel pro příchozí provoz
> * Vytvoření zónových virtuálních počítačů a připojení k nástroji pro vyrovnávání zatížení
> * Vytvoření sondy stavu nástroje pro vyrovnávání zatížení
> * Vytvoření pravidel provozu pro nástroj pro vyrovnávání zatížení
> * Vytvoření základního webu služby IIS
> * Zobrazení nástroje pro vyrovnávání zatížení v akci

Další informace o používání zón dostupnosti s Load Balancerem úrovně Standard najdete v tématu o [Load Balanceru úrovně Standard a zónách dostupnosti](load-balancer-standard-availability-zones.md).

Pokud chcete, můžete tento kurz absolvovat s použitím [Azure CLI](load-balancer-standard-public-zonal-cli.md).

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-standard-load-balancer"></a>Vytvoření veřejného Load Balanceru úrovně Standard

Load Balancer úrovně Standard podporuje pouze standardní veřejnou IP adresu. Když při vytváření nástroje pro vyrovnávání zatížení vytvoříte novou veřejnou IP adresu, automaticky se nakonfiguruje jako standardní verze SKU a je také automaticky zónově redundantní.

1. V levém horním rohu obrazovky klikněte na **Vytvořit prostředek** > **Sítě** > **Load Balancer**.
2. Na stránce **Vytvoření nástroje pro vyrovnávání zatížení** zadejte pro nástroj pro vyrovnávání zatížení tyto hodnoty:
    - *myLoadBalancer* – název nástroje pro vyrovnávání zatížení.
    - **Public** – typ nástroje pro vyrovnávání zatížení.
     - *myPublicIPZonal* – nová veřejná IP adresa, kterou vytvoříte. Postupujte tak, že kliknete na **Vybrat veřejnou IP adresu** a pak na **Vytvořit novou**. Jako název zadejte *myPublicIP*, SKU je ve výchozím nastavení standardní a pro možnost **Zóna dostupnosti** vyberte **Zóna 1**.
    - *myResourceGroupZLB* – název nové skupiny prostředků, kterou vytvoříte.
    - **westeurope** – umístění.
3. Kliknutím na **Vytvořit** vytvořte nástroj pro vyrovnávání zatížení.
   
    ![Vytvoření zónového Load Balanceru úrovně Standard na webu Azure Portal](./media/tutorial-load-balancer-standard-zonal-portal/create-load-balancer-zonal-frontend.png)


## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části vytvoříte virtuální síť, ve stejné zóně (konkrétně v zóně 1) pro danou oblast vytvoříte dva virtuální počítače, které se přidají do back-endového fondu vašeho nástroje pro vyrovnávání zatížení, a pak na tyto virtuální počítače nainstalujete službu IIS, která vám pomůže zónově redundantní nástroj pro vyrovnávání zatížení otestovat. Pokud tedy dojde k selhání jednoho virtuálního počítače, selže sonda stavu pro virtuální počítač v dané zóně a pro provoz se dále použije druhý virtuální počítač v dané zóně.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
1. V levém horním rohu obrazovky klikněte na **Vytvořit prostředek** > **Sítě** > **Virtuální síť** a zadejte pro svou virtuální síť tyto hodnoty:
    - *myVnet* – název virtuální sítě.
    - *myResourceGroupZLB* – název existující skupiny prostředků.
    - *myBackendSubnet* – název podsítě.
2. Kliknutím na **Vytvořit** vytvořte virtuální síť.

    ![Vytvoření virtuální sítě](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-network.png)

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

1. V levém horním rohu obrazovky klikněte na **Vytvořit prostředek**, do vyhledávacího pole zadejte *Skupina zabezpečení sítě* a na stránce Skupina zabezpečení sítě klikněte na **Vytvořit**.
2. Na stránce Vytvořit skupinu zabezpečení sítě zadejte tyto hodnoty:
    - *myNetworkSecurityGroup* – název skupiny zabezpečení sítě.
    - *myResourceGroupLBAZ* – název existující skupiny prostředků.
   
    ![Vytvoření virtuální sítě](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>Vytvoření pravidel skupiny zabezpečení sítě

V této části vytvoříte na webu Azure Portal pravidla skupiny zabezpečení sítě, která povolí příchozí připojení pomocí protokolů HTTP a RDP.

1. Na webu Azure Portal v levé nabídce klikněte na **Všechny prostředky** a pak vyhledejte a vyberte **myNetworkSecurityGroup** ve skupině prostředků **myResourceGroupZLB**.
2. V části **Nastavení** klikněte na **Příchozí pravidla zabezpečení** a pak klikněte na **Přidat**.
3. Zadáním následujících hodnot pro příchozí pravidlo zabezpečení *myHTTPRule* povolte příchozí připojení HTTP na portu 80:
    - Jako **Zdroj** zadejte *Značka služby*.
    - Jako **Značka zdrojové služby** zadejte *Internet*.
    - Jako **Rozsahy cílových portů** zadejte *80*.
    - Jako **Protokol** zadejte *TCP*.
    - Jako **Akce** zadejte *Povolit*.
    - Jako **Priorita** zadejte *100*.
    - Jako **Název** zadejte *myHTTPRule*.
    - Jako **Popis** zadejte *Povolení protokolu HTTP*.
4. Klikněte na **OK**.
 
 ![Vytvoření virtuální sítě](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)

5. Zopakováním kroků 2 až 4 vytvořte další pravidlo *myRDPRule*, které povolí příchozí připojení RDP na portu 3389, s použitím následujících hodnot:
    - Jako **Zdroj** zadejte *Značka služby*.
    - Jako **Značka zdrojové služby** zadejte *Internet*.
    - Jako **Rozsahy cílových portů** zadejte *3389*.
    - Jako **Protokol** zadejte *TCP*.
    - Jako **Akce** zadejte *Povolit*.
    - Jako **Priorita** zadejte *200*.
    - Jako název zadejte *myRDPRule*.
    - Jako popis zadejte *Povolení protokolu RDP*.

    ![Vytvoření pravidla protokolu RDP](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

1. V levém horním rohu obrazovky klikněte na **Vytvořit prostředek** > **Compute** > **Windows Server 2016 Datacenter** a zadejte pro virtuální počítač tyto hodnoty:
    - *myVM1* – název virtuálního počítače.        
    - *azureuser* – uživatelské jméno správce.    
    - *myResourceGroupZLB* – v části **Skupina prostředků** vyberte **Použít existující** a pak vyberte *myResourceGroupZLB*.
2. Klikněte na **OK**.
3. Vyberte velikost virtuálního počítače **DS1_V2** a klikněte na **Vybrat**.
4. Zadejte následující hodnoty nastavení virtuálního počítače:
    - *zone 1* – zóna dostupnosti, do které umístíte daný virtuální počítač.
    -  *myVNet* – ujistěte se, že je vybraná tato virtuální síť.
    - *myVM1PIP* – standardní veřejná IP adresa, kterou vytvoříte. Klikněte na *Vytvořit novou* a pak jako název zadejte *myVM1PIP* a jako zónu vyberte **1**. SKU IP adresy je ve výchozím nastavení standardní.
    - *myBackendSubnet* – ujistěte se, že je vybraná tato podsíť.
    - *myNetworkSecurityGroup* – název skupiny zabezpečení sítě (brána firewall), která už existuje.
5. Kliknutím na **Zakázáno** zakažte diagnostiku spouštění.
6. Klikněte na **OK**, na stránce souhrnu zkontrolujte nastavení a pak klikněte na **Vytvořit**.
7. Podle kroků 1 až 6 vytvořte v zóně 1 druhý virtuální počítač s názvem *myVM2*, virtuální sítí *myVnet*, standardní veřejnou IP adresou *myVM2PIP*, podsítí *myBackendSubnet* a skupinou zabezpečení sítě **myNetworkSecurityGroup*.

    ![Vytvoření pravidla protokolu RDP](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>Instalace služby IIS na virtuální počítače

1. V levé nabídce klikněte na **Všechny prostředky** a pak v seznamu prostředků klikněte na **myVM1** ve skupině prostředků *myResourceGroupZLB*.
2. Na stránce **Přehled** klikněte na **Připojit** a připojte se přes RDP k virtuálnímu počítači.
3. Přihlaste se k virtuálnímu počítači pomocí uživatelského jména a hesla, které jste zadali při vytváření virtuálního počítače (abyste mohli zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače, možná budete muset vybrat **Další možnosti** a pak **Použít jiný účet**), a pak vyberte **OK**. Během procesu přihlášení se může zobrazit upozornění certifikátu. Vyberte **Ano** a pokračujte v připojování.
4. Na ploše serveru přejděte do části **Nástroje pro správu Windows**>**Windows PowerShell**.
6. V okně PowerShellu spuštěním následujících příkazů nainstalujte server služby IIS, odeberte soubor default.htm a přidejte nový soubor default.htm, který zobrazuje název virtuálního počítače:

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello from" + $env:computername)
   ```
8. Ukončete relaci RDP s *myVM1*.
9. Opakováním kroků 1 až 8 nainstalujte službu IIS na *myVM2*.

## <a name="create-load-balancer-resources"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení

V této části nakonfigurujete nastavení nástroje pro vyrovnávání zatížení pro fond back-endových adres a sondu stavu a zadáte pravidla nástroje pro vyrovnávání zatížení a překladu adres.


### <a name="create-a-backend-address-pool"></a>Vytvoření fondu back-endových adres

Za účelem distribuce provozu do virtuálních počítačů obsahuje fond back-endových adres IP adresy virtuálních síťových karet připojených k nástroji pro vyrovnávání zatížení. Vytvořte fond back-endových adres *myBackendPool*, který zahrnuje virtuální počítače *VM1* a *VM2*.

1. V levé nabídce klikněte na **Všechny prostředky** a pak v seznamu prostředků klikněte na **myLoadBalancer*.
2. V části **Nastavení** klikněte na **Back-endové fondy** a pak klikněte na **Přidat**.
3. Na stránce **Přidat back-endový fond** postupujte následovně:
    - Jako název back-endového fondu zadejte *myBackEndPool*.
    - Pro možnost **Virtuální síť** v rozevírací nabídce klikněte na *myVNet*.
    - Pro možnosti **Virtuální počítač** a **IP adresa** zadejte *myVM1* a *myVM2* a jejich odpovídající veřejné IP adresy.
4. Klikněte na tlačítko **Add** (Přidat).
5. Zkontrolujte, že se oba virtuální počítače (**myVM1** a **myVM2**) zobrazují v nastavení back-endového fondu vašeho nástroje pro vyrovnávání zatížení.
 
    ![Vytvoření back-endového fondu](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

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

   ![Přidání testu](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce provozu do virtuálních počítačů. Nadefinujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Vytvořte pravidlo nástroje pro vyrovnávání zatížení *myLoadBalancerRuleWeb* pro naslouchání na portu 80 ve front-endu *FrontendLoadBalancer* a odesílání síťového provozu s vyrovnáváním zatížení do back-endového fondu adres *myBackEndPool* rovněž na portu 80. 

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
    
    ![Přidání pravidla vyrovnávání zatížení](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení
1. Na obrazovce **Přehled** vyhledejte veřejnou IP adresu Load Balanceru. Klikněte na **Všechny prostředky** a pak klikněte na **myPublicIP**. 

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. V prohlížeči se zobrazí výchozí stránka, která obsahuje název stránky webového serveru.

      ![Webový server služby IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. Pokud chcete vidět nástroj pro vyrovnávání zatížení v akci, můžete vynutit zastavení zobrazeného virtuálního počítače a aktualizovat prohlížeč, aby se v něm zobrazil název dalšího serveru.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, nástroj pro vyrovnávání zatížení a všechny související prostředky. Provedete to výběrem skupiny prostředků, která obsahuje nástroj pro vyrovnávání zatížení, a kliknutím na **Odstranit**.

## <a name="next-steps"></a>Další kroky

- Další informace o [Load Balanceru úrovně Standard](load-balancer-standard-overview.md).
- [Vyrovnávání zatížení virtuálních počítačů napříč zónami dostupnosti](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
