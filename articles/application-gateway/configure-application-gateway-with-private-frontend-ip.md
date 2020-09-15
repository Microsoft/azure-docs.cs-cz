---
title: Konfigurace koncového bodu interního nástroje pro vyrovnávání zatížení (interního nástroje)
titleSuffix: Azure Application Gateway
description: Tento článek poskytuje informace o tom, jak nakonfigurovat Application Gateway s použitím privátní IP adresy front-endu.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/16/2020
ms.author: victorh
ms.openlocfilehash: 64dfe284772faf2a345b7959f1a1bd6f474cd1bf
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90562481"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Konfigurace aplikační brány s koncovým bodem interního nástroje pro vyrovnávání zatížení (interního nástroje)

Azure Application Gateway se dá nakonfigurovat s internetovou VIP nebo interním koncovým bodem, který není přístupný na internetu. Interní koncový bod používá privátní IP adresu pro front-end, který se taky označuje jako *koncový bod interního nástroje pro vyrovnávání zatížení (interního nástroje)*.

Konfigurace brány pomocí privátní IP adresy front-endu je užitečná pro interní obchodní aplikace, které nejsou přístupné pro Internet. Je to také užitečné pro služby a vrstvy v rámci vícevrstvé aplikace, které jsou v hranici zabezpečení, která není dostupná pro Internet, ale pořád vyžaduje distribuci zatížení pomocí kruhového dotazování, vytrvalost relace nebo zabezpečení TLS (Transport Layer Security), dříve označované jako SSL (Secure Sockets Layer) (SSL), ukončení.

Tento článek vás provede kroky konfigurace aplikační brány s privátní IP adresou front-endu pomocí Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com>.

## <a name="create-an-application-gateway"></a>Vytvoření brány Application Gateway

Aby mohl Azure komunikovat mezi prostředky, které vytvoříte, potřebuje virtuální síť. Můžete buď vytvořit novou virtuální síť, nebo použít existující. V tomto příkladu vytvoříte novou virtuální síť. Virtuální síť můžete vytvořit současně s aplikační bránou. Instance Application Gateway se vytvářejí v oddělených podsítích. V tomto příkladu vytvoříte dvě podsítě: jednu pro aplikační bránu a druhou pro back-end servery.

