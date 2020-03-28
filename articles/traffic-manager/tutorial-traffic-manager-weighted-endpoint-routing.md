---
title: Kurz:Směrování provozu do vážených koncových bodů – Azure Traffic Manager
description: Tento kurz popisuje, jak pomocí služby Traffic Manager směrovat provoz do vážených koncových bodů.
services: traffic-manager
author: rohinkoul
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: rohink
ms.openlocfilehash: a4738b2e36786cd627f53af3e36bd8f1e3fbc375
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76939482"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-by-using-traffic-manager"></a>Kurz: Řízení směrování provozu s váženými koncovými body pomocí služby Traffic Manager

Tento kurz popisuje, jak pomocí služby Azure Traffic Manager řídit směrování uživatelského provozu mezi koncovými body s využitím metody váženého směrování. V této metodě směrování každému koncovému bodu přiřadíte váhu v konfiguraci profilu služby Traffic Manager. Uživatelský provoz se pak bude směrovat na základě váhy přiřazené jednotlivým koncovým bodům. Váha je celé číslo od 1 do 1 000. Čím vyšší je hodnota váhy přiřazená ke koncovému bodu, tím vyšší má prioritu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření dvou virtuálních počítačů se základním webem ve službě IIS
> * Vytvoření dvou testovacích virtuálních počítačů pro zobrazení služby Traffic Manager v akci
> * Konfigurace názvů DNS pro virtuální počítače se službou IIS
> * Vytvoření profilu služby Traffic Manager
> * Přidání koncových bodů virtuálních počítačů do profilu služby Traffic Manager
> * Zobrazte službu Traffic Manager v akci.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Pokud chcete zobrazit službu Traffic Manager v akci, nasaďte pro účely tohoto kurzu následující:

- Dvě instance základních webů spuštěné v různých oblastech Azure: USA – východ a Západní Evropa
- Dva testovací virtuální počítače pro testování služby Traffic Manager: jeden v oblasti USA – východ a druhý v oblasti Západní Evropa Testovací virtuální počítače slouží k předvedení způsobu, jakým Traffic Manager směruje uživatelský provoz na web, jehož koncový bod má přiřazenou vyšší váhu.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [portálu Azure](https://portal.azure.com).

### <a name="create-websites"></a>Vytvoření webů

V této části vytvoříte dvě instance webů, které zajistí dva požadované koncové body služby ve dvou oblastech Azure pro profil služby Traffic Manager. Tyto dva weby vytvoříte provedením následujících kroků:

1. Vytvořte dva virtuální počítače pro provoz základního webu: jeden v oblasti USA – východ a druhý v oblasti Západní Evropa.
2. Na oba virtuální počítače nainstalujte server služby IIS. Aktualizujte výchozí webovou stránku zobrazující název virtuálního počítače, ke kterému je uživatel při prohlížení webu připojený.

#### <a name="create-vms-for-running-websites"></a>Vytvoření virtuálních počítačů pro provoz webů

V této části vytvoříte dva virtuální počítače *(myIISVMEastUS* a *myIISVMWestEurope)* v oblastech Azure – východní USA a západní Evropa).

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

#### <a name="install-iis-and-customize-the-default-webpage"></a>Instalace služby IIS a přizpůsobení výchozí webové stránky

V této části nainstalujete server Služby IIS na dva virtuální maze myIISVMEastUS a myIISVMWestEurope a potom aktualizujte výchozí webovou stránku. Na přizpůsobené webové stránce se zobrazí název virtuálního počítače, ke kterému se připojujete při prohlížení webu ve webovém prohlížeči.

