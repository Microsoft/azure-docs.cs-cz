---
title: 'Kurz: vytvoření pomocí portálu – Firewall webových aplikací'
description: V tomto kurzu se naučíte, jak vytvořit Aplikační bránu pomocí brány firewall webových aplikací pomocí Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: tutorial
ms.date: 09/16/2020
ms.author: victorh
ms.openlocfilehash: 58002140cd6ec0cd90eefc506dc743be05e7be7e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91274465"
---
# <a name="tutorial-create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Kurz: vytvoření aplikační brány pomocí brány firewall webových aplikací pomocí Azure Portal

V tomto kurzu se dozvíte, jak pomocí Azure Portal vytvořit Application Gateway pomocí firewallu webových aplikací (WAF). WAF používá k ochraně aplikace pravidla [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project). Tato pravidla zahrnují ochranu před útoky, jako je injektáž SQL, skriptování mezi weby a krádeže relací. Po vytvoření služby Application Gateway ji otestujete, abyste se ujistili, že funguje správně. V případě Azure Application Gateway nasměrujete webový provoz aplikace na konkrétní prostředky přiřazením posluchačů k portům, vytvořením pravidel a přidáním prostředků do back-endového fondu. V zájmu zjednodušení používá tento kurz jednoduché nastavení s veřejnou front-end IP adresou, základní naslouchací proces, který hostuje jednu lokalitu v této aplikační bráně, dva virtuální počítače používané pro back-end fond a pravidlo základního směrování požadavků.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit aplikační bránu se zapnutým Firewallem webových aplikací
> * Vytvoření virtuálních počítačů používaných jako servery back-end
> * Vytvoření účtu úložiště a konfigurace diagnostiky
> * Otestování aplikační brány

