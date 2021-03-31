---
title: 'Kurz: směrování provozu do vážených koncových bodů – Azure Traffic Manager'
description: Tento kurz popisuje, jak pomocí služby Traffic Manager směrovat provoz do vážených koncových bodů.
services: traffic-manager
author: duongau
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: 55c316a370b9e44e906e48b4716201384567c9c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003782"
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

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

Pokud chcete zobrazit službu Traffic Manager v akci, nasaďte pro účely tohoto kurzu následující:

- Dvě instance základních webů spuštěné v různých oblastech Azure: USA – východ a Západní Evropa
- Dva testovací virtuální počítače pro testování služby Traffic Manager: jeden v oblasti USA – východ a druhý v oblasti Západní Evropa Testovací virtuální počítače slouží k předvedení způsobu, jakým Traffic Manager směruje uživatelský provoz na web, jehož koncový bod má přiřazenou vyšší váhu.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

### <a name="create-websites"></a>Vytvoření webů

V této části vytvoříte dvě instance webů, které zajistí dva požadované koncové body služby ve dvou oblastech Azure pro profil služby Traffic Manager. Tyto dva weby vytvoříte provedením následujících kroků:

1. Vytvořte dva virtuální počítače pro provoz základního webu: jeden v oblasti USA – východ a druhý v oblasti Západní Evropa.
2. Na oba virtuální počítače nainstalujte server služby IIS. Aktualizujte výchozí webovou stránku zobrazující název virtuálního počítače, ke kterému je uživatel při prohlížení webu připojený.

#### <a name="create-vms-for-running-websites"></a>Vytvoření virtuálních počítačů pro provoz webů

V této části vytvoříte dva virtuální počítače (*myIISVMEastUS* a *myIISVMWestEurope*) ve východní USA a západní Evropa oblasti Azure.

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

#### <a name="install-iis-and-customize-the-default-webpage"></a>Instalace služby IIS a přizpůsobení výchozí webové stránky

V této části nainstalujete server IIS na dva virtuální počítače myIISVMEastUS a myIISVMWestEurope a pak aktualizujete výchozí webovou stránku. Na přizpůsobené webové stránce se zobrazí název virtuálního počítače, ke kterému se připojujete při prohlížení webu ve webovém prohlížeči.

1. V nabídce vlevo vyberte **Všechny prostředky**. Ze seznamu prostředků vyberte **myIISVMEastUS** ve skupině prostředků **myResourceGroupTM1**.
2. Na stránce **Přehled** vyberte **Připojit**. V části **Připojení k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**.
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná budete muset vybrat **Další volby**  >  **použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.
4. Vyberte **OK**.
5. Při přihlášení se může zobrazit upozornění na certifikát. Pokud se zobrazí upozornění, vyberte **Ano** nebo **pokračovat** a pokračujte v připojování.
6. Na ploše serveru přejděte na **Nástroje pro správu systému Windows**  >  **Správce serveru**.
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
9. Zopakujte kroky 1 až 8. Vytvořte připojení RDP s **myIISVMWestEurope** virtuálního počítače ve skupině prostředků **myResourceGroupTM2** , abyste nainstalovali službu IIS a přizpůsobili její výchozí webovou stránku.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurace názvů DNS pro virtuální počítače se službou IIS

Traffic Manager směruje uživatelský provoz na základě názvů DNS koncových bodů služby. V této části nakonfigurujete názvy DNS pro servery IIS myIISVMEastUS a myIISVMWestEurope.

1. V nabídce vlevo vyberte **Všechny prostředky**. Ze seznamu prostředků vyberte **myIISVMEastUS** ve skupině prostředků **myResourceGroupTM1**.
2. Na stránce **Přehled** v části **Název DNS** vyberte **Konfigurovat**.
3. Na stránce **Konfigurace** v části Popisek názvu DNS přidejte jedinečný název. Pak vyberte **Uložit**.
4. Opakujte kroky 1-3 pro virtuální počítač s názvem **myIISVMWestEurope** ve skupině prostředků **myResourceGroupTM2** .

### <a name="create-a-test-vm"></a>Vytvoření testovacího virtuálního počítače

