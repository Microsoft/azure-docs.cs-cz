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
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>Nástroj pro vyrovnávání zatížení virtuálních počítačů mezi různými dostupnosti pásmy standardní pro vyrovnávání zatížení pomocí portálu Azure

Tento postup článku procesem vytvoření standardní veřejné zatížení na vyrovnávání s redundantní front-end zóny k dosažení dosáhnout zálohování zóny bez závislosti na více záznamů DNS. Jediné front-end IP adresy v standardní Vyrovnávání zatížení je automaticky zónově redundantní. Používat redundantní front-endu zóny pro nástroj pro vyrovnávání zatížení, s jedinou IP adresu můžete nyní dosáhnout žádné virtuální počítače ve virtuální síti v rámci oblasti, která je v rámci všech zón dostupnosti. Zóny dostupnosti se používají k ochraně aplikací a dat před málo pravděpodobným selháním nebo ztrátou celého datového centra. Pomocí zálohování zóny jednu nebo více zón dostupnosti může selhat a cestu k datům odolává stejně dlouho jako jednu zónu v i nadále oblast, která je v pořádku. 

Další informace o použití zón dostupnosti se nástroj pro vyrovnávání zatížení najdete v tématu [nástroj pro vyrovnávání zatížení a dostupnosti zón](load-balancer-standard-availability-zones.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-standard-load-balancer"></a>Vytvořte veřejný Standard pro vyrovnávání zatížení

Nástroj pro vyrovnávání zatížení podporuje pouze standardní veřejnou IP adresu. Když vytvoříte novou veřejnou IP adresu při vytváření nástroje pro vyrovnávání zatížení, se automaticky nakonfiguruje jako standardní SKU verze který je taky automaticky zónově redundantní.

1. Na levé straně horní části obrazovky, klikněte na tlačítko **vytvořit prostředek** > **sítě** > **nástroj pro vyrovnávání zatížení**.
2. V **vytvořit nástroj pro vyrovnávání zatížení** stránky, zadejte tyto hodnoty pro vyrovnávání zatížení:
    - *myLoadBalancer* – pro název služby Vyrovnávání zatížení.
    - **Veřejné** – pro typ služby Vyrovnávání zatížení.
     - *myPublicIP* – pro novou veřejnou IP adresu, kterou vytvoříte. Chcete-li to provést, klikněte na tlačítko **zvolte veřejnou IP adresu**a potom klikněte na **vytvořit nový**. Název typu *myPublicIP*, SKU je ve výchozím nastavení a vyberte možnost Standard **Zónově redundantní** pro **dostupnost zóny**.
    - *myResourceGroupLBAZ* – název nové skupiny prostředků, které vytvoříte.
    - **westeurope** – pro umístění.
3. Klikněte na tlačítko **vytvořit** vytvořit nástroj pro vyrovnávání zatížení.
   
    ![Vytvoření nástroje pro vyrovnávání zatížení](./media/load-balancer-standard-public-availability-zones-portal/1a-load-balancer.png)


## <a name="create-backend-servers"></a>Vytvoření back-end serverů

V této části můžete vytvořit virtuální síť, vytvoření virtuálních počítačů v různých zóny (zóny 1, 2 zóně a zóny 3) pro oblasti, kterou chcete přidat do back-endový fond Vyrovnávání zatížení, a potom nainstalujte IIS na virtuálních počítačích můžete otestovat Předv zatížení zónově redundantní ncer. Proto pokud zónu selže, selže test stavu virtuálního počítače ve stejné zóně a dál provoz rutinou virtuálních počítačů v jiné zóně.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
1. Na levé straně horní části obrazovky klikněte na tlačítko **vytvořit prostředek** > **sítě** > **virtuální síť** a zadejte tyto hodnoty virtuální sítě:
    - *myVnet* – pro název virtuální sítě.
    - *myResourceGroupLBAZ* – pro název skupiny prostředků
    - *myBackendSubnet* – název podsítě.
2. Klikněte na tlačítko **vytvořit** pro vytvoření virtuální sítě.

    ![Vytvoření virtuální sítě](./media/load-balancer-standard-public-availability-zones-portal/2-load-balancer-virtual-network.png)

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

1. Na levé straně horní části obrazovky, klikněte na tlačítko **vytvořit prostředek**, do pole vyhledávání zadejte *skupinu zabezpečení sítě*a na stránce skupiny zabezpečení sítě, klikněte na tlačítko **vytvořit**.
2. Na stránce skupiny zabezpečení sítě vytvořit, zadejte tyto hodnoty:
    - *myNetworkSecurityGroup* – pro název skupiny zabezpečení sítě.
    - *myResourceGroupLBAZ* – pro název skupiny prostředků.
   
![Vytvoření virtuální sítě](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-nsg-rules"></a>Vytvoření pravidla NSG

V této části vytvoříte pravidla NSG umožňuje příchozí připojení pomocí protokolu HTTP a RDP pomocí portálu Azure.

1. Na portálu Azure klikněte na tlačítko **všechny prostředky** v levé nabídce a pak hledání a klikněte na **myNetworkSecurityGroup** který je umístěný v **myResourceGroupLBAZ** Skupina prostředků.
2. V části **nastavení**, klikněte na tlačítko **příchozí pravidla zabezpečení**a potom klikněte na **přidat**.
3. Zadejte tyto hodnoty s názvem pravidla zabezpečení příchozích *myHTTPRule* povolit pro příchozí připojení protokolu HTTP, na portu 80 pomocí:
    - *Služba značka* – **zdroj**.
    - *Internet* – **zdroje služby značky**
    - *80* – **rozsahy cílový port**
    - *TCP* – **protokolu**
    - *Povolit* – **akce**
    - *100* pro **s prioritou**
    - *myHTTPRule* pro název
    - *Povolit HTTP* – popis
4. Klikněte na **OK**.
 
 ![Vytvoření virtuální sítě](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)
5. Opakujte kroky 2 až 4 vytvořit jiné pravidlo s názvem *myRDPRule* povolit pro příchozí připojení RDP pomocí portu 3389 s následujícími hodnotami:
    - *Služba značka* – **zdroj**.
    - *Internet* – **zdroje služby značky**
    - *3389* – **rozsahy cílový port**
    - *TCP* – **protokolu**
    - *Povolit* – **akce**
    - *200* pro **s prioritou**
    - *myRDPRule* pro název
    - *Povolit RDP* – popis


### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

1. Na levé straně horní části obrazovky, klikněte na tlačítko **vytvořit prostředek** > **výpočetní** > **Windows Server 2016 Datacenter** a zadejte tyto hodnoty pro virtuální počítač:
    - *myVM1* – pro název virtuálního počítače.        
    - *azureuser* – pro uživatelské jméno správce.    
    - *myResourceGroupLBAZ* – **skupiny prostředků**, vyberte **použít existující**a potom vyberte *myResourceGroupLBAZ*.
2. Klikněte na **OK**.
3. Vyberte **DS1_V2** pro velikost virtuálního počítače, a klikněte na tlačítko **vyberte**.
4. Zadejte tyto hodnoty pro nastavení virtuálního počítače:
    - *zóny 1* – pro zónu, kam umístit virtuální počítač.
    -  *myVNet* -zkontrolujte je vybraný jako virtuální síť.
    - *myBackendSubnet* -zkontrolujte je vybraný jako podsítě.
    - *myNetworkSecurityGroup* – pro název skupiny zabezpečení sítě (brány firewall).
5. Klikněte na tlačítko **zakázané** zakázat Diagnostika spouštění.
6. Klikněte na tlačítko **OK**, zkontrolujte nastavení na stránce Souhrn a pak klikněte na tlačítko **vytvořit**.
  
 ![Vytvoření virtuálního počítače](./media/load-balancer-standard-public-availability-zones-portal/create-vm-standard-ip.png)

7. Vytvořit druhý virtuální počítač s názvem, *virtuálního počítače 2* v zóně 2 a třetí virtuálního počítače v zóně 3 a s *myVnet* jako virtuální síť, *myBackendSubnet* jako podsíť, a * *myNetworkSecurityGroup* jako skupinu zabezpečení sítě pomocí kroků 1 až 6.

### <a name="install-iis-on-vms"></a>Instalace služby IIS na virtuálních počítačích

1. Klikněte na tlačítko **všechny prostředky** klikněte v levé nabídce a potom v seznamu prostředků **myVM1** který je umístěný v *myResourceGroupLBAZ* skupinu prostředků.
2. Na **přehled** klikněte na tlačítko **Connect** pro připojení RDP do virtuálního počítače.
3. Přihlaste se k virtuálnímu počítači pomocí uživatelského jména *azureuser*.
4. Na ploše serveru, přejděte na **nástroje pro správu Windows**>**správce serveru**.
5. Na stránce Rychlý start správce serveru klikněte na tlačítko **přidat role a funkce**.

   ![Přidání do back-endového fondu adres – ](./media/load-balancer-standard-public-availability-zones-portal/servermanager.png)    

1. V **Průvodce přidáním rolí a funkcí**, použijte následující hodnoty:
    - V **vybrat typ instalace** klikněte na tlačítko **instalace na základě rolí nebo na základě funkcí**.
    - V **vybrat cílový server** klikněte na tlačítko **myVM1**.
    - V **role serveru vyberte** klikněte na tlačítko **webového serveru (IIS)**.
    - Postupujte podle pokynů dokončete průvodce.
2. Ukončení relace RDP s virtuálním počítačem - *myVM1*.
3. Opakujte kroky 1 až 7 instalace služby IIS na virtuálních počítačích *Můjvp2* a *myVM3*.

## <a name="create-load-balancer-resources"></a>Vytvořit prostředky nástroje pro vyrovnávání zatížení

V této části Konfigurace nastavení služby Vyrovnávání zatížení pro back-endových adres a test stavu a zadejte pro vyrovnávání zatížení a pravidla NAT.


### <a name="create-a-backend-address-pool"></a>Vytvořit fond adres back-end

Za účelem distribuce provozu do virtuálních počítačů obsahuje fond back-endových adres IP adresy virtuálních síťových karet připojených k nástroji pro vyrovnávání zatížení. Vytvořit fond adres back-end *myBackendPool* zahrnout *VM1* a *virtuálního počítače 2*.

1. Klikněte na tlačítko **všechny prostředky** v levé nabídce a pak klikněte na tlačítko **myLoadBalancer** ze seznamu prostředků.
2. V části **nastavení**, klikněte na tlačítko **back-endové fondy**, pak klikněte na tlačítko **přidat**.
3. Na **přidat fond back-end** proveďte následující:
    - Název, zadejte * myBackEndPool, jako název vašeho fondu back-end.
    - Pro **virtuální síť**, v rozevírací nabídce klikněte na **myVNet**
    - Pro **virtuálního počítače**, v rozevírací nabídce klikněte na tlačítko, **myVM1**.
    - Pro **IP adresu**, v rozevírací nabídce klikněte na IP adresu myVM1.
4. Klikněte na tlačítko **přidat nový prostředek back-end** přidat každý virtuální počítač (*Můjvp2* a *myVM3*) pro přidání do fondu back-end služby Vyrovnávání zatížení.
5. Klikněte na tlačítko **Add** (Přidat).

    ![Přidání do back-endového fondu adres – ](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. Zkontrolujte, vaše nastavení fondu back-end pro vyrovnávání zatížení zobrazí všechny tři virtuální počítače - **myVM1**, **Můjvp2** a **myVM3**.

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Pokud chcete nástroji pro vyrovnávání zatížení povolit monitorování stavu vaší aplikace, použijte sondu stavu. Sonda stavu dynamicky přidává virtuální počítače do oběhu nástroje pro vyrovnávání zatížení nebo je z něj odebírá na základě jejich reakce na kontroly stavu. Vytvoření test stavu *myHealthProbe* k monitorování stavu virtuálních počítačů.

1. Klikněte na tlačítko **všechny prostředky** v levé nabídce a pak klikněte na tlačítko **myLoadBalancer** ze seznamu prostředků.
2. V části **nastavení**, klikněte na tlačítko **testy stavu**, pak klikněte na tlačítko **přidat**.
3. K vytvoření test stavu použijte tyto hodnoty:
    - *myHealthProbe* – název Test stavu.
    - **HTTP** – pro typ protokolu.
    - *80* – číslo portu.
    - *15* – počet **Interval** v sekundách mezi pokusy o sondování.
    - *2* – počet **prahová hodnota špatného stavu** nebo po sobě jdoucích kontroly chyb, které musíte udělat předtím, než virtuální počítač není v pořádku.
4. Klikněte na **OK**.

   ![Přidání testu](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce provozu do virtuálních počítačů. Nadefinujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Vytvořit pravidlo služby load balancer *myLoadBalancerRuleWeb* pro naslouchání na portu 80 v front-endu *FrontendLoadBalancer* a odesílání provoz s vyrovnáváním zatížení sítě k fondu adres back-end *myBackEndPool* také používá port 80. 

1. Klikněte na tlačítko **všechny prostředky** v levé nabídce a pak klikněte na tlačítko **myLoadBalancer** ze seznamu prostředků.
2. V části **nastavení**, klikněte na tlačítko **pravidla Vyrovnávání zatížení**, pak klikněte na tlačítko **přidat**.
3. Tyto hodnoty použijte ke konfiguraci pravidlo Vyrovnávání zatížení:
    - *myHTTPRule* – název pravidlo Vyrovnávání zatížení.
    - **TCP** – pro typ protokolu.
    - *80* – číslo portu.
    - *80* – pro back-endový port.
    - *myBackendPool* – název fondu back-end.
    - *myHealthProbe* – název Test stavu.
4. Klikněte na **OK**.
    
    ![Přidání pravidla vyrovnávání zatížení](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testovací nástroje pro vyrovnávání zatížení
1. Najít veřejnou IP adresu pro nástroj pro vyrovnávání zatížení na **přehled** obrazovky. Klikněte na tlačítko **všechny prostředky** a pak klikněte na **myPublicIP**.

2. Zkopírujte veřejnou IP adresu a pak ji vložit do panelu Adresa prohlížeče. Výchozí stránka webového serveru služby IIS se zobrazí v prohlížeči.

      ![IIS Web server](./media/load-balancer-standard-public-availability-zones-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, odstraňte skupinu prostředků, a všechny související prostředky a nástroje pro vyrovnávání zatížení. To pokud chcete udělat, vyberte skupinu prostředků, který obsahuje nástroj pro vyrovnávání zatížení a klikněte na tlačítko **odstranit**.

## <a name="next-steps"></a>Další kroky

Další informace o [nástroj pro vyrovnávání zatížení](load-balancer-standard-overview.md).
