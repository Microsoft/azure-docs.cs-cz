---
title: Kurz – vytvoření služby application gateway pomocí firewallu webových aplikací – Azure portal | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit službu application gateway s firewallem webových aplikací s využitím webu Azure portal.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/25/2019
ms.author: victorh
ms.openlocfilehash: 1284ddec4cd9cea3ea53c20d437550405dd614d9
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905864"
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Vytvoření služby application gateway s firewallem webových aplikací, pomocí webu Azure portal

> [!div class="op_single_selector"]
>
> - [portál Azure](application-gateway-web-application-firewall-portal.md)
> - [PowerShell](tutorial-restrict-web-traffic-powershell.md)
> - [Azure CLI](tutorial-restrict-web-traffic-cli.md)
>
> 

V tomto kurzu se dozvíte, jak pomocí webu Azure portal k vytvoření [služba application gateway](application-gateway-introduction.md) s [firewallu webových aplikací](application-gateway-web-application-firewall-overview.md) (WAF). WAF používá k ochraně aplikace pravidla [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project). Tato pravidla zahrnují ochranu před útoky, jako je injektáž SQL, skriptování mezi weby a krádeže relací. Po vytvoření aplikační brány, můžete otestovat a ujistěte se, že funguje správně. Pomocí služby Azure Application Gateway řízení provozu webové aplikace ke konkrétním prostředkům prostřednictvím přiřazení naslouchacích procesů k portům, vytváření pravidel a přidávání prostředků do back-endový fond. Z důvodu zjednodušení tento článek používá jednoduché uspořádání s veřejnou IP adresu front-endu, základní naslouchací proces pro hostování jedné lokalitě v této brány application gateway, dva virtuální počítače pro back-endového fondu a pravidel směrování základní požadavek.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořit aplikační bránu se zapnutým Firewallem webových aplikací
> * Vytvoření virtuálních počítačů použít jako servery back-endu
> * Vytvořit účet úložiště a nakonfigurovat diagnostiku

