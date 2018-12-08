---
title: Vytvoření služby application gateway s protokolem HTTP na HTTPS přesměrování pomocí webu Azure portal
description: Informace o vytvoření služby application gateway s přesměrované přenosy z HTTP na HTTPS pomocí webu Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 12/7/2018
ms.author: victorh
ms.openlocfilehash: 1d30ddfb97b065d0d2fdf3bf91a73d3f7eb1b70f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109712"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Vytvoření služby application gateway s protokolem HTTP na HTTPS přesměrování pomocí webu Azure portal

Na webu Azure portal můžete použít k vytvoření [služba application gateway](overview.md) certifikátem pro ukončení protokolu SSL. Pravidlo směrování se používá k přesměrování provozu HTTP na port HTTPS ve službě application gateway. V tomto příkladu můžete také vytvořit [škálovací sadu virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) pro back-endový fond služby application gateway, která obsahuje dvě instance virtuálních počítačů.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
> * Nastavit síť
> * Vytvořit aplikační bránu s certifikátem
> * Přidat pravidlo naslouchací proces a přesměrování
> * Vytvořit škálovací sadu virtuálních počítačů s výchozím back-endovým fondem

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Tento kurz vyžaduje modul Azure PowerShell verze 3.6 nebo novější vytvořit certifikát a nainstalovat službu IIS. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Ke spuštění příkazů v tomto kurzu, budete také muset spustit `Login-AzureRmAccount` vytvořit připojení k Azure.

## <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)

Použití v produkčním prostředí byste měli importovat platný certifikát podepsaný důvěryhodným poskytovatelem. Pro účely tohoto kurzu vytvoříte certifikát podepsaný svým držitelem (self-signed certificate) pomocí rutiny [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate). K exportu souboru pfx z certifikátu můžete použít rutinu [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) s vráceným kryptografickým otiskem.

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

Virtuální sítě je potřeba ke komunikaci mezi prostředky, které vytvoříte. V tomto příkladu jsou vytvořeny dvě podsítě: jedna pro aplikační bránu a druhá pro back-endové servery. Virtuální síť můžete vytvořit současně s aplikační bránou.

