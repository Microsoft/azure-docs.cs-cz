---
title: Rychlý start – Směrování webového provozu pomocí služby Azure Application Gateway – Azure Portal | Microsoft Docs
description: Přečtěte si o tom, jak pomocí portálu Azure Portal vytvořit službu Azure Application Gateway, která bude směrovat webový provoz na virtuální počítače v back-endovém fondu.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 16e23f77509d2402f765981b39a30e08a2309f68
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156523"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Rychlý start: Přímé webového provozu s využitím Azure Application Gateway – Azure portal

V tomto rychlém startu se dozvíte, jak pomocí portálu Azure Portal rychle vytvořit aplikační bránu se dvěma virtuálními počítači v back-endovém fondu. Také se naučíte bránu otestovat, abyste si mohli ověřit, že správně funguje. Pomocí služby Azure Application Gateway, Přímá provozu webové aplikace ke konkrétním prostředkům pomocí: přiřazování naslouchacích procesů k portům, vytváření pravidel a přidávání prostředků do back-endový fond.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

U Azure ke komunikaci mezi prostředky, že vytvoříte potřebuje virtuální sítě. V tomto příkladu vytvoříte dvě podsítě: jednu pro application gateway a jinou pro back-end serverů. Virtuální síť můžete vytvořit současně s aplikační bránou.

1. Vyberte **vytvořit prostředek** v nabídce vlevo na webu Azure portal. **Nový** zobrazí se okno.

2. Vyberte **sítě** a pak vyberte **Application Gateway** v **doporučené** seznamu.

### <a name="basics-page"></a>Základní informace o stránce

