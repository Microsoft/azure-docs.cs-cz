---
title: Konfigurace metody směrování podsítě provozu pomocí Azure Traffic Manageru
description: Tento článek vysvětluje, jak nakonfigurovat službu Traffic Manager tak, aby směrovala provoz z podsítí uživatelů do konkrétních koncových bodů.
services: traffic-manager
documentationcenter: ''
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: allensu
ms.openlocfilehash: da2d4816f3f7a99ac2d213d72d7e801cf630e165
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304933"
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

### <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

### <a name="create-websites"></a>Vytvoření webů

V této části vytvoříte dvě instance webů, které zajistí dva požadované koncové body služby ve dvou oblastech Azure pro profil služby Traffic Manager. Vytvoření těchto dvou webů zahrnuje následující kroky:

1. Vytvoření dvou virtuálních počítačů pro provoz základního webu – jeden v oblasti **USA – východ** a druhý v oblasti **Západní Evropa**
2. Instalace serveru služby IIS na oba virtuální počítače a aktualizace výchozí webové stránky zobrazující název virtuálního počítače, ke kterému je uživatel při prohlížení webu připojený

#### <a name="create-vms-for-running-websites"></a>Vytvoření virtuálních počítačů pro provoz webů

V této části vytvoříte dva virtuální počítače *myIISVMEastUS* a *myIISVMWestEurope* v **USA – východ** a **západní Evropa** oblastí Azure.

1. V pravém horním levém horním rohu webu Azure portal vyberte **vytvořit prostředek** > **Compute** > **systému Windows Server. 2019 Datacenter**.
2. V **vytvoření virtuálního počítače**, zadejte nebo vyberte následující hodnoty **Základy** kartu:

   - **Předplatné** > **skupiny prostředků**: Vyberte **vytvořit nový** a pak zadejte **myResourceGroupTM1**.
   - **Podrobnosti o instanci** > **název virtuálního počítače**: Typ *myIISVMEastUS*.
   - **Podrobnosti o instanci** > **oblasti**:  Vyberte **USA – východ**.
   - **Účet správce** > **uživatelské jméno**:  Zadejte libovolné uživatelské jméno.
   - **Účet správce** > **heslo**:  Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Příchozí pravidla portů** > **veřejné příchozí porty**: Vyberte **povolit vybrané porty**.
   - **Příchozí pravidla portů** > **vyberte příchozí porty**: Vyberte **RDP** a **HTTP** o přijetí změn seznamu.

3. Vyberte **správu** kartě nebo vyberte **Další: Disky**, pak **Další: Sítě**, pak **Další: Správa**. V části **monitorování**, nastavte **Diagnostika spouštění** k **vypnout**.
4. Vyberte **Zkontrolovat a vytvořit**.
5. Zkontrolujte nastavení a potom klikněte na tlačítko **vytvořit**.  
6. Podle pokynů vytvořte druhý virtuální počítač s názvem *myIISVMWestEurope*, s **skupiny prostředků** název *myResourceGroupTM2*, **umístění**z *západní Evropa*a všechna ostatní nastavení stejný jako *myIISVMEastUS*.
7. Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte ve zbývajících krocích, dokud se oba virtuální počítače nevytvoří.

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalace služby IIS a přizpůsobení výchozí webové stránky

V této části nainstalujete server služby IIS na dva virtuální počítače – *myIISVMEastUS* & *myIISVMWestEurope*a pak aktualizujte výchozí stránku webu. Na přizpůsobené webové stránce se zobrazí název virtuálního počítače, ke kterému se připojujete při prohlížení webu ve webovém prohlížeči.

1. V levé nabídce vyberte **Všechny prostředky** a pak v seznamu prostředků klikněte na *myIISVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** klikněte na **Připojit** a pak v části **Připojit k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**.
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.
6. Na ploše serveru přejděte do části **Nástroje pro správu Windows**>**Správce serveru**.
7. Spuštění Windows Powershellu na virtuálním počítači *myIISVMEastUS*a pomocí následujících příkazů nainstalujte server služby IIS a aktualizovat výchozí soubor htm.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. Ukončete připojení RDP s *myIISVMEastUS* virtuálního počítače.
9. Opakujte kroky 1 až 6 s tím, že vytvoříte připojení ke vzdálené ploše s virtuálním Počítačem *myIISVMWestEurope* v rámci *myResourceGroupTM2* skupinu prostředků k instalaci IIS a přizpůsobit její výchozí webové stránky.
10. Spuštění Windows Powershellu na *myIISVMWestEurope* virtuálního počítače a pomocí následujících příkazů nainstalujte server služby IIS a aktualizovat soubor htm výchozí.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurace názvů DNS pro virtuální počítače se službou IIS

