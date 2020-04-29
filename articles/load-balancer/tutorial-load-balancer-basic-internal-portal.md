---
title: 'Kurz: vytvoření interního nástroje pro vyrovnávání zatížení – Azure Portal'
titleSuffix: Azure Load Balancer
description: V tomto kurzu se dozvíte, jak vytvořit interní základní nástroj pro vyrovnávání zatížení pomocí Azure Portal.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "79096130"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-basic-load-balancer-in-the-azure-portal"></a>Kurz: Vyrovnávání zatížení interního provozu pomocí základního nástroje pro vyrovnávání zatížení v Azure Portal

Vyrovnávání zatížení poskytuje vyšší úroveň dostupnosti a škálování na základě rozprostření příchozích požadavků napříč virtuálními počítači (VM). Pomocí Azure Portal můžete vytvořit základní nástroj pro vyrovnávání zatížení a vyrovnávat interní provoz mezi virtuálními počítači. V tomto kurzu se dozvíte, jak vytvořit a nakonfigurovat interní nástroj pro vyrovnávání zatížení, back-endové servery a síťové prostředky v cenové úrovni Basic.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

Pokud chcete, můžete tyto kroky provést pomocí [Azure CLI](load-balancer-get-started-ilb-arm-cli.md) nebo [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) místo na portálu.

