---
title: 'Kurz: konfigurace předávání portů – Azure Portal'
titleSuffix: Azure Load Balancer
description: V tomto kurzu se dozvíte, jak nakonfigurovat předávání portů pomocí Azure Load Balancer k vytvoření připojení k virtuálním počítačům ve službě Azure Virtual Network.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to configure port forwarding in Azure Load Balancer to remotely connect to VMs in an Azure virtual network.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 63d1a08dc588f0303ccb1ae13bd4c28af2a393c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92043649"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-portal"></a>Kurz: konfigurace předávání portů v Azure Load Balancer pomocí portálu

Přesměrování portů umožňuje připojit se k virtuálním počítačům ve virtuální síti Azure pomocí Azure Load Balancer veřejné IP adresy a čísla portu. 

V tomto kurzu nastavíte přesměrování portů na Azure Load Balancer. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření veřejného nástroje pro vyrovnávání zatížení pro vyrovnávání zatížení sítě v rámci virtuálních počítačů. 
> * Vytvořte virtuální síť a virtuální počítače s pravidlem skupiny zabezpečení sítě (NSG). 
> * Přidejte virtuální počítače do fondu adres back-endu nástroje pro vyrovnávání zatížení.
> * Vytvoření sondy stavu nástroje pro vyrovnávání zatížení a pravidel provozu.
> * Vytvořte pravidla přesměrování na příchozím portu NAT pro vyrovnávání zatížení.
> * Nainstalujte a nakonfigurujte IIS na virtuálních počítačích, abyste zobrazili vyrovnávání zatížení a předávání portů v akci.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

Pro všechny kroky v tomto kurzu se přihlaste k Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com) .

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure.

## <a name="create-a-standard-load-balancer"></a>Vytvoření standardního nástroje pro vyrovnávání zatížení

Nejdřív vytvořte veřejný Nástroj pro vyrovnávání zatížení, který může vyrovnávat zatížení provozu virtuálních počítačů. Standardní nástroj pro vyrovnávání zatížení podporuje jenom standardní veřejnou IP adresu. Když vytvoříte standardní nástroj pro vyrovnávání zatížení, vytvoříte také novou standardní veřejnou IP adresu, která je ve výchozím nastavení nakonfigurovaná jako front-end nástroje pro vyrovnávání zatížení a s názvem **LoadBalancerFrontEnd** . 

1. V levém horním rohu obrazovky klikněte na **vytvořit prostředek**  >  **síť**  >  **Load Balancer**.
2. Na kartě **základy** na stránce **vytvořit službu Vyrovnávání zatížení** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **vytvořit nový** a do textového pole zadejte *MyResourceGroupLB* .|
    | Name                   | *myLoadBalancer*                                   |
    | Oblast         | Vyberte **Západní Evropa**.                                        |
    | Typ          | Vyberte **Veřejný**.                                        |
    | SKU           | Vyberte **Standard**.                          |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. |
    | Název veřejné IP adresy              | Do textového pole zadejte *myPublicIP* .   |
    |Zóna dostupnosti| Vyberte **zóna redundantní**.    |
     
    >[!NOTE]
     >Ujistěte se, že jste pro něj vytvořili Load Balancer a všechny prostředky v umístění, které podporuje Zóny dostupnosti. Další informace najdete v tématu [oblasti, které podporují zóny dostupnosti](../availability-zones/az-region.md). 

3. Na kartě **Revize + vytvořit** klikněte na **vytvořit**.  
  
## <a name="create-and-configure-back-end-servers"></a>Vytvoření a konfigurace back-endové serverů

Vytvořte virtuální síť se dvěma virtuálními počítači a přidejte virtuální počítače do fondu back-end vašeho nástroje pro vyrovnávání zatížení. 

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

V této části budete muset v krocích níže nahradit následující parametry:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB (vyberte existující skupinu prostředků) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | West Europe      |
| **\<IPv4-address-space>**   | 10.3.0.0 \ 16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>Vytvořte virtuální počítače a přidejte je do fondu back-end nástroje pro vyrovnávání zatížení.

