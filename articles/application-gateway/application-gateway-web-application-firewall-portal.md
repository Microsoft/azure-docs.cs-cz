---
title: Vytvoření služby application gateway s brány firewall webových aplikací – portálu Azure | Microsoft Docs
description: Postup vytvoření služby application gateway pomocí brány firewall webových aplikací pomocí portálu Azure.
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
ms.openlocfilehash: 9967813b193159b68aa0f008dae4440aa6e533dc
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Vytvoření služby application gateway pomocí brány firewall webových aplikací pomocí portálu Azure

Na portálu Azure můžete použít k vytvoření [Aplikační brána](application-gateway-introduction.md) s [brány firewall webových aplikací](application-gateway-web-application-firewall-overview.md) (firewall webových aplikací). Firewall webových aplikací používá [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) pravidla k ochraně vaší aplikace. Mezi tato pravidla patří ochranu před útoky, například typu Injektáž SQL a útoky skriptování mezi weby a hijacks relace.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření služby application gateway s povolen firewall webových aplikací
> * Vytvoření virtuálních počítačů, které slouží jako back-end serverů
> * Vytvoření účtu úložiště a konfiguraci diagnostiky

![Příklad brány firewall webových aplikací](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k portálu Azure v [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Virtuální síť je požadován pro komunikaci mezi prostředky, které vytvoříte. V tomto příkladu jsou vytvořeny dvě podsítě: jednu pro aplikační bránu a druhou pro back-end serverů. Můžete vytvořit virtuální síť ve stejnou dobu, kterou vytvoříte službu application gateway.

1. Klikněte na tlačítko **nový** najít v levém horním rohu portálu Azure.
2. Vyberte **sítě** a pak vyberte **Application Gateway** v seznamu doporučený.
3. Pro aplikační bránu, zadejte tyto hodnoty:

    - *myAppGateway* – pro název služby application gateway.
    - *myResourceGroupAG* – pro novou skupinu prostředků.
    - Vyberte *firewall webových aplikací* pro úroveň služby application gateway.

    ![Vytvořte novou aplikační bránu](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

4. Přijměte výchozí hodnoty u ostatních nastavení a potom klikněte na **OK**.
5. Klikněte na tlačítko **vyberte virtuální síť**, klikněte na tlačítko **vytvořit nový**a potom zadejte tyto hodnoty pro virtuální síť:

    - *myVNet* – pro název virtuální sítě.
    - *10.0.0.0/16* – pro adresní prostor virtuální sítě.
    - *myAGSubnet* – název podsítě.
    - *10.0.0.0/24* – adresního prostoru podsítě.

    ![Vytvoření virtuální sítě](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)

6. Klikněte na tlačítko **OK** k vytvoření virtuální sítě a podsítě.
7. Klikněte na tlačítko **zvolte veřejnou IP adresu**, klikněte na tlačítko **vytvořit nový**a potom zadejte název veřejné IP adresy. V tomto příkladu je název veřejné IP adresy *myAGPublicIPAddress*. Přijměte výchozí hodnoty u ostatních nastavení a potom klikněte na **OK**.
8. Přijměte výchozí hodnoty pro konfiguraci naslouchacího procesu nechte zakázáno brány firewall webových aplikací a pak klikněte na tlačítko **OK**.
9. Zkontrolujte nastavení na stránce Souhrn a pak klikněte na tlačítko **OK** vytvoření síťové prostředky a aplikační brány. Ho může trvat několik minut, než aplikační brány, aby lze vytvořit, počkejte na dokončení nasazení přejde k další části úspěšně.

### <a name="add-a-subnet"></a>Přidat podsíť

1. Klikněte na tlačítko **všechny prostředky** v levé nabídce a pak klikněte na tlačítko **myVNet** ze seznamu prostředků.
2. Klikněte na tlačítko **podsítě**a potom klikněte na **podsítě**.

    ![Vytvoření podsítě](./media/application-gateway-web-application-firewall-portal/application-gateway-subnet.png)

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

    ![Instalace vlastní rozšíření](./media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

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

1. Klikněte na tlačítko **všechny prostředky**a potom klikněte na **myAppGateway**.
2. Klikněte na tlačítko **back-endové fondy**. Výchozí fond byl automaticky vytvořen s aplikační brány. Klikněte na tlačítko **appGateayBackendPool**.
3. Klikněte na tlačítko **přidat cíl** pro přidání do fondu back-end každý virtuální počítač, který jste vytvořili.

    ![Přidat back-end serverů](./media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

4. Klikněte na **Uložit**.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Vytvoření účtu úložiště a konfiguraci diagnostiky

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

V tomto kurzu se Aplikační brána používá účet úložiště k ukládání dat za účelem odhalování a prevence. Analýzy protokolů nebo Centrum událostí můžete použít také k zaznamenání dat.

1. Klikněte na tlačítko **nový** najít v levém horním rohu portálu Azure.
2. Vyberte **Úložiště** a pak vyberte **Účet úložiště – objekt blob, soubor, tabulka, fronta**.
3. Zadejte název účtu úložiště, vyberte **použít existující** pro skupinu prostředků a potom vyberte **myResourceGroupAG**. V tomto příkladu je název účtu úložiště *myagstore1*. Přijměte výchozí hodnoty u ostatních nastavení a potom klikněte na **vytvořit**.

## <a name="configure-diagnostics"></a>Konfigurace diagnostiky

Konfigurace diagnostiky k záznamu dat do ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog a ApplicationGatewayFirewallLog protokoly.

1. V levé nabídce klikněte na tlačítko **všechny prostředky**a potom vyberte *myAppGateway*.
2. V části sledování, klikněte na tlačítko **protokolů diagnostiky**.
3. Klikněte na tlačítko **přidat nastavení diagnostiky**.
4. Zadejte *myDiagnosticsSettings* jako název pro nastavení diagnostiky.
5. Vyberte **archivu do účtu úložiště**a potom klikněte na **konfigurace** vyberte *myagstore1* účet úložiště, který jste dříve vytvořili.
6. Vyberte protokoly brány aplikací ke sběru a zachovat.
7. Klikněte na **Uložit**.

    ![Konfigurace diagnostiky](./media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="test-the-application-gateway"></a>Testování služby application gateway

1. Najít veřejnou IP adresu pro službu application gateway na obrazovce Přehled. Klikněte na tlačítko **všechny prostředky** a pak klikněte na **myAGPublicIPAddress**.

    ![Zaznamenejte veřejná IP adresa brány aplikace](./media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png)

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.

    ![Test aplikační brány](./media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli, jak:

> [!div class="checklist"]
> * Vytvoření služby application gateway s povolen firewall webových aplikací
> * Vytvoření virtuálních počítačů, které slouží jako back-end serverů
> * Vytvoření účtu úložiště a konfiguraci diagnostiky

Další informace o aplikačních bran a jejich přidružené prostředky, i nadále články s návody.