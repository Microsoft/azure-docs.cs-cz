---
title: Kurz – směrování provozu do koncových bodů vážený - Azure Traffic Manageru
description: Tento kurz popisuje, jak pomocí služby Traffic Manager směrovat provoz do vážených koncových bodů.
services: traffic-manager
author: KumudD
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: kumud
ms.openlocfilehash: f4c29526f675cab461153b4749c4f6edc237dada
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467328"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-by-using-traffic-manager"></a>Kurz: Řídit směrování provozu s koncovými body vážený pomocí Traffic Manageru

Tento kurz popisuje, jak pomocí služby Azure Traffic Manager řídit směrování uživatelského provozu mezi koncovými body s využitím metody váženého směrování. V této metodě směrování každému koncovému bodu přiřadíte váhu v konfiguraci profilu služby Traffic Manager. Uživatelský provoz se pak bude směrovat na základě váhy přiřazené jednotlivým koncovým bodům. Váha je celé číslo od 1 do 1 000. Čím vyšší je hodnota váhy přiřazená ke koncovému bodu, tím vyšší má prioritu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření dvou virtuálních počítačů se základním webem ve službě IIS
> * Vytvoření dvou testovacích virtuálních počítačů pro zobrazení služby Traffic Manager v akci
> * Konfigurace názvů DNS pro virtuální počítače se službou IIS
> * Vytvoření profilu služby Traffic Manager
> * Přidání koncových bodů virtuálních počítačů do profilu služby Traffic Manager
> * Zobrazte službu Traffic Manager v akci.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Pokud chcete zobrazit službu Traffic Manager v akci, nasaďte pro účely tohoto kurzu následující:
- Dvě instance webů na úrovni basic spuštěné v různých oblastech Azure: Východní USA a západní Evropa.
- Dva testovací virtuální počítače pro testování služby Traffic Manager: jeden v oblasti USA – východ a druhý v oblasti Západní Evropa Testovací virtuální počítače slouží k předvedení způsobu, jakým Traffic Manager směruje uživatelský provoz na web, jehož koncový bod má přiřazenou vyšší váhu.

### <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

### <a name="create-websites"></a>Vytvoření webů

V této části vytvoříte dvě instance webů, které zajistí dva požadované koncové body služby ve dvou oblastech Azure pro profil služby Traffic Manager. Tyto dva weby vytvoříte provedením následujících kroků:
1. Vytvořte dva virtuální počítače pro provoz základního webu: jeden v oblasti USA – východ a druhý v oblasti Západní Evropa.
2. Na oba virtuální počítače nainstalujte server služby IIS. Aktualizujte výchozí webovou stránku zobrazující název virtuálního počítače, ke kterému je uživatel při prohlížení webu připojený.

#### <a name="create-vms-for-running-websites"></a>Vytvoření virtuálních počítačů pro provoz webů
V této části vytvoříte dva virtuální počítače (*myIISVMEastUS* a *myIISVMWEurope*) v oblastech Azure USA – východ a Západní Evropa.

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek** > **Compute** > **Virtuální počítač s Windows Serverem 2016**.
2. V části **Základy** zadejte nebo vyberte následující informace. U ostatních nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**.

    |Nastavení|Hodnota|
    |---|---|
    |Název|Zadejte **myIISVMEastUS**.|
    |Uživatelské jméno| Zadejte libovolné uživatelské jméno.|
    |Heslo| Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Skupina prostředků| Vyberte **Nová** a zadejte **myResourceGroupTM1**.|
    |Umístění| Vyberte **USA – východ**.|
    |||
4. V části **Zvolte velikost** vyberte velikost virtuálního počítače.
5. V části **Nastavení** vyberte následující hodnoty a pak vyberte **OK**:
    
    |Nastavení|Hodnota|
    |---|---|
    |Virtuální síť| Vyberte **Virtuální síť**. V části **Vytvořit virtuální síť** jako **Název** zadejte **myVNet1**. Jako **Podsíť** zadejte **mySubnet**.|
    |Skupina zabezpečení sítě|Vyberte **Basic**. V rozevíracím seznamu **Vyberte veřejné příchozí porty** vyberte **HTTP** a **RDP**. |
    |Diagnostika spouštění|Vyberte **Zakázáno**.|
    |||
6. V části **Vytvořit** na kartě **Souhrn** vyberte **Vytvořit** a spusťte nasazování virtuálního počítače.

7. Zopakujte kroky 1 až 6 s následujícími změnami:

    |Nastavení|Hodnota|
    |---|---|
    |Skupina prostředků | Vyberte **Nová** a zadejte **myResourceGroupTM2**.|
    |Umístění|Zadejte **Západní Evropa**.|
    |Název virtuálního počítače | Zadejte **myIISVMWEurope**.|
    |Virtuální síť | Vyberte **Virtuální síť**. V části **Vytvořit virtuální síť** jako **Název** zadejte **myVNet2**. Jako **Podsíť** zadejte **mySubnet**.|
    |||
8. Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte v dalších krocích, dokud se oba virtuální počítače nevytvoří.

![Vytvoření virtuálního počítače](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-webpage"></a>Instalace služby IIS a přizpůsobení výchozí webové stránky

V této části nainstalujete službu IIS na dva virtuální počítače&mdash;myIISVMEastUS a myIISVMWEurope&mdash;a aktualizujte výchozí webovou stránku. Na přizpůsobené webové stránce se zobrazí název virtuálního počítače, ke kterému se připojujete při prohlížení webu ve webovém prohlížeči.

1. V nabídce vlevo vyberte **Všechny prostředky**. Ze seznamu prostředků vyberte **myIISVMEastUS** ve skupině prostředků **myResourceGroupTM1**.
2. Na stránce **Přehled** vyberte **Připojit**. V části **Připojení k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**.
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** > **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.
4. Vyberte **OK**.
5. Při přihlášení se může zobrazit upozornění na certifikát. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.
6. Na ploše serveru přejděte do části **Nástroje pro správu Windows** > **Správce serveru**.
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
9. Zopakujte kroky 1 až 8. Vytvořte připojení RDP k virtuálnímu počítači **myIISVMWEurope** ve skupině prostředků **myResourceGroupTM2** a nainstalujte na něj službu IIS a přizpůsobte výchozí webovou stránku.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurace názvů DNS pro virtuální počítače se službou IIS

Traffic Manager směruje uživatelský provoz na základě názvů DNS koncových bodů služby. V této části nakonfigurujete názvy DNS pro servery služby IIS myIISVMEastUS a myIISVMWEurope.

1. V nabídce vlevo vyberte **Všechny prostředky**. Ze seznamu prostředků vyberte **myIISVMEastUS** ve skupině prostředků **myResourceGroupTM1**.
2. Na stránce **Přehled** v části **Název DNS** vyberte **Konfigurovat**.
3. Na stránce **Konfigurace** v části Popisek názvu DNS přidejte jedinečný název. Potom vyberte **Uložit**.
4. Zopakujte kroky 1 až 3 pro virtuální počítač **myIISVMWEurope** ve skupině prostředků **myResourceGroupTM1**.

### <a name="create-a-test-vm"></a>Vytvoření testovacího virtuálního počítače

V této části vytvoříte virtuální počítač *mVMEastUS*. Tento virtuální počítač použijete k otestování směrování provozu službou Traffic Manager do koncového bodu webu s vyšší hodnotou váhy.

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek** > **Compute** > **Virtuální počítač s Windows Serverem 2016**.
2. V části **Základy** zadejte nebo vyberte následující informace. U ostatních nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|Zadejte **myVMEastUS**.|
    |Uživatelské jméno| Zadejte libovolné uživatelské jméno.|
    |Heslo| Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Skupina prostředků| Vyberte **Použít existující** a pak vyberte **myResourceGroupTM1**.|
    |||

4. V části **Zvolte velikost** vyberte velikost virtuálního počítače.
5. V části **Nastavení** vyberte následující hodnoty a pak vyberte **OK**:
    |Nastavení|Hodnota|
    |---|---|
    |Virtuální síť| Vyberte **Virtuální síť**. V části **Vytvořit virtuální síť** jako **Název** zadejte **myVNet3**. Jako podsíť zadejte **mySubnet**.|
    |Skupina zabezpečení sítě|Vyberte **Basic**. V rozevíracím seznamu **Vyberte veřejné příchozí porty** vyberte **HTTP** a **RDP**. |
    |Diagnostika spouštění|Vyberte **Zakázáno**.|
    |||

6. V části **Vytvořit** na kartě **Souhrn** vyberte **Vytvořit** a spusťte nasazování virtuálního počítače.
8. Vytvoření virtuálního počítače trvá několik minut. Nepokračujte v dalších krocích, dokud se virtuální počítač nevytvoří.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru
Vytvořte profil služby Traffic Manager založený na metodě **váženého** směrování.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek** > **Sítě** > **Profil služby Traffic Manager** > **Vytvořit**.
2. V části **Vytvořit profil služby Traffic Manager** zadejte nebo vyberte následující informace. U ostatních nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**.

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Název                   | Zadejte jedinečný název v rámci zóny trafficmanager.net. Výsledkem bude název DNS trafficmanager.net, který bude sloužit k přístupu k vašemu profilu služby Traffic Manager.                                   |
    | Metoda směrování          | Vyberte metodu **váženého** směrování.                                       |
    | Předplatné            | Vyberte své předplatné.                          |
    | Skupina prostředků          | Vyberte **Použít existující** a pak vyberte **myResourceGroupTM1**. |
    |        |   |

    ![Vytvoření profilu Traffic Manageru](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

Přidejte dva virtuální počítače se službou myIISVMEastUS servery služby IIS a myIISVMWEurope, směrovat provoz uživatelů na ně.

1. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který jste vytvořili v předchozí části. Ve výsledcích, které se zobrazí, vyberte profil.
2. V okně **Profil služby Traffic Manager** v části **Nastavení** vyberte **Koncové body** > **Přidat**.
3. Zadejte nebo vyberte následující informace. U ostatních nastavení přijměte výchozí hodnoty a pak vyberte **OK**.

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Typ                    | Zadejte koncový bod Azure.                                   |
    | Název           | Zadejte **myEastUSEndpoint**.                                        |
    | Typ cílového prostředku           | Vyberte **Veřejná IP adresa**.                          |
    | Cílový prostředek          | Zvolte veřejnou IP adresu a zobrazí se výpis prostředků s veřejnými IP adresami ve stejném předplatném. Jako **Prostředek** vyberte veřejnou IP adresu **myIISVMEastUS-ip**. Toto je veřejná IP adresa virtuálního počítače se serverem služby IIS v oblasti USA – východ.|
    |  Hmotnost      | Zadejte **100**.        |
    |        |           |

4. Zopakujte kroky 2 a 3 a přidejte další koncový bod **myWestEuropeEndpoint** pro veřejnou IP adresu **myIISVMWEurope-ip**. Tato adresa je přidružená k virtuálnímu počítači se serverem služby IIS myIISVMWEurope. Jako **Váha** zadejte **25**.
5. Po přidání se oba koncové body zobrazí v profilu služby Traffic Manager a jejich stav monitorování bude **Online**.

## <a name="test-the-traffic-manager-profile"></a>Test profilu služby Traffic Manager
Pokud chcete zobrazit službu Traffic Manager v akci, proveďte následující kroky:
1. Určete název DNS vašeho profilu služby Traffic Manager.
2. Zobrazte službu Traffic Manager v akci.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Určení názvu DNS profilu služby Traffic Manager
V tomto kurzu pro zjednodušení k prohlížení webů použijete název DNS profilu služby Traffic Manager.

Název DNS profilu služby Traffic Manager můžete určit následujícím způsobem:

1. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který jste vytvořili v předchozí části. Ve výsledcích, které se zobrazí, vyberte profil služby Traffic Manager.
1. Vyberte **Přehled**.
2. V profilu služby Traffic Manager se zobrazí jeho název DNS. V produkčních nasazeních pomocí záznamu DNS CNAME nakonfigurujete individuální název domény odkazující na název domény služby Traffic Manager.

   ![Název DNS služby Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Zobrazení služby Traffic Manager v akci
V této části uvidíte službu Traffic Manager v akci.

1. V nabídce vlevo vyberte **Všechny prostředky**. Ze seznamu prostředků vyberte **myVMEastUS** ve skupině prostředků **myResourceGroupTM1**.
2. Na stránce **Přehled** vyberte **Připojit**. V části **Připojení k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**.
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** > **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.
4. Vyberte **OK**.
5. Při přihlášení se může zobrazit upozornění na certifikát. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.
6. Do webového prohlížeče na virtuálním počítači myVMEastUS zadejte název DNS vašeho profilu služby Traffic Manager a zobrazte váš web. Budete přesměrováni na web hostovaný na serveru služby IIS myIISVMEastUS, protože má přiřazenou vyšší váhu s hodnotou **100**. Server služby IIS myIISVMWEurope má přiřazenou nižší váhu koncového bodu s hodnotou **25**.

   ![Test profilu služby Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

## <a name="delete-the-traffic-manager-profile"></a>Odstranění profilu služby Traffic Manager
Pokud už skupiny prostředků vytvořené v tomto kurzu nepotřebujete, můžete je odstranit. Uděláte to tak, že vyberte skupinu prostředků (**ResourceGroupTM1** nebo **ResourceGroupTM2**) a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Směrování provozu do konkrétních koncových bodů na základě zeměpisného umístění uživatele](traffic-manager-configure-geographic-routing-method.md)
