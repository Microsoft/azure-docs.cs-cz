---
title: Virtuální počítače nástroje pro vyrovnávání zatížení v rámci zóny dostupnosti - portálu Azure | Microsoft Docs
description: Vytvořte standardní Vyrovnávání zatížení s zónově redundantní front-endu vyrovnávat virtuálních počítačů mezi různými portálu Azure pomocí pásmy dostupnost zatížení
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/18
ms.author: kumud
ms.openlocfilehash: ad476922342844a908961960407eb344711932f5
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>Nástroj pro vyrovnávání zatížení virtuálních počítačů mezi různými dostupnosti pásmy standardní pro vyrovnávání zatížení pomocí portálu Azure

Tento postup článku procesem vytvoření standardní veřejné zatížení na vyrovnávání s redundantní front-end zóny k dosažení dosáhnout zálohování zóny bez závislosti na více záznamů DNS. Jediné front-end IP adresy v standardní Vyrovnávání zatížení je automaticky zónově redundantní. Používat redundantní front-endu zóny pro nástroj pro vyrovnávání zatížení, s jedinou IP adresu můžete nyní dosáhnout žádné virtuální počítače ve virtuální síti v rámci oblasti, která je v rámci všech zón dostupnosti. Zóny dostupnosti se používají k ochraně aplikací a dat před málo pravděpodobným selháním nebo ztrátou celého datového centra. Pomocí zálohování zóny jednu nebo více zón dostupnosti může selhat a cestu k datům odolává stejně dlouho jako jednu zónu v i nadále oblast, která je v pořádku. 

