---
title: Konfigurace aplikační brány s ukončení protokolu SSL - portálu Azure | Microsoft Docs
description: Zjistěte, jak přidat certifikát pro ukončení protokolu SSL pomocí portálu Azure a konfigurace aplikační brány.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.date: 5/15/2018
ms.author: victorh
ms.openlocfilehash: c64754595ef67b7c083ee8d47da5b412467c191b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Konfigurace aplikační brány s ukončení protokolu SSL pomocí portálu Azure

Na portálu Azure můžete použít ke konfiguraci [Aplikační brána](overview.md) s certifikát pro ukončení protokolu SSL používaný virtuálních počítačů pro back-end serverů.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořit certifikát podepsaný svým držitelem
> * Vytvoření služby application gateway s certifikátem
> * Vytvoření virtuálních počítačů, které slouží jako back-end serverů

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k portálu Azure v [http://portal.azure.com](http://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem

V této části můžete použít [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) vytvořit certifikát podepsaný svým držitelem, který nahrajete do portálu Azure při vytváření naslouchací proces pro službu application gateway.

V místním počítači otevřete okno prostředí Windows PowerShell jako správce. Spusťte následující příkaz k vytvoření certifikátu:

```powershell
New-SelfSignedCertificate \
  -certstorelocation cert:\localmachine\my \
  -dnsname www.contoso.com
```

Měli byste vidět něco podobného jako této odpovědi:

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

Virtuální síť je požadován pro komunikaci mezi prostředky, které vytvoříte. V tomto příkladu jsou vytvořeny dvě podsítě: jednu pro aplikační bránu a druhou pro back-end serverů. Můžete vytvořit virtuální síť ve stejnou dobu, kterou vytvoříte službu application gateway.

1. Klikněte na tlačítko **nový** najít v levém horním rohu portálu Azure.
2. Vyberte **sítě** a pak vyberte **Application Gateway** v seznamu doporučený.
3. Zadejte *myAppGateway* pro název brány, aplikace a *myResourceGroupAG* pro novou skupinu prostředků.
4. Přijměte výchozí hodnoty u ostatních nastavení a potom klikněte na **OK**.
5. Klikněte na tlačítko **vyberte virtuální síť**, klikněte na tlačítko **vytvořit nový**a potom zadejte tyto hodnoty pro virtuální síť:

    - *myVNet* – pro název virtuální sítě.
    - *10.0.0.0/16* – pro adresní prostor virtuální sítě.
    - *myAGSubnet* – název podsítě.
    - *10.0.0.0/24* – adresního prostoru podsítě.

    ![Vytvoření virtuální sítě](./media/create-ssl-portal/application-gateway-vnet.png)

6. Klikněte na tlačítko **OK** k vytvoření virtuální sítě a podsítě.
7. Klikněte na tlačítko **zvolte veřejnou IP adresu**, klikněte na tlačítko **vytvořit nový**a potom zadejte název veřejné IP adresy. V tomto příkladu je název veřejné IP adresy *myAGPublicIPAddress*. Přijměte výchozí hodnoty u ostatních nastavení a potom klikněte na **OK**.
8. Klikněte na tlačítko **HTTPS** pro protokol naslouchacího procesu a ujistěte se, že port je definován jako **443**.
9. Klikněte na ikonu složky a vyhledejte *appgwcert.pfx* certifikát, který jste předtím vytvořili pro nahrajte ho.
10. Zadejte *mycert1* pro název certifikátu a *Azure123456!* pro heslo a pak klikněte na tlačítko **OK**.

    ![Vytvořte novou aplikační bránu](./media/create-ssl-portal/application-gateway-create.png)

11. Zkontrolujte nastavení na stránce Souhrn a pak klikněte na tlačítko **OK** vytvoření síťové prostředky a aplikační brány. Ho může trvat několik minut, než aplikační brány, aby lze vytvořit, počkejte na dokončení nasazení přejde k další části úspěšně.

### <a name="add-a-subnet"></a>Přidat podsíť

1. Klikněte na tlačítko **všechny prostředky** v levé nabídce a pak klikněte na tlačítko **myVNet** ze seznamu prostředků.
2. Klikněte na tlačítko **podsítě**a potom klikněte na **podsítě**.

    ![Vytvoření podsítě](./media/create-ssl-portal/application-gateway-subnet.png)

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

    ![Instalace vlastní rozšíření](./media/create-ssl-portal/application-gateway-extension.png)

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
4. Klikněte na tlačítko **back-endové fondy**. Výchozí fond byl automaticky vytvořen s aplikační brány. Klikněte na tlačítko **appGateayBackendPool**.
5. Klikněte na tlačítko **přidat cíl** pro přidání do fondu back-end každý virtuální počítač, který jste vytvořili.

    ![Přidat back-end serverů](./media/create-ssl-portal/application-gateway-backend.png)

6. Klikněte na **Uložit**.

## <a name="test-the-application-gateway"></a>Testování služby application gateway

1. Klikněte na tlačítko **všechny prostředky**a potom klikněte na **myAGPublicIPAddress**.

    ![Zaznamenejte veřejná IP adresa brány aplikace](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. Přijmout upozornění zabezpečení, pokud se používá certifikát podepsaný svým držitelem, vyberte podrobnosti a potom přejděte na webovou stránku:

    ![Upozornění zabezpečení](./media/create-ssl-portal/application-gateway-secure.png)

    Potom se zobrazí váš zabezpečený web služby IIS, jak je znázorněno v následujícím příkladu:

    ![Otestovat základní adresu URL v aplikační brány](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit certifikát podepsaný svým držitelem
> * Vytvoření služby application gateway s certifikátem
> * Vytvoření virtuálních počítačů, které slouží jako back-end serverů

Další informace o aplikačních bran a jejich přidružené prostředky, i nadále články s návody.