V této části vytvoříte virtuální počítač (*myVMEastUS* a *myVMWestEurope*) v každé oblasti Azure (**východní USA** a **západní Evropa**). Tyto virtuální počítače použijete k otestování způsobu, jakým Traffic Manager směrují provoz na koncový bod webu s vyšší hodnotou váhy.

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

Vytvořte profil služby Traffic Manager založený na metodě **váženého** směrování.

1. V levé horní části obrazovky vyberte **vytvořit prostředek**  >  **sítě**  >  **Traffic Manager profil**  >  **vytvořit**.
2. V části **Vytvořit profil služby Traffic Manager** zadejte nebo vyberte následující informace. Přijměte výchozí hodnoty pro ostatní nastavení a pak vyberte **vytvořit**.

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Název                   | Zadejte jedinečný název v rámci zóny trafficmanager.net. Výsledkem bude název DNS trafficmanager.net, který bude sloužit k přístupu k vašemu profilu služby Traffic Manager.                                   |
    | Metoda směrování          | Vyberte metodu **váženého** směrování.                                       |
    | Předplatné            | Vyberte své předplatné.                          |
    | Skupina prostředků          | Vyberte **Použít existující** a pak vyberte **myResourceGroupTM1**. |
    |        |   |

    ![Vytvoření profilu Traffic Manageru](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

Přidejte dva virtuální počítače, na kterých běží servery IIS myIISVMEastUS a myIISVMWestEurope, abyste na ně mohli směrovat uživatelský provoz.

1. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který jste vytvořili v předchozí části. Ve výsledcích, které se zobrazí, vyberte profil.
2. V okně **Profil služby Traffic Manager** v části **Nastavení** vyberte **Koncové body** > **Přidat**.
3. Zadejte nebo vyberte následující informace. U ostatních nastavení přijměte výchozí hodnoty a pak vyberte **OK**.

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Typ                    | Zadejte koncový bod Azure.                                   |
    | Name           | Zadejte **myEastUSEndpoint**.                                        |
    | Typ cílového prostředku           | Vyberte **Veřejná IP adresa**.                          |
    | Cílový prostředek          | Zvolte veřejnou IP adresu a zobrazí se výpis prostředků s veřejnými IP adresami ve stejném předplatném. Jako **Prostředek** vyberte veřejnou IP adresu **myIISVMEastUS-ip**. Toto je veřejná IP adresa virtuálního počítače se serverem služby IIS v oblasti USA – východ.|
    |  Hmotnost      | Zadejte **100**.        |
    |        |           |

4. Opakujte kroky 2 a 3 a přidejte další koncový bod s názvem **myWestEuropeEndpoint** pro veřejnou IP adresu **myIISVMWestEurope-IP**. Tato adresa je přidružená k virtuálnímu počítači serveru služby IIS s názvem myIISVMWestEurope. Jako **Váha** zadejte **25**.
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
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná budete muset vybrat **Další volby**  >  **použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.
4. Vyberte **OK**.
5. Při přihlášení se může zobrazit upozornění na certifikát. Pokud se zobrazí upozornění, vyberte **Ano** nebo **pokračovat** a pokračujte v připojování.
6. Do webového prohlížeče na virtuálním počítači myVMEastUS zadejte název DNS vašeho profilu služby Traffic Manager a zobrazte váš web. Budete přesměrováni na web hostovaný na serveru služby IIS myIISVMEastUS, protože má přiřazenou vyšší váhu s hodnotou **100**. MyIISVMWestEurope serveru služby IIS je přiřazena nižší váha koncového bodu s hodnotou **25**.

   ![Test profilu služby Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

7. Opakujte kroky 1-6 na virtuálním počítači myVMWestEurope a podívejte se na váženou odpověď webu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už skupiny prostředků vytvořené v tomto kurzu nepotřebujete, můžete je odstranit. Uděláte to tak, že vyberte skupinu prostředků (**ResourceGroupTM1** nebo **ResourceGroupTM2**) a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

Další informace o metodách směrování najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Metoda směrování provozu](traffic-manager-routing-methods.md)
