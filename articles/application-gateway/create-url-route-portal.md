---
title: Kurz – vytvoření služby application gateway pomocí adresy URL na základě cest pravidla směrování – Azure portal
description: V tomto kurzu se dozvíte, jak vytvořit na základě cest pravidly směrování adres URL pro application gateway a virtuálního počítače škálovací sady pomocí webu Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/18/2019
ms.author: victorh
ms.openlocfilehash: 5307f7674635fd33241e1faba9bb0b7c0432d10b
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001027"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Kurz: Vytvoření služby application gateway s na základě cest pravidla směrování pomocí webu Azure portal

Na webu Azure portal můžete použít ke konfiguraci [pravidla směrování na základě cest URL](url-route-overview.md) při vytváření [služba application gateway](overview.md). V tomto kurzu vytvoříte back-endové fondy pomocí virtuálních počítačů. Pak vytvoříte pravidla směrování, která Ujistěte se, že webový provoz dorazí na příslušné servery ve fondech.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření služby Application Gateway
> * Vytvoření virtuálního počítače pro back-end serverů
> * Vytvoření back-endové fondy s back-end serverů
> * Vytvořit naslouchací proces back-endu
> * Vytvořte pravidlo směrování na základě cest

![Příklad směrování na základě adresy URL](./media/create-url-route-portal/scenario.png)

