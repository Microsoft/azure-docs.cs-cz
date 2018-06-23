---
title: Vytvoření služby application gateway s pravidel adres URL na základě cestu směrování - portálu Azure | Microsoft Docs
description: Naučte se vytvořit adresu URL na základě cesty pravidla směrování pro aplikační bránu a škálování virtuálních počítačů, nastavit pomocí portálu Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: ecb8a46c57d31c8a19f3a7b75306e42a7d3981bd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335733"
---
# <a name="create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Vytvoření služby application gateway s cesta pravidla založená na směrování pomocí portálu Azure

Na portálu Azure můžete použít ke konfiguraci [pravidla směrování na základě cesty adresy URL](url-route-overview.md) při vytváření [Aplikační brána](overview.md). V tomto kurzu vytvoříte back-endové fondy používání virtuálních počítačů. Potom můžete vytvořit pravidla směrování pro Ujistěte se, že web přenos dorazí na příslušné servery ve fondech.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření služby Application Gateway
> * Vytváření virtuálních počítačů pro back-end serverů
> * Vytvoření back-endové fondy s back-end serverů
> * Vytvořit naslouchací proces back-end
> * Vytvoření pravidla směrování na základě cesty

![Příklad směrování na základě adresy URL](./media/create-url-route-portal/scenario.png)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k portálu Azure Portal na adrese [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Virtuální síť je požadován pro komunikaci mezi prostředky, které vytvoříte. V tomto příkladu jsou vytvořeny dvě podsítě: jedna pro aplikační bránu a druhá pro back-endové servery. Virtuální síť můžete vytvořit současně s aplikační bránou.

1. Klikněte na tlačítko **nový** najít v levém horním rohu portálu Azure.
2. Vyberte **Sítě** a potom v seznamu Doporučené vyberte **Application Gateway**.
3. Pro aplikační bránu zadejte tyto hodnoty:

    - *myAppGateway* – tuto hodnotu zadejte jako název aplikační brány.
    - *myResourceGroupAG* – tuto hodnotu zadejte jako skupinu prostředků.

    ![Vytvoření nové aplikační brány](./media/create-url-route-portal/application-gateway-create.png)

4. U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.
5. Klikněte na tlačítko **vyberte virtuální síť**, klikněte na tlačítko **vytvořit nový**a potom zadejte tyto hodnoty pro virtuální síť:

    - *myVNet* – tuto hodnotu zadejte jako název virtuální sítě.
    - *10.0.0.0/16* – tuto hodnotu zadejte jako adresní prostor virtuální sítě.
    - *myBackendSubnet* – tuto hodnotu zadejte jako název podsítě.
    - *10.0.0.0/24* – tuto hodnotu zadejte jako adresní prostor podsítě.

    ![Vytvoření virtuální sítě](./media/create-url-route-portal/application-gateway-vnet.png)

6. Kliknutím na **OK** vytvořte virtuální síť a podsíť.
7. Klikněte na tlačítko **zvolte veřejnou IP adresu**, klikněte na tlačítko **vytvořit nový**a potom zadejte název veřejné IP adresy. V tomto příkladu se veřejná IP adresa nazývá *myAGPublicIPAddress*. U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.
8. Přijměte výchozí hodnoty pro konfiguraci naslouchacího procesu nechte zakázáno brány firewall webových aplikací a pak klikněte na tlačítko **OK**.
9. Zkontrolujte nastavení na stránce Souhrn a pak klikněte na tlačítko **OK** vytvoření síťové prostředky a aplikační brány. Ho může trvat několik minut, než aplikační brány, aby lze vytvořit, počkejte na dokončení nasazení přejde k další části úspěšně.

### <a name="add-a-subnet"></a>Přidání podsítě

1. V nabídce nalevo klikněte na **Všechny prostředky** a potom v seznamu prostředků klikněte na **myVNet**.
2. Klikněte na tlačítko **podsítě**a potom klikněte na **podsítě**.

    ![Vytvoření podsítě](./media/create-url-route-portal/application-gateway-subnet.png)

3. Jako název podsítě zadejte *myBackendSubnet* a potom klikněte na **OK**.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

V tomto příkladu můžete vytvořit tři virtuální počítače, který se má použít jako back-end serverů pro službu application gateway. Na virtuální počítače také nainstalujete službu IIS, abyste ověřili, že se aplikační brána úspěšně vytvořila.

1. Klikněte na možnost **Nové**.
2. Klikněte na tlačítko **výpočetní** a pak vyberte **Windows Server 2016 Datacenter** v seznamu doporučený.
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

    ![Instalace vlastního rozšíření](./media/create-url-route-portal/application-gateway-extension.png)

2. Spuštěním následujícího příkazu nainstalujte službu IIS na virtuální počítač: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/davidmu1/samplescripts/master/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
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

3. Vytvořte dva další virtuální počítače a instalace služby IIS pomocí kroky, které právě dokončila. Zadejte názvy *Můjvp2* a *myVM3* pro názvy a hodnoty VMName v AzureRmVMExtension sady.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Vytvoření back-endové fondy s virtuálními počítači

1. Klikněte na tlačítko **všechny prostředky** a pak klikněte na **myAppGateway**.
2. Klikněte na **Back-endové fondy**. V aplikační bráně je automaticky vytvořen výchozí fond. Klikněte na tlačítko **appGateayBackendPool**.
3. Klikněte na tlačítko **přidat cíl** přidat *myVM1* k appGatewayBackendPool.

    ![Přidání back-endových serverů](./media/create-url-route-portal/application-gateway-backend.png)

4. Klikněte na **Uložit**.
5. Klikněte na tlačítko **back-endové fondy** a pak klikněte na **přidat**.
6. Zadejte název *imagesBackendPool* a přidejte *Můjvp2* pomocí **přidat cíl**.
7. Klikněte na **OK**.
8. Klikněte na tlačítko **přidat** znovu a přidat jiného fondu back-end s názvem *videoBackendPool* a přidejte *myVM3* k němu.

## <a name="create-a-backend-listener"></a>Vytvořit naslouchací proces back-end

1. Klikněte na tlačítko **naslouchací procesy** a kliknutím na možnost **základní**.
2. Zadejte *myBackendListener* pro název, *myFrontendPort* pro název front-endový port a potom *8080* jako port pro naslouchací proces.
3. Klikněte na **OK**.

## <a name="create-a-path-based-routing-rule"></a>Vytvoření pravidla směrování na základě cesty

1. Klikněte na tlačítko **pravidla** a pak klikněte na **na základě cesty**.
2. Zadejte *rule2* pro název.
3. Zadejte *bitové kopie* pro název první cesty. Zadejte */images/* \* pro cestu. Vyberte **imagesBackendPool** pro fond back-end.
4. Zadejte *Video* pro název druhé cesty. Zadejte */video/* \* pro cestu. Vyberte **videoBackendPool** pro fond back-end.

    ![Vytvoření pravidla, na základě cesty](./media/create-url-route-portal/application-gateway-route-rule.png)

5. Klikněte na **OK**.

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

1. Klikněte na tlačítko **všechny prostředky**a potom klikněte na **myAGPublicIPAddress**.

    ![Záznam veřejné IP adresy aplikační brány](./media/create-url-route-portal/application-gateway-record-ag-address.png)

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. Příklad: http://40.121.222.19.

    ![Otestování základní adresy URL v aplikační bráně](./media/create-url-route-portal/application-gateway-iistest.png)

3. Změňte adresu URL k http://&lt;ip adresu&gt;: 8080/video/test.htm, nahraďte &lt;ip adresu&gt; s IP adresu a měli vidět něco podobného jako v následujícím příkladu:

    ![Testování adresy URL obrázků v aplikační bráně](./media/create-url-route-portal/application-gateway-iistest-images.png)

4. Změňte adresu URL k http://&lt;ip adresu&gt;: 8080/video/test.htm, nahraďte &lt;ip adresu&gt; s IP adresu a měli vidět něco podobného jako v následujícím příkladu:

    ![Testování adresy URL videa v aplikační bráně](./media/create-url-route-portal/application-gateway-iistest-video.png)

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli, jak

> [!div class="checklist"]
> * Vytvoření služby Application Gateway
> * Vytváření virtuálních počítačů pro back-end serverů
> * Vytvoření back-endové fondy s back-end serverů
> * Vytvořit naslouchací proces back-end
> * Vytvoření pravidla směrování na základě cesty

Další informace o aplikačních bran a jejich přidružené prostředky, i nadále články s návody.
