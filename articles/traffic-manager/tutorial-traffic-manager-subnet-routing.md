---
title: Konfigurace metody směrování provozu podsítě pomocí služby Azure Traffic Manager | Microsoft Docs
description: Tento článek vysvětluje, jak nakonfigurovat službu Traffic Manager tak, aby směrovala provoz z podsítí uživatelů do konkrétních koncových bodů.
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jpconnock
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: kumud
ms.openlocfilehash: 20c34b820eb326a18be1c4298b0850a58599be64
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956230"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Směrování provozu do konkrétních koncových bodů na základě podsítě uživatele pomocí služby Traffic Manager

Tento článek popisuje, jak nakonfigurovat metodu směrování provozu podsítě. Metoda směrování provozu **podsítě** umožňuje namapovat sadu rozsahů IP adres na konkrétní koncové body. Když Traffic Manager přijme požadavek, prozkoumá se zdrojová IP adresa požadavku a vrátí se koncový bod, který je k ní přidružený. 

V tomto kurzu se s využitím směrování podsítě bude provoz směrovat na interní nebo produkční web v závislosti na IP adrese dotazu uživatele.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření dvou virtuálních počítačů se základním webem ve službě IIS
> * Vytvoření dvou testovacích virtuálních počítačů pro zobrazení služby Traffic Manager v akci
> * Konfigurace názvů DNS pro virtuální počítače se službou IIS
> * Vytvoření profilu služby Traffic Manager pro směrování provozu na základě podsítě uživatele
> * Přidání koncových bodů virtuálních počítačů do profilu služby Traffic Manager
> * Zobrazení služby Traffic Manager v akci


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Aby bylo možné zobrazit službu Traffic Manager v akci, vyžaduje tento kurz, abyste nasadili:
- dva základní weby spuštěné v různých oblastech Azure – **USA – východ** (slouží jako interní web) a **Západní Evropa** (slouží jako produkční web)
- dva testovací virtuální počítače pro testování služby Traffic Manager – jeden virtuální počítač v oblasti **USA – východ** a druhý v oblasti **Západní Evropa** 

Testovací virtuální počítače slouží k ilustraci způsobu, jakým Traffic Manager směruje provoz uživatelů na interní nebo produkční web v závislosti na podsíti, ze které dotaz uživatele pochází.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

### <a name="create-websites"></a>Vytvoření webů

V této části vytvoříte dvě instance webů, které zajistí dva požadované koncové body služby ve dvou oblastech Azure pro profil služby Traffic Manager. Vytvoření těchto dvou webů zahrnuje následující kroky:
1. Vytvoření dvou virtuálních počítačů pro provoz základního webu – jeden v oblasti **USA – východ** a druhý v oblasti **Západní Evropa**
2. Instalace serveru služby IIS na oba virtuální počítače a aktualizace výchozí webové stránky zobrazující název virtuálního počítače, ke kterému je uživatel při prohlížení webu připojený

#### <a name="create-vms-for-running-websites"></a>Vytvoření virtuálních počítačů pro provoz webů
V této části vytvoříte dva virtuální počítače *InternalWebsite* a *ProdWebsite* v oblastech Azure **USA – východ** a **Západní Evropa**.

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek** > **Compute** > **Virtuální počítač s Windows Serverem 2016**.
2. V části **Základy** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|InternalWebsite|
    |Uživatelské jméno| Zadejte libovolné uživatelské jméno.|
    |Heslo| Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Skupina prostředků| Vyberte **Nová** a zadejte *myResourceGroupTM1*.|
    |Umístění| Vyberte **USA – východ**.|
    |||
