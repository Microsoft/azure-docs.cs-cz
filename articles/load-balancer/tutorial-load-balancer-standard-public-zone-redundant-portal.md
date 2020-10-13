---
title: 'Kurz: Vyrovnávání zatížení virtuálních počítačů napříč zónami dostupnosti – Azure Portal'
titleSuffix: Azure Load Balancer
description: Tento kurz ukazuje, jak na webu Azure Portal vytvořit Load Balancer úrovně Standard se zónově redundantním front-endem, který bude vyrovnávat zatížení virtuálních počítačů napříč zónami dostupnosti.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines across availability zones in a region, so that the customers can still access the web service if a datacenter is unavailable.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 4deab6fcadda36ad729096ff2f38e40ce81c7ae9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91446095"
---
# <a name="tutorial-load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>Kurz: Vyrovnávání zatížení virtuálních počítačů napříč zónami dostupnosti pomocí Load Balanceru úrovně Standard na webu Azure Portal

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti tím, že rozprostírá příchozí požadavky na více virtuálních počítačů. Tento kurz vás provede vytvořením veřejné Standard Load Balancer, který vyrovnává zatížení virtuálních počítačů napříč zónami dostupnosti. Tento proces zvyšuje ochranu aplikací a dat před málo pravděpodobným selháním nebo ztrátou celého datového centra. Díky redundanci zón zůstane cesta k datům dostupná i v případě, že dojde k selhání jedné nebo několika zón (pokud alespoň jedna zóna v oblasti zůstane v pořádku). Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření Load Balanceru úrovně Standard
> * Vytvoření skupin zabezpečení sítě pro definování pravidel pro příchozí provoz
> * Vytvoření zónově redundantních virtuálních počítačů napříč několika zónami a jejich připojení k nástroji pro vyrovnávání zatížení
> * Vytvoření sondy stavu nástroje pro vyrovnávání zatížení
> * Vytvoření pravidel provozu pro nástroj pro vyrovnávání zatížení
> * Vytvoření základního webu služby IIS
> * Zobrazení nástroje pro vyrovnávání zatížení v akci

Další informace o používání zón dostupnosti s Load Balancerem úrovně Standard najdete v tématu o [Load Balanceru úrovně Standard a zónách dostupnosti](load-balancer-standard-availability-zones.md).

Pokud chcete, můžete tento kurz absolvovat s použitím [Azure CLI](load-balancer-standard-public-zone-redundant-cli.md).

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Vytvoření Load Balanceru úrovně Standard

Load Balancer úrovně Standard podporuje pouze standardní veřejnou IP adresu. Když při vytváření nástroje pro vyrovnávání zatížení vytvoříte novou veřejnou IP adresu, automaticky se nakonfiguruje jako standardní verze SKU a je také automaticky zónově redundantní.

1. V levém horním rohu obrazovky klikněte na **vytvořit prostředek**  >  **síť**  >  **Load Balancer**.
2. Na kartě **základy** na stránce **vytvořit službu Vyrovnávání zatížení** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **vytvořit nový** a do textového pole zadejte *MyResourceGroupLBAZ* .|
    | Název                   | *myLoadBalancer*                                   |
    | Oblast         | Vyberte **Západní Evropa**.                                        |
    | Typ          | Vyberte **Veřejný**.                                        |
    | SKU           | Vyberte **Standard**.                          |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. |
    | Název veřejné IP adresy              | Do textového pole zadejte *myPublicIP* .   |
    |Zóna dostupnosti| Vyberte **zóna redundantní**.    |
   

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části vytvoříte virtuální síť a virtuální počítače v různých zónách pro danou oblast a pak na tyto virtuální počítače nainstalujete službu IIS, která vám pomůže zónově redundantní nástroj pro vyrovnávání zatížení otestovat. Pokud tedy dojde k selhání nějaké zóny, selže sonda stavu pro virtuální počítač v dané zóně a pro provoz se dále použijí virtuální počítače v jiných zónách.

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

V této části budete muset v krocích níže nahradit následující parametry:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLBAZ (vyberte existující skupinu prostředků) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | West Europe      |
| **\<IPv4-address-space>**   | 10.0.0.0/16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.0.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořte skupinu zabezpečení sítě, která definuje příchozí připojení k vaší virtuální síti.

