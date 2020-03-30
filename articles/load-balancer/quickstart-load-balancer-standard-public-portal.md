---
title: Úvodní příručka:Vytvoření veřejného nástrojů pro vyrovnávání zatížení – portál Azure
titleSuffix: Azure Load Balancer
description: Tento rychlý start ukazuje, jak vytvořit vyrovnávání zatížení pomocí portálu Azure.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78898835"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Úvodní příručka: Vytvoření nástrojů pro vyrovnávání zatížení pro vyrovnávání zatížení virtuálních počítačů pomocí portálu Azure

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky na více virtuálních počítačů. K vytvoření nástroje pro vyrovnávání zatížení virtuálních počítačů můžete použít web Azure Portal. Tento rychlý start ukazuje, jak vyrovnávání zatížení virtuálních ms pomocí veřejného vyrovnávání zatížení.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete. 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="create-a-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

V této části vytvoříte nástroj pro vyrovnávání zatížení, který pomáhá virtuální počítače pro vyrovnávání zatížení. Můžete vytvořit veřejný systém vyrovnávání zatížení nebo interní systém vyrovnávání zatížení. Při vytváření veřejného vykladače zatížení musíte také vytvořit novou veřejnou IP adresu, která je nakonfigurována jako front-end (ve výchozím nastavení pojmenovaný jako *LoadBalancerFrontend)* pro vykladač zatížení.

1. V levém horním rohu obrazovky vyberte Vytvořit nástroje**pro vyrovnávání zatížení****sítě** >  **.** > 
2. Na kartě **Základy** na stránce **Vytvořit vyrovnávání zatížení** zadejte nebo vyberte následující informace, přijměte výchozí hodnoty zbývajících nastavení a pak vyberte **Zkontrolovat + vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **Vytvořit nový** a do textového pole zadejte *myResourceGroupSLB.*|
    | Name (Název)                   | *myLoadBalancer*                                   |
    | Region (Oblast)         | Vyberte **Západní Evropa**.                                        |
    | Typ          | Vyberte **Možnost Veřejné**.                                        |
    | Skladová jednotka (SKU)           | Vyberte **standardní** nebo **základní**. Společnost Microsoft doporučuje standard pro produkční úlohy. |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. Pokud máte existující veřejnou IP adresu, kterou chcete použít, vyberte **Použít stávající** |
    | Název veřejné IP adresy              | Do textového pole zadejte *adresu myPublicIP.*   Slouží ```-SKU Basic``` k vytvoření základní veřejné IP adresy. Základní veřejné IP adresy nejsou kompatibilní se **standardním** vyvažovačem zatížení. Společnost Microsoft doporučuje používat **standard** pro produkční úlohy.|
    | Zóna dostupnosti | Zadejte *Zone redundantní* vytvořit odolný vyrovnávání zatížení. Chcete-li vytvořit zonální vyvažovač zatížení, vyberte určitou zónu od 1, 2 nebo 3 |

> [!IMPORTANT]
> Zbytek tohoto rychlého startu předpokládá, že **standardní** skladová položka je vybrána během výše uvedeného procesu výběru skladové položky.


