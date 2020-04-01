---
title: 'Kurz: Konfigurace předávání portů – portál Azure'
titleSuffix: Azure Load Balancer
description: Tento kurz ukazuje, jak nakonfigurovat předávání portů pomocí Nástroje pro vyrovnávání zatížení Azure k vytvoření připojení k virtuálním počítačům ve virtuální síti Azure.
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
ms.openlocfilehash: e740a65d453a69a987e938a5170ae8e04c7bfe40
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78249886"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-portal"></a>Kurz: Konfigurace předávání portů v Azure Load Balancer pomocí portálu

Předávání portů umožňuje připojit se k virtuálním počítačům (VM) ve virtuální síti Azure pomocí veřejné IP adresy azure balanceru a čísla portu. 

V tomto kurzu nastavíte předávání portů na Azure Balancer. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte veřejný standardní vyrovnávání zatížení pro vyrovnávání síťového provozu nad virtuálními virtuálními sítěmi. 
> * Vytvořte virtuální síť a virtuální počítače s pravidlem skupiny zabezpečení sítě (NSG). 
> * Přidejte virtuální choddo fondu back-endových adres vykladače zatížení.
> * Vytvořte sondu stavu vykladače zatížení a pravidla provozu.
> * Vytvořte pravidla příchozího předávání portů NAT pro vyrovnávání zatížení.
> * Nainstalujte a nakonfigurujte službu IIS na virtuálních počítačích tak, aby zobrazovala vyrovnávání zatížení a předávání portů v akci.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete. 

