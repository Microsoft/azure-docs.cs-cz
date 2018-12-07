---
title: Vytvoření služby application gateway pomocí adresy URL na základě cest pravidla směrování – Azure portal
description: Zjistěte, jak vytvořit na základě cest pravidly směrování adres URL pro application gateway a virtuálního počítače škálovací sady pomocí webu Azure portal.
services: application-gateway
author: vhorne
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 3/26/2018
ms.author: victorh
ms.openlocfilehash: 108045c691d711dfdd12df39fe72e536f842f68f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993217"
---
# <a name="create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Vytvoření služby application gateway s na základě cest pravidla směrování pomocí webu Azure portal

Na webu Azure portal můžete použít ke konfiguraci [pravidla směrování na základě cest URL](application-gateway-url-route-overview.md) při vytváření [služba application gateway](application-gateway-introduction.md). V tomto kurzu vytvoříte back-endové fondy pomocí virtuálních počítačů. Pak vytvoříte pravidla směrování, která Ujistěte se, že webový provoz dorazí na příslušné servery ve fondech.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření služby Application Gateway
> * Vytvoření virtuálního počítače pro back-end serverů
> * Vytvoření back-endové fondy s back-end serverů
> * Vytvořit naslouchací proces back-endu
> * Vytvořte pravidlo směrování na základě cest

![Příklad směrování na základě adresy URL](./media/application-gateway-create-url-route-portal/scenario.png)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k portálu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Virtuální sítě je potřeba ke komunikaci mezi prostředky, které vytvoříte. V tomto příkladu jsou vytvořeny dvě podsítě: jedna pro aplikační bránu a druhá pro back-endové servery. Virtuální síť můžete vytvořit současně s aplikační bránou.

1. Klikněte na tlačítko **nový** v levém horním rohu webu Azure portal.
2. Vyberte **Sítě** a potom v seznamu Doporučené vyberte **Application Gateway**.
3. Pro aplikační bránu zadejte tyto hodnoty:

    - *myAppGateway* – tuto hodnotu zadejte jako název aplikační brány.
    - *myResourceGroupAG* – tuto hodnotu zadejte jako skupinu prostředků.

    ![Vytvoření nové aplikační brány](./media/application-gateway-create-url-route-portal/application-gateway-create.png)

4. U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.
5. Klikněte na tlačítko **zvolit virtuální síť**, klikněte na tlačítko **vytvořit nový**a potom zadejte tyto hodnoty pro virtuální síť:

    - *myVNet* – tuto hodnotu zadejte jako název virtuální sítě.
    - *10.0.0.0/16* – tuto hodnotu zadejte jako adresní prostor virtuální sítě.
    - *myBackendSubnet* – tuto hodnotu zadejte jako název podsítě.
    - *10.0.0.0/24* – tuto hodnotu zadejte jako adresní prostor podsítě.

    ![Vytvoření virtuální sítě](./media/application-gateway-create-url-route-portal/application-gateway-vnet.png)

6. Kliknutím na **OK** vytvořte virtuální síť a podsíť.
7. Klikněte na tlačítko **zvolte veřejnou IP adresu**, klikněte na tlačítko **vytvořit nový**a potom zadejte název veřejné IP adresy. V tomto příkladu se veřejná IP adresa nazývá *myAGPublicIPAddress*. U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.
8. Přijměte výchozí hodnoty pro konfiguraci naslouchacího procesu, nechat zakázané brány firewall webových aplikací a pak klikněte na **OK**.
9. Zkontrolujte nastavení na stránce Souhrn a pak klikněte na tlačítko **OK** k vytvoření síťových prostředků a aplikační brány. Může trvat několik minut, než pro službu application gateway, chcete-li vytvořit, počkejte na úspěšné dokončení nasazení přejde k další části.

### <a name="add-a-subnet"></a>Přidání podsítě

1. V nabídce nalevo klikněte na **Všechny prostředky** a potom v seznamu prostředků klikněte na **myVNet**.
2. Klikněte na tlačítko **podsítě**a potom klikněte na tlačítko **podsítě**.

    ![Vytvoření podsítě](./media/application-gateway-create-url-route-portal/application-gateway-subnet.png)

3. Jako název podsítě zadejte *myBackendSubnet* a potom klikněte na **OK**.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

V tomto příkladu vytvoříte tři virtuální počítače má být použit jako servery back-end pro službu application gateway. Na virtuální počítače také nainstalujete službu IIS, abyste ověřili, že se aplikační brána úspěšně vytvořila.

