---
title: 'Rychlý start: Vytvoření veřejného Load Balanceru Basic pomocí webu Azure portal'
titlesuffix: Azure Load Balancer
description: Tento rychlý start ukazuje, jak vytvořit veřejné Load Balancer úrovně Basic pomocí webu Azure Portal.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: 64c0a42ac3cc074e5fd9e2824180009431b11e1e
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54231967"
---
# <a name="quickstart-create-a-public-basic-load-balancer-by-using-the-azure-portal"></a>Rychlý start: Vytvoření veřejného load balanceru úrovně Basic pomocí webu Azure portal

Vyrovnávání zatížení zajišťuje vyšší úroveň dostupnosti a škálování tím, že rozprostírá příchozí požadavky ve virtuálních počítačích (VM). Na webu Azure portal můžete použít k vytvoření nástroje pro vyrovnávání zatížení a vyrovnávat přenosy mezi virtuálními počítači. V tomto rychlém startu se dozvíte, jak vytvořit a nakonfigurovat nástroj pro vyrovnávání zatížení, back-end serverů a síťových prostředků v cenové úrovni Basic.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

Provádění úkolů v rámci tohoto rychlého startu, přihlaste se k [webu Azure portal](http://portal.azure.com).

## <a name="create-a-basic-load-balancer"></a>Vytvoření Load Balanceru úrovně Basic

Nejprve vytvořte veřejného load balanceru úrovně Basic prostřednictvím portálu. Název a veřejnou IP adresu, kterou jste vytvořili, se automaticky nakonfiguruje jako front-endu nástroje pro vyrovnávání zatížení.

1. V levém horním rohu webu Azure Portal klikněte na **Vytvořit prostředek** > **Sítě** > **Nástroj pro vyrovnávání zatížení**.
   
1. V **vytvořit nástroj pro vyrovnávání zatížení** podokně, zadejte nebo vyberte tyto hodnoty:
   
   - **Název**: Typ *MyLoadBalancer*.
   - **Typ**: Vyberte **veřejné**. 
   - **SKLADOVÁ POLOŽKA**: Vyberte **Basic**.
   - **Veřejná IP adresa:** Vyberte, že chcete **vytvořit novou** IP adresu. 
     - **Veřejná IP adresa** pole: Typ *MyPublicIP*.
     - **Konfigurace veřejné IP adresy** > **přiřazení**: Vyberte **dynamické**.
   - **Skupina prostředků**: Vyberte **vytvořit nový**, zadejte *MyResourceGroupLB*a vyberte **OK**. 
   
1. Vyberte **Vytvořit**.
   
![Vytvoření nástroje pro vyrovnávání zatížení](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)

## <a name="create-back-end-servers"></a>Vytvoření back-end serverů

Dále vytvořte virtuální síť a dva virtuální počítače pro back endový fond vašeho load balanceru úrovně Basic. 

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Na straně levého horního rohu portálu, vyberte **vytvořit prostředek** > **sítě** > **virtuální síť**.
   
1. V **vytvořit virtuální síť** podokně, zadejte nebo vyberte tyto hodnoty:
   
   - **Název**: Typ *MyVnet*.
   - **Skupina prostředků**: Rozevírací nabídka **vybrat existující** a vyberte **MyResourceGroupLB**. 
   - **Podsíť** > **název**: Typ *MyBackendSubnet*.
   
1. Vyberte **Vytvořit**.

   ![Vytvoření virtuální sítě](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

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
   - **Virtuální síť**: **myVnet**
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
   
   - **Zdroj**: Vyberte **značka služby**.  
   - **Značka zdrojové služby**: Vyberte **Internet**. 
   - **Rozsahy cílových portů**: Typ *80*.
   - **Protokol**: Vyberte **TCP**. 
   - **Akce**: Vyberte **povolit**.  
   - **Priorita**: Typ *100*. 
   - **Název**: Typ *MyHTTPRule*. 
   - **Popis**: Typ *povolení protokolu HTTP*. 
   
1. Vyberte **Přidat**. 
   
   ![Vytvoření pravidla skupiny zabezpečení sítě](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
   
1. Opakujte kroky pro příchozí pravidlo protokolu RDP s použitím následujících odlišných hodnot:
   - **Rozsahy cílových portů**: Typ *3389*.
   - **Priorita**: Typ *200*. 
   - **Název**: Typ *MyRDPRule*. 
   - **Popis**: Typ *povolit RDP*. 

## <a name="create-resources-for-the-load-balancer"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení

V této části nakonfigurujete nastavení nástroje pro vyrovnávání zatížení back endový fond adres, sondu stavu a pravidlo služby load balancer.

### <a name="create-a-backend-address-pool"></a>Vytvoření fondu back-endových adres

Účelem distribuce provozu do virtuálních počítačů, nástroje pro vyrovnávání zatížení používá fond back endových adres. Fond back endových adres obsahuje IP adresy virtuálních síťových rozhraní (NIC), které jsou připojené k nástroji pro vyrovnávání zatížení. 

**Vytvoření fondu back endových adres, který obsahuje VM1 a VM2:**

1. Vyberte **všechny prostředky** v nabídce vlevo a pak vyberte **MyLoadBalancer** ze seznamu prostředků.
   
1. V **Nastavení** vyberte **Back-endové fondy** a potom vyberte **Přidat**.
   
1. Na **přidat back-endový fond** stránky zadejte nebo vyberte následující hodnoty:
   
   - **Název**: Typ *MyBackEndPool*.
   - **Přidružené k**: Rozevírací seznam a vyberte **dostupnosti**.
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
   
   - **Název**: Typ *MyHealthProbe*.
   - **Protokol**: Rozevírací seznam a vyberte **HTTP**. 
   - **Port**: Typ *80*. 
   - **Cesta**: Přijměte */* pro výchozí identifikátor URI. Tuto hodnotu můžete nahradit jiný identifikátor URI. 
   - **Interval**: Typ *15*. Interval je počet sekund mezi pokusy o testování.
   - **Prahová hodnota špatného stavu**: Typ *2*. Tato hodnota je počet chyb po sobě jdoucích sondování, ke kterým dojde před virtuální počítač považoval za poškozený.
   
1. Vyberte **OK**.
   
   ![Přidat test](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje, jak se provoz distribuuje do virtuálních počítačů. Pravidlo definuje konfiguraci front-end IP adresy pro příchozí provoz, back endového fondu IP pro příjem provozu a požadované zdrojových a cílových portů. 

Pravidlo nástroje pro vyrovnávání zatížení s názvem **MyLoadBalancerRule** naslouchá na portu 80 ve front-endu **LoadBalancerFrontEnd**. Toto pravidlo automaticky odesílá síťový provoz do fondu back endových adres **MyBackEndPool**, rovněž na portu 80. 

**Vytvořte pravidlo nástroje pro vyrovnávání zatížení:**


1. Vyberte **všechny prostředky** v nabídce vlevo a pak vyberte **MyLoadBalancer** ze seznamu prostředků.
   
1. V části **Nastavení** vyberte **Pravidla vyrovnávání zatížení** a potom vyberte **Přidat**.
   
1. Na **přidat pravidlo Vyrovnávání zatížení** stránky zadejte nebo vyberte následující hodnoty:
   
   - **Název**: Typ *MyLoadBalancerRule*.
   - **Front-endová IP adresa:** Typ *LoadBalancerFrontend*.
   - **Protokol**: Vyberte **TCP**.
   - **Port**: Typ *80*.
   - **Back-endový port**: Typ *80*.
   - **Back-endový fond**: Vyberte **MyBackendPool**.
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
   
**Instalace služby IIS na virtuálním počítači:**

1. Pokud **správce serveru** není již otevřete na ploše serveru, přejděte na **nástroje pro správu Windows** > **správce serveru**.
   
1. V **správce serveru**vyberte **přidat role a funkce**.
   
   ![Přidání role správce serveru](./media/load-balancer-get-started-internet-portal/servermanager.png)
   
1. V **funkce Průvodce přidáním rolí a**:
   1. Na stránce **Výběr typu instalace** vyberte **Instalace na základě role nebo funkce**.
   1. Na **vybrat cílový server** stránce **MyVM1**.
   1. Na stránce **Výběr role serveru** vyberte na **Webový server (služba IIS)**. 
   1. Do příkazového řádku pro instalaci požadované nástroje vyberte **přidat funkce**. 
   1. Přijměte výchozí hodnoty a vyberte **nainstalovat**. 
   1. Po dokončení funkce instalaci, vyberte **Zavřít**. 
   
1. Opakujte kroky pro virtuální počítač **MyVM2**, s výjimkou nastavte na cílovém serveru **MyVM2**.

### <a name="test-the-load-balancer"></a>Test nástroje pro vyrovnávání zatížení

Otevřete prohlížeč a vyrovnávání zatížení veřejnou IP adresu vložte do panelu Adresa prohlížeče. V prohlížeči by se zobrazit výchozí stránka serveru webové služby IIS.

![Webový server služby IIS](./media/load-balancer-get-started-internet-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odstranit nástroj pro vyrovnávání zatížení a všech souvisejících prostředků, když je už nepotřebujete, otevřete **MyResourceGroupLB** prostředku, skupiny a vyberte **odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili nástroj pro vyrovnávání zatížení úrovně Basic. Vytvoříte a nakonfigurujete skupinu prostředků, síťové prostředky, back-end serverů, sondu stavu a pravidla pomocí nástroje pro vyrovnávání zatížení. Službu IIS nainstalovali na virtuálních počítačích a použít ho k otestování nástroje pro vyrovnávání zatížení. 

Další informace o nástroji Azure Load Balancer, nadále v kurzech.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)
