---
title: Výuka – zlepšení odezvy na webu pomocí Azure Traffic Manageru
description: Tento článek popisuje, jak vytvořit profil Traffic Manager vytvořit vysoce responzivní web.
services: traffic-manager
author: rohinkoul
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/23/2018
ms.author: rohink
ms.openlocfilehash: f7778b60a5e84f5d8900b8e37bfa655a7915d403
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77136392"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Kurz: Zlepšení odezvy webových stránek pomocí Traffic Manageru

Tento kurz popisuje, jak pomocí Traffic Manageru vytvořit vysoce responzivní web tím, že nasměrujeprovoz uživatelů na web s nejnižší latencí. Datové centrum s nejnižší latencí je obvykle ten, který je nejblíže v geografické vzdálenosti.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření dvou virtuálních počítačů se základním webem ve službě IIS
> * Vytvoření dvou testovacích virtuálních počítačů pro zobrazení služby Traffic Manager v akci
> * Konfigurace názvů DNS pro virtuální počítače se službou IIS
> * Vytvoření profilu Traffic Manageru pro zvýšení výkonu webu
> * Přidání koncových bodů virtuálních počítačů do profilu služby Traffic Manager
> * Zobrazení služby Traffic Manager v akci

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Aby bylo možné zobrazit službu Traffic Manager v akci, vyžaduje tento kurz, abyste nasadili:

- Dvě instance základních webů spuštěných v různých oblastech Azure – **východní USA** a **západní Evropa**.
- Dva testovací virtuální aplikace pro testování Traffic Manageru – jeden virtuální virtuální byl ve **východní části USA** a druhý virtuální v západní **Evropě**. Testovací virtuální servery slouží k ilustraci, jak Traffic Manager směruje provoz uživatelů na web, který běží ve stejné oblasti, protože poskytuje nejnižší latenci.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

### <a name="create-websites"></a>Vytvoření webů

V této části vytvoříte dvě instance webů, které zajistí dva požadované koncové body služby ve dvou oblastech Azure pro profil služby Traffic Manager. Vytvoření těchto dvou webů zahrnuje následující kroky:

1. Vytvoření dvou virtuálních počítačů pro provoz základního webu – jeden v oblasti **USA – východ** a druhý v oblasti **Západní Evropa**
2. Instalace serveru služby IIS na oba virtuální počítače a aktualizace výchozí webové stránky zobrazující název virtuálního počítače, ke kterému je uživatel při prohlížení webu připojený

#### <a name="create-vms-for-running-websites"></a>Vytvoření virtuálních počítačů pro provoz webů

V této části vytvoříte dva virtuální počítače *myIISVMEastUS* a *myIISVMWestEurope* v oblastech **Azure východní USA** a západní **Evropy.**

1. V levém horním rohu portálu Azure vyberte **Vytvořit prostředek** > **Compute** > **Windows Server 2019 Datacenter**.
2. Do **pole Vytvořit virtuální počítač**zadejte nebo vyberte na kartě **Základy** následující hodnoty:

   - **Skupina** > **prostředků odběru**: Vyberte **vytvořit nový** a zadejte **myResourceGroupTM1**.
   - **Podrobnosti instance** > **Název virtuálního počítače**: Zadejte *myIISVMEastUS*.
   - **Instance Details** > **Oblast**podrobností instance : Vyberte **možnost Východ USA**.
   - **Administrator Account** > **Uživatelské jméno**účtu správce : Zadejte uživatelské jméno podle vašeho výběru.
   - **Administrator Account** > **Heslo**účtu správce : Zadejte heslo podle vašeho výběru. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Vstupní** > vstupní porty Pravidla**příchozích portů**: Vyberte **Povolit vybrané porty**.
   - **Příchozí pravidla portu:** > **Select inbound ports**V rozbalovacím poli vyberte **protokol RDP** a **HTTP.**

