---
title: Rychlý start – Směrování webového provozu pomocí služby Azure Application Gateway – Azure Portal | Microsoft Docs
description: Přečtěte si o tom, jak pomocí portálu Azure Portal vytvořit službu Azure Application Gateway, která bude směrovat webový provoz na virtuální počítače v back-endovém fondu.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 11/15/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 6ad839b9cf1179e282b9163df5a38e13417408e2
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706224"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Rychlý start: Směrování webového provozu pomocí služby Azure Application Gateway – Azure Portal

Pomocí služby Azure Application Gateway můžete směrovat webový provoz aplikací na konkrétní prostředky tak, že přiřadíte naslouchací procesy portům, vytvoříte pravidla a přidáte prostředky do back-endového fondu.

V tomto rychlém startu se dozvíte, jak pomocí portálu Azure Portal rychle vytvořit aplikační bránu se dvěma virtuálními počítači v back-endovém fondu. Také se naučíte bránu otestovat, abyste si mohli ověřit, že správně funguje.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [http://portal.azure.com](http://portal.azure.com).

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Virtuální sítě je potřeba ke komunikaci mezi prostředky, které vytvoříte. V tomto příkladu jsou vytvořeny dvě podsítě: jedna pro aplikační bránu a druhá pro back-endové servery. Virtuální síť můžete vytvořit současně s aplikační bránou.

1. Klikněte na **Vytvořit prostředek** v levém horním rohu portálu Azure Portal.
2. Klikněte na tlačítko **sítě** a potom klikněte na tlačítko **Application Gateway** v seznamu vybrané.

### <a name="basics"></a>Základy

1. Pro aplikační bránu zadejte tyto hodnoty:

    - *myAppGateway* – tuto hodnotu zadejte jako název aplikační brány.
    - *myResourceGroupAG* – tuto hodnotu zadejte jako skupinu prostředků.

    ![Vytvoření nové aplikační brány](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

2. U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.

### <a name="settings"></a>Nastavení

1. Klikněte na tlačítko **zvolit virtuální síť**, klikněte na tlačítko **vytvořit nový**a potom zadejte tyto hodnoty pro virtuální síť:

    - *myVNet* – tuto hodnotu zadejte jako název virtuální sítě.
    - *10.0.0.0/16* – tuto hodnotu zadejte jako adresní prostor virtuální sítě.
    - *myBackendSubnet* – tuto hodnotu zadejte jako název podsítě.
    - *10.0.0.0/24* – rozsah adres podsítě.

    ![Vytvoření virtuální sítě](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

6. Klikněte na tlačítko **OK** přejděte zpět na stránku nastavení.
7. V části **konfigurace protokolu IP front-endu** zajistit **IP adres jako typu** je nastavena na **veřejné**a v části **veřejnou IP adresu**, zkontrolujte **Vytvořit nový** zaškrtnuto. Typ *myAGPublicIPAddress* pro název veřejné IP adresy. U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.

### <a name="summary"></a>Souhrn

Projděte si nastavení na stránce souhrnu a potom kliknutím na **OK** vytvořte virtuální síť, veřejnou IP adresu a aplikační bránu. Vytvoření aplikační brány může trvat několik minut. Počkejte na úspěšné dokončení nasazení přejde k další části.

## <a name="add-a-subnet"></a>Přidání podsítě

1. V nabídce nalevo klikněte na **Všechny prostředky** a potom v seznamu prostředků klikněte na **myVNet**.
2. Klikněte na tlačítko **podsítě**a potom klikněte na tlačítko **+ podsíť**.

    ![Vytvoření podsítě](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Jako název podsítě zadejte *myBackendSubnet* a potom klikněte na **OK**.

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V tomto příkladu vytvoříte dva virtuální počítače, které se používají jako servery back-end pro službu application gateway. Na virtuální počítače také nainstalujete službu IIS, abyste ověřili, že se aplikační brána úspěšně vytvořila.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. Na webu Azure Portal klikněte na **Vytvořit prostředek**.
2. Klikněte na tlačítko **Compute** a pak vyberte **systému Windows Server 2016 Datacenter** v seznamu vybrané.
3. Zadejte pro virtuální počítač tyto hodnoty:

    - *myResourceGroupAG* pro skupinu prostředků.
    - *myVM1* – název virtuálního počítače.
    - *azureuser* – uživatelské jméno správce.
    - *Azure123456!* – jako heslo.

   Přijměte ostatní výchozí hodnoty a klikněte na tlačítko **Další: disky**.
4. Přijměte výchozí nastavení disku a klikněte na tlačítko **Další: sítě**.
5. Zkontrolujte, že u virtuální sítě je vybrána možnost **myVNet** a u podsítě **myBackendSubnet**.
6. Přijměte ostatní výchozí hodnoty a klikněte na tlačítko **Další: Správa**.
7. Klikněte na tlačítko **vypnout** zakažte diagnostiku spouštění. Přijměte ostatní výchozí hodnoty a klikněte na tlačítko **revize + vytvořit**.
8. Zkontrolujte nastavení na stránce Souhrn a pak klikněte na tlačítko **vytvořit**.
9. Počkejte na dokončení před pokračováním vytváření virtuálního počítače.

### <a name="install-iis"></a>Instalace služby IIS

1. Otevřete prostředí pro interaktivní a ujistěte se, že je nastavena na **Powershellu**.

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

3. Vytvořte druhý virtuální počítač a stejným postupem na něj nainstalujte službu IIS. Jako název a VMName v Set-AzureRmVMExtension zadejte *myVM2*.

### <a name="add-backend-servers"></a>Přidání back-endových serverů

1. Klikněte na tlačítko **všechny prostředky**a potom klikněte na tlačítko **myAppGateway**.
4. Klikněte na **Back-endové fondy**. V aplikační bráně je automaticky vytvořen výchozí fond. Klikněte na **appGatewayBackendPool**.
5. V části **cíle**, klikněte na tlačítko **IP adresu nebo plně kvalifikovaný název domény** vyberte **virtuálního počítače**.
6. V části **virtuální počítač**, přidejte myVM a myVM2 virtuálních počítačů a jejich přidružené síťové rozhraní.

    ![Přidání back-endových serverů](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Klikněte na **Uložit**.

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

1. Na obrazovce Přehled vyhledejte veřejnou IP adresu aplikační brány. Klikněte na tlačítko **všechny prostředky** a potom klikněte na tlačítko **myAGPublicIPAddress**.

    ![Záznam veřejné IP adresy aplikační brány](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.

    ![Otestování aplikační brány](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, služba application gateway a všechny související prostředky. Provedete to výběrem skupiny prostředků, která obsahuje aplikační bránu, a kliknutím na **Odstranit**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Správa webového provozu pomocí aplikační brány a Azure CLI](./tutorial-manage-web-traffic-cli.md)