1. V levé horní části portálu vyberte **vytvořit prostředek**  >  **COMPUTE**  >  **Windows Server 2016 Datacenter**. 
   
1. V části **vytvořit virtuální počítač** zadejte nebo vyberte následující hodnoty na kartě **základy** :
   - **Předplatné**  >  **Skupina prostředků**: rozevírací seznam a vyberte **MyResourceGroupLB**.
   - **Název virtuálního počítače**: zadejte *MyVM1*.
   - **Oblast**: vyberte **západní Evropa**. 
   - **Uživatelské jméno**: zadejte *azureuser*.
   - **Heslo**: zadejte *Azure1234567*. 
     Znovu zadejte heslo do pole **Potvrdit heslo** .
   
1. Vyberte kartu **síť** nebo vyberte **Další: disky** a **Další: síť**. 
   
   Ujistěte se, že jsou vybrané následující:
   - **Virtuální síť**: **MyVNet**
   - **Podsíť**: **MyBackendSubnet**
   
1. V části **Veřejná IP** adresa vyberte **vytvořit novou**, na stránce **vytvořit veřejnou IP adresu** vyberte **Standard** a pak vyberte **OK**. 
   
1. V části **Skupina zabezpečení sítě** vyberte **Upřesnit** a vytvořte novou skupinu zabezpečení sítě (NSG), což je typ brány firewall. 
   1. V poli **Konfigurovat skupinu zabezpečení sítě** vyberte **vytvořit novou**. 
   1. Zadejte *MyNetworkSecurityGroup* a vyberte **OK**. 
   
   >[!NOTE]
   >Všimněte si, že ve výchozím nastavení má NSG již příchozí pravidlo pro otevření portu 3389, portu vzdálené plochy (RDP).
   