4. V části **Zvolte velikost** vyberte velikost virtuálního počítače.
5. V části **Nastavení** vyberte následující hodnoty a pak vyberte **OK**:
    
    |Nastavení|Hodnota|
    |---|---|
    |Virtuální síť| Vyberte **Virtuální síť** a v části **Vytvořit virtuální síť** jako **Název** zadejte *myVNet1* a jako podsíť zadejte *mySubnet*.|
    |Skupina zabezpečení sítě|Vyberte **Basic** a v rozevíracím seznamu **Vyberte veřejné příchozí porty** vyberte **HTTP** a **RDP**. |
    |Diagnostika spouštění|Vyberte **Zakázáno**.|
    |||
6. V části **Vytvořit** na kartě **Souhrn** vyberte **Vytvořit** a spusťte nasazování virtuálního počítače.

7. Zopakujte kroky 1 až 6 s následujícími změnami:

    |Nastavení|Hodnota|
    |---|---|
    |Skupina prostředků | Vyberte **Nová** a zadejte *myResourceGroupTM2*.|
    |Umístění|Západní Evropa|
    |Název virtuálního počítače | ProdWebsite|
    |Virtuální síť | Vyberte **Virtuální síť** a v části **Vytvořit virtuální síť** jako **Název** zadejte *myVNet2* a jako podsíť zadejte *mySubnet*.|
    |||
8. Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte ve zbývajících krocích, dokud se oba virtuální počítače nevytvoří.

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalace služby IIS a přizpůsobení výchozí webové stránky

V této části na oba virtuální počítače (*myIISVMEastUS*  & *myIISVMWEurope*) nainstalujete server služby IIS a pak aktualizujete výchozí webovou stránku. Na přizpůsobené webové stránce se zobrazí název virtuálního počítače, ke kterému se připojujete při prohlížení webu ve webovém prohlížeči.

