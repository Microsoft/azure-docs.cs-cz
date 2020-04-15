---
title: Přesměrování HTTP na HTTPS na portálu – Aplikační brána Azure
description: Zjistěte, jak vytvořit aplikační bránu s přesměrovanou návštěvností z PROTOKOLU HTTP na protokol HTTPS pomocí portálu Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: cd33d23a506bd86b9651af3d4c3bbca01673a7a4
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312099"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Vytvoření aplikační brány s přesměrováním HTTP na HTTPS pomocí webu Azure Portal

Portál Azure můžete použít k vytvoření [aplikační brány](overview.md) s certifikátem pro ukončení TLS. Pravidlo směrování se používá k přesměrování přenosu HTTP na port HTTPS v aplikační bráně. V tomto příkladu také vytvoříte [škálovací sadu virtuálních strojů](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) pro back-endový fond aplikační brány, který obsahuje dvě instance virtuálního počítače.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
> * Nastavit síť
> * Vytvořit aplikační bránu s certifikátem
> * Přidání naslouchací proces a pravidlo přesměrování
> * Vytvořit škálovací sadu virtuálních počítačů s výchozím back-endovým fondem

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější k vytvoření certifikátu a instalaci služby IIS. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Chcete-li spustit příkazy v tomto kurzu, musíte také spustit `Login-AzAccount` k vytvoření připojení s Azure.

## <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)

Pro použití v produkčním prostředí byste měli importovat platný certifikát podepsaný důvěryhodným poskytovatelem. Pro účely tohoto kurzu vytvoříte certifikát podepsaný svým držitelem (self-signed certificate) pomocí rutiny [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate). K exportu souboru pfx z certifikátu můžete použít rutinu [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) s vráceným kryptografickým otiskem.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Měli byste získat podobný výsledek:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Použití kryptografického otisku k vytvoření souboru pfx:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Virtuální síť je potřebná pro komunikaci mezi prostředky, které vytvoříte. V tomto příkladu jsou vytvořeny dvě podsítě: jedna pro aplikační bránu a druhá pro back-endové servery. Virtuální síť můžete vytvořit současně s aplikační bránou.

1. Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .
2. Klikněte na **Vytvořit prostředek** v levém horním rohu portálu Azure Portal.
3. Vyberte **Sítě** a potom v seznamu Doporučené vyberte **Application Gateway**.
4. Pro aplikační bránu zadejte tyto hodnoty:

   - *myAppGateway* – tuto hodnotu zadejte jako název aplikační brány.
   - *myResourceGroupAG* – tuto hodnotu zadejte jako skupinu prostředků.

     ![Vytvoření nové aplikační brány](./media/create-url-route-portal/application-gateway-create.png)

5. U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.
6. Klikněte na **Vybrat virtuální síť**, klikněte na Vytvořit **nový**a zadejte pro virtuální síť tyto hodnoty:

   - *myVNet* – tuto hodnotu zadejte jako název virtuální sítě.
   - *10.0.0.0/16* – tuto hodnotu zadejte jako adresní prostor virtuální sítě.
   - *myBackendSubnet* – tuto hodnotu zadejte jako název podsítě.
   - *10.0.0.0/24* – tuto hodnotu zadejte jako adresní prostor podsítě.

     ![Vytvoření virtuální sítě](./media/create-url-route-portal/application-gateway-vnet.png)

7. Kliknutím na **OK** vytvořte virtuální síť a podsíť.
8. V části **Frontend IP konfigurace**, ujistěte se, **že typ IP adresy** je **veřejné**a **vytvořit nový** je vybrán. Zadejte *název myAGPublicIPAddress.* U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.
9. V části **Konfigurace posluchače**vyberte **https**, pak vyberte **Vybrat soubor** a přejděte na soubor *c:\appgwcert.pfx* a vyberte **Otevřít**.
10. Zadejte *appgwcert* pro název certifikátu a *Azure123456!* jako heslo.
11. Ponechejte bránu firewall webové aplikace zakázanou a pak vyberte **OK**.
12. Zkontrolujte nastavení na stránce souhrnu a pak vyberte **OK,** chcete-li vytvořit síťové prostředky a aplikační bránu. Může trvat několik minut, než se vytvoří brána aplikace, počkejte, dokud nasazení úspěšně neskončí, než přejdete k další části.

### <a name="add-a-subnet"></a>Přidání podsítě

1. V levé nabídce vyberte **Všechny prostředky** a v seznamu zdrojů vyberte **myVNet.**
2. Vyberte **Podsítě**a klepněte na položku **Podsíť**.

    ![Vytvoření podsítě](./media/create-url-route-portal/application-gateway-subnet.png)

3. Pro název podsítě zadejte *myBackendSubnet.*
4. Zadejte *10.0.2.0/24* pro rozsah adres a pak vyberte **OK**.

## <a name="add-a-listener-and-redirection-rule"></a>Přidání naslouchací proces a pravidlo přesměrování

### <a name="add-the-listener"></a>Přidání posluchače

Nejprve přidejte naslouchací proces s názvem *myListener* pro port 80.

1. Otevřete skupinu prostředků **myResourceGroupAG** a vyberte **myAppGateway**.
2. Vyberte **Naslouchací procesy** a potom vyberte **+ Základní**.
3. Zadejte *MyListener* pro název.
4. Zadejte *httpPort* pro nový název portu front-end u *portu a 80* pro port.
5. Zkontrolujte, zda je protokol nastaven na **protokol HTTP**, a pak vyberte **možnost OK**.

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Přidání pravidla směrování s konfigurací přesměrování

