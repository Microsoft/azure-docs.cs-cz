---
title: 'Kurz: vytvoření interního nástroje pro vyrovnávání zatížení – Azure Portal'
titleSuffix: Azure Load Balancer
description: V tomto kurzu se dozvíte, jak vytvořit interní load balanceru úrovně Basic pomocí webu Azure portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internal traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 6f62771d707d1aebccbfaf809dee7d0dedf5fefa
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096130"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-basic-load-balancer-in-the-azure-portal"></a>Kurz: Zůstatek interní zatížení s load balanceru úrovně Basic na webu Azure Portal

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky ve virtuálních počítačích (VM). Na webu Azure portal můžete použít k vytvoření load balanceru úrovně Basic a vyvážit interního provozu mezi virtuálními počítači. V tomto kurzu se dozvíte, jak vytvořit a nakonfigurovat interního nástroje load balancer, back-end serverů a síťových prostředků v cenové úrovni Basic.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

Pokud chcete, můžete tyto kroky provést pomocí [Azure CLI](load-balancer-get-started-ilb-arm-cli.md) nebo [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) místo na portálu.

Pokud chcete postupovat podle tohoto kurzu, přihlaste se k Azure Portal v [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>Vytvoření virtuální sítě, back-end serverů a testovacího virtuálního počítače

Nejprve vytvořte virtuální síť (VNet). Ve virtuální síti vytvořte dva virtuální počítače používat pro back endový fond vašeho load balanceru úrovně Basic, a třetí virtuální počítač má použít pro testování nástroje pro vyrovnávání zatížení. 

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. V levé horní části portálu vyberte **vytvořit prostředek** > **síť** > **virtuální síť**.
   
1. V podokně **vytvořit virtuální síť** zadejte nebo vyberte tyto hodnoty:
   
   - **Název**: zadejte *MyVNet*.
   - **Zdroj dat**: vyberte **vytvořit novou**a potom zadejte *MyResourceGroupLB*a vyberte **OK**. 
   - **Název** > **podsítě** : zadejte *MyBackendSubnet*.
   
1. Vyberte **Vytvořit**.

   ![Vytvoření virtuální sítě](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

1. V levé horní části portálu vyberte **vytvořit prostředek** > **COMPUTE** > **Windows Server 2016 Datacenter**. 
   
1. V části **vytvořit virtuální počítač**zadejte nebo vyberte následující hodnoty na kartě **základy** :
   - **Předplatné** > **skupinu prostředků**: vyřaďte rozevírací seznam a vyberte **MyResourceGroupLB**.
   - **Podrobnosti Instance** > **název virtuálního počítače**: zadejte *MyVM1*.
   - **Podrobnosti Instance** > **Možnosti dostupnosti**: 
     1. Rozevírací seznam a vyberte **skupinu dostupnosti**. 
     2. Vyberte **vytvořit nový**, zadejte *MyAvailabilitySet*a vyberte **OK**.
   
1. Vyberte kartu **síť** nebo vyberte **Další: disky**a **Další: síť**. 
   
   Ujistěte se, že jsou vybrány následující:
   - **Virtuální síť**: **MyVNet**
   - **Podsíť**: **MyBackendSubnet**
   
   V části **Skupina zabezpečení sítě**:
   1. Vyberte **Upřesnit**. 
   1. Rozbalte položku **Konfigurovat skupinu zabezpečení sítě** a vyberte možnost **žádná**. 
   
1. Vyberte kartu **Správa** nebo vyberte možnost **Další** > **Správa**. V části **monitorování**nastavte **diagnostiku spouštění** na **vypnuto**.
   
1. Vyberte **Zkontrolovat a vytvořit**.
   
1. Zkontrolujte nastavení a pak vyberte **vytvořit**. 

1. Podle pokynů vytvořte druhý virtuální počítač s názvem *MyVM2*a všechna ostatní nastavení se shodují s MyVM1. 

1. Pomocí kroků znovu vytvořte třetí virtuální počítač s názvem *MyTestVM*. 

## <a name="create-a-basic-load-balancer"></a>Vytvoření Load Balanceru úrovně Basic

Vytvoření interní load balanceru úrovně Basic prostřednictvím portálu. Název a IP adresu, kterou vytvoříte, se automaticky nakonfiguruje jako front-endu nástroje pro vyrovnávání zatížení.

1. V levém horním rohu webu Azure Portal klikněte na **Vytvořit prostředek** > **Sítě** > **Nástroj pro vyrovnávání zatížení**.
   
2. Na kartě **základy** na stránce **vytvořit službu Vyrovnávání zatížení** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **vytvořit nový** a do textového pole zadejte *MyResourceGroupLB* .|
    | Název                   | *myLoadBalancer*                                   |
    | Oblast         | Vyberte **USA – východ 2**.                                        |
    | Typ          | Vyberte **interní**.                                        |
    | Skladová jednotka (SKU)           | Vyberte **Basic**.                          |
    | Virtuální síť           | Vyberte *MyVNet*.                          |    
    | Přiřazení IP adresy              | Vyberte možnost **static**.   |
    | Privátní IP adresa|Zadejte adresu, která se nachází v adresním prostoru virtuální sítě a podsítě, například *10.3.0.7*.  |

3. Na kartě **Revize + vytvořit** klikněte na **vytvořit**. 
   

## <a name="create-basic-load-balancer-resources"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení základní

V této části nakonfigurujete nastavení nástroje pro vyrovnávání zatížení pro fond back-end adres a sondu stavu a zadáte pravidla nástroje pro vyrovnávání zatížení.

### <a name="create-a-back-end-address-pool"></a>Vytvoření fondu back-endových adres

Účelem distribuce provozu do virtuálních počítačů, nástroje pro vyrovnávání zatížení používá fond back endových adres. Fond back endových adres obsahuje IP adresy virtuálních síťových rozhraní (NIC), které jsou připojené k nástroji pro vyrovnávání zatížení. 

**Vytvoření fondu adres back-endu, který zahrnuje VM1 a VM2:**

1. V nabídce vlevo vyberte **všechny prostředky** a v seznamu prostředků vyberte **MyLoadBalancer** .
   
1. V **Nastavení** vyberte **Back-endové fondy** a potom vyberte **Přidat**.
   
1. Na stránce **Přidat back-end fond** zadejte nebo vyberte následující hodnoty:
   
   - **Název**: zadejte *MyBackendPool*.
   - **Přidruženo k**: rozevírací seznam a výběr **virtuálního počítače**.
   
   
1. Vyberte **virtuální počítač**. 
   1. Do fondu back-end přidejte **MyVM1** a **MyVM2** .
   2. Po přidání jednotlivých počítačů rozevírací seznam a výběr **Konfigurace sítě IP**. 
   
   >[!NOTE]
   >Do fondu nepřidávejte **MyTestVM** . 
   
1. Vyberte **OK**.
   
   ![Přidejte fond back endových adres](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)
   
1. Na stránce **back-end fondy** rozbalte **MyBackendPool** a ujistěte se, že jsou uvedené **VM1** i **VM2** .

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Pokud chcete povolit monitorování stavu virtuálních počítačů nástroje pro vyrovnávání zatížení, použijte sondu stavu. Sonda stavu dynamicky přidává virtuální počítače do oběhu nástroje pro vyrovnávání zatížení nebo je z něj odebírá na základě jejich reakce na kontroly stavu. 

**Vytvoření sondy stavu pro monitorování stavu virtuálních počítačů:**

1. V nabídce vlevo vyberte **všechny prostředky** a v seznamu prostředků vyberte **MyLoadBalancer** .
   
1. V **Nastavení** vyberte **Sondy stavu** a potom vyberte **Přidat**.
   
1. Na stránce **Přidat sondu stavu** zadejte nebo vyberte následující hodnoty:
   
   - **Název**: zadejte *MyHealthProbe*.
   - **Protokol**: rozevírací seznam a výběr **http**. 
   - **Port**: typ *80*. 
   - **Cesta**: přijmout */* pro výchozí identifikátor URI. Tuto hodnotu můžete nahradit jiný identifikátor URI. 
   - **Interval**: typ *15*. Interval je počet sekund mezi pokusy o testování.
   - **Prahová hodnota chybného stavu**: typ *2*. Tato hodnota je počet chyb po sobě jdoucích sondování, ke kterým dojde před virtuální počítač považoval za poškozený.
   
1. Vyberte **OK**.
   
   ![Přidat test](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje, jak se provoz distribuuje do virtuálních počítačů. Pravidlo definuje konfiguraci front-end IP adresy pro příchozí provoz, back endového fondu IP pro příjem provozu a požadované zdrojových a cílových portů. 

Pravidlo nástroje pro vyrovnávání zatížení s názvem **MyLoadBalancerRule** naslouchá na portu 80 ve front-endu **LoadBalancerFrontEnd**. Pravidlo odesílá síťový provoz do fondu back-end adres **MyBackendPool**, a to i na portu 80. 

**Vytvoření pravidla nástroje pro vyrovnávání zatížení:**

1. V nabídce vlevo vyberte **všechny prostředky** a v seznamu prostředků vyberte **MyLoadBalancer** .
   
1. V části **Nastavení** vyberte **Pravidla vyrovnávání zatížení** a potom vyberte **Přidat**.
   
1. Na stránce **Přidat pravidlo vyrovnávání zatížení** zadejte nebo vyberte následující hodnoty (Pokud ještě nejsou přítomny):
   
   - **Název**: zadejte *MyLoadBalancerRule*.
   - **IP adresa front-endu:** Zadejte *LoadBalancerFrontEnd* , pokud není k dispozici.
   - **Protokol**: vyberte **TCP**.
   - **Port**: typ *80*.
   - **Back-end port**: typ *80*.
   - **Back-end fond**: vyberte **MyBackendPool**.
   - **Sonda stavu**: vyberte **MyHealthProbe**. 
   
1. Vyberte **OK**.
   
   ![Přidat pravidlo služby load balancer](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

Instalace Internetové informační služby (IIS) na back-end serverů a pak použít MyTestVM k otestování nástroje pro vyrovnávání zatížení pomocí jeho privátní IP adresy. Každý back endového virtuálního počítače slouží jinou verzi výchozí webovou stránku IIS, abyste si mohli zobrazit distribuci požadavků mezi dvěma virtuálními počítači nástroje pro vyrovnávání zatížení.

Na portálu na stránce **Přehled** pro **MYLOADBALANCER**Najděte jeho IP adresu v části **privátní IP adresa**. Najeďte myší na adresu a výběrem ikony **kopírování** ji zkopírujte. V tomto příkladu je **10.3.0.7**. 

### <a name="connect-to-the-vms-with-rdp"></a>Připojení k virtuálním počítačům přes RDP

Připojte se nejprve pro všechny tři virtuální počítače pomocí vzdálené plochy (RDP). 

>[!NOTE]
>Ve výchozím nastavení se virtuálním počítačům už má otevřený port **RDP** (Vzdálená plocha), aby se povolil přístup ke vzdálené ploše. 

**Do virtuálních počítačů vzdálené plochy (RDP):**

1. Na portálu vyberte **všechny prostředky** v nabídce vlevo. V seznamu prostředků vyberte všechny virtuální počítače ve skupině prostředků **MyResourceGroupLB** .
   
1. Na stránce **Přehled** vyberte **připojit**a pak vyberte **Stáhnout soubor RDP**. 
   
1. Otevřete soubor RDP, který jste stáhli, a vyberte **připojit**.
   
1. Na obrazovce zabezpečení systému Windows vyberte **Další možnosti** a pak **použijte jiný účet**. 
   
   Zadejte uživatelské jméno a heslo a pak vyberte **OK**.
   
1. Odpovědět **Ano** na výzvu k zadání certifikátu. 
   
   V novém okně se otevře na plochu virtuálního počítače. 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>Instalace služby IIS a nahradí výchozí stránku služby IIS na virtuálních počítačích back-end

Na každém serveru back-end pomocí prostředí PowerShell k instalaci IIS a výchozí webovou stránku IIS nahraďte upravené stránky.

>[!NOTE]
>Službu IIS můžete nainstalovat taky pomocí **Průvodce přidáním rolí a funkcí** v **Správce serveru** . 

**Instalace služby IIS a aktualizace výchozí webové stránky pomocí prostředí PowerShell:**

1. V MyVM1 a v MyVM2 spusťte **prostředí Windows PowerShell** z nabídky **Start** . 

2. Spusťte následující příkazy k instalaci IIS a nahradit výchozí webovou stránku IIS:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. Kliknutím na **Odpojit**zavřete připojení RDP k MyVM1 a MyVM2. Nevypínejte virtuálních počítačů.

### <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

1. V MyTestVM otevřete **Internet Explorer**a odpovězte na všechny **výzvy ke konfiguraci** . 
   
1. Do adresního řádku prohlížeče vložte nebo zadejte privátní IP adresu nástroje pro vyrovnávání zatížení (*10.3.0.7*). 
   
   Přizpůsobené serveru výchozí stránku webové služby IIS se zobrazí v prohlížeči. Zpráva čte buď **Hello World z MyVM1**, nebo **Hello World z MyVM2**.
   
1. Aktualizujte prohlížeč, pokud chcete zobrazit distribuci provozu mezi virtuální počítače, nástroj pro vyrovnávání zatížení. Také budete muset vymazat mezipaměť prohlížeče mezi pokusy.

   V některých případech se zobrazí stránka **MyVM1** a jindy se zobrazí stránka **MyVM2** , protože nástroj pro vyrovnávání zatížení distribuuje požadavky na každý virtuální počítač back-end. 

   ![Nové výchozí stránka služby IIS](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odstranit Nástroj pro vyrovnávání zatížení a všechny související prostředky, pokud je už nepotřebujete, otevřete skupinu prostředků **MyResourceGroupLB** a vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili úroveň Basic interního nástroje load balancer. Vytvoření a konfiguraci síťových prostředků, back-end serverů, sondu stavu a pravidel nástroje pro vyrovnávání zatížení. Službu IIS nainstalovali na virtuálních počítačích back-end a použít testovací virtuální počítač k otestování nástroje pro vyrovnávání zatížení v prohlížeči. 

Dále se naučíte vyrovnávat zatížení virtuálních počítačů napříč zónami dostupnosti.

> [!div class="nextstepaction"]
> [Vyrovnávání zatížení virtuálních počítačů napříč zónami dostupnosti](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