1. V levé nabídce vyberte **Všechny prostředky** a pak v seznamu prostředků klikněte na *myIISVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** klikněte na **Připojit** a pak v části **Připojit k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**. 
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače. 
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.
6. Na ploše serveru přejděte do části **Nástroje pro správu Windows**>**Správce serveru**.
7. Spusťte na virtuálním počítači *InternalWebsite* Windows PowerShell a pomocí následujících příkazů nainstalujte server služby IIS a aktualizujte výchozí soubor htm.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```
8. Ukončete připojení RDP k virtuálnímu počítači *InternalWebsite*.
9. Zopakujte kroky 1 až 6 a vytvořte připojení RDP k virtuálnímu počítači *ProdWebsite* ve skupině prostředků *myResourceGroupTM2* a nainstalujte na něj službu IIS a přizpůsobte výchozí webovou stránku.
10. Spusťte na virtuálním počítači *ProdWebsite* Windows PowerShell a pomocí následujících příkazů nainstalujte server služby IIS a aktualizujte výchozí soubor htm.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurace názvů DNS pro virtuální počítače se službou IIS

Traffic Manager směruje provoz uživatelů na základě názvů DNS koncových bodů služby. V této části nakonfigurujete názvy DNS pro servery služby IIS – *InternalWebsite* a *ProdWebsite*.

1. V levé nabídce klikněte na **Všechny prostředky** a pak v seznamu prostředků vyberte *InternalWebsite* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** v části **Název DNS** vyberte **Konfigurovat**.
3. Na stránce **Konfigurace** v části Popisek názvu DNS přidejte jedinečný název a pak vyberte **Uložit**.
4. Zopakujte kroky 1 až 3 pro virtuální počítač *ProdWebsite* ve skupině prostředků *myResourceGroupTM2*.

### <a name="create-test-vms"></a>Vytvoření testovacích virtuálních počítačů

V této části vytvoříte virtuální počítač *UserVMUS* v oblasti Azure**USA – východ** a virtuální počítač *UserVMEurope* v oblasti Azure **Západní Evropa**. Pomocí těchto virtuálních počítačů otestujete, jak Traffic Manager směruje provoz při prohlížení webu na nejbližší server služby IIS.

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek** > **Compute** > **Windows Server 2016 Datacenter**.
2. V části **Základy** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|*UserVMUS*|
    |Uživatelské jméno| Zadejte libovolné uživatelské jméno.|
    |Heslo| Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Skupina prostředků| Vyberte **Existující** a pak vyberte *myResourceGroupTM1*.|
    |||

4. V části **Zvolte velikost** vyberte velikost virtuálního počítače.
5. V části **Nastavení** vyberte následující hodnoty a pak vyberte **OK**:
    |Nastavení|Hodnota|
    |---|---|
    |Virtuální síť| Vyberte **Virtuální síť** a v části **Vytvořit virtuální síť** jako **Název** zadejte *myVNet3* a jako podsíť zadejte *mySubnet3*.|
    |Skupina zabezpečení sítě|Vyberte **Basic** a v rozevíracím seznamu **Vyberte veřejné příchozí porty** vyberte **HTTP** a **RDP**. |
    |Diagnostika spouštění|Vyberte **Zakázáno**.|
    |||

6. V části **Vytvořit** na kartě **Souhrn** vyberte **Vytvořit** a spusťte nasazování virtuálního počítače.

7. Zopakujte kroky 1 až 5 s následujícími změnami:

    |Nastavení|Hodnota|
    |---|---|
    |Název virtuálního počítače | *UserVMEurope*|
    |Skupina prostředků | Vyberte **Existující** a pak zadejte *myResourceGroupTM2*.|
    |Virtuální síť | Vyberte **Virtuální síť** a v části **Vytvořit virtuální síť** jako **Název** zadejte *myVNet4* a jako podsíť zadejte *mySubnet4*.|
    |||

8. Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte ve zbývajících krocích, dokud se oba virtuální počítače nevytvoří.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru
Vytvořte profil služby Traffic Manager, který vám umožní na základě zdrojové IP adresy požadavku vracet konkrétní koncové body.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek** > **Sítě** > **Profil služby Traffic Manager** > **Vytvořit**.
2. V části **Vytvořit profil služby Traffic Manager** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:
    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Název                   | Tento název musí být jedinečný v rámci zóny trafficmanager.net a ve výsledcích názvu DNS trafficmanager.net, který slouží k přístupu k vašemu profilu služby Traffic Manager.                                   |
    | Metoda směrování          | Jako metodu směrování vyberte **Podsíť**.                                       |
    | Předplatné            | Vyberte své předplatné.                          |
    | Skupina prostředků          | Vyberte **Existující** a zadejte *myResourceGroupTM1*. |
    | |                              |
    |
  
    ![Vytvoření profilu Traffic Manageru](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

Přidejte oba virtuální počítače se službou IIS (*InternalWebsite*  & *ProdWebsite*), aby se provoz uživatelů směroval na základě podsítě dotazu uživatele.

1. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který jste vytvořili v předchozí části, a pak tento profil služby Traffic Manager vyberte v zobrazených výsledcích.
2. V okně **Profil služby Traffic Manager** v části **Nastavení** klikněte na **Koncové body** a pak na **Přidat**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Typ                    | Koncový bod Azure                                   |
    | Název           | myInternalWebSiteEndpoint                                        |
    | Typ cílového prostředku           | Veřejná IP adresa                          |
    | Cílový prostředek          | **Zvolte veřejnou IP adresu** a zobrazí se výpis prostředků s veřejnými IP adresami ve stejném předplatném. Jako **Prostředek** vyberte veřejnou IP adresu *InternalWebsite-ip*. Toto je veřejná IP adresa virtuálního počítače se serverem služby IIS v oblasti USA – východ.|
    |  Nastavení směrování podsítě    |   Přidejte IP adresu testovacího virtuálního počítače *UserVMUS*. Všechny dotazy uživatelů pocházející z tohoto virtuálního počítače se budou směrovat do koncového bodu *InternalWebSiteEndpoint*.    |

4. Zopakujte kroky 2 a 3 a přidejte další koncový bod *ProdWebsiteEndpoint* pro veřejnou IP adresu *ProdWebsite-ip*, která je přidružená k virtuálnímu počítači se serverem služby IIS *ProdWebsite*. V části **Nastavení směrování podsítě** přidejte IP adresu testovacího virtuálního počítače *UserVMEurope*. Všechny dotazy uživatelů z tohoto testovacího virtuálního počítače se budou směrovat do koncového bodu *myProdWebsiteEndpoint*.
5.  Po přidání se oba koncové body zobrazí v části **Profil služby Traffic Manager** a jejich stav monitorování bude **Online**.

 
## <a name="test-traffic-manager-profile"></a>Test profilu služby Traffic Manager
V této části otestujete, jak Traffic Manager směruje provoz uživatelů z dané podsítě do konkrétního koncového bodu. Pokud chcete zobrazit službu Traffic Manager v akci, proveďte následující kroky:
1. Určete název DNS vašeho profilu služby Traffic Manager.
2. Službu Traffic Manager v akci zobrazíte následovně:
    - Na testovacím virtuálním počítači *UserVMUS* v oblasti **USA – východ** ve webovém prohlížeči přejděte na název DNS vašeho profilu služby Traffic Manager.
    - Na testovacím virtuálním počítači *UserVMEurope* v oblasti **Západní Evropa** ve webovém prohlížeči přejděte na název DNS vašeho profilu služby Traffic Manager.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Určení názvu DNS profilu služby Traffic Manager
V tomto kurzu pro zjednodušení k prohlížení webů použijete název DNS profilu služby Traffic Manager. 

Název DNS profilu služby Traffic Manager můžete určit následujícím způsobem:

1.  Na panelu hledání na portálu vyhledejte název **profilu služby Traffic Manager**, který jste vytvořili v předchozí části. Ve výsledcích, které se zobrazí, klikněte na profil služby Traffic Manager.
1. Klikněte na **Přehled**.
2. V části **Profil služby Traffic Manager** se zobrazí název DNS nově vytvořeného profilu služby Traffic Manager. V produkčních nasazeních pomocí záznamu DNS CNAME nakonfigurujete individuální název domény odkazující na název domény služby Traffic Manager.

### <a name="view-traffic-manager-in-action"></a>Zobrazení služby Traffic Manager v akci
V této části uvidíte službu Traffic Manager v akci. 

1. V levé nabídce vyberte **Všechny prostředky** a pak v seznamu prostředků klikněte na *myUserVMUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** klikněte na **Připojit** a pak v části **Připojit k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**. 
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače. 
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování. 
1. Do webového prohlížeče na virtuálním počítači *UserVMUS* zadejte název DNS vašeho profilu služby Traffic Manager a zobrazte váš web. Vzhledem k tomu, že je IP adresa virtuálního počítače *UserVMUS* přidružená ke koncovému bodu *myInternalWebsiteEndpoint*, webový prohlížeč spustí testovací webový server *InternalWebsite*.

2. Pak se pomocí kroků 1 až 5 připojte k virtuálnímu počítači *UserVMEurope* v oblasti **Západní Evropa** a na tomto virtuálním počítači přejděte na název domény profilu služby Traffic Manager. Vzhledem k tomu, že je IP adresa virtuálního počítače *UserVMEurope* přidružená ke koncovému bodu *myProductionWebsiteEndpoint*, webový prohlížeč spustí testovací webový server *ProdWebsite*. 
  
## <a name="delete-the-traffic-manager-profile"></a>Odstranění profilu služby Traffic Manager
Pokud už skupiny prostředků (**ResourceGroupTM1** a **ResourceGroupTM2**) nepotřebujete, odstraňte je. Uděláte to tak, že vyberte skupinu prostředků (**ResourceGroupTM1** nebo **ResourceGroupTM2**) a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [metodě váženého směrování provozu](traffic-manager-configure-weighted-routing-method.md).
- Přečtěte si o [metodě prioritního směrování](traffic-manager-configure-priority-routing-method.md).
- Přečtěte si o [metodě geografického směrování](traffic-manager-configure-geographic-routing-method.md).