Další informace o použití zón dostupnosti se nástroj pro vyrovnávání zatížení najdete v tématu [nástroj pro vyrovnávání zatížení a dostupnosti zón](load-balancer-standard-availability-zones.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-standard-load-balancer"></a>Vytvořte veřejný Standard pro vyrovnávání zatížení

Load Balancer úrovně Standard podporuje pouze standardní veřejnou IP adresu. Když vytvoříte novou veřejnou IP adresu při vytváření nástroje pro vyrovnávání zatížení, se automaticky nakonfiguruje jako standardní SKU verze který je taky automaticky zónově redundantní.

1. V levém horním rohu obrazovky klikněte na **Vytvořit prostředek** > **Sítě** > **Load Balancer**.
2. Na stránce **Vytvoření nástroje pro vyrovnávání zatížení** zadejte pro nástroj pro vyrovnávání zatížení tyto hodnoty:
    - *myLoadBalancer* – název nástroje pro vyrovnávání zatížení.
    - **Public** – typ nástroje pro vyrovnávání zatížení.
     - *myPublicIP* – pro novou veřejnou IP adresu, kterou vytvoříte. Chcete-li to provést, klikněte na tlačítko **zvolte veřejnou IP adresu**a potom klikněte na **vytvořit nový**. Název typu *myPublicIP*, SKU je ve výchozím nastavení a vyberte možnost Standard **Zónově redundantní** pro **dostupnost zóny**.
    - *myResourceGroupLBAZ* – název nové skupiny prostředků, které vytvoříte.
    - **westeurope** – umístění.
3. Kliknutím na **Vytvořit** vytvořte nástroj pro vyrovnávání zatížení.
   
    ![Vytvoření nástroje pro vyrovnávání zatížení](./media/load-balancer-standard-public-availability-zones-portal/1a-load-balancer.png)


## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části můžete vytvořit virtuální síť, vytvoření virtuálních počítačů v různých zóny (zóny 1, 2 zóně a zóny 3) pro oblasti, kterou chcete přidat do back-endový fond Vyrovnávání zatížení, a potom nainstalujte IIS na virtuálních počítačích můžete otestovat Předv zatížení zónově redundantní ncer. Proto pokud zónu selže, selže test stavu virtuálního počítače ve stejné zóně a dál provoz rutinou virtuálních počítačů v jiné zóně.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
1. Na levé straně horní části obrazovky klikněte na tlačítko **vytvořit prostředek** > **sítě** > **virtuální síť** a zadejte tyto hodnoty virtuální sítě:
    - *myVnet* – název virtuální sítě.
    - *myResourceGroupLBAZ* – pro název skupiny prostředků
    - *myBackendSubnet* – název podsítě.
2. Kliknutím na **Vytvořit** vytvořte virtuální síť.

    ![Vytvoření virtuální sítě](./media/load-balancer-standard-public-availability-zones-portal/2-load-balancer-virtual-network.png)

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

1. Na levé straně horní části obrazovky, klikněte na tlačítko **vytvořit prostředek**, do pole vyhledávání zadejte *skupinu zabezpečení sítě*a na stránce skupiny zabezpečení sítě, klikněte na tlačítko **vytvořit**.
2. Na stránce skupiny zabezpečení sítě vytvořit, zadejte tyto hodnoty:
    - *myNetworkSecurityGroup* – pro název skupiny zabezpečení sítě.
    - *myResourceGroupLBAZ* – pro název skupiny prostředků.
   
![Vytvoření virtuální sítě](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-nsg-rules"></a>Vytvoření pravidel skupiny zabezpečení sítě

V této části vytvoříte pravidla NSG umožňuje příchozí připojení pomocí protokolu HTTP a RDP pomocí portálu Azure.

1. Na portálu Azure klikněte na tlačítko **všechny prostředky** v levé nabídce a pak hledání a klikněte na **myNetworkSecurityGroup** který je umístěný v **myResourceGroupLBAZ** Skupina prostředků.
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


### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

1. Na levé straně horní části obrazovky, klikněte na tlačítko **vytvořit prostředek** > **výpočetní** > **Windows Server 2016 Datacenter** a zadejte tyto hodnoty pro virtuální počítač:
    - *myVM1* – název virtuálního počítače.        
    - *azureuser* – uživatelské jméno správce.    
    - *myResourceGroupLBAZ* – **skupiny prostředků**, vyberte **použít existující**a potom vyberte *myResourceGroupLBAZ*.
2. Klikněte na **OK**.
3. Vyberte velikost virtuálního počítače **DS1_V2** a klikněte na **Vybrat**.
4. Zadejte následující hodnoty nastavení virtuálního počítače:
    - *zóny 1* – pro zónu, kam umístit virtuální počítač.
    -  *myVNet* – ujistěte se, že je vybraná tato virtuální síť.
    - *myBackendSubnet* – ujistěte se, že je vybraná tato podsíť.
    - *myNetworkSecurityGroup* – pro název skupiny zabezpečení sítě (brány firewall).
5. Kliknutím na **Zakázáno** zakažte diagnostiku spouštění.
6. Klikněte na **OK**, na stránce souhrnu zkontrolujte nastavení a pak klikněte na **Vytvořit**.
  
 ![Vytvoření virtuálního počítače](./media/load-balancer-standard-public-availability-zones-portal/create-vm-standard-ip.png)

7. Vytvořit druhý virtuální počítač s názvem, *virtuálního počítače 2* v zóně 2 a třetí virtuálního počítače v zóně 3 a s *myVnet* jako virtuální síť, *myBackendSubnet* jako podsíť, a * *myNetworkSecurityGroup* jako skupinu zabezpečení sítě pomocí kroků 1 až 6.

### <a name="install-iis-on-vms"></a>Instalace služby IIS na virtuálních počítačích

1. Klikněte na tlačítko **všechny prostředky** klikněte v levé nabídce a potom v seznamu prostředků **myVM1** který je umístěný v *myResourceGroupLBAZ* skupinu prostředků.
2. Na stránce **Přehled** klikněte na **Připojit** a připojte se přes RDP k virtuálnímu počítači.
3. Přihlaste se k virtuálnímu počítači s použitím uživatelského jména *azureuser*.
4. Na ploše serveru přejděte do části **Nástroje pro správu Windows**>**Správce serveru**.
5. Na stránce Rychlý start správce serveru klikněte na tlačítko **přidat role a funkce**.

   ![Přidání do back-endového fondu adres – ](./media/load-balancer-standard-public-availability-zones-portal/servermanager.png)    

1. V **Průvodci přidáním rolí a funkcí** použijte následující hodnoty:
    - Na stránce **Výběr typu instalace** klikněte na **Instalace na základě role nebo funkce**.
    - V **vybrat cílový server** klikněte na tlačítko **myVM1**.
    - V **role serveru vyberte** klikněte na tlačítko **webového serveru (IIS)**.
    - Postupujte podle pokynů dokončete průvodce.
2. Ukončení relace RDP s virtuálním počítačem - *myVM1*.
3. Opakujte kroky 1 až 7 instalace služby IIS na virtuálních počítačích *Můjvp2* a *myVM3*.

## <a name="create-load-balancer-resources"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení

V této části nakonfigurujete nastavení nástroje pro vyrovnávání zatížení pro fond back-endových adres a sondu stavu a zadáte pravidla nástroje pro vyrovnávání zatížení a překladu adres.


### <a name="create-a-backend-address-pool"></a>Vytvoření fondu back-endových adres

Za účelem distribuce provozu do virtuálních počítačů obsahuje fond back-endových adres IP adresy virtuálních síťových karet připojených k nástroji pro vyrovnávání zatížení. Vytvořte fond back-endových adres *myBackendPool*, který zahrnuje virtuální počítače *VM1* a *VM2*.

1. V levé nabídce klikněte na **Všechny prostředky** a pak v seznamu prostředků klikněte na **myLoadBalancer**.
2. V části **Nastavení** klikněte na **Back-endové fondy** a pak klikněte na **Přidat**.
3. Na stránce **Přidat back-endový fond** postupujte následovně:
    - Jako název back-endového fondu zadejte myBackEndPool.
    - Pro **virtuální síť**, v rozevírací nabídce klikněte na **myVNet**
    - Pro **virtuálního počítače**, v rozevírací nabídce klikněte na tlačítko, **myVM1**.
    - Pro **IP adresu**, v rozevírací nabídce klikněte na IP adresu myVM1.
4. Klikněte na tlačítko **přidat nový prostředek back-end** přidat každý virtuální počítač (*Můjvp2* a *myVM3*) pro přidání do fondu back-end služby Vyrovnávání zatížení.
5. Klikněte na tlačítko **Add** (Přidat).

    ![Přidání do back-endového fondu adres – ](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. Zkontrolujte, vaše nastavení fondu back-end pro vyrovnávání zatížení zobrazí všechny tři virtuální počítače - **myVM1**, **Můjvp2** a **myVM3**.

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

## <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení
1. Na obrazovce **Přehled** vyhledejte veřejnou IP adresu Load Balanceru. Klikněte na **Všechny prostředky** a pak klikněte na **myPublicIP**.

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

      ![Webový server služby IIS](./media/load-balancer-standard-public-availability-zones-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, nástroj pro vyrovnávání zatížení a všechny související prostředky. Provedete to výběrem skupiny prostředků, která obsahuje nástroj pro vyrovnávání zatížení, a kliknutím na **Odstranit**.

## <a name="next-steps"></a>Další postup

Další informace o [Load Balanceru úrovně Standard](load-balancer-standard-overview.md).
