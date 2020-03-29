---
title: Konfigurace směrování provozu podsítě – Azure Traffic Manager
description: Tento článek vysvětluje, jak nakonfigurovat Traffic Manager pro směrování provozu z konkrétních podsítí.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: rohink
ms.openlocfilehash: 60cddce610d223433d0ffe1f6b9234625aca9881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938737"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Směrování provozu do konkrétních koncových bodů na základě podsítě uživatele pomocí služby Traffic Manager

Tento článek popisuje, jak nakonfigurovat metodu směrování provozu podsítě. Metoda směrování provozu **podsítě** umožňuje namapovat sadu rozsahů IP adres na konkrétní koncové body. Když Traffic Manager přijme požadavek, prozkoumá se zdrojová IP adresa požadavku a vrátí se koncový bod, který je k ní přidružený.

Ve scénáři popsaném v tomto článku pomocí směrování podsítě, v závislosti na ADRESE IP dotazu uživatele, je provoz směrován na interní web nebo produkční web.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

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
V této části vytvoříte dva virtuální počítače *myEndpointVMEastUS* a *myEndpointVMWEurope* v oblastech **Azure východní USA** a západní **Evropy.**

1. V levém horním rohu portálu Azure vyberte **Vytvořit prostředek** > **Compute** > **Windows Server 2016 VM**.
2. V části **Základy** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Name (Název)|myIISVMEastUS|
    |Uživatelské jméno| Zadejte libovolné uživatelské jméno.|
    |Heslo| Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Skupina prostředků| Vyberte **nový** a zadejte *myResourceGroupTM1*.|
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
    |Název virtuálního počítače | myIISVMWEurope|
    |Virtuální síť | Vyberte **Virtuální síť** a v části **Vytvořit virtuální síť** jako **Název** zadejte *myVNet2* a jako podsíť zadejte *mySubnet*.|
    |||

8. Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte ve zbývajících krocích, dokud se oba virtuální počítače nevytvoří.

   ![Vytvoření virtuálního počítače](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalace služby IIS a přizpůsobení výchozí webové stránky

V této části nainstalujete server Služby IIS na dva virtuální počítače – *myIISVMEastUS*  & *myIISVMWEurope*a potom aktualizujte výchozí stránku webu. Na přizpůsobené webové stránce se zobrazí název virtuálního počítače, ke kterému se připojujete při prohlížení webu ve webovém prohlížeči.

1. V levé nabídce vyberte **Všechny prostředky** a pak v seznamu prostředků klikněte na *myIISVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** klikněte na **Připojit** a pak v části **Připojit k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**.
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.
6. Na ploše serveru přejděte na server Správce nástrojů> **pro správu systému Windows**.**Server Manager**
7. Spusťte prostředí Windows PowerShell na *myIISVMEastUS* a pomocí následujících příkazů nainstalujte server Služby IIS a aktualizujte výchozí soubor HTM.
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my test website server - " + $env:computername)
    ```
8. Ukončete připojení RDP k virtuálnímu počítači *myIISVMEastUS*.
9. Opakujte kroky 1-6 vytvořením připojení RDP s VM *myIISVMWEurope* v rámci skupiny prostředků *myResourceGroupTM2* k instalaci služby IIS a přizpůsobení výchozí webové stránky.
10. Spusťte prostředí Windows PowerShell na *myIISVMWEurope* a pomocí následujících příkazů nainstalujte server Služby IIS a aktualizujte výchozí soubor HTM.
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my production website server - " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurace názvů DNS pro virtuální počítače se službou IIS

Traffic Manager směruje provoz uživatelů na základě názvů DNS koncových bodů služby. V této části nakonfigurujete názvy DNS pro servery služby IIS – *myIISVMEastUS* a *myIISVMWEurope*.

1. V levé nabídce klikněte na **Všechny prostředky** a pak v seznamu prostředků vyberte *myIISVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** v části **Název DNS** vyberte **Konfigurovat**.
3. Na stránce **Konfigurace** v části Popisek názvu DNS přidejte jedinečný název a pak vyberte **Uložit**.
4. Zopakujte kroky 1 až 3 pro virtuální počítač *myIISVMWEurope* ve skupině prostředků *myResourceGroupTM1*.

### <a name="create-test-vms"></a>Vytvoření testovacích virtuálních počítačů

V této části vytvoříte virtuální počítač *(mVMEastUS* a *myVMWestEurope)* v každé oblasti Azure **(východní USA** a **západní Evropa**. Pomocí těchto virtuálních počítačů otestujete, jak Traffic Manager směruje provoz při prohlížení webu na nejbližší server služby IIS.

1. V levém horním rohu portálu Azure vyberte **Vytvořit prostředek** > **Compute** > **Windows Server 2016 VM**.
2. V části **Základy** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Name (Název)|myVMEastUS|
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
    |Název virtuálního počítače | *myVMWEurope*|
    |Skupina prostředků | Vyberte **Existující** a pak zadejte *myResourceGroupTM2*.|
    |Virtuální síť | Vyberte **Virtuální síť** a v části **Vytvořit virtuální síť** jako **Název** zadejte *myVNet4* a jako podsíť zadejte *mySubnet4*.|
    |||

8. Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte ve zbývajících krocích, dokud se oba virtuální počítače nevytvoří.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru
Vytvořte profil služby Traffic Manager, který vám umožní na základě zdrojové IP adresy požadavku vracet konkrétní koncové body.

1. V levém horním rohu obrazovky vyberte Vytvořit**profil** >  **správce** > provozu**sítě** > **Vytvořit**.
2. V části **Vytvořit profil služby Traffic Manager** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Name (Název)                   | Tento název musí být jedinečný v rámci zóny trafficmanager.net a ve výsledcích názvu DNS trafficmanager.net, který slouží k přístupu k vašemu profilu služby Traffic Manager.                                   |
    | Metoda směrování          | Jako metodu směrování vyberte **Podsíť**.                                       |
    | Předplatné            | Vyberte své předplatné.                          |
    | Skupina prostředků          | Vyberte **Existující** a zadejte *myResourceGroupTM1*. |
    | |                              |
    |

    ![Vytvoření profilu Traffic Manageru](./media/traffic-manager-subnet-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

Přidejte dva virtuální servery se servery IIS – *myIISVMEastUS* & *myIISVMWEurope* pro směrování uživatelských přenosů na základě podsítě dotazu uživatele.

1. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který jste vytvořili v předchozí části, a pak tento profil služby Traffic Manager vyberte v zobrazených výsledcích.
2. V okně **Profil služby Traffic Manager** v části **Nastavení** klikněte na **Koncové body** a pak na **Přidat**.
3. Zadejte nebo vyberte následující informace, přijměte výchozí hodnoty zbývajících nastavení a pak vyberte **OK**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Typ                    | Koncový bod Azure                                   |
    | Name (Název)           | myTestWebSiteEndpoint                                        |
    | Typ cílového prostředku           | Veřejná IP adresa                          |
    | Cílový prostředek          | **Zvolte veřejnou IP adresu** a zobrazí se výpis prostředků s veřejnými IP adresami ve stejném předplatném. Jako **Prostředek** vyberte veřejnou IP adresu *myIISVMEastUS-ip*. Toto je veřejná IP adresa virtuálního počítače se serverem služby IIS v oblasti USA – východ.|
    |  Nastavení směrování podsítě    |   Přidejte IP adresu testovacího virtuálního počítačů *myVMEastUS.* Všechny uživatelské dotazy pocházející z tohoto virtuálního aplikace budou přesměrovány na *myTestWebSiteEndpoint*.    |

4. Opakováním kroků 2 a 3 přidejte další koncový bod s názvem *myProductionEndpoint* pro veřejnou IP adresu *myIISVMWEurope-ip,* která je přidružena k virtuálnímu virtuálnímu ma serveru IIS s názvem *myIISVMWEurope*. Pro **nastavení směrování podsítě**přidejte IP adresu testovacího virtuálního počítače - *myVMWestEurope*. Všechny uživatelské dotazy z tohoto testovacího virtuálního virtuálního provozu budou směrovány do koncového bodu - *myProductionWebsiteEndpoint*.
5. Po přidání se oba koncové body zobrazí v části **Profil služby Traffic Manager** a jejich stav monitorování bude **Online**.

    ![Přidání koncového bodu služby Traffic Manager](./media/traffic-manager-subnet-routing-method/customize-endpoint-with-subnet-routing-eastus.png)

## <a name="test-traffic-manager-profile"></a>Test profilu služby Traffic Manager
V této části otestujete, jak Traffic Manager směruje provoz uživatelů z dané podsítě do konkrétního koncového bodu. Pokud chcete zobrazit službu Traffic Manager v akci, proveďte následující kroky:
1. Určete název DNS vašeho profilu služby Traffic Manager.
2. Službu Traffic Manager v akci zobrazíte následovně:
    - Z testovacího virtuálního počítače *(myVMEastUS),* který se nachází v oblasti **Usa – východ,** ve webovém prohlížeči vyhledejte název DNS profilu traffic manageru.
    - Z testovacího virtuálního počítače *(myVMEastUS),* který se nachází v oblasti **Západní Evropa,** ve webovém prohlížeči vyhledejte název DNS profilu Traffic Manageru.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Určení názvu DNS profilu služby Traffic Manager
V tomto kurzu pro zjednodušení k prohlížení webů použijete název DNS profilu služby Traffic Manager.

Název DNS profilu služby Traffic Manager můžete určit následujícím způsobem:

1. Na vyhledávacím panelu portálu vyhledejte název **profilu Traffic Manageru,** který jste vytvořili v předchozí části. Ve výsledcích, které se zobrazí, klikněte na profil služby Traffic Manager.
1. Klikněte na **Přehled**.
2. V části **Profil služby Traffic Manager** se zobrazí název DNS nově vytvořeného profilu služby Traffic Manager. V produkčních nasazeních pomocí záznamu DNS CNAME nakonfigurujete individuální název domény odkazující na název domény služby Traffic Manager.

   ![Název DNS služby Traffic Manager](./media/traffic-manager-subnet-routing-method/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Zobrazení služby Traffic Manager v akci
V této části uvidíte službu Traffic Manager v akci.

1. V levé nabídce vyberte **Všechny prostředky** a pak v seznamu prostředků klikněte na *myVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** klikněte na **Připojit** a pak v části **Připojit k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**.
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.
1. Do webového prohlížeče na virtuálním počítači *myVMEastUS* zadejte název DNS vašeho profilu služby Traffic Manager a zobrazte váš web. Vzhledem k tomu, že IP adresa VM *myVMEastUS* je spojena s koncovým bodem *myIISVMEastUS*, webový prohlížeč spouští server webových stránek Test - *myIISVMEastUS*.

   ![Test profilu služby Traffic Manager](./media/traffic-manager-subnet-routing-method/test-traffic-manager.png)

2. Dále se připojte k virtuálnímu virtuálnímu zařízení *myVMWestEurope umístěnému* v **západní Evropě** pomocí kroků 1-5 a přejděte na název domény profilu Traffic Manageru z tohoto virtuálního aplikace. Vzhledem k tomu, že IP adresa VM *myVMWestEurope* je spojena s koncovým bodem *myIISVMEastUS*, webový prohlížeč spouští server webových stránek Test - *myIISVMWEurope*.

## <a name="delete-the-traffic-manager-profile"></a>Odstranění profilu služby Traffic Manager
Pokud už skupiny prostředků (**ResourceGroupTM1** a **ResourceGroupTM2**) nepotřebujete, odstraňte je. Uděláte to tak, že vyberte skupinu prostředků (**ResourceGroupTM1** nebo **ResourceGroupTM2**) a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [metodě váženého směrování provozu](traffic-manager-configure-weighted-routing-method.md).
- Přečtěte si o [metodě prioritního směrování](traffic-manager-configure-priority-routing-method.md).
- Přečtěte si o [metodě geografického směrování](traffic-manager-configure-geographic-routing-method.md).
