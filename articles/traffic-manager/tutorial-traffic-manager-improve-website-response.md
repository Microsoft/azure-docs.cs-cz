---
title: Tutorial - Improve website response with Azure Traffic Manager
description: This tutorial article describes how to create a Traffic Manager profile to build a highly responsive website.
services: traffic-manager
author: asudbring
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/23/2018
ms.author: allensu
ms.openlocfilehash: 3686e9a7d82f8134b44cd40468c5e430eb2e72f3
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422857"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Tutorial: Improve website response using Traffic Manager

This tutorial describes how to use Traffic Manager to create a highly responsive website by directing user traffic to the website with the lowest latency. Typically, the datacenter with the lowest latency is the one that is closest in geographic distance.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření dvou virtuálních počítačů se základním webem ve službě IIS
> * Vytvoření dvou testovacích virtuálních počítačů pro zobrazení služby Traffic Manager v akci
> * Konfigurace názvů DNS pro virtuální počítače se službou IIS
> * Create a Traffic Manager profile for improved website performance
> * Přidání koncových bodů virtuálních počítačů do profilu služby Traffic Manager
> * Zobrazení služby Traffic Manager v akci

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

Aby bylo možné zobrazit službu Traffic Manager v akci, vyžaduje tento kurz, abyste nasadili:

- Two instances of basic websites running in different Azure regions - **East US** and **West Europe**.
- Two test VMs for testing the Traffic Manager - one VM in **East US** and the second VM in **West Europe**. The test VMs are used to illustrate how Traffic Manager routes user traffic to the website that is running in the same region as it provides the lowest latency.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

### <a name="create-websites"></a>Vytvoření webů

V této části vytvoříte dvě instance webů, které zajistí dva požadované koncové body služby ve dvou oblastech Azure pro profil služby Traffic Manager. Vytvoření těchto dvou webů zahrnuje následující kroky:

1. Vytvoření dvou virtuálních počítačů pro provoz základního webu – jeden v oblasti **USA – východ** a druhý v oblasti **Západní Evropa**
2. Instalace serveru služby IIS na oba virtuální počítače a aktualizace výchozí webové stránky zobrazující název virtuálního počítače, ke kterému je uživatel při prohlížení webu připojený

#### <a name="create-vms-for-running-websites"></a>Vytvoření virtuálních počítačů pro provoz webů

In this section, you create two VMs *myIISVMEastUS* and *myIISVMWestEurope* in the **East US** and **West Europe** Azure regions.

1. On the upper, left corner of the Azure portal, select **Create a resource** > **Compute** > **Windows Server 2019 Datacenter**.
2. In **Create a virtual machine**, type or select the following values in the **Basics** tab:

   - **Subscription** > **Resource Group**: Select **Create new** and then type **myResourceGroupTM1**.
   - **Instance Details** > **Virtual machine name**: Type *myIISVMEastUS*.
   - **Instance Details** > **Region**:  Select **East US**.
   - **Administrator Account** > **Username**:  Enter a user name of your choosing.
   - **Administrator Account** > **Password**:  Enter a password of your choosing. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Inbound Port Rules** > **Public inbound ports**: Select **Allow selected ports**.
   - **Inbound Port Rules** > **Select inbound ports**: Select **RDP** and **HTTP** in the pull down box.

