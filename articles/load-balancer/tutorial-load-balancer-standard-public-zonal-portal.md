---
title: 'Kurz: Použití Load Balanceru pro virtuální počítače v rámci zóny – Azure Portal | Microsoft Docs'
description: V tomto kurzu si ukážeme, jak vytvořit Standard Load Balancer se zónovým front-endem, který bude vyrovnávat zatížení virtuálních počítačů v rámci zóny dostupnosti pomocí webu Azure Portal.
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
ms.date: 05/17/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 580015b7f8b1f894c69ddec0f26daeb524932e4b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637289"
---
# <a name="tutorial-load-balance-vms-within-an-availability-zone-with-standard-load-balancer-by-using-the-azure-portal"></a>Kurz: Vyrovnávání zatížení virtuálních počítačů v rámci zóny dostupnosti pomocí Standard Load Balanceru na webu Azure Portal

V tomto kurzu vytvoříte veřejnou [instanci Azure Standard Load Balanceru](https://aka.ms/azureloadbalancerstandard) se zónovým front-endem, který používá standardní veřejnou IP adresu na webu Azure Portal. V tomto scénáři zadáte určitou zónu pro instance front-endu a back-endu, aby se datové cesty a prostředky spojily s určitou zónou. Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvořit instanci Standard Load Balanceru se zónovým front-endem.
> * Vytvořit skupiny zabezpečení sítě pro definování pravidel příchozího provozu.
> * Vytvořit zónové virtuální počítače a připojit je k nástroji pro vyrovnávání zatížení.
> * Vytvořit sondu stavu k nástroji pro vyrovnávání zatížení.
> * Vytvořit pravidla provozu nástroje pro vyrovnávání zatížení.
> * Vytvořit základní web Internetové informační služby (IIS).
> * Zobrazit nástroj pro vyrovnávání zatížení v akci.

Další informace o používání zón dostupnosti se Standard Load Balancerem najdete v tématu [Standard Load Balancer a zóny dostupnosti](load-balancer-standard-availability-zones.md).

K tomuto kurzu také můžete použít [Azure CLI](load-balancer-standard-public-zonal-cli.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-standard-load-balancer-instance"></a>Vytvoření veřejné instance Standard Load Balanceru

Standard Load Balancer podporuje jenom standardní veřejnou IP adresu. Když při vytváření nástroje pro vyrovnávání zatížení vytvoříte novou veřejnou IP adresu, automaticky se nakonfiguruje jako standardní verze SKU a bude automaticky zónově redundantní.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek** > **Sítě** > **Load Balancer**.
2. Na stránce **Vytvořit nástroj pro vyrovnávání zatížení** zadejte pro nástroj pro vyrovnávání zatížení tyto hodnoty:
    - **myLoadBalancer** – název nástroje pro vyrovnávání zatížení.
    - **Public** – typ nástroje pro vyrovnávání zatížení.
     - **myPublicIPZonal** – nová veřejná IP adresa, kterou vytvoříte. Vyberte **Zvolte veřejnou IP adresu**. Potom vyberte, že chcete **vytvořit novou** adresu. Do názvu zadejte **myPublicIP**. SKU je ve výchozím nastavení standardní. V poli **Zóna dostupnosti** vyberte **Zóna 1**.
    - **myResourceGroupZLB** – název nové skupiny prostředků, kterou vytvoříte.
    - **westeurope** – umístění.
3. Stisknutím tlačítka **Vytvořit** vytvořte nástroj pro vyrovnávání zatížení.
   
    ![Vytvoření instance Standard Load Balanceru na webu Azure Portal](./media/tutorial-load-balancer-standard-zonal-portal/create-load-balancer-zonal-frontend.png)


## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části vytvoříte virtuální síť. Vytvoříte také dva virtuální počítače ve stejné zóně (konkrétně v zóně 1) oblasti a přidáte je do back-endového fondu nástroje pro vyrovnávání zatížení. Potom na virtuální počítače nainstalujete službu IIS, abyste mohli zónově redundantní nástroj pro vyrovnávání zatížení otestovat. Pokud jeden virtuální počítač selže, ohlásí chybu i sonda stavu virtuálního počítače ve stejné zóně. K zajištění provozu se použijí další virtuální počítače ve stejné zóně.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek** > **Sítě** > **Virtuální síť**.  Zadejte tyto hodnoty virtuální sítě:
    - **myVNet** – název virtuální sítě.
    - **myResourceGroupZLB** – název existující skupiny prostředků.
    - **myBackendSubnet** – název podsítě.
2. Volbou **Vytvořit** vytvořte virtuální síť.

    ![Vytvoření virtuální sítě](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-network.png)

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek**. Do vyhledávacího pole zadejte **Skupina zabezpečení sítě**. Na stránce skupiny zabezpečení sítě vyberte **Vytvořit**.
2. Na stránce **Vytvořit skupinu zabezpečení sítě** zadejte tyto hodnoty:
    - **myNetworkSecurityGroup** – název skupiny zabezpečení sítě.
    - **myResourceGroupLBAZ** – název existující skupiny prostředků.
   
    ![Vytvoření skupiny zabezpečení sítě](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>Vytvoření pravidel skupiny zabezpečení sítě

V této části vytvoříte na webu Azure Portal pravidla NSG, která povolí příchozí připojení využívající protokoly HTTP a Microsoft Remote Desktop Protocol (RDP).

1. Z nabídky na webu Azure Portal, která je úplně vlevo, vyberte **Všechny prostředky**. Najděte skupinu **myNetworkSecurityGroup** a vyberte ji. Virtuální počítač se nachází ve skupině prostředků **myResourceGroupZLB**.
2. V části **Nastavení** vyberte **Příchozí pravidla zabezpečení**. Pak vyberte **Přidat**.
3. Zadejte tyto hodnoty pro příchozí pravidlo zabezpečení s názvem **myHTTPRule** a povolte příchozí připojení HTTP přes port 80:
    - **Zdroj** – zadejte **Značka služby**.
    - **Značka zdrojové služby** – zadejte **Internet**.
    - **Rozsahy cílových portů** – zadejte **80**.
    - **Protokol** – zadejte **vTCP**.
    - **Akce** – zadejte **Povolit**.
    - **Priorita** – zadejte **100**.
    - **Název** – zadejte **myHTTPRule**.
    - **Popis** – zadejte **Povolení protokolu HTTP**.
4. Vyberte **OK**.
 
 ![Vytvoření pravidel skupiny zabezpečení sítě](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)

5. Opakováním kroků 2–4 vytvořte další pravidlo s názvem **myRDPRule**. Toto pravidlo umožní příchozí připojení RDP, které používá port 3389 a má následující hodnoty:
    - **Zdroj** – zadejte **Značka služby**.
    - **Značka zdrojové služby** – zadejte **Internet**.
    - **Rozsahy cílových portů** – zadejte **3389**.
    - **Protokol** – zadejte **TCP**.
    - **Akce** – zadejte **Povolit**.
    - **Priorita** – zadejte **200**.
    - **Název** – zadejte **myRDPRule**.
    - **Popis** – zadejte **Povolení protokolu RDP**.

    ![Vytvoření pravidla RDP](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek** > **Compute** > **Windows Server 2016 Datacenter**. Zadejte pro virtuální počítač tyto hodnoty:
    - **myVM1** – název virtuálního počítače.        
    - **azureuser** – uživatelské jméno správce.    
    - **myResourceGroupZLB** – název **skupiny prostředků**. Vyberte **Použít existující** a pak vyberte **myResourceGroupZLB**.
2. Vyberte **OK**.
3. Velikost virtuálního počítače vyberte **DS1_V2**. Zvolte **Vybrat**.
4. Zadejte následující hodnoty nastavení virtuálního počítače:
    - **zóna 1** – zóna dostupnosti, do které umístíte virtuální počítač.
    -  **myVNet** – zkontrolujte, že je vybraná tato virtuální síť.
    - **myVM1PIP** – standardní veřejná IP adresa, kterou vytvoříte. Vyberte, že chcete **vytvořit novou** IP adresu. Pak do názvu zadejte **myVM1PIP**. Jako **zónu** vyberte **1**. SKU IP adresy je ve výchozím nastavení standardní.
    - **myBackendSubnet** – zkontrolujte, že je vybraná tato podsíť.
    - **myNetworkSecurityGroup** – název existující skupiny zabezpečení sítě (brány firewall).
5. Vybráním možnosti **Zakázáno** zakažte diagnostiku spouštění.
6. Vyberte **OK**. Zkontrolujte nastavení na souhrnné stránce. Potom vyberte **Vytvořit**.
7. Opakováním kroků 1 až 6 vytvořte v zóně 1 druhý virtuální počítač s názvem **myVM2**. Jako virtuální síť zadejte **myVnet**. Jako standardní veřejnou IP adresu zadejte **myVM2PIP**. Jako podsíť zadejte **myBackendSubnet**. Jako skupinu zabezpečení sítě zadejte **myNetworkSecurityGroup**.

    ![Vytvoření virtuálních počítačů](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>Instalace služby IIS na virtuální počítače

1. V nabídce úplně vlevo vyberte **Všechny prostředky**. V seznamu prostředků vyberte **myVM1**. Virtuální počítač se nachází ve skupině prostředků **myResourceGroupZLB**.
2. Na stránce **Přehled** vyberte **Připojit** a pro připojení k virtuálnímu počítači použijte protokol RDP.
3. Přihlaste se k virtuálnímu počítači pod uživatelským jménem a heslem, které jste zadali při vytváření virtuálního počítače. Pokud chcete zadat přihlašovací údaje, které jste zadali při vytvoření virtuálního počítače, možná budete muset vybrat **další možnosti**. Dále vyberte **Použít jiný účet**. A pak vyberte **OK**. Při přihlášení se může zobrazit upozornění na certifikát. Vyberte **Ano** a pokračujte v připojování.
4. Na ploše serveru přejděte do části **Nástroje pro správu Windows** > **Windows PowerShell**.
6. V okně **PowerShell** spusťte následující příkazy, kterými nainstalujete server IIS. Těmito příkazy také odeberete výchozí soubor iisstart.htm a přidáte nový soubor iisstart.htm, který zobrazuje název virtuálního počítače:

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
7. Ukončete relaci RDP s **myVM1**.
8. Opakováním kroků 1 až 7 nainstalujte službu IIS na **myVM2**.

## <a name="create-load-balancer-resources"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení

V této části nakonfigurujete nastavení nástroje pro vyrovnávání zatížení pro fond back-endových adres a sondu stavu. Zadáte také nástroj pro vyrovnávání zatížení a pravidla překladu síťových adres.


### <a name="create-a-backend-address-pool"></a>Vytvoření fondu back-endových adres

Aby bylo možné distribuovat provoz do virtuálních počítačů, obsahuje fond back-endových adres IP adresy adaptérů virtuální sítě připojených k nástroji pro vyrovnávání zatížení. Vytvořte fond back-endových adres **myBackendPool**, který zahrnuje virtuální počítače **VM1** a **VM2**.

1. V nabídce úplně vlevo vyberte **Všechny prostředky**. Ze seznamu prostředků vyberte **myLoadBalancer**.
2. V části **Nastavení** vyberte **Back-endové fondy**. Pak vyberte **Přidat**.
3. Na stránce **Přidat back-endový fond** proveďte tyto akce:
    - Jako název back-endového fondu zadejte **myBackEndPool**.
    - V rozevírací nabídce **Virtuální síť** vyberte **myVNet**. 
    - V polích **Virtuální počítač** a **IP adresa** přidejte **myVM1**, **myVM2** a jejich odpovídající veřejné IP adresy.
4. Vyberte **Přidat**.
5. Zkontrolujte, že se v nastavení back-endového fondu vašeho nástroje pro vyrovnávání zatížení zobrazují oba virtuální počítače (**myVM1** a **myVM2**).
 
    ![Vytvoření back-endového fondu](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Sonda stavu slouží monitorování stavu vaší aplikace nástrojem pro vyrovnávání zatížení. Sonda stavu dynamicky přidává virtuální počítače do oběhu nástroje pro vyrovnávání zatížení nebo je z něj odebírá na základě jejich reakce na kontroly stavu. Vytvořte sondu stavu **myHealthProbe** pro monitorování stavu virtuálních počítačů.

1. V nabídce úplně vlevo vyberte **Všechny prostředky**. Ze seznamu prostředků vyberte **myLoadBalancer**.
2. V části **Nastavení** vyberte **Sondy stavu**. Pak vyberte **Přidat**.
3. K vytvoření sondy stavu použijte tyto hodnoty:
    - **myHealthProbe** – název sondy stavu.
    - **HTTP** – typ protokolu.
    - **80** – číslo portu.
    - **15** – **interval** mezi pokusy o testování sondy v sekundách.
    - **2** – **prahová hodnota špatného stavu** neboli počet opakovaných selhání sondy, ke kterým musí dojít, aby byl virtuální počítač považován za poškozený.
4. Vyberte **OK**.

   ![Přidání sondy stavu](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje, jak se provoz distribuuje do virtuálních počítačů. Definujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Vytvořte pravidlo nástroje pro vyrovnávání zatížení **myLoadBalancerRuleWeb**, které naslouchá portu 80 front-endu **FrontendLoadBalancer**. Toto pravidlo posílá vyrovnaný síťový provoz fondu back-endových adres **myBackEndPool**. Používá k tomu také port 80. 

1. V nabídce úplně vlevo vyberte **Všechny prostředky**. Ze seznamu prostředků vyberte **myLoadBalancer**.
2. V části **Nastavení** vyberte **Pravidla vyrovnávání zatížení**. Pak vyberte **Přidat**.
3. Ke konfiguraci pravidla vyrovnávání zatížení použijte tyto hodnoty:
    - **myHTTPRule** – název pravidla vyrovnávání zatížení.
    - **TCP** – typ protokolu.
    - **80** – číslo portu.
    - **80** – číslo back-endového portu.
    - **myBackendPool** – název backendového fondu.
    - **myHealthProbe** – název sondy stavu.
4. Vyberte **OK**.
    
    ![Přidání pravidla vyrovnávání zatížení](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení
1. Na obrazovce **Přehled** vyhledejte veřejnou IP adresu nástroje pro vyrovnávání zatížení. Vyberte **Všechny prostředky**. Pak vyberte **myPublicIP**. 

2. Zkopírujte veřejnou IP adresu a vložte ji do adresního řádku prohlížeče. V prohlížeči se zobrazí výchozí stránka, která obsahuje název stránky webového serveru.

      ![Webový server služby IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. Pokud se chcete podívat, jak funguje nástroj pro vyrovnávání zatížení, zastavte zobrazený virtuální počítač a aktualizujte prohlížeč, aby se v něm zobrazil název druhého serveru.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, nástroj pro vyrovnávání zatížení a všechny další související prostředky. Vyberte skupinu prostředků, která obsahuje nástroj pro vyrovnávání zatížení. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

- Další informace o [Load Balanceru úrovně Standard](load-balancer-standard-overview.md).
- [Vyrovnávání zatížení virtuálních počítačů mezi zónami dostupnosti](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
