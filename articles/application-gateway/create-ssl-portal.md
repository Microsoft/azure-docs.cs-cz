---
title: Konfigurace aplikační brány s ukončení protokolu SSL – portál Azure portal | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat službu application gateway a přidání certifikátu pro ukončení protokolu SSL pomocí webu Azure portal.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.date: 5/15/2018
ms.author: victorh
ms.openlocfilehash: 2ae8c14b40fa13a1aa8008588fb0efb1b1d2c3f6
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159413"
---
# <a name="configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Konfigurace aplikační brány s ukončení protokolu SSL pomocí webu Azure portal

Na webu Azure portal můžete použít ke konfiguraci [služba application gateway](overview.md) certifikátem pro ukončení protokolu SSL, který používá virtuální počítače pro back-end serverů.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
> * Vytvořit aplikační bránu s certifikátem
> * Vytvoření virtuálních počítačů použít jako servery back-endu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k portálu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)

V této části použijete [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) vytvořit certifikát podepsaný svým držitelem, který nahrajete do portálu Azure při vytváření naslouchacího procesu pro službu application gateway.

Na svém místním počítači otevřete okno Windows Powershellu jako správce. Spusťte následující příkaz k vytvoření certifikátu:

```powershell
New-SelfSignedCertificate \
  -certstorelocation cert:\localmachine\my \
  -dnsname www.contoso.com
```

By měl vypadat podobně jako tato odpověď:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com

Use [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) with the Thumbprint that was returned to export a pfx file from the certificate:
```

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate \
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 \
  -FilePath c:\appgwcert.pfx \
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Virtuální sítě je potřeba ke komunikaci mezi prostředky, které vytvoříte. V tomto příkladu jsou vytvořeny dvě podsítě: jedna pro aplikační bránu a druhá pro back-endové servery. Virtuální síť můžete vytvořit současně s aplikační bránou.

1. Klikněte na tlačítko **nový** v levém horním rohu webu Azure portal.
2. Vyberte **Sítě** a potom v seznamu Doporučené vyberte **Application Gateway**.
3. Zadejte *myAppGateway* pro název služby application gateway a *myResourceGroupAG* pro novou skupinu prostředků.
4. U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.
5. Klikněte na tlačítko **zvolit virtuální síť**, klikněte na tlačítko **vytvořit nový**a potom zadejte tyto hodnoty pro virtuální síť:

    - *myVNet* – tuto hodnotu zadejte jako název virtuální sítě.
    - *10.0.0.0/16* – tuto hodnotu zadejte jako adresní prostor virtuální sítě.
    - *myBackendSubnet* – tuto hodnotu zadejte jako název podsítě.
    - *10.0.0.0/24* – tuto hodnotu zadejte jako adresní prostor podsítě.

    ![Vytvoření virtuální sítě](./media/create-ssl-portal/application-gateway-vnet.png)

6. Kliknutím na **OK** vytvořte virtuální síť a podsíť.
7. Klikněte na tlačítko **zvolte veřejnou IP adresu**, klikněte na tlačítko **vytvořit nový**a potom zadejte název veřejné IP adresy. V tomto příkladu se veřejná IP adresa nazývá *myAGPublicIPAddress*. U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.
8. Klikněte na tlačítko **HTTPS** pro protokol naslouchacího procesu a ujistěte se, že port, který je definován jako **443**.
9. Klikněte na ikonu složky a přejděte *appgwcert.pfx* certifikát, který jste předtím vytvořili, a pak ji nahrajte.
10. Zadejte *mycert1* pro název certifikátu a *Azure123456!* pro heslo a pak klikněte na tlačítko **OK**.

    ![Vytvoření nové aplikační brány](./media/create-ssl-portal/application-gateway-create.png)

11. Zkontrolujte nastavení na stránce Souhrn a pak klikněte na tlačítko **OK** k vytvoření síťových prostředků a aplikační brány. Může trvat několik minut, než pro službu application gateway, chcete-li vytvořit, počkejte na úspěšné dokončení nasazení přejde k další části.

### <a name="add-a-subnet"></a>Přidání podsítě

1. V nabídce nalevo klikněte na **Všechny prostředky** a potom v seznamu prostředků klikněte na **myVNet**.
2. Klikněte na tlačítko **podsítě**a potom klikněte na tlačítko **podsítě**.

    ![Vytvoření podsítě](./media/create-ssl-portal/application-gateway-subnet.png)

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

    ![Instalace vlastního rozšíření](./media/create-ssl-portal/application-gateway-extension.png)

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

3. Klikněte na tlačítko **všechny prostředky**a potom klikněte na tlačítko **myAppGateway**.
4. Klikněte na **Back-endové fondy**. V aplikační bráně je automaticky vytvořen výchozí fond. Klikněte na **appGatewayBackendPool**.
5. Klikněte na tlačítko **přidat cíl** přidáte každý virtuální počítač, který jste vytvořili pro back-endový fond.

    ![Přidání back-endových serverů](./media/create-ssl-portal/application-gateway-backend.png)

6. Klikněte na **Uložit**.

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

1. Klikněte na tlačítko **všechny prostředky**a potom klikněte na tlačítko **myAGPublicIPAddress**.

    ![Záznam veřejné IP adresy aplikační brány](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. Pokud jste použili certifikát podepsaný svým držitelem, přijměte upozornění zabezpečení, vyberte podrobnosti, a potom přejděte na webovou stránku:

    ![Zabezpečené upozornění](./media/create-ssl-portal/application-gateway-secure.png)

    Potom se zobrazí váš zabezpečený web služby IIS, jak je znázorněno v následujícím příkladu:

    ![Otestování základní adresy URL v aplikační bráně](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
> * Vytvořit aplikační bránu s certifikátem
> * Vytvoření virtuálních počítačů použít jako servery back-endu

Další informace o aplikačních bran a jejich souvisejících prostředcích najdete i nadále články s postupy.