---
title: Kurz – směrování provozu ke zlepšení odezvy webu pomocí služby Azure Traffic Manager | Dokumentace Microsoftu
description: Tento kurz článek popisuje postup vytvoření profilu Traffic Manageru k vytvoření webu s velmi rychlou odezvou.
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: jeconnoc
editor: ''
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/23/2018
ms.author: kumud
ms.openlocfilehash: 89518d30b862e18fb7c989c95144ffa7f1c294fc
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40024961"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Kurz: Zlepšení odezvy webu pomocí služby Traffic Manager 

Tento kurz popisuje, jak pomocí Traffic Manageru k vytvoření webu s velmi rychlou odezvou prostřednictvím uživatelského provozu na webu s nejnižší latenci. Datové centrum s nejnižší latencí je obvykle ten, který je nejblíže zeměpisné vzdálenost.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření dvou virtuálních počítačů s základního webu ve službě IIS
> * Vytvoření testu dva virtuální počítače, které Traffic Manageru zobrazit v akci
> * Konfigurace názvu DNS pro virtuální počítače se službou IIS
> * Vytvořit profil služby Traffic Manager pro lepší webu výkonu
> * Přidání koncových bodů virtuálního počítače do profilu služby Traffic Manager
> * Zobrazení Traffic Manageru v akci

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Aby bylo možné, Traffic Manager v akci najdete v tomto kurzu potřebovat nasadit následující:
- dvě instance základní webů spuštěných v různých oblastech Azure - **USA – východ** a **západní Evropa**.
- dvou testovacích virtuálních počítačů pro účely testování Traffic Manager – jeden virtuální počítač v **USA – východ** a druhý virtuální počítač v **západní Evropa**. Testovací virtuální počítače se používají pro ilustraci, jak Traffic Manager směruje provoz uživatelů na web, na kterém běží ve stejné oblasti jako poskytuje nejnižší latenci.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

### <a name="create-websites"></a>Vytváření webů

V této části vytvoříte dvě instance webů, které poskytují koncové body dvě služby pro profil Traffic Manageru ve dvou oblastech Azure. Vytvoření dvou webů zahrnuje následující kroky:
1. Vytvoření dvou virtuálních počítačů pro spouštění základní web - jeden v **USA – východ**a druhý v **západní Evropa**.
2. Instalace serveru služby IIS na každém virtuálním počítači a aktualizujte výchozí stránku webu, který popisuje název virtuálního počítače, který uživatel je připojený k při návštěvě webu.

#### <a name="create-vms-for-running-websites"></a>Vytvoření virtuálních počítačů pro weby
V této části vytvoříte dva virtuální počítače *myIISVMEastUS* a *myIISVMWEurope* v **USA – východ** a **západní Evropa** oblastí Azure.

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
    |Virtuální síť| Vyberte **virtuální síť**v **vytvořit virtuální síť**, pro **název**, zadejte *myVNet1*, podsíť, zadejte * mySubnet*.|
    |Skupina zabezpečení sítě|Vyberte **základní**a v **vyberte veřejné příchozí porty** rozevíracího seznamu, vyberte **HTTP** a **protokolu RDP** |
    |Diagnostika spouštění|Vyberte **zakázané**.|
    |||
6. V části **Vytvořit** na kartě **Souhrn** vyberte **Vytvořit** a spusťte nasazování virtuálního počítače.

7. Zopakujte kroky 1 až 6 s následujícími změnami:

    |Nastavení|Hodnota|
    |---|---|
    |Skupina prostředků | Vyberte **nový**a pak zadejte *myResourceGroupTM2*|
    |Umístění|Západní Evropa|
    |Název virtuálního počítače | myIISVMWEurope|
    |Virtuální síť | Vyberte **virtuální síť**v **vytvořit virtuální síť**, pro **název**, zadejte *myVNet2*, podsíť, zadejte * mySubnet*.|
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
7. Spusťte na virtuálním počítači VM1 Windows PowerShell a pomocí následujících příkazů nainstalujte server služby IIS a aktualizujte výchozí soubor htm.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![Instalace služby IIS a přizpůsobit webovou stránku](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)
8. Ukončete připojení RDP s *myIISVMEastUS*.
9. Opakujte kroky 1-8 s tím, že vytvoříte připojení ke vzdálené ploše s virtuálním Počítačem *myIISVMWEurope* v rámci *myResourceGroupTM2* skupinu prostředků k instalaci IIS a přizpůsobit její výchozí webové stránky.

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
    |Skupina prostředků| Vyberte **existující** a pak vyberte *myResourceGroupTM1*.|
    |||