Pro všechny kroky v tomto kurzu se [https://portal.azure.com](https://portal.azure.com)přihlaste na webu Portál Azure na .

## <a name="create-a-standard-load-balancer"></a>Vytvoření standardního odvykaču zatížení

Nejprve vytvořte veřejný standardní vyrovnávání zatížení, který může vyvážit zatížení provozu nad virtuálními virtuálními sítěmi. Standardní vyrovnávání zatížení podporuje pouze standardní veřejnou IP adresu. Při vytváření standardního systému vyrovnávání zatížení vytvoříte také novou veřejnou IP adresu Standard, která je ve výchozím nastavení nakonfigurována jako front-end pro vyrovnávání zatížení a pojmenována **LoadBalancerFrontEnd.** 

1. V levém horním rohu obrazovky klepněte na tlačítko Vytvořit nástroje**pro vyrovnávání zatížení****sítě** >  **.** > 
2. Na kartě **Základy** na stránce **Vytvořit vyrovnávání zatížení** zadejte nebo vyberte následující informace, přijměte výchozí hodnoty zbývajících nastavení a pak vyberte **Zkontrolovat + vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **Vytvořit nový** a do textového pole zadejte *MyResourceGroupLB.*|
    | Name (Název)                   | *myLoadBalancer*                                   |
    | Region (Oblast)         | Vyberte **Západní Evropa**.                                        |
    | Typ          | Vyberte **Možnost Veřejné**.                                        |
    | Skladová jednotka (SKU)           | Vyberte **standardní**.                          |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. |
    | Název veřejné IP adresy              | Do textového pole zadejte *adresu myPublicIP.*   |
    |Zóna dostupnosti| Vyberte **redundantní zónu**.    |
     
    >[!NOTE]
     >Ujistěte se, že vytvořit vyrovnávání zatížení a všechny prostředky pro něj v umístění, které podporuje zóny dostupnosti. Další informace naleznete [v tématu Oblasti, které podporují zóny dostupnosti](../availability-zones/az-overview.md#services-support-by-region). 

3. Na kartě **Revize + vytvoření** klikněte na **Vytvořit**.  
  
## <a name="create-and-configure-back-end-servers"></a>Vytvoření a konfigurace serverů back-end

Vytvořte virtuální síť se dvěma virtuálními počítači a přidejte virtuální počítače do back-endového fondu nástroje pro vyrovnávání zatížení. 

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

V této části budete muset nahradit následující parametry v krocích s níže uvedenými informacemi:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<>název skupiny prostředků**  | myResourceGroupLB (Vyberte existující skupinu prostředků) |
| **\<>názvů virtuálních sítí** | myVNet          |
| **\<>názvu oblasti**          | Západní Evropa      |
| **\<>adresního prostoru IPv4**   | 10.3.0.0\16          |
| **\<>názvu podsítě**          | myBackendSubnet        |
| **\<>rozsah emitované sítě** | 10.3.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>Vytvoření virtuálních her a jejich přidání do back-endového fondu vyrovnávání zatížení

1. Na levé horní straně portálu vyberte **Vytvořit prostředek** > **Compute** > **Windows Server 2016 Datacenter**. 
   
1. Do **pole Vytvořit virtuální počítač**zadejte nebo vyberte na kartě **Základy** následující hodnoty:
   - **Subscription** > **Skupina prostředků předplatného**: Rozevírací seznam a výběr **položky MyResourceGroupLB**.
   - **Název virtuálního počítače**: Zadejte *MyVM1*.
   - **Region**: Vyberte **možnost Západní Evropa**. 
   - **Uživatelské jméno**: Zadejte *azureuser*.
   - **Heslo**: Zadejte *Azure1234567*. 
     Znovu zadejte heslo do pole **Potvrdit heslo.**
   
1. Vyberte kartu **Síť** nebo **další: Disky**, pak **Další: Síť**. 
   
   Ujistěte se, že jsou vybrány následující:
   - **Virtuální síť**: **MyVNet**
   - **Podsíť**: **MyBackendSubnet**
   
1. V části **Veřejná IP adresa**vyberte **Vytvořit nový**, na stránce **Vytvořit veřejnou IP adresu** vyberte **Standardní** a pak vyberte **OK**. 
   
1. V části **Skupina zabezpečení sítě**vyberte **Upřesnit,** chcete-li vytvořit novou skupinu zabezpečení sítě (NSG), což je typ brány firewall. 
   1. V poli **Konfigurovat skupinu zabezpečení sítě** vyberte vytvořit **nový**. 
   1. Zadejte *příkaz MyNetworkSecurityGroup*a vyberte **ok**. 
   
   >[!NOTE]
   >Všimněte si, že ve výchozím nastavení nsg již má příchozí pravidlo pro otevření portu 3389, portu vzdálené plochy (RDP).
   
1. Přidejte virtuální ho do back-endového fondu vyrovnávání zatížení, který vytvoříte:
   
   1. V části **VYROVNÁVÁNÍ** > ZATÍŽENÍ Umístěte tento virtuální počítač za **Yes**existující**řešení vyrovnávání zatížení?** 
   1. V **části Možnosti vyrovnávání zatížení**rozbalte a vyberte Azure **balancer**. 
   1. V **části Vyberte vyvažovač elektoru**, rozbalte položku A vyberte **položku MyLoadBalancer**. 
   1. V části **Select a back-end pool**vyberte Create **new**, zadejte *MyBackendPool*a vyberte **Create**. 
   
   ![Vytvoření virtuální sítě](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. Vyberte kartu **Správa** nebo možnost **Další** > **správa**. V části **Sledování**nastavte **diagnostiku spouštění** na **Vypnuto**.
   
1. Vyberte **Zkontrolovat a vytvořit**.
   
1. Zkontrolujte nastavení a po úspěšném ověření vyberte **vytvořit**. 

1. Podle pokynů vytvořte druhý virtuální virtuální počítače s názvem *MyVM2*, přičemž všechna ostatní nastavení budou stejná jako MyVM1. 
   
   V **části Skupina zabezpečení sítě**po výběru **možnosti Upřesnit**rozbalte a vyberte **skupinu MyNetworkSecurityGroup,** kterou jste již vytvořili. 
   
   V části **Vyberte back-endový fond**zkontrolujte, jestli je vybraná možnost **MyBackendPool.** 

### <a name="create-an-nsg-rule-for-the-vms"></a>Vytvoření pravidla nsg pro virtuální chod

Vytvořte pravidlo skupiny zabezpečení sítě (NSG) pro virtuální servery povolit příchozí připojení k Internetu (HTTP).

>[!NOTE]
>Ve výchozím nastavení již skupina zabezpečení zabezpečení zabezpečení má pravidlo, které otevírá port 3389, port vzdálené plochy (RDP).

1. V nabídce vlevo vyberte **Všechny prostředky**. Ze seznamu prostředků vyberte ve skupině prostředků **MyResourceGroupLB** položku **MyNetworkSecurityGroup.**
   
1. V části **Nastavení** vyberte **Příchozí pravidla zabezpečení** a potom vyberte **Přidat**.
   
1. V dialogovém okně **Přidat příchozí pravidlo zabezpečení** zadejte nebo vyberte následující:
   
   - **Zdroj**: Vyberte **výrobní číslo**.  
   - **Zdrojová servisní značka**: Vyberte **Možnost Internet**. 
   - **Rozsahy cílových portů**: Typ *80*.
   - **Protokol**: Vyberte **tcp**. 
   - **Akce**: Vyberte **povolit**.  
   - **Priorita**: typ *100*. 
   - **Název**: Zadejte *myHTTPRule*. 
   - **Popis**: Zadejte *povolit protokol HTTP*. 
   
1. Vyberte **Přidat**. 
   
   ![Vytvoření pravidla skupiny zabezpečení sítě](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení

V této části zkontrolujete back-endový fond vykladače vyrovnávání zatížení a nakonfigurujete sondu stavu a pravidla provozu vykladače zatížení.

### <a name="view-the-back-end-address-pool"></a>Zobrazení fondu adres back-endu

K distribuci provozu na virtuální počítače používá nástroj pro vyrovnávání zatížení back-endový fond adres, který obsahuje IP adresy rozhraní virtuální sítě (NIC), které jsou připojeny k nástrojovi pro vyrovnávání zatížení. 

Vytvořili jste back-endový fond vyrovnávání zatížení a při vytváření virtuálních počítačů jste do něj přidali virtuální počítače. Můžete také vytvořit back-endové fondy a přidat nebo odebrat virtuální mích ze stránky **back-endového fondu** vyrovnávání zatížení. 

1. V levém menu vyberte **Všechny prostředky** a ze seznamu zdrojů vyberte **MyLoadBalancer.**
   
1. V části **Nastavení**vyberte **Back-endové fondy**.
   
1. Na stránce **back-endfondy** rozbalte **MyBackendPool** a ujistěte se, že jsou uvedeny **v Seznamu VM1** i **VM2.**

1. Vyberte **možnost MyBackendPool**. 
   
   Na stránce **MyBackendPool** v části **VIRTUÁLNÍ POČÍTAČ** a **IP adresa**můžete odebrat nebo přidat dostupné virtuální počítače do fondu.

Nové back-endové fondy můžete vytvořit tak, že vyberete **Přidat** na stránce **Back-endové fondy.**

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Chcete-li povolit nástroj pro vyrovnávání zatížení ke sledování stavu virtuálního provozu, použijte sondu stavu. Sonda stavu dynamicky přidává virtuální počítače do oběhu nástroje pro vyrovnávání zatížení nebo je z něj odebírá na základě jejich reakce na kontroly stavu. 

1. V levém menu vyberte **Všechny prostředky** a ze seznamu zdrojů vyberte **MyLoadBalancer.**
   
1. V **Nastavení** vyberte **Sondy stavu** a potom vyberte **Přidat**.
   
1. Na stránce **Přidat sondu stavu** zadejte nebo vyberte následující hodnoty:
   
   - **Název**: Zadejte *MyHealthProbe*.
   - **Protokol**: Rozevírací a vyberte **možnost HTTP**. 
   - **Port**: Typ *80*. 
   - **Cesta:** */* Přijmout pro výchozí identifikátor URI. Tuto hodnotu můžete nahradit jiným identifikátorem URI. 
   - **Interval**: Typ *15*. Interval je počet sekund mezi pokusy o sondu.
   - **Prahová hodnota není v pořádku**: Typ *2*. Tato hodnota je počet po sobě jdoucích selhání sondy, ke kterým dochází před virtuální ms je považovánza nefunkční.
   
1. Vyberte **OK**.
   
   ![Přidání sondy](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje, jak se provoz distribuuje do virtuálních počítačů. Pravidlo definuje konfiguraci ip adres front-endu pro příchozí provoz, back-endový fond IP pro příjem přenosů a požadované zdrojové a cílové porty. 

Pravidlo pro vyrovnávání zatížení s názvem **MyLoadBalancerRule** naslouchá portu 80 v front-endu **LoadBalancerFrontEndEnd .** Pravidlo odesílá síťový provoz do fondu adres back-end **MyBackendPool**, také na portu 80. 

1. V levém menu vyberte **Všechny prostředky** a ze seznamu zdrojů vyberte **MyLoadBalancer.**
   
1. V části **Nastavení** vyberte **Pravidla vyrovnávání zatížení** a potom vyberte **Přidat**.
   
1. Na stránce **Přidat pravidlo vyrovnávání zatížení** zadejte nebo vyberte následující hodnoty:
   
   - **Název**: Zadejte *MyLoadBalancerRule*.
   - **Protokol**: Vyberte **tcp**.
   - **Port**: Typ *80*.
   - **Back-end port**: Typ *80*.
   - **Back-endový fond**: Vyberte **MyBackendPool**.
   - **Zdravotní sonda**: Vyberte **MyHealthProbe**. 
   
1. Vyberte **OK**.
   
   ![Přidání pravidla vyrovnávání zatížení](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>Vytvoření pravidla pro předávání příchozího portu NAT

Vytvořte pravidlo pro překlad síťových adres (NAT) pro předávání přenosů z konkrétního portu adresy IP front-endu na konkrétní port back-endového virtuálního účtu.

1. V yberte **Všechny prostředky** v levé nabídce a pak ze seznamu zdrojů vyberte **MyLoadBalancer.**
   
1. V části **Nastavení**vyberte **Pravidla příchozího nat**a pak vyberte **Přidat**. 
   
1. Na stránce **Přidat příchozí pravidla NAT** zadejte nebo vyberte následující hodnoty:
   
   - **Název**: Zadejte *mynatrulevm1*.
   - **Port**: Typ *4221*.
   - **Cílový virtuální počítač**: Z rozbalovací nabídky vyberte **MyVM1.**
   - **Konfigurace IP sítě**: Z rozevíracího souboru vyberte **ipconfig1.**
   - **Mapování portů**: Vyberte **vlastní**.
   - **Cílový port**: Typ *3389*.
   
1. Vyberte **OK**.
   
1. Opakováním kroků přidejte pravidlo příchozího nat s názvem *MyNATRuleVM2*pomocí **portu**: *4222* a **Cílového virtuálního počítače**: **MyVM2**.

## <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

V této části nainstalujete Internetovou informační službu (IIS) na servery back-end a upravíte výchozí webovou stránku tak, aby zobrazovala název počítače. Potom použijete veřejnou IP adresu vykladače zatížení k testování vykladače zatížení. 

Každý back-endový virtuální virtuální server slouží jiné verzi výchozí webové stránky služby IIS, takže uvidíte, že vyrovnávání zatížení distribuuje požadavky mezi dva virtuální servery.

### <a name="connect-to-the-vms-with-rdp"></a>Připojení k virtuálním kvadám pomocí RDP

Připojte se ke každému virtuálnímu počítači pomocí vzdálené plochy (RDP). 

1. Na portálu vyberte **všechny prostředky** v levé nabídce. Ze seznamu prostředků vyberte každý virtuální ms ve skupině prostředků **MyResourceGroupLB.**
   
1. Na stránce **Přehled** vyberte **Připojit**a pak **vyberte Stáhnout soubor RDP**. 
   
1. Otevřete stažený soubor RDP a vyberte **Připojit**.
   
1. Na obrazovce Zabezpečení Systému Windows vyberte **Další volby** a **potom použijte jiný účet**. 
   
   Zadejte uživatelské jméno *azureuser* a heslo *Azure1234567*a vyberte **OK**.
   
1. Odpověď **Ano** na výzvu k certifikátu. 
   
   Plocha virtuálního počítače se otevře v novém okně. 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>Instalace služby IIS a nahrazení výchozí webové stránky služby IIS 

Pomocí prostředí PowerShell nainstalujte službu IIS a nahraďte výchozí webovou stránku služby IIS stránkou, která zobrazuje název virtuálního soudu.

1. Na MyVM1 a MyVM2 spusťte **prostředí Windows PowerShell** z nabídky **Start.** 

2. Chcete-li nainstalovat službu IIS a nahradit výchozí webovou stránku služby IIS, spusťte následující příkazy:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    
   ```
   
1. Zavřete připojení RDP pomocí myvm1 a myvm2 výběrem **možnosti Odpojit**. Nevypínejte virtuální měsíč.

### <a name="test-load-balancing"></a>Zkušební vyrovnávání zatížení

1. Na portálu zkopírujte na stránce **Přehled** pro **MyLoadBalancer**veřejnou IP adresu pod **veřejnou IP adresou**. Najeďte na adresu a vyberte ikonu **Kopírovat,** kterou chcete zkopírovat. V tomto příkladu je **40.67.218.235**. 
   
1. Vložte nebo zadejte veřejnou IP adresu vykladače zatížení (*40.67.218.235*) do adresního řádku vašeho internetového prohlížeče. 
   
   Přizpůsobená výchozí stránka webového serveru služby IIS se zobrazí v prohlížeči. Zpráva čte buď **Hello World z MyVM1**, nebo **Hello World z MyVM2**.
   
   ![Nová výchozí stránka iis](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. Aktualizujte prohlížeč, abyste viděli, jak balancer na vyrovnávání zatížení distribuuje provoz mezi virtuálními počítačemi. Někdy se zobrazí stránka **MyVM1** a jindy se zobrazí stránka **MyVM2,** protože vyrovnávání zatížení distribuuje požadavky na každý back-endový virtuální počítače.
   
   >[!NOTE]
   >Možná budete muset vymazat mezipaměť prohlížeče nebo otevřít nové okno prohlížeče mezi pokusy.

## <a name="test-port-forwarding"></a>Test přesměrování portů

Při předávání portů můžete vzdálenou plochu k back-endovému virtuálnímu počítači pomocí IP adresy nástroje pro vyrovnávání zatížení a hodnoty front-endového portu definované v pravidle NAT. 

1. Na portálu na stránce **Přehled** pro **MyLoadBalancer**zkopírujte jeho veřejnou IP adresu. Najeďte na adresu a vyberte ikonu **Kopírovat,** kterou chcete zkopírovat. V tomto příkladu je **40.67.218.235**. 
   
1. Otevřete příkazový řádek a pomocí následujícího příkazu vytvořte relaci vzdálené plochy s MyVM2 pomocí veřejné IP adresy nástroje pro vyrovnávání zatížení a front-endového portu, který jste definovali v pravidle NAT virtuálního počítače. 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. Otevřete stažený soubor RDP a vyberte **Připojit**.
   
1. Na obrazovce Zabezpečení Systému Windows vyberte **Další volby** a **potom použijte jiný účet**. 
   
   Zadejte uživatelské jméno *azureuser* a heslo *Azure1234567*a vyberte **OK**.
   
1. Odpověď **Ano** na výzvu k certifikátu. 
   
   MyVM2 desktop se otevře v novém okně. 

Připojení RDP úspěšné, protože příchozí pravidlo NAT **MyNATRuleVM2** směruje provoz z front-end portu nástroj pro vyrovnávání zatížení 4222 na port MyVM2 3389 (port RDP).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odstranit správce zatížení a všechny související prostředky, když je již nepotřebujete, otevřete skupinu prostředků **MyResourceGroupLB** a vyberte **odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili standardní veřejný vyrovnávání zatížení. Vytvořili jste a nakonfigurovali síťové prostředky, servery back-end, sondu stavu a pravidla pro vyrovnávání zatížení. Nainstalovali jste iis na back-end ové virtuální choda a použili jste veřejnou IP adresu vykladače zatížení k testování zařízení pro vyrovnávání zatížení. Nastavení a testování předávání portů z určeného portu na vyrovnávání zatížení na port na back-end virtuálním počítači. 

Další informace o Azure Load Balancer, pokračujte další kurzy pro vyrovnávání zatížení.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
