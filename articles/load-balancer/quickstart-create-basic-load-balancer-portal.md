---
title: 'Rychlý start: Vytvoření veřejné Load Balancer Basic pomocí Azure Portal'
titlesuffix: Azure Load Balancer
description: Tento rychlý start ukazuje, jak vytvořit veřejné Load Balancer úrovně Basic pomocí webu Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 9819111c8264493648233f40252db4fb4410aaf1
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274084"
---
# <a name="quickstart-create-a-basic-load-balancer-by-using-the-azure-portal"></a>Rychlý start: Vytvoření základní Load Balancer pomocí Azure Portal

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky ve virtuálních počítačích (VM). Na webu Azure portal můžete použít k vytvoření nástroje pro vyrovnávání zatížení a vyrovnávat přenosy mezi virtuálními počítači. V tomto rychlém startu se dozvíte, jak vytvořit a nakonfigurovat nástroj pro vyrovnávání zatížení, back-end serverů a síťových prostředků v cenové úrovni Basic.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

Provádění úkolů v rámci tohoto rychlého startu, přihlaste se k [webu Azure portal](https://portal.azure.com).

## <a name="create-a-basic-load-balancer"></a>Vytvoření Load Balanceru úrovně Basic

Nejprve pomocí portálu Vytvořte veřejnou základní Load Balancer. Název a veřejnou IP adresu, kterou jste vytvořili, se automaticky nakonfiguruje jako front-endu nástroje pro vyrovnávání zatížení.

1. V levém horním rohu obrazovky klikněte na **Vytvořit prostředek** > **Sítě** > **Load Balancer**.
2. Na kartě **základy** na stránce vytvořit službu Vyrovnávání **zatížení** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**:

    | Nastavení                 | Value                                              |
    | ---                     | ---                                                |
    | Subscription               | Vyberte své předplatné.    |    
    | Resource group         | Vyberte **vytvořit nový** a do textového pole zadejte *MyResourceGroupLB* .|
    | Name                   | *myLoadBalancer*                                   |
    | Oblast         | Vyberte **Západní Evropa**.                                        |
    | type          | Vyberte možnost **veřejné**.                                        |
    | SKU           | Vyberte **Basic**.                          |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. |
    | Název veřejné IP adresy              | *MyPublicIP*   |
    | Přiřazení| Static|

3. Na kartě **Revize + vytvořit** klikněte na **vytvořit**.   


## <a name="create-back-end-servers"></a>Vytvoření back-end serverů

Dále vytvořte virtuální síť a dva virtuální počítače pro back endový fond vašeho load balanceru úrovně Basic. 

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Na straně levého horního rohu portálu, vyberte **vytvořit prostředek** > **sítě** > **virtuální síť**.
   
1. V **vytvořit virtuální síť** podokně, zadejte nebo vyberte tyto hodnoty:
   
   - **Název**: Zadejte *MyVnet*.
   - **ResourceGroup**: Rozevírací seznam **Vyberte existující** a pak vyberte **MyResourceGroupLB**. 
   -  > **Název**podsítě: Zadejte *MyBackendSubnet*.
   
1. Vyberte **Vytvořit**.

   ![Vytvoření virtuální sítě](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

1. Na straně levého horního rohu portálu, vyberte **vytvořit prostředek** > **Compute** > **systému Windows Server 2016 Datacenter**. 
   
1. V **vytvoření virtuálního počítače**, zadejte nebo vyberte následující hodnoty **Základy** kartu:
   -  > **Skupina prostředků**předplatného: Rozevírací seznam a vyberte **MyResourceGroupLB**.
   - Podrobnosti > instance**název virtuálního počítače**: Zadejte *MyVM1*.
   - **Podrobnosti o instanci** > **možností dostupnosti**: 
     1. Rozevírací seznam a vyberte **dostupnosti**. 
     2. Vyberte **vytvořit nový**, typ *MyAvailabilitySet*a vyberte **OK**.
  
1. Vyberte kartu **síť** nebo vyberte **další: Disky**a potom **další: Sítě**. 
   
   Ujistěte se, že jsou vybrány následující:
   - **Virtuální síť**: **MyVnet**
   - **Podsíť**: **MyBackendSubnet**
   - **Veřejná IP adresa**: **MyVM1-ip**
   
   Chcete-li vytvořit novou skupinu zabezpečení sítě (NSG), typ brány firewall, v části **skupinu zabezpečení sítě**vyberte **Upřesnit**. 
   1. V **konfigurovat skupinu zabezpečení sítě** pole, vyberte **vytvořit nový**. 
   1. Typ *MyNetworkSecurityGroup*a vyberte **OK**. 
   
1. Vyberte **správu** kartě nebo vyberte **Další** > **správu**. V části **monitorování**, nastavte **Diagnostika spouštění** k **vypnout**.
   
1. Vyberte **Zkontrolovat a vytvořit**.
   
1. Zkontrolujte nastavení a pak vyberte **vytvořit**. 

1. Podle pokynů vytvořte druhý virtuální počítač s názvem *MyVM2*, s **veřejnou IP adresu** adresu *MyVM2 ip*a všechna ostatní nastavení stejný jako MyVM1. 

### <a name="create-nsg-rules-for-the-vms"></a>Vytvoření pravidla skupiny zabezpečení sítě pro virtuální počítače

V této části vytvoříte zabezpečení sítě (pravidla skupiny NSG) pro virtuální počítače povolit příchozí internet (HTTP) a připojení ke vzdálené ploše (RDP).

1. V nabídce vlevo vyberte **Všechny prostředky**. V seznamu prostředků vyberte **MyNetworkSecurityGroup** v **MyResourceGroupLB** skupinu prostředků.
   
1. V části **Nastavení** vyberte **Příchozí pravidla zabezpečení** a potom vyberte **Přidat**.
   
1. V **přidat příchozí pravidlo zabezpečení** dialogové okno pro pravidlo protokolu HTTP, zadejte nebo vyberte následující:
   
   - **Zdroj**: Vyberte **značku služby**.  
   - **Značka zdrojové služby**: Vyberte možnost **Internet**. 
   - **Rozsahy cílových portů**: Zadejte *80*.
   - **Protokol**: Vyberte **TCP**. 
   - **Akce**: Vyberte možnost **udělit**.  
   - **Priorita**: Zadejte *100*. 
   - **Název**: Zadejte *MyHTTPRule*. 
   - **Popis**: Zadejte možnost *Allow http*. 
   
1. Vyberte **Přidat**. 
   
   ![Vytvoření pravidla skupiny zabezpečení sítě](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
   
1. Opakujte kroky pro příchozí pravidlo protokolu RDP s použitím následujících odlišných hodnot:
   - **Rozsahy cílových portů**: Zadejte *3389*.
   - **Priorita**: Zadejte *200*. 
   - **Název**: Zadejte *MyRDPRule*. 
   - **Popis**: Zadejte *Allow RDP*. 

## <a name="create-resources-for-the-load-balancer"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení

V této části nakonfigurujete nastavení nástroje pro vyrovnávání zatížení back endový fond adres, sondu stavu a pravidlo služby load balancer.

### <a name="create-a-backend-address-pool"></a>Vytvoření fondu back-endových adres

Účelem distribuce provozu do virtuálních počítačů, nástroje pro vyrovnávání zatížení používá fond back endových adres. Fond back endových adres obsahuje IP adresy virtuálních síťových rozhraní (NIC), které jsou připojené k nástroji pro vyrovnávání zatížení. 

**Vytvoření fondu back endových adres, který obsahuje VM1 a VM2:**

1. Vyberte **všechny prostředky** v nabídce vlevo a pak vyberte **MyLoadBalancer** ze seznamu prostředků.
   
1. V **Nastavení** vyberte **Back-endové fondy** a potom vyberte **Přidat**.
   
1. Na **přidat back-endový fond** stránky zadejte nebo vyberte následující hodnoty:
   
   - **Název**: Zadejte *MyBackEndPool*.
   - **Přidruženo k**: Rozevírací seznam a vyberte **dostupnosti**.
   - **Skupina dostupnosti**: Vyberte **MyAvailabilitySet**.
   
1. Vyberte **přidat cílovou konfiguraci protokolu IP sítě**. 
   1. Přidejte všechny virtuální počítače (**MyVM1** a **MyVM2**), který jste vytvořili pro back endového fondu.
   2. Jakmile přidáte každý počítač, rozevírací seznam a vyberte jeho **konfigurace protokolu IP sítě**. 
   
1. Vyberte **OK**.
   
   ![Přidání fondu adres back-endu](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)
   
1. Na **back-endové fondy** stránce, rozbalte **MyBackendPool** a ujistěte se, že **VM1** a **VM2** jsou uvedeny.

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Pokud chcete povolit monitorování stavu virtuálních počítačů nástroje pro vyrovnávání zatížení, použijte sondu stavu. Sonda stavu dynamicky přidává virtuální počítače do oběhu nástroje pro vyrovnávání zatížení nebo je z něj odebírá na základě jejich reakce na kontroly stavu. 

**K vytvoření sondy stavu pro monitorování stavu virtuálních počítačů:**

1. Vyberte **všechny prostředky** v nabídce vlevo a pak vyberte **MyLoadBalancer** ze seznamu prostředků.
   
1. V **Nastavení** vyberte **Sondy stavu** a potom vyberte **Přidat**.
   
1. Na **přidat sondu stavu** stránky zadejte nebo vyberte následující hodnoty:
   
   - **Název**: Zadejte *MyHealthProbe*.
   - **Protokol**: Rozevírací seznam a vyberte **http**. 
   - **Port**: Zadejte *80*. 
   - **Cesta**: Přijměte */* výchozí identifikátor URI. Tuto hodnotu můžete nahradit jiný identifikátor URI. 
   - **Interval**: Zadejte *15*. Interval je počet sekund mezi pokusy o testování.
   - **Prahová hodnota chybného stavu**: Zadejte *2*. Tato hodnota je počet chyb po sobě jdoucích sondování, ke kterým dojde před virtuální počítač považoval za poškozený.
   
1. Vyberte **OK**.
   
   ![Přidat test](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje, jak se provoz distribuuje do virtuálních počítačů. Pravidlo definuje konfiguraci front-end IP adresy pro příchozí provoz, back endového fondu IP pro příjem provozu a požadované zdrojových a cílových portů. 

Pravidlo nástroje pro vyrovnávání zatížení s názvem **MyLoadBalancerRule** naslouchá na portu 80 ve front-endu **LoadBalancerFrontEnd**. Toto pravidlo automaticky odesílá síťový provoz do fondu back endových adres **MyBackEndPool**, rovněž na portu 80. 

**Vytvořte pravidlo nástroje pro vyrovnávání zatížení:**


1. Vyberte **všechny prostředky** v nabídce vlevo a pak vyberte **MyLoadBalancer** ze seznamu prostředků.
   
1. V části **Nastavení** vyberte **Pravidla vyrovnávání zatížení** a potom vyberte **Přidat**.
   
1. Na **přidat pravidlo Vyrovnávání zatížení** stránky zadejte nebo vyberte následující hodnoty:
   
   - **Název**: Zadejte *MyLoadBalancerRule*.
   - **IP adresa front-endu:** Zadejte *LoadBalancerFrontend*.
   - **Protokol**: Vyberte **TCP**.
   - **Port**: Zadejte *80*.
   - **Back-end port**: Zadejte *80*.
   - **Back-end fond**: Vyberte **MyBackendPool**.
   - **Sonda stavu**: Vyberte **MyHealthProbe**. 
   
1. Vyberte **OK**.
   
   ![Přidat pravidlo služby load balancer](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

Veřejnou IP adresu použijete k testování nástroje pro vyrovnávání zatížení na virtuálních počítačích. 

Na portálu na **přehled** stránce **MyLoadBalancer**, vyhledejte svou veřejnou IP adresu v rámci **veřejnou IP adresu**. Podržte ukazatel myši nad adres a vyberte **kopírování** ikonu zkopírujte. 

### <a name="install-iis-on-the-vms"></a>Instalace služby IIS na virtuálních počítačích

Instalace Internetové informační služby (IIS) u virtuálních počítačů, která vám pomůže nástroj pro vyrovnávání zatížení otestovat.

**Pro vzdálené plochy (RDP) k virtuálnímu počítači:**

1. Na portálu vyberte **všechny prostředky** v nabídce vlevo. V seznamu prostředků vyberte **MyVM1** v **MyResourceGroupLB** skupinu prostředků.
   
1. Na **přehled** stránce **připojit**a pak vyberte **soubor stáhnout RDP**. 
   
1. Otevřít protokol RDP jste stáhli a vyberte možnost **připojit**.
   
1. V dialogovém okně zabezpečení Windows vyberte **víc možností** a potom **použít jiný účet**. 
   
   Zadejte uživatelské jméno a heslo a vyberte **OK**.
   
1. Reakce **Ano** do libovolného řádku pro certifikát. 
   
   V novém okně se otevře na plochu virtuálního počítače. 
   
**Instalace služby IIS**

1. V nabídce vlevo vyberte **všechny služby** , vyberte **všechny prostředky**a potom v seznamu prostředky vyberte **myVM1** , která je umístěná ve skupině prostředků *myResourceGroupSLB* .
2. Na stránce **Přehled** vyberte **Připojit** a připojte se přes RDP k virtuálnímu počítači.
5. Přihlaste se k virtuálnímu počítači pomocí přihlašovacích údajů, které jste zadali při vytváření tohoto virtuálního počítače. Tím se otevře relace vzdálené plochy s virtuálním počítačem *myVM1*.
6. Na ploše serveru přejděte do části **Nástroje pro správu Windows**>**Windows PowerShell**.
7. V okně PowerShellu spuštěním následujících příkazů nainstalujte server služby IIS, odeberte výchozí soubor iisstart.htm a pak přidejte nový soubor iisstart.htm, který zobrazuje název virtuálního počítače:

   ```azurepowershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
    remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Ukončete relaci RDP s *myVM1*.
7. Opakováním kroků 1 až 6 nainstalujte službu IIS a aktualizovaný soubor iisstart.htm na *myVM2*.
   
1. Opakujte kroky pro virtuální počítač **MyVM2**, s výjimkou nastavte na cílovém serveru **MyVM2**.

### <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

Otevřete prohlížeč a vyrovnávání zatížení veřejnou IP adresu vložte do panelu Adresa prohlížeče. V prohlížeči by se zobrazit výchozí stránka serveru webové služby IIS.

![Webový server služby IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Pokud chcete zobrazit distribuci provozu nástrojem pro vyrovnávání zatížení mezi oba virtuální počítače, na kterých je vaše aplikace spuštěná, můžete vynutit aktualizaci webového prohlížeče.
## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odstranit nástroj pro vyrovnávání zatížení a všech souvisejících prostředků, když je už nepotřebujete, otevřete **MyResourceGroupLB** prostředku, skupiny a vyberte **odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili nástroj pro vyrovnávání zatížení úrovně Basic. Vytvoříte a nakonfigurujete skupinu prostředků, síťové prostředky, back-end serverů, sondu stavu a pravidla pomocí nástroje pro vyrovnávání zatížení. Službu IIS nainstalovali na virtuálních počítačích a použít ho k otestování nástroje pro vyrovnávání zatížení. 

Další informace o nástroji Azure Load Balancer, nadále v kurzech.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)