1. V levém horním rohu obrazovky klikněte na **Vytvořit prostředek**, do vyhledávacího pole zadejte *Skupina zabezpečení sítě* a na stránce Skupina zabezpečení sítě klikněte na **Vytvořit**.
2. Na stránce Vytvořit skupinu zabezpečení sítě zadejte tyto hodnoty:
    - *myNetworkSecurityGroup* – název skupiny zabezpečení sítě.
    - *myResourceGroupLBAZ* – název existující skupiny prostředků.
   
![Snímek obrazovky se zobrazí v podokně vytvořit skupinu zabezpečení sítě.](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-network-security-group-rules"></a>Vytvoření pravidel skupiny zabezpečení sítě

V této části vytvoříte pravidla skupiny zabezpečení sítě, která povolí příchozí připojení pomocí protokolů HTTP a RDP na webu Azure Portal.

1. Na webu Azure Portal v levé nabídce klikněte na **Všechny prostředky** a pak vyhledejte a vyberte **myNetworkSecurityGroup** ve skupině prostředků **myResourceGroupLBAZ**.
2. V části **Nastavení** klikněte na **Příchozí pravidla zabezpečení** a pak klikněte na **Přidat**.
3. Zadáním následujících hodnot pro příchozí pravidlo zabezpečení *myHTTPRule* povolte příchozí připojení HTTP na portu 80:
    - Jako **Zdroj** zadejte *Značka služby*.
    - Jako **Značka zdrojové služby** zadejte *Internet*.
    - Jako **Rozsahy cílových portů** zadejte *80*.
    - Jako **Protokol** zadejte *TCP*.
    - Jako **Akce** zadejte *Povolit*.
    - Jako **Priorita** zadejte *100*.
    - Jako název pravidla nástroje pro vyrovnávání zatížení zadejte *myHTTPRule*.
    - Jako popis pravidla nástroje pro vyrovnávání zatížení zadejte *Povolení protokolu HTTP*.
4. Klikněte na **OK**.
 
   ![Snímek obrazovky se zobrazí v podokně Přidat příchozí pravidlo zabezpečení.](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)
5. Zopakováním kroků 2 až 4 vytvořte další pravidlo *myRDPRule*, které povolí příchozí připojení RDP na portu 3389, s použitím následujících hodnot:
    - Jako **Zdroj** zadejte *Značka služby*.
    - Jako **Značka zdrojové služby** zadejte *Internet*.
    - Jako **Rozsahy cílových portů** zadejte *3389*.
    - Jako **Protokol** zadejte *TCP*.
    - Jako **Akce** zadejte *Povolit*.
    - Jako **Priorita** zadejte *200*.
    - Jako název zadejte *myRDPRule*.
    - Jako popis zadejte *Povolení protokolu RDP*.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte v různých zónách (zóny 1, 2 a 3) pro danou oblast virtuální počítače, které můžou fungovat jako servery back-end pro nástroj pro vyrovnávání zatížení.

1. V levém horním rohu obrazovky klikněte na **vytvořit prostředek**  >  **COMPUTE**  >  **Windows Server 2016 Datacenter** a zadejte tyto hodnoty pro virtuální počítač:
    - *myVM1* – název virtuálního počítače.        
    - *azureuser* – uživatelské jméno správce.    
    - *myResourceGroupLBAZ* – v části **Skupina prostředků** vyberte **Použít existující** a pak vyberte *myResourceGroupLBAZ*.
2. Klikněte na **OK**.
3. Vyberte velikost virtuálního počítače **DS1_V2** a klikněte na **Vybrat**.
4. Zadejte následující hodnoty nastavení virtuálního počítače:
    - *zone 1* – zóna, do které umístíte virtuální počítač.
    -  *myVNet* – ujistěte se, že je vybraná tato virtuální síť.
    - *myBackendSubnet* – ujistěte se, že je vybraná tato podsíť.
    - *myNetworkSecurityGroup* – název skupiny zabezpečení sítě (brána firewall).
5. Kliknutím na **Zakázáno** zakažte diagnostiku spouštění.
6. Klikněte na **OK**, na stránce souhrnu zkontrolujte nastavení a pak klikněte na **Vytvořit**.
7. Podle kroků 1 až 6 vytvořte druhý virtuální počítač s názvem *VM2* v zóně 2 a třetí virtuální počítač v zóně 3, s virtuální sítí *myVnet*, podsítí *myBackendSubnet* a skupinou zabezpečení sítě **myNetworkSecurityGroup*.

### <a name="install-iis-on-vms"></a>Instalace služby IIS na virtuální počítače

1. V levé nabídce klikněte na **Všechny prostředky** a pak v seznamu prostředků klikněte na **myVM1** ve skupině prostředků *myResourceGroupLBAZ*.
2. Na stránce **Přehled** klikněte na **Připojit** a připojte se přes RDP k virtuálnímu počítači.
3. Přihlaste se k virtuálnímu počítači s použitím uživatelského jména *azureuser*.
4. Na ploše serveru přejděte do části **Nástroje pro správu Windows** > **Windows PowerShell**.
5. V okně PowerShellu spuštěním následujících příkazů nainstalujte server služby IIS, odeberte výchozí soubor iisstart.htm a pak přidejte nový soubor iisstart.htm, který zobrazuje název virtuálního počítače:
   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
6. Zavřete relaci RDP pomocí *myVM1*.
7. Opakováním kroků 1 až 6 nainstalujte službu IIS a aktualizovaný soubor iisstart.htm na *myVM2* a *myVM3*.

## <a name="create-load-balancer-resources"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení

V této části nakonfigurujete nastavení nástroje pro vyrovnávání zatížení pro fond back-endových adres a sondu stavu a zadáte pravidla nástroje pro vyrovnávání zatížení a překladu adres.


### <a name="create-a-backend-address-pool"></a>Vytvoření fondu back-endových adres

Za účelem distribuce provozu do virtuálních počítačů obsahuje fond back-endových adres IP adresy virtuálních síťových karet připojených k nástroji pro vyrovnávání zatížení. Vytvořte fond back-endových adres *myBackendPool*, který zahrnuje virtuální počítače *VM1*, *VM2* a *VM3*.

1. V levé nabídce klikněte na **Všechny prostředky** a pak v seznamu prostředků klikněte na **myLoadBalancer**.
2. V části **Nastavení** klikněte na **Back-endové fondy** a pak klikněte na **Přidat**.
3. Na stránce **Přidat back-endový fond** postupujte následovně:
    - Do pole název zadejte *myBackEndPool*jako název vašeho back-end fondu.
    - Pro možnost **Virtuální síť** v rozevírací nabídce klikněte na **myVNet**.
    - Pro možnost **Virtuální počítač** v rozevírací nabídce klikněte na **myVM1**.
    - Pro možnost **IP adresa** v rozevírací nabídce klikněte na IP adresu myVM1.
4. Kliknutím na **Přidat nový back-endový prostředek** přidejte jednotlivé virtuální počítače (*myVM2* a *myVM3*), které chcete přidat do back-endového fondu nástroje pro vyrovnávání zatížení.
5. Klikněte na **Přidat**.

    ![Přidání do back-endového fondu adres –](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. Zkontrolujte, že se v nastavení back-endového fondu vašeho nástroje pro vyrovnávání zatížení zobrazují všechny tři virtuální počítače – **myVM1**, **myVM2** a **myVM3**.

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
    
    
    ![Přidání pravidla vyrovnávání zatížení](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testování Load Balanceru
1. Na obrazovce **Přehled** vyhledejte veřejnou IP adresu Load Balanceru. Klikněte na **Všechny prostředky** a pak klikněte na **myPublicIP**.

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

      ![Webový server služby IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Pokud chcete zobrazit distribuci provozu nástrojem pro vyrovnávání zatížení mezi virtuálními počítači distribuovanými v zóně, můžete vynutit aktualizaci webového prohlížeče.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, nástroj pro vyrovnávání zatížení a všechny související prostředky. Provedete to výběrem skupiny prostředků, která obsahuje nástroj pro vyrovnávání zatížení, a kliknutím na **Odstranit**.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [Standard Load Balancer](load-balancer-standard-overview.md).
