---
title: 'Kurz: Vytvoření interního nástroje – Azure portal'
titlesuffix: Azure Load Balancer
description: V tomto kurzu se dozvíte, jak vytvořit interní load balanceru úrovně Basic pomocí webu Azure portal.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internal traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: 16c9eea61391511f7515308131b3541e186cd7ae
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54232613"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-basic-load-balancer-in-the-azure-portal"></a>Kurz: Vyrovnávání zatížení interního provozu load balanceru úrovně Basic na webu Azure Portal

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky ve virtuálních počítačích (VM). Na webu Azure portal můžete použít k vytvoření load balanceru úrovně Basic a vyvážit interního provozu mezi virtuálními počítači. V tomto kurzu se dozvíte, jak vytvořit a nakonfigurovat interního nástroje load balancer, back-end serverů a síťových prostředků v cenové úrovni Basic.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

Pokud dáváte přednost, můžete provést tento postup pomocí [rozhraní příkazového řádku Azure](load-balancer-get-started-ilb-arm-cli.md) nebo [prostředí Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) místo na portálu.

Postup použití v tomto kurzu, přihlaste se k webu Azure portal na [ https://portal.azure.com ](https://portal.azure.com).

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>Vytvoření virtuální sítě, back-end serverů a testovacího virtuálního počítače

Nejprve vytvořte virtuální síť (VNet). Ve virtuální síti vytvořte dva virtuální počítače používat pro back endový fond vašeho load balanceru úrovně Basic, a třetí virtuální počítač má použít pro testování nástroje pro vyrovnávání zatížení. 

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Na straně levého horního rohu portálu, vyberte **vytvořit prostředek** > **sítě** > **virtuální síť**.
   
1. V **vytvořit virtuální síť** podokně, zadejte nebo vyberte tyto hodnoty:
   
   - **Název**: Typ *MyVNet*.
   - **Skupina prostředků**: Vyberte **vytvořit nový**, zadejte *MyResourceGroupLB*a vyberte **OK**. 
   - **Podsíť** > **název**: Typ *MyBackendSubnet*.
   
1. Vyberte **Vytvořit**.

   ![Vytvoření virtuální sítě](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

1. Na straně levého horního rohu portálu, vyberte **vytvořit prostředek** > **Compute** > **systému Windows Server 2016 Datacenter**. 
   
1. V **vytvoření virtuálního počítače**, zadejte nebo vyberte následující hodnoty **Základy** kartu:
   - **Předplatné** > **skupiny prostředků**: Rozevírací seznam a vyberte **MyResourceGroupLB**.
   - **Podrobnosti o instanci** > **název virtuálního počítače**: Typ *MyVM1*.
   - **Podrobnosti o instanci** > **možností dostupnosti**: 
     1. Rozevírací seznam a vyberte **dostupnosti**. 
     2. Vyberte **vytvořit nový**, typ *MyAvailabilitySet*a vyberte **OK**.
   
1. Vyberte **sítě** kartě nebo vyberte **Další: Disky**, pak **Další: Sítě**. 
   
   Ujistěte se, že jsou vybrány následující:
   - **Virtuální síť**: **MyVNet**
   - **Podsíť**: **MyBackendSubnet**
   
   V části **skupinu zabezpečení sítě**:
   1. Vyberte **Upřesnit**. 
   1. Rozevírací nabídka **konfigurovat skupinu zabezpečení sítě** a vyberte **žádný**. 
   
1. Vyberte **správu** kartě nebo vyberte **Další** > **správu**. V části **monitorování**, nastavte **Diagnostika spouštění** k **vypnout**.
   
1. Vyberte **Zkontrolovat a vytvořit**.
   
1. Zkontrolujte nastavení a pak vyberte **vytvořit**. 

1. Podle pokynů vytvořte druhý virtuální počítač s názvem *MyVM2*, se všemi nastaveními stejný jako MyVM1. 

1. Podle pokynů znovu vytvořte třetí virtuální počítač s názvem *MyTestVM*. 

## <a name="create-a-basic-load-balancer"></a>Vytvoření Load Balanceru úrovně Basic

Vytvoření interní load balanceru úrovně Basic prostřednictvím portálu. Název a IP adresu, kterou vytvoříte, se automaticky nakonfiguruje jako front-endu nástroje pro vyrovnávání zatížení.

1. V levém horním rohu webu Azure Portal klikněte na **Vytvořit prostředek** > **Sítě** > **Nástroj pro vyrovnávání zatížení**.
   
1. V **vytvořit nástroj pro vyrovnávání zatížení** podokně, zadejte nebo vyberte tyto hodnoty:
   
   - **Název**: Typ *MyLoadBalancer*.
   - **Typ**: Vyberte **interní**. 
   - **SKLADOVÁ POLOŽKA**: Vyberte **Basic**.
   - **Virtuální síť**: Vyberte **zvolit virtuální síť**a pak vyberte **MyVNet**.
   - **Podsíť**: Vyberte **zvolit podsíť**a pak vyberte **MyBackendSubnet**.
   - **Přiřazení IP adresy**: Vyberte **statické** Pokud není vybrán.
   - **Privátní IP adresa**: Zadejte adresu, která je v adresním prostoru virtuální sítě a podsítě, například *10.3.0.7*.
   - **Skupina prostředků**: Rozevírací nabídka **vybrat existující** a vyberte **MyResourceGroupLB**. 
   
1. Vyberte **Vytvořit**.
   
![Vytvoření nástroje pro vyrovnávání zatížení](./media/tutorial-load-balancer-basic-internal-portal/1-load-balancer.png)

## <a name="create-basic-load-balancer-resources"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení základní

V této části nakonfigurujete nastavení nástroje pro vyrovnávání zatížení pro fond back endových adres a sondu stavu a určit pravidla nástroje pro vyrovnávání zatížení.

### <a name="create-a-back-end-address-pool"></a>Vytvoření fondu back-endových adres

Účelem distribuce provozu do virtuálních počítačů, nástroje pro vyrovnávání zatížení používá fond back endových adres. Fond back endových adres obsahuje IP adresy virtuálních síťových rozhraní (NIC), které jsou připojené k nástroji pro vyrovnávání zatížení. 

**Vytvoření fondu back endových adres, který obsahuje VM1 a VM2:**

1. Vyberte **všechny prostředky** v nabídce vlevo a pak vyberte **MyLoadBalancer** ze seznamu prostředků.
   
1. V **Nastavení** vyberte **Back-endové fondy** a potom vyberte **Přidat**.
   
1. Na **přidat back-endový fond** stránky zadejte nebo vyberte následující hodnoty:
   
   - **Název**: Typ *MyBackendPool*.
   - **Přidružené k**: Rozevírací seznam a vyberte **dostupnosti**.
   - **Skupina dostupnosti**: Vyberte **MyAvailabilitySet**.
   
1. Vyberte **přidat cílovou konfiguraci protokolu IP sítě**. 
   1. Přidat **MyVM1** a **MyVM2** do back endového fondu.
   2. Jakmile přidáte každý počítač, rozevírací seznam a vyberte jeho **konfigurace protokolu IP sítě**. 
   
   >[!NOTE]
   >Nepřidávejte **MyTestVM** do fondu. 
   
1. Vyberte **OK**.
   
   ![Přidejte fond back endových adres](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)
   
1. Na **back-endové fondy** stránce, rozbalte **MyBackendPool** a ujistěte se, že **VM1** a **VM2** jsou uvedeny.

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Pokud chcete povolit monitorování stavu virtuálních počítačů nástroje pro vyrovnávání zatížení, použijte sondu stavu. Sonda stavu dynamicky přidává virtuální počítače do oběhu nástroje pro vyrovnávání zatížení nebo je z něj odebírá na základě jejich reakce na kontroly stavu. 

**K vytvoření sondy stavu pro monitorování stavu virtuálních počítačů:**

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
   
   ![Přidat test](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje, jak se provoz distribuuje do virtuálních počítačů. Pravidlo definuje konfiguraci front-end IP adresy pro příchozí provoz, back endového fondu IP pro příjem provozu a požadované zdrojových a cílových portů. 

Pravidlo nástroje pro vyrovnávání zatížení s názvem **MyLoadBalancerRule** naslouchá na portu 80 ve front-endu **LoadBalancerFrontEnd**. Toto pravidlo automaticky odesílá síťový provoz do fondu back endových adres **MyBackendPool**, rovněž na portu 80. 

**Vytvořte pravidlo nástroje pro vyrovnávání zatížení:**

1. Vyberte **všechny prostředky** v nabídce vlevo a pak vyberte **MyLoadBalancer** ze seznamu prostředků.
   
1. V části **Nastavení** vyberte **Pravidla vyrovnávání zatížení** a potom vyberte **Přidat**.
   
1. Na **přidat pravidlo Vyrovnávání zatížení** stránky, zadejte nebo vyberte následující hodnoty, pokud není již k dispozici:
   
   - **Název**: Typ *MyLoadBalancerRule*.
   - **Front-endová IP adresa:** Typ *LoadBalancerFrontEnd* Pokud není k dispozici.
   - **Protokol**: Vyberte **TCP**.
   - **Port**: Typ *80*.
   - **Back-endový port**: Typ *80*.
   - **Back-endový fond**: Vyberte **MyBackendPool**.
   - **Sonda stavu**: Vyberte **MyHealthProbe**. 
   
1. Vyberte **OK**.
   
  ![Přidat pravidlo služby load balancer](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

Instalace Internetové informační služby (IIS) na back-end serverů a pak použít MyTestVM k otestování nástroje pro vyrovnávání zatížení pomocí jeho privátní IP adresy. Každý back endového virtuálního počítače slouží jinou verzi výchozí webovou stránku IIS, abyste si mohli zobrazit distribuci požadavků mezi dvěma virtuálními počítači nástroje pro vyrovnávání zatížení.

Na portálu na **přehled** stránce **MyLoadBalancer**, vyhledejte jeho IP adresu v rámci **privátní IP adresa**. Podržte ukazatel myši nad adres a vyberte **kopírování** ikonu zkopírujte. V tomto příkladu je **10.3.0.7**. 

### <a name="connect-to-the-vms-with-rdp"></a>Připojení k virtuálním počítačům přes RDP

Připojte se nejprve pro všechny tři virtuální počítače pomocí vzdálené plochy (RDP). 

>[!NOTE]
>Ve výchozím nastavení, budou již máte virtuální počítače **RDP** otevřete port (vzdálené plochy) povolit přístup přes vzdálenou plochu. 

**Pro vzdálené plochy (RDP) k virtuálním počítačům:**

1. Na portálu vyberte **všechny prostředky** v nabídce vlevo. V seznamu prostředků vyberte každý virtuální počítač **MyResourceGroupLB** skupinu prostředků.
   
1. Na **přehled** stránce **připojit**a pak vyberte **soubor stáhnout RDP**. 
   
1. Otevřít protokol RDP jste stáhli a vyberte možnost **připojit**.
   
1. V dialogovém okně zabezpečení Windows vyberte **víc možností** a potom **použít jiný účet**. 
   
   Zadejte uživatelské jméno a heslo a pak vyberte **OK**.
   
1. Reakce **Ano** do libovolného řádku pro certifikát. 
   
   V novém okně se otevře na plochu virtuálního počítače. 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>Instalace služby IIS a nahradí výchozí stránku služby IIS na virtuálních počítačích back-end

Na každém serveru back-end pomocí prostředí PowerShell k instalaci IIS a výchozí webovou stránku IIS nahraďte upravené stránky.

>[!NOTE]
>Můžete také použít **Průvodce přidání rolí a funkcí** v **správce serveru** instalace služby IIS. 

**K instalaci IIS a výchozí webové stránky aktualizovat pomocí prostředí PowerShell:**

1. Spustit na MyVM1 a na MyVM2 **prostředí Windows PowerShell** z **Start** nabídky. 

2. Spusťte následující příkazy k instalaci IIS a nahradit výchozí webovou stránku IIS:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. Ukončete připojení RDP s MyVM1 a MyVM2 tak, že vyberete **odpojit**. Nevypínejte virtuálních počítačů.

### <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

1. Na MyTestVM, otevřete **aplikace Internet Explorer**a Odpovědět **OK** všechny konfigurace zobrazí výzvu. 
   
1. Vložte nebo zadejte privátní IP adresu nástroje pro vyrovnávání zatížení (*10.3.0.7*) do adresního řádku prohlížeče. 
   
   Přizpůsobené serveru výchozí stránku webové služby IIS se zobrazí v prohlížeči. Přečte zprávu buď **Hello World z MyVM1**, nebo **Hello World z MyVM2**.
   
1. Aktualizujte prohlížeč, pokud chcete zobrazit distribuci provozu mezi virtuální počítače, nástroj pro vyrovnávání zatížení. Také budete muset vymazat mezipaměť prohlížeče mezi pokusy.

   Někdy **MyVM1** se zobrazí stránka a jindy **MyVM2** stránky se zobrazí, jak nástroj pro vyrovnávání zatížení distribuuje požadavky na každý back endového virtuálního počítače. 

   ![Nové výchozí stránka služby IIS](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odstranit nástroj pro vyrovnávání zatížení a všech souvisejících prostředků, když je už nepotřebujete, otevřete **MyResourceGroupLB** prostředku, skupiny a vyberte **odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili úroveň Basic interního nástroje load balancer. Vytvoření a konfiguraci síťových prostředků, back-end serverů, sondu stavu a pravidel nástroje pro vyrovnávání zatížení. Službu IIS nainstalovali na virtuálních počítačích back-end a použít testovací virtuální počítač k otestování nástroje pro vyrovnávání zatížení v prohlížeči. 

Dále se naučíte vyrovnávat zatížení virtuálních počítačů napříč zónami dostupnosti.

> [!div class="nextstepaction"]
> [Vyrovnávání zatížení virtuálních počítačů napříč zónami dostupnosti](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