3. Vyberte kartu **Správa** nebo **další: Disky**, pak **Další: Síť**, pak **Další: Správa**. V části **Sledování**nastavte **diagnostiku spouštění** na **Vypnuto**.
4. Vyberte **Zkontrolovat a vytvořit**.
5. Zkontrolujte nastavení a klepněte na tlačítko **Vytvořit**.  
6. Postupujte podle pokynů k vytvoření druhého virtuálního počítače s názvem *myIISVMWestEurope*, s názvem **skupiny prostředků** *myResourceGroupTM2*, **umístění** *západní Evropy*a všechna ostatní nastavení stejná jako *myIISVMEastUS*.
7. Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte ve zbývajících krocích, dokud se oba virtuální počítače nevytvoří.

   ![Vytvoření virtuálního počítače](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalace služby IIS a přizpůsobení výchozí webové stránky

V této části nainstalujete server Služby IIS na dva virtuální my *myIISVMEastUS* a *myIISVMWestEurope*a potom aktualizujte výchozí stránku webu. Na přizpůsobené webové stránce se zobrazí název virtuálního počítače, ke kterému se připojujete při prohlížení webu ve webovém prohlížeči.

1. V levé nabídce vyberte **Všechny prostředky** a pak v seznamu prostředků klikněte na *myIISVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** klikněte na **Připojit** a pak v části **Připojit k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**.
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.
6. Na ploše serveru přejděte na server Správce nástrojů> **pro správu systému Windows**.**Server Manager**
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
9. Opakujte kroky 1-8 vytvořením připojení RDP s vm *myIISVMWestEurope* v rámci skupiny prostředků *myResourceGroupTM2* k instalaci služby IIS a přizpůsobení výchozí webové stránky.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurace názvů DNS pro virtuální počítače se službou IIS

Traffic Manager směruje provoz uživatelů na základě názvů DNS koncových bodů služby. V této části nakonfigurujete názvy DNS pro servery služby IIS – *myIISVMEastUS* a *myIISVMWestEurope*.

1. V levé nabídce klikněte na **Všechny prostředky** a pak v seznamu prostředků vyberte *myIISVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** v části **Název DNS** vyberte **Konfigurovat**.
3. Na stránce **Konfigurace** v části Popisek názvu DNS přidejte jedinečný název a pak vyberte **Uložit**.
4. Opakujte kroky 1-3 pro virtuální ho s názvem *myIISVMWestEurope,* který se nachází ve skupině prostředků *myResourceGroupTM2.*

### <a name="create-test-vms"></a>Vytvoření testovacích virtuálních počítačů

V této části vytvoříte virtuální počítač *(myVMEastUS* a *myVMWestEurope)* v každé oblasti Azure **(východní USA** a **západní Evropa).** Pomocí těchto virtuálních počítačů otestujete, jak Traffic Manager směruje provoz při prohlížení webu na nejbližší server služby IIS.

1. V levém horním rohu portálu Azure vyberte **Vytvořit prostředek** > **Compute** > **Windows Server 2019 Datacenter**.
2. Do **pole Vytvořit virtuální počítač**zadejte nebo vyberte na kartě **Základy** následující hodnoty:

   - **Subscription** > **Skupina prostředků předplatného**: Vyberte **myResourceGroupTM1**.
   - **Podrobnosti instance** > **Název virtuálního počítače**: Zadejte *myVMEastUS*.
   - **Instance Details** > **Oblast**podrobností instance : Vyberte **možnost Východ USA**.
   - **Administrator Account** > **Uživatelské jméno**účtu správce : Zadejte uživatelské jméno podle vašeho výběru.
   - **Administrator Account** > **Heslo**účtu správce : Zadejte heslo podle vašeho výběru. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Vstupní** > vstupní porty Pravidla**příchozích portů**: Vyberte **Povolit vybrané porty**.
   - **Příchozí pravidla portu:** > **Select inbound ports**Vyberte v rozbalovacím poli **možnost IP.**

3. Vyberte kartu **Správa** nebo **další: Disky**, pak **Další: Síť**, pak **Další: Správa**. V části **Sledování**nastavte **diagnostiku spouštění** na **Vypnuto**.
4. Vyberte **Zkontrolovat a vytvořit**.
5. Zkontrolujte nastavení a klepněte na tlačítko **Vytvořit**.  
6. Postupujte podle pokynů k vytvoření druhého virtuálního počítače s názvem *myVMWestEurope*, s názvem **skupiny prostředků** *myResourceGroupTM2*, **umístění** *západní Evropy*a všechna ostatní nastavení stejná jako *myVMEastUS*.
7. Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte ve zbývajících krocích, dokud se oba virtuální počítače nevytvoří.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

Vytvořte profil Traffic Manager, který směruje provoz uživatelů jejich odesláním do koncového bodu s nejnižší latencí.

1. V levém horním rohu obrazovky vyberte Vytvořit**profil** >  **správce** > provozu**sítě** > **Vytvořit**.
2. V části **Vytvořit profil služby Traffic Manager** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Name (Název)                   | Tento název musí být jedinečný v rámci zóny trafficmanager.net a ve výsledcích názvu DNS trafficmanager.net, který slouží k přístupu k vašemu profilu služby Traffic Manager.                                   |
    | Metoda směrování          | Vyberte metodu směrování **výkonu.**                                       |
    | Předplatné            | Vyberte své předplatné.                          |
    | Skupina prostředků          | Vyberte skupinu prostředků *myResourceGroupTM1*. |
    | Umístění                | Vyberte **USA – východ**. Toto nastavení se týká umístění skupiny prostředků a nemá žádný vliv na profil Traffic Manageru, který se nasadí globálně.                              |
    |

    ![Vytvoření profilu Traffic Manageru](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

Přidejte dva virtuální servery se servery IIS – *myIISVMEastUS* & *myIISVMWestEurope,* které směrují provoz uživatelů do nejbližšího koncového bodu k uživateli.

1. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který jste vytvořili v předchozí části, a pak tento profil služby Traffic Manager vyberte v zobrazených výsledcích.
2. V okně **Profil služby Traffic Manager** v části **Nastavení** klikněte na **Koncové body** a pak na **Přidat**.
3. Zadejte nebo vyberte následující informace, přijměte výchozí hodnoty zbývajících nastavení a pak vyberte **OK**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Typ                    | Koncový bod Azure                                   |
    | Name (Název)           | myEastUSEndpoint                                        |
    | Typ cílového prostředku           | Veřejná IP adresa                          |
    | Cílový prostředek          | **Zvolte veřejnou IP adresu** a zobrazí se výpis prostředků s veřejnými IP adresami ve stejném předplatném. Jako **Prostředek** vyberte veřejnou IP adresu *myIISVMEastUS-ip*. Toto je veřejná IP adresa virtuálního počítače se serverem služby IIS v oblasti USA – východ.|
    |        |           |

4. Opakováním kroků 2 a 3 přidejte další koncový bod s názvem *myWestEuropeEndpoint* pro veřejnou IP adresu *myIISVMWestEurope-ip,* která je přidružena k virtuálnímu virtuálnímu ms serveru IIS s názvem *myIISVMWestEurope*.
5. Po přidání se oba koncové body zobrazí v části **Profil služby Traffic Manager** a jejich stav monitorování bude **Online**.

    ![Přidání koncového bodu služby Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)

## <a name="test-traffic-manager-profile"></a>Test profilu služby Traffic Manager

V této části otestujete, jak Traffic Manager směruje provoz uživatelů na nejbližší virtuální uživatele se systémem webu, aby poskytoval minimální latenci. Pokud chcete zobrazit službu Traffic Manager v akci, proveďte následující kroky:

1. Určete název DNS vašeho profilu služby Traffic Manager.
2. Službu Traffic Manager v akci zobrazíte následovně:
    - Z testovacího virtuálního počítače *(myVMEastUS),* který se nachází v oblasti **Usa – východ,** ve webovém prohlížeči vyhledejte název DNS profilu traffic manageru.
    - Z testovacího virtuálního počítače *(myVMWestEurope),* který se nachází v oblasti **Západní Evropa,** ve webovém prohlížeči přejděte na název DNS profilu Traffic Manageru.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Určení názvu DNS profilu služby Traffic Manager

V tomto kurzu pro zjednodušení k prohlížení webů použijete název DNS profilu služby Traffic Manager.

Název DNS profilu služby Traffic Manager můžete určit následujícím způsobem:

1. Na vyhledávacím panelu portálu vyhledejte název **profilu Traffic Manageru,** který jste vytvořili v předchozí části. Ve výsledcích, které se zobrazí, klikněte na profil služby Traffic Manager.
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
1. Do webového prohlížeče na virtuálním počítači *myVMEastUS* zadejte název DNS vašeho profilu služby Traffic Manager a zobrazte váš web. Vzhledem k tomu, že virtuální počítač umístěný ve **východní části USA**, budete směrováni na nejbližší web hostovaný na nejbližším serveru IIS *myIISVMEastUS,* který se nachází ve **východní části USA**.

   ![Test profilu služby Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Dále se připojte k virtuálnímu virtuálnímu zařízení *myVMWestEurope umístěnému* v **západní Evropě** pomocí kroků 1-5 a přejděte na název domény profilu Traffic Manageru z tohoto virtuálního aplikace. Vzhledem k tomu, že virtuální počítač umístěný v **západní Evropě**, jste nyní směrováni na webovou stránku hostovovnu na nejbližším serveru *IIS myIISVMWestEurope,* který se nachází v **západní Evropě**.

   ![Test profilu služby Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)

## <a name="delete-the-traffic-manager-profile"></a>Odstranění profilu služby Traffic Manager

Pokud už skupiny prostředků (**ResourceGroupTM1** a **ResourceGroupTM2**) nepotřebujete, odstraňte je. Uděláte to tak, že vyberte skupinu prostředků (**ResourceGroupTM1** nebo **ResourceGroupTM2**) a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Distribuce provozu do sady koncových bodů](traffic-manager-configure-weighted-routing-method.md)
