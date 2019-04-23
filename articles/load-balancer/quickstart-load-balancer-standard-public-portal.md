---
title: 'Rychlý start: vytvoření Load balanceru úrovně Standard – webu Azure portal'
titlesuffix: Azure Load Balancer
description: Tento rychlý start ukazuje, jak vytvořit Load balanceru úrovně Standard s využitím webu Azure portal.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 77e322e32d19433d9ce4629c2e04c8bbd7e17f3f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405498"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Rychlý start: Vytvoření Load Balanceru úrovně Standard pro vyrovnávání zatížení virtuálních počítačů pomocí webu Azure Portal

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky na více virtuálních počítačů. K vytvoření nástroje pro vyrovnávání zatížení virtuálních počítačů můžete použít web Azure Portal. V tomto rychlém startu se dozvíte, jak vyrovnávat zatížení virtuálních počítačů pomocí Load Balanceru úrovně Standard.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Vytvoření Load Balanceru úrovně Standard

V této části vytvoříte Load balanceru úrovně Standard, který pomáhá vyrovnávat zatížení virtuálních počítačů. Load Balancer úrovně Standard podporuje pouze standardní veřejnou IP adresu. Při vytváření Load Balanceru úrovně Standard musíte vytvořit také novou standardní veřejnou IP adresu nakonfigurovanou jako jeho front-end (ve výchozím nastavení má název *LoadBalancerFrontend*). 

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek** > **sítě** > **nástroje pro vyrovnávání zatížení**.
2. V **Základy** karty **vytvořit nástroj pro vyrovnávání zatížení** stránky, zadejte nebo vyberte následující informace, přijměte výchozí hodnoty pro zbývající nastavení a pak vyberte **revize + vytvořit**:

    | Nastavení                 | Value                                              |
    | ---                     | ---                                                |
    | Předplatné               | Vyberte své předplatné.    |    
    | Skupina prostředků         | Vyberte **vytvořit nový** a typ *myResourceGroupSLB* v textovém poli.|
    | Name                   | *myLoadBalancer*                                   |
    | Oblast         | Vyberte **Západní Evropa**.                                        |
    | Type          | Vyberte **veřejné**.                                        |
    | Skladová jednotka (SKU)           | Vyberte **standardní**.                          |
    | Veřejná IP adresa | Vyberte, že chcete **vytvořit novou** IP adresu. |
    | Název veřejné IP adresy              | Typ *myPublicIP* v textovém poli.   |
    |Zóna dostupnosti| Vyberte **Zónově redundantní**.    |
