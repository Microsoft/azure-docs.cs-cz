---
title: Konfigurace koncového bodu interního správce zatížení (ILB)
titleSuffix: Azure Application Gateway
description: Tento článek obsahuje informace o konfiguraci brány aplikace s privátní front-endovou IP adresou
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 01/30/2020
ms.author: victorh
ms.openlocfilehash: f56929e14aef34f675139782328ed5c559df12c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198594"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Konfigurace aplikační brány s interním koncovým bodem pro vyrovnávání zatížení (ILB)

Azure Application Gateway můžete nakonfigurovat pomocí virtuální ip adresy pro internet nebo s interním koncovým bodem, který není vystavený internetu. Interní koncový bod používá privátní IP adresu pro front-end, který se také označuje jako *koncový bod interního vykladače zatížení (ILB).*

Konfigurace brány pomocí privátní IP adresy front-end je užitečná pro interní obchodní aplikace, které nejsou vystaveny Internetu. Je také užitečné pro služby a vrstvy v rámci vícevrstvé aplikace, které jsou v hranici zabezpečení, která není vystavena internetu, ale stále vyžadují rozdělení zatížení kruhového dotazování, lepivost relace nebo ukončení ssl (Secure Sockets Layer).

Tento článek vás provede kroky konfigurace aplikační brány s privátní IP adresou front-end pomocí portálu Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com>.

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Pro Azure komunikovat mezi prostředky, které vytvoříte, potřebuje virtuální síť. Můžete buď vytvořit novou virtuální síť, nebo použít existující. V tomto příkladu vytvoříte novou virtuální síť. Virtuální síť můžete vytvořit současně s aplikační bránou. Instance aplikační brány se vytvářejí v samostatných podsítích. V tomto příkladu vytvoříte dvě podsítě: jednu pro aplikační bránu a druhou pro servery back-endu.

