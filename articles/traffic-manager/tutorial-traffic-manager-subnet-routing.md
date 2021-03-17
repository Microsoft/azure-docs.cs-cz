---
title: Kurz – konfigurace směrování provozu v podsíti pomocí Azure Traffic Manager
description: V tomto kurzu se dozvíte, jak nakonfigurovat Traffic Manager pro směrování provozu z podsítí uživatelů do konkrétních koncových bodů.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2021
ms.author: duau
ms.openlocfilehash: 9b916f9942b0459b41d98b952fad072ae48318b3
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505425"
---
# <a name="tutorial-direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Kurz: směrování provozu do konkrétních koncových bodů na základě podsítě uživatele pomocí Traffic Manager

Tento článek popisuje, jak nakonfigurovat metodu směrování provozu podsítě. Metoda směrování provozu v **podsíti** umožňuje mapovat sadu rozsahů IP adres na konkrétní koncové body. Když Traffic Manager obdrží požadavek, zkontroluje zdrojovou IP adresu požadavku a vrátí koncový bod, který je k němu přidružený.

V tomto kurzu použijete směrování podsítě v závislosti na IP adrese dotazu uživatele, provoz se směruje buď na interní web, nebo na produkční Web.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření dvou virtuálních počítačů se základním webem ve službě IIS
> * Vytvoření dvou testovacích virtuálních počítačů pro zobrazení služby Traffic Manager v akci
> * Konfigurace názvů DNS pro virtuální počítače se službou IIS
> * Vytvoření profilu služby Traffic Manager pro směrování provozu na základě podsítě uživatele
> * Přidání koncových bodů virtuálních počítačů do profilu služby Traffic Manager
> * Zobrazení služby Traffic Manager v akci

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

Chcete-li zobrazit Traffic Manager v akci, tento kurz vyžaduje, abyste nasadili následující:

- dva základní weby spuštěné v různých oblastech Azure – **USA – východ** (slouží jako interní web) a **Západní Evropa** (slouží jako produkční web)
- dva testovací virtuální počítače pro testování služby Traffic Manager – jeden virtuální počítač v oblasti **USA – východ** a druhý v oblasti **Západní Evropa**

Testovací virtuální počítače slouží k ilustraci způsobu, jakým Traffic Manager směruje provoz uživatelů na interní nebo produkční web v závislosti na podsíti, ze které dotaz uživatele pochází.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

### <a name="create-websites"></a>Vytvoření webů

V této části vytvoříte dvě instance webů, které zajistí dva požadované koncové body služby ve dvou oblastech Azure pro profil služby Traffic Manager. Vytvoření těchto dvou webů zahrnuje následující kroky:

1. Vytvoření dvou virtuálních počítačů pro provoz základního webu – jeden v oblasti **USA – východ** a druhý v oblasti **Západní Evropa**
2. Instalace serveru služby IIS na oba virtuální počítače a aktualizace výchozí webové stránky zobrazující název virtuálního počítače, ke kterému je uživatel při prohlížení webu připojený

#### <a name="create-vms-for-running-websites"></a>Vytvoření virtuálních počítačů pro provoz webů

V této části vytvoříte dva virtuální počítače *myIISVMEastUS* a *myIISVMWestEurope* v oblastech **východní USA** a **západní Evropa** Azure.