Traffic Manager směruje provoz uživatelů na základě názvů DNS koncových bodů služby. V této části nakonfigurujete názvy DNS pro servery služby IIS – *myIISVMEastUS* a *myIISVMWestEurope*.

1. V levé nabídce klikněte na **Všechny prostředky** a pak v seznamu prostředků vyberte *myIISVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** v části **Název DNS** vyberte **Konfigurovat**.
3. Na stránce **Konfigurace** v části Popisek názvu DNS přidejte jedinečný název a pak vyberte **Uložit**.
4. Opakujte kroky 1-3 pro virtuální počítač s názvem *myIISVMWestEurope* , který je umístěný v *myResourceGroupTM2* skupinu prostředků.

### <a name="create-test-vms"></a>Vytvoření testovacích virtuálních počítačů

V této části vytvoříte virtuální počítač (*myVMEastUS* a *myVMWestEurope*) v každé oblasti Azure (**USA – východ** a **západní Evropa**). Tyto virtuální počítače budete používat pro testování, jak Traffic Manager směruje provoz uživatelů na základě podsítě dotaz uživatele.

1. V pravém horním levém horním rohu webu Azure portal vyberte **vytvořit prostředek** > **Compute** > **systému Windows Server. 2019 Datacenter**.
2. V **vytvoření virtuálního počítače**, zadejte nebo vyberte následující hodnoty **Základy** kartu:

   - **Předplatné** > **skupiny prostředků**: Select **myResourceGroupTM1**.
   - **Podrobnosti o instanci** > **název virtuálního počítače**: Typ *myVMEastUS*.
   - **Podrobnosti o instanci** > **oblasti**:  Vyberte **USA – východ**.
   - **Účet správce** > **uživatelské jméno**:  Zadejte libovolné uživatelské jméno.
   - **Účet správce** > **heslo**:  Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Příchozí pravidla portů** > **veřejné příchozí porty**: Vyberte **povolit vybrané porty**.
   - **Příchozí pravidla portů** > **vyberte příchozí porty**: Vyberte **RDP** o přijetí změn seznamu.

3. Vyberte **správu** kartě nebo vyberte **Další: Disky**, pak **Další: Sítě**, pak **Další: Správa**. V části **monitorování**, nastavte **Diagnostika spouštění** k **vypnout**.
4. Vyberte **Zkontrolovat a vytvořit**.
5. Zkontrolujte nastavení a potom klikněte na tlačítko **vytvořit**.  
6. Podle pokynů vytvořte druhý virtuální počítač s názvem *myVMWestEurope*, s **skupiny prostředků** název *myResourceGroupTM2*, **umístění** z *západní Evropa*a všechna ostatní nastavení stejný jako *myVMEastUS*.
7. Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte ve zbývajících krocích, dokud se oba virtuální počítače nevytvoří.

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

Přidejte dva virtuální počítače se službou IIS servery – *myIISVMEastUS* & *myIISVMWestEurope* směrovat provoz uživatelů na základě podsítě dotaz uživatele.

1. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který jste vytvořili v předchozí části, a pak tento profil služby Traffic Manager vyberte v zobrazených výsledcích.
2. V okně **Profil služby Traffic Manager** v části **Nastavení** klikněte na **Koncové body** a pak na **Přidat**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Type                    | Koncový bod Azure                                   |
    | Název           | myInternalWebSiteEndpoint                                        |
    | Typ cílového prostředku           | Veřejná IP adresa                          |
    | Cílový prostředek          | **Zvolte veřejnou IP adresu** a zobrazí se výpis prostředků s veřejnými IP adresami ve stejném předplatném. Jako **Prostředek** vyberte veřejnou IP adresu *myIISVMEastUS-ip*. Toto je veřejná IP adresa virtuálního počítače se serverem služby IIS v oblasti USA – východ.|
    |  Nastavení směrování podsítě    |   Přidat IP adresu *myVMEastUS* testovací virtuální počítač. Jakýkoli dotaz uživatelů pocházejících z tohoto virtuálního počítače budete přesměrováni na *myInternalWebSiteEndpoint*.    |

