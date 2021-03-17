---
title: Kurz – vylepšení odpovědi webu pomocí Azure Traffic Manager
description: Tento článek kurzu popisuje, jak vytvořit profil Traffic Manager pro vytvoření vysoce reagujících webových stránek.
services: traffic-manager
author: duongau
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: d8262a80fac42f103d571523c75c5064d5d43949
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003816"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Kurz: vylepšení odpovědi webu pomocí Traffic Manager

V tomto kurzu se dozvíte, jak pomocí Traffic Manager vytvořit vysoce reagující web pomocí přesměrování uživatelského provozu na web s nejnižší latencí. Datové centrum s nejnižší latencí je obvykle ten, který je nejblíže geografické vzdálenosti.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření dvou virtuálních počítačů se základním webem ve službě IIS
> * Vytvoření dvou testovacích virtuálních počítačů pro zobrazení služby Traffic Manager v akci
> * Konfigurace názvů DNS pro virtuální počítače se službou IIS
> * Vytvoření profilu Traffic Manager pro zlepšení výkonu webu
> * Přidání koncových bodů virtuálních počítačů do profilu služby Traffic Manager
> * Zobrazení služby Traffic Manager v akci

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

Aby bylo možné zobrazit službu Traffic Manager v akci, vyžaduje tento kurz, abyste nasadili:

- Dvě instance základních webů, které běží v různých oblastech Azure – **východní USA** a **západní Evropa**.
- Dva testovací virtuální počítače pro testování Traffic Manager-One virtuální počítač v **východní USA** a druhý virtuální počítač v **západní Evropa**. Testovací virtuální počítače slouží k ilustraci, jak Traffic Manager směrují provoz uživatele na web, který běží ve stejné oblasti, protože poskytuje nejnižší latenci.

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
   - Pravidla portů pro **příchozí spojení**  >  **Vyberte příchozí porty**: v poli pro stažení vyberte **RDP** a **http** .