1. Přihlaste se k webu Azure Portal na adrese [http://portal.azure.com](http://portal.azure.com).
2. Klikněte na **Vytvořit prostředek** v levém horním rohu portálu Azure Portal.
3. Vyberte **Sítě** a potom v seznamu Doporučené vyberte **Application Gateway**.
4. Pro aplikační bránu zadejte tyto hodnoty:

    - *myAppGateway* – tuto hodnotu zadejte jako název aplikační brány.
    - *myResourceGroupAG* – tuto hodnotu zadejte jako skupinu prostředků.

    ![Vytvoření nové aplikační brány](./media/create-url-route-portal/application-gateway-create.png)

5. U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.
6. Klikněte na tlačítko **zvolit virtuální síť**, klikněte na tlačítko **vytvořit nový**a potom zadejte tyto hodnoty pro virtuální síť:

    - *myVNet* – tuto hodnotu zadejte jako název virtuální sítě.
    - *10.0.0.0/16* – tuto hodnotu zadejte jako adresní prostor virtuální sítě.
    - *myBackendSubnet* – tuto hodnotu zadejte jako název podsítě.
    - *10.0.1.0/24* – adresní prostor podsítě.

    ![Vytvoření virtuální sítě](./media/create-url-route-portal/application-gateway-vnet.png)

7. Kliknutím na **OK** vytvořte virtuální síť a podsíť.
8. V části **konfigurace protokolu IP front-endu**, zkontrolujte **IP adres jako typu** je **veřejné**, a **vytvořit nový** je vybrána. Zadejte *myAGPublicIPAddress* pro název. U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.
9. V části **konfigurace naslouchacího procesu**vyberte **HTTPS**a pak vyberte **vyberte soubor** a přejděte do *c:\appgwcert.pfx* souboru a Vyberte **otevřít**.
10. Typ *appgwcert* pro název certifikátu a *Azure123456!* jako heslo.
11. Ponechte firewallu webových aplikací, které jsou zakázané a pak vyberte **OK**.
12. Zkontrolujte nastavení na stránce Souhrn a pak vyberte **OK** k vytvoření síťových prostředků a aplikační brány. Může trvat několik minut, než pro službu application gateway, chcete-li vytvořit, počkejte na úspěšné dokončení nasazení přejde k další části.

### <a name="add-a-subnet"></a>Přidání podsítě

1. Vyberte **všechny prostředky** v panelu nabídky na levé straně a pak vyberte **myVNet** v seznamu prostředků.
2. Vyberte **podsítě**a potom klikněte na tlačítko **podsítě**.

    ![Vytvoření podsítě](./media/create-url-route-portal/application-gateway-subnet.png)

3. Typ *myBackendSubnet* pro název podsítě.
4. Typ *10.0.2.0/24* rozsah adres a pak vyberte **OK**.

## <a name="add-a-listener-and-redirection-rule"></a>Přidat pravidlo naslouchací proces a přesměrování

### <a name="add-the-listener"></a>Přidejte naslouchací proces

Nejprve přidejte naslouchací proces s názvem *myListener* pro port 80.

1. Otevřít **myResourceGroupAG** prostředku, skupiny a vyberte **myAppGateway**.
2. Vyberte **naslouchacích procesů** a pak vyberte **+ základní**.
3. Typ *MyListener* pro název.
4. Typ *httpPort* pro nový název front-endový port a *80* pro port.
5. Zkontrolujte protokol je nastavená na **HTTP**a pak vyberte **OK**.

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Přidat pravidlo směrování s konfigurací přesměrování

1. Na **myAppGateway**vyberte **pravidla** a pak vyberte **+ základní**.
2. Pro **název**, typ *Rule2*.
3. Zajištění **MyListener** je vybrán pro naslouchací proces.
4. Vyberte **konfigurace přesměrování** zaškrtávací políčko.
5. Pro **typ přesměrování**vyberte **trvalé**.
6. Pro **cíl přesměrování**vyberte **naslouchací proces**.
7. Zkontrolujte, **cílového naslouchacího procesu** je nastavena na **appGatewayHttpListener**.
8. Vyberte **zahrnout řetězec dotazu** a **cesty zahrnutí** zaškrtávací políčka.
9. Vyberte **OK**.

## <a name="create-a-virtual-machine-scale-set"></a>Vytvoření škálovací sady virtuálních počítačů

V tomto příkladu vytvoříte škálovací sadu virtuálních počítačů, která v aplikační bráně poskytuje servery back-endového fondu.

1. Na portálu levého horního rohu, vyberte **+ vytvořit prostředek**.
2. Vyberte **Compute**.
3. Do vyhledávacího pole zadejte *škálovací sady* a stiskněte klávesu Enter.
4. Vyberte **škálovací sadu virtuálních počítačů**a pak vyberte **vytvořit**.
5. Pro **škálovací sady virtuálních počítačů název**, typ *myvmss*.
6. Pro image disku operačního systému ** ověřte **systému Windows Server 2016 Datacenter** zaškrtnuto.
7. Pro **skupiny prostředků**vyberte **myResourceGroupAG**.
8. Pro **uživatelské jméno**, typ *azureuser*.
9. Pro **heslo**, typ *Azure123456!* a potvrďte heslo.
10. Pro **počet instancí**, zkontrolujte že hodnota je **2**.
11. Pro **velikost Instance**vyberte **D2s_v3**.
12. V části **sítě**, zkontrolujte **zvolit možnosti vyrovnávání zatížení** je nastavena na **Application Gateway**.
13. Zajištění **Application gateway** je nastavena na **myAppGateway**.
14. Zajištění **podsítě** je nastavena na **myBackendSubnet**.
15. Vyberte **Vytvořit**.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>Přidružit škálovací sady pomocí správného back-endový fond

Vytvoří nový fond back-endu pro škálovací sady virtuálního počítače škálovací sady uživatelské rozhraní portálu, ale chcete přidružit k vaší existující appGatewayBackendPool.

1. Otevřít **myResourceGroupAg** skupinu prostředků.
2. Vyberte **myAppGateway**.
3. Vyberte **back-endové fondy**.
4. Vyberte **myAppGatewaymyvmss**.
5. Vyberte **odebrat všechny cíle z back-endový fond**.
6. Vyberte **Uložit**.
7. Po dokončení tohoto procesu, vyberte **myAppGatewaymyvmss** back-endový fond, vyberte **odstranit** a potom **OK** potvrďte.
8. Vyberte **appGatewayBackendPool**.
9. V části **cíle**vyberte **VMSS**.
10. V části **VMSS**vyberte **myvmss**.
11. V části **konfiguraci síťového rozhraní**vyberte **myvmssNic**.
12. Vyberte **Uložit**.

### <a name="upgrade-the-scale-set"></a>Upgrade škálovací sady

Nakonec je třeba upgradovat škálovací sadu s těmito změnami.

1. Vyberte **myvmss** škálovací sady.
2. V části **nastavení**vyberte **instance**.
3. Vyberte obě instance a pak vyberte **upgradovat**.
4. Odstranění potvrďte výběrem **Ano**.
5. Po dokončení tohoto postupu, přejděte zpět **myAppGateway** a vyberte **back-endové fondy**. Teď byste měli vidět, který **appGatewayBackendPool** má dva cíle a **myAppGatewaymyvmss** nemá žádnou cíle.
6. Vyberte **myAppGatewaymyvmss**a pak vyberte **odstranit**.
7. Vyberte **OK** potvrďte.

### <a name="install-iis"></a>Instalace služby IIS

Je snadný způsob, jak nainstalovat službu IIS ve škálovací sadě pomocí Powershellu. Z portálu, klikněte na ikonu Cloud Shell a ujistěte se, že **Powershellu** zaškrtnuto.

Vložte následující kód do okna Powershellu a stiskněte klávesu Enter.

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzureRmVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>Upgrade škálovací sady

Po změně instance se službou IIS, je nutné znovu upgradovat škálovací sadu s touto změnou.

1. Vyberte **myvmss** škálovací sady.
2. V části **nastavení**vyberte **instance**.
3. Vyberte obě instance a pak vyberte **upgradovat**.
4. Odstranění potvrďte výběrem **Ano**.

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

Veřejnou IP adresu aplikace můžete získat z stránka s přehledem application gateway.

1. Vyberte **myAppGateway**.
2. Na **přehled** stránce si poznamenejte IP adresu v rámci **veřejné IP adresy front-endu**.

3. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. Například http://52.170.203.149.

   ![Zabezpečené upozornění](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Pokud chcete přijímat upozornění zabezpečení v případě použití certifikátu podepsaného svým držitelem (self-signed certificate), vyberte **Podrobnosti** a potom **Pokračovat na web**. Potom se zobrazí váš zabezpečený web služby IIS, jak je znázorněno v následujícím příkladu:

   ![Otestování základní adresy URL v aplikační bráně](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Další postup

Zjistěte, jak [vytvoření služby application gateway s interním přesměrování](redirect-internal-site-powershell.md).