---
title: Vytvoření služby Application Gateway - portálu Azure | Microsoft Docs
description: Postup vytvoření služby Application Gateway pomocí portálu Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.openlocfilehash: 0df71c445d2c5fc6827b69f708203a3b3e6e2b53
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
ms.locfileid: "33202384"
---
# <a name="create-an-application-gateway-using-the-azure-portal"></a>Vytvoření služby application gateway pomocí portálu Azure

Na portálu Azure můžete použít k vytvoření nebo správě aplikačních bran. Tento rychlý start se dozvíte, jak vytvořit síťovým prostředkům, back-end serverů a aplikační brány.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k portálu Azure v [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Virtuální síť je požadován pro komunikaci mezi prostředky, které vytvoříte. V tomto příkladu jsou vytvořeny dvě podsítě: jednu pro aplikační bránu a druhou pro back-end serverů. Můžete vytvořit virtuální síť ve stejnou dobu, kterou vytvoříte službu application gateway.

1. Klikněte na tlačítko **nový** najít v levém horním rohu portálu Azure.
2. Vyberte **sítě** a pak vyberte **Application Gateway** v seznamu doporučený.
3. Pro aplikační bránu, zadejte tyto hodnoty:

    - *myAppGateway* – pro název služby application gateway.
    - *myResourceGroupAG* – pro novou skupinu prostředků.

    ![Vytvořte novou aplikační bránu](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

4. Přijměte výchozí hodnoty u ostatních nastavení a potom klikněte na **OK**.
5. Klikněte na tlačítko **vyberte virtuální síť**, klikněte na tlačítko **vytvořit nový**a potom zadejte tyto hodnoty pro virtuální síť:

    - *myVNet* – pro název virtuální sítě.
    - *10.0.0.0/16* – pro adresní prostor virtuální sítě.
    - *myAGSubnet* – název podsítě.
    - *10.0.0.0/24* – adresního prostoru podsítě.

    ![Vytvoření virtuální sítě](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

6. Klikněte na tlačítko **OK** k vytvoření virtuální sítě a podsítě.
6. Klikněte na tlačítko **zvolte veřejnou IP adresu**, klikněte na tlačítko **vytvořit nový**a potom zadejte název veřejné IP adresy. V tomto příkladu je název veřejné IP adresy *myAGPublicIPAddress*. Přijměte výchozí hodnoty u ostatních nastavení a potom klikněte na **OK**.
8. Přijměte výchozí hodnoty pro konfiguraci naslouchacího procesu nechte zakázáno brány firewall webových aplikací a pak klikněte na tlačítko **OK**.
9. Zkontrolujte nastavení na stránce Souhrn a pak klikněte na tlačítko **OK** k vytvoření virtuální sítě, veřejnou IP adresu a aplikační brány. Ho může trvat několik minut, než aplikační brány, aby lze vytvořit, počkejte na dokončení nasazení přejde k další části úspěšně.

### <a name="add-a-subnet"></a>Přidat podsíť

1. Klikněte na tlačítko **všechny prostředky** v levé nabídce a pak klikněte na tlačítko **myVNet** ze seznamu prostředků.
2. Klikněte na tlačítko **podsítě**a potom klikněte na **podsítě**.

    ![Vytvoření podsítě](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Zadejte *myBackendSubnet* pro název podsítě a pak klikněte na tlačítko **OK**.

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V tomto příkladu vytvoříte dva virtuální počítače, který se má použít jako back-end serverů pro službu application gateway. Je také nainstalovat službu IIS na virtuálních počítačích, chcete-li ověřit, že aplikační brány byl úspěšně vytvořen.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. Klikněte na možnost **Nové**.
2. Klikněte na tlačítko **výpočetní** a pak vyberte **Windows Server 2016 Datacenter** v seznamu doporučený.
3. Pro virtuální počítač, zadejte tyto hodnoty:

    - *Můjvp* – pro název virtuálního počítače.
    - *azureuser* – uživatelské jméno správce.
    - *Azure123456!* pro heslo.
    - Vyberte **použít existující**a potom vyberte *myResourceGroupAG*.

4. Klikněte na **OK**.
5. Vyberte velikost virtuálního počítače **DS1_V2** a klikněte na **Vybrat**.
6. Ujistěte se, že **myVNet** je vybraná pro virtuální síť a podsíť je **myBackendSubnet**. 
7. Kliknutím na **Zakázáno** zakažte diagnostiku spouštění.
8. Klikněte na **OK**, na stránce souhrnu zkontrolujte nastavení a pak klikněte na **Vytvořit**.

### <a name="install-iis"></a>Instalace služby IIS

1. Otevřete prostředí pro interaktivní a ujistěte se, že je nastavena na **prostředí PowerShell**.

    ![Instalace vlastní rozšíření](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Spusťte následující příkaz pro instalaci služby IIS na virtuálním počítači: 

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

3. Vytvořit druhý virtuální počítač a nainstalujte IIS pomocí kroky, které právě dokončila. Zadejte *Můjvp2* pro její název a VMName v AzureRmVMExtension sady.

### <a name="add-backend-servers"></a>Přidat back-end serverů

3. Klikněte na tlačítko **všechny prostředky**a potom klikněte na **myAppGateway**.
4. Klikněte na tlačítko **back-endové fondy**. Výchozí fond byl automaticky vytvořen s aplikační brány. Klikněte na tlačítko **appGatewayBackendPool**.
5. Klikněte na tlačítko **přidat cíl** pro přidání do fondu back-end každý virtuální počítač, který jste vytvořili.

    ![Přidat back-end serverů](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Klikněte na **Uložit**.

## <a name="test-the-application-gateway"></a>Testování služby application gateway

1. Najít veřejnou IP adresu pro službu application gateway na obrazovce Přehled. Klikněte na tlačítko **všechny prostředky** a pak klikněte na **myAGPublicIPAddress**.

    ![Zaznamenejte veřejná IP adresa brány aplikace](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.

    ![Test aplikační brány](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, odstraňte skupinu prostředků, aplikační brány a všechny související prostředky. To pokud chcete udělat, vyberte skupinu prostředků, který obsahuje aplikační brány a klikněte na tlačítko **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili skupinu prostředků, síťové prostředky a servery back-end. Tyto prostředky se pak použít k vytvoření aplikační brány. Další informace o aplikačních bran a jejich přidružené prostředky, i nadále články s návody.