1. V levém horním rohu Azure Portal vyberte **vytvořit prostředek**  >  **COMPUTE**  >  **Windows Server 2019 Datacenter**.
2. V části **vytvořit virtuální počítač** zadejte nebo vyberte následující hodnoty na kartě **základy** :

   - **Předplatné**  >  **Skupina prostředků**: vyberte **vytvořit novou** a potom zadejte **myResourceGroupTM1**.
   - **Podrobnosti instance**  >  **Název virtuálního počítače**: zadejte *myIISVMEastUS*.
   - **Podrobnosti instance**  >  **Oblast**: vyberte **východní USA**.
   - **Účet správce**  >  **Uživatelské jméno**: zadejte uživatelské jméno, které chcete zvolit.
   - **Účet správce**  >  **Heslo**: zadejte heslo, které jste si zvolili. Heslo musí mít délku aspoň 12 znaků a musí splňovat [definované požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - Pravidla portů pro **příchozí spojení**  >  **Veřejné příchozí porty**: vyberte **Povolit vybrané porty**.
   - Pravidla portů pro **příchozí spojení**  >  **Vyberte příchozí porty**: v rozevíracím seznamu vyberte **RDP** a **http** .

3. Vyberte kartu **Správa** nebo vyberte **Další: disky**, **Další: sítě** a **Další: Správa**. V části **monitorování** nastavte **diagnostiku spouštění** na **vypnuto**.
4. Vyberte **Zkontrolovat a vytvořit**.
5. Zkontrolujte nastavení a pak vyberte **vytvořit**.  
6. Postupujte podle kroků a vytvořte druhý virtuální počítač s názvem *myIISVMWestEurope* s názvem **skupiny prostředků** *myResourceGroupTM2*, **umístěním** *západní Evropa* a všechna ostatní nastavení stejné jako *myIISVMEastUS*.
7. Vytvoření virtuálních počítačů trvá několik minut. Ve zbývajících krocích nepokračujte, dokud se nevytvoří oba virtuální počítače.

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalace služby IIS a přizpůsobení výchozí webové stránky

V této části nainstalujete server IIS na dva virtuální počítače – *myIISVMEastUS*  &  *myIISVMWestEurope* a pak aktualizujete výchozí stránku webu. Stránka s vlastním webem zobrazuje název virtuálního počítače, ke kterému se připojujete při návštěvě webu z webového prohlížeče.

1. V nabídce vlevo vyberte **všechny prostředky** a v seznamu prostředky vyberte *myIISVMEastUS* , která je umístěná ve skupině prostředků *myResourceGroupTM1* .
2. Na stránce **Přehled** vyberte **připojit** a potom v části **připojit k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**.
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění, vyberte **Ano** nebo **pokračovat** a pokračujte v připojování.
6. Na ploše serveru přejděte na **Nástroje pro správu systému Windows** > **Správce serveru**.
7. Spusťte Windows PowerShell na virtuálním počítači *myIISVMEastUS* a pomocí následujících příkazů nainstalujte server IIS a aktualizujte výchozí soubor htm.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. Zavřete připojení RDP k virtuálnímu počítači *myIISVMEastUS* .
9. Opakujte kroky 1-6 s vytvořením připojení RDP s *myIISVMWestEurope* virtuálního počítače ve skupině prostředků *myResourceGroupTM2* pro instalaci služby IIS a přizpůsobení její výchozí webové stránky.
10. Spusťte Windows PowerShell na virtuálním počítači s *myIISVMWestEurope* a pomocí následujících příkazů nainstalujte server IIS a aktualizujte výchozí soubor htm.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurace názvů DNS pro virtuální počítače se službou IIS

Traffic Manager směruje provoz uživatelů na základě názvů DNS koncových bodů služby. V této části nakonfigurujete názvy DNS pro servery IIS – *myIISVMEastUS* a *myIISVMWestEurope*.

1. V nabídce vlevo vyberte **všechny prostředky** a potom v seznamu prostředky vyberte *myIISVMEastUS* , která je umístěná ve skupině prostředků *myResourceGroupTM1* .
2. Na stránce **Přehled** v části **Název DNS** vyberte **Konfigurovat**.
3. Na stránce **Konfigurace** v části Popisek názvu DNS přidejte jedinečný název a pak vyberte **Uložit**.
4. Opakujte kroky 1-3 pro virtuální počítač s názvem *myIISVMWestEurope* , který je umístěný ve skupině prostředků *myResourceGroupTM2* .

### <a name="create-test-vms"></a>Vytvoření testovacích virtuálních počítačů

V této části vytvoříte virtuální počítač (*myVMEastUS* a *myVMWestEurope*) v každé oblasti Azure (**východní USA** a **západní Evropa**). Tyto virtuální počítače použijete k otestování, jak Traffic Manager směrují přenosy uživatelů na základě podsítě dotazu uživatele.

1. V levém horním rohu Azure Portal vyberte **vytvořit prostředek**  >  **COMPUTE**  >  **Windows Server 2019 Datacenter**.
2. V části **vytvořit virtuální počítač** zadejte nebo vyberte následující hodnoty na kartě **základy** :

   - **Předplatné**  >  **Skupina prostředků**: vyberte **myResourceGroupTM1**.
   - **Podrobnosti instance**  >  **Název virtuálního počítače**: zadejte *myVMEastUS*.
   - **Podrobnosti instance**  >  **Oblast**: vyberte **východní USA**.
   - **Účet správce**  >  **Uživatelské jméno**: zadejte uživatelské jméno, které chcete zvolit.
   - **Účet správce**  >  **Heslo**: zadejte heslo, které jste si zvolili. Heslo musí mít délku aspoň 12 znaků a musí splňovat [definované požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - Pravidla portů pro **příchozí spojení**  >  **Veřejné příchozí porty**: vyberte **Povolit vybrané porty**.
   - Pravidla portů pro **příchozí spojení**  >  **Vyberte příchozí porty**: v rozevíracím seznamu vyberte **RDP** .

3. Vyberte kartu **Správa** nebo vyberte **Další: disky**, **Další: sítě** a **Další: Správa**. V části **monitorování** nastavte **diagnostiku spouštění** na **vypnuto**.
4. Vyberte **Zkontrolovat a vytvořit**.
5. Zkontrolujte nastavení a pak vyberte **vytvořit**.  
6. Postupujte podle kroků a vytvořte druhý virtuální počítač s názvem *myVMWestEurope* s názvem **skupiny prostředků** *myResourceGroupTM2*, **umístěním** *západní Evropa* a všechna ostatní nastavení stejné jako *myVMEastUS*.
7. Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte ve zbývajících krocích, dokud se oba virtuální počítače nevytvoří.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

Vytvořte profil služby Traffic Manager, který vám umožní na základě zdrojové IP adresy požadavku vracet konkrétní koncové body.

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek**. Vyhledejte *profil Traffic Manager* a vyberte **vytvořit**.
2. V **profilu vytvořit Traffic Manager** zadejte nebo vyberte následující informace. U zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **vytvořit**.

    ![Vytvoření profilu Traffic Manageru](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Název                   | Tento název musí být jedinečný v rámci zóny trafficmanager.net a ve výsledcích názvu DNS trafficmanager.net, který slouží k přístupu k vašemu profilu služby Traffic Manager.                                   |
    | Metoda směrování          | Jako metodu směrování vyberte **Podsíť**.                                       |
    | Předplatné            | Vyberte své předplatné.                          |
    | Skupina prostředků          | Vyberte **Existující** a zadejte *myResourceGroupTM1*. |

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

Přidejte dva virtuální počítače se spuštěným serverem IIS – *myIISVMEastUS*  &  *myIISVMWestEurope* ke směrování provozu uživatelů na základě podsítě dotazu uživatele.

1. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který jste vytvořili v předchozí části, a pak tento profil služby Traffic Manager vyberte v zobrazených výsledcích.
2. V **Traffic Manager profil** v části **Nastavení** vyberte **koncové body** a pak vyberte **Přidat**.
3. Zadejte nebo vyberte následující informace. U zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Typ                    | Koncový bod Azure                                   |
    | Name           | myInternalWebSiteEndpoint                                        |
    | Typ cílového prostředku           | Veřejná IP adresa                          |
    | Cílový prostředek          | **Zvolte veřejnou IP adresu** a zobrazí se výpis prostředků s veřejnými IP adresami ve stejném předplatném. Jako **Prostředek** vyberte veřejnou IP adresu *myIISVMEastUS-ip*. Toto je veřejná IP adresa virtuálního počítače se serverem služby IIS v oblasti USA – východ.|
    |  Nastavení směrování podsítě    |   Přidejte IP adresu testovacího virtuálního počítače *myVMEastUS* . Všechny dotazy uživatelů pocházející z tohoto virtuálního počítače se přesměrují na *myInternalWebSiteEndpoint*.    |

4. Opakujte kroky 2 a 3 a přidejte další koncový bod s názvem *myProdWebsiteEndpoint* pro veřejnou IP adresu *myIISVMWestEurope-IP* , která je přidružená k VIRTUÁLNÍMU počítači serveru služby IIS s názvem *myIISVMWestEurope*. V části **nastavení směrování podsítě** přidejte IP adresu testovacího virtuálního počítače – *myVMWestEurope*. Všechny dotazy uživatelů z tohoto testovacího virtuálního počítače se budou směrovat do koncového bodu *myProdWebsiteEndpoint*.
5. Když se dokončí přidávání obou koncových bodů, zobrazí se v **profilu Traffic Manager** spolu s jejich stavem monitorování jako **online**.

## <a name="test-traffic-manager-profile"></a>Test profilu služby Traffic Manager

V této části otestujete, jak Traffic Manager směruje provoz uživatelů z dané podsítě do konkrétního koncového bodu. Pokud chcete zobrazit službu Traffic Manager v akci, proveďte následující kroky:

1. Určete název DNS vašeho profilu služby Traffic Manager.
2. Službu Traffic Manager v akci zobrazíte následovně:
    - Z testovacího virtuálního počítače (*myVMEastUS*), který je umístěný v oblasti **východní USA** , přejděte ve webovém prohlížeči na název DNS vašeho profilu Traffic Manager.
    - Z testovacího virtuálního počítače (*myVMWestEurope*), který je umístěný v oblasti **západní Evropa** , přejděte ve webovém prohlížeči na název DNS vašeho profilu Traffic Manager.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Určení názvu DNS profilu služby Traffic Manager

V tomto kurzu pro zjednodušení k prohlížení webů použijete název DNS profilu služby Traffic Manager.

Název DNS profilu služby Traffic Manager můžete určit následujícím způsobem:

1. Na panelu hledání na portálu vyhledejte název **profilu Traffic Manager** , který jste vytvořili v předchozí části. V zobrazených výsledcích vyberte profil Traffic Manageru.
2. Vyberte **Přehled**.
3. V části **Profil služby Traffic Manager** se zobrazí název DNS nově vytvořeného profilu služby Traffic Manager. V produkčních nasazeních pomocí záznamu DNS CNAME nakonfigurujete individuální název domény odkazující na název domény služby Traffic Manager.

### <a name="view-traffic-manager-in-action"></a>Zobrazení služby Traffic Manager v akci

V této části uvidíte službu Traffic Manager v akci.

1. V nabídce vlevo vyberte **všechny prostředky** a v seznamu prostředky vyberte *myVMEastUS* , která je umístěná ve skupině prostředků *myResourceGroupTM1* .
2. Na stránce **Přehled** vyberte **připojit** a potom v části **připojit k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**.
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění, vyberte **Ano** nebo **pokračovat** a pokračujte v připojování.
6. Do webového prohlížeče na virtuálním počítači *myVMEastUS* zadejte název DNS vašeho profilu služby Traffic Manager a zobrazte váš web. Vzhledem k tomu, že *myVMEastUS* IP adresa virtuálního počítače je přidružená ke koncovému bodu *myInternalWebsiteEndpoint*, webový prohlížeč spustí testovací webový server – *myIISVMEastUS*.

7. V dalším kroku se připojte k virtuálnímu počítači *myVMWestEurope* , který se nachází v **Západní Evropa** pomocí kroků 1-5 a přejděte k názvu domény profilu Traffic Manager z tohoto virtuálního počítače. Vzhledem k tomu, že *myVMWestEurope* IP adresa virtuálního počítače je přidružená ke koncovému bodu *myProductionWebsiteEndpoint*, webový prohlížeč spustí testovací webový server – *myIISVMWestEurope*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už skupiny prostředků (**ResourceGroupTM1** a **ResourceGroupTM2**) nepotřebujete, odstraňte je. Uděláte to tak, že vyberte skupinu prostředků (**ResourceGroupTM1** nebo **ResourceGroupTM2**) a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

Další informace o metodě směrování podsítí najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Metoda směrování provozu podsítě](traffic-manager-routing-methods.md#subnet)
