---
title: Vytvoření služby application gateway pomocí firewallu webových aplikací – Azure portal | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit službu application gateway s firewallem webových aplikací s využitím webu Azure portal.
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
ms.openlocfilehash: 12ef6447e53ba0da3fbd3d56749d7798c49a2cfe
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993943"
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Vytvoření služby application gateway s firewallem webových aplikací, pomocí webu Azure portal

Na webu Azure portal můžete použít k vytvoření [služba application gateway](application-gateway-introduction.md) s [firewallu webových aplikací](application-gateway-web-application-firewall-overview.md) (WAF). WAF používá k ochraně aplikace pravidla [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project). Tato pravidla zahrnují ochranu před útoky, jako je injektáž SQL, skriptování mezi weby a krádeže relací.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořit aplikační bránu se zapnutým Firewallem webových aplikací
> * Vytvoření virtuálních počítačů použít jako servery back-endu
> * Vytvořit účet úložiště a nakonfigurovat diagnostiku

![Příklad firewallu webových aplikací](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k portálu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Virtuální sítě je potřeba ke komunikaci mezi prostředky, které vytvoříte. V tomto příkladu jsou vytvořeny dvě podsítě: jedna pro aplikační bránu a druhá pro back-endové servery. Virtuální síť můžete vytvořit současně s aplikační bránou.

1. Klikněte na tlačítko **nový** v levém horním rohu webu Azure portal.
2. Vyberte **Sítě** a potom v seznamu Doporučené vyberte **Application Gateway**.
3. Pro aplikační bránu zadejte tyto hodnoty:

    - *myAppGateway* – tuto hodnotu zadejte jako název aplikační brány.
    - *myResourceGroupAG* – tuto hodnotu zadejte jako skupinu prostředků.
    - Vyberte *WAF* pro úroveň služby application gateway.

    ![Vytvoření nové aplikační brány](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

4. U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.
5. Klikněte na tlačítko **zvolit virtuální síť**, klikněte na tlačítko **vytvořit nový**a potom zadejte tyto hodnoty pro virtuální síť:

    - *myVNet* – tuto hodnotu zadejte jako název virtuální sítě.
    - *10.0.0.0/16* – tuto hodnotu zadejte jako adresní prostor virtuální sítě.
    - *myBackendSubnet* – tuto hodnotu zadejte jako název podsítě.
    - *10.0.0.0/24* – tuto hodnotu zadejte jako adresní prostor podsítě.

    ![Vytvoření virtuální sítě](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)

6. Kliknutím na **OK** vytvořte virtuální síť a podsíť.
7. Klikněte na tlačítko **zvolte veřejnou IP adresu**, klikněte na tlačítko **vytvořit nový**a potom zadejte název veřejné IP adresy. V tomto příkladu se veřejná IP adresa nazývá *myAGPublicIPAddress*. U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.
8. Přijměte výchozí hodnoty pro konfiguraci naslouchacího procesu, nechat zakázané brány firewall webových aplikací a pak klikněte na **OK**.
9. Zkontrolujte nastavení na stránce Souhrn a pak klikněte na tlačítko **OK** k vytvoření síťových prostředků a aplikační brány. Může trvat několik minut, než pro službu application gateway, chcete-li vytvořit, počkejte na úspěšné dokončení nasazení přejde k další části.

### <a name="add-a-subnet"></a>Přidání podsítě

1. V nabídce nalevo klikněte na **Všechny prostředky** a potom v seznamu prostředků klikněte na **myVNet**.
2. Klikněte na tlačítko **podsítě**a potom klikněte na tlačítko **podsítě**.

    ![Vytvoření podsítě](./media/application-gateway-web-application-firewall-portal/application-gateway-subnet.png)

3. Jako název podsítě zadejte *myBackendSubnet* a potom klikněte na **OK**.

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V tomto příkladu vytvoříte dva virtuální počítače, které se použijí jako servery back-end pro aplikační bránu. Na virtuální počítače také nainstalujete službu IIS, abyste ověřili, že se aplikační brána úspěšně vytvořila.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

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

    ![Instalace vlastního rozšíření](./media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

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
2. Klikněte na **Back-endové fondy**. V aplikační bráně je automaticky vytvořen výchozí fond. Klikněte na tlačítko **appGateayBackendPool**.
3. Klikněte na tlačítko **přidat cíl** přidáte každý virtuální počítač, který jste vytvořili pro back-endový fond.

    ![Přidání back-endových serverů](./media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

4. Klikněte na **Uložit**.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Vytvořit účet úložiště a nakonfigurovat diagnostiku

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

V tomto kurzu používá aplikační brána k ukládání dat účet úložiště, aby bylo možné je rozpoznat a také z preventivních důvodů. K záznamu dat můžete také použít Log Analytics nebo Centrum událostí.

1. Klikněte na tlačítko **nový** v levém horním rohu webu Azure portal.
2. Vyberte **Úložiště** a pak vyberte **Účet úložiště – objekt blob, soubor, tabulka, fronta**.
3. Zadejte název účtu úložiště vyberte **použít existující** pro skupinu prostředků a pak vyberte **myResourceGroupAG**. V tomto příkladu je název účtu úložiště *myagstore1*. Přijměte výchozí hodnoty pro další nastavení a klikněte na **vytvořit**.

## <a name="configure-diagnostics"></a>Konfigurace diagnostiky

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

1. Na obrazovce Přehled vyhledejte veřejnou IP adresu aplikační brány. Klikněte na tlačítko **všechny prostředky** a potom klikněte na tlačítko **myAGPublicIPAddress**.

    ![Záznam veřejné IP adresy aplikační brány](./media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png)

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.

    ![Otestování aplikační brány](./media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvořit aplikační bránu se zapnutým Firewallem webových aplikací
> * Vytvoření virtuálních počítačů použít jako servery back-endu
> * Vytvořit účet úložiště a nakonfigurovat diagnostiku

Další informace o aplikačních bran a jejich souvisejících prostředcích najdete i nadále články s postupy.