1. Na **myAppGateway**vyberte **Pravidla** a pak vyberte pravidlo **směrování +Požadavek**.
2. Pro **název pravidla**zadejte *pravidlo2*.
3. Ujistěte **se, myListener** je vybrán pro naslouchací proces.
4. Klikněte na kartu **Cíle back-endu** a vyberte **Typ cíle** jako *Přesměrování*.
5. V **popřípadě Typ přesměrování**vyberte možnost **Trvalé**.
6. V **případě cíle přesměrování**vyberte **naslouchací proces**.
7. Ujistěte se, **že naslouchací proces Target** je nastaven na **appGatewayHttpListener**.
8. Pro **řetězec Zahrnout dotaz** a **Zahrnout cestu** vyberte *Ano*.
9. Vyberte **Přidat**.

## <a name="create-a-virtual-machine-scale-set"></a>Vytvoření škálovací sady virtuálních počítačů

V tomto příkladu vytvoříte škálovací sadu virtuálních počítačů, která v aplikační bráně bude poskytovat servery pro back-endový fond.

1. V levém horním rohu portálu vyberte **možnost +Vytvořit prostředek**.
2. Vyberte **Compute**.
3. Do vyhledávacího pole zadejte *měřítko a* stiskněte Enter.
4. Vyberte **škálovací sadu virtuálních strojů**a pak vyberte **Vytvořit**.
5. Do **pole Název sady měřítka virtuálního počítače**zadejte *myvms .*
6. V případě bitové kopie disku operačního systému** zkontrolujte, zda je **vybráno datové centrum Windows Serveru 2016.**
7. Ve **skupině Resource**vyberte **položku myResourceGroupAG**.
8. Do **pole Uživatelské jméno**zadejte *azureuser*.
9. Do **pole Heslo**zadejte příkaz *Azure123456!* a potvrďte heslo.
10. Pro **počet instancí**zkontrolujte, zda je hodnota **2**.
11. V **případě velikosti instance**vyberte **D2s_v3**.
12. V **části Networking**zkontrolujte, zda je volba **možnosti vyrovnávání zatížení** nastavena na **položku Application Gateway**.
13. Ujistěte **se, že je aplikační brána** nastavena na **myAppGateway**.
14. Ujistěte **se, že** je podsíť nastavena na **myBackendSubnet**.
15. Vyberte **Vytvořit**.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>Přidružení škálovací sady ke správnému fondu back-endů

U portálu škálování virtuálních strojů vytvoří nový back-endový fond pro škálovací sadu, ale chcete ho přidružit k vaší stávající aplikaciGatewayBackendPool.

1. Otevřete skupinu prostředků **myResourceGroupAg.**
2. Vyberte **myAppGateway**.
3. Vyberte **back-endové fondy**.
4. Vyberte **myAppGatewaymyvmss**.
5. Vyberte **Odebrat všechny cíle z back-endfondu**.
6. Vyberte **Uložit**.
7. Po dokončení tohoto procesu vyberte back-endový fond **myAppGatewaymyvmss,** vyberte **Odstranit** a **potvrďte** to.
8. Vyberte **appGatewayBackendPool**.
9. V části **Cíle**vyberte **VMSS**.
10. V **části VMSS**vyberte **myvms .**
11. V části **Konfigurace síťového rozhraní**vyberte **položku myvmssNic**.
12. Vyberte **Uložit**.

### <a name="upgrade-the-scale-set"></a>Upgrade škálovací sady

Nakonec je nutné upgradovat škálovací sadu s těmito změnami.

1. Vyberte sadu měřítek **myvms.**
2. V části **Nastavení** vyberte **Instance**.
3. Vyberte obě instance a pak vyberte **Upgradovat**.
4. Výběrem **Ano** potvrďte.
5. Po dokončení se vraťte na **myAppGateway** a vyberte **back-endové fondy**. Nyní byste měli vidět, že **appGatewayBackendPool** má dva cíle a **myAppGatewaymyvmss** má nulové cíle.
6. Vyberte **myAppGatewaymyvmss**a pak vyberte **Odstranit**.
7. Vyberte **OK**. Tím akci potvrdíte.

### <a name="install-iis"></a>Instalace služby IIS

Snadný způsob instalace služby IIS na škálovací sadu je použití prostředí PowerShell. Na portálu klikněte na ikonu Cloud Shell u zajištění, že je **vybráno Prostředí PowerShell.**

Vložte následující kód do okna PowerShellu a stiskněte Enter.

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>Upgrade škálovací sady

Po změně instancí pomocí služby IIS je nutné znovu upgradovat škálovací sadu pomocí této změny.

1. Vyberte sadu měřítek **myvms.**
2. V části **Nastavení** vyberte **Instance**.
3. Vyberte obě instance a pak vyberte **Upgradovat**.
4. Výběrem **Ano** potvrďte.

## <a name="test-the-application-gateway"></a>Testování brány Application Gateway

Veřejnou IP adresu aplikace můžete získat ze stránky Přehled aplikační brány.

1. Vyberte **myAppGateway**.
2. Na stránce **Přehled** poznamenejte si ip adresu v části **Frontend public IP address**.

3. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. Například http://52.170.203.149.

   ![Zabezpečené upozornění](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Pokud chcete přijímat upozornění zabezpečení v případě použití certifikátu podepsaného svým držitelem (self-signed certificate), vyberte **Podrobnosti** a potom **Pokračovat na web**. Potom se zobrazí váš zabezpečený web služby IIS, jak je znázorněno v následujícím příkladu:

   ![Testování základní adresy URL v aplikační bráně](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [vytvořit aplikační bránu s interním přesměrováním](redirect-internal-site-powershell.md).