1. V nabídce vlevo vyberte **Všechny prostředky**. Ze seznamu prostředků vyberte **myIISVMEastUS** ve skupině prostředků **myResourceGroupTM1**.
2. Na stránce **Přehled** vyberte **Připojit**. V části **Připojení k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**.
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná budete muset vybrat **Další volby:** > K zadání pověření, která jste zadali při vytváření virtuálního vztahu,**můžete zadat jiný účet**.
4. Vyberte **OK**.
5. Při přihlášení se může zobrazit upozornění na certifikát. Pokud se zobrazí upozornění, vyberte **ano** nebo **Pokračovat** pokračovat v připojení.
6. Na ploše serveru přejděte na webu Správce serveru Nástrojů >  **pro správu systému Windows**.**Server Manager**
7. Na prvním virtuálním počítači otevřete Windows PowerShell. Pomocí následujících příkazů nainstalujte server služby IIS a aktualizujte výchozí soubor .htm.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default .htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom .htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

    ![Instalace služby IIS a přizpůsobení webové stránky](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)

8. Ukončete připojení RDP k virtuálnímu počítači **myIISVMEastUS**.
9. Zopakujte kroky 1 až 8. Vytvořte připojení RDP s virtuálním jazykem **myIISVMWestEurope** v rámci skupiny prostředků **myResourceGroupTM2,** chcete-li nainstalovat službu IIS a přizpůsobit výchozí webovou stránku.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurace názvů DNS pro virtuální počítače se službou IIS

Traffic Manager směruje uživatelský provoz na základě názvů DNS koncových bodů služby. V této části nakonfigurujete názvy DNS pro servery služby IIS myIISVMEastUS a myIISVMWestEurope.

1. V nabídce vlevo vyberte **Všechny prostředky**. Ze seznamu prostředků vyberte **myIISVMEastUS** ve skupině prostředků **myResourceGroupTM1**.
2. Na stránce **Přehled** v části **Název DNS** vyberte **Konfigurovat**.
3. Na stránce **Konfigurace** v části Popisek názvu DNS přidejte jedinečný název. Potom vyberte **Uložit**.
4. Opakujte kroky 1-3 pro virtuální hosti s názvem **myIISVMWestEurope** ve skupině prostředků **myResourceGroupTM2.**

### <a name="create-a-test-vm"></a>Vytvoření testovacího virtuálního počítače

V této části vytvoříte virtuální počítač *(myVMEastUS* a *myVMWestEurope)* v každé oblasti Azure **(východní USA** a **západní Evropa).** Tyto virtuální stránky se použijí k testování toho, jak Traffic Manager směruje provoz do koncového bodu webu, který má vyšší hodnotu hmotnosti.

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

Vytvořte profil služby Traffic Manager založený na metodě **váženého** směrování.

