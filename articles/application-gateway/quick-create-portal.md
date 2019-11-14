---
title: 'Rychlý Start: směrování webového provozu pomocí portálu'
titleSuffix: Azure Application Gateway
description: Naučte se, jak pomocí Azure Portal vytvořit Azure Application Gateway, který směruje webový provoz do virtuálních počítačů v back-endu fondu.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 11/14/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 4f5a20f80ea8eafa73cbef394dcfdde75087326b
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074565"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Rychlý start: Směrování webového provozu pomocí služby Azure Application Gateway – Azure Portal

V tomto rychlém startu se dozvíte, jak pomocí Azure Portal vytvořit Aplikační bránu.  Po vytvoření služby Application Gateway ji otestujete, abyste se ujistili, že funguje správně. V případě Azure Application Gateway nasměrujete webový provoz aplikace na konkrétní prostředky přiřazením posluchačů k portům, vytvořením pravidel a přidáním prostředků do back-endového fondu. V zájmu zjednodušení Tento článek používá jednoduché nastavení s veřejnou front-end IP adresou, základní naslouchací proces, který hostuje jednu lokalitu v této aplikační bráně, dva virtuální počítače používané pro back-end fond a pravidlo základního směrování požadavků.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

1. V nabídce Azure Portal nebo na **domovské** stránce vyberte **vytvořit prostředek**. Zobrazí se **nové** okno.

2. Vyberte **sítě** a v seznamu **Doporučené** vyberte **Application Gateway** .

### <a name="basics-tab"></a>Karta základy