![Příklad firewallu webových aplikací](../media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!---If you prefer, you can complete this tutorial using [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md) or [Azure CLI](tutorial-restrict-web-traffic-cli.md).--->

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Vytvoření brány Application Gateway

Aby mohl Azure komunikovat mezi prostředky, potřebuje virtuální síť. Můžete buď vytvořit novou virtuální síť, nebo použít existující. V tomto příkladu vytvoříte novou virtuální síť. Virtuální síť můžete vytvořit současně s aplikační bránou. Instance Application Gateway se vytvářejí v oddělených podsítích. V tomto příkladu vytvoříte dvě podsítě: jednu pro aplikační bránu a druhou pro back-end servery.

V levé nabídce Azure Portal vyberte **vytvořit prostředek** . Zobrazí se **nové** okno.

Vyberte **sítě** a v seznamu **Doporučené** vyberte **Application Gateway** .

### <a name="basics-tab"></a>Karta základy

1. Na kartě **základy** zadejte tyto hodnoty pro následující nastavení služby Application Gateway:

   - **Skupina prostředků**: pro skupinu prostředků vyberte **myResourceGroupAG** . Pokud neexistuje, vyberte **vytvořit novou** a vytvořte ji.
   - **Název aplikační brány**: jako název služby Application Gateway zadejte *myAppGateway* .
   - **Úroveň**: vyberte **WAF v2**.

     ![Vytvořit novou aplikační bránu: Základy](../media/application-gateway-web-application-firewall-portal/application-gateway-create-basics.png)

2.  Aby mohl Azure komunikovat mezi prostředky, které vytvoříte, potřebuje virtuální síť. Můžete buď vytvořit novou virtuální síť, nebo použít existující. V tomto příkladu vytvoříte novou virtuální síť ve stejnou chvíli, kdy vytvoříte Aplikační bránu. Instance Application Gateway se vytvářejí v oddělených podsítích. V tomto příkladu vytvoříte dvě podsítě: jednu pro aplikační bránu a druhou pro back-end servery.

    V části **Konfigurovat virtuální síť**vytvořte novou virtuální síť výběrem možnosti **vytvořit nový**. V okně **vytvořit virtuální síť** , které se otevře, zadejte následující hodnoty pro vytvoření virtuální sítě a dvě podsítě:

    - **Název**: jako název virtuální sítě zadejte *myVNet* .

    - **Název podsítě** (Application Gateway podsíť): v mřížce **podsítě** se zobrazí podsíť s názvem *výchozí*. Změňte název této podsítě na *myAGSubnet*.<br>Podsíť aplikační brány může obsahovat jenom aplikační brány. Žádné další prostředky nejsou povoleny.

    - **Název podsítě** (podsíť back-end serveru): v druhém řádku mřížky **podsítě** zadejte *myBackendSubnet* do sloupce **název podsítě** .

    - **Rozsah adres** (podsíť back-end serveru): ve druhém řádku mřížky **podsítě** zadejte rozsah adres, který se nepřekrývá s rozsahem adres *myAGSubnet*. Pokud má například rozsah adres *myAGSubnet* 10.0.0.0/24, zadejte pro rozsah adres *myBackendSubnet* *10.0.1.0/24* .

    Výběrem **OK** zavřete okno **vytvořit virtuální síť** a uložte nastavení virtuální sítě.

     ![Vytvořit novou aplikační bránu: virtuální síť](../media/application-gateway-web-application-firewall-portal/application-gateway-create-vnet.png)
    
3. Na kartě **základy** přijměte výchozí hodnoty pro ostatní nastavení a potom vyberte **Další: front-endu**.

### <a name="frontends-tab"></a>Karta front-endu

1. Na kartě **front-endu** ověřte, že **typ IP adresy front-end** je nastavený na **veřejné**. <br>Front-end IP adresu můžete nakonfigurovat tak, aby byla veřejná nebo soukromá jako na základě vašeho případu použití. V tomto příkladu zvolíte veřejnou front-end IP adresu.
   > [!NOTE]
   > V případě SKU Application Gateway v2 můžete zvolit jenom **veřejnou** konfiguraci IP adresy front-endu. V tuto chvíli není u této SKU verze V2 povolená soukromá konfigurace IP adresy front-endu.

2. Zvolte **vytvořit nový** pro **veřejnou IP adresu** a jako název veřejné IP adresy zadejte *myAGPublicIPAddress* a pak vyberte **OK**. 

     ![Vytvořit novou aplikační bránu: front-endové](../media/application-gateway-web-application-firewall-portal/application-gateway-create-frontends.png)

3. Vyberte **Další: back-endy**.

### <a name="backends-tab"></a>Karta back-endy

Back-end fond slouží ke směrování požadavků na servery back-end, které obsluhují požadavek. Back-endové fondy se dají skládat ze síťových adaptérů, virtuálních počítačů a virtuálních IP adres, interních IP adres, plně kvalifikovaných názvů domény (FQDN) a back-endu s více klienty, jako je Azure App Service. V tomto příkladu vytvoříte prázdný back-end fond s aplikační bránou a potom přidáte cíle back-end do fondu back-end.

1. Na kartě back- **endy** vyberte **+ Přidat back-end fond**.

2. V okně **Přidat fond back-end** , které se otevře, zadejte následující hodnoty a vytvořte prázdný back-end fond:

    - **Název**: jako název back-end fondu zadejte *myBackendPool* .
    - **Přidat back-end fond bez cílů**: vyberte **Ano** , pokud chcete vytvořit back-end fond bez cílů. Po vytvoření aplikační brány přidáte cíle back-endu.

3. V okně **Přidat fond back-endu** vyberte **Přidat** a uložte konfiguraci fondu back-end a vraťte se na kartu back- **endy** .

     ![Vytvořit novou aplikační bránu: back-endy](../media/application-gateway-web-application-firewall-portal/application-gateway-create-backends.png)

4. Na kartě **back-endy** vyberte **Další: Konfigurace**.

### <a name="configuration-tab"></a>Karta konfigurace

Na kartě **Konfigurace** se připojíte k front-endovému a back-endovému fondu, který jste vytvořili pomocí pravidla směrování.

1. Ve sloupci **pravidla směrování** vyberte **Přidat pravidlo** .

2. V okně **Přidat pravidlo směrování** , které se otevře, jako **název pravidla**zadejte *myRoutingRule* .

3. Pravidlo směrování vyžaduje naslouchací proces. Na kartě **naslouchací proces** v okně **Přidat pravidlo směrování** zadejte následující hodnoty pro naslouchací proces:

    - **Název naslouchacího procesu**: jako název naslouchacího procesu zadejte *MyListener* .
    - **Front-end IP adresa**: vyberte **veřejné** a zvolte veřejnou IP adresu, kterou jste vytvořili pro front-end.
  
      Přijměte výchozí hodnoty pro ostatní nastavení na kartě **naslouchací proces** a potom vyberte kartu **cílení na back-end** a nakonfigurujte zbývající část pravidla směrování.

   ![Vytvoření nové aplikační brány: naslouchací proces](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-listener.png)

4. Na kartě **cílení na server back-end** vyberte **MyBackendPool** pro **cíl back-endu**.

5. Pro **Nastavení http**vyberte **vytvořit novou** a vytvořte nové nastavení http. Nastavením protokolu HTTP se určí chování pravidla směrování. V okně **Přidat nastavení protokolu HTTP** , které se otevře, zadejte *myHTTPSetting* pro **název nastavení http**. Přijměte výchozí hodnoty pro ostatní nastavení v okně **Přidat nastavení http** a pak vyberte **Přidat** a vraťte se do okna **Přidat pravidlo směrování** . 

     ![Vytvořit novou aplikační bránu: nastavení HTTP](../media/application-gateway-web-application-firewall-portal/application-gateway-create-httpsetting.png)

6. V okně **Přidat pravidlo směrování** vyberte **Přidat** a uložte pravidlo směrování a vraťte se na kartu **Konfigurace** .

     ![Vytvoření nové aplikační brány: pravidlo směrování](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-backends.png)

7. Vyberte **Další: značky** a potom **Další: zkontrolovat + vytvořit**.

### <a name="review--create-tab"></a>Revize + vytvořit kartu

Zkontrolujte nastavení na kartě **Revize + vytvořit** a pak vyberte **vytvořit** k vytvoření virtuální sítě, veřejné IP adresy a aplikační brány. Vytvoření služby Application Gateway v Azure může trvat několik minut. 

Před přechodem k další části počkejte na úspěšné dokončení nasazení.

## <a name="add-backend-targets"></a>Přidat cíle back-endu

V tomto příkladu budete jako cílový back-end používat virtuální počítače. Můžete buď použít stávající virtuální počítače, nebo vytvořit nové. Vytvoříte dva virtuální počítače, které Azure používá jako servery back-end pro službu Application Gateway.

Uděláte to takto:

1. Vytvořte dva nové virtuální počítače, *myVM* a *myVM2*, které se budou používat jako servery back-end.
2. Nainstalujte službu IIS na virtuální počítače, abyste ověřili, že se služba Application Gateway úspěšně vytvořila.
3. Přidejte back-end servery do fondu back-end.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. V Azure Portal vyberte **vytvořit prostředek**. Zobrazí se **nové** okno.
2. V seznamu **oblíbených** vyberte **Windows Server 2016 Datacenter** . Zobrazí se stránka **vytvořit virtuální počítač** .<br>Application Gateway může směrovat provoz na libovolný typ virtuálního počítače, který se používá v jeho fondu back-endu. V tomto příkladu použijete Windows Server 2016 Datacenter.
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

    ![Instalace vlastního rozšíření](../media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

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

1. Vyberte **všechny prostředky**a pak vyberte **myAppGateway**.

2. V nabídce vlevo vyberte **back-endové fondy** .

3. Vyberte **myBackendPool**.

4. V části **cíle**vyberte v rozevíracím seznamu možnost **virtuální počítač** .

5. V části **virtuální počítač** a **Síťová rozhraní**vyberte virtuální počítače **myVM** a **myVM2** a jejich přidružená síťová rozhraní z rozevíracích seznamů.

    ![Přidání back-endových serverů](../media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

6. Vyberte **Uložit**.

7. Než budete pokračovat k dalšímu kroku, počkejte na dokončení nasazení.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Vytvoření účtu úložiště a konfigurace diagnostiky

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Pro účely tohoto článku používá Aplikační brána účet úložiště k ukládání dat pro účely detekce a prevence. K zaznamenávání dat můžete použít také protokoly Azure Monitor nebo centra událostí.

1. V levém horním rohu Azure Portal vyberte **vytvořit prostředek** .
1. Vyberte **úložiště**a pak vyberte **účet úložiště**.
1. Pro *skupinu prostředků*vyberte **myResourceGroupAG** pro skupinu prostředků.
1. Jako název účtu úložiště zadejte *myagstore1* .
1. Přijměte výchozí hodnoty pro ostatní nastavení a potom vyberte **zkontrolovat + vytvořit**.
1. Zkontrolujte nastavení a pak vyberte **vytvořit**.

### <a name="configure-diagnostics"></a>Konfigurace diagnostiky

Nakonfigurujte diagnostiku, aby se data zaznamenávala do protokolů ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog a ApplicationGatewayFirewallLog.

1. V nabídce na levé straně vyberte **všechny prostředky**a pak vyberte *myAppGateway*.
2. V části monitorování vyberte **nastavení diagnostiky**.
3. Vyberte **Přidat nastavení diagnostiky**.
4. Jako název pro nastavení diagnostiky zadejte *myDiagnosticsSettings* .
5. Vyberte možnost **archivovat do účtu úložiště**a pak vyberte **Konfigurovat** a vyberte účet úložiště *myagstore1* , který jste vytvořili dříve, a pak vyberte **OK**.
6. Vyberte protokoly aplikační brány, které chcete shromažďovat a uchovávat.
7. Vyberte **Uložit**.

    ![Konfigurace diagnostiky](../media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="create-and-link-a-web-application-firewall-policy"></a>Vytvoření a propojení zásad firewallu webových aplikací

Všechna přizpůsobení a nastavení WAF se nacházejí v samostatném objektu, který se nazývá zásady WAF. Zásada musí být přidružená k vašemu Application Gateway. Pokud chcete vytvořit zásadu WAF, přečtěte si téma [Vytvoření zásady WAF](create-waf-policy-ag.md). Po vytvoření můžete zásadu přidružit k vašemu WAF (nebo k individuálnímu naslouchacího procesu) ze zásady WAF na **přidružené kartě Application gateways** . 

![Přidružené aplikační brány](../media/application-gateway-web-application-firewall-portal/associated-application-gateways.png)

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

I když služba IIS není nutná k vytvoření aplikační brány, nainstalujete ji, abyste ověřili, jestli Azure úspěšně vytvořil Aplikační bránu. Použijte službu IIS k otestování služby Application Gateway:

1. Na stránce s **přehledem** vyhledejte veřejnou IP adresu pro aplikační bránu. ![ Záznam veřejné IP adresy aplikační brány](../media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png) 

   Nebo můžete vybrat **všechny prostředky**, do vyhledávacího pole zadat *myAGPublicIPAddress* a pak ho vybrat ve výsledcích hledání. Azure zobrazí veřejnou IP adresu na stránce **Přehled** .
1. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.
1. Ověřte odpověď. Platná odpověď ověří, že se služba Application Gateway úspěšně vytvořila, a může se úspěšně připojit k back-endu.

   ![Otestování aplikační brány](../media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili v rámci služby Application Gateway, odeberte skupinu prostředků. Odebráním skupiny prostředků odeberete také aplikační bránu a všechny související prostředky. 

Odebrání skupiny prostředků:

1. V levé nabídce Azure Portal vyberte **skupiny prostředků**.
2. Na stránce **skupiny prostředků** vyhledejte v seznamu **myResourceGroupAG** a pak ho vyberte.
3. Na **stránce skupina prostředků**vyberte **Odstranit skupinu prostředků**.
4. Jako **název skupiny prostředků** zadejte *myResourceGroupAG* a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o firewallu webových aplikací](../overview.md)