3. Select the **Management** tab, or select **Next: Disks**, then **Next: Networking**, then **Next: Management**. Under **Monitoring**, set **Boot diagnostics** to **Off**.
4. Vyberte **Zkontrolovat a vytvořit**.
5. Review the settings, and then click **Create**.  
6. Follow the steps to create a second VM named *myIISVMWestEurope*, with a **Resource group** name of *myResourceGroupTM2*, a **location** of *West Europe*, and all the other settings the same as *myIISVMEastUS*.
7. Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte ve zbývajících krocích, dokud se oba virtuální počítače nevytvoří.

   ![Vytvoření virtuálního počítače](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalace služby IIS a přizpůsobení výchozí webové stránky

In this section, you install the IIS server on the two VMs *myIISVMEastUS* and *myIISVMWestEurope*, and then update the default website page. Na přizpůsobené webové stránce se zobrazí název virtuálního počítače, ke kterému se připojujete při prohlížení webu ve webovém prohlížeči.

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
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![Instalace služby IIS a přizpůsobení webové stránky](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)
8. Ukončete připojení RDP k virtuálnímu počítači *myIISVMEastUS*.
9. Repeat steps 1-8 with by creating an RDP connection with the VM *myIISVMWestEurope* within the *myResourceGroupTM2* resource group to install IIS and customize its default web page.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurace názvů DNS pro virtuální počítače se službou IIS

Traffic Manager směruje provoz uživatelů na základě názvů DNS koncových bodů služby. In this section, you configure the DNS names for the IIS servers - *myIISVMEastUS* and *myIISVMWestEurope*.

1. V levé nabídce klikněte na **Všechny prostředky** a pak v seznamu prostředků vyberte *myIISVMEastUS* ve skupině prostředků *myResourceGroupTM1*.
2. Na stránce **Přehled** v části **Název DNS** vyberte **Konfigurovat**.
3. Na stránce **Konfigurace** v části Popisek názvu DNS přidejte jedinečný název a pak vyberte **Uložit**.
4. Repeat steps 1-3, for the VM named *myIISVMWestEurope* that is located in the *myResourceGroupTM2* resource group.

### <a name="create-test-vms"></a>Vytvoření testovacích virtuálních počítačů

In this section, you create a VM (*myVMEastUS* and *myVMWestEurope*) in each Azure region (**East US** and **West Europe**). Pomocí těchto virtuálních počítačů otestujete, jak Traffic Manager směruje provoz při prohlížení webu na nejbližší server služby IIS.

1. On the upper, left corner of the Azure portal, select **Create a resource** > **Compute** > **Windows Server 2019 Datacenter**.
2. In **Create a virtual machine**, type or select the following values in the **Basics** tab:

   - **Subscription** > **Resource Group**: Select **myResourceGroupTM1**.
   - **Instance Details** > **Virtual machine name**: Type *myVMEastUS*.
   - **Instance Details** > **Region**:  Select **East US**.
   - **Administrator Account** > **Username**:  Enter a user name of your choosing.
   - **Administrator Account** > **Password**:  Enter a password of your choosing. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Inbound Port Rules** > **Public inbound ports**: Select **Allow selected ports**.
   - **Inbound Port Rules** > **Select inbound ports**: Select **RDP** in the pull down box.

3. Select the **Management** tab, or select **Next: Disks**, then **Next: Networking**, then **Next: Management**. Under **Monitoring**, set **Boot diagnostics** to **Off**.
4. Vyberte **Zkontrolovat a vytvořit**.
5. Review the settings, and then click **Create**.  
6. Follow the steps to create a second VM named *myVMWestEurope*, with a **Resource group** name of *myResourceGroupTM2*, a **location** of *West Europe*, and all the other settings the same as *myVMEastUS*.
7. Vytvoření virtuálních počítačů trvá několik minut. Nepokračujte ve zbývajících krocích, dokud se oba virtuální počítače nevytvoří.

## <a name="create-a-traffic-manager-profile"></a>Vytvoření profilu Traffic Manageru

Create a Traffic Manager profile that directs user traffic by sending them to the endpoint with lowest latency.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek** > **Sítě** > **Profil služby Traffic Manager** > **Vytvořit**.
2. V části **Vytvořit profil služby Traffic Manager** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Name (Název)                   | Tento název musí být jedinečný v rámci zóny trafficmanager.net a ve výsledcích názvu DNS trafficmanager.net, který slouží k přístupu k vašemu profilu služby Traffic Manager.                                   |
    | Metoda směrování          | Select the **Performance** routing method.                                       |
    | Předplatné            | Vyberte své předplatné.                          |
    | Skupina prostředků          | Select the Resource group *myResourceGroupTM1*. |
    | Umístění                | Vyberte **USA – východ**. Toto nastavení se týká umístění skupiny prostředků a nemá žádný vliv na profil Traffic Manageru, který se nasadí globálně.                              |
    |

    ![Vytvoření profilu Traffic Manageru](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

Add the two VMs running the IIS servers - *myIISVMEastUS* & *myIISVMWestEurope* to route user traffic to the closest endpoint to the user.

1. Na panelu hledání na portálu vyhledejte název profilu služby Traffic Manager, který jste vytvořili v předchozí části, a pak tento profil služby Traffic Manager vyberte v zobrazených výsledcích.
2. V okně **Profil služby Traffic Manager** v části **Nastavení** klikněte na **Koncové body** a pak na **Přidat**.
3. Zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **OK**:

    | Nastavení                 | Hodnota                                              |
    | ---                     | ---                                                |
    | Typ                    | Koncový bod Azure                                   |
    | Name (Název)           | myEastUSEndpoint                                        |
    | Typ cílového prostředku           | Veřejná IP adresa                          |
    | Cílový prostředek          | **Zvolte veřejnou IP adresu** a zobrazí se výpis prostředků s veřejnými IP adresami ve stejném předplatném. Jako **Prostředek** vyberte veřejnou IP adresu *myIISVMEastUS-ip*. Toto je veřejná IP adresa virtuálního počítače se serverem služby IIS v oblasti USA – východ.|
    |        |           |

4. Repeat steps 2 and 3 to add another endpoint named *myWestEuropeEndpoint* for the public IP address *myIISVMWestEurope-ip* that is associated with the IIS server VM named *myIISVMWestEurope*.
5. Po přidání se oba koncové body zobrazí v části **Profil služby Traffic Manager** a jejich stav monitorování bude **Online**.

    ![Přidání koncového bodu služby Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)

## <a name="test-traffic-manager-profile"></a>Test profilu služby Traffic Manager

In this section, you test how the Traffic Manager routes user traffic to the nearest VMs running the website to provide minimum latency. Pokud chcete zobrazit službu Traffic Manager v akci, proveďte následující kroky:

1. Určete název DNS vašeho profilu služby Traffic Manager.
2. Službu Traffic Manager v akci zobrazíte následovně:
    - From the test VM (*myVMEastUS*) that is located in the **East US** region, in a web browser, browse to the DNS name of your Traffic Manager profile.
    - From the test VM (*myVMWestEurope*) that is located in the **West Europe** region, in a web browser, browse to the DNS name of your Traffic Manager profile.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Určení názvu DNS profilu služby Traffic Manager

V tomto kurzu pro zjednodušení k prohlížení webů použijete název DNS profilu služby Traffic Manager.

Název DNS profilu služby Traffic Manager můžete určit následujícím způsobem:

1. Na panelu hledání na portálu vyhledejte název **profilu služby Traffic Manager**, který jste vytvořili v předchozí části. Ve výsledcích, které se zobrazí, klikněte na profil služby Traffic Manager.
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
1. Do webového prohlížeče na virtuálním počítači *myVMEastUS* zadejte název DNS vašeho profilu služby Traffic Manager a zobrazte váš web. Since the VM located in **East US**, you are routed to the nearest website hosted on the nearest IIS server *myIISVMEastUS* that is located in **East US**.

   ![Test profilu služby Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Next, connect to the VM *myVMWestEurope* located in **West Europe** using steps 1-5 and browse to the Traffic Manager profile domain name from this VM. Since the VM located in **West Europe**, you are now routed to the website hosted on nearest the IIS server *myIISVMWestEurope* that is located in **West Europe**.

   ![Test profilu služby Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)

## <a name="delete-the-traffic-manager-profile"></a>Odstranění profilu služby Traffic Manager

Pokud už skupiny prostředků (**ResourceGroupTM1** a **ResourceGroupTM2**) nepotřebujete, odstraňte je. Uděláte to tak, že vyberte skupinu prostředků (**ResourceGroupTM1** nebo **ResourceGroupTM2**) a pak vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Distribuce provozu do sady koncových bodů](traffic-manager-configure-weighted-routing-method.md)