4. Opakujte kroky 2 a 3 a přidat jiný koncový bod s názvem *myProdWebsiteEndpoint* pro veřejnou IP adresu *myIISVMWestEurope ip* , který je přidružen k serveru služby IIS virtuální počítač s názvem  *myIISVMWestEurope*. Pro **směrování nastavení podsítě**, přidat IP adresu testovacího virtuálního počítače – *myVMWestEurope*. Všechny dotazy uživatelů z tohoto testovacího virtuálního počítače se budou směrovat do koncového bodu *myProdWebsiteEndpoint*.
5. Po přidání se oba koncové body zobrazí v části **Profil služby Traffic Manager** a jejich stav monitorování bude **Online**.

## <a name="test-traffic-manager-profile"></a>Test profilu služby Traffic Manager

V této části otestujete, jak Traffic Manager směruje provoz uživatelů z dané podsítě do konkrétního koncového bodu. Pokud chcete zobrazit službu Traffic Manager v akci, proveďte následující kroky:

1. Určete název DNS vašeho profilu služby Traffic Manager.
2. Službu Traffic Manager v akci zobrazíte následovně:
    - Z testovacího virtuálního počítače (*myVMEastUS*), který je umístěný v **USA – východ** oblast, ve webovém prohlížeči přejděte na název DNS vašeho profilu Traffic Manageru.
    - Z testovacího virtuálního počítače (*myVMWestEurope*), který je umístěný v **západní Evropa** oblast, ve webovém prohlížeči přejděte na název DNS vašeho profilu Traffic Manageru.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Určení názvu DNS profilu služby Traffic Manager

V tomto kurzu pro zjednodušení k prohlížení webů použijete název DNS profilu služby Traffic Manager.

Název DNS profilu služby Traffic Manager můžete určit následujícím způsobem:

1. Na panelu hledání na portálu vyhledejte název **profilu služby Traffic Manager**, který jste vytvořili v předchozí části. Ve výsledcích, které se zobrazí, klikněte na profil služby Traffic Manager.
2. Klikněte na **Přehled**.
3. V části **Profil služby Traffic Manager** se zobrazí název DNS nově vytvořeného profilu služby Traffic Manager. V produkčních nasazeních pomocí záznamu DNS CNAME nakonfigurujete individuální název domény odkazující na název domény služby Traffic Manager.

### <a name="view-traffic-manager-in-action"></a>Zobrazení služby Traffic Manager v akci

V této části uvidíte službu Traffic Manager v akci.

1. V levé nabídce vyberte **Všechny prostředky** a pak v seznamu prostředků klikněte na *myVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** klikněte na **Připojit** a pak v části **Připojit k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**.
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.
6. Do webového prohlížeče na virtuálním počítači *myVMEastUS* zadejte název DNS vašeho profilu služby Traffic Manager a zobrazte váš web. Od virtuálního počítače *myVMEastUS* IP adresa je přidružená ke koncovému bodu *myInternalWebsiteEndpoint*, webový prohlížeč spustí Test webu serveru - *myIISVMEastUS*.

7. Dále se připojte k virtuálnímu počítači *myVMWestEurope* umístěné v **západní Evropa** pomocí kroků 1 až 5 a přejděte na název domény pro profil Traffic Manageru z tohoto virtuálního počítače. Od virtuálního počítače *myVMWestEurope* IP adresa je přidružená ke koncovému bodu *myProductionWebsiteEndpoint*, webový prohlížeč spustí Test webu serveru - *myIISVMWestEurope*.

## <a name="delete-the-traffic-manager-profile"></a>Odstranění profilu služby Traffic Manager

Pokud už skupiny prostředků (**ResourceGroupTM1** a **ResourceGroupTM2**) nepotřebujete, odstraňte je. Uděláte to tak, že vyberte skupinu prostředků (**ResourceGroupTM1** nebo **ResourceGroupTM2**) a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další postup

- Přečtěte si o [metodě váženého směrování provozu](traffic-manager-configure-weighted-routing-method.md).
- Přečtěte si o [metodě prioritního směrování](traffic-manager-configure-priority-routing-method.md).
- Přečtěte si o [metodě geografického směrování](traffic-manager-configure-geographic-routing-method.md).
