---
title: Kurz – konfigurace aplikační brány s ukončení protokolu SSL – portál Azure portal
description: V tomto kurzu se dozvíte, jak nakonfigurovat službu application gateway a přidání certifikátu pro ukončení protokolu SSL pomocí webu Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/17/2019
ms.author: victorh
ms.openlocfilehash: f3ba3eb12dc85a72c4e49c374e62209b83400d33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "66134412"
---
# <a name="tutorial-configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Kurz: Konfigurace aplikační brány s ukončení protokolu SSL pomocí webu Azure portal

Na webu Azure portal můžete použít ke konfiguraci [služba application gateway](overview.md) certifikátem pro ukončení protokolu SSL, který používá virtuální počítače pro back-end serverů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
> * Vytvořit aplikační bránu s certifikátem
> * Vytvoření virtuálních počítačů použít jako servery back-endu
> * Otestování aplikační brány

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)

V této části použijete [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) vytvořit certifikát podepsaný svým držitelem. Nahrajte certifikát na webu Azure portal, při vytváření naslouchacího procesu pro službu application gateway.

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

1. Vyberte **nový** v levém horním rohu webu Azure portal.
2. Vyberte **Sítě** a potom v seznamu Doporučené vyberte **Application Gateway**.
3. Zadejte *myAppGateway* pro název služby application gateway a *myResourceGroupAG* pro novou skupinu prostředků.
4. Přijměte výchozí hodnoty pro další nastavení a pak vyberte **OK**.
5. Vyberte **zvolit virtuální síť**vyberte **vytvořit nový**a potom zadejte tyto hodnoty pro virtuální síť:

   - *myVNet* – tuto hodnotu zadejte jako název virtuální sítě.
   - *10.0.0.0/16* – tuto hodnotu zadejte jako adresní prostor virtuální sítě.
   - *myBackendSubnet* – tuto hodnotu zadejte jako název podsítě.
   - *10.0.0.0/24* – tuto hodnotu zadejte jako adresní prostor podsítě.

     ![Vytvoření virtuální sítě](./media/create-ssl-portal/application-gateway-vnet.png)

6. Vyberte **OK** k vytvoření virtuální sítě a podsítě.
7. Vyberte **zvolte veřejnou IP adresu**vyberte **vytvořit nový**a potom zadejte název veřejné IP adresy. V tomto příkladu se veřejná IP adresa nazývá *myAGPublicIPAddress*. Přijměte výchozí hodnoty pro další nastavení a pak vyberte **OK**.
8. Vyberte **HTTPS** pro protokol naslouchacího procesu a ujistěte se, že port, který je definován jako **443**.
9. Vyberte ikonu složky a přejděte *appgwcert.pfx* certifikát, který jste předtím vytvořili, a pak ji nahrajte.
10. Zadejte *mycert1* pro název certifikátu a *Azure123456!* heslo a pak vyberte **OK**.

    ![Vytvoření nové aplikační brány](./media/create-ssl-portal/application-gateway-create.png)

11. Zkontrolujte nastavení na stránce Souhrn a pak vyberte **OK** k vytvoření síťových prostředků a aplikační brány. Může trvat několik minut, než pro službu application gateway, chcete-li vytvořit, počkejte na úspěšné dokončení nasazení přejde k další části.

### <a name="add-a-subnet"></a>Přidání podsítě

1. Vyberte **všechny prostředky** v panelu nabídky na levé straně a pak vyberte **myVNet** v seznamu prostředků.
2. Vyberte **podsítě**a pak vyberte **podsítě**.

    ![Vytvoření podsítě](./media/create-ssl-portal/application-gateway-subnet.png)

3. Zadejte *myBackendSubnet* pro název podsítě a pak vyberte **OK**.

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V tomto příkladu vytvoříte dva virtuální počítače použít jako servery back-endu pro službu application gateway. Také nainstalujte IIS na virtuálních počítačích, chcete-li ověřit, že službu application gateway se úspěšně vytvořil.

### <a name="create-a-virtual-machine"></a>Vytvořit virtuální počítač

1. Vyberte **Nový**.
2. Vyberte **Výpočty** a potom v seznamu Doporučené vyberte **Windows Server 2016 Datacenter**.
3. Zadejte pro virtuální počítač tyto hodnoty:

    - *myVM1* – název virtuálního počítače.
    - *azureuser* – uživatelské jméno správce.
    - *Azure123456!* heslo.
    - Vyberte **Použít existující** a pak vyberte *myResourceGroupAG*.

4. Vyberte **OK**.
5. Vyberte **DS1_V2** pro velikost virtuálního počítače a vyberte **vyberte**.
6. Zkontrolujte, že u virtuální sítě je vybrána možnost **myVNet** a u podsítě **myBackendSubnet**. 
7. Vybráním možnosti **Zakázáno** zakažte diagnostiku spouštění.
8. Vyberte **OK**, na stránce souhrnu zkontrolujte nastavení a pak vyberte **Vytvořit**.

### <a name="install-iis"></a>Instalace služby IIS

1. Otevřete prostředí pro interaktivní a ujistěte se, že je nastavena na **Powershellu**.

    ![Instalace vlastního rozšíření](./media/create-ssl-portal/application-gateway-extension.png)

2. Spuštěním následujícího příkazu nainstalujte službu IIS na virtuální počítač: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Vytvořte druhý virtuální počítač a stejným postupem na něj nainstalujte službu IIS. Zadejte *myVM2* pro její název a VMName v sadě AzVMExtension.

### <a name="add-backend-servers"></a>Přidání back-endových serverů

1. Vyberte **všechny prostředky**a pak vyberte **myAppGateway**.
1. Vyberte **back-endové fondy**. V aplikační bráně je automaticky vytvořen výchozí fond. select **appGatewayBackendPool**.
1. Vyberte **přidat cíl** přidáte každý virtuální počítač, který jste vytvořili pro back-endový fond.

    ![Přidání back-endových serverů](./media/create-ssl-portal/application-gateway-backend.png)

1. Vyberte **Uložit**.

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

1. Vyberte **všechny prostředky**a pak vyberte **myAGPublicIPAddress**.

    ![Záznam veřejné IP adresy aplikační brány](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. Pokud jste použili certifikát podepsaný svým držitelem, přijměte upozornění zabezpečení, vyberte podrobnosti, a potom přejděte na webovou stránku:

    ![Zabezpečené upozornění](./media/create-ssl-portal/application-gateway-secure.png)

    Potom se zobrazí váš zabezpečený web služby IIS, jak je znázorněno v následujícím příkladu:

    ![Otestování základní adresy URL v aplikační bráně](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o tom, co můžete dělat pomocí služby Azure Application Gateway](application-gateway-introduction.md)
