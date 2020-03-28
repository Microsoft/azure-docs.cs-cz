---
title: 'Kurz: Vytvoření interního systému vyrovnávání zatížení – portál Azure'
titleSuffix: Azure Load Balancer
description: Tento kurz ukazuje, jak vytvořit interní standardní vyrovnávání zatížení pomocí portálu Azure.
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
ms.date: 01/08/2020
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 918a7700df6b5be3ebca7949875127e42f8d3a91
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75780378"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-standard-load-balancer-in-the-azure-portal"></a>Kurz: Vyvážení zatížení interního provozu standardním vyrovnáváním zatížení na webu Azure Portal

Vyrovnávání zatížení poskytuje vyšší úroveň dostupnosti a škálování rozprostřením příchozích požadavků mezi virtuální počítače .) Portál Azure můžete použít k vytvoření standardního vyrovnávání zatížení a vyvážení interního provozu mezi virtuálními počítači. Tento kurz ukazuje, jak vytvořit a nakonfigurovat interní vyrovnávání zatížení, back-endové servery a síťové prostředky na standardní cenové úrovni.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete. 

Pokud dáváte přednost, můžete provést tyto kroky pomocí [Azure CLI](load-balancer-get-started-ilb-arm-cli.md) nebo [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) místo portálu.

Postup pomocí tohoto kurzu přihlaste na webu [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>Vytvoření virtuální sítě, serverů back-end a testovacího virtuálního virtuálního mandatáře

Nejprve vytvořte virtuální síť (VNet). Ve virtuální síti vytvořte dva virtuální počítače, které se použijí pro back-endový fond vašeho standardního nástroje pro vyrovnávání zatížení, a třetí virtuální počítač, který se použije pro testování nástroje pro vyrovnávání zatížení. 

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Na levé horní straně portálu vyberte Vytvořit**Networking** > **virtuální síť**pro **síť o prostředku** > .
   
1. V podokně **Vytvořit virtuální síť** zadejte nebo vyberte tyto hodnoty:
   
   - **Název**: Zadejte **myvnet**.
   - **ResourceGroup**: Vyberte **Vytvořit nový**, zadejte **MyResourceGroupLB**a vyberte **OK**. 
   - **Název podsítě** > **Name**: Zadejte **mybackendsubnet**.
   
1. Vyberte **Vytvořit**.

   ![Vytvoření virtuální sítě](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

1. Na levé horní straně portálu vyberte **Vytvořit prostředek** > **Compute** > **Windows Server 2016 Datacenter**. 
   
1. Do **pole Vytvořit virtuální počítač**zadejte nebo vyberte na kartě **Základy** následující hodnoty:
   - **Subscription** > **Skupina prostředků předplatného**: Rozevírací seznam a výběr **položky MyResourceGroupLB**.
   - **Podrobnosti instance** > **Název virtuálního počítače**: Zadejte **MyVM1**.
   - **Instance Details** > **Oblast**podrobností instance : Vyberte **možnost Východ USA 2**.
  
   
1. Vyberte kartu **Síť** nebo **další: Disky**, pak **Další: Síť**. 
   
   Ujistěte se, že jsou vybrány následující:
   - **Virtuální síť**: **MyVNet**
   - **Podsíť**: **MyBackendSubnet**
   - **Skupina zabezpečení sítě NIC**: Vyberte **základní**.
   - **Veřejná IP >** Vyberte **Vytvořit nový** a zadejte následující hodnoty a vyberte **OK**:
       - **Název**: **MyVM1-IP**
       - **Skladová položka :** Výběr **standardního**
   - **Veřejné příchozí porty**: Vyberte **Povolit vybrané porty**.
   - **Vybrat příchozí porty**: Rozevírací vyrozené a vyberte **rdp (3389)**

   
   
1. Vyberte kartu **Správa** nebo možnost **Další** > **správa**. V části **Sledování**nastavte **diagnostiku spouštění** na **Vypnuto**.
   
1. Vyberte **Zkontrolovat a vytvořit**.
   
1. Zkontrolujte nastavení a pak vyberte **Vytvořit**. 

1. Podle pokynů vytvořte druhý virtuální virtuální počítače s názvem **MyVM2**, přičemž všechna ostatní nastavení budou stejná jako MyVM1. 

1. Podle pokynů znovu vytvořte třetí virtuální ms s názvem **MyTestVM**. 

## <a name="create-a-standard-load-balancer"></a>Vytvoření standardního odvykaču zatížení

Vytvořte standardní interní vyrovnávání zatížení pomocí portálu. Název a adresa IP, které vytvoříte, jsou automaticky konfigurovány jako front-end uvyklací zatížení.

1. Na levé horní straně portálu vyberte Vytvořit nástroje**pro vyrovnávání zatížení****sítě** >  **.** > 
   
2. Na kartě **Základy** na stránce **Vytvořit vyrovnávání zatížení** zadejte nebo vyberte následující informace, přijměte výchozí hodnoty zbývajících nastavení a pak vyberte **Zkontrolovat + vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **Vytvořit nový** a do textového pole zadejte *MyResourceGroupLB.*|
    | Name (Název)                   | *myLoadBalancer*                                   |
    | Region (Oblast)         | Vyberte **USA – východ 2**.                                        |
    | Typ          | Vyberte **interní**.                                        |
    | Skladová jednotka (SKU)           | Vyberte **standardní**.                          |
    | Virtuální síť           | Vyberte *myvnet*.                          |    
    | Přiřazení IP adresy              | Vyberte **možnost Statické**.   |
    | Privátní IP adresa|Zadejte adresu, která je v adresním prostoru virtuální sítě a podsítě, například *10.3.0.7*.  |

3. Na kartě **Revize + vytvoření** klikněte na **Vytvořit**. 
   

## <a name="create-standard-load-balancer-resources"></a>Vytvořit standardní prostředky pro vyrovnávání zatížení

V této části nakonfigurujete nastavení vyrovnávání zatížení pro fond adres back-end a sondu stavu a určíte pravidla vyrovnávání zatížení.

### <a name="create-a-back-end-address-pool"></a>Vytvoření fondu back-endových adres

K distribuci provozu na virtuální chod, vyrovnávání zatížení používá fond adres back-end. Fond back-endových adres obsahuje IP adresy rozhraní virtuální sítě ,, které jsou připojeny k nástrojovi pro vyrovnávání zatížení. 

**Vytvoření fondu adres back-end, který zahrnuje VM1 a VM2:**

1. V levém menu vyberte **Všechny prostředky** a ze seznamu zdrojů vyberte **MyLoadBalancer.**
   
1. V **Nastavení** vyberte **Back-endové fondy** a potom vyberte **Přidat**.
   
1. Na stránce **Přidat fond back-endu** zadejte nebo vyberte následující hodnoty:
   
   - **Název**: Zadejte **MyBackendPool**.
   
1. V části **Virtuální počítače**. 
   1. Přidejte **MyVM1** a **MyVM2** do back-end fondu.
   2. Po přidání každého počítače rozbalte položku a vyberte jeho **konfiguraci ip sítě**. 
     
1. Vyberte **Přidat**.
   
   ![Přidání fondu adres back-end](./media/tutorial-load-balancer-standard-internal-portal/3-load-balancer-backend-02.png)
   
1. Na stránce **back-endfondy** rozbalte **MyBackendPool** a ujistěte se, že jsou uvedeny **v Seznamu VM1** i **VM2.**

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Chcete-li povolit nástroj pro vyrovnávání zatížení ke sledování stavu virtuálního provozu, použijte sondu stavu. Sonda stavu dynamicky přidává virtuální počítače do oběhu nástroje pro vyrovnávání zatížení nebo je z něj odebírá na základě jejich reakce na kontroly stavu. 

**Vytvoření sondy stavu pro sledování stavu virtuálních jevů:**

1. V levém menu vyberte **Všechny prostředky** a ze seznamu zdrojů vyberte **MyLoadBalancer.**
   
1. V **Nastavení** vyberte **Sondy stavu** a potom vyberte **Přidat**.
   
1. Na stránce **Přidat sondu stavu** zadejte nebo vyberte následující hodnoty:
   
   - **Název**: Zadejte **MyHealthProbe**.
   - **Protokol**: Rozevírací a vyberte **možnost HTTP**. 
   - **Port**: Typ **80**. 
   - **Cesta:** **/** Přijmout pro výchozí identifikátor URI. Tuto hodnotu můžete nahradit jiným identifikátorem URI. 
   - **Interval**: Typ **15**. Interval je počet sekund mezi pokusy o sondu.
   - **Prahová hodnota není v pořádku**: Typ **2**. Tato hodnota je počet po sobě jdoucích selhání sondy, ke kterým dochází před virtuální ms je považovánza nefunkční.
   
1. Vyberte **OK**.
   
   ![Přidání sondy](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje, jak se provoz distribuuje do virtuálních počítačů. Pravidlo definuje konfiguraci ip adres front-endu pro příchozí provoz, back-endový fond IP pro příjem přenosů a požadované zdrojové a cílové porty. 

Pravidlo pro vyrovnávání zatížení s názvem **MyLoadBalancerRule** naslouchá portu 80 v front-endu **LoadBalancerFrontEndEnd .** Pravidlo odesílá síťový provoz do fondu adres back-end **MyBackendPool**, také na portu 80. 

**Vytvoření pravidla pro vyrovnávání zatížení:**

1. V levém menu vyberte **Všechny prostředky** a ze seznamu zdrojů vyberte **MyLoadBalancer.**
   
1. V části **Nastavení** vyberte **Pravidla vyrovnávání zatížení** a potom vyberte **Přidat**.
   
1. Na stránce **Přidat pravidlo vyrovnávání zatížení** zadejte nebo vyberte následující hodnoty, pokud již nejsou k dispozici:
   
   - **Název**: Zadejte **MyLoadBalancerRule**.
   - **Ip adresa front-endu:** Zadejte **LoadBalancerFrontEnd,** pokud není k dispozici.
   - **Protokol**: Vyberte **tcp**.
   - **Port**: Typ **80**.
   - **Back-end port**: Typ **80**.
   - **Back-endový fond**: Vyberte **MyBackendPool**.
   - **Zdravotní sonda**: Vyberte **MyHealthProbe**. 
   
1. Vyberte **OK**.
   
   ![Přidání pravidla vyrovnávání zatížení](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

Nainstalujte internetovou informační službu (IIS) na servery back-end a potom pomocí myTestVM otestujte vyvyčovávač zatížení pomocí privátní IP adresy. Každý back-endový virtuální virtuální server slouží jiné verzi výchozí webové stránky služby IIS, takže uvidíte, že vyrovnávání zatížení distribuuje požadavky mezi dva virtuální servery.

Na portálu na stránce **Přehled** pro **MyLoadBalancer**vyhledejte jeho IP adresu v části **Privátní IP adresa**. Najeďte na adresu a vyberte ikonu **Kopírovat,** kterou chcete zkopírovat. V tomto příkladu je **10.3.0.7**. 

### <a name="connect-to-the-vms-with-rdp"></a>Připojení k virtuálním kvadám pomocí RDP

Nejprve se připojte ke všem třem virtuálním počítačům pomocí vzdálené plochy (RDP). 

>[!NOTE]
>Ve výchozím nastavení virtuální počítače již mají otevřený port **RDP** (Vzdálená plocha), který umožňuje přístup ke vzdálené ploše. 

**Chcete-li vzdálenou plochu (RDP) do virtuálních počítačů:**

1. Na portálu vyberte **všechny prostředky** v levé nabídce. Ze seznamu prostředků vyberte každý virtuální ms ve skupině prostředků **MyResourceGroupLB.**
   
1. Na stránce **Přehled** vyberte **Připojit**a pak **vyberte Stáhnout soubor RDP**. 
   
1. Otevřete stažený soubor RDP a vyberte **Připojit**.
   
1. Na obrazovce Zabezpečení Systému Windows vyberte **Další volby** a **potom použijte jiný účet**. 
   
   Zadejte uživatelské jméno a heslo a pak vyberte **OK**.
   
1. Odpověď **Ano** na výzvu k certifikátu. 
   
   Plocha virtuálního počítače se otevře v novém okně. 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>Nainstalujte službu IIS a nahraďte výchozí stránku služby IIS na koncových virtuálních počítačích

Na každém serveru back-end použijte prostředí PowerShell k instalaci služby IIS a nahraďte výchozí webovou stránku služby IIS přizpůsobenou stránkou.

>[!NOTE]
>K instalaci služby IIS můžete také nainstalovat **Průvodce přidáním rolí a funkcí** ve **Správci serveru.** 

**Instalace služby IIS a aktualizace výchozí webové stránky pomocí prostředí PowerShell:**

1. Na MyVM1 a MyVM2 spusťte **prostředí Windows PowerShell** z nabídky **Start.** 

2. Chcete-li nainstalovat službu IIS a nahradit výchozí webovou stránku služby IIS, spusťte následující příkazy:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
      remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add custom htm file
      Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. Zavřete připojení RDP pomocí myvm1 a myvm2 výběrem **možnosti Odpojit**. Nevypínejte virtuální chod.

### <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

1. V aplikaci MyTestVM sem otevřete **aplikaci Internet Explorer**a odpovězte na výzvy **konfigurace.** 
   
1. Do adresního řádku prohlížeče vložte nebo zadejte soukromou IP adresu vykladače zatížení (*10.3.0.7).* 
   
   Přizpůsobená výchozí stránka webového serveru služby IIS se zobrazí v prohlížeči. Zpráva čte buď **Hello World z MyVM1**, nebo **Hello World z MyVM2**.
   
1. Aktualizujte prohlížeč, abyste viděli, jak balancer na vyrovnávání zatížení distribuuje provoz mezi virtuálními počítačemi. Může být také nutné vymazat mezi pokusy mezi pokusy mezi mezi pokusy.

   Někdy se zobrazí stránka **MyVM1** a jindy se zobrazí stránka **MyVM2,** protože vyrovnávání zatížení distribuuje požadavky na každý back-endový virtuální počítače. 

   ![Nová výchozí stránka iis](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odstranit správce zatížení a všechny související prostředky, když je již nepotřebujete, otevřete skupinu prostředků **MyResourceGroupLB** a vyberte **odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili standardní interní vyrovnávání zatížení. Vytvořili jste a nakonfigurovali síťové prostředky, servery back-end, sondu stavu a pravidla pro vyrovnávání zatížení. Nainstalovali jste iis na back-end virtuálních počítačích a pomocí testovacího virtuálního počítače otestovat vyrovnávání zatížení v prohlížeči. 

Dále se dozvíte, jak vyvážit virtuální virtuální mích napříč zónami dostupnosti.

> [!div class="nextstepaction"]
> [Vyrovnávání zatížení virtuálních počítačů napříč zónami dostupnosti](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
