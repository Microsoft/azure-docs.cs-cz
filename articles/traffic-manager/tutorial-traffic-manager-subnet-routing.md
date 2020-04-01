---
title: Kurz – Konfigurace směrování provozu podsítě pomocí Azure Traffic Manageru
description: Tento kurz vysvětluje, jak nakonfigurovat Traffic Manager pro směrování provozu z podsítí uživatelů do konkrétních koncových bodů.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: rohink
ms.openlocfilehash: 49e0bce6eea8fac32f49bb905c225e898e709af0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77136289"
---
# <a name="tutorial-direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Kurz: Přímý přenos na konkrétní koncové body na základě podsítě uživatele pomocí Traffic Manageru

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

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Aby bylo možné zobrazit službu Traffic Manager v akci, vyžaduje tento kurz, abyste nasadili:

- dva základní weby spuštěné v různých oblastech Azure – **USA – východ** (slouží jako interní web) a **Západní Evropa** (slouží jako produkční web)
- dva testovací virtuální počítače pro testování služby Traffic Manager – jeden virtuální počítač v oblasti **USA – východ** a druhý v oblasti **Západní Evropa**

Testovací virtuální počítače slouží k ilustraci způsobu, jakým Traffic Manager směruje provoz uživatelů na interní nebo produkční web v závislosti na podsíti, ze které dotaz uživatele pochází.

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

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalace služby IIS a přizpůsobení výchozí webové stránky

V této části nainstalujete server Služby IIS na dva virtuální počítače – *myIISVMEastUS* & *myIISVMWestEurope*a potom aktualizujte výchozí stránku webu. Na přizpůsobené webové stránce se zobrazí název virtuálního počítače, ke kterému se připojujete při prohlížení webu ve webovém prohlížeči.

1. V levé nabídce vyberte **Všechny prostředky** a pak v seznamu prostředků klikněte na *myIISVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** klikněte na **Připojit** a pak v části **Připojit k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**.
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.
6. Na ploše serveru přejděte na server Správce nástrojů> **pro správu systému Windows**.**Server Manager**
7. Spusťte prostředí Windows PowerShell ve službě VM *myIISVMEastUS*a pomocí následujících příkazů nainstalujte server Služby IIS a aktualizujte výchozí soubor HTM.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. Zavřete připojení RDP pomocí virtuálního virtuálního mm *myIISVMEastUS.*
9. Opakujte kroky 1-6 vytvořením připojení RDP s vm *myIISVMWestEurope* v rámci skupiny prostředků *myResourceGroupTM2* k instalaci služby IIS a přizpůsobení výchozí webové stránky.
10. Spusťte prostředí Windows PowerShell na virtuálním *počítači myIISVMWestEurope* a pomocí následujících příkazů nainstalujte server Služby IIS a aktualizujte výchozí soubor HTM.

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
4. Opakujte kroky 1-3 pro virtuální ho s názvem *myIISVMWestEurope,* který se nachází ve skupině prostředků *myResourceGroupTM2.*

### <a name="create-test-vms"></a>Vytvoření testovacích virtuálních počítačů