![Příklad firewallu webových aplikací](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

U Azure ke komunikaci mezi prostředky, že vytvoříte potřebuje virtuální sítě. Můžete vytvořit novou virtuální síť, nebo použijte již existující. V tomto příkladu vytvoříme nové virtuální sítě. Virtuální síť můžete vytvořit současně s aplikační bránou. Do samostatných podsítí se vytvářejí instance Application Gateway. V tomto příkladu vytvoříte dvě podsítě: jednu pro application gateway a jinou pro back-end serverů.

Vyberte **vytvořit prostředek** v nabídce vlevo na webu Azure portal. **Nový** zobrazí se okno.

Vyberte **sítě** a pak vyberte **Application Gateway** v **doporučené** seznamu.

### <a name="basics-page"></a>Základní informace o stránce

1. Na **Základy** stránky, zadejte tyto hodnoty pro následující nastavení aplikační brány:
   - **Název**: Zadejte *myAppGateway* pro název služby application gateway.
   - **Skupina prostředků**: Vyberte **myResourceGroupAG** pro skupinu prostředků. Pokud neexistuje, vyberte **vytvořit nový** k jeho vytvoření.
   - Vyberte *WAF* pro úroveň služby application gateway.![ Vytvořit novou aplikační bránu](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.

### <a name="settings-page"></a>Stránka Nastavení

1. Na **nastavení** stránce v části **konfiguraci podsítě**vyberte **zvolit virtuální síť**. <br>
2. Na **zvolte virtuální síť** stránce **vytvořit nový**a potom zadejte hodnoty pro následující nastavení virtuální sítě:
   - **Název**: Zadejte *myVNet* pro název virtuální sítě.
   - **Adresní prostor**: Zadejte *10.0.0.0/16* pro adresní prostor virtuální sítě.
   - **Název podsítě**: Zadejte *myAGSubnet* název podsítě.<br>Podsítě služby application gateway může obsahovat jenom aplikační brány. Jsou povoleny žádné další prostředky.
   - **Rozsah adres podsítě**: Zadejte *10.0.0.0/24* pro rozsah adres podsítě.![ Vytvoření virtuální sítě](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)
3. Kliknutím na **OK** vytvořte virtuální síť a podsíť.
4. Zvolte **konfigurace protokolu IP front-endu**. V části **konfigurace protokolu IP front-endu**, ověřte **IP adres jako typu** je nastavena na **veřejné**. V části **veřejnou IP adresu**, ověřte **vytvořit nový** zaškrtnuto. <br>Můžete nakonfigurovat IP front-endu být veřejné a privátní podle vašemu případu použití. V tomto příkladu vybereme možnost veřejná IP adresa front-endu. 
5. Zadejte *myAGPublicIPAddress* pro název veřejné IP adresy. 
6. Přijměte výchozí hodnoty pro další nastavení a pak vyberte **OK**.<br>Vybereme možnost výchozí hodnoty v tomto článku pro zjednodušení, ale můžete nakonfigurovat vlastní hodnoty u ostatních nastavení v závislosti na vašemu případu použití 

### <a name="summary-page"></a>Stránka souhrnu

Zkontrolujte nastavení na **Souhrn** stránce a pak vyberte **OK** vytvořit virtuální síť, veřejnou IP adresu a application gateway. Může trvat několik minut, než Azure k vytvoření služby application gateway. Počkejte na úspěšné dokončení nasazení přejde k další části.

## <a name="add-backend-pool"></a>Přidat back-endový fond

Back-endového fondu se používá k směrovat požadavky do back-end serverů, které budou sloužit pro požadavek. Back-endové fondy se může skládat ze síťových rozhraní škálovací sady virtuálních počítačů, veřejné IP adresy, interní IP adresy nebo plně kvalifikované názvy (plně kvalifikovaný název domény) a více tenantů back EndY, jako je Azure App Service. Budete muset přidat cíle vaší back-endu do back-endový fond.

V tomto příkladu budeme používat virtuální počítače jako cíl back-endu. Můžeme použít stávající virtuální počítače nebo vytvořit nové. V tomto příkladu vytvoříte dva virtuální počítače, které Azure používá jako servery back-end pro službu application gateway. Chcete-li to provést, provedeme následující:

1. Vytvořit novou podsíť *myBackendSubnet*, ve kterém se vytvoří nové virtuální počítače. 
2. Vytvořit 2 nové virtuální počítače, *myVM* a *myVM2*, které se použijí jako servery back-end.
3. Instalace služby IIS na virtuálních počítačích a ověřte, že službu application gateway vytvořil úspěšně.
4. Přidání serverů back-endu do back-endový fond.

### <a name="add-a-subnet"></a>Přidání podsítě

Přidání podsítě do virtuální sítě, kterou jste vytvořili pomocí následujících kroků:

1. Vyberte **všechny prostředky** v nabídce vlevo na webu Azure portal, zadejte *myVNet* ve vyhledávacím poli a pak vyberte **myVNet** ve výsledcích hledání.

2. Vyberte **podsítě** v levé nabídce a pak vyberte **+ podsíť**. 

   ![Vytvoření podsítě](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Z **přidat podsíť** zadejte *myBackendSubnet* pro **název** podsíť a pak vyberte **OK**.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. Na webu Azure portal, vyberte **vytvořit prostředek**. **Nový** zobrazí se okno.
2. Vyberte **Compute** a pak vyberte **systému Windows Server 2016 Datacenter** v **doporučené** seznamu. **Vytvoření virtuálního počítače** se zobrazí stránka.<br>Služba Application Gateway může směrovat provoz na libovolný typ virtuálního počítače, v jeho back-endový fond. V tomto příkladu použijete Windows Server 2016 Datacenter.
3. Zadejte tyto hodnoty **Základy** kartu pro následující nastavení virtuálního počítače:
   - **Skupina prostředků**: Vyberte **myResourceGroupAG** pro název skupiny prostředků.
   - **Název virtuálního počítače**: Zadejte *myVM* pro název virtuálního počítače.
   - **Uživatelské jméno**: Zadejte *azureuser* pro uživatelské jméno správce.
   - **Heslo**: Enter *Azure123456!* k zadání hesla správce.
4. Přijměte ostatní výchozí hodnoty a pak vyberte **Další: Disky**.  
5. Přijměte **disky** karta výchozí hodnoty a pak vyberte **Další: Sítě**.
6. Na **sítě** kartu, ověřte, že **myVNet** vybraná **virtuální síť** a **podsítě** je nastavena na  **myBackendSubnet**. Přijměte ostatní výchozí hodnoty a pak vyberte **Další: Správa**.<br>Služba Application Gateway může komunikovat s instancemi mimo virtuální síť, která je v, ale potřebujeme zajistit, že existuje připojení pomocí IP adresy. 
7. Na **správu** kartu, nastavte **Diagnostika spouštění** k **vypnout**. Přijměte ostatní výchozí hodnoty a pak vyberte **revize + vytvořit**.
8. Na **zkontrolujte + vytvořit** kartu, zkontrolujte nastavení, opravte všechny chyby ověření a potom vyberte **vytvořit**.
9. Počkejte na dokončení před pokračováním vytváření virtuálního počítače.

### <a name="install-iis-for-testing"></a>Instalace služby IIS pro účely testování

V tomto příkladu jsme se instalace služby IIS na virtuální počítače pouze pro účely ověření, že Azure application gateway úspěšně vytvořil. 

1. Otevřít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Chcete-li to provést, vyberte **Cloud Shell** z horního navigačního panelu webu Azure portal a pak vyberte **Powershellu** z rozevíracího seznamu. 

   ![Instalace vlastního rozšíření](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Spuštěním následujícího příkazu nainstalujte službu IIS na virtuální počítač: 

   ```azurepowershell-interactive
   Set-AzVMExtension `
     -ResourceGroupName myResourceGroupAG `
     -ExtensionName IIS `
     -VMName myVM `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.4 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
   ```

3. Vytvoření druhého virtuálního počítače a instalace služby IIS pomocí kroků, které jste dříve dokončili. Použití *myVM2* pro název virtuálního počítače a **VMName** nastavení **Set-AzVMExtension** rutiny.

### <a name="add-backend-servers-to-backend-pool"></a>Přidání serverů back-endu do back-endový fond

1. Vyberte **všechny prostředky**a pak vyberte **myAppGateway**.

2. Vyberte **back-endové fondy** v levé nabídce. Azure automaticky vytvoří výchozí fond **appGatewayBackendPool**, když vytvoříte službu application gateway. 

3. Vyberte **appGatewayBackendPool**.

4. V části **cíle**vyberte **virtuálního počítače** z rozevíracího seznamu.

5. V části **VIRTUÁLNÍHO počítače** a **síťová rozhraní**, vyberte **myVM** a **myVM2** virtuálních počítačů a jejich přidružené síťové rozhraní z rozevíracích seznamů.

   ![Přidání back-endových serverů](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Vyberte **Uložit**.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Vytvořit účet úložiště a nakonfigurovat diagnostiku

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

V tomto kurzu používá aplikační brána k ukládání dat účet úložiště, aby bylo možné je rozpoznat a také z preventivních důvodů. Protokoly Azure monitoru nebo centra událostí můžete použít také k zaznamenání dat.

1. Klikněte na tlačítko **nový** v levém horním rohu webu Azure portal.
2. Vyberte **Úložiště** a pak vyberte **Účet úložiště – objekt blob, soubor, tabulka, fronta**.
3. Zadejte název účtu úložiště vyberte **použít existující** pro skupinu prostředků a pak vyberte **myResourceGroupAG**. V tomto příkladu je název účtu úložiště *myagstore1*. Přijměte výchozí hodnoty pro další nastavení a klikněte na **vytvořit**.

### <a name="configure-diagnostics"></a>Konfigurace diagnostiky

Nakonfigurujte diagnostiku, aby se data zaznamenávala do protokolů ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog a ApplicationGatewayFirewallLog.

1. V nabídce vlevo klikněte na tlačítko **všechny prostředky**a pak vyberte *myAppGateway*.
2. V části monitorování, klikněte na tlačítko **diagnostické protokoly**.
3. Klikněte na tlačítko **přidejte nastavení diagnostiky**.
4. Zadejte *myDiagnosticsSettings* jako název pro nastavení diagnostiky.
5. Vyberte **archivovat do účtu úložiště**a potom klikněte na tlačítko **konfigurovat** vyberte *myagstore1* účet úložiště, který jste předtím vytvořili.
6. Vyberte protokolů application gateway ke shromáždění a zachovat.
7. Klikněte na **Uložit**.

    ![Konfigurace diagnostiky](./media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

I když služba IIS není nutné k vytvoření služby application gateway, nainstalovaná v tomto rychlém startu k ověření úspěšného vytvoření aplikační brány Azure. Služba IIS použijte k otestování application gateway:

1. Najít veřejnou IP adresu pro službu application gateway na jeho **přehled** stránky.![ Zaznamenat veřejné IP adresy brány aplikace](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)Alternativně můžete vybrat **všechny prostředky**, zadejte *myAGPublicIPAddress* do vyhledávacího pole a vyberte ho v hledání výsledky. Azure se zobrazí na veřejnou IP adresu **přehled** stránky.
2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.
3. Zkontrolujte odpovědi. Platné odpovědi ověří, jestli application gateway se úspěšně vytvořil, se může úspěšně připojit s back-endu.![Otestování aplikační brány](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili ve službě application gateway, odeberte skupinu prostředků. Odstraněním skupiny prostředků je taky odebrat application gateway a všechny související prostředky. 

K odebrání skupiny prostředků:

1. V nabídce vlevo na webu Azure portal vyberte **skupiny prostředků**.
2. Na **skupiny prostředků** stránky, vyhledejte **myResourceGroupAG** v seznamu, vyberte ji.
3. Na **stránce skupiny prostředků**vyberte **odstranit skupinu prostředků**.
4. Zadejte *myResourceGroupAG* pro **zadejte název skupiny prostředků** a pak vyberte **odstranit**

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvořit aplikační bránu se zapnutým Firewallem webových aplikací
> * Vytvoření virtuálních počítačů použít jako servery back-endu
> * Vytvořit účet úložiště a nakonfigurovat diagnostiku

Další informace o aplikačních bran a jejich souvisejících prostředcích najdete i nadále články s postupy.