1. Rozbalte nabídku portál a vyberte **vytvořit prostředek**.
2. Vyberte **Sítě** a potom v seznamu Doporučené vyberte **Application Gateway**.
3. Jako název služby Application Gateway a *myResourceGroupAG* pro novou skupinu prostředků zadejte *myAppGateway* .
4. V **oblasti oblast**vyberte **(US) Střed USA**.
5. Jako **úroveň**vyberte **standardní**.
6. V části **Konfigurovat virtuální síť** vyberte **vytvořit novou**a zadejte tyto hodnoty pro virtuální síť:
   - *myVNet* – tuto hodnotu zadejte jako název virtuální sítě.
   - *10.0.0.0/16* – tuto hodnotu zadejte jako adresní prostor virtuální sítě.
   - *myBackendSubnet* – tuto hodnotu zadejte jako název podsítě.
   - *10.0.0.0/24* – tuto hodnotu zadejte jako adresní prostor podsítě.
   - *myBackendSubnet* – název podsítě back-endu.
   - *10.0.1.0/24* – pro adresní prostor back-end podsítě.

    ![Vytvoření virtuální sítě](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. Vyberte **OK** a vytvořte virtuální síť a podsíť.
7. Vyberte **Další: front-endové**.
8. Jako **typ IP adresy front-endu**vyberte **Private**.

   Ve výchozím nastavení se jedná o dynamické přiřazení IP adresy. První dostupná adresa nakonfigurované podsítě se přiřadí jako IP adresa front-endu.
   > [!NOTE]
   > Po přidělení se typ IP adresy (statický nebo dynamický) nedá později změnit.
9. Vyberte **Další: back-endy**.
10. Vyberte **Přidat back-end fond**.
11. Jako **název**zadejte *appGatewayBackendPool*.
12. Pro **přidání back-end fondu bez cílů**vyberte **Ano**. Cíle přidáte později.
13. Vyberte **Přidat**.
14. Vyberte **Další: Konfigurace**.
15. V části **pravidla směrování**vyberte **Přidat pravidlo**.
16. Jako **název pravidla**zadejte *RRULE-01*.
17. Jako **název naslouchacího procesu**zadejte *Listener-01*.
18. V případě **IP adresy front-endu**vyberte **Private**.
19. Přijměte zbývající výchozí hodnoty a vyberte kartu **cílení na back-end** .
20. Jako **cílový typ**vyberte **back-end fond**a pak vyberte **appGatewayBackendPool**.
21. V **Nastavení http**vyberte **vytvořit novou**.
22. Jako **název nastavení http**zadejte *http-Setting-01*.
23. V případě **back-end protokolu**vyberte **http**.
24. Pro **back-end port**zadejte *80*.
25. Potvrďte zbývající výchozí hodnoty a vyberte **Přidat**.
26. Na stránce **Přidat pravidlo směrování** vyberte **Přidat**.
27. Vyberte **Další: značky**.
28. Vyberte **Další: Zkontrolovat a vytvořit**.
29. Zkontrolujte nastavení na stránce Souhrn a pak vyberte **vytvořit** a vytvořte tak síťové prostředky a aplikační bránu. Vytvoření aplikační brány může trvat několik minut. Před přechodem k další části počkejte na úspěšné dokončení nasazení.

## <a name="add-backend-pool"></a>Přidat back-end fond

Back-end fond slouží ke směrování požadavků na servery back-end, které obsluhují požadavek. Back-end se může skládat ze síťových adaptérů, virtuálních počítačů s měřítkem, veřejných IP adres, interních IP adres, plně kvalifikovaných názvů domény (FQDN) a back-endu s více klienty, jako je Azure App Service. V tomto příkladu použijete jako cílový back-end virtuální počítače. Můžete buď použít stávající virtuální počítače, nebo vytvořit nové. V tomto příkladu vytvoříte dva virtuální počítače, které Azure používá jako servery back-end pro službu Application Gateway.

Uděláte to takto:

1. Vytvořte dva nové virtuální počítače, *myVM* a *myVM2*, které se používají jako servery back-end.
2. Nainstalujte službu IIS na virtuální počítače, abyste ověřili, že se služba Application Gateway úspěšně vytvořila.
3. Přidejte back-end servery do fondu back-end.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. Vyberte **Vytvořit prostředek**.
2. Vyberte **COMPUTE** a potom vyberte **virtuální počítač**.
4. Zadejte pro virtuální počítač tyto hodnoty:
   - pro **skupinu prostředků**vyberte *myResourceGroupAG* .
   - *myVM* – **název virtuálního počítače**
   - Pro **Image**vyberte **Windows Server 2019 Datacenter** .
   - platné **uživatelské jméno**.
   - platné **heslo**.
5. Potvrďte zbývající výchozí hodnoty a vyberte **Další: disky**.
6. Přijměte výchozí hodnoty a vyberte **Další: sítě**.
7. Zkontrolujte, že u virtuální sítě je vybrána možnost **myVNet** a u podsítě **myBackendSubnet**.
8. Potvrďte zbývající výchozí hodnoty a vyberte **Další: Správa**.
9. Výběrem možnosti **vypnuto** zakážete diagnostiku spouštění.
10. Potvrďte zbývající výchozí hodnoty a vyberte **Další: Upřesnit**.
11. Vyberte **Další: Značky**.
12. Vyberte **Další: zkontrolovat + vytvořit**.
13. Zkontrolujte nastavení na stránce Souhrn a pak vyberte **vytvořit**. Vytvoření virtuálního počítače může trvat několik minut. Před přechodem k další části počkejte na úspěšné dokončení nasazení.

### <a name="install-iis"></a>Instalace služby IIS

1. Otevřete Cloud Shell a ujistěte se, že je nastavená na **PowerShell**.
    ![Snímek obrazovky ukazuje otevřené okno konzoly Azure Cloud Shell, které používá PowerShell.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
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



3. Vytvořte druhý virtuální počítač a stejným postupem na něj nainstalujte službu IIS. Jako název zadejte myVM2 a pro VMName v set-AzVMExtension.

### <a name="add-backend-servers-to-backend-pool"></a>Přidání back-end serverů do fondu back-endu

1. Vyberte **všechny prostředky**a pak vyberte **myAppGateway**.
2. Vyberte **back-end fondy**. Vyberte **appGatewayBackendPool**.
3. V části **cílový typ** vyberte **virtuální počítač**  a v části **cíl**vyberte vNIC přidružené k myVM.
4. Opakujte pro přidání MyVM2.
   ![Snímek obrazovky se zobrazí podokno upravit fond back-end s cílovými typy a cíli zvýrazněnými.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. Vyberte **Uložit.**

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

1. Kliknutím na stránku **Konfigurace IP adresy front** -Endu na portálu ověřte IP adresu front-end, která se přiřadila.
    ![Snímek obrazovky se zobrazí v podokně Konfigurace protokolu IP front-endu se zvýrazněným soukromým typem.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Zkopírujte privátní IP adresu a vložte ji do adresního řádku prohlížeče na virtuálním počítači ve stejné virtuální síti nebo v místním počítači, která má připojení k této virtuální síti, a pokuste se o přístup k Application Gateway.

## <a name="next-steps"></a>Další kroky

Pokud chcete monitorovat stav vašeho back-endu, přečtěte si téma [stav back-end a diagnostické protokoly pro Application Gateway](application-gateway-diagnostics.md).