3. Na kartě **Revize + vytvoření** vyberte **Vytvořit**.   

    ![Vytvoření Load Balanceru úrovně Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Vytvořit prostředky pro vyrovnávání zatížení

V této části nakonfigurujete nastavení vykladače zatížení pro fond back-endových adres, sondu stavu a určíte pravidlo vyvažovače.

### <a name="create-a-backend-pool"></a>Vytvoření back-endu fondu

Chcete-li distribuovat provoz na virtuální počítače, fond back-endových adres obsahuje IP adresy virtuálních (NIC) připojených k nástroji pro vyrovnávání zatížení. Vytvořte back-endový fond adres *myBackendPool* tak, aby zahrnoval virtuální počítače pro vyrovnávání zatížení internetového provozu.

1. V yberte **Všechny služby** v levé nabídce, vyberte **Všechny prostředky**a pak vyberte **myLoadBalancer** ze seznamu zdrojů.
2. V části **Nastavení**vyberte **Back-endové fondy**a pak vyberte **Přidat**.
3. Na stránce **Přidat back-endový fond** zadejte název *myBackendPool*jako název back-endového fondu a pak vyberte **Přidat**.

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Chcete-li povolit nástroj pro vyrovnávání zatížení sledovat stav vaší aplikace, použijte sondu stavu. Sonda stavu dynamicky přidává nebo odebere virtuální chody z rotace vykladače zatížení na základě jejich reakce na kontroly stavu. Vytvořte sondu stavu *myHealthProbe* pro monitorování stavu virtuálních počítačů.

1. V yberte **Všechny služby** v levé nabídce, vyberte **Všechny prostředky**a pak vyberte **myLoadBalancer** ze seznamu zdrojů.
2. V části **Nastavení**vyberte **Sondy stavu a**pak vyberte **Přidat**.
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte *myHealthProbe*. |
    | Protocol (Protokol) | Vyberte **možnost HTTP**. |
    | Port | Zadejte *80*.|
    | Interval | Zadejte *hodnotu 15* pro počet **intervalů** v sekundách mezi pokusy o sondu. |
    | Prahová hodnota pro poškozený stav | Vyberte **2** pro počet **selhání nefunkční prahové hodnoty** nebo po sobě jdoucích selhání sondy, ke kterým musí dojít, než je virtuální virtuální ms považován za nefunkční.|
    | | |
4. Vyberte **OK**.

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla Load Balanceru
Pravidlo Load Balanceru slouží k definování způsobu distribuce provozu do virtuálních počítačů. Nadefinujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Vytvořte pravidlo vykladače zatížení *myLoadBalancerRuleWeb* pro poslech portu 80 v *frontendu FrontendLoadBalancer* a odesílání zatížení s vyrovnáváním síťového provozu do back-endového fondu adres *myBackEndPool* také pomocí portu 80. 

1. V yberte **Všechny služby** v levé nabídce, vyberte **Všechny prostředky**a pak vyberte **myLoadBalancer** ze seznamu zdrojů.
2. V části **Nastavení**vyberte **Pravidla vyrovnávání zatížení**a pak vyberte **Přidat**.
3. Ke konfiguraci pravidla vyrovnávání zatížení použijte tyto hodnoty:
    
    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte *myHTTPRule*. |
    | Protocol (Protokol) | Vyberte **tcp**. |
    | Port | Zadejte *80*.|
    | Back-endový port | Zadejte *80*. |
    | Back-endový fond | Vyberte *myBackendPool*.|
    | Sonda stavu | Vyberte *myHealthProbe*. |
4. Ponechte zbývající výchozí hodnoty a pak vyberte **OK**.


## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části vytvoříte virtuální síť, vytvoříte tři virtuální počítače pro back-endový fond nástroje pro vyrovnávání zatížení a pak nainstalujete službu IIS na virtuální počítače, abyste pomohli otestovat nástroj pro vyrovnávání zatížení.

## <a name="virtual-network-and-parameters"></a>Virtuální síť a parametry

V této části budete muset nahradit následující parametry v krocích s níže uvedenými informacemi:

| Parametr                   | Hodnota                |
|-----------------------------|----------------------|
| **\<>název skupiny prostředků**  | myResourceGroupSLB |
| **\<>názvů virtuálních sítí** | myVNet          |
| **\<>názvu oblasti**          | Západní Evropa      |
| **\<>adresního prostoru IPv4**   | 10.1.0.0\16          |
| **\<>názvu podsítě**          | myBackendSubnet        |
| **\<>rozsah emitované sítě** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů
Veřejné sloky sloky IP a sousady vyrovnávání zatížení musí odpovídat. Pro standardní vyrovnávání zatížení použijte virtuální chod se standardními IP adresami v back-endovém fondu. V této části vytvoříte tři virtuální hody *(myVM1*, *myVM2* a *myVM3)* se standardní veřejnou IP adresou ve třech různých zónách *(zóna 1*, *zóna 2*a *zóna 3),* které jsou později přidány do back-endového fondu nástrojů pro vyrovnávání zatížení, který byl vytvořen dříve. Pokud jste vybrali Základní, použijte virtuální chod se základními IP adresami.

1. Na levé horní straně portálu vyberte **Vytvořit prostředek** > **Compute** > **Windows Server 2019 Datacenter**. 
   
1. Do **pole Vytvořit virtuální počítač**zadejte nebo vyberte na kartě **Základy** následující hodnoty:
   - **Subscription** > **Skupina prostředků předplatného**: Vyberte **položku myResourceGroupSLB**.
   - **Podrobnosti instance** > **Název virtuálního počítače**: Zadejte *myVM1*.
   - **Oblast podrobností** > instance **>** vyberte možnost **Západní Evropa**.
   - **Možnosti** > **dostupnosti** podrobností instance > Vybrat **zóny dostupnosti**. 
   - **Zóna dostupnosti podrobností instance** > **Availability zone** > Vybrat **1**.
   - **Účet správce**> Zadejte **uživatelské jméno**, **heslo** **a potvrzení** hesla.
   - Vyberte kartu **Síť** nebo **další: Disky**, pak **Další: Síť**.
  
1. Na kartě **Síť** zkontrolujte, zda jsou vybrány následující položky:
   - **Virtuální síť**: *myVnet*
   - **Podsíť**: *myBackendSubnet*
   - **Veřejná adresa >** vyberte **Vytvořit nový**a v okně **Vytvořit veřejnou IP adresu** pro **skladovou položku**vyberte **standardní**a pro **zónu Dostupnost**, vyberte **redundantní zónu**a pak vyberte **OK**. Pokud jste vytvořili základní vytápěč zatížení, vyberte Základní. Společnost Microsoft doporučuje používat standardní skladovou položku pro produkční úlohy.
   - Chcete-li vytvořit novou skupinu zabezpečení sítě (NSG), typ brány firewall v části **Skupina zabezpečení sítě**vyberte **upřesnit**. 
       1. V poli **Konfigurovat skupinu zabezpečení sítě** vyberte vytvořit **nový**. 
       1. Zadejte *příkaz myNetworkSecurityGroup*a vyberte **ok**.
   - Chcete-li, aby se virtuální virtuální hotel zasoučástnění back-endového fondu vykladače zatížení, proveďte následující kroky:
        - V **nástroj Vyrovnávání zatížení**v části Umístění tohoto **Yes** **virtuálního počítače za existující řešení vyrovnávání zatížení?**
        - V **nastavení vyrovnávání zatížení**vyberte v části **Možnosti vyrovnávání zatížení** **vyberte nástroje Provyrovnávání zatížení Azure**.
        - V **části Vyberte vyvažovač zatížení**, *myLoadBalancer*.
        - Vyberte kartu **Správa** nebo možnost **Další** > **správa**.
2. Na kartě **Správa** nastavte v části **Sledování** **diagnostiku spouštění** na **Vypnuto**. 
1. Vyberte **Zkontrolovat a vytvořit**.   
1. Zkontrolujte nastavení a pak vyberte **Vytvořit**.
1. Podle kroků 2 až 6 vytvořte dva další virtuální počítače s následujícími hodnotami a všechna ostatní nastavení stejná jako *myVM1*:

    | Nastavení | VIRTUÁLNÍ POČÍTAČ 2| Virtuální ho dispozice 3|
    | ------- | ----- |---|
    | Name (Název) |  *myVM2* |*myVM3*|
    | Zóna dostupnosti | 2 |3|
    |Veřejná IP adresa| **Standardní** Sku|**Standardní** Sku|
    | Veřejná IP adresa – zóna dostupnosti| **Zóna redundantní** |**Zóna redundantní**|
    | Skupina zabezpečení sítě | Vyberte existující *skupinu myNetworkSecurity*| Vyberte existující *skupinu myNetworkSecurity*|

 ### <a name="create-nsg-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

V této části vytvoříte pravidlo skupiny zabezpečení sítě, které povolí příchozí připojení pomocí protokolu HTTP.

1. Vyberte **Všechny služby** v levé nabídce, vyberte **Všechny prostředky**a potom ze seznamu prostředků vyberte **myNetworkSecurityGroup,** která se nachází ve skupině prostředků **myResourceGroupSLB.**
2. V části **Nastavení** vyberte **Příchozí pravidla zabezpečení** a potom vyberte **Přidat**.
3. Zadáním následujících hodnot pro příchozí pravidlo zabezpečení *myHTTPRule* povolte příchozí připojení HTTP na portu 80:
    - **Zdroj**: *Výrobní číslo*
    -  **Zdrojová servisní značka**: *Internet*
    - **Rozsahy cílových přístavů**: *80*
    - **Protokol**: *TCP*
    - **Akce**: *Povolit*
    - **Priorita**: *100*
    - **Název**: *myHTTPRule* 
    - **Popis**: "*Povolit protokol HTTP* 
4. Vyberte **Přidat**.
5. V případě potřeby opakujte kroky pro příchozí pravidlo RDP s následujícími různými hodnotami:
   - **Rozsahy cílových portů**: Typ *3389*.
   - **Priorita**: typ *200*. 
   - **Název**: Zadejte *myRDPrule*. 
   - **Popis**: Zadejte *povolit prv*. 
 
### <a name="install-iis"></a>Instalace služby IIS

1. Vyberte **Všechny služby** v levé nabídce, vyberte **Všechny prostředky**a potom ze seznamu zdrojů vyberte **myVM1,** který se nachází ve skupině prostředků *myResourceGroupSLB.*
2. Na stránce **Přehled** vyberte **Připojit** a připojte se přes RDP k virtuálnímu počítači.
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
1. Na obrazovce **Přehled** vyhledejte veřejnou IP adresu Load Balanceru. V yberte **Všechny služby** v levé nabídce, vyberte **Všechny prostředky**a pak vyberte **myPublicIP**.

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

   ![Webový server služby IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Chcete-li zobrazit balancer distribuovat provoz napříč všemi třemi virtuálními počítači, můžete přizpůsobit výchozí stránku každého virtuálního počítače iis webového serveru a pak vynuceně aktualizovat webový prohlížeč z klientského počítače.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, odstraňte skupinu prostředků, vyrovnávání zatížení a všechny související prostředky. Chcete-li tak učinit, vyberte skupinu prostředků *(myResourceGroupSLB),* která obsahuje vyrovnávání zatížení, a pak vyberte **odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili standardní vytápěč zatížení, připojili k němu virtuální počítače, nakonfigurovali pravidlo provozu nástrojů vyrovnávání zatížení, sondu stavu a poté otestovali systém vyrovnávání zatížení. Další informace o Azure Load Balancer, pokračujte v [azure balancer kurzy](tutorial-load-balancer-standard-public-zone-redundant-portal.md).

Další informace o [nástrojůch pro vyrovnávání zatížení a zónách dostupnosti](load-balancer-standard-availability-zones.md).
