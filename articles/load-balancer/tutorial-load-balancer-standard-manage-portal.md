---
title: 'Kurz: Internetový provoz vyrovnávání zatížení na virtuální počítače – portál Azure'
titleSuffix: Azure Load Balancer
description: Tento kurz ukazuje, jak vytvořit a spravovat Standard Load Balancer pomocí webu Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create and Standard Load Balancer so that I can load balance internet traffic to VMs and add and remove VMs from the load-balanced set.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 8961a50490bdbf8b456e87e1c00577c2c8afd050
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2020
ms.locfileid: "80240368"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Kurz: Internetová návštěvnost vyrovnávání zatížení na virtuální počítače pomocí portálu Azure

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky na více virtuálních počítačů. V tomto kurzu se dozvíte o různých součástech Azure Standard Balancer, které distribuují internetový provoz do virtuálních počítačů a poskytují vysokou dostupnost. Získáte informace o těchto tématech:


> [!div class="checklist"]
> * Vytvoření Azure Balancer
> * Vytvořit prostředky pro vyrovnávání zatížení
> * Vytvoření virtuálních počítačů a instalace serveru IIS
> * Zobrazit vyrovnávání zatížení v akci
> * Přidání a odebrání virtuálních mích z doby tísně zatížení

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete. 

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="create-a-standard-load-balancer"></a>Vytvoření Load Balanceru úrovně Standard

V této části vytvoříte standardní nástroj pro vyrovnávání zatížení, který pomáhá virtuální počítače pro vyrovnávání zatížení. Load Balancer úrovně Standard podporuje pouze standardní veřejnou IP adresu. Při vytváření Standard Load Balanceru musíte vytvořit také novou standardní veřejnou IP adresu nakonfigurovanou jako jeho front-end (ve výchozím nastavení má název *LoadBalancerFrontend*). 

1. V levém horním rohu obrazovky klepněte na tlačítko Vytvořit nástroje**pro vyrovnávání zatížení****sítě** >  **.** > 
2. Na kartě **Základy** na stránce **Vytvořit vyrovnávání zatížení** zadejte nebo vyberte následující informace, přijměte výchozí hodnoty zbývajících nastavení a pak vyberte **Zkontrolovat + vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **Vytvořit nový** a do textového pole zadejte *myResourceGroupSLB.*|
    | Name (Název)                   | *myLoadBalancer*                                   |
    | Region (Oblast)         | Vyberte **Západní Evropa**.                                        |
    | Typ          | Vyberte **Možnost Veřejné**.                                        |
    | Skladová jednotka (SKU)           | Vyberte **standardní**.                          |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. |
    | Název veřejné IP adresy              | Do textového pole zadejte *adresu myPublicIP.*   |
    |Zóna dostupnosti| Vyberte **redundantní zónu**.    |

