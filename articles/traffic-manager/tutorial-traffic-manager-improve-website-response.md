---
title: Kurz – směrování provozu ke zlepšení odezvy webu pomocí služby Azure Traffic Manager
description: Tento kurz článek popisuje postup vytvoření profilu Traffic Manageru k vytvoření webu s velmi rychlou odezvou.
services: traffic-manager
documentationcenter: ''
author: kumudd
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/23/2018
ms.author: kumud
ms.openlocfilehash: fcbacb14ae1cf0d8fa31d84e281c96fab5e3b0d3
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052083"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Kurz: Zlepšení odezvy webu pomocí služby Traffic Manager 

Tento kurz popisuje, jak pomocí Traffic Manageru k vytvoření webu s velmi rychlou odezvou prostřednictvím uživatelského provozu na webu s nejnižší latenci. Datové centrum s nejnižší latencí je obvykle ten, který je nejblíže zeměpisné vzdálenost.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření dvou virtuálních počítačů se základním webem ve službě IIS
> * Vytvoření dvou testovacích virtuálních počítačů pro zobrazení služby Traffic Manager v akci
> * Konfigurace názvů DNS pro virtuální počítače se službou IIS
> * Vytvořit profil služby Traffic Manager pro lepší webu výkonu
> * Přidání koncových bodů virtuálních počítačů do profilu služby Traffic Manager
> * Zobrazení služby Traffic Manager v akci

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Aby bylo možné zobrazit službu Traffic Manager v akci, vyžaduje tento kurz, abyste nasadili:
- dvě instance základních webů spuštěné v různých oblastech Azure – **USA – východ** a **Západní Evropa**
- dva testovací virtuální počítače pro testování služby Traffic Manager – jeden virtuální počítač v oblasti **USA – východ** a druhý v oblasti **Západní Evropa** Testovací virtuální počítače se používají pro ilustraci, jak Traffic Manager směruje provoz uživatelů na web, na kterém běží ve stejné oblasti jako poskytuje nejnižší latenci.

### <a name="sign-in-to-azure"></a>Přihlásit se k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

### <a name="create-websites"></a>Vytvoření webů

V této části vytvoříte dvě instance webů, které zajistí dva požadované koncové body služby ve dvou oblastech Azure pro profil služby Traffic Manager. Vytvoření těchto dvou webů zahrnuje následující kroky:
1. Vytvoření dvou virtuálních počítačů pro provoz základního webu – jeden v oblasti **USA – východ** a druhý v oblasti **Západní Evropa**
2. Instalace serveru služby IIS na oba virtuální počítače a aktualizace výchozí webové stránky zobrazující název virtuálního počítače, ke kterému je uživatel při prohlížení webu připojený

#### <a name="create-vms-for-running-websites"></a>Vytvoření virtuálních počítačů pro provoz webů
V této části vytvoříte dva virtuální počítače *myIISVMEastUS* a *myIISVMWEurope* v oblastech Azure **USA – východ** a **Západní Evropa**.

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek** > **Compute** > **Virtuální počítač s Windows Serverem 2016**.
2. V části **Základy** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    |Nastavení|Hodnota|
    |---|---|
    |Název|myIISVMEastUS|
    |Uživatelské jméno| Zadejte libovolné uživatelské jméno.|
    |Heslo| Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Skupina prostředků| Vyberte **Nová** a zadejte *myResourceGroupTM1*.|
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

V této části na oba virtuální počítače (*myIISVMEastUS*  & *myIISVMWEurope*) nainstalujete server služby IIS a pak aktualizujete výchozí webovou stránku. Na přizpůsobené webové stránce se zobrazí název virtuálního počítače, ke kterému se připojujete při prohlížení webu ve webovém prohlížeči.

1. V levé nabídce vyberte **Všechny prostředky** a pak v seznamu prostředků klikněte na *myIISVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** klikněte na **Připojit** a pak v části **Připojit k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**. 
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače. 
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování.
6. Na ploše serveru přejděte do části **Nástroje pro správu Windows**>**Správce serveru**.
7. Spusťte na virtuálním počítači VM1 Windows PowerShell a pomocí následujících příkazů nainstalujte server služby IIS a aktualizujte výchozí soubor htm.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![Instalace služby IIS a přizpůsobení webové stránky](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)
8. Ukončete připojení RDP k virtuálnímu počítači *myIISVMEastUS*.
9. Zopakujte kroky 1 až 8 a vytvořte připojení RDP k virtuálnímu počítači *myIISVMWEurope* ve skupině prostředků *myResourceGroupTM2* a nainstalujte na něj službu IIS a přizpůsobte výchozí webovou stránku.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurace názvů DNS pro virtuální počítače se službou IIS

