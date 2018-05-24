---
title: 'Rychlý start: Vytvoření veřejného Load Balanceru úrovně Basic pomocí webu Azure Portal | Microsoft Docs'
description: Tento rychlý start ukazuje, jak vytvořit veřejné Load Balancer úrovně Basic pomocí webu Azure Portal.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 610aa8d5652b89f36aeb2a6ae517d378c5bfa666
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-public-basic-load-balancer-by-using-the-azure-portal"></a>Rychlý start: Vytvoření veřejného Load Balanceru úrovně Basic pomocí webu Azure Portal

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky na více virtuálních počítačů. K vytvoření nástroje pro vyrovnávání zatížení, který bude vyrovnávat virtuální počítače, můžete použít web Azure Portal. V tomto rychlém startu se dozvíte, jak vytvořit síťové prostředky, back-end servery a Load Balancer úrovně Basic.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

U všech úloh v tomto rychlém startu je nutné být přihlášený k webu [Azure Portal](http://portal.azure.com).

## <a name="create-a-basic-load-balancer"></a>Vytvoření Load Balanceru úrovně Basic

V této části pomocí webu Azure Portal vytvoříte veřejný Load Balancer úrovně Basic. Když pomocí portálu vytvoříte veřejnou IP adresu a prostředek nástroje pro vyrovnávání zatížení, veřejná IP adresa se automaticky nakonfiguruje jako front-end nástroje pro vyrovnávání zatížení. Název tohoto front-endu je **LoadBalancerFrontend**.

1. V levém horním rohu webu Azure Portal klikněte na **Vytvořit prostředek** > **Sítě** > **Nástroj pro vyrovnávání zatížení**.
2. Do podokna **Nástroj pro vyrovnávání zatížení** zadejte tyto hodnoty:
   - **myLoadBalancer** jako název nástroje pro vyrovnávání zatížení
   - **Public** jako typ nástroje pro vyrovnávání zatížení 
   - **myPublicIP** jako veřejnou IP adresu, kterou je potřeba vytvořit se **skladovou položkou** nastavenou na **Basic** a **přiřazením** nastaveným na **Dynamické**.
   - **myResourceGroupLB** jako název existující skupiny prostředků
3. Vyberte **Vytvořit**.
   
![Vytvoření nástroje pro vyrovnávání zatížení](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)


## <a name="create-back-end-servers"></a>Vytvoření back-end serverů

V této části vytvoříte virtuální sít a dva virtuální počítače pro back-endový fond vašeho Load Balanceru úrovně Basic. Potom na virtuální počítače nainstalujete Internetovou informační službu (služba IIS), abyste mohli nástroj pro vyrovnávání zatížení otestovat.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
1. V pravém horním rohu webu Azure Portal vyberte **Nový** > **Sítě** > **Virtuální síť**.
2. Do podokna **Vytvořit virtuální síť** zadejte následující hodnoty a potom vyberte **Vytvořit**:
   - **myVnet** jako název virtuální sítě
   - **myResourceGroupLB** jako název existující skupiny prostředků
   - **myBackendSubnet** jako název podsítě

   ![Vytvoření virtuální sítě](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

1. V levé horní části webu Azure Portal vyberte **Nový** > **Compute** > **Windows Server 2016 Datacenter**. 
2. Zadejte pro virtuální počítač tyto hodnoty a potom vyberte **OK**:
   - **myVM1** jako název virtuálního počítače        
   - **azureuser** jako uživatelské jméno správce    
   - **myResourceGroupLB** jako název skupiny prostředků (V části **Skupina prostředků** vyberte **Použít existující** a pak vyberte **myResourceGroupLB**.)   
3. Vyberte velikost virtuálního počítače **DS1_V2** a klikněte na **Vybrat**.
4. Zadejte následující hodnoty nastavení virtuálního počítače:
   - **myAvailabilitySet** jako název nové skupiny dostupnosti, kterou vytvoříte.
   - **myVNet** jako název virtuální sítě (Zkontrolujte, že je vybraný.)
   - **myBackendSubnet** jako název podsítě (Zkontrolujte, že je vybraný.)
   - **myVM1-ip** jako veřejná IP adresa
   - **myNetworkSecurityGroup** jako název nové skupiny zabezpečení sítě (typ brány firewall), kterou je potřeba vytvořit.
5. Vybráním možnosti **Zakázáno** zakažte diagnostiku spouštění.
6. Vyberte **OK**, na stránce souhrnu zkontrolujte nastavení a pak vyberte **Vytvořit**.
7. Zopakováním kroků 1 až 6 vytvořte druhý virtuální počítač s názvem **VM2** a těmito hodnotami:
   - **myAvailabilityset** jako skupina dostupnosti
   - **myVnet** jako virtuální síť
   - **myBackendSubnet** jako podsíť
   - **myNetworkSecurityGroup** jako skupina zabezpečení sítě 

### <a name="create-nsg-rules"></a>Vytvoření pravidel skupiny zabezpečení sítě

V této části vytvoříte pravidla skupiny zabezpečení sítě, která povolí příchozí připojení používající protokoly HTTP a RDP.

1. V nabídce vlevo vyberte **Všechny prostředky**. Ve skupině prostředků **myResourceGroupLB** vyberte ze seznamu prostředků **myNetworkSecurityGroup**.
2. V části **Nastavení** vyberte **Příchozí pravidla zabezpečení** a potom vyberte **Přidat**.
3. Zadáním následujících hodnot pro příchozí pravidlo zabezpečení s názvem **myHTTPRule** povolte příchozí připojení HTTP používající port 80. Pak vyberte **OK**.
   - Jako **Zdroj** zadejte **Značka služby**.
   - Jako **Značka zdrojové služby** zadejte **Internet**.
   - Jako **Rozsahy cílových portů** zadejte **80**.
   - Jako **Protokol** zadejte **TCP**.
   - Jako **Akce** zadejte **Povolit**.
   - Jako **Priorita** zadejte **100**.
   - Jako **Název** zadejte **myHTTPRule**.
   - Jako **Popis** zadejte **Povolení protokolu HTTP**.
 
   ![Vytvoření pravidla skupiny zabezpečení sítě](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
4. Zopakováním kroků 2 a 3 vytvořte další pravidlo s názvem **myRDPRule**, které prostřednictvím portu 3389 povolí příchozí připojení RDP. Použijte následující hodnoty:
   - Jako **Zdroj** zadejte **Značka služby**.
   - Jako **Značka zdrojové služby** zadejte **Internet**.
   - Jako **Rozsahy cílových portů** zadejte **3389**.
   - Jako **Protokol** zadejte **TCP**.
   - Jako **Akce** zadejte **Povolit**.
   - Jako **Priorita** zadejte **200**.
   - Jako **Název** zadejte **myRDPRule**.
   - Jako **Popis** zadejte **Povolení protokolu RDP**.

   

### <a name="install-iis"></a>Instalace služby IIS

1. V nabídce vlevo vyberte **Všechny prostředky**. Ve skupině prostředků **myResourceGroupLB** vyberte ze seznamu prostředků **myVM1**.
2. Na stránce **Přehled** vyberte **Připojit** a připojte se přes RDP k virtuálnímu počítači.
3. Přihlaste se k virtuálnímu počítači pomocí uživatelského jména **azureuser** a hesla **Azure123456!**.
4. Na ploše serveru přejděte do části **Nástroje pro správu Windows** > **Správce serveru**.
5. Ve Správci serveru vyberte **Spravovat** a potom vyberte **Přidat role a funkce**.
   ![Přidání role správce serveru](./media/load-balancer-get-started-internet-portal/servermanager.png)
6. V Průvodci přidáním rolí a funkcí použijte následující hodnoty:
   - Na stránce **Výběr typu instalace** vyberte **Instalace na základě role nebo funkce**.
   - Na stránce **Výběr cílového serveru** vyberte **myVM1**.
   - Na stránce **Výběr role serveru** vyberte na **Webový server (služba IIS)**.
   - Postupujte podle pokynů a dokončete zbytek průvodce. 
7. Zopakujte kroky 1 až 6 pro virtuální počítač **myVM2**.

## <a name="create-resources-for-the-basic-load-balancer"></a>Vytvoření prostředků pro Load Balancer úrovně Basic

V této části nakonfigurujete nastavení nástroje pro vyrovnávání zatížení pro fond back-endových adres a sondu stavu. Navíc zadáte pravidla nástroje pro vyrovnávání zatížení a překladu adres.


### <a name="create-a-back-end-address-pool"></a>Vytvoření fondu back-endových adres

Za účelem distribuce provozu do virtuálních počítačů obsahuje fond back-endových adres IP adresy virtuálních síťových karet, které jsou připojené k nástroji pro vyrovnávání zatížení. Vytvořte fond back-endových adres **myBackendPool**, abyste do něj mohli zahrnout virtuální počítače **VM1** a **VM2**.

1. V levé nabídce vyberte **Všechny prostředky** a potom v seznamu prostředků vyberte **myLoadBalancer**.
2. V **Nastavení** vyberte **Back-endové fondy** a potom vyberte **Přidat**.
3. Na stránce **Přidat back-endový fond** postupujte následovně a potom vyberte **OK**:
   - V části **Název** zadejte **myBackEndPool**.
   - V části **Přidruženo k** vyberte v rozevírací nabídce **Skupina dostupnosti**.
   - V části **Skupina dostupnosti** vyberte **myAvailabilitySet**.
   - Vybráním možnosti **Přidat konfiguraci IP adresy cílové sítě** přidejte do back-endového fondu jednotlivé virtuální počítače (**myVM1** a **myVM2**), které jste vytvořili.

   ![Přidání virtuálních počítačů do back-endového fondu adres](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. Zkontrolujte, že v nastavení back-endového fondu vašeho nástroje pro vyrovnávání zatížení se zobrazují oba virtuální počítače **VM1** i **VM2**.

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Pokud chcete Load Balanceru úrovně Basic povolit monitorování stavu vaší aplikace, použijte sondu stavu. Sonda stavu dynamicky přidává virtuální počítače do oběhu nástroje pro vyrovnávání zatížení nebo je z něj odebírá na základě jejich reakce na kontroly stavu. Vytvořte sondu stavu s názvem **myHealthProbe**, abyste mohli monitorovat stav virtuálních počítačů.

1. V levé nabídce vyberte **Všechny prostředky** a potom v seznamu prostředků vyberte **myLoadBalancer**.
2. V **Nastavení** vyberte **Sondy stavu** a potom vyberte **Přidat**.
3. Použijte následující hodnoty a potom vyberte **OK**:
   - **myHealthProbe** jako název sondy stavu
   - **HTTP** jako typ protokolu
   - **80** jako číslo portu
   - **15** jako **Interval** mezi pokusy o testování (v sekundách)
   - **2** jako **Prahová hodnota špatného stavu**, tedy počet po sobě jdoucích selhání sondy, ke kterým musí dojít, aby se virtuální počítač považoval za poškozený.

   ![Přidání testu](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Použijte pravidlo nástroje pro vyrovnávání zatížení k definování způsobu distribuce provozu do virtuálních počítačů. Nadefinujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. 

Vytvořte pravidlo nástroje pro vyrovnávání zatížení s názvem **myLoadBalancerRuleWeb**, které bude naslouchat portu 80 ve front-endu **LoadBalancerFrontEnd**. Toto pravidlo bude také odesílat síťový provoz s vyrovnáním zatížení do fondu back-endových adres **myBackEndPool** (rovněž pomocí portu 80). 

1. V levé nabídce vyberte **Všechny prostředky** a potom v seznamu prostředků vyberte **myLoadBalancer**.
2. V části **Nastavení** vyberte **Pravidla vyrovnávání zatížení** a potom vyberte **Přidat**.
3. Použijte následující hodnoty a potom vyberte **OK**:
   - **myHTTPRule** jako název pravidla nástroje pro vyrovnávání zatížení
   - **TCP** jako typ protokolu
   - **80** jako číslo portu
   - **80** jako back-endový port
   - **myBackendPool** jako název backendového fondu
   - **myHealthProbe** jako název sondy stavu
    
   ![Přidání pravidla nástroje pro vyrovnávání zatížení](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení
1. Na obrazovce **Přehled** vyhledejte veřejnou IP adresu nástroje pro vyrovnávání zatížení. Vyberte **Všechny prostředky** a potom vyberte **myPublicIP**.

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

   ![Webový server služby IIS](./media/load-balancer-get-started-internet-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete skupinu prostředků, nástroj pro vyrovnávání zatížení a všechny související prostředky odstranit. Vyberte skupinu prostředků, která obsahuje nástroj pro vyrovnávání zatížení, a potom vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili skupinu prostředků, síťové prostředky a back-end servery. Pomocí těchto prostředků jste pak vytvořili nástroj pro vyrovnávání zatížení. Další informace o nástrojích pro vyrovnávání zatížení a jejich souvisejících prostředcích najdete v dalších kurzech.
