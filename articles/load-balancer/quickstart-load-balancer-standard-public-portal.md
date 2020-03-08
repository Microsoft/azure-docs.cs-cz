---
title: 'Rychlý Start: vytvoření veřejné Load Balancer – Azure Portal'
titleSuffix: Azure Load Balancer
description: V tomto rychlém startu se dozvíte, jak vytvořit Load Balancer pomocí Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: b424fe315737b84479283eed2d77398c8ce4f148
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898835"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Rychlý Start: vytvoření Load Balancer pro vyrovnávání zatížení virtuálních počítačů pomocí Azure Portal

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky na více virtuálních počítačů. K vytvoření nástroje pro vyrovnávání zatížení virtuálních počítačů můžete použít web Azure Portal. V tomto rychlém startu se dozvíte, jak vyrovnávat zatížení virtuálních počítačů pomocí veřejné Load Balancer.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-load-balancer"></a>Vytvoření Load Balancer

V této části vytvoříte Load Balancer, která pomáhá vyrovnávat zatížení virtuálních počítačů. Můžete vytvořit veřejné Load Balancer nebo interní Load Balancer. Při vytváření veřejné Load Balancer musíte také vytvořit novou veřejnou IP adresu, která je ve výchozím nastavení nakonfigurovaná jako front-end (s názvem jako *LoadBalancerFrontend* ) pro Load Balancer.

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek** > **síťové** > **Load Balancer**.
2. Na kartě **základy** na stránce **vytvořit službu Vyrovnávání zatížení** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **vytvořit nový** a do textového pole zadejte *myResourceGroupSLB* .|
    | Název                   | *myLoadBalancer*                                   |
    | Oblast         | Vyberte **Západní Evropa**.                                        |
    | Typ          | Vyberte možnost **veřejné**.                                        |
    | Skladová položka           | Vyberte **Standard** nebo **Basic**. Microsoft doporučuje pro produkční úlohy Standard. |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. Pokud máte existující veřejnou IP adresu, kterou chcete použít, vyberte **použít existující** . |
    | Název veřejné IP adresy              | Do textového pole zadejte *myPublicIP* .   K vytvoření základní veřejné IP adresy použijte ```-SKU Basic```. Základní veřejné IP adresy nejsou kompatibilní se službou Load Balancer **úrovně Standard** . Microsoft doporučuje používat pro produkční úlohy **Standard** .|
    | Zóna dostupnosti | Typ *zóna – redundantní* pro vytvoření odolného Load Balancer. Pokud chcete vytvořit oblast Load Balancer, vyberte konkrétní zónu z 1, 2 nebo 3. |

> [!IMPORTANT]
> Zbytek v tomto rychlém startu předpokládá, že se během výše uvedeného procesu výběru skladové položky vybere **standardní** SKU.