1. Klikněte na možnost **Nové**.
2. Klikněte na tlačítko **Compute** a pak vyberte **systému Windows Server 2016 Datacenter** v seznamu vybrané.
3. Zadejte pro virtuální počítač tyto hodnoty:

    - *myVM1* – název virtuálního počítače.
    - *azureuser* – uživatelské jméno správce.
    - *Azure123456!* – heslo.
    - Vyberte **Použít existující** a pak vyberte *myResourceGroupAG*.

4. Klikněte na **OK**.
5. Vyberte velikost virtuálního počítače **DS1_V2** a klikněte na **Vybrat**.
6. Zkontrolujte, že u virtuální sítě je vybrána možnost **myVNet** a u podsítě **myBackendSubnet**. 
7. Kliknutím na **Zakázáno** zakažte diagnostiku spouštění.
8. Klikněte na **OK**, na stránce souhrnu zkontrolujte nastavení a pak klikněte na **Vytvořit**.

### <a name="install-iis"></a>Instalace služby IIS

1. Otevřete interaktivní prostředí a zkontrolujte, že je nastaveno na **PowerShell**.

    ![Instalace vlastního rozšíření](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Spuštěním následujícího příkazu nainstalujte službu IIS na virtuální počítač: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName myVM1 `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Vytvořte dva další virtuální počítače a instalace služby IIS pomocí kroky, které jste právě dokončili. Zadejte názvy *myVM2* a *myVM3* pro názvy a hodnoty VMName v Set-AzureRmVMExtension.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Vytvoření back-endové fondy s virtuálními počítači

1. Klikněte na tlačítko **všechny prostředky** a potom klikněte na tlačítko **myAppGateway**.
2. Klikněte na **Back-endové fondy**. V aplikační bráně je automaticky vytvořen výchozí fond. Klikněte na **appGatewayBackendPool**.
3. Klikněte na tlačítko **přidat cíl** přidat *myVM1* k appGatewayBackendPool.

    ![Přidání back-endových serverů](./media/application-gateway-create-url-route-portal/application-gateway-backend.png)

4. Klikněte na **Uložit**.
5. Klikněte na tlačítko **back-endové fondy** a potom klikněte na tlačítko **přidat**.
6. Zadejte název *imagesBackendPool* a přidejte *myVM2* pomocí **přidat cíl**.
7. Klikněte na **OK**.
8. Klikněte na tlačítko **přidat** přidat jiný back-endový fond s názvem *videoBackendPool* a přidejte *myVM3* k němu.

## <a name="create-a-backend-listener"></a>Vytvořit naslouchací proces back-endu

1. Klikněte na tlačítko **naslouchacích procesů** a kliknutím na **základní**.
2. Zadejte *myBackendListener* název *myFrontendPort* pro název front-endový port a potom *8080* jako port pro naslouchací proces.
3. Klikněte na **OK**.

## <a name="create-a-path-based-routing-rule"></a>Vytvořte pravidlo směrování na základě cest

1. Klikněte na tlačítko **pravidla** a potom klikněte na tlačítko **na základě cest**.
2. Zadejte *rule2* pro název.
3. Zadejte *Imagí* pro název prvního cesty. Zadejte */images/* \* pro danou cestu. Vyberte **imagesBackendPool** pro back-endový fond.
4. Zadejte *Video* pro název druhé cesty. Zadejte */video/* \* pro danou cestu. Vyberte **videoBackendPool** pro back-endový fond.

    ![Vytvořit pravidlo na základě cest](./media/application-gateway-create-url-route-portal/application-gateway-route-rule.png)

5. Klikněte na **OK**.

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

1. Klikněte na tlačítko **všechny prostředky**a potom klikněte na tlačítko **myAGPublicIPAddress**.

    ![Záznam veřejné IP adresy aplikační brány](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. Příklad: http:// http://40.121.222.19.

    ![Otestování základní adresy URL v aplikační bráně](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

3. Změňte adresu URL na http://&lt;ip-address&gt;: 8080/images/test.htm, nahraďte &lt;ip-address&gt; vaši IP adresu kde by měl vypadat přibližně jako v následujícím příkladu:

    ![Testování adresy URL obrázků v aplikační bráně](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

4. Změňte adresu URL na http://&lt;ip-address&gt;: 8080/video/test.htm, nahraďte &lt;ip-address&gt; vaši IP adresu kde by měl vypadat přibližně jako v následujícím příkladu:

    ![Testování adresy URL videa v aplikační bráně](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak

> [!div class="checklist"]
> * Vytvoření služby Application Gateway
> * Vytvoření virtuálního počítače pro back-end serverů
> * Vytvoření back-endové fondy s back-end serverů
> * Vytvořit naslouchací proces back-endu
> * Vytvořte pravidlo směrování na základě cest

Další informace o aplikačních bran a jejich souvisejících prostředcích najdete i nadále články s postupy.