Pokud dáváte přednost, můžete dokončit tento kurz pomocí [rozhraní příkazového řádku Azure](tutorial-url-route-cli.md) nebo [prostředí Azure PowerShell](tutorial-url-route-powershell.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Virtuální sítě je potřeba ke komunikaci mezi prostředky, které vytvoříte. V tomto příkladu jsou vytvořeny dvě podsítě: jedna pro aplikační bránu a druhá pro back-endové servery. Vytvoření virtuální sítě ve stejnou dobu vytvoření aplikační brány.

1. Vyberte **nový** v levém horním rohu webu Azure portal.
2. Vyberte **Sítě** a potom v seznamu Doporučené vyberte **Application Gateway**.
3. Pro aplikační bránu zadejte tyto hodnoty:

   - *myAppGateway* – tuto hodnotu zadejte jako název aplikační brány.
   - *myResourceGroupAG* – tuto hodnotu zadejte jako skupinu prostředků.

     ![Vytvoření nové aplikační brány](./media/create-url-route-portal/application-gateway-create.png)

4. Přijměte výchozí hodnoty pro další nastavení a pak vyberte **OK**.
5. Vyberte **zvolit virtuální síť**vyberte **vytvořit nový**a potom zadejte tyto hodnoty pro virtuální síť:

   - *myVNet* – tuto hodnotu zadejte jako název virtuální sítě.
   - *10.0.0.0/16* – tuto hodnotu zadejte jako adresní prostor virtuální sítě.
   - *myBackendSubnet* – tuto hodnotu zadejte jako název podsítě.
   - *10.0.0.0/24* – tuto hodnotu zadejte jako adresní prostor podsítě.

     ![Vytvoření virtuální sítě](./media/create-url-route-portal/application-gateway-vnet.png)

6. Vyberte **OK** k vytvoření virtuální sítě a podsítě.
7. Vyberte **zvolte veřejnou IP adresu**vyberte **vytvořit nový**a potom zadejte název veřejné IP adresy. V tomto příkladu se veřejná IP adresa nazývá *myAGPublicIPAddress*. Přijměte výchozí hodnoty pro další nastavení a pak vyberte **OK**.
8. Přijměte výchozí hodnoty pro konfiguraci naslouchacího procesu, nechat zakázané brány firewall webových aplikací a pak vyberte **OK**.
9. Zkontrolujte nastavení na stránce Souhrn a pak vyberte **OK** k vytvoření síťových prostředků a aplikační brány. Může trvat několik minut, než pro službu application gateway, chcete-li vytvořit, počkejte na úspěšné dokončení nasazení přejde k další části.

### <a name="add-a-subnet"></a>Přidání podsítě

1. Vyberte **všechny prostředky** v panelu nabídky na levé straně a pak vyberte **myVNet** v seznamu prostředků.
2. Vyberte **podsítě**a pak vyberte **podsítě**.

    ![Vytvoření podsítě](./media/create-url-route-portal/application-gateway-subnet.png)

3. Zadejte *myBackendSubnet* pro název podsítě a pak vyberte **OK**.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

V tomto příkladu vytvoříte tři virtuální počítače má být použit jako servery back-end pro službu application gateway. Také nainstalujte IIS na virtuálních počítačích, chcete-li ověřit, že službu application gateway se úspěšně vytvořil.

1. Vyberte **Nový**.
2. Vyberte **Výpočty** a potom v seznamu Doporučené vyberte **Windows Server 2016 Datacenter**.
3. Zadejte pro virtuální počítač tyto hodnoty:

    - *myVM1* – název virtuálního počítače.
    - *azureuser* – uživatelské jméno správce.
    - *Azure123456!* – heslo.
    - Vyberte **Použít existující** a pak vyberte *myResourceGroupAG*.

4. Vyberte **OK**.
5. Vyberte **DS1_V2** pro velikost virtuálního počítače a vyberte **vyberte**.
6. Zkontrolujte, že u virtuální sítě je vybrána možnost **myVNet** a u podsítě **myBackendSubnet**. 
7. Vybráním možnosti **Zakázáno** zakažte diagnostiku spouštění.
8. Vyberte **OK**, na stránce souhrnu zkontrolujte nastavení a pak vyberte **Vytvořit**.

### <a name="install-iis"></a>Instalace služby IIS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Otevřete prostředí pro interaktivní a ujistěte se, že je nastavena na **Powershellu**.

    ![Instalace vlastního rozšíření](./media/create-url-route-portal/application-gateway-extension.png)

2. Spuštěním následujícího příkazu nainstalujte službu IIS na virtuální počítač: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName myVM1 `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Vytvořte dva další virtuální počítače a instalace služby IIS pomocí kroky, které jste právě dokončili. Zadejte názvy *myVM2* a *myVM3* pro názvy a hodnoty VMName v sadě AzVMExtension.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Vytvoření back-endové fondy s virtuálními počítači

1. Vyberte **všechny prostředky** a pak vyberte **myAppGateway**.
2. Vyberte **back-endové fondy**. V aplikační bráně je automaticky vytvořen výchozí fond. Vyberte **appGatewayBackendPool**.
3. Vyberte **přidat cíl** přidat *myVM1* k appGatewayBackendPool.

    ![Přidání back-endových serverů](./media/create-url-route-portal/application-gateway-backend.png)

4. Vyberte **Uložit**.
5. Vyberte **back-endové fondy** a pak vyberte **přidat**.
6. Zadejte název *imagesBackendPool* a přidejte *myVM2* pomocí **přidat cíl**.
7. Vyberte **OK**.
8. Vyberte **přidat** přidat jiný back-endový fond s názvem *videoBackendPool* a přidejte *myVM3* k němu.

## <a name="create-a-backend-listener"></a>Vytvořit naslouchací proces back-endu

1. Vyberte **naslouchacích procesů** a vyberte položku **základní**.
2. Zadejte *myBackendListener* název *myFrontendPort* pro název front-endový port a potom *8080* jako port pro naslouchací proces.
3. Vyberte **OK**.

## <a name="create-a-path-based-routing-rule"></a>Vytvořte pravidlo směrování na základě cest

1. Vyberte **pravidla** a pak vyberte **na základě cest**.
2. Zadejte *rule2* pro název.
3. Zadejte *Imagí* pro název prvního cesty. Zadejte */images/* \* pro danou cestu. Vyberte **imagesBackendPool** pro back-endový fond.
4. Zadejte *Video* pro název druhé cesty. Zadejte */video/* \* pro danou cestu. Vyberte **videoBackendPool** pro back-endový fond.

    ![Vytvořit pravidlo na základě cest](./media/create-url-route-portal/application-gateway-route-rule.png)

5. Vyberte **OK**.

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

1. Vyberte **všechny prostředky**a pak vyberte **myAGPublicIPAddress**.

    ![Záznam veřejné IP adresy aplikační brány](./media/create-url-route-portal/application-gateway-record-ag-address.png)

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. Příklad: http://40.121.222.19.

    ![Otestování základní adresy URL v aplikační bráně](./media/create-url-route-portal/application-gateway-iistest.png)

3. Změňte adresu URL na http://&lt;ip-address&gt;: 8080/images/test.htm, nahrazení &lt;ip-address&gt; vaši IP adresu kde by měl vypadat přibližně jako v následujícím příkladu:

    ![Testování adresy URL obrázků v aplikační bráně](./media/create-url-route-portal/application-gateway-iistest-images.png)

4. Změňte adresu URL na http://&lt;ip-address&gt;: 8080/video/test.htm, nahrazení &lt;ip-address&gt; vaši IP adresu kde by měl vypadat přibližně jako v následujícím příkladu:

    ![Testování adresy URL videa v aplikační bráně](./media/create-url-route-portal/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili ve službě application gateway, odeberte skupinu prostředků. Odstraněním skupiny prostředků je taky odebrat application gateway a všechny související prostředky. 

K odebrání skupiny prostředků:

1. V nabídce vlevo na webu Azure portal vyberte **skupiny prostředků**.
2. Na **skupiny prostředků** stránky, vyhledejte **myResourceGroupAG** v seznamu, vyberte ji.
3. Na **stránce skupiny prostředků**vyberte **odstranit skupinu prostředků**.
4. Zadejte *myResourceGroupAG* pro **zadejte název skupiny prostředků** a pak vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o tom, co můžete dělat pomocí služby Azure Application Gateway](application-gateway-introduction.md)