3. Vyberte kartu **Správa** nebo vyberte **Další: disky**, **Další: sítě** a **Další: Správa**. V části **monitorování** nastavte **diagnostiku spouštění** na **vypnuto**.
4. Vyberte **Zkontrolovat a vytvořit**.
5. Zkontrolujte nastavení a klikněte na **vytvořit**.  
6. Postupujte podle kroků a vytvořte druhý virtuální počítač s názvem *myIISVMWestEurope* s názvem **skupiny prostředků** *myResourceGroupTM2*, **umístěním** *západní Evropa* a všechna ostatní nastavení stejné jako *myIISVMEastUS*.
7. Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte ve zbývajících krocích, dokud se oba virtuální počítače nevytvoří.

   ![Vytvoření virtuálního počítače](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalace služby IIS a přizpůsobení výchozí webové stránky

V této části nainstalujete server IIS na dva virtuální počítače *myIISVMEastUS* a *myIISVMWestEurope* a pak aktualizujete výchozí stránku webu. Na přizpůsobené webové stránce se zobrazí název virtuálního počítače, ke kterému se připojujete při prohlížení webu ve webovém prohlížeči.

1. V levé nabídce vyberte **Všechny prostředky** a pak v seznamu prostředků klikněte na *myIISVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** klikněte na **Připojit** a pak v části **Připojit k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**.
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.
6. Na ploše serveru přejděte na **Nástroje pro správu systému Windows** > **Správce serveru**.
7. Spusťte na virtuálním počítači VM1 Windows PowerShell a pomocí následujících příkazů nainstalujte server služby IIS a aktualizujte výchozí soubor htm.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![Instalace služby IIS a přizpůsobení webové stránky](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)
8. Ukončete připojení RDP k virtuálnímu počítači *myIISVMEastUS*.
9. Opakujte kroky 1-8 s vytvořením připojení RDP s *myIISVMWestEurope* virtuálního počítače ve skupině prostředků *myResourceGroupTM2* pro instalaci služby IIS a přizpůsobení její výchozí webové stránky.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurace názvů DNS pro virtuální počítače se službou IIS

Traffic Manager směruje provoz uživatelů na základě názvů DNS koncových bodů služby. V této části nakonfigurujete názvy DNS pro servery IIS – *myIISVMEastUS* a *myIISVMWestEurope*.

1. V levé nabídce klikněte na **Všechny prostředky** a pak v seznamu prostředků vyberte *myIISVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** v části **Název DNS** vyberte **Konfigurovat**.
3. Na stránce **Konfigurace** v části Popisek názvu DNS přidejte jedinečný název a pak vyberte **Uložit**.
4. Opakujte kroky 1-3 pro virtuální počítač s názvem *myIISVMWestEurope* , který je umístěný ve skupině prostředků *myResourceGroupTM2* .

### <a name="create-test-vms"></a>Vytvoření testovacích virtuálních počítačů

V této části vytvoříte virtuální počítač (*myVMEastUS* a *myVMWestEurope*) v každé oblasti Azure (**východní USA** a **západní Evropa**). Pomocí těchto virtuálních počítačů otestujete, jak Traffic Manager směruje provoz při prohlížení webu na nejbližší server služby IIS.

1. V levém horním rohu Azure Portal vyberte **vytvořit prostředek**  >  **COMPUTE**  >  **Windows Server 2019 Datacenter**.
2. V části **vytvořit virtuální počítač** zadejte nebo vyberte následující hodnoty na kartě **základy** :

   - **Předplatné**  >  **Skupina prostředků**: vyberte **myResourceGroupTM1**.
   - **Podrobnosti instance**  >  **Název virtuálního počítače**: zadejte *myVMEastUS*.
   - **Podrobnosti instance**  >  **Oblast**: vyberte **východní USA**.
   - **Účet správce**  >  **Uživatelské jméno**: zadejte uživatelské jméno, které chcete zvolit.
   - **Účet správce**  >  **Heslo**: zadejte heslo, které jste si zvolili. Heslo musí mít délku aspoň 12 znaků a musí splňovat [definované požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - Pravidla portů pro **příchozí spojení**  >  **Veřejné příchozí porty**: vyberte **Povolit vybrané porty**.
   - Pravidla portů pro **příchozí spojení**  >  **Vyberte příchozí porty**: v rozevíracím seznamu stáhnout vyberte **RDP** .

3. Vyberte kartu **Správa** nebo vyberte **Další: disky**, **Další: sítě** a **Další: Správa**. V části **monitorování** nastavte **diagnostiku spouštění** na **vypnuto**.
4. Vyberte **Zkontrolovat a vytvořit**.
5. Zkontrolujte nastavení a klikněte na **vytvořit**.  
6. Postupujte podle kroků a vytvořte druhý virtuální počítač s názvem *myVMWestEurope* s názvem **skupiny prostředků** *myResourceGroupTM2*, **umístěním** *západní Evropa* a všechna ostatní nastavení stejné jako *myVMEastUS*.
7. Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte ve zbývajících krocích, dokud se oba virtuální počítače nevytvoří.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

Vytvořte profil Traffic Manager, který směruje provoz uživatele odesláním do koncového bodu s nejnižší latencí.

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek**  >  **sítě**  >  **Traffic Manager profil**  >  **vytvořit**.
2. V části **Vytvořit profil služby Traffic Manager** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Název                   | Tento název musí být jedinečný v rámci zóny trafficmanager.net a ve výsledcích názvu DNS trafficmanager.net, který slouží k přístupu k vašemu profilu služby Traffic Manager.                                   |
    | Metoda směrování          | Vyberte metodu směrování **výkonu** .                                       |
    | Předplatné            | Vyberte své předplatné.                          |
    | Skupina prostředků          | Vyberte skupinu prostředků *myResourceGroupTM1*. |
    | Umístění                | Vyberte **USA – východ**. Toto nastavení se týká umístění skupiny prostředků a nemá žádný vliv na profil Traffic Manageru, který se nasadí globálně.                              |
    |

    ![Vytvoření profilu Traffic Manageru](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

Přidejte dva virtuální počítače se spuštěným serverem IIS – *myIISVMEastUS*  &  *myIISVMWestEurope* pro směrování provozu uživatelů do nejbližšího koncového bodu uživateli.

1. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který jste vytvořili v předchozí části, a pak tento profil služby Traffic Manager vyberte v zobrazených výsledcích.
2. V okně **Profil služby Traffic Manager** v části **Nastavení** klikněte na **Koncové body** a pak na **Přidat**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Typ                    | Koncový bod Azure                                   |
    | Name           | myEastUSEndpoint                                        |
    | Typ cílového prostředku           | Veřejná IP adresa                          |
    | Cílový prostředek          | **Zvolte veřejnou IP adresu** a zobrazí se výpis prostředků s veřejnými IP adresami ve stejném předplatném. Jako **Prostředek** vyberte veřejnou IP adresu *myIISVMEastUS-ip*. Toto je veřejná IP adresa virtuálního počítače se serverem služby IIS v oblasti USA – východ.|
    |        |           |

4. Opakujte kroky 2 a 3 a přidejte další koncový bod s názvem *myWestEuropeEndpoint* pro veřejnou IP adresu *myIISVMWestEurope-IP* , která je přidružená k VIRTUÁLNÍMU počítači serveru služby IIS s názvem *myIISVMWestEurope*.
5. Po přidání se oba koncové body zobrazí v části **Profil služby Traffic Manager** a jejich stav monitorování bude **Online**.

    ![Přidání koncového bodu služby Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)

## <a name="test-traffic-manager-profile"></a>Test profilu služby Traffic Manager

V této části otestujete, jak Traffic Manager směruje provoz uživatele na nejbližší virtuální počítače běžící na webu, aby poskytoval minimální latenci. Pokud chcete zobrazit službu Traffic Manager v akci, proveďte následující kroky:

1. Určete název DNS vašeho profilu služby Traffic Manager.
2. Službu Traffic Manager v akci zobrazíte následovně:
    - Z testovacího virtuálního počítače (*myVMEastUS*), který je umístěný v oblasti **východní USA** , přejděte ve webovém prohlížeči na název DNS vašeho profilu Traffic Manager.
    - Z testovacího virtuálního počítače (*myVMWestEurope*), který je umístěný v oblasti **západní Evropa** , přejděte ve webovém prohlížeči na název DNS vašeho profilu Traffic Manager.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Určení názvu DNS profilu služby Traffic Manager

V tomto kurzu pro zjednodušení k prohlížení webů použijete název DNS profilu služby Traffic Manager.

Název DNS profilu služby Traffic Manager můžete určit následujícím způsobem:

1. Na panelu hledání na portálu vyhledejte název **profilu Traffic Manager** , který jste vytvořili v předchozí části. Ve výsledcích, které se zobrazí, klikněte na profil služby Traffic Manager.
2. Klikněte na **Přehled**.
3. V části **Profil služby Traffic Manager** se zobrazí název DNS nově vytvořeného profilu služby Traffic Manager. V produkčních nasazeních pomocí záznamu DNS CNAME nakonfigurujete individuální název domény odkazující na název domény služby Traffic Manager.

   ![Název DNS služby Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Zobrazení služby Traffic Manager v akci

V této části uvidíte službu Traffic Manager v akci.

1. V levé nabídce vyberte **Všechny prostředky** a pak v seznamu prostředků klikněte na *myVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** klikněte na **Připojit** a pak v části **Připojit k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**.
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.
1. Do webového prohlížeče na virtuálním počítači *myVMEastUS* zadejte název DNS vašeho profilu služby Traffic Manager a zobrazte váš web. Vzhledem k tomu, že virtuální počítač umístěný v **východní USA**, budete přesměrováni na nejbližší web hostovaný na nejbližším serveru služby IIS *myIISVMEastUS* , který se nachází v **východní USA**.

   ![Snímek obrazovky, který zobrazuje profil "Traffic Manager" ve webovém prohlížeči.](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. V dalším kroku se připojte k virtuálnímu počítači *myVMWestEurope* , který se nachází v **Západní Evropa** pomocí kroků 1-5 a přejděte k názvu domény profilu Traffic Manager z tohoto virtuálního počítače. Vzhledem k tomu, že se virtuální počítač nachází v **západní Evropa**, jste nyní přesměrováni na web hostovaný na nejbližším serveru služby IIS *myIISVMWestEurope* , který je umístěn v **západní Evropa**.

   ![Test profilu služby Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už skupiny prostředků (**ResourceGroupTM1** a **ResourceGroupTM2**) nepotřebujete, odstraňte je. Uděláte to tak, že vyberte skupinu prostředků (**ResourceGroupTM1** nebo **ResourceGroupTM2**) a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Distribuce provozu do sady koncových bodů](traffic-manager-configure-weighted-routing-method.md)