3. Na kartě **Revize + vytvoření** klikněte na **Vytvořit**.

   ![Vytvoření Load Balanceru úrovně Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Vytvořit prostředky pro vyrovnávání zatížení

V této části nakonfigurujete nastavení vykladače zatížení pro fond back-endových adres, sondu stavu a určíte pravidlo vyvažovače.

### <a name="create-a-backend-address-pool"></a>Vytvoření fondu back-endových adres

Chcete-li distribuovat provoz na virtuální počítače, fond back-endových adres obsahuje IP adresy virtuálních (NIC) připojených k nástroji pro vyrovnávání zatížení. Vytvořte back-endový fond adres *myBackendPool* tak, aby zahrnoval virtuální počítače pro vyrovnávání zatížení internetového provozu.

1. V yberte **Všechny služby** v levé nabídce, vyberte **Všechny prostředky**a klikněte na **myLoadBalancer** ze seznamu zdrojů.
2. V části **Nastavení** klikněte na **Back-endové fondy** a pak klikněte na **Přidat**.
3. Na stránce **Přidat back-endový fond** zadejte název *myBackendPool*jako název back-endového fondu a pak vyberte **Přidat**.

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Chcete-li povolit nástroj pro vyrovnávání zatížení sledovat stav vaší aplikace, použijte sondu stavu. Sonda stavu dynamicky přidává nebo odebere virtuální chody z rotace vykladače zatížení na základě jejich reakce na kontroly stavu. Vytvořte sondu stavu *myHealthProbe* pro monitorování stavu virtuálních počítačů.

1. V yberte **Všechny služby** v levé nabídce, vyberte **Všechny prostředky**a klikněte na **myLoadBalancer** ze seznamu zdrojů.
2. V části **Nastavení** klikněte na **Sondy stavu** a pak klikněte na **Přidat**.
3. K vytvoření sondy stavu použijte tyto hodnoty:
     
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte *myHealthProbe*. |
    | Protocol (Protokol) | Vyberte **možnost HTTP**. |
    | Port | Zadejte *80*.|
    | Interval | Zadejte *hodnotu 15* pro počet **intervalů** v sekundách mezi pokusy o sondu. |
    | Prahová hodnota pro poškozený stav | Vyberte *2* pro počet **selhání nefunkční prahové hodnoty** nebo po sobě jdoucích selhání sondy, ke kterým musí dojít, než je virtuální virtuální ms považován za nefunkční.|
    
4. Vyberte **OK**.

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla Load Balanceru

Pravidlo Load Balanceru slouží k definování způsobu distribuce provozu do virtuálních počítačů. Nadefinujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Vytvořte pravidlo vykladače zatížení *myLoadBalancerRuleWeb* pro poslech portu 80 v *frontendu FrontendLoadBalancer* a odesílání zatížení s vyrovnáváním síťového provozu do back-endového fondu adres *myBackEndPool* také pomocí portu 80.

1. V yberte **Všechny služby** v levé nabídce, vyberte **Všechny prostředky**a klikněte na **myLoadBalancer** ze seznamu zdrojů.
2. V části **Nastavení** klikněte na **Pravidla vyrovnávání zatížení** a pak klikněte na **Přidat**.
3. Tyto hodnoty slouží ke konfiguraci pravidla vyrovnávání zatížení:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte *myHTTPRule*. |
    | Protocol (Protokol) | Vyberte **tcp**. |
    | Port | Zadejte *80*.|
    | Back-endový port | Zadejte *80*. |
    | Back-endový fond | Vyberte *myBackendPool*.|
    | Sonda stavu | Vyberte *myHealthProbe*. |
    
4. Pro ostatní nastavení nechte zvolené výchozí hodnoty a vyberte **OK**.

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části vytvoříte virtuální síť, vytvoříte tři virtuální počítače pro back-endový fond nástroje pro vyrovnávání zatížení a pak nainstalujete službu IIS na virtuální počítače, abyste pomohli otestovat nástroj pro vyrovnávání zatížení.

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

V této části budete muset nahradit následující parametry v krocích s níže uvedenými informacemi:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<>název skupiny prostředků**  | myResourceGroupSLB (Vyberte existující skupinu prostředků) |
| **\<>názvů virtuálních sítí** | myVNet          |
| **\<>názvu oblasti**          | Západní Evropa      |
| **\<>adresního prostoru IPv4**   | 10.1.0.0/16          |
| **\<>názvu podsítě**          | mySubnet        |
| **\<>rozsah emitované sítě** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Standardní vyrovnávání zatížení podporuje pouze virtuální chod se standardními IP adresami v back-endovém fondu. V této části vytvoříte tři virtuální hody *(myVM1*, *myVM2*a *myVM3)* se standardní veřejnou IP adresou ve třech různých zónách *(zóna 1*, *zóna 2*a *zóna 3),* které jsou přidány do back-endového fondu standardního vyrovnávání zatížení, který byl vytvořen dříve.

1. Na levé horní straně portálu vyberte **Vytvořit prostředek** > **Compute** > **Windows Server 2016 Datacenter**. 
   
1. Do **pole Vytvořit virtuální počítač**zadejte nebo vyberte na kartě **Základy** následující hodnoty:
   - **Subscription** > **Skupina prostředků předplatného**: Vyberte **položku myResourceGroupSLB**.
   - **Podrobnosti instance** > **Název virtuálního počítače**: Zadejte *myVM1*.
   - **Oblast podrobností** > instance **>** vyberte možnost **Západní Evropa**.
   - **Možnosti** > **dostupnosti** podrobností instance > Vybrat **zóny dostupnosti**. 
   - **Zóna dostupnosti podrobností instance** > **Availability zone** > Vybrat **1**.
  
1. Vyberte kartu **Síť** nebo **další: Disky**, pak **Další: Síť**. 
   
   - Ujistěte se, že jsou vybrány následující:
       - **Virtuální síť**: **myVnet**
       - **Podsíť**: **myBackendSubnet**
       - **Veřejná IP >** vyberte **Vytvořit nový**a v okně Vytvořit **veřejnou IP adresu** pro **skladovou položku**, vyberte **standardní**a pro **zónu Dostupnost**, vyberte **redundantní zónu**
      
   - Chcete-li vytvořit novou skupinu zabezpečení sítě (NSG), typ brány firewall v části **Skupina zabezpečení sítě**vyberte **upřesnit**. 
       1. V poli **Konfigurovat skupinu zabezpečení sítě** vyberte vytvořit **nový**. 
       1. Zadejte *příkaz myNetworkSecurityGroup*a vyberte **ok**.

   - Chcete-li, aby se virtuální virtuální hotel zasoučástnění back-endového fondu vykladače zatížení, proveďte následující kroky:
        - V **nástroj Vyrovnávání zatížení**v části Umístění tohoto **Yes** **virtuálního počítače za existující řešení vyrovnávání zatížení?**
        - V **nastavení vyrovnávání zatížení**vyberte v části **Možnosti vyrovnávání zatížení** **vyberte nástroje Provyrovnávání zatížení Azure**.
        - V **části Vyberte vyvažovač zatížení**, *myLoadBalancer*. 
1. Vyberte kartu **Správa** nebo možnost **Další** > **správa**. V části **Sledování**nastavte **diagnostiku spouštění** na **Vypnuto**. 
1. Vyberte **Zkontrolovat a vytvořit**.   
1. Zkontrolujte nastavení a pak vyberte **Vytvořit**.
1. Podle pokynů vytvořte dva další virtuální počítače – *myVM2* a *myVM3*, s veřejnou IP adresou Standardní skladové položky v **zóně dostupnosti** **2** a **3** a všechna ostatní nastavení stejná jako *myVM1*.  

### <a name="create-network-security-group-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

V této části vytvoříte pravidlo skupiny zabezpečení sítě, které povolí příchozí připojení pomocí protokolu HTTP.

1. V yberte **Všechny služby** v levé nabídce, vyberte **Všechny prostředky**a potom ze seznamu prostředků klikněte na **položku myNetworkSecurityGroup,** která je umístěna ve skupině prostředků **myResourceGroupSLB.**
2. V části **Nastavení** klikněte na **Příchozí pravidla zabezpečení** a pak klikněte na **Přidat**.
3. Zadáním následujících hodnot pro příchozí pravidlo zabezpečení *myHTTPRule* povolte příchozí připojení HTTP na portu 80:
    - Jako **Zdroj** zadejte *Značka služby*.
    - Jako **Značka zdrojové služby** zadejte *Internet*.
    - Jako **Rozsahy cílových portů** zadejte *80*.
    - Jako **Protokol** zadejte *TCP*.
    - Jako **Akce** zadejte *Povolit*.
    - Jako **Priorita** zadejte *100*.
    - *myHTTPRule* pro název
    - *Povolit HTTP* - pro popis
4. Vyberte **Přidat**.

### <a name="install-iis-on-vms"></a>Instalace služby IIS na virtuální počítače

1. V yberte **Všechny služby** v levé nabídce, vyberte **Všechny prostředky**a potom ze seznamu zdrojů klikněte na **myVM1,** která se nachází ve skupině prostředků *myResourceGroupSLB.*
2. Na stránce **Přehled** klikněte na **Připojit** a připojte se přes RDP k virtuálnímu počítači.
3. V automaticky otevíraném okně **Připojení k virtuálnímu počítači** vyberte **Stáhnout soubor RDP** a pak otevřete stažený soubor RDP.
4. V okně **Připojení ke vzdálené ploše** klikněte na **Připojit**.
5. Přihlaste se k virtuálnímu počítači pomocí přihlašovacích údajů, které jste zadali při vytváření tohoto virtuálního počítače. Tím se otevře relace vzdálené plochy s virtuálním počítačem *myVM1*.
6. Na ploše serveru přejděte na **windows nástroje pro**>správu Windows**PowerShell**.
7. V okně PowerShellu spuštěním následujících příkazů nainstalujte server služby IIS, odeberte výchozí soubor iisstart.htm a pak přidejte nový soubor iisstart.htm, který zobrazuje název virtuálního počítače:

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Zavřete relaci RDP s *myVM1*.
7. Opakováním kroků 1 až 6 nainstalujte službu IIS a aktualizovaný soubor iisstart.htm na *myVM2* a *myVM3*.

## <a name="test-the-load-balancer"></a>Testování prolivadtá zatížení
1. Na obrazovce **Přehled** vyhledejte veřejnou IP adresu Load Balanceru. V yberte **Všechny služby** v levé nabídce, vyberte **Všechny zdroje**a klepněte na **položku myPublicIP**.

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

      ![Webový server služby IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Pokud chcete zobrazit, jak Balancer navyrovnávání zatížení distribuuje provoz mezi třemi virtuálními počítači, na kterých je spuštěna vaše aplikace, můžete vytvořit vynucenou aktualizaci webového prohlížeče.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Odeberte nebo přidejte virtuální počítače z back-endového fondu.
Na virtuálních počítačích, na kterých je vaše aplikace spuštěná, možná budete potřebovat provést údržbu, například nainstalovat aktualizace operačního systému. Abyste si poradili se zvýšením provozu do vaší aplikace, možná budete muset přidat další virtuální počítače. V této části se zobrazí, jak odebrat nebo přidat virtuální hod *(myVM1)* z balanceru zatížení.

### <a name="remove-vm-from-a-backend-pool"></a>Odebrání virtuálního virtuálního mísy z back-endového fondu
Chcete-li odebrat *myVM1* z back-endového fondu, proveďte následující kroky:

1. V yberte **Všechny služby** v levé nabídce, vyberte **Všechny prostředky**a klikněte na **myLoadBalancer** ze seznamu zdrojů.
2. V části **Nastavení** klikněte na **Back-endové fondy** a v seznamu back-endového fondu klikněte na **myBackendPool**.
3. Chcete-li odebrat *v1* položku **MyBackendPool,** vyberte na konci řádku, který zobrazuje *myVM1*, ikonu odstranění a klepněte na tlačítko **Uložit**.

Když už *myVM1* není v back-endovém fondu adres, můžete na *myVM1* provádět všechny úlohy údržby, jako jsou třeba instalace aktualizací softwaru. V nepřítomnosti *VM1*, zatížení je nyní vyvážena mezi *myVM2* a *myVM3*. 

### <a name="add-vm-to-a-backend-pool"></a>Přidání virtuálního virtuálního montovadou do back-endového fondu
Chcete-li přidat *myVM1* zpět do back-endového fondu, proveďte následující kroky:

1. V yberte **Všechny služby** v levé nabídce, vyberte **Všechny zdroje**a v seznamu zdrojů vyberte **myVM1.**
2. Na stránce **VM1** vyberte v části **Nastavení** **položku Networking**.
3. Na stránce **Síť** vyberte kartu **Vyrovnávání zatížení** a pak vyberte **Přidat vyrovnávání zatížení**.
4. Na stránce **Přidat vyrovnávání zatížení** postupujte takto:
   1. V **části Možnosti vyrovnávání zatížení**vyberte azure **balancer**.
   2. V **části Vybrat vyvažovač zatížení**vyberte *položku myLoadBalancer*.
   3. V **části Vybrat back-endový fond**vyberte *myBackendPool*. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nejsou potřeba, odstraňte skupinu prostředků, vyrovnávání zatížení a všechny související prostředky. Chcete-li tak učinit, vyberte skupinu prostředků *myResouceGroupSLB,* která obsahuje vyrovnávání zatížení, a pak vyberte **odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili standardní vyrovnávání zatížení, připojené virtuální počítače k němu, nakonfiguroval pravidlo provozu vyrovnávání zatížení, sonda stavu a pak testovány vyrovnávání zatížení. Také jste odebrali virtuální počítač ze skupiny s vyrovnáváním zatížení a přidali jste virtuální počítač zpátky do fondu back-endových adres. Chcete-li zjistit další informace o službě Azure Load Balancer, přejděte ke kurzům pro Azure Load Balancer.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