1. Rozbalte nabídku **portálu**a vyberte Vytvořit prostředek .
2. Vyberte **Sítě** a potom v seznamu Doporučené vyberte **Application Gateway**.
3. Zadejte *myAppGateway* pro název brány aplikace a *myResourceGroupAG* pro novou skupinu prostředků.
4. V **případě oblasti**vyberte možnost **(USA) – střed USA**.
5. V **popřípadě možnost Úroveň**vyberte **Standardní**.
6. V části **Konfigurovat virtuální síť** vyberte Vytvořit **nový**a zadejte pro virtuální síť tyto hodnoty:
   - *myVNet* – tuto hodnotu zadejte jako název virtuální sítě.
   - *10.0.0.0/16* – tuto hodnotu zadejte jako adresní prostor virtuální sítě.
   - *myBackendSubnet* – tuto hodnotu zadejte jako název podsítě.
   - *10.0.0.0/24* – tuto hodnotu zadejte jako adresní prostor podsítě.
   - *myBackendSubnet* - pro název podsítě back-end.
   - *10.0.1.0/24* - pro adresní prostor podsítě back-endu.

    ![Vytvoření virtuální sítě](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. Chcete-li vytvořit virtuální síť a podsíť, vyberte **OK.**
7. Vyberte **Další:Frontendy**.
8. V **části Typ adresy IP front-endu**vyberte možnost **Soukromé**.

   Ve výchozím nastavení se jedná o dynamické přiřazení IP adres. První dostupná adresa nakonfigurované podsítě je přiřazena jako adresa IP front-endu.
   > [!NOTE]
   > Po přidělení nelze typ adresy IP (statický nebo dynamický) později změnit.
9. Vyberte **Další:Backendy**.
10. Vyberte **Přidat fond back-end .**
11. Do **pole Název**zadejte *appGatewayBackendPool*.
12. V **části Přidat back-endový fond bez cílů**vyberte **ano**. Cíle přidáte později.
13. Vyberte **Přidat**.
14. Vyberte **Další:Konfigurace**.
15. V části **Pravidla směrování**vyberte **Přidat pravidlo**.
16. Do **pole Název pravidla**zadejte *Rrule-01*.
17. Do **pole Název posluchače**zadejte *Listener-01*.
18. V **části Frontend IP**vyberte možnost **Soukromé**.
19. Přijměte zbývající výchozí hodnoty a vyberte kartu **Cíle back-endu.**
20. V **části Cílový typ**vyberte **back-endový fond**a pak vyberte **appGatewayBackendPool**.
21. Pro **nastavení HTTP**vyberte Vytvořit **nový**.
22. Do **pole Název nastavení PROTOKOLU HTTP**zadejte příkaz *http-setting-01*.
23. V **části Back-endový protokol**vyberte možnost **HTTP**.
24. Pro **port Back-end**zadejte *80*.
25. Přijměte zbývající výchozí hodnoty a vyberte **Přidat**.
26. Na stránce **Přidat pravidlo směrování** vyberte **Přidat**.
27. Vyberte **další: Značky**.
28. Vyberte **další: Kontrola + vytvoření**.
29. Zkontrolujte nastavení na stránce souhrnu a pak vyberte **Vytvořit,** chcete-li vytvořit síťové prostředky a aplikační bránu. Vytvoření aplikační brány může trvat několik minut. Počkejte, dokud nasazení úspěšně dokončí před přechodem na další část.

## <a name="add-backend-pool"></a>Přidat back-endový fond

Back-endový fond se používá ke směrování požadavků na servery back-endu, které tento požadavek obsluhují. Back-end se může skládat z nic, škálovacísady virtuálních počítačů, veřejných IP adres, interních IP adres, plně kvalifikovaných názvů domén (Plně kvalifikovaný název domény) a víceklientských back-endů, jako je Azure App Service. V tomto příkladu použijete virtuální počítače jako cílový back-end. Můžete buď použít existující virtuální počítače, nebo vytvořit nové. V tomto příkladu vytvoříte dva virtuální počítače, které Azure používá jako back-endové servery pro aplikační bránu.

Chcete-li to provést, můžete:

1. Vytvořte dva nové virtuální počítače, *myVM* a *myVM2*, které se používají jako back-endové servery.
2. Nainstalujte službu IIS do virtuálních počítačů a ověřte, zda byla aplikační brána úspěšně vytvořena.
3. Přidejte back-endové servery do back-endového fondu.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. Vyberte **Vytvořit prostředek**.
2. Vyberte **Vypočítat** a pak vyberte **Virtuální počítač**.
4. Zadejte pro virtuální počítač tyto hodnoty:
   - vyberte *položku myResourceGroupAG* pro **skupinu prostředků**.
   - *myVM* - pro **název virtuálního počítače**.
   - Vyberte **Windows Server 2019 Datacenter** for **Image**.
   - *azureadmin* - pro **uživatelské jméno**.
   - *Azure123456!* – pro **heslo**.
5. Přijměte zbývající výchozí hodnoty a vyberte **další : Disky**.
6. Přijměte výchozí hodnoty a vyberte **další : Síť**.
7. Zkontrolujte, že u virtuální sítě je vybrána možnost **myVNet** a u podsítě **myBackendSubnet**.
8. Přijměte zbývající výchozí hodnoty a vyberte **Další : Správa**.
9. Chcete-li diagnostiku spouštění zakázat, vyberte **možnost Vypnuto.**
10. Přijměte zbývající výchozí hodnoty a vyberte **Další : Upřesnit**.
11. Vyberte **další : Značky**.
12. Vyberte **další : Kontrola + vytvoření**.
13. Zkontrolujte nastavení na stránce souhrnu a pak vyberte **Vytvořit**. Vytvoření virtuálního virtuálního mísy může trvat několik minut. Počkejte, dokud nasazení úspěšně dokončí před přechodem na další část.

### <a name="install-iis"></a>Instalace služby IIS

1. Otevřete Cloud Shell a ujistěte se, že je nastavena na **PowerShell**.
    ![privátní-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Spuštěním následujícího příkazu nainstalujte službu IIS na virtuální počítač:

   ```azurepowershell
   Set-AzVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `

     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `

     -Location CentralUS `

   ```



3. Vytvořte druhý virtuální počítač a stejným postupem na něj nainstalujte službu IIS. Zadejte myVM2 pro jeho název a pro VMName v Set-AzVMExtension.

### <a name="add-backend-servers-to-backend-pool"></a>Přidání back-endových serverů do back-endového fondu

1. Vyberte **Všechny prostředky**a pak **vyberte myAppGateway**.
2. Vyberte **back-endové fondy**. Vyberte **appGatewayBackendPool**.
3. V části **Typ cíle** vyberte **Virtuální počítač** a v části **Cíl**vyberte vNIC přidružené k myVM.
4. Opakujte pro přidání MyVM2.
   ![privátní frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. vyberte **Uložit.**

## <a name="test-the-application-gateway"></a>Testování brány Application Gateway

1. Zkontrolujte front-end IP, která byla přiřazena kliknutím na stránku **Konfigurace ip adresy front-endu** na portálu.
    ![privátní-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Zkopírujte privátní IP adresu a vložte ji do adresního řádku prohlížeče ve virtuálním počítači ve stejné virtuální síti nebo v místním prostředí, který má připojení k této virtuální síti a pokuste se o přístup k aplikační bráně.

## <a name="next-steps"></a>Další kroky

Pokud chcete sledovat stav back-endu, naleznete [v tématu Back-end ové protokoly stavu a diagnostiky pro aplikační bránu](application-gateway-diagnostics.md).