Pokud chcete postupovat podle tohoto kurzu, přihlaste se k Azure Portal [https://portal.azure.com](https://portal.azure.com)v.

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>Vytvoření virtuální sítě, back-endové serverů a testovacího virtuálního počítače

Nejdřív vytvořte virtuální síť (VNet). Vytvořte ve virtuální síti dva virtuální počítače, které se mají použít pro fond back-end vašeho základního nástroje pro vyrovnávání zatížení, a třetí virtuální počítač, který se použije pro testování nástroje pro vyrovnávání zatížení. 

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. V levé horní části portálu vyberte **vytvořit prostředek** > **síť** > **virtuální síť**.
   
1. V podokně **vytvořit virtuální síť** zadejte nebo vyberte tyto hodnoty:
   
   - **Název**: zadejte *MyVNet*.
   - **Zdroj dat**: vyberte **vytvořit novou**a potom zadejte *MyResourceGroupLB*a vyberte **OK**. 
   - **Subnet** > **Název**podsítě: zadejte *MyBackendSubnet*.
   
1. Vyberte **Vytvořit**.

   ![Vytvoření virtuální sítě](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

1. V levé horní části portálu vyberte **vytvořit prostředek** > **COMPUTE** > **Windows Server 2016 Datacenter**. 
   
1. V části **vytvořit virtuální počítač**zadejte nebo vyberte následující hodnoty na kartě **základy** :
   - **Subscription** > **Skupina prostředků**předplatného: rozevírací seznam a vyberte **MyResourceGroupLB**.
   - **Podrobnosti** > instance**název virtuálního počítače**: zadejte *MyVM1*.
   - **Instance Details** > **Možnosti dostupnosti**podrobností instance: 
     1. Rozevírací seznam a vyberte **skupinu dostupnosti**. 
     2. Vyberte **vytvořit nový**, zadejte *MyAvailabilitySet*a vyberte **OK**.
   
1. Vyberte kartu **síť** nebo vyberte **Další: disky**a **Další: síť**. 
   
   Ujistěte se, že jsou vybrané následující:
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

Vytvořte základní interní nástroj pro vyrovnávání zatížení pomocí portálu. Název a IP adresa, které vytvoříte, se automaticky nakonfigurují jako front-end nástroje pro vyrovnávání zatížení.

1. V levé horní části portálu vyberte **vytvořit prostředek** > **síť** > **Load Balancer**.
   
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
   

## <a name="create-basic-load-balancer-resources"></a>Vytvoření prostředků základního nástroje pro vyrovnávání zatížení

V této části nakonfigurujete nastavení nástroje pro vyrovnávání zatížení pro fond back-end adres a sondu stavu a zadáte pravidla nástroje pro vyrovnávání zatížení.

### <a name="create-a-back-end-address-pool"></a>Vytvoření fondu back-endových adres

K distribuci provozu do virtuálních počítačů využívá nástroj pro vyrovnávání zatížení fond back-endu adres. Fond back-endové adresy obsahuje IP adresy rozhraní virtuální sítě (nic), které jsou připojené k nástroji pro vyrovnávání zatížení. 

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
   
   ![Přidat fond back-endové adresy](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)
   
1. Na stránce **back-end fondy** rozbalte **MyBackendPool** a ujistěte se, že jsou uvedené **VM1** i **VM2** .

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Pokud chcete nástroji pro vyrovnávání zatížení dovolit monitorování stavu virtuálního počítače, použijte sondu stavu. Sonda stavu dynamicky přidává virtuální počítače do oběhu nástroje pro vyrovnávání zatížení nebo je z něj odebírá na základě jejich reakce na kontroly stavu. 

**Vytvoření sondy stavu pro monitorování stavu virtuálních počítačů:**

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
   
   ![Přidání sondy](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje, jak se provoz distribuuje do virtuálních počítačů. Pravidlo definuje konfiguraci front-end IP adres pro příchozí provoz, fond back-end IP adres pro příjem provozu a požadovaný zdrojový a cílový port. 

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
   
   ![Přidání pravidla vyrovnávání zatížení](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

Na back-endové servery nainstalujte Internetová informační služba (IIS) a pak pomocí MyTestVM otestujte Nástroj pro vyrovnávání zatížení pomocí jeho privátní IP adresy. Každý virtuální počítač back-end slouží jako jiná verze výchozí webové stránky IIS, takže můžete vidět požadavky na distribuci vyrovnávání zatížení mezi dvěma virtuálními počítači.

Na portálu na stránce **Přehled** pro **MYLOADBALANCER**Najděte jeho IP adresu v části **privátní IP adresa**. Najeďte myší na adresu a výběrem ikony **kopírování** ji zkopírujte. V tomto příkladu je **10.3.0.7**. 

### <a name="connect-to-the-vms-with-rdp"></a>Připojení k virtuálním počítačům pomocí protokolu RDP

Nejdřív se připojte ke všem třem virtuálním počítačům pomocí vzdálené plochy (RDP). 

>[!NOTE]
>Ve výchozím nastavení se virtuálním počítačům už má otevřený port **RDP** (Vzdálená plocha), aby se povolil přístup ke vzdálené ploše. 

**Do virtuálních počítačů vzdálené plochy (RDP):**

1. Na portálu vyberte **všechny prostředky** v nabídce vlevo. V seznamu prostředků vyberte všechny virtuální počítače ve skupině prostředků **MyResourceGroupLB** .
   
1. Na stránce **Přehled** vyberte **připojit**a pak vyberte **Stáhnout soubor RDP**. 
   
1. Otevřete soubor RDP, který jste stáhli, a vyberte **připojit**.
   
1. Na obrazovce zabezpečení systému Windows vyberte **Další možnosti** a pak **použijte jiný účet**. 
   
   Zadejte uživatelské jméno a heslo a pak vyberte **OK**.
   
1. Odpovědět **Ano** na výzvu k zadání certifikátu. 
   
   Pracovní plochu virtuálního počítače se otevře v novém okně. 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>Instalace služby IIS a nahrazení výchozí stránky IIS na back-endové virtuální počítače

Na každém back-end serveru použijte PowerShell k instalaci služby IIS a nahraďte výchozí webovou stránku služby IIS přizpůsobenou stránkou.

>[!NOTE]
>Službu IIS můžete nainstalovat taky pomocí **Průvodce přidáním rolí a funkcí** v **Správce serveru** . 

**Instalace služby IIS a aktualizace výchozí webové stránky pomocí prostředí PowerShell:**

1. V MyVM1 a v MyVM2 spusťte **prostředí Windows PowerShell** z nabídky **Start** . 

2. Spuštěním následujících příkazů nainstalujte službu IIS a nahraďte výchozí webovou stránku služby IIS:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. Kliknutím na **Odpojit**zavřete připojení RDP k MyVM1 a MyVM2. Virtuální počítače neukončíte.

### <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

1. V MyTestVM otevřete **Internet Explorer**a odpovězte na všechny **výzvy ke konfiguraci** . 
   
1. Do adresního řádku prohlížeče vložte nebo zadejte privátní IP adresu nástroje pro vyrovnávání zatížení (*10.3.0.7*). 
   
   V prohlížeči se zobrazí výchozí stránka vlastní webový server služby IIS. Zpráva čte buď **Hello World z MyVM1**, nebo **Hello World z MyVM2**.
   
1. Aktualizujte prohlížeč, aby se zobrazila distribuce provozu nástroje pro vyrovnávání zatížení napříč virtuálními počítači. Také může být nutné vymazat mezipaměť prohlížeče mezi pokusy.

   V některých případech se zobrazí stránka **MyVM1** a jindy se zobrazí stránka **MyVM2** , protože nástroj pro vyrovnávání zatížení distribuuje požadavky na každý virtuální počítač back-end. 

   ![Nová výchozí stránka služby IIS](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odstranit Nástroj pro vyrovnávání zatížení a všechny související prostředky, pokud je už nepotřebujete, otevřete skupinu prostředků **MyResourceGroupLB** a vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili interní nástroj pro vyrovnávání zatížení úrovně Basic. Vytvořili jste a nakonfigurovali síťové prostředky, back-endové servery, sondu stavu a pravidla pro nástroj pro vyrovnávání zatížení. Nainstalovali jste službu IIS na back-endové virtuální počítače a použili jste testovací virtuální počítač k otestování nástroje pro vyrovnávání zatížení v prohlížeči. 

V dalším kroku se dozvíte, jak vyrovnávat zatížení virtuálních počítačů napříč zónami dostupnosti.

> [!div class="nextstepaction"]
> [Vyrovnávání zatížení virtuálních počítačů napříč zónami dostupnosti](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