1. Na levé horní straně obrazovky vyberte Vytvořit**profil** >  **správce** > provozu**sítě** > **vytvořit**.
2. V části **Vytvořit profil služby Traffic Manager** zadejte nebo vyberte následující informace. Přijměte výchozí hodnoty pro ostatní nastavení a pak vyberte **Vytvořit**.

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Name (Název)                   | Zadejte jedinečný název v rámci zóny trafficmanager.net. Výsledkem bude název DNS trafficmanager.net, který bude sloužit k přístupu k vašemu profilu služby Traffic Manager.                                   |
    | Metoda směrování          | Vyberte metodu **váženého** směrování.                                       |
    | Předplatné            | Vyberte své předplatné.                          |
    | Skupina prostředků          | Vyberte **Použít existující** a pak vyberte **myResourceGroupTM1**. |
    |        |   |

    ![Vytvoření profilu Traffic Manageru](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

Přidejte dva virtuální servery se servery IIS, které jsou servery iis iisaeastUS a myIISVMWestEurope, a směrujte na ně uživatelský provoz.

1. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který jste vytvořili v předchozí části. Ve výsledcích, které se zobrazí, vyberte profil.
2. V okně **Profil služby Traffic Manager** v části **Nastavení** vyberte **Koncové body** > **Přidat**.
3. Zadejte nebo vyberte následující informace. U ostatních nastavení přijměte výchozí hodnoty a pak vyberte **OK**.

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Typ                    | Zadejte koncový bod Azure.                                   |
    | Name (Název)           | Zadejte **myEastUSEndpoint**.                                        |
    | Typ cílového prostředku           | Vyberte **Veřejná IP adresa**.                          |
    | Cílový prostředek          | Zvolte veřejnou IP adresu a zobrazí se výpis prostředků s veřejnými IP adresami ve stejném předplatném. Jako **Prostředek** vyberte veřejnou IP adresu **myIISVMEastUS-ip**. Toto je veřejná IP adresa virtuálního počítače se serverem služby IIS v oblasti USA – východ.|
    |  Hmotnost      | Zadejte **100**.        |
    |        |           |

4. Opakováním kroků 2 a 3 přidáte další koncový bod s názvem **myWestEuropeEndpoint** pro veřejnou IP adresu **myIISVMWestEurope-ip**. Tato adresa je přidružena k virtuálnímu virtuálnímu ms serveru IIS s názvem myIISVMWestEurope. Jako **Váha** zadejte **25**.
5. Po přidání se oba koncové body zobrazí v profilu služby Traffic Manager a jejich stav monitorování bude **Online**.

## <a name="test-the-traffic-manager-profile"></a>Test profilu služby Traffic Manager

Pokud chcete zobrazit službu Traffic Manager v akci, proveďte následující kroky:

1. Určete název DNS vašeho profilu služby Traffic Manager.
2. Zobrazte službu Traffic Manager v akci.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Určení názvu DNS profilu služby Traffic Manager

V tomto kurzu pro zjednodušení k prohlížení webů použijete název DNS profilu služby Traffic Manager.

Název DNS profilu služby Traffic Manager můžete určit následujícím způsobem:

1. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který jste vytvořili v předchozí části. Ve výsledcích, které se zobrazí, vyberte profil služby Traffic Manager.
2. Vyberte **Přehled**.
3. V profilu služby Traffic Manager se zobrazí jeho název DNS. V produkčních nasazeních pomocí záznamu DNS CNAME nakonfigurujete individuální název domény odkazující na název domény služby Traffic Manager.

   ![Název DNS služby Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Zobrazení služby Traffic Manager v akci

V této části uvidíte službu Traffic Manager v akci.

1. V nabídce vlevo vyberte **Všechny prostředky**. Ze seznamu prostředků vyberte **myVMEastUS** ve skupině prostředků **myResourceGroupTM1**.
2. Na stránce **Přehled** vyberte **Připojit**. V části **Připojení k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**.
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná budete muset vybrat **Další volby:** > K zadání pověření, která jste zadali při vytváření virtuálního vztahu,**můžete zadat jiný účet**.
4. Vyberte **OK**.
5. Při přihlášení se může zobrazit upozornění na certifikát. Pokud se zobrazí upozornění, vyberte **ano** nebo **Pokračovat** pokračovat v připojení.
6. Do webového prohlížeče na virtuálním počítači myVMEastUS zadejte název DNS vašeho profilu služby Traffic Manager a zobrazte váš web. Budete přesměrováni na web hostovaný na serveru služby IIS myIISVMEastUS, protože má přiřazenou vyšší váhu s hodnotou **100**. Serveru IIS myIISVMWestEurope je přiřazena nižší hodnota hmotnosti koncového bodu **25**.

   ![Test profilu služby Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

7. Opakujte kroky 1-6 na vozíčkovaného myVMWestEurope zobrazíte váženou odezvu webu.

## <a name="delete-the-traffic-manager-profile"></a>Odstranění profilu služby Traffic Manager

Pokud už skupiny prostředků vytvořené v tomto kurzu nepotřebujete, můžete je odstranit. Uděláte to tak, že vyberte skupinu prostředků (**ResourceGroupTM1** nebo **ResourceGroupTM2**) a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Směrování provozu do konkrétních koncových bodů na základě zeměpisného umístění uživatele](traffic-manager-configure-geographic-routing-method.md)