Traffic Manager směruje provoz uživatelů na základě názvů DNS koncových bodů služby. V této části nakonfigurujete názvy DNS pro servery služby IIS – *myIISVMEastUS* a *myIISVMWEurope*.

1. V levé nabídce klikněte na **Všechny prostředky** a pak v seznamu prostředků vyberte *myIISVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** v části **Název DNS** vyberte **Konfigurovat**.
3. Na stránce **Konfigurace** v části Popisek názvu DNS přidejte jedinečný název a pak vyberte **Uložit**.
4. Zopakujte kroky 1 až 3 pro virtuální počítač *myIISVMWEurope* ve skupině prostředků *myResourceGroupTM1*.

### <a name="create-test-vms"></a>Vytvoření testovacích virtuálních počítačů

V této části vytvoříte virtuální počítač (*mVMEastUS* a *myVMWestEurope*) v každé oblasti Azure (**USA – východ** a **západní Evropa**. Pomocí těchto virtuálních počítačů otestujete, jak Traffic Manager směruje provoz při prohlížení webu na nejbližší server služby IIS.

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek** > **Compute** > **Virtuální počítač s Windows Serverem 2016**.
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
    |Virtuální síť| Vyberte **Virtuální síť** a v části **Vytvořit virtuální síť** jako **Název** zadejte *myVNet3* a jako podsíť zadejte *mySubnet*.|
    |Skupina zabezpečení sítě|Vyberte **Basic** a v rozevíracím seznamu **Vyberte veřejné příchozí porty** vyberte **HTTP** a **RDP**. |
    |Diagnostika spouštění|Vyberte **Zakázáno**.|
    |||

6. V části **Vytvořit** na kartě **Souhrn** vyberte **Vytvořit** a spusťte nasazování virtuálního počítače.

7. Zopakujte kroky 1 až 5 s následujícími změnami:

    |Nastavení|Hodnota|
    |---|---|
    |Název virtuálního počítače | *myVMWEurope*|
    |Skupina prostředků | Vyberte **Existující** a pak zadejte *myResourceGroupTM2*.|
    |Virtuální síť | Vyberte **virtuální síť**v **vytvořit virtuální síť**, pro **název**, zadejte *myVNet4*, podsíť, zadejte  *mySubnet*.|
    |||

8. Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte ve zbývajících krocích, dokud se oba virtuální počítače nevytvoří.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru
Vytvořte profil služby Traffic Manager, která přesměruje uživatelský provoz tak, že je pošlete na koncový bod s nejnižší latenci.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek** > **Sítě** > **Profil služby Traffic Manager** > **Vytvořit**.
2. V části **Vytvořit profil služby Traffic Manager** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:
    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Název                   | Tento název musí být jedinečný v rámci zóny trafficmanager.net a ve výsledcích názvu DNS trafficmanager.net, který slouží k přístupu k vašemu profilu služby Traffic Manager.                                   |
    | Metoda směrování          | Vyberte **výkonu** metodu směrování.                                       |
    | Předplatné            | Vyberte své předplatné.                          |
    | Skupina prostředků          | Vyberte **vytvořit nový** a zadejte *myResourceGroupTM1*. |
    | Umístění                | Vyberte **USA – východ**.  Toto nastavení se týká umístění skupiny prostředků a nemá žádný vliv na profil Traffic Manageru, který se nasadí globálně.                              |
    |
  
    ![Vytvoření profilu Traffic Manageru](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

Přidejte dva virtuální počítače se službou IIS servery – *myIISVMEastUS*  & *myIISVMWEurope* uživatele směrovat provoz na nejbližší koncový bod pro uživatele.

1. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který jste vytvořili v předchozí části, a pak tento profil služby Traffic Manager vyberte v zobrazených výsledcích.
2. V okně **Profil služby Traffic Manager** v části **Nastavení** klikněte na **Koncové body** a pak na **Přidat**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Typ                    | Koncový bod Azure                                   |
    | Název           | myEastUSEndpoint                                        |
    | Typ cílového prostředku           | Veřejná IP adresa                          |
    | Cílový prostředek          | **Zvolte veřejnou IP adresu** a zobrazí se výpis prostředků s veřejnými IP adresami ve stejném předplatném. Jako **Prostředek** vyberte veřejnou IP adresu *myIISVMEastUS-ip*. Toto je veřejná IP adresa virtuálního počítače se serverem služby IIS v oblasti USA – východ.|
    |        |           |

4. Opakujte kroky 2 a 3 a přidat jiný koncový bod s názvem *myWestEuropeEndpoint* pro veřejnou IP adresu *myIISVMWEurope ip* , který je přidružen k serveru služby IIS virtuální počítač s názvem *myIISVMWEurope* .
5.  Po přidání se oba koncové body zobrazí v části **Profil služby Traffic Manager** a jejich stav monitorování bude **Online**.

    ![Přidání koncového bodu služby Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)
  

## <a name="test-traffic-manager-profile"></a>Test profilu služby Traffic Manager
V této části můžete otestovat, jak Traffic Manager směruje provoz uživatelů na nejbližší virtuální počítače se službou Web a poskytnout minimální latenci. Pokud chcete zobrazit službu Traffic Manager v akci, proveďte následující kroky:
1. Určete název DNS vašeho profilu služby Traffic Manager.
2. Službu Traffic Manager v akci zobrazíte následovně:
    - Z testovacího virtuálního počítače (*myVMEastUS*), který je umístěný v **USA – východ** oblast, ve webovém prohlížeči přejděte na název DNS vašeho profilu Traffic Manageru.
    - Z testovacího virtuálního počítače (*myVMEastUS*), který je umístěný v **západní Evropa** oblast, ve webovém prohlížeči přejděte na název DNS vašeho profilu Traffic Manageru.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Určení názvu DNS profilu služby Traffic Manager
V tomto kurzu pro zjednodušení k prohlížení webů použijete název DNS profilu služby Traffic Manager. 

Název DNS profilu služby Traffic Manager můžete určit následujícím způsobem:

1.  Na panelu hledání na portálu vyhledejte název **profilu služby Traffic Manager**, který jste vytvořili v předchozí části. Ve výsledcích, které se zobrazí, klikněte na profil služby Traffic Manager.
1. Klikněte na **Přehled**.
2. V části **Profil služby Traffic Manager** se zobrazí název DNS nově vytvořeného profilu služby Traffic Manager. V produkčních nasazeních pomocí záznamu DNS CNAME nakonfigurujete individuální název domény odkazující na název domény služby Traffic Manager.

   ![Název DNS služby Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Zobrazení služby Traffic Manager v akci
V této části uvidíte službu Traffic Manager v akci. 

1. V levé nabídce vyberte **Všechny prostředky** a pak v seznamu prostředků klikněte na *myVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** klikněte na **Připojit** a pak v části **Připojit k virtuálnímu počítači** vyberte **Stáhnout soubor RDP**. 
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače. 
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování. 
1. Do webového prohlížeče na virtuálním počítači *myVMEastUS* zadejte název DNS vašeho profilu služby Traffic Manager a zobrazte váš web. Protože virtuální počítač nachází v **USA – východ**, směrují na nejbližší web hostovaný na nejbližší server služby IIS *myIISVMEastUS* , který je umístěný v **USA – východ**.

   ![Test profilu služby Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Dále se připojte k virtuálnímu počítači *myVMWestEurope* umístěné v **západní Evropa** pomocí kroků 1 až 5 a přejděte na název domény pro profil Traffic Manageru z tohoto virtuálního počítače.  Protože virtuální počítač nachází v **západní Evropa**, nyní jsou směrovány na web hostovaný na nejbližší server služby IIS *myIISVMWEurope* , který je umístěný v **západní Evropa**. 

   ![Test profilu služby Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)
   
## <a name="delete-the-traffic-manager-profile"></a>Odstranění profilu služby Traffic Manager
Pokud už skupiny prostředků (**ResourceGroupTM1** a **ResourceGroupTM2**) nepotřebujete, odstraňte je. Uděláte to tak, že vyberte skupinu prostředků (**ResourceGroupTM1** nebo **ResourceGroupTM2**) a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Distribuce provozu do sady koncových bodů](traffic-manager-configure-weighted-routing-method.md)


