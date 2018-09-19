---
title: Konfigurace metody směrování podsítě provozu pomocí Azure Traffic Manager | Dokumentace Microsoftu
description: Tento článek vysvětluje postup konfigurace Traffic Manageru směrovat provoz z konkrétní podsítě.
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jpconnock
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: kumud
ms.openlocfilehash: 6e5e6008741306d322ebd07bcbf144133ca4e632
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46131277"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Směrovat přenos dat až po konkrétní koncové body na základě podsítě uživatele pomocí služby Traffic Manager

Tento článek popisuje, jak konfigurovat metodu směrování provozu podsítě. **Podsítě** metody směrování provozu umožňuje mapování sadu rozsahů IP adres ke konkrétním koncovým bodům a při přijetí požadavku službou Traffic Manager, zkontroluje zdrojovou IP adresu požadavku a vrátí koncový bod s ním spojená. 

Ve scénáři popisovaných v tomto článku, použití směrování podsítě, v závislosti na IP adresu dotaz uživatele provoz se směruje buď na interní web nebo web produkčního prostředí.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Aby bylo možné, Traffic Manager v akci najdete v tomto kurzu potřebovat nasadit následující:
- dvě základní webů spuštěných v různých oblastech Azure - **USA – východ** (slouží jako interní web) a **západní Evropa** (slouží jako provozním webu).
- dvou testovacích virtuálních počítačů pro účely testování Traffic Manager – jeden virtuální počítač v **USA – východ** a druhý virtuální počítač v **západní Evropa**. 

Testovací virtuální počítače se používají pro ilustraci, jak Traffic Manager směruje provoz uživatelů na interní web nebo provozním webu na základě podsítě, ze které pochází uživatelský dotaz.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

### <a name="create-websites"></a>Vytváření webů

V této části vytvoříte dvě instance webů, které poskytují koncové body dvě služby pro profil Traffic Manageru ve dvou oblastech Azure. Vytvoření dvou webů zahrnuje následující kroky:
1. Vytvoření dvou virtuálních počítačů pro spouštění základní web - jeden v **USA – východ**a druhý v **západní Evropa**.
2. Instalace serveru služby IIS na každém virtuálním počítači a aktualizujte výchozí stránku webu, který popisuje název virtuálního počítače, který uživatel je připojený k při návštěvě webu.

#### <a name="create-vms-for-running-websites"></a>Vytvoření virtuálních počítačů pro weby
V této části vytvoříte dva virtuální počítače *myEndpointVMEastUS* a *myEndpointVMWEurope* v **USA – východ** a **západní Evropa** Azure oblasti.

1. V pravém horním levém horním rohu webu Azure portal vyberte **vytvořit prostředek** > **Compute** > **virtuálního počítače s Windows serverem 2016**.
2. V části **Základy** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myIISVMEastUS|
    |Uživatelské jméno| Zadejte libovolné uživatelské jméno.|
    |Heslo| Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Skupina prostředků| Vyberte **nový** a pak zadejte *myResourceGroupTM1*.|
    |Umístění| Vyberte **USA – východ**.|
    |||
4. V části **Zvolte velikost** vyberte velikost virtuálního počítače.
5. V části **Nastavení** vyberte následující hodnoty a pak vyberte **OK**:
    
    |Nastavení|Hodnota|
    |---|---|
    |Virtuální síť| Vyberte **virtuální síť**v **vytvořit virtuální síť**, pro **název**, zadejte *myVNet1*, podsíť, zadejte  *mySubnet*.|
    |Skupina zabezpečení sítě|Vyberte **základní**a v **vyberte veřejné příchozí porty** rozevíracího seznamu, vyberte **HTTP** a **protokolu RDP** |
    |Diagnostika spouštění|Vyberte **zakázané**.|
    |||
6. V části **Vytvořit** na kartě **Souhrn** vyberte **Vytvořit** a spusťte nasazování virtuálního počítače.

