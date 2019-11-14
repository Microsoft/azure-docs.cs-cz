---
title: Konfigurace koncového bodu interního nástroje pro vyrovnávání zatížení (interního nástroje)
titleSuffix: Azure Application Gateway
description: Tento článek poskytuje informace o tom, jak nakonfigurovat Application Gateway s použitím privátní IP adresy front-endu.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: a9e3150a5382e4d690ddf66c43bbe51e125509d3
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075220"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Konfigurace aplikační brány s koncovým bodem interního nástroje pro vyrovnávání zatížení (interního nástroje)

Azure Application Gateway se dá nakonfigurovat s internetovou VIP nebo interním koncovým bodem, který se nezveřejňuje na internetu (pomocí privátní IP adresy pro front-end IP adresu), označovaného taky jako koncový bod interního nástroje pro vyrovnávání zatížení (interního nástroje). Konfigurace brány pomocí privátní IP adresy front-endu je užitečná pro interní obchodní aplikace, které nejsou přístupné pro Internet. To se hodí i pro služby a vrstvy v rámci vícevrstvé aplikace, které se nacházejí v rámci hranice zabezpečení nevystavené k Internetu, ale stále vyžadují distribuci zatížení modelu kruhového dotazování, dlouhodobost relace nebo ukončení SSL (Secure Sockets Layer).

Tento článek vás provede jednotlivými kroky konfigurace aplikační brány s privátní IP adresou front-endu z webu Azure Portal.

V tomto článku se dozvíte, jak:

- Vytvoření privátní konfigurace IP adresy front-endu pro Application Gateway
- Vytvoření aplikační brány s konfigurací privátní IP adresy front-endu


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com>.

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Aby mohl Azure komunikovat mezi prostředky, které vytvoříte, potřebuje virtuální síť. Můžete buď vytvořit novou virtuální síť, nebo použít existující. V tomto příkladu vytvoříme novou virtuální síť. Virtuální síť můžete vytvořit současně s aplikační bránou. Instance Application Gateway se vytvářejí v oddělených podsítích. V tomto příkladu vytvoříte dvě podsítě: jednu pro aplikační bránu a druhou pro back-end servery.

1. V levém horním rohu Azure Portal klikněte na **Nový** .
2. Vyberte **Sítě** a potom v seznamu Doporučené vyberte **Application Gateway**.
3. Jako název služby Application Gateway a *myResourceGroupAG* pro novou skupinu prostředků zadejte *myAppGateway* .
4. U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.
5. Klikněte na tlačítko **zvolit virtuální síť**, klikněte na tlačítko **vytvořit nový**a potom zadejte tyto hodnoty pro virtuální síť:
   - myVNet * – název virtuální sítě.
   - 10.0.0.0/16 * – pro adresní prostor virtuální sítě.
   - *myBackendSubnet* – tuto hodnotu zadejte jako název podsítě.
   - *10.0.0.0/24* – tuto hodnotu zadejte jako adresní prostor podsítě.  
     ![private-frontendip-1](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)
6. Kliknutím na **OK** vytvořte virtuální síť a podsíť.
7. Vyberte konfiguraci IP adresy front-endu jako soukromou a ve výchozím nastavení se jedná o dynamické přidělování IP adres. První dostupná adresa zvolené podsítě bude přiřazena jako IP adresa front-endu.
8. Pokud chcete zvolit privátní IP adresu z rozsahu adres podsítě (statické přidělení), klikněte na příslušné pole, **zvolte konkrétní privátní IP adresu** a zadejte IP adresu.
   > [!NOTE]
   > Po přidělení se typ IP adresy (statický nebo dynamický) nedá později změnit.
9. Vyberte konfiguraci naslouchacího procesu pro protokol a port, konfiguraci WAF (Pokud je potřeba) a klikněte na OK.
    ![private-frontendip-2](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-2.png)
10. Zkontrolujte nastavení na stránce Souhrn a potom kliknutím na tlačítko **OK** vytvořte síťové prostředky a aplikační bránu. Může trvat několik minut, než pro službu application gateway, chcete-li vytvořit, počkejte na úspěšné dokončení nasazení přejde k další části.

## <a name="add-backend-pool"></a>Přidat back-end fond

Back-end fond se používá ke směrování požadavků na servery back-end, které budou obsluhovat žádosti. Back-end se může skládat z síťových adaptérů, virtuálních počítačů a virtuálních IP adres, interních IP adres, plně kvalifikovaných názvů domény (FQDN) a back-endu s více klienty, jako je Azure App Service. V tomto příkladu budeme používat virtuální počítače jako cílový back-end. Můžeme použít buď existující virtuální počítače, nebo vytvořit nové. V tomto příkladu vytvoříme dva virtuální počítače, které Azure používá jako servery back-end pro službu Application Gateway. K tomu je potřeba:

1. Vytvořte dva nové virtuální počítače, *myVM* a *myVM2*, které se budou používat jako servery back-end.
2. Nainstalujte službu IIS na virtuální počítače, abyste ověřili, že se služba Application Gateway úspěšně vytvořila.
3. Přidejte back-end servery do fondu back-end.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. Klikněte na možnost **Nové**.
2. Klikněte na **COMPUTE** a v seznamu Doporučené vyberte **Windows Server 2016 Datacenter** .
3. Zadejte pro virtuální počítač tyto hodnoty:
   - *myVM1* – název virtuálního počítače.
   - *azureuser* – uživatelské jméno správce.
   - *Azure123456!* jako heslo.
   - Vyberte **Použít existující** a pak vyberte *myResourceGroupAG*.
4. Klikněte na tlačítko **OK**.
5. Pro velikost virtuálního počítače vyberte **DS1_V2** a klikněte na **Vybrat**.
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