V této části vytvoříte virtuální počítač *(myVMEastUS* a *myVMWestEurope)* v každé oblasti Azure **(východní USA** a **západní Evropa).** Tyto virtuální aplikace se použijí k testování způsobu, jakým Traffic Manager směruje provoz uživatelů na základě podsítě dotazu uživatele.

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

    ![Vytvoření profilu Traffic Manageru](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

Přidejte dva virtuální servery se servery IIS – *myIISVMEastUS* & *myIISVMWestEurope* pro směrování uživatelských přenosů na základě podsítě dotazu uživatele.

1. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který jste vytvořili v předchozí části, a pak tento profil služby Traffic Manager vyberte v zobrazených výsledcích.
2. V okně **Profil služby Traffic Manager** v části **Nastavení** klikněte na **Koncové body** a pak na **Přidat**.
3. Zadejte nebo vyberte následující informace, přijměte výchozí hodnoty zbývajících nastavení a pak vyberte **OK**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Typ                    | Koncový bod Azure                                   |
    | Name (Název)           | myInternalWebSiteEndpoint                                        |
    | Typ cílového prostředku           | Veřejná IP adresa                          |
    | Cílový prostředek          | **Zvolte veřejnou IP adresu** a zobrazí se výpis prostředků s veřejnými IP adresami ve stejném předplatném. Jako **Prostředek** vyberte veřejnou IP adresu *myIISVMEastUS-ip*. Toto je veřejná IP adresa virtuálního počítače se serverem služby IIS v oblasti USA – východ.|
    |  Nastavení směrování podsítě    |   Přidejte IP adresu testovacího virtuálního počítačů *myVMEastUS.* Všechny uživatelské dotazy pocházející z tohoto virtuálního aplikace budou přesměrovány na *myInternalWebSiteEndpoint*.    |

4. Opakováním kroků 2 a 3 přidejte další koncový bod s názvem *myProdWebsiteEndpoint* pro veřejnou IP adresu *myIISVMWestEurope-ip,* která je přidružena k virtuálnímu virtuálnímu ms serveru IIS s názvem *myIISVMWestEurope*. Pro **nastavení směrování podsítě**přidejte IP adresu testovacího virtuálního počítače - *myVMWestEurope*. Všechny dotazy uživatelů z tohoto testovacího virtuálního počítače se budou směrovat do koncového bodu *myProdWebsiteEndpoint*.
5. Po přidání se oba koncové body zobrazí v části **Profil služby Traffic Manager** a jejich stav monitorování bude **Online**.

## <a name="test-traffic-manager-profile"></a>Test profilu služby Traffic Manager

V této části otestujete, jak Traffic Manager směruje provoz uživatelů z dané podsítě do konkrétního koncového bodu. Pokud chcete zobrazit službu Traffic Manager v akci, proveďte následující kroky:

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

### <a name="view-traffic-manager-in-action"></a>Zobrazení služby Traffic Manager v akci

V této části uvidíte službu Traffic Manager v akci.

1. V levé nabídce vyberte **Všechny prostředky** a pak v seznamu prostředků klikněte na *myVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** klikněte na **Připojit** a pak v části **Připojit k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**.
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.
6. Do webového prohlížeče na virtuálním počítači *myVMEastUS* zadejte název DNS vašeho profilu služby Traffic Manager a zobrazte váš web. Vzhledem k tomu, že IP adresa VM *myVMEastUS* je spojena s koncovým bodem *myInternalWebsiteEndpoint*, webový prohlížeč spouští server webových stránek Test - *myIISVMEastUS*.

7. Dále se připojte k virtuálnímu virtuálnímu zařízení *myVMWestEurope umístěnému* v **západní Evropě** pomocí kroků 1-5 a přejděte na název domény profilu Traffic Manageru z tohoto virtuálního aplikace. Vzhledem k tomu, že IP adresa VM *myVMWestEurope* je spojena s koncovým bodem *myProductionWebsiteEndpoint*, webový prohlížeč spouští server webových stránek Test - *myIISVMWestEurope*.

## <a name="delete-the-traffic-manager-profile"></a>Odstranění profilu služby Traffic Manager

Pokud už skupiny prostředků (**ResourceGroupTM1** a **ResourceGroupTM2**) nepotřebujete, odstraňte je. Uděláte to tak, že vyberte skupinu prostředků (**ResourceGroupTM1** nebo **ResourceGroupTM2**) a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [metodě váženého směrování provozu](traffic-manager-configure-weighted-routing-method.md).
- Přečtěte si o [metodě prioritního směrování](traffic-manager-configure-priority-routing-method.md).
- Přečtěte si o [metodě geografického směrování](traffic-manager-configure-geographic-routing-method.md).