4. V části **Zvolte velikost** vyberte velikost virtuálního počítače.
5. V části **Nastavení** vyberte následující hodnoty a pak vyberte **OK**:
    |Nastavení|Hodnota|
    |---|---|
    |Virtuální síť| Vyberte **virtuální síť**v **vytvořit virtuální síť**, pro **název**, zadejte *myVNet3*, podsíť, zadejte * mySubnet*.|
    |Skupina zabezpečení sítě|Vyberte **základní**a v **vyberte veřejné příchozí porty** rozevíracího seznamu, vyberte **HTTP** a **protokolu RDP** |
    |Diagnostika spouštění|Vyberte **zakázané**.|
    |||

6. V části **Vytvořit** na kartě **Souhrn** vyberte **Vytvořit** a spusťte nasazování virtuálního počítače.

7. Zopakujte kroky 1 až 5 s následujícími změnami:

    |Nastavení|Hodnota|
    |---|---|
    |Název virtuálního počítače | *myVMWEurope*|
    |Skupina prostředků | Vyberte **existující**a pak zadejte *myResourceGroupTM2*|
    |Virtuální síť | Vyberte **virtuální síť**v **vytvořit virtuální síť**, pro **název**, zadejte *myVNet4*, podsíť, zadejte * mySubnet*.|
    |||

8. Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte ve zbývajících krocích, dokud se oba virtuální počítače nevytvoří.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru
Vytvořte profil služby Traffic Manager, která přesměruje uživatelský provoz tak, že je pošlete na koncový bod s nejnižší latenci.

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek** > **sítě** > **profil služby Traffic Manager**  >  **Vytvořit**.
2. V **vytvořit profil Traffic Manageru**, zadejte nebo vyberte následující informace, přijměte výchozí hodnoty pro zbývající nastavení a pak vyberte **vytvořit**:
    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Název                   | Tento název musí být jedinečný v rámci zóny trafficmanager.net a výsledky v názvu DNS, trafficmanager.net, který se používá pro přístup k vašeho profilu Traffic Manageru.                                   |
    | Metoda směrování          | Vyberte **Priority** metodu směrování.                                       |
    | Předplatné            | Vyberte své předplatné.                          |
    | Skupina prostředků          | Vyberte **vytvořit nový** a zadejte *myResourceGroupTM1*. |
    | Umístění                | Vyberte **USA – východ**.  Toto nastavení se týká umístění skupiny prostředků a nemá žádný vliv na profil Traffic Manageru, který se nasadí globálně.                              |
    |
  
    ![Vytvoření profilu Traffic Manageru](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Přidat koncové body Traffic Manageru

Přidejte dva virtuální počítače se službou IIS servery – *myIISVMEastUS*  & *myIISVMWEurope* uživatele směrovat provoz na nejbližší koncový bod pro uživatele.

1. Na panelu hledání na portálu vyhledejte název profilu Traffic Manageru, který jste vytvořili v předchozí části a ve výsledcích vyberte profil, který je zobrazeno.
2. V **profil služby Traffic Manager**v **nastavení** klikněte na tlačítko **koncové body**a potom klikněte na tlačítko **přidat**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Typ                    | Koncový bod Azure                                   |
    | Název           | myEastUSEndpoint                                        |
    | Typ cílového prostředku           | Veřejná IP adresa                          |
    | Cílový prostředek          | **Zvolte veřejnou IP adresu** zobrazíte seznam prostředků pomocí veřejné IP adresy ve stejném předplatném. V **prostředků**, vyberte veřejnou IP adresu s názvem *myIISVMEastUS ip*. Toto je veřejnou IP adresu serveru služby IIS VM v oblasti východní USA.|
    |        |           |

4. Opakujte kroky 2 a 3 a přidat jiný koncový bod s názvem *myWestEuropeEndpoint* pro veřejnou IP adresu *myIISVMWEurope ip* , který je přidružen k serveru služby IIS virtuální počítač s názvem *myIISVMWEurope *.
5.  Po dokončení přidávání oba koncové body jsou zobrazeny v **profil služby Traffic Manager** spolu s jejich stav monitorování bude **Online**.

    ![Přidat koncový bod služby Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)
  

## <a name="test-traffic-manager-profile"></a>Profil služby Traffic Manager testu
V této části můžete otestovat, jak Traffic Manager směruje provoz uživatelů na nejbližší virtuální počítače se službou Web a poskytnout minimální latenci. Chcete-li zobrazit Traffic Manageru v akci, proveďte následující kroky:
1. Určení názvu DNS vašeho profilu Traffic Manageru.
2. Traffic Manager zobrazení v akci, následujícím způsobem:
    - Z testovacího virtuálního počítače (*myVMEastUS*), který je umístěný v **USA – východ** oblast, ve webovém prohlížeči přejděte na název DNS vašeho profilu Traffic Manageru.
    - Z testovacího virtuálního počítače (*myVMEastUS*), který je umístěný v **západní Evropa** oblast, ve webovém prohlížeči přejděte na název DNS vašeho profilu Traffic Manageru.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Určení názvu DNS profil služby Traffic Manager
V tomto kurzu se pro jednoduchost, použijete DNS název profilu Traffic Manageru navštívit weby. 

Název DNS profilu Traffic Manageru můžete zjistit takto:

1.  Na panelu hledání na portálu vyhledejte **profil služby Traffic Manager** název, který jste vytvořili v předchozí části. Ve výsledcích, které se zobrazí klikněte na profil traffic Manageru.
1. Klikněte na tlačítko **přehled**.
2. **Profil služby Traffic Manager** zobrazí název DNS váš nově vytvořený profil Traffic Manageru. V nasazení v produkčním prostředí je vlastní název domény tak, aby odkazoval na název domény Traffic Manageru, pomocí nakonfigurovat záznam DNS CNAME.

   ![Název DNS Traffic Manageru](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Zobrazení Traffic Manageru v akci
V této části uvidíte, že Traffic Manager je akce. 

1. Vyberte **všechny prostředky** v levé nabídce a potom v seznamu prostředků klikněte na tlačítko *myVMEastUS* , který je umístěný v *myResourceGroupTM1* skupinu prostředků.
2. Na **přehled** klikněte na **připojit**a potom v **připojit k virtuálnímu počítači**vyberte **soubor stáhnout RDP**. 
3. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná bude nutné vybrat **Další možnosti** a pak **Použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače. 
4. Vyberte **OK**.
5. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování. 
1. Ve webovém prohlížeči na virtuálním počítači *myVMEastUS*, zadejte název DNS vašeho profilu Traffic Manageru k zobrazení vašeho webu. Protože virtuální počítač nachází v **USA – východ**, směrují na nejbližší web hostovaný na nejbližší server služby IIS *myIISVMEastUS* , který je umístěný v **USA – východ**.

   ![Profil služby Traffic Manager testu](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Dále se připojte k virtuálnímu počítači *myVMWestEurope* umístěné v **západní Evropa** pomocí kroků 1 až 5 a přejděte na název domény pro profil Traffic Manageru z tohoto virtuálního počítače.  Protože virtuální počítač nachází v **západní Evropa**, nyní jsou směrovány na web hostovaný na nejbližší server služby IIS *myIISVMWEurope* , který je umístěný v **západní Evropa**. 

   ![Profil služby Traffic Manager testu](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)
   
## <a name="delete-the-traffic-manager-profile"></a>Odstranit profil služby Traffic Manager
Pokud už je nepotřebujete, odstraňte skupiny prostředků (**ResourceGroupTM1** a **ResourceGroupTM2**). Uděláte to tak, vyberte skupinu prostředků (**ResourceGroupTM1** nebo **ResourceGroupTM2**) a pak vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Distribuce provozu do sady koncových bodů](traffic-manager-configure-weighted-routing-method.md)


