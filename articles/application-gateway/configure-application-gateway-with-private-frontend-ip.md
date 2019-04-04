---
title: Konfigurace Azure Application Gateway s privátní front-endovou IP adresou
description: Tento článek obsahuje informace o tom, jak nakonfigurovat Application Gateway s privátní front-endovou IP adresou
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/26/2019
ms.author: absha
ms.openlocfilehash: cfc63349e20aa6dbef4e0d31e81842d325bd3ec6
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905533"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Konfigurace aplikační brány s koncovým bodem interní služby load balancer (ILB)

Azure Application Gateway můžete nakonfigurovat pomocí virtuální IP adresy přístupem k Internetu, nebo pomocí interního koncového bodu, který není vystavený v Internetu (s použitím privátní IP adresa front-endové IP adresy), označuje se taky jako službě interní služby load balancer (ILB) koncového bodu. Konfigurace brány pomocí privátní IP adresy front-endu je užitečné pro interní-obchodní aplikace, které nejsou vystaveny v Internetu. To se hodí i pro služby a vrstvy v rámci vícevrstvé aplikace, které se nacházejí v rámci hranice zabezpečení nevystavené k Internetu, ale stále vyžadují distribuci zatížení modelu kruhového dotazování, dlouhodobost relace nebo ukončení SSL (Secure Sockets Layer).

Tento článek vás provede kroky konfigurace aplikační brány s privátní IP adresy front-endu na webu Azure Portal.

V tomto článku se dozvíte, jak:

- Vytvořte konfiguraci IP adresy privátní front-endu pro službu Application Gateway
- Vytvoření služby application gateway s privátní front-endovou konfiguraci protokolu IP


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure portal na <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

U Azure ke komunikaci mezi prostředky, že vytvoříte potřebuje virtuální sítě. Můžete vytvořit novou virtuální síť, nebo použijte již existující. V tomto příkladu vytvoříme nové virtuální sítě. Virtuální síť můžete vytvořit současně s aplikační bránou. Do samostatných podsítí se vytvářejí instance Application Gateway. V tomto příkladu vytvoříte dvě podsítě: jednu pro application gateway a jinou pro back-end serverů.

1. Klikněte na tlačítko **nový** v levém horním rohu webu Azure portal.
2. Vyberte **Sítě** a potom v seznamu Doporučené vyberte **Application Gateway**.
3. Zadejte *myAppGateway* pro název služby application gateway a *myResourceGroupAG* pro novou skupinu prostředků.
4. U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.
5. Klikněte na tlačítko **zvolit virtuální síť**, klikněte na tlačítko **vytvořit nový**a potom zadejte tyto hodnoty pro virtuální síť:
   - myVNet * - pro název virtuální sítě.
   - 10.0.0.0/16* - pro adresní prostor virtuální sítě.
   - *myBackendSubnet* – tuto hodnotu zadejte jako název podsítě.
   - *10.0.0.0/24* – tuto hodnotu zadejte jako adresní prostor podsítě.  
     ![private-frontendip-1](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)
6. Kliknutím na **OK** vytvořte virtuální síť a podsíť.
7. Vyberte konfigurace IP front-endu jako privátní a ve výchozím nastavení, je dynamické přiřazení IP adresy. První dostupnou adresu zvolený podsítě se přiřadí jako IP adresa front-endu.
8. Pokud chcete vybrat privátní IP adresa z rozsahu adres podsítě (statického přidělování), klikněte na políčko **zvolte konkrétní privátní IP adresu** a zadejte IP adresu.
   > [!NOTE]
   > Po přidělení IP adres jako typu (statická nebo dynamická) není možné později změnit.
9. Vyberte konfiguraci naslouchacího procesu pro protokol a port, konfiguraci WAF (v případě potřeby) a klikněte na tlačítko OK.
    ![private-frontendip-2](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-2.png)
10. Zkontrolujte nastavení na stránce Souhrn a pak klikněte na tlačítko **OK** k vytvoření síťových prostředků a aplikační brány. Může trvat několik minut, než pro službu application gateway, chcete-li vytvořit, počkejte na úspěšné dokončení nasazení přejde k další části.

## <a name="add-backend-pool"></a>Přidat back-endový fond

Back-endového fondu se používá k směrovat požadavky do back-end serverů, které budou sloužit pro požadavek. Back-end může skládat ze síťových rozhraní škálovací sady virtuálních počítačů, veřejné IP adresy, interní IP adresy nebo plně kvalifikované názvy (plně kvalifikovaný název domény) a více tenantů back EndY, jako je Azure App Service. V tomto příkladu budeme používat virtuální počítače jako cíl back-endu. Můžeme použít stávající virtuální počítače nebo vytvořit nové. V tomto příkladu vytvoříte dva virtuální počítače, které Azure používá jako servery back-end pro službu application gateway. Chcete-li to provést, provedeme následující:

1. Vytvořit 2 nové virtuální počítače, *myVM* a *myVM2*, které se použijí jako servery back-end.
2. Instalace služby IIS na virtuálních počítačích a ověřte, že službu application gateway vytvořil úspěšně.
3. Přidání serverů back-endu do back-endový fond.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. Klikněte na možnost **Nové**.
2. Klikněte na tlačítko **Compute** a pak vyberte **systému Windows Server 2016 Datacenter** v seznamu vybrané.
3. Zadejte pro virtuální počítač tyto hodnoty:
   - *myVM1* – název virtuálního počítače.
   - *azureuser* – uživatelské jméno správce.
   - *Azure123456!* jako heslo.
   - Vyberte **Použít existující** a pak vyberte *myResourceGroupAG*.
4. Klikněte na **OK**.
5. Vyberte **DS1_V2** pro velikost virtuálního počítače a klikněte na tlačítko **vyberte**.
6. Zkontrolujte, že u virtuální sítě je vybrána možnost **myVNet** a u podsítě **myBackendSubnet**.
7. Kliknutím na **Zakázáno** zakažte diagnostiku spouštění.
8. Klikněte na **OK**, na stránce souhrnu zkontrolujte nastavení a pak klikněte na **Vytvořit**.

### <a name="install-iis"></a>Instalace služby IIS

1. Otevřete interaktivní prostředí a zkontrolujte, že je nastaveno na **PowerShell**.
    ![private-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Spuštěním následujícího příkazu nainstalujte službu IIS na virtuální počítač:

   ```azurepowershell
   Set-AzVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `
   
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' -Location EastUS  ```



3. Create a second virtual machine and install IIS using the steps that you just finished. Enter myVM2 for its name and for VMName in Set-AzVMExtension.

### Add backend servers to backend pool

1. Click **All resources**, and then click **myAppGateway**.
2. Click **Backend pools**. A default pool was automatically created with the application gateway. Click **appGatewayBackendPool**.
3. Click **Add target** to add each virtual machine that you created to the backend pool.
   ![private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
4. Click **Save.**

## Test the application gateway

1. Check your frontend IP that got assigned by clicking the **Frontend IP Configurations** blade in the portal.
    ![private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copy the private IP address, and then paste it into the address bar of your browser of a VM in the same VNet or on-premises which has connectivity to this VNet and try to access the Application Gateway.

## Next steps

In this tutorial, you learned how to:

- Create a private frontend IP configuration for an Application Gateway
- Create an application gateway with private frontend IP configuration

If you want to monitor the health of your backend, see [Application Gateway Diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
