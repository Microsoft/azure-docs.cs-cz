---
title: 'Kurz: Konfigurace přesměrování portu ve službě Azure Load Balancer pomocí webu Azure portal'
titlesuffix: Azure Load Balancer
description: Tento kurz ukazuje, jak nakonfigurovat přesměrování portu pomocí nástroje pro vyrovnávání zatížení Azure vytvořit připojení k virtuálním počítačům ve virtuální síti Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: As an IT administrator, I want to configure port forwarding in Azure Load Balancer to remotely connect to VMs in an Azure virtual network.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/18
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: da41b33f3e5d24c0391c8486d9c0b372877eff21
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54232188"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-portal"></a>Kurz: Konfigurace přesměrování portu ve službě Azure Load Balancer pomocí portálu

Přesměrování portu vám umožní připojit se k virtuálním počítačům (VM) ve virtuální síti Azure pomocí Azure Load Balancer veřejnou IP adresu a číslo portu. 

V tomto kurzu můžete nastavit přesměrování portu ve službě Azure Load Balancer. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření veřejný load balancer úrovně Standard pro vyrovnávání zatížení sítě virtuálních počítačů. 
> * Vytvoření virtuální sítě a virtuální počítače se pravidlo zabezpečení sítě (NSG) skupiny. 
> * Přidáte do fondu back endových adres nástroje pro vyrovnávání zatížení virtuálních počítačů.
> * Vytvoření stavu nástroje pro vyrovnávání zatížení provozu a test pravidla.
> * Vytvoření load balanceru úrovně příchozí pravidla NAT přesměrování portu.
> * Instalace a konfigurace služby IIS na virtuální počítače do zobrazení zátěže a přesměrování portu v akci.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

Pro všechny kroky v tomto kurzu, přihlaste se k webu Azure portal na [ https://portal.azure.com ](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Vytvořte nástroj pro vyrovnávání zatížení Standard

Nejprve vytvořte veřejný load balancer úrovně Standard, která můžou vyrovnávat zatížení virtuálních počítačů. Nástroj pro vyrovnávání zatížení Standard podporuje pouze standardní veřejnou IP adresu. Když vytvoříte nástroj pro vyrovnávání zatížení Standard, vytvoříte novou standardní veřejnou IP adresu, která je nakonfigurovaná jako front-endu nástroje pro vyrovnávání zatížení a s názvem **LoadBalancerFrontEnd** ve výchozím nastavení. 

1. V levém horním rohu webu Azure Portal klikněte na **Vytvořit prostředek** > **Sítě** > **Nástroj pro vyrovnávání zatížení**.
   
1. V **vytvořit nástroj pro vyrovnávání zatížení** podokně, zadejte nebo vyberte tyto hodnoty:
   
   - **Název**: Typ *MyLoadBalancer*.
   - **Typ**: Vyberte **veřejné**. 
   - **SKLADOVÁ POLOŽKA**: Vyberte **standardní**.
   - **Veřejná IP adresa**: Vyberte **vytvořit nový**, zadejte *MyPublicIP* v poli.
   - **Nakonfigurovat veřejnou IP adresu** > **zóna dostupnosti**: Vyberte **zónově redundantní**.
   - **Skupina prostředků**: Vyberte **vytvořit nový**, zadejte *MyResourceGroupLB*a vyberte **OK**. 
   - **Umístění**: Vyberte **Západní Evropa**. 
     
     >[!NOTE]
     >Ujistěte se, že chcete vytvořit nástroj pro vyrovnávání zatížení a všech prostředků, do umístění, které podporují zóny dostupnosti. Další informace najdete v tématu [oblasti, které podporují zóny dostupnosti](../availability-zones/az-overview.md#regions-that-support-availability-zones). 
   
1. Vyberte **Vytvořit**.
   
![Vytvoření nástroje pro vyrovnávání zatížení](./media/tutorial-load-balancer-port-forwarding-portal/1-load-balancer.png)

## <a name="create-and-configure-back-end-servers"></a>Vytvoření a konfigurace back-end serverů

Vytvoření virtuální sítě s dva virtuální počítače a přidání do back endový fond vašeho nástroje pro vyrovnávání zatížení virtuálních počítačů. 

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Na straně levého horního rohu portálu, vyberte **vytvořit prostředek** > **sítě** > **virtuální síť**.
   
1. V **vytvořit virtuální síť** podokně, zadejte nebo vyberte tyto hodnoty:
   
   - **Název**: Typ *MyVNet*.
   - **Skupina prostředků**: Rozevírací nabídka **vybrat existující** a vyberte **MyResourceGroupLB**. 
   - **Podsíť** > **název**: Typ *MyBackendSubnet*.
   
1. Vyberte **Vytvořit**.

   ![Vytvoření virtuální sítě](./media/tutorial-load-balancer-port-forwarding-portal/2-load-balancer-virtual-network.png)

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>Vytvoření virtuálních počítačů a jejich přidání do back endovému fondu nástroje pro vyrovnávání zatížení

1. Na straně levého horního rohu portálu, vyberte **vytvořit prostředek** > **Compute** > **systému Windows Server 2016 Datacenter**. 
   
1. V **vytvoření virtuálního počítače**, zadejte nebo vyberte následující hodnoty **Základy** kartu:
   - **Předplatné** > **skupiny prostředků**: Rozevírací seznam a vyberte **MyResourceGroupLB**.
   - **Název virtuálního počítače**: Typ *MyVM1*.
   - **Oblast**: Vyberte **Západní Evropa**. 
   - **Uživatelské jméno**: Typ *azureuser*.
   - **Heslo**: Typ *Azure1234567*. 
     Potvrďte heslo v **potvrzení hesla** pole.
   
1. Vyberte **sítě** kartě nebo vyberte **Další: Disky**, pak **Další: Sítě**. 
   
   Ujistěte se, že jsou vybrány následující:
   - **Virtuální síť**: **MyVNet**
   - **Podsíť**: **MyBackendSubnet**
   
1. V části **veřejnou IP adresu**vyberte **vytvořit nový**vyberte **standardní** na **vytvoření veřejné IP adresy** stránce a pak vyberte **OK**. 
   
1. V části **skupinu zabezpečení sítě**vyberte **Upřesnit** vytvořit novou skupinu zabezpečení sítě (NSG), typ brány firewall. 
   1. V **konfigurovat skupinu zabezpečení sítě** pole, vyberte **vytvořit nový**. 
   1. Typ *MyNetworkSecurityGroup*a vyberte **OK**. 
   
   >[!NOTE]
   >Podívejte se, že ve výchozím nastavení, skupiny zabezpečení sítě už má příchozí pravidla pro otevření portu 3389, vzdálený port klasické pracovní plochy (RDP).
   
1. Přidáte virtuální počítač do back endového fondu nástroje pro vyrovnávání zatížení, kterou vytvoříte:
   
   1. V části **Vyrovnávání zatížení** > **umístit virtuální počítač za existující řešení vyrovnávání zatížení?** vyberte **Ano**. 
   1. Pro **možnosti vyrovnávání zatížení**, rozevírací seznam a vyberte **nástroji Azure load balancer**. 
   1. Pro **vyberte nástroj pro vyrovnávání zatížení**, rozevírací seznam a vyberte **MyLoadBalancer**. 
   1. V části **vyberte back-endový fond**vyberte **vytvořit nový**, zadejte *MyBackendPool*a vyberte **vytvořit**. 
   
   ![Vytvoření virtuální sítě](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. Vyberte **správu** kartě nebo vyberte **Další** > **správu**. V části **monitorování**, nastavte **Diagnostika spouštění** k **vypnout**.
   
1. Vyberte **Zkontrolovat a vytvořit**.
   
1. Zkontrolujte nastavení a pokud ověření proběhne úspěšně, vyberte **vytvořit**. 

1. Podle pokynů vytvořte druhý virtuální počítač s názvem *MyVM2*, se všemi nastaveními stejný jako MyVM1. 
   
   Pro **skupinu zabezpečení sítě**, po výběru **Upřesnit**, rozevírací seznam a vyberte **MyNetworkSecurityGroup** , kterou jste již vytvořili. 
   
   V části **vyberte back-endový fond**, ujistěte se, že **MyBackendPool** zaškrtnuto. 

### <a name="create-an-nsg-rule-for-the-vms"></a>Vytvořte pravidlo skupiny zabezpečení sítě pro virtuální počítače

Vytvořte pravidlo skupinu zabezpečení sítě pro virtuální počítače, které povolí příchozí připojení k Internetu (HTTP).

>[!NOTE]
>Ve výchozím nastavení již NSG pravidlo, které otevře port 3389, port vzdálené plochy (RDP).

1. V nabídce vlevo vyberte **Všechny prostředky**. V seznamu prostředků vyberte **MyNetworkSecurityGroup** v **MyResourceGroupLB** skupinu prostředků.
   
1. V části **Nastavení** vyberte **Příchozí pravidla zabezpečení** a potom vyberte **Přidat**.
   
1. V **přidat příchozí pravidlo zabezpečení** dialogové okno, zadejte nebo vyberte následující:
   
   - **Zdroj**: Vyberte **značka služby**.  
   - **Značka zdrojové služby**: Vyberte **Internet**. 
   - **Rozsahy cílových portů**: Typ *80*.
   - **Protokol**: Vyberte **TCP**. 
   - **Akce**: Vyberte **povolit**.  
   - **Priorita**: Typ *100*. 
   - **Název**: Typ *MyHTTPRule*. 
   - **Popis**: Typ *povolení protokolu HTTP*. 
   
1. Vyberte **Přidat**. 
   
   ![Vytvoření pravidla skupiny zabezpečení sítě](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení

V této části zkontrolovat back endovému fondu nástroje pro vyrovnávání zatížení a nakonfigurovat pravidel pro vyrovnávání zatížení stavu testu a provozu.

### <a name="view-the-back-end-address-pool"></a>Zobrazit fond back endových adres

Účelem distribuce provozu do virtuálních počítačů, nástroje pro vyrovnávání zatížení používá fond back endových adres, který obsahuje IP adresy virtuálních síťových rozhraní (NIC), které jsou připojené k nástroji pro vyrovnávání zatížení. 

Vytvoření vaší back endového fondu nástroje pro vyrovnávání zatížení a virtuální počítače do ní přidat při vytváření virtuálních počítačů. Můžete také vytvořit fondy back-end a přidat nebo odebrat virtuální počítače z nástroje pro vyrovnávání zatížení **back-endové fondy** stránky. 

1. Vyberte **všechny prostředky** v nabídce vlevo a pak vyberte **MyLoadBalancer** ze seznamu prostředků.
   
1. V části **Nastavení** vyberte **Back-endové fondy**.
   
1. Na **back-endové fondy** stránce, rozbalte **MyBackendPool** a ujistěte se, že **VM1** a **VM2** jsou uvedeny.

1. Vyberte **MyBackendPool**. 
   
   Na **MyBackendPool** stránce v části **VIRTUÁLNÍHO počítače** a **IP adresu**, můžete odebrat nebo přidat do fondu dostupných virtuálních počítačů.

Můžete vytvořit nový back endové fondy výběrem **přidat** na **back-endové fondy** stránky.

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Pokud chcete povolit monitorování stavu virtuálních počítačů nástroje pro vyrovnávání zatížení, použijte sondu stavu. Sonda stavu dynamicky přidává virtuální počítače do oběhu nástroje pro vyrovnávání zatížení nebo je z něj odebírá na základě jejich reakce na kontroly stavu. 

1. Vyberte **všechny prostředky** v nabídce vlevo a pak vyberte **MyLoadBalancer** ze seznamu prostředků.
   
1. V **Nastavení** vyberte **Sondy stavu** a potom vyberte **Přidat**.
   
1. Na **přidat sondu stavu** stránky zadejte nebo vyberte následující hodnoty:
   
   - **Název**: Typ *MyHealthProbe*.
   - **Protokol**: Rozevírací seznam a vyberte **HTTP**. 
   - **Port**: Typ *80*. 
   - **Cesta**: Přijměte */* pro výchozí identifikátor URI. Tuto hodnotu můžete nahradit jiný identifikátor URI. 
   - **Interval**: Typ *15*. Interval je počet sekund mezi pokusy o testování.
   - **Prahová hodnota špatného stavu**: Typ *2*. Tato hodnota je počet chyb po sobě jdoucích sondování, ke kterým dojde před virtuální počítač považoval za poškozený.
   
1. Vyberte **OK**.
   
   ![Přidat test](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje, jak se provoz distribuuje do virtuálních počítačů. Pravidlo definuje konfiguraci front-end IP adresy pro příchozí provoz, back endového fondu IP pro příjem provozu a požadované zdrojových a cílových portů. 

Pravidlo nástroje pro vyrovnávání zatížení s názvem **MyLoadBalancerRule** naslouchá na portu 80 ve front-endu **LoadBalancerFrontEnd**. Toto pravidlo automaticky odesílá síťový provoz do fondu back endových adres **MyBackendPool**, rovněž na portu 80. 

1. Vyberte **všechny prostředky** v nabídce vlevo a pak vyberte **MyLoadBalancer** ze seznamu prostředků.
   
1. V části **Nastavení** vyberte **Pravidla vyrovnávání zatížení** a potom vyberte **Přidat**.
   
1. Na **přidat pravidlo Vyrovnávání zatížení** stránky zadejte nebo vyberte následující hodnoty:
   
   - **Název**: Typ *MyLoadBalancerRule*.
   - **Protokol**: Vyberte **TCP**.
   - **Port**: Typ *80*.
   - **Back-endový port**: Typ *80*.
   - **Back-endový fond**: Vyberte **MyBackendPool**.
   - **Sonda stavu**: Vyberte **MyHealthProbe**. 
   
1. Vyberte **OK**.
   
  ![Přidat pravidlo služby load balancer](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>Vytvoření příchozího pravidla NAT přesměrování portu

Vytvoření translation (NAT) pravidlo služby load balancer příchozí síťová adresa směrovat provoz z konkrétní port front-end IP adresy na konkrétní port back endového virtuálního počítače.

1. Vyberte **všechny prostředky** v panelu nabídky na levé straně a pak vyberte **MyLoadBalancer** ze seznamu prostředků.
   
1. V části **nastavení**vyberte **pravidla příchozího překladu adres**a pak vyberte **přidat**. 
   
1. Na **přidat příchozí pravidlo NAT** stránky zadejte nebo vyberte následující hodnoty:
   
   - **Název**: Typ *MyNATRuleVM1*.
   - **Port**: Typ *4221*.
   - **Cílit na virtuální počítač**: Vyberte **MyVM1** z rozevíracího seznamu.
   - **Mapování portů**: Vyberte **vlastní**.
   - **Cílit na port**: Typ *3389*.
   
1. Vyberte **OK**.
   
1. Opakujte postup pro přidání příchozích pravidel NAT pravidlo s názvem *MyNATRuleVM2*s použitím **Port**: *4222* a **cílový virtuální počítač**: **MyVM2**.

## <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

V této části nainstalujete Internetové informační služby (IIS) na back-end serverech a úprava výchozí webové stránky, aby se zobrazil název počítače. Pak použijete nástroj pro vyrovnávání zatížení veřejnou IP adresu k testování nástroje pro vyrovnávání zatížení. 

Každý back endového virtuálního počítače slouží jinou verzi výchozí webovou stránku IIS, abyste si mohli zobrazit distribuci požadavků mezi dvěma virtuálními počítači nástroje pro vyrovnávání zatížení.

### <a name="connect-to-the-vms-with-rdp"></a>Připojení k virtuálním počítačům přes RDP

Připojte se ke všem virtuálním počítačům pomocí vzdálené plochy (RDP). 

1. Na portálu vyberte **všechny prostředky** v nabídce vlevo. V seznamu prostředků vyberte každý virtuální počítač **MyResourceGroupLB** skupinu prostředků.
   
1. Na **přehled** stránce **připojit**a pak vyberte **soubor stáhnout RDP**. 
   
1. Otevřít protokol RDP jste stáhli a vyberte možnost **připojit**.
   
1. V dialogovém okně zabezpečení Windows vyberte **víc možností** a potom **použít jiný účet**. 
   
   Zadejte uživatelské jméno *azureuser* a heslo *Azure1234567*a vyberte **OK**.
   
1. Reakce **Ano** do libovolného řádku pro certifikát. 
   
   V novém okně se otevře na plochu virtuálního počítače. 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>Instalace služby IIS a nahradit výchozí webová stránka služby IIS 

Instalace služby IIS a výchozí webovou stránku IIS nahraďte stránku, která zobrazuje název virtuálního počítače pomocí Powershellu.

1. Spustit na MyVM1 a na MyVM2 **prostředí Windows PowerShell** z **Start** nabídky. 

2. Spusťte následující příkazy k instalaci IIS a nahradit výchozí webovou stránku IIS:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    
   ```
   
1. Ukončete připojení RDP s MyVM1 a MyVM2 tak, že vyberete **odpojit**. Není vypnout virtuální počítače.

### <a name="test-load-balancing"></a>Vyrovnávání zatížení pro test

1. Na portálu na **přehled** stránce **MyLoadBalancer**, zkopírujte veřejnou IP adresu v rámci **veřejnou IP adresu**. Podržte ukazatel myši nad adres a vyberte **kopírování** ikonu zkopírujte. V tomto příkladu je **40.67.218.235**. 
   
1. Vložte nebo zadejte veřejnou IP adresu nástroje pro vyrovnávání zatížení (*40.67.218.235*) do adresního řádku webového prohlížeče. 
   
   Přizpůsobené serveru výchozí stránku webové služby IIS se zobrazí v prohlížeči. Přečte zprávu buď **Hello World z MyVM1**, nebo **Hello World z MyVM2**.
   
   ![Nové výchozí stránka služby IIS](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. Aktualizujte prohlížeč, pokud chcete zobrazit distribuci provozu mezi virtuální počítače, nástroj pro vyrovnávání zatížení. Někdy **MyVM1** se zobrazí stránka a jindy **MyVM2** stránky se zobrazí, jak nástroj pro vyrovnávání zatížení distribuuje požadavky na každý back endového virtuálního počítače.
   
   >[!NOTE]
   >Budete muset vymazat mezipaměť prohlížeče nebo otevřete nové okno prohlížeče mezi pokusy.

## <a name="test-port-forwarding"></a>Test přesměrování portů

Díky přesměrování portu, můžete k back endového virtuálního počítače pomocí vzdálené plochy s použitím IP adresy nástroje pro vyrovnávání zatížení a front-endový port hodnota definovaná v pravidlu překladu adres. 

1. Na portálu na **přehled** stránce **MyLoadBalancer**, zkopírujte jeho veřejné IP adresy. Podržte ukazatel myši nad adres a vyberte **kopírování** ikonu zkopírujte. V tomto příkladu je **40.67.218.235**. 
   
1. Otevřete příkazový řádek a následujícím příkazem vytvořte relaci vzdálené plochy s MyVM2, pomocí nástroje pro vyrovnávání zatížení veřejnou IP adresu a front-endový port, který jste definovali v pravidlu překladu adres Virtuálního počítače. 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. Otevřete stažený soubor RDP a vyberte **připojit**.
   
1. V dialogovém okně zabezpečení Windows vyberte **víc možností** a potom **použít jiný účet**. 
   
   Zadejte uživatelské jméno *azureuser* a heslo *Azure1234567*a vyberte **OK**.
   
1. Reakce **Ano** do libovolného řádku pro certifikát. 
   
   V novém okně se otevře plocha MyVM2. 

Úspěšná připojení RDP, protože příchozí pravidlo NAT **MyNATRuleVM2** směruje provoz z front-endu nástroje pro vyrovnávání zatížení port 4222 MyVM2 na port 3389 (RDP port).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odstranit nástroj pro vyrovnávání zatížení a všech souvisejících prostředků, když je už nepotřebujete, otevřete **MyResourceGroupLB** prostředku, skupiny a vyberte **odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili veřejný load balancer úrovně Standard. Vytvoření a konfiguraci síťových prostředků, back-end serverů, sondu stavu a pravidel nástroje pro vyrovnávání zatížení. Službu IIS nainstalovali na virtuálních počítačích back-end a použít nástroje pro vyrovnávání zatížení veřejnou IP adresu k otestování nástroje pro vyrovnávání zatížení. Nastavení a testovat přesměrování portu z zadaný port v nástroji pro vyrovnávání zatížení na port v back endového virtuálního počítače. 

Další informace o Azure Load Balancer, nadále více kurzy nástroje pro vyrovnávání zatížení.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