1. Přidejte virtuální počítač do fondu back-end nástroje pro vyrovnávání zatížení, který vytvoříte:
   
   1. V části **Vyrovnávání zatížení**  >  **umístěte tento virtuální počítač za existující řešení vyrovnávání zatížení** a vyberte **Ano**. 
   1. V **možnostech vyrovnávání zatížení** rozbalte rozevírací seznam a vyberte **Azure Load Balancer**. 
   1. V rozevíracím seznamu **Vyberte nástroj pro vyrovnávání zatížení** vyberte **MyLoadBalancer**. 
   1. V části **Vyberte fond back-end** vyberte **vytvořit nový**, zadejte *MyBackendPool* a vyberte **vytvořit**. 
   
   ![Vytvoření virtuální sítě](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. Vyberte kartu **Správa** nebo vyberte možnost **Další**  >  **Správa**. V části **monitorování** nastavte **diagnostiku spouštění** na **vypnuto**.
   
1. Vyberte **Zkontrolovat a vytvořit**.
   
1. Zkontrolujte nastavení a po úspěšném ověření vyberte **vytvořit**. 

1. Podle pokynů vytvořte druhý virtuální počítač s názvem *MyVM2* a všechna ostatní nastavení se shodují s MyVM1. 
   
   V poli **Skupina zabezpečení sítě** po výběru možnosti **Upřesnit**, vyřaďte dolů a vyberte **MyNetworkSecurityGroup** , který jste už vytvořili. 
   
   V části **Vyberte fond back-end** se ujistěte, že je vybraná možnost **MyBackendPool** . 

### <a name="create-an-nsg-rule-for-the-vms"></a>Vytvoření pravidla NSG pro virtuální počítače

Vytvořte pravidlo skupiny zabezpečení sítě (NSG) pro virtuální počítače, které umožní příchozí připojení přes Internet (HTTP).

>[!NOTE]
>Ve výchozím nastavení má NSG již pravidlo, které otevře port 3389, port vzdálené plochy (RDP).

1. V nabídce vlevo vyberte **Všechny prostředky**. V seznamu prostředků vyberte ve skupině prostředků **MyResourceGroupLB** možnost **MyNetworkSecurityGroup** .
   
1. V části **Nastavení** vyberte **Příchozí pravidla zabezpečení** a potom vyberte **Přidat**.
   
1. V dialogovém okně **Přidat příchozí pravidlo zabezpečení** zadejte nebo vyberte následující:
   
   - **Zdroj**: vyberte **tag služby**.  
   - **Značka zdrojové služby**: vyberte možnost **Internet**. 
   - **Rozsahy cílových portů**: zadejte *80*.
   - **Protokol**: vyberte **TCP**. 
   - **Akce**: vyberte možnost **udělit**.  
   - **Priorita**: typ *100*. 
   - **Název**: zadejte *MyHTTPRule*. 
   - **Popis**: typ *povoluje protokol HTTP*. 
   
1. Vyberte **Přidat**. 
   
   ![Vytvoření pravidla skupiny zabezpečení sítě](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení

V této části provedete kontrolu fondu back-endu nástroje pro vyrovnávání zatížení a nakonfigurujete pravidla testování stavu a provozu nástroje pro vyrovnávání zatížení.

### <a name="view-the-back-end-address-pool"></a>Zobrazit fond back-endu adres

K distribuci provozu do virtuálních počítačů používá nástroj pro vyrovnávání zatížení fond back-end adres, který obsahuje IP adresy rozhraní virtuální sítě (nic), které jsou připojené k nástroji pro vyrovnávání zatížení. 

Vytvořili jste fond back-endu nástroje pro vyrovnávání zatížení a přidali do něj virtuální počítače při vytváření virtuálních počítačů. Můžete také vytvořit fondy back-endu a přidat nebo odebrat virtuální počítače ze stránky **back-end fondů** nástroje pro vyrovnávání zatížení. 

1. V nabídce vlevo vyberte **všechny prostředky** a v seznamu prostředků vyberte **MyLoadBalancer** .
   
1. V části **Nastavení** vyberte **back-endové fondy**.
   
1. Na stránce **back-end fondy** rozbalte **MyBackendPool** a ujistěte se, že jsou uvedené **VM1** i **VM2** .

1. Vyberte **MyBackendPool**. 
   
   Na stránce **MyBackendPool** v části **virtuální počítač** a **IP adresa** můžete odebrat nebo přidat dostupné virtuální počítače do fondu.

Nové fondy back-endu můžete vytvořit tak, že na stránce back-end **fondy** vyberete **Přidat** .

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Pokud chcete nástroji pro vyrovnávání zatížení dovolit monitorování stavu virtuálního počítače, použijte sondu stavu. Sonda stavu dynamicky přidává virtuální počítače do oběhu nástroje pro vyrovnávání zatížení nebo je z něj odebírá na základě jejich reakce na kontroly stavu. 

1. V nabídce vlevo vyberte **všechny prostředky** a v seznamu prostředků vyberte **MyLoadBalancer** .
   
1. V **Nastavení** vyberte **Sondy stavu** a potom vyberte **Přidat**.
   
1. Na stránce **Přidat sondu stavu** zadejte nebo vyberte následující hodnoty:
   
   - **Název**: zadejte *MyHealthProbe*.
   - **Protokol**: rozevírací seznam a výběr **http**. 
   - **Port**: typ *80*. 
   - **Cesta**: přijmout */* pro výchozí identifikátor URI. Tuto hodnotu můžete nahradit jakýmkoliv jiným identifikátorem URI. 
   - **Interval**: typ *15*. Interval je počet sekund mezi pokusy o sondy.
   - **Prahová hodnota chybného stavu**: typ *2*. Tato hodnota představuje počet po sobě jdoucích selhání testu, ke kterým dojde, když se virtuální počítač považuje za špatný.
   
1. Vyberte **OK**.
   
   ![Přidání sondy](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje, jak se provoz distribuuje do virtuálních počítačů. Pravidlo definuje konfiguraci front-end IP adres pro příchozí provoz, fond back-end IP adres pro příjem provozu a požadovaný zdrojový a cílový port. 

Pravidlo nástroje pro vyrovnávání zatížení s názvem **MyLoadBalancerRule** naslouchá na portu 80 ve front-endu **LoadBalancerFrontEnd**. Pravidlo odesílá síťový provoz do fondu back-end adres **MyBackendPool**, a to i na portu 80. 

1. V nabídce vlevo vyberte **všechny prostředky** a v seznamu prostředků vyberte **MyLoadBalancer** .
   
1. V části **Nastavení** vyberte **Pravidla vyrovnávání zatížení** a potom vyberte **Přidat**.
   
1. Na stránce **Přidat pravidlo vyrovnávání zatížení** zadejte nebo vyberte následující hodnoty:
   
   - **Název**: zadejte *MyLoadBalancerRule*.
   - **Protokol**: vyberte **TCP**.
   - **Port**: typ *80*.
   - **Back-end port**: typ *80*.
   - **Back-end fond**: vyberte **MyBackendPool**.
   - **Sonda stavu**: vyberte **MyHealthProbe**. 
   
1. Vyberte **OK**.
   
   ![Přidání pravidla vyrovnávání zatížení](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>Vytvoření pravidla předávání portů příchozího překladu adres (NAT)

Vytvořte příchozí pravidlo překladu síťových adres (NAT) pro vyrovnávání zatížení pro přenos provozu z konkrétního portu front-endové IP adresy do konkrétního portu back-endu virtuálního počítače.

1. V nabídce na levé straně vyberte **všechny prostředky** a v seznamu prostředků vyberte **MyLoadBalancer** .
   
1. V části **Nastavení** vyberte **pravidla příchozího překladu adres (NAT)** a pak vyberte **Přidat**. 
   
1. Na stránce **Přidat pravidlo příchozího překladu adres (NAT)** zadejte nebo vyberte následující hodnoty:
   
   - **Název**: zadejte *MyNATRuleVM1*.
   - **Port**: typ *4221*.
   - **Cílový virtuální počítač**: v rozevíracím seznamu vyberte **MyVM1** .
   - **Konfigurace IP sítě**: z rozevíracího seznamu vyberte **ipconfig1** .
   - **Mapování portů**: vyberte **vlastní**.
   - **Cílový port**: typ *3389*.
   
1. Vyberte **OK**.
   
1. Opakujte postup pro přidání příchozího pravidla NAT s názvem *MyNATRuleVM2* s použitím **portu**: *4222* a **cílového virtuálního počítače**: **MyVM2**.

## <a name="test-the-load-balancer"></a>Testování Load Balanceru

V této části nainstalujete Internetová informační služba (IIS) na back-endové servery a přizpůsobíte výchozí webovou stránku tak, aby zobrazovala název počítače. Pak použijete veřejnou IP adresu nástroje pro vyrovnávání zatížení k otestování nástroje pro vyrovnávání zatížení. 

Každý virtuální počítač back-end slouží jako jiná verze výchozí webové stránky IIS, takže můžete vidět požadavky na distribuci vyrovnávání zatížení mezi dvěma virtuálními počítači.

### <a name="connect-to-the-vms-with-rdp"></a>Připojení k virtuálním počítačům pomocí protokolu RDP

Připojte se ke každému virtuálnímu počítači pomocí vzdálené plochy (RDP). 

1. Na portálu vyberte **všechny prostředky** v nabídce vlevo. V seznamu prostředků vyberte všechny virtuální počítače ve skupině prostředků **MyResourceGroupLB** .
   
1. Na stránce **Přehled** vyberte **připojit** a pak vyberte **Stáhnout soubor RDP**. 
   
1. Otevřete soubor RDP, který jste stáhli, a vyberte **připojit**.
   
1. Na obrazovce zabezpečení systému Windows vyberte **Další možnosti** a pak **použijte jiný účet**. 
   
   Zadejte uživatelské jméno *azureuser* a heslo *Azure1234567* a vyberte **OK**.
   
1. Odpovědět **Ano** na výzvu k zadání certifikátu. 
   
   Pracovní plochu virtuálního počítače se otevře v novém okně. 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>Instalace služby IIS a nahrazení výchozí webové stránky služby IIS 

Pomocí PowerShellu nainstalujte službu IIS a nahraďte výchozí webovou stránku služby IIS stránkou, která zobrazuje název virtuálního počítače.

1. V MyVM1 a v MyVM2 spusťte **prostředí Windows PowerShell** z nabídky **Start** . 

2. Spuštěním následujících příkazů nainstalujte službu IIS a nahraďte výchozí webovou stránku služby IIS:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    
   ```
   
1. Kliknutím na **Odpojit** zavřete připojení RDP k MyVM1 a MyVM2. Virtuální počítače neukončíte.

### <a name="test-load-balancing"></a>Vyrovnávání zatížení testu

1. Na portálu na stránce **Přehled** pro **MYLOADBALANCER** zkopírujte veřejnou IP adresu do pole **Veřejná IP adresa**. Najeďte myší na adresu a výběrem ikony **kopírování** ji zkopírujte. V tomto příkladu je **40.67.218.235**. 
   
1. Do adresního řádku v internetovém prohlížeči vložte nebo zadejte veřejnou IP adresu nástroje pro vyrovnávání zatížení (*40.67.218.235*). 
   
   V prohlížeči se zobrazí výchozí stránka vlastní webový server služby IIS. Zpráva čte buď **Hello World z MyVM1**, nebo **Hello World z MyVM2**.
   
   ![Nová výchozí stránka služby IIS](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. Aktualizujte prohlížeč, aby se zobrazila distribuce provozu nástroje pro vyrovnávání zatížení napříč virtuálními počítači. V některých případech se zobrazí stránka **MyVM1** a jindy se zobrazí stránka **MyVM2** , protože nástroj pro vyrovnávání zatížení distribuuje požadavky na každý virtuální počítač back-end.
   
   >[!NOTE]
   >Možná budete muset vymazat mezipaměť prohlížeče nebo otevřít nové okno prohlížeče mezi pokusy.

## <a name="test-port-forwarding"></a>Test přesměrování portů

S přesměrováním portů můžete použít vzdálenou plochu k back-endovému virtuálnímu počítači pomocí IP adresy nástroje pro vyrovnávání zatížení a hodnoty portu front-end definované v pravidle NAT. 

1. Na portálu na stránce **Přehled** pro **MyLoadBalancer** zkopírujte jeho veřejnou IP adresu. Najeďte myší na adresu a výběrem ikony **kopírování** ji zkopírujte. V tomto příkladu je **40.67.218.235**. 
   
1. Otevřete příkazový řádek a pomocí následujícího příkazu vytvořte relaci vzdálené plochy s MyVM2 pomocí veřejné IP adresy nástroje pro vyrovnávání zatížení a portu front-end, který jste definovali v pravidle NAT virtuálního počítače. 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. Otevřete stažený soubor RDP a vyberte **připojit**.
   
1. Na obrazovce zabezpečení systému Windows vyberte **Další možnosti** a pak **použijte jiný účet**. 
   
   Zadejte uživatelské jméno *azureuser* a heslo *Azure1234567* a vyberte **OK**.
   
1. Odpovědět **Ano** na výzvu k zadání certifikátu. 
   
   Plocha MyVM2 se otevře v novém okně. 

Připojení RDP je úspěšné, protože příchozí pravidlo NAT **MyNATRuleVM2** směruje provoz z front-endového portu pro vyrovnávání zatížení 4222 na MyVM2's port 3389 (port RDP).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odstranit Nástroj pro vyrovnávání zatížení a všechny související prostředky, pokud je už nepotřebujete, otevřete skupinu prostředků **MyResourceGroupLB** a vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili standardní veřejný Nástroj pro vyrovnávání zatížení. Vytvořili jste a nakonfigurovali síťové prostředky, back-endové servery, sondu stavu a pravidla pro nástroj pro vyrovnávání zatížení. Nainstalovali jste službu IIS na back-endové virtuální počítače a pomocí veřejné IP adresy nástroje pro vyrovnávání zatížení otestujete Nástroj pro vyrovnávání zatížení. Nastavili jste a otestovali předávání portů ze zadaného portu v nástroji pro vyrovnávání zatížení do portu na back-endovém virtuálním počítači. 

Další informace o Azure Load Balancer najdete v dalších kurzech k vyrovnávání zatížení.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