3. Na kartě **Revize + vytvořit** vyberte **vytvořit**.   

    ![Vytvoření Load Balanceru úrovně Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Vytvoření prostředků Load Balancer

V této části nakonfigurujete Load Balancer nastavení pro fond back-end adres, sondu stavu a zadáváte pravidlo pro vyrovnávání zatížení.

### <a name="create-a-backend-pool"></a>Vytvoření fondu back-endu

Aby bylo možné distribuovat provoz do virtuálních počítačů, fond adres back-endu obsahuje IP adresy virtuálních (síťových rozhraní) připojených k Load Balancer. Vytvořte fond back-end adres *myBackendPool* , který bude zahrnovat virtuální počítače pro internetovou komunikaci s vyrovnáváním zatížení.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myLoadBalancer** .
2. V části **Nastavení**vyberte **back-end fondy**a pak vyberte **Přidat**.
3. Do pole název na stránce **Přidat fond back-end** serveru zadejte *myBackendPool*jako název vašeho back-end fondu a pak vyberte **Přidat**.

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Pokud chcete Load Balancer, aby mohl monitorovat stav vaší aplikace, použijte sondu stavu. Sonda stavu dynamicky přidává nebo odebírá virtuální počítače z Load Balancer rotace na základě jejich reakcí na kontroly stavu. Vytvořte sondu stavu *myHealthProbe* pro monitorování stavu virtuálních počítačů.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myLoadBalancer** .
2. V části **Nastavení**vyberte **sondy stavu**a pak vyberte **Přidat**.
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte *myHealthProbe*. |
    | Protokol | Vyberte **http**. |
    | Port | Zadejte *80*.|
    | Interval | Zadejte hodnotu *15* pro **interval** mezi pokusy o sondu v sekundách. |
    | Prahová hodnota špatného stavu | Vyberte **2** pro počet chybných **prahových hodnot** nebo po sobě jdoucích selhání sondy, ke kterým musí dojít, aby se virtuální počítač považoval za poškozený.|
    | | |
4. Vyberte **OK**.

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla Load Balanceru
Pravidlo Load Balanceru slouží k definování způsobu distribuce provozu do virtuálních počítačů. Definujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Vytvořte pravidlo Load Balancer *myLoadBalancerRuleWeb* pro naslouchání na portu 80 ve front-endu *FrontendLoadBalancer* a odesílání síťového provozu s vyrovnáváním zatížení do fondu back-end adres *myBackEndPool* také pomocí portu 80. 

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředků vyberte **myLoadBalancer** .
2. V části **Nastavení**vyberte **pravidla vyrovnávání zatížení**a pak vyberte **Přidat**.
3. Ke konfiguraci pravidla vyrovnávání zatížení použijte tyto hodnoty:
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte *myHTTPRule*. |
    | Protokol | Vyberte **TCP**. |
    | Port | Zadejte *80*.|
    | Back-endový port | Zadejte *80*. |
    | Back-endový fond | Vyberte *myBackendPool*.|
    | Sonda stavu | Vyberte *myHealthProbe*. |
4. Ponechte zbytek výchozích hodnot a pak vyberte **OK**.


## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části vytvoříte virtuální síť, vytvoříte tři virtuální počítače pro back-end fond Load Balancer a pak na virtuální počítače nainstalujete službu IIS, která vám pomůžou otestovat Load Balancer.

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

V této části budete muset v krocích níže nahradit následující parametry:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<Resource-Group-Name >**  | myResourceGroupSLB |
| **\<název virtuální sítě >** | myVNet          |
| **\<název oblasti >**          | Západní Evropa      |
| **\<IPv4-Address-Space >**   | 10.1.0.0 \ 16          |
| **\<název podsítě >**          | myBackendSubnet        |
| **\<> rozsahu adres** | 10.1.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů
SKU veřejných IP adres a Load Balancer SKU se musí shodovat. V případě Standard Load Balancer použijte virtuální počítače se standardními IP adresami ve fondu back-end. V této části vytvoříte tři virtuální počítače (*myVM1*, *myVM2* a *MYVM3*) se standardní veřejnou IP adresou ve třech různých zónách (*zóna 1*, *zóna 2*a *zóna 3*), které jsou později přidané do fondu back-end Load Balancer, který jste vytvořili dříve. Pokud jste vybrali základní, použijte virtuální počítače se základními IP adresami.

1. V levé horní části portálu vyberte **vytvořit prostředek** > **COMPUTE** > **Windows Server 2019 Datacenter**. 
   
1. V části **vytvořit virtuální počítač**zadejte nebo vyberte následující hodnoty na kartě **základy** :
   - **Předplatné** > **skupinu prostředků**: vyberte **myResourceGroupSLB**.
   - **Podrobnosti Instance** > **název virtuálního počítače**: zadejte *myVM1*.
   - **Podrobnosti Instance** > **oblasti** > vyberte **západní Evropa**.
   - **Podrobnosti Instance** > **Možnosti dostupnosti** > vybrat **zóny dostupnosti**. 
   - **Podrobnosti Instance** > **zóně dostupnosti** > vybrat **1**.
   - **Účet správce**> zadejte **uživatelské jméno**, **heslo** a **potvrzení informací o hesle** .
   - Vyberte kartu **síť** nebo vyberte **Další: disky**a **Další: síť**.
  
1. Na kartě **sítě** zkontrolujte, že jsou vybrané následující:
   - **Virtuální síť**: *myVnet*
   - **Podsíť**: *myBackendSubnet*
   - **Veřejná IP** adresa > vyberte **vytvořit novou**a v okně **vytvořit veřejnou IP adresu** pro položku **SKU**vyberte možnost **Standard**a v části **zóna dostupnosti**vyberte **zóna – redundantní**a pak vyberte **OK**. Pokud jste vytvořili základní Load Balancer, vyberte základní. Microsoft doporučuje pro produkční úlohy používat standardní SKU.
   - Pokud chcete vytvořit novou skupinu zabezpečení sítě (NSG), typ brány firewall, v části **Skupina zabezpečení sítě**vyberte **Upřesnit**. 
       1. V poli **Konfigurovat skupinu zabezpečení sítě** vyberte **vytvořit novou**. 
       1. Zadejte *myNetworkSecurityGroup*a vyberte **OK**.
   - Pokud chcete virtuálnímu počítači udělat součást back-endu fondu Load Balancer, proveďte následující kroky:
        - V případě **Vyrovnávání zatížení** **umístěte tento virtuální počítač za existující řešení vyrovnávání zatížení**a vyberte **Ano**.
        - V **nastavení vyrovnávání zatížení**v možnosti vyrovnávání **zatížení**vyberte **Azure Load Balancer**.
        - Pro **Vyberte nástroj pro vyrovnávání zatížení** *myLoadBalancer*.
        - Vyberte kartu **Správa** nebo vyberte možnost **Další** > **Správa**.
2. Na kartě **Správa** v části **monitorování**nastavte **diagnostiku spouštění** na **vypnuto**. 
1. Vyberte **Zkontrolovat a vytvořit**.   
1. Zkontrolujte nastavení a pak vyberte **vytvořit**.
1. Postupujte podle kroků 2 až 6 a vytvořte dva další virtuální počítače s následujícími hodnotami a všechna ostatní nastavení, která jsou stejná jako *myVM1*:

    | Nastavení | VM 2| VM 3|
    | ------- | ----- |---|
    | Název |  *myVM2* |*myVM3*|
    | Zóna dostupnosti | 2 |3|
    |Veřejná IP adresa| **Standardní** SKLADOVÉ|**Standardní** SKLADOVÉ|
    | Veřejná IP adresa – zóna dostupnosti| **Zóna redundantní** |**Zóna redundantní**|
    | Skupina zabezpečení sítě | Vybrat existující *skupinu myNetworkSecurity*| Vybrat existující *skupinu myNetworkSecurity*|

 ### <a name="create-nsg-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

V této části vytvoříte pravidlo skupiny zabezpečení sítě, které povolí příchozí připojení pomocí protokolu HTTP.

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom ze seznamu prostředky vyberte **myNetworkSecurityGroup** , která je umístěná ve skupině prostředků **myResourceGroupSLB** .
2. V části **Nastavení** vyberte **Příchozí pravidla zabezpečení** a potom vyberte **Přidat**.
3. Zadáním následujících hodnot pro příchozí pravidlo zabezpečení *myHTTPRule* povolte příchozí připojení HTTP na portu 80:
    - **Zdroj**: *značka služby*
    -  **Značka zdrojové služby**: *Internet*
    - **Rozsahy cílových portů**: *80*
    - **Protokol**: *TCP*
    - **Akce**: *Povolení*
    - **Priorita**: *100*
    - **Název**: *myHTTPRule* 
    - **Popis**: "*Allow http* 
4. Vyberte **Přidat**.
5. Opakujte postup pro příchozí pravidlo protokolu RDP, pokud je potřeba, s následujícími hodnotami, které se liší:
   - **Rozsahy cílových portů**: zadejte *3389*.
   - **Priorita**: typ *200*. 
   - **Název**: zadejte *MyRDPRule*. 
   - **Popis**: zadejte *Allow RDP*. 
 
### <a name="install-iis"></a>Instalace služby IIS

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředky vyberte **myVM1** , která je umístěná ve skupině prostředků *myResourceGroupSLB* .
2. Na stránce **Přehled** vyberte **Připojit** a připojte se přes RDP k virtuálnímu počítači.
5. Přihlaste se k virtuálnímu počítači pomocí přihlašovacích údajů, které jste zadali při vytváření tohoto virtuálního počítače. Tím se otevře relace vzdálené plochy s virtuálním počítačem *myVM1*.
6. Na ploše serveru přejděte do části **Nástroje pro správu Windows**>**Windows PowerShell**.
7. V okně PowerShellu spuštěním následujících příkazů nainstalujte server služby IIS, odeberte výchozí soubor iisstart.htm a pak přidejte nový soubor iisstart.htm, který zobrazuje název virtuálního počítače:

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Ukončete relaci RDP s *myVM1*.
7. Opakováním kroků 1 až 6 nainstalujte službu IIS a aktualizovaný soubor iisstart.htm na *myVM2* a *myVM3*.

## <a name="test-the-load-balancer"></a>Otestování Load Balancer
1. Na obrazovce **Přehled** vyhledejte veřejnou IP adresu Load Balanceru. V nabídce na levé straně vyberte **všechny služby** a vyberte **všechny prostředky**a pak vyberte **myPublicIP**.

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

   ![Webový server služby IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Pokud chcete zobrazit Load Balancer distribuci provozu napříč všemi třemi virtuálními počítači, můžete přizpůsobit výchozí stránku webového serveru IIS každého virtuálního počítače a potom vynutit aktualizaci webového prohlížeče z klientského počítače.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, Load Balancer a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků (*myResourceGroupSLB*) obsahující Load Balancer a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Standard Load Balancer k němu připojené virtuální počítače, nakonfigurovali jste pravidlo Load Balancer přenosů dat, sondu stavu a pak jste otestovali Load Balancer. Pokud se chcete dozvědět víc o Azure Load Balancer, pokračujte [Azure Load Balancer kurzy](tutorial-load-balancer-standard-public-zone-redundant-portal.md).

Přečtěte si další informace o [Load Balancer a zónách dostupnosti](load-balancer-standard-availability-zones.md).