1. Na kartě **základy** zadejte tyto hodnoty pro následující nastavení služby Application Gateway:

   - **Skupina prostředků**: pro skupinu prostředků vyberte **myResourceGroupAG** . Pokud neexistuje, vyberte **vytvořit novou** a vytvořte ji.
   - **Název aplikační brány**: jako název služby Application Gateway zadejte *myAppGateway* .

     ![Vytvořit novou aplikační bránu: Základy](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2. Aby mohl Azure komunikovat mezi prostředky, které vytvoříte, potřebuje virtuální síť. Můžete buď vytvořit novou virtuální síť, nebo použít existující. V tomto příkladu vytvoříte novou virtuální síť ve stejnou chvíli, kdy vytvoříte Aplikační bránu. Instance Application Gateway se vytvářejí v oddělených podsítích. V tomto příkladu vytvoříte dvě podsítě: jednu pro aplikační bránu a druhou pro back-end servery.

    V části **Konfigurovat virtuální síť**vytvořte novou virtuální síť výběrem možnosti **vytvořit nový**. V okně **vytvořit virtuální síť** , které se otevře, zadejte následující hodnoty pro vytvoření virtuální sítě a dvě podsítě:

    - **Název**: jako název virtuální sítě zadejte *myVNet* .

    - **Název podsítě** (Application Gateway podsíť): v mřížce **podsítě** se zobrazí podsíť s názvem *výchozí*. Změňte název této podsítě na *myAGSubnet*.<br>Podsíť aplikační brány může obsahovat jenom aplikační brány. Žádné další prostředky nejsou povoleny.

    - **Název podsítě** (podsíť back-end serveru): v druhém řádku mřížky **podsítě** zadejte *myBackendSubnet* do sloupce **název podsítě** .

    - **Rozsah adres** (podsíť back-end serveru): ve druhém řádku mřížky **podsítě** zadejte rozsah adres, který se nepřekrývá s rozsahem adres *myAGSubnet*. Pokud má například rozsah adres *myAGSubnet* 10.0.0.0/24, zadejte pro rozsah adres *myBackendSubnet* *10.0.1.0/24* .

    Výběrem **OK** zavřete okno **vytvořit virtuální síť** a uložte nastavení virtuální sítě.

     ![Vytvořit novou aplikační bránu: virtuální síť](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Na kartě **základy** přijměte výchozí hodnoty pro ostatní nastavení a potom vyberte **Další: front-endu**.

### <a name="frontends-tab"></a>Karta front-endu

1. Na kartě **front-endu** ověřte, že **typ IP adresy front-end** je nastavený na **veřejné**. <br>Front-end IP adresu můžete nakonfigurovat tak, aby byla veřejná nebo soukromá jako na základě vašeho případu použití. V tomto příkladu zvolíte veřejnou front-end IP adresu.
   > [!NOTE]
   > V případě SKU Application Gateway v2 můžete zvolit jenom **veřejnou** konfiguraci IP adresy front-endu. V tuto chvíli není u této SKU verze V2 povolená jenom soukromá konfigurace IP adresy front-endu. Můžete mít konfiguraci veřejné i privátní IP adresy front-endu.

2. Zvolte **vytvořit nový** pro **veřejnou IP adresu** a jako název veřejné IP adresy zadejte *myAGPublicIPAddress* a pak vyberte **OK**. 

     ![Vytvořit novou aplikační bránu: front-endové](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Vyberte **Další: back-endy**.

### <a name="backends-tab"></a>Karta back-endy

Back-end fond slouží ke směrování požadavků na servery back-end, které obsluhují požadavek. Back-endové fondy se dají skládat ze síťových adaptérů, virtuálních počítačů a virtuálních IP adres, interních IP adres, plně kvalifikovaných názvů domény (FQDN) a back-endu s více klienty, jako je Azure App Service. V tomto příkladu vytvoříte prázdný back-end fond s aplikační bránou a potom přidáte cíle back-end do fondu back-end.

1. Na kartě back- **endy** vyberte **+ Přidat back-end fond**.

2. V okně **Přidat fond back-end** , které se otevře, zadejte následující hodnoty a vytvořte prázdný back-end fond:

    - **Název**: jako název back-end fondu zadejte *myBackendPool* .
    - **Přidat back-end fond bez cílů**: vyberte **Ano** , pokud chcete vytvořit back-end fond bez cílů. Po vytvoření aplikační brány přidáte cíle back-endu.

3. V okně **Přidat fond back-endu** vyberte **Přidat** a uložte konfiguraci fondu back-end a vraťte se na kartu back- **endy** .

     ![Vytvořit novou aplikační bránu: back-endy](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. Na kartě **back-endy** vyberte **Další: Konfigurace**.

### <a name="configuration-tab"></a>Karta Konfigurace

Na kartě **Konfigurace** se připojíte k front-endovému a back-endovému fondu, který jste vytvořili pomocí pravidla směrování.

1. Ve sloupci **pravidla směrování** vyberte **Přidat pravidlo** .

2. V okně **Přidat pravidlo směrování** , které se otevře, jako **název pravidla**zadejte *myRoutingRule* .

3. Pravidlo směrování vyžaduje naslouchací proces. Na kartě **naslouchací proces** v okně **Přidat pravidlo směrování** zadejte následující hodnoty pro naslouchací proces:

    - **Název naslouchacího procesu**: jako název naslouchacího procesu zadejte *MyListener* .
    - **Front-end IP adresa**: vyberte **veřejné** a zvolte veřejnou IP adresu, kterou jste vytvořili pro front-end.
  
      Přijměte výchozí hodnoty pro ostatní nastavení na kartě **naslouchací proces** a potom vyberte kartu **cílení na back-end** a nakonfigurujte zbývající část pravidla směrování.

   ![Vytvoření nové aplikační brány: naslouchací proces](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-listener.png)

4. Na kartě **cílení na server back-end** vyberte **MyBackendPool** pro **cíl back-endu**.

5. Pro **Nastavení http**vyberte **vytvořit novou** a vytvořte nové nastavení http. Nastavením protokolu HTTP se určí chování pravidla směrování. V okně **Přidat nastavení protokolu HTTP** , které se otevře, zadejte *myHTTPSetting* pro **název nastavení http**. Přijměte výchozí hodnoty pro ostatní nastavení v okně **Přidat nastavení http** a pak vyberte **Přidat** a vraťte se do okna **Přidat pravidlo směrování** . 

     ![Vytvořit novou aplikační bránu: nastavení HTTP](./media/application-gateway-create-gateway-portal/application-gateway-create-httpsetting.png)

6. V okně **Přidat pravidlo směrování** vyberte **Přidat** a uložte pravidlo směrování a vraťte se na kartu **Konfigurace** .

     ![Vytvoření nové aplikační brány: pravidlo směrování](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Vyberte **Další: značky** a potom **Další: zkontrolovat + vytvořit**.

### <a name="review--create-tab"></a>Revize + vytvořit kartu

Zkontrolujte nastavení na kartě **Revize + vytvořit** a pak vyberte **vytvořit** k vytvoření virtuální sítě, veřejné IP adresy a aplikační brány. Vytvoření služby Application Gateway v Azure může trvat několik minut. Před přechodem k další části počkejte na úspěšné dokončení nasazení.

## <a name="add-backend-targets"></a>Přidat cíle back-endu

V tomto příkladu budete jako cílový back-end používat virtuální počítače. Můžete buď použít stávající virtuální počítače, nebo vytvořit nové. Vytvoříte dva virtuální počítače, které Azure používá jako servery back-end pro službu Application Gateway.

Uděláte to takto:

1. Vytvořte dva nové virtuální počítače, *myVM* a *myVM2*, které se budou používat jako servery back-end.
2. Nainstalujte službu IIS na virtuální počítače, abyste ověřili, že se služba Application Gateway úspěšně vytvořila.
3. Přidejte back-end servery do fondu back-end.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. V nabídce Azure Portal nebo na **domovské** stránce vyberte **vytvořit prostředek**. Zobrazí se **nové** okno.
2. Vyberte **COMPUTE** a potom v seznamu **oblíbených** vyberte **Windows Server 2016 Datacenter** . Zobrazí se stránka **vytvořit virtuální počítač** .<br>Application Gateway může směrovat provoz na libovolný typ virtuálního počítače, který se používá v jeho fondu back-endu. V tomto příkladu použijete Windows Server 2016 Datacenter.
3. Zadejte tyto hodnoty na kartě **základy** pro následující nastavení virtuálního počítače:

    - **Skupina prostředků**: pro název skupiny prostředků vyberte **myResourceGroupAG** .
    - **Název virtuálního počítače**: jako název virtuálního počítače zadejte *myVM* .
    - **Uživatelské jméno**: jako uživatelské jméno správce zadejte *azureuser* .
    - **Heslo**: zadejte *Azure123456.* pro heslo správce.
4. Přijměte ostatní výchozí hodnoty a potom vyberte **Další: disky**.  
5. Přijměte výchozí hodnoty na kartě **disky** a potom vyberte **Další: sítě**.
6. Na kartě **sítě** ověřte, že je pro **virtuální síť** vybraný **myVNet** a že **podsíť** je nastavená na **myBackendSubnet**. Přijměte ostatní výchozí hodnoty a potom vyberte **Další: Správa**.<br>Application Gateway může komunikovat s instancemi mimo virtuální síť, ve které je, ale je potřeba zajistit připojení k IP adrese.
7. Na kartě **Správa** nastavte **diagnostiku spouštění** na **vypnuto**. Přijměte ostatní výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.
8. Na kartě **Revize + vytvořit** zkontrolujte nastavení, opravte chyby ověřování a potom vyberte **vytvořit**.
9. Než budete pokračovat, počkejte na dokončení vytváření virtuálního počítače.

### <a name="install-iis-for-testing"></a>Nainstalovat službu IIS pro testování

V tomto příkladu nainstalujete službu IIS na virtuální počítače jenom k úspěšnému ověření, že se brána Application Gateway úspěšně vytvořila.

1. Otevřete [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Provedete to tak, že v horním navigačním panelu Azure Portal vyberete **Cloud Shell** a v rozevíracím seznamu vyberete **PowerShell** . 

    ![Instalace vlastního rozšíření](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

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

3. Vytvořte druhý virtuální počítač a nainstalujte službu IIS pomocí dříve dokončených kroků. Pro název virtuálního počítače a pro nastavení **VMName** rutiny **set-AzVMExtension** použijte *myVM2* .

### <a name="add-backend-servers-to-backend-pool"></a>Přidání back-end serverů do fondu back-endu

1. V nabídce Azure Portal vyberte **všechny prostředky** , nebo vyhledejte a vyberte *všechny prostředky*. Pak vyberte **myAppGateway**.

2. V nabídce vlevo vyberte **back-endové fondy** .

3. Vyberte **myBackendPool**.

4. V části **cíle**vyberte v rozevíracím seznamu možnost **virtuální počítač** .

5. V části **virtuální počítač** a **Síťová rozhraní**vyberte virtuální počítače **myVM** a **myVM2** a jejich přidružená síťová rozhraní z rozevíracích seznamů.

    ![Přidání back-endových serverů](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Vyberte **Uložit**.

7. Než budete pokračovat k dalšímu kroku, počkejte na dokončení nasazení.

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

I když služba IIS není nutná k vytvoření aplikační brány, nainstalovali jste se v tomto rychlém startu, abyste ověřili, jestli Azure úspěšně vytvořil Aplikační bránu. Použijte službu IIS k otestování služby Application Gateway:

1. přehledem vyhledejte veřejnou IP adresu pro aplikační **Na stránce** bránu.![Zaznamenejte si veřejnou IP adresu](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) aplikační brány nebo vyberte **všechny prostředky**, do vyhledávacího pole zadejte *myAGPublicIPAddress* a potom ho vyberte ve výsledcích hledání. Azure zobrazí veřejnou IP adresu na stránce **Přehled** .
2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.
3. Ověřte odpověď. Platná odpověď ověří, že se služba Application Gateway úspěšně vytvořila, a může se úspěšně připojit k back-endu.![Otestování aplikační brány](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili v rámci služby Application Gateway, odeberte skupinu prostředků. Odebráním skupiny prostředků odeberete také aplikační bránu a všechny související prostředky. 

Odebrání skupiny prostředků:

1. V nabídce Azure Portal vyberte **skupiny prostředků** nebo vyhledejte a vyberte *skupiny prostředků*.
2. Na stránce **skupiny prostředků** vyhledejte v seznamu **myResourceGroupAG** a pak ho vyberte.
3. Na **stránce skupina prostředků**vyberte **Odstranit skupinu prostředků**.
4. Zadejte *myResourceGroupAG* pro **typ název skupiny prostředků** a pak vyberte **Odstranit** .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa webového provozu pomocí aplikační brány a Azure CLI](./tutorial-manage-web-traffic-cli.md)