3. V **revize + vytvořit** kartu, vyberte možnost **vytvořit**.   

    ![Vytvoření Load Balanceru úrovně Standard](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení

V této části nakonfigurujte nastavení nástroje pro vyrovnávání zatížení pro back-endový fond adres sondu stavu a zadat pravidlo nástroje pro vyrovnávání.

### <a name="create-a-backend-address-pool"></a>Vytvoření fondu back-endových adres

Back-endový fond adres pro distribuci provozu do virtuálních počítačů, obsahuje IP adresy virtuální (NIC) připojené k nástroji pro vyrovnávání zatížení. Vytvoření back-endový fond adres *myBackendPool* zahrnout virtuální počítače pro vyrovnávání zatížení přenosy z Internetu.

1. Vyberte **všechny služby** v nabídce vlevo vyberte **všechny prostředky**a pak vyberte **myLoadBalancer** v seznamu prostředků.
2. V části **nastavení**vyberte **back-endové fondy**a pak vyberte **přidat**.
3. Na **přidat back-endový fond** stránky pro název, typ *myBackendPool*, jako název back-endového fondu a pak vyberte **přidat**.

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu

Povolit nástroje pro vyrovnávání zatížení pro monitorování stavu vaší aplikace, použijte sondu stavu. Sonda stavu dynamicky přidá nebo odebere virtuální počítače z oběhu nástroje pro vyrovnávání zatížení, na základě jejich reakce na kontroly stavu. Vytvořte sondu stavu *myHealthProbe* pro monitorování stavu virtuálních počítačů.

1. Vyberte **všechny služby** v nabídce vlevo vyberte **všechny prostředky**a pak vyberte **myLoadBalancer** v seznamu prostředků.
2. V části **nastavení**vyberte **sondy stavu**a pak vyberte **přidat**.
    
    | Nastavení | Value |
    | ------- | ----- |
    | Name | Zadejte *myHealthProbe*. |
    | Protocol (Protokol) | Vyberte **HTTP**. |
    | Port | Zadejte *80*.|
    | Interval | Zadejte *15* počet **Interval** v sekundách mezi pokusy o testování. |
    | Prahová hodnota pro poškozený stav | Vyberte *2* počet **prahová hodnota špatného stavu** nebo selhání po sobě jdoucích testu, které se musí vyskytovat před virtuální počítač považoval za poškozený.|
    | Sonda stavu | Vyberte *myHealthProbe*. |
4. Vyberte **OK**.

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla Load Balanceru
Pravidlo Load Balanceru slouží k definování způsobu distribuce provozu do virtuálních počítačů. Definujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Vytvořit pravidlo služby Load balancer úrovně *myLoadBalancerRuleWeb* pro naslouchání na portu 80 ve front-endu *FrontendLoadBalancer* a odesílání síťového provozu s vyrovnáváním zatížení do back-endový fond adres *myBackEndPool* rovněž na portu 80. 

1. Vyberte **všechny služby** v nabídce vlevo vyberte **všechny prostředky**a pak vyberte **myLoadBalancer** v seznamu prostředků.
2. V části **nastavení**vyberte **pravidla Vyrovnávání zatížení**a pak vyberte **přidat**.
3. Ke konfiguraci pravidla vyrovnávání zatížení použijte tyto hodnoty:
    
    | Nastavení | Value |
    | ------- | ----- |
    | Name | Enter *myHTTPRule*. |
    | Protocol (Protokol) | Vyberte **TCP**. |
    | Port | Zadejte *80*.|
    | Back-endový port | Zadejte *80*. |
    | Back-endový fond | Vyberte *myBackendPool*.|
    | Sonda stavu | Vyberte *myHealthProbe*. |
4. Pro ostatní nastavení nechte zvolené výchozí hodnoty a vyberte **OK**.
4. Vyberte **OK**.

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části vytvořit virtuální síť, vytvořte dva virtuální počítače pro back-endového fondu nástroje pro vyrovnávání zatížení a pak nainstalujete službu IIS na virtuálních počítačích, která vám pomůže nástroj pro vyrovnávání zatížení otestovat.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
1. Na straně levého horního rohu obrazovky vyberte **vytvořit prostředek** > **sítě** > **virtuální síť**.

1. V **vytvořit virtuální síť**, zadejte nebo vyberte tyto informace:

    | Nastavení | Value |
    | ------- | ----- |
    | Name | Zadejte *myVNet*. |
    | Adresní prostor | Zadejte *10.1.0.0/16*. |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte existující prostředek - *myResourceGroupSLB*. |
    | Location | Vyberte **Západní Evropa**.|
    | Podsíť - Name | Zadejte *myBackendSubnet*. |
    | Podsíť – Rozsah adres | Zadejte *10.1.0.0/24*. |
1. Ponechejte zbývající výchozí hodnoty a vyberte **vytvořit**.

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů
Load balancer úrovně standard podporuje pouze virtuální počítače se standardní IP adresami v back-endový fond. V této části vytvoříte dva virtuální počítače (*myVM1* a *myVM2*) s standardní veřejné IP adresy ve dvou různých zónách (*zóna 1* a *zónu 2*), které se přidají do back-endový fond Standard pro vyrovnávání zatížení, který jste vytvořili dříve.

1. Na straně levého horního rohu portálu, vyberte **vytvořit prostředek** > **Compute** > **systému Windows Server 2016 Datacenter**. 
   
1. V **vytvoření virtuálního počítače**, zadejte nebo vyberte následující hodnoty **Základy** kartu:
   - **Předplatné** > **skupiny prostředků**: Select **myResourceGroupSLB**.
   - **Podrobnosti o instanci** > **název virtuálního počítače**: Typ *myVM1*.
   - **Podrobnosti o instanci** > **oblasti** > vyberte **západní Evropa**.
   - **Podrobnosti o instanci** > **možností dostupnosti** > vyberte **zóny dostupnosti**. 
   - **Podrobnosti o instanci** > **zóna dostupnosti** > vyberte **1**.
  
1. Vyberte **sítě** kartě nebo vyberte **Další: Disky**, pak **Další: Sítě**. 
   
   - Ujistěte se, že jsou vybrány následující:
       - **Virtuální síť**: *myVnet*
       - **Podsíť**: *myBackendSubnet*
       - **Veřejná IP adresa** > vyberte **vytvořit nový**a v **vytvoření veřejné IP adresy** okně pro **SKU**vyberte **standardní**, a pro **zóna dostupnosti**vyberte **zónově redundantní**a pak vyberte **OK**.
   - Chcete-li vytvořit novou skupinu zabezpečení sítě (NSG), typ brány firewall, v části **skupinu zabezpečení sítě**vyberte **Upřesnit**. 
       1. V **konfigurovat skupinu zabezpečení sítě** pole, vyberte **vytvořit nový**. 
       1. Typ *myNetworkSecurityGroup*a vyberte **OK**.
   - Chcete-li virtuální počítač součástí back-endového fondu nástroje pro vyrovnávání zatížení, proveďte následující kroky:
        - V **Vyrovnávání zatížení**, pro **umístit virtuální počítač za existující řešení vyrovnávání zatížení?** vyberte **Ano**.
        - V **nastavení služby Vyrovnávání zatížení**, pro **možnosti vyrovnávání zatížení**vyberte **nástroji Azure load balancer**.
        - Pro **vyberte nástroj pro vyrovnávání zatížení**, *myLoadBalancer*. 
1. Vyberte **správu** kartě nebo vyberte **Další** > **správu**. V části **monitorování**, nastavte **Diagnostika spouštění** k **vypnout**. 
1. Vyberte **Zkontrolovat a vytvořit**.   
1. Zkontrolujte nastavení a pak vyberte **vytvořit**.
1. Podle pokynů vytvořte druhý virtuální počítač s názvem *myVM2*, pomocí standardní SKU veřejné IP adresy s názvem *myVM2 ip*, a **zóna dostupnosti** nastavení **2** a všechna ostatní nastavení stejný jako *myVM1*. 

### <a name="create-nsg-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

V této části vytvoříte pravidlo skupiny zabezpečení sítě, které povolí příchozí připojení pomocí protokolu HTTP.

1. Vyberte **všechny služby** v nabídce vlevo vyberte **všechny prostředky**a potom z prostředků seznamu vyberte **myNetworkSecurityGroup** , který je umístěný v **myResourceGroupSLB** skupinu prostředků.
2. V části **Nastavení** vyberte **Příchozí pravidla zabezpečení** a potom vyberte **Přidat**.
3. Zadáním následujících hodnot pro příchozí pravidlo zabezpečení *myHTTPRule* povolte příchozí připojení HTTP na portu 80:
    - Jako **Zdroj** zadejte *Značka služby*.
    - Jako **Značka zdrojové služby** zadejte *Internet*.
    - Jako **Rozsahy cílových portů** zadejte *80*.
    - Jako **Protokol** zadejte *TCP*.
    - Jako **Akce** zadejte *Povolit*.
    - Jako **Priorita** zadejte *100*.
    - Jako název zadejte *myHTTPRule*.
    - Jako popis zadejte *Povolení protokolu HTTP*.
4. Vyberte **OK**.
 
### <a name="install-iis"></a>Instalace služby IIS

1. Vyberte **všechny služby** v nabídce vlevo vyberte **všechny prostředky**a pak v seznamu prostředků vyberte **myVM1** , který je umístěný v  *myResourceGroupSLB* skupinu prostředků.
2. Na stránce **Přehled** vyberte **Připojit** a připojte se přes RDP k virtuálnímu počítači.
3. Přihlaste se k virtuálnímu počítači s použitím uživatelského jména *azureuser*.
4. Na ploše serveru přejděte do části **Nástroje pro správu Windows**>**Správce serveru**.
5. Ve Správci serveru vyberte **přidat role a funkce**.
6. V **Průvodci přidáním rolí a funkcí** použijte následující hodnoty:
    - V **vybrat typ instalace** stránce **instalace na základě rolí nebo na základě funkcí**.
    - V **vybrat cílový server** stránce **myVM1**
    - V **výběr role serveru** stránce **webového serveru (IIS)**
    - Postupujte podle pokynů a dokončete zbytek průvodce. 
7. Zopakujte kroky 1 až 6 pro virtuální počítač *myVM2*.

## <a name="test-the-load-balancer"></a>Otestování nástroje pro vyrovnávání zatížení
1. Na obrazovce **Přehled** vyhledejte veřejnou IP adresu Load Balanceru. Vyberte **všechny služby** v nabídce vlevo vyberte **všechny prostředky**a pak vyberte **myPublicIP**.

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

      ![Webový server služby IIS](./media/load-balancer-standard-public-portal/9-load-balancer-test.png)

Zobrazit nástroje pro vyrovnávání zatížení distribuuje provoz mezi všechny tři virtuální počítače používající vaši aplikaci, můžete můžete vynutit aktualizaci webového prohlížeče.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, nástroje pro vyrovnávání zatížení a všech souvisejících prostředků. Uděláte to tak, vyberte skupinu prostředků (*myResourceGroupSLB*), který obsahuje nástroje pro vyrovnávání zatížení a pak vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili Load balanceru úrovně Standard, připojené virtuální počítače, nakonfigurovali pravidlo provozu nástroje pro vyrovnávání zatížení a sondu stavu a pak jste nástroj pro vyrovnávání zatížení otestovali. Další informace o službě Azure Load Balancer najdete v kurzech týkajících se služby Azure Load Balancer.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