1. Na **Základy** stránky, zadejte tyto hodnoty pro následující nastavení aplikační brány:

    - **Název**: Zadejte *myAppGateway* pro název služby application gateway.
    - **Skupina prostředků**: Vyberte **myResourceGroupAG** pro skupinu prostředků. Pokud neexistuje, vyberte **vytvořit nový** k jeho vytvoření.

    ![Vytvoření nové aplikační brány](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

2. Přijměte výchozí hodnoty pro další nastavení a pak vyberte **OK**.

### <a name="settings-page"></a>Stránka Nastavení

1. Na **nastavení** stránce v části **konfiguraci podsítě**vyberte **zvolit virtuální síť**.

2. Na **zvolte virtuální síť** stránce **vytvořit nový**a potom zadejte hodnoty pro následující nastavení virtuální sítě:

    - **Název**: Zadejte *myVNet* pro název virtuální sítě.

    - **Adresní prostor**: Zadejte *10.0.0.0/16* pro adresní prostor virtuální sítě.

    - **Název podsítě**: Zadejte *myAGSubnet* název podsítě.<br>Podsítě služby application gateway může obsahovat jenom aplikační brány. Jsou povoleny žádné další prostředky.

    - **Rozsah adres podsítě**: Zadejte *10.0.0.0/24* pro rozsah adres podsítě.

    ![Vytvoření virtuální sítě](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

3. Vyberte **OK** se vrátíte **nastavení** stránky.

4. V části **konfigurace protokolu IP front-endu**, ověřte **IP adres jako typu** je nastavena na **veřejné**. V části **veřejnou IP adresu**, ověřte **vytvořit nový** zaškrtnuto. 

5. Zadejte *myAGPublicIPAddress* pro název veřejné IP adresy. 

6. Přijměte výchozí hodnoty pro další nastavení a pak vyberte **OK**.

### <a name="summary-page"></a>Stránka souhrnu

Zkontrolujte nastavení na **Souhrn** stránce a pak vyberte **OK** vytvořit virtuální síť, veřejnou IP adresu a application gateway. Může trvat několik minut, než Azure k vytvoření služby application gateway. Počkejte na úspěšné dokončení nasazení přejde k další části.

## <a name="add-a-subnet"></a>Přidání podsítě

Přidání podsítě do virtuální sítě, kterou jste vytvořili pomocí následujících kroků:

1. Vyberte **všechny prostředky** v nabídce vlevo na webu Azure portal, zadejte *myVNet* ve vyhledávacím poli a pak vyberte **myVNet** ve výsledcích hledání.

2. Vyberte **podsítě** v levé nabídce a pak vyberte **+ podsíť**. 

    ![Vytvoření podsítě](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Z **přidat podsíť** zadejte *myBackendSubnet* pro **název** podsíť a pak vyberte **OK**.

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V tomto příkladu vytvoříte dva virtuální počítače, které Azure používá jako servery back-end pro službu application gateway. Také nainstalujte IIS na virtuálních počítačích, chcete-li ověřit, že Azure application gateway úspěšně vytvořil.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. Na webu Azure portal, vyberte **vytvořit prostředek**. **Nový** zobrazí se okno.

2. Vyberte **Compute** a pak vyberte **systému Windows Server 2016 Datacenter** v **doporučené** seznamu. **Vytvoření virtuálního počítače** se zobrazí stránka.

3. Zadejte tyto hodnoty **Základy** kartu pro následující nastavení virtuálního počítače:

    - **Skupina prostředků**: Vyberte **myResourceGroupAG** pro název skupiny prostředků.
    - **Název virtuálního počítače**: Zadejte *myVM* pro název virtuálního počítače.
    - **Uživatelské jméno**: Zadejte *azureuser* pro uživatelské jméno správce.
    - **Heslo**: Zadejte *Azure123456!* k zadání hesla správce.

4. Přijměte ostatní výchozí hodnoty a pak vyberte **Další: Disky**.  

5. Přijměte **disky** karta výchozí hodnoty a pak vyberte **Další: Sítě**.

6. Na **sítě** kartu, ověřte, že **myVNet** vybraná **virtuální síť** a **podsítě** je nastavena na  **myBackendSubnet**. Přijměte ostatní výchozí hodnoty a pak vyberte **Další: Správa**.

8. Na **správu** kartu, nastavte **Diagnostika spouštění** k **vypnout**. Přijměte ostatní výchozí hodnoty a pak vyberte **revize + vytvořit**.

9. Na **zkontrolujte + vytvořit** kartu, zkontrolujte nastavení, opravte všechny chyby ověření a potom vyberte **vytvořit**.

10. Počkejte na dokončení před pokračováním vytváření virtuálního počítače.

### <a name="install-iis"></a>Instalace služby IIS

1. Otevřít [prostředí Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Chcete-li to provést, vyberte **Cloud Shell** z horního navigačního panelu webu Azure portal a pak vyberte **Powershellu** z rozevíracího seznamu. 

    ![Instalace vlastního rozšíření](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Spuštěním následujícího příkazu nainstalujte službu IIS na virtuální počítač: 

    ```azurepowershell-interactive
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Vytvoření druhého virtuálního počítače a instalace služby IIS pomocí kroků, které jste dříve dokončili. Použití *myVM2* pro název virtuálního počítače a **VMName** nastavení **Set-AzureRmVMExtension** rutiny.

### <a name="add-backend-servers"></a>Přidání back-endových serverů

1. Vyberte **všechny prostředky**a pak vyberte **myAppGateway**.

2. Vyberte **back-endové fondy** v levé nabídce. Azure automaticky vytvoří výchozí fond **appGatewayBackendPool**, když vytvoříte službu application gateway. 

3. Vyberte **appGatewayBackendPool**.

4. V části **cíle**vyberte **virtuálního počítače** z rozevíracího seznamu.

5. V části **VIRTUÁLNÍHO počítače** a **síťová rozhraní**, vyberte **myVM** a **myVM2** virtuálních počítačů a jejich přidružené síťové rozhraní z rozevíracích seznamů.

    ![Přidání back-endových serverů](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Vyberte **Uložit**.

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

1. Najít veřejnou IP adresu pro službu application gateway na jeho **přehled** stránky.

    ![Záznam veřejné IP adresy aplikační brány](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

    Alternativně můžete vybrat **všechny prostředky**, zadejte *myAGPublicIPAddress* do vyhledávacího pole a pak vyberte ve výsledcích hledání. Azure se zobrazí na veřejnou IP adresu **přehled** stránky.

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.

    ![Otestování aplikační brány](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili ve službě application gateway, odeberte skupinu prostředků. Odstraněním skupiny prostředků je taky odebrat application gateway a všechny související prostředky. 

K odebrání skupiny prostředků:
1. V nabídce vlevo na webu Azure portal vyberte **skupiny prostředků**.
2. Na **skupiny prostředků** stránky, vyhledejte **myResourceGroupAG** v seznamu, vyberte ji.
3. Na **stránce skupiny prostředků**vyberte **odstranit skupinu prostředků**.
4. Zadejte *myResourceGroupAG* pro **zadejte název skupiny prostředků** a pak vyberte **odstranit**

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Správa webového provozu pomocí aplikační brány a Azure CLI](./tutorial-manage-web-traffic-cli.md)
