---
title: Přesměrování HTTP na HTTPS na portálu – Azure Application Gateway
description: Naučte se vytvořit Aplikační bránu s přesměrovaným provozem z HTTP na HTTPS pomocí Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 67153fa750fee765dcaa1072eec87a2f6169b918
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397276"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Vytvoření služby Application Gateway s použitím přesměrování HTTP na HTTPS pomocí Azure Portal

Pomocí Azure Portal můžete vytvořit [Aplikační bránu](overview.md) s certifikátem pro ukončení protokolu TLS. Pravidlo směrování se používá k přesměrování provozu HTTP na port HTTPS ve vaší aplikační bráně. V tomto příkladu vytvoříte také [sadu škálování virtuálního počítače](../virtual-machine-scale-sets/overview.md) pro back-end fond aplikační brány, která obsahuje dvě instance virtuálních počítačů.

V tomto článku získáte informace o těchto tématech:

* Vytvoření certifikátu podepsaného svým držitelem (self-signed certificate)
* Nastavit síť
* Vytvořit aplikační bránu s certifikátem
* Přidat pravidlo naslouchacího procesu a přesměrování
* Vytvořit škálovací sadu virtuálních počítačů s výchozím back-endovým fondem

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tento kurz vyžaduje, aby modul Azure PowerShell verze 1.0.0 nebo novější vytvořil certifikát a nainstaloval službu IIS. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Chcete-li spustit příkazy v tomto kurzu, je nutné spustit také `Login-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-self-signed-certificate"></a>Vytvoření certifikátu podepsaného svým držitelem (self-signed certificate)

V případě použití v produkčním prostředí byste měli importovat platný certifikát podepsaný důvěryhodným poskytovatelem. Pro účely tohoto kurzu vytvoříte certifikát podepsaný svým držitelem (self-signed certificate) pomocí rutiny [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). K exportu souboru pfx z certifikátu můžete použít rutinu [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate) s vráceným kryptografickým otiskem.

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

## <a name="create-an-application-gateway"></a>Vytvoření brány Application Gateway

Pro komunikaci mezi prostředky, které vytvoříte, je potřeba virtuální síť. V tomto příkladu jsou vytvořeny dvě podsítě: jedna pro aplikační bránu a druhá pro back-endové servery. Virtuální síť můžete vytvořit současně s aplikační bránou.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Klikněte na **Vytvořit prostředek** v levém horním rohu portálu Azure Portal.
3. Vyberte **Sítě** a potom v seznamu Doporučené vyberte **Application Gateway**.
4. Pro aplikační bránu zadejte tyto hodnoty:

   - *myAppGateway* – tuto hodnotu zadejte jako název aplikační brány.
   - *myResourceGroupAG* – tuto hodnotu zadejte jako skupinu prostředků.

     ![Vytvoření nové aplikační brány](./media/create-url-route-portal/application-gateway-create.png)

5. U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.
6. Klikněte na **Vybrat virtuální síť**, klikněte na **vytvořit novou** a zadejte tyto hodnoty pro virtuální síť:

   - *myVNet* – tuto hodnotu zadejte jako název virtuální sítě.
   - *10.0.0.0/16* – tuto hodnotu zadejte jako adresní prostor virtuální sítě.
   - *myBackendSubnet* – tuto hodnotu zadejte jako název podsítě.
   - *10.0.0.0/24* – tuto hodnotu zadejte jako adresní prostor podsítě.

     ![Vytvoření virtuální sítě](./media/create-url-route-portal/application-gateway-vnet.png)

7. Kliknutím na **OK** vytvořte virtuální síť a podsíť.
8. V části **Konfigurace protokolu IP**, ujistěte se, že **typ IP adresy** je **veřejný** a je vybraná možnost **vytvořit nový** . Jako název zadejte *myAGPublicIPAddress* . U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.
9. V části **Konfigurace naslouchacího procesu** vyberte **https**, pak vyberte **Vybrat soubor** , přejděte k souboru *c:\appgwcert.pfx* a vyberte **otevřít**.
10. Jako  název certifikátu a Azure123456 zadejte appgwcert *.* jako heslo.
11. Ponechte bránu firewall webových aplikací zakázanou a pak vyberte **OK**.
12. Zkontrolujte nastavení na stránce Souhrn a pak vyberte **OK** a vytvořte tak síťové prostředky a aplikační bránu. Vytvoření aplikační brány může trvat několik minut, než budete pokračovat k další části, počkejte, než se nasazení dokončí úspěšně.

### <a name="add-a-subnet"></a>Přidání podsítě

1. V nabídce na levé straně vyberte **všechny prostředky** a v seznamu prostředky vyberte **myVNet** .
2. Vyberte **podsítě** a pak klikněte na **podsíť**.

    ![Vytvoření podsítě](./media/create-url-route-portal/application-gateway-subnet.png)

3. Jako název podsítě zadejte *myBackendSubnet* .
4. Jako rozsah adres zadejte *10.0.2.0/24* a pak vyberte **OK**.

## <a name="add-a-listener-and-redirection-rule&quot;></a>Přidat pravidlo naslouchacího procesu a přesměrování

### <a name=&quot;add-the-listener&quot;></a>Přidat naslouchací proces

Nejprve přidejte naslouchací proces s názvem *MyListener* pro port 80.

1. Otevřete skupinu prostředků **myResourceGroupAG** a vyberte **myAppGateway**.
2. Vyberte **naslouchací procesy** a pak vyberte **+ základní**.
3. Jako název zadejte *MyListener* .
4. Zadejte *httpPort* pro nový název portu front-end a *80* pro port.
5. Zajistěte, aby byl protokol nastavený na **http**, a pak vyberte **OK**.

### <a name=&quot;add-a-routing-rule-with-a-redirection-configuration&quot;></a>Přidání pravidla směrování s konfigurací přesměrování

1. V **myAppGateway** vyberte **pravidla** a pak vyberte **+ pravidlo směrování žádostí**.
2. Jako **název pravidla** zadejte *Rule2*.
3. Ujistěte se, že je pro naslouchací proces vybraná možnost **MyListener** .
4. Klikněte na kartu **cílení na back-end** a vyberte **cílový typ** jako *přesměrování*.
5. V případě **typu přesměrování** vyberte možnost **trvalá**.
6. V případě **cíle přesměrování** vyberte **naslouchací proces**.
7. Zajistěte, aby byl **cílový naslouchací proces** nastavený na **appGatewayHttpListener**.
8. Pro **řetězec dotazu include** a možnost **zahrnout cestu** vyberte *Ano*.
9. Vyberte **Přidat**.

## <a name=&quot;create-a-virtual-machine-scale-set&quot;></a>Vytvoření škálovací sady virtuálních počítačů

V tomto příkladu vytvoříte škálovací sadu virtuálních počítačů, která v aplikační bráně bude poskytovat servery pro back-endový fond.

1. V levém horním rohu portálu vyberte **+ vytvořit prostředek**.
2. Vyberte **Compute**.
3. Do vyhledávacího pole zadejte *sadu škálování* a stiskněte klávesu ENTER.
4. Vyberte možnost **sada škálování virtuálního počítače** a pak vyberte **vytvořit**.
5. Jako **název sady škálování virtuálního počítače** zadejte *myvmss*.
6. Pro bitovou kopii disku operačního systému * * ujistěte se, že je vybraná možnost **Windows Server 2016 Datacenter** .
7. V případě **skupiny prostředků** vyberte **myResourceGroupAG**.
8. Do tohoto **uživatelského jména** zadejte *azureuser*.
9. Jako **heslo** zadejte *Azure123456.* a potvrďte heslo.
10. Pro **počet instancí** zajistěte, aby byla hodnota **2**.
11. Jako **velikost instance** vyberte **D2s_v3**.
12. V části **sítě** ověřte, že možnost **Možnosti vyrovnávání zatížení** je nastavená na **Application Gateway**.
13. Ujistěte se, že je **Aplikační brána** nastavená na **myAppGateway**.
14. Zajistěte, aby byla **podsíť** nastavená na **myBackendSubnet**.
15. Vyberte **Vytvořit**.

### <a name=&quot;associate-the-scale-set-with-the-proper-backend-pool&quot;></a>Přidružit sadu škálování ke správnému back-end fondu

Uživatelské rozhraní portálu pro sadu škálování virtuálního počítače vytvoří nový back-end fond pro sadu škálování, ale chcete ho přidružit k vašemu stávajícímu appGatewayBackendPool.

1. Otevřete skupinu prostředků **myResourceGroupAg** .
2. Vyberte **myAppGateway**.
3. Vyberte **back-end fondy**.
4. Vyberte **myAppGatewaymyvmss**.
5. Vyberte možnost **Odebrat všechny cíle z back-endu fondu**.
6. Vyberte **Uložit**.
7. Po dokončení tohoto procesu vyberte fond back-endu **myAppGatewaymyvmss** , vyberte **Odstranit** a potvrďte to kliknutím na **OK** .
8. Vyberte **appGatewayBackendPool**.
9. V části **cíle** vyberte **VMSS**.
10. V části **VMSS** vyberte **myvmss**.
11. V části **Konfigurace síťového rozhraní** vyberte **myvmssNic**.
12. Vyberte **Uložit**.

### <a name=&quot;upgrade-the-scale-set&quot;></a>Upgrade sady škálování

Nakonec musíte upgradovat sadu škálování pomocí těchto změn.

1. Vyberte **myvmss** sadu škálování.
2. V části **Nastavení** vyberte **Počet instancí**.
3. Vyberte obě instance a pak vyberte **upgradovat**.
4. Akci potvrďte výběrem **Ano**.
5. Po dokončení tohoto procesu se vraťte k **myAppGateway** a vyberte **back-end fondy**. Nyní byste měli vidět, že **appGatewayBackendPool** má dva cíle a  **myAppGatewaymyvmss** nemá žádné cíle.
6. Vyberte **myAppGatewaymyvmss** a pak vyberte **Odstranit**.
7. Vyberte **OK**. Tím akci potvrdíte.

### <a name=&quot;install-iis&quot;></a>Instalace služby IIS

Snadný způsob, jak nainstalovat IIS do sady škálování, je použít PowerShell. Na portálu klikněte na ikonu Cloud Shell a ujistěte se, že je vybraná možnost **PowerShell** .

Vložte následující kód do okna prostředí PowerShell a stiskněte klávesu ENTER.

```azurepowershell
$publicSettings = @{ &quot;fileUris&quot; = (,&quot;https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
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

### <a name="upgrade-the-scale-set"></a>Upgrade sady škálování

Po změně instancí služby IIS musíte znovu upgradovat sadu škálování s touto změnou.

1. Vyberte **myvmss** sadu škálování.
2. V části **Nastavení** vyberte **Počet instancí**.
3. Vyberte obě instance a pak vyberte **upgradovat**.
4. Akci potvrďte výběrem **Ano**.

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

Veřejnou IP adresu aplikace můžete získat ze stránky přehled služby Application Gateway.

1. Vyberte **myAppGateway**.
2. Na stránce **Přehled** si poznamenejte IP adresu v části **Veřejná IP adresa front-endu**.

3. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. Například http://52.170.203.149.

   ![Zabezpečené upozornění](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Pokud chcete přijímat upozornění zabezpečení v případě použití certifikátu podepsaného svým držitelem (self-signed certificate), vyberte **Podrobnosti** a potom **Pokračovat na web**. Potom se zobrazí váš zabezpečený web služby IIS, jak je znázorněno v následujícím příkladu:

   ![Testování základní adresy URL v aplikační bráně](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Další kroky

Naučte [se vytvořit Aplikační bránu s interním přesměrováním](redirect-internal-site-powershell.md).