7. Zopakujte kroky 1 až 6 s následujícími změnami:

    |Nastavení|Hodnota|
    |---|---|
    |Skupina prostředků | Vyberte **Nová** a zadejte *myResourceGroupTM2*.|
    |Umístění|Západní Evropa|
    |Název virtuálního počítače | myIISVMWEurope|
    |Virtuální síť | Vyberte **virtuální síť**v **vytvořit virtuální síť**, pro **název**, zadejte *myVNet2*, podsíť, zadejte  *mySubnet*.|
    |||
8. Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte ve zbývajících krocích, dokud se oba virtuální počítače nevytvoří.

   ![Vytvoření virtuálního počítače](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalace služby IIS a přizpůsobení výchozí webové stránky

V této části nainstalujete server služby IIS na dva virtuální počítače – *myIISVMEastUS*  & *myIISVMWEurope*a pak aktualizujte výchozí stránku webu. Na stránce vlastní web zobrazí název virtuálního počítače, které se připojujete k při návštěvě webu z webového prohlížeče.

1. Vyberte **všechny prostředky** v levé nabídce a potom v seznamu prostředků klikněte na tlačítko *myIISVMEastUS* , který je umístěný v *myResourceGroupTM1* skupinu prostředků.
2. Na **přehled** klikněte na **připojit**a potom v **připojit k virtuálnímu počítači**vyberte **soubor stáhnout RDP**. 
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače. 
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.
6. Na ploše serveru přejděte do části **Nástroje pro správu Windows**>**Správce serveru**.
7. Spuštění Windows Powershellu na *myIISVMEastUS* a pomocí následujících příkazů nainstalujte server služby IIS a aktualizovat výchozí soubor htm.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my test website server - " + $env:computername)
    ```
8. Ukončete připojení RDP s *myIISVMEastUS*.
9. Opakujte kroky 1 až 6 s tím, že vytvoříte připojení ke vzdálené ploše s virtuálním Počítačem *myIISVMWEurope* v rámci *myResourceGroupTM2* skupinu prostředků k instalaci IIS a přizpůsobit její výchozí webové stránky.
10. Spuštění Windows Powershellu na *myIISVMWEurope* a pomocí následujících příkazů nainstalujte server služby IIS a aktualizovat výchozí soubor htm.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my production website server - " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurovat názvy DNS pro virtuální počítače se službou IIS

Traffic Manager směruje provoz uživatelů na základě názvu DNS koncových bodů služby. V této části nakonfigurujete názvy DNS pro servery služby IIS – *myIISVMEastUS* a *myIISVMWEurope*.

1. Klikněte na tlačítko **všechny prostředky** v levé nabídce a potom v seznamu prostředků vyberte *myIISVMEastUS* , který je umístěný v *myResourceGroupTM1* skupinu prostředků.
2. Na **přehled** stránce v části **název DNS**vyberte **konfigurovat**.
3. Na **konfigurace** stránce pod popisek názvu DNS, přidejte jedinečný název a pak vyberte **Uložit**.
4. Opakujte kroky 1-3 pro virtuální počítač s názvem *myIISVMWEurope* , který je umístěný v *myResourceGroupTM1* skupinu prostředků.

### <a name="create-test-vms"></a>Vytvořit testovací virtuální počítače

V této části vytvoříte virtuální počítač (*mVMEastUS* a *myVMWestEurope*) v každé oblasti Azure (**USA – východ** a **západní Evropa**. Tyto virtuální počítače budete používat pro testování, jak Traffic Manageru směruje provoz nejbližší server služby IIS při procházení webu.

1. V pravém horním levém horním rohu webu Azure portal vyberte **vytvořit prostředek** > **Compute** > **virtuálního počítače s Windows serverem 2016**.
2. V části **Základy** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myVMEastUS|
    |Uživatelské jméno| Zadejte libovolné uživatelské jméno.|
    |Heslo| Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Skupina prostředků| Vyberte **Existující** a pak vyberte *myResourceGroupTM1*.|
    |||

4. V části **Zvolte velikost** vyberte velikost virtuálního počítače.
5. V části **Nastavení** vyberte následující hodnoty a pak vyberte **OK**:
    |Nastavení|Hodnota|
    |---|---|
    |Virtuální síť| Vyberte **virtuální síť**v **vytvořit virtuální síť**, pro **název**, zadejte *myVNet3*, podsíť, zadejte  *mySubnet3*.|
    |Skupina zabezpečení sítě|Vyberte **základní**a v **vyberte veřejné příchozí porty** rozevíracího seznamu, vyberte **HTTP** a **protokolu RDP** |
    |Diagnostika spouštění|Vyberte **zakázané**.|
    |||

6. V části **Vytvořit** na kartě **Souhrn** vyberte **Vytvořit** a spusťte nasazování virtuálního počítače.

7. Zopakujte kroky 1 až 5 s následujícími změnami:

    |Nastavení|Hodnota|
    |---|---|
    |Název virtuálního počítače | *myVMWEurope*|
    |Skupina prostředků | Vyberte **existující**a pak zadejte *myResourceGroupTM2*|
    |Virtuální síť | Vyberte **virtuální síť**v **vytvořit virtuální síť**, pro **název**, zadejte *myVNet4*, podsíť, zadejte  *mySubnet4*.|
    |||

8. Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte ve zbývajících krocích, dokud se oba virtuální počítače nevytvoří.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru
Vytvořte profil služby Traffic Manager, která umožňuje vrátit konkrétní koncové body podle Zdrojová IP adresa požadavku.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek** > **Sítě** > **Profil služby Traffic Manager** > **Vytvořit**.
2. V části **Vytvořit profil služby Traffic Manager** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:
    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Název                   | Tento název musí být jedinečný v rámci zóny trafficmanager.net a výsledky v názvu DNS, trafficmanager.net, který se používá pro přístup k vašeho profilu Traffic Manageru.                                   |
    | Metoda směrování          | Vyberte **podsítě** metodu směrování.                                       |
    | Předplatné            | Vyberte své předplatné.                          |
    | Skupina prostředků          | Vyberte **existující** a zadejte *myResourceGroupTM1*. |
    | |                              |
    |
  
    ![Vytvoření profilu Traffic Manageru](./media/traffic-manager-subnet-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

Přidejte dva virtuální počítače se službou IIS servery – *myIISVMEastUS*  & *myIISVMWEurope* směrovat provoz uživatelů na základě podsítě dotaz uživatele.

1. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který jste vytvořili v předchozí části, a pak tento profil služby Traffic Manager vyberte v zobrazených výsledcích.
2. V okně **Profil služby Traffic Manager** v části **Nastavení** klikněte na **Koncové body** a pak na **Přidat**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Typ                    | Koncový bod Azure                                   |
    | Název           | myTestWebSiteEndpoint                                        |
    | Typ cílového prostředku           | Veřejná IP adresa                          |
    | Cílový prostředek          | **Zvolte veřejnou IP adresu** zobrazíte seznam prostředků pomocí veřejné IP adresy ve stejném předplatném. V **prostředků**, vyberte veřejnou IP adresu s názvem *myIISVMEastUS ip*. Toto je veřejnou IP adresu serveru služby IIS VM v oblasti východní USA.|
    |  Nastavení směrování podsítě    |   Přidat IP adresu *myVMEastUS* testovací virtuální počítač. Jakýkoli dotaz uživatelů pocházejících z tohoto virtuálního počítače budete přesměrováni na *myTestWebSiteEndpoint*.    |

4. Opakujte kroky 2 a 3 a přidat jiný koncový bod s názvem *myProductionEndpoint* pro veřejnou IP adresu *myIISVMWEurope ip* , který je přidružen k serveru služby IIS virtuální počítač s názvem *myIISVMWEurope* . Pro **směrování nastavení podsítě**, přidat IP adresu testovacího virtuálního počítače – *myVMWestEurope*. Jakýkoli dotaz uživatele z tohoto testovacího virtuálního počítače se budou směrovat do koncového bodu - *myProductionWebsiteEndpoint*.
5.  Po přidání se oba koncové body zobrazí v části **Profil služby Traffic Manager** a jejich stav monitorování bude **Online**.

    ![Přidání koncového bodu služby Traffic Manager](./media/traffic-manager-subnet-routing-method/customize-endpoint-with-subnet-routing-eastus.png)
  
## <a name="test-traffic-manager-profile"></a>Test profilu služby Traffic Manager
V této části můžete otestovat, jak Traffic Manager směruje provoz uživatelů z dané podsítě do určitého koncového bodu. Chcete-li zobrazit Traffic Manageru v akci, proveďte následující kroky:
1. Určení názvu DNS vašeho profilu Traffic Manageru.
2. Traffic Manager zobrazení v akci, následujícím způsobem:
    - Z testovacího virtuálního počítače (*myVMEastUS*), který je umístěný v **USA – východ** oblast, ve webovém prohlížeči přejděte na název DNS vašeho profilu Traffic Manageru.
    - Z testovacího virtuálního počítače (*myVMEastUS*), který je umístěný v **západní Evropa** oblast, ve webovém prohlížeči přejděte na název DNS vašeho profilu Traffic Manageru.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Určení názvu DNS profil služby Traffic Manager
V tomto kurzu se pro jednoduchost, použijete DNS název profilu Traffic Manageru navštívit weby. 

Název DNS profilu Traffic Manageru můžete zjistit takto:

1.  Na panelu hledání na portálu vyhledejte název **profilu služby Traffic Manager**, který jste vytvořili v předchozí části. Ve výsledcích, které se zobrazí, klikněte na profil služby Traffic Manager.
1. Klikněte na **Přehled**.
2. V části **Profil služby Traffic Manager** se zobrazí název DNS nově vytvořeného profilu služby Traffic Manager. V nasazení v produkčním prostředí je vlastní název domény tak, aby odkazoval na název domény Traffic Manageru, pomocí nakonfigurovat záznam DNS CNAME.

   ![Název DNS služby Traffic Manager](./media/traffic-manager-subnet-routing-method/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Zobrazení služby Traffic Manager v akci
V této části uvidíte, že Traffic Manager je akce. 

1. Vyberte **všechny prostředky** v levé nabídce a potom v seznamu prostředků klikněte na tlačítko *myVMEastUS* , který je umístěný v *myResourceGroupTM1* skupinu prostředků.
2. Na **přehled** klikněte na **připojit**a potom v **připojit k virtuálnímu počítači**vyberte **soubor stáhnout RDP**. 
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače. 
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování. 
1. Ve webovém prohlížeči na virtuálním počítači *myVMEastUS*, zadejte název DNS vašeho profilu Traffic Manageru k zobrazení vašeho webu. Od virtuálního počítače *myVMEastUS* IP adresa je přidružená ke koncovému bodu *myIISVMEastUS*, webový prohlížeč spustí Test webu serveru - *myIISVMEastUS*.

   ![Test profilu služby Traffic Manager](./media/traffic-manager-subnet-routing-method/test-traffic-manager.png)

2. Dále se připojte k virtuálnímu počítači *myVMWestEurope* umístěné v **západní Evropa** pomocí kroků 1 až 5 a přejděte na název domény pro profil Traffic Manageru z tohoto virtuálního počítače. Od virtuálního počítače *myVMWestEurope* IP adresa je přidružená ke koncovému bodu *myIISVMEastUS*, webový prohlížeč spustí Test webu serveru - *myIISVMWEurope*. 
  
## <a name="delete-the-traffic-manager-profile"></a>Odstranit profil služby Traffic Manager
Pokud už je nepotřebujete, odstraňte skupiny prostředků (**ResourceGroupTM1** a **ResourceGroupTM2**). Uděláte to tak, vyberte skupinu prostředků (**ResourceGroupTM1** nebo **ResourceGroupTM2**) a pak vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

- Další informace o [váha metodu směrování provozu](traffic-manager-configure-weighted-routing-method.md).
- Další informace o [metody prioritního směrování](traffic-manager-configure-priority-routing-method.md).
- Další informace o [metody geografického směrování](traffic-manager-configure-geographic-routing-method.md).


