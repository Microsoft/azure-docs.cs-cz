---
title: 'Kurz: Vytvoření pomocí portálu – Brána firewall pro webové aplikace'
description: V tomto kurzu se dozvíte, jak vytvořit aplikační bránu s bránou webové aplikace pomocí webu Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 68a9f051bf3d59cbf32377cb503e9ded0a54d5e9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74049222"
---
# <a name="tutorial-create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Kurz: Vytvoření aplikační brány s bránou webové aplikace pomocí portálu Azure

Tento kurz ukazuje, jak pomocí portálu Azure vytvořit aplikační bránu s bránou webové aplikace (WAF). WAF používá k ochraně aplikace pravidla [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project). Tato pravidla zahrnují ochranu před útoky, jako je injektáž SQL, skriptování mezi weby a krádeže relací. Po vytvoření brány aplikace ji otestujete, abyste se ujistili, že funguje správně. S Azure Application Gateway nasměrujete webový provoz aplikací na konkrétní prostředky přiřazením posluchačů k portům, vytvořením pravidel a přidáním prostředků do back-endového fondu. Z důvodu jednoduchosti tento kurz používá jednoduché nastavení s veřejnou front-endovou IP adresou, základní naslouchací proces pro hostování jednoho webu na této aplikační bráně, dva virtuální počítače používané pro back-endový fond a pravidlo směrování základního požadavku.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit aplikační bránu se zapnutým Firewallem webových aplikací
> * Vytvoření virtuálních počítačů používaných jako back-endové servery
> * Vytvoření účtu úložiště a konfigurace diagnostiky
> * Testování brány Application Gateway

![Příklad firewallu webových aplikací](../media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!---If you prefer, you can complete this tutorial using [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md) or [Azure CLI](tutorial-restrict-web-traffic-cli.md).--->

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Aby Azure mohl komunikovat mezi prostředky, potřebuje virtuální síť. Můžete buď vytvořit novou virtuální síť, nebo použít existující. V tomto příkladu vytvoříte novou virtuální síť. Virtuální síť můžete vytvořit současně s aplikační bránou. Instance aplikační brány se vytvářejí v samostatných podsítích. V tomto příkladu vytvoříte dvě podsítě: jednu pro aplikační bránu a druhou pro servery back-endu.

V levé nabídce portálu Azure vyberte **Vytvořit prostředek.** Zobrazí se okno **Nový.**

Vyberte **Síť** a pak v seznamu **Doporučené** vyberte **Aplikační brána.**

### <a name="basics-tab"></a>Karta Základy

1. Na kartě **Základy** zadejte tyto hodnoty pro následující nastavení brány aplikace:

   - **Skupina prostředků**: Vyberte **myResourceGroupAG** pro skupinu prostředků. Pokud neexistuje, vyberte **Vytvořit nový** a vytvořte ho.
   - **Název aplikační brány**: Zadejte *myAppGateway* pro název aplikační brány.
   - **Úroveň**: vyberte **WAF V2**.

     ![Vytvořit novou aplikační bránu: Základy](../media/application-gateway-web-application-firewall-portal/application-gateway-create-basics.png)

2.  Pro Azure komunikovat mezi prostředky, které vytvoříte, potřebuje virtuální síť. Můžete buď vytvořit novou virtuální síť, nebo použít existující. V tomto příkladu vytvoříte novou virtuální síť současně s vytvořením aplikační brány. Instance aplikační brány se vytvářejí v samostatných podsítích. V tomto příkladu vytvoříte dvě podsítě: jednu pro aplikační bránu a druhou pro servery back-endu.

    V části **Konfigurace virtuální sítě**vytvořte novou virtuální síť výběrem **možnosti Vytvořit novou**. V okně **Vytvořit virtuální síť,** které se otevře, zadejte následující hodnoty pro vytvoření virtuální sítě a dvou podsítí:

    - **Název**: Zadejte *myVNet* pro název virtuální sítě.

    - **Název podsítě** (podsíť Aplikační brána): Mřížka **podsítí** zobrazí podsíť s názvem *Výchozí*. Změňte název této podsítě na *myAGSubnet*.<br>Podsíť aplikační brány může obsahovat pouze aplikační brány. Nejsou povoleny žádné další prostředky.

    - **Název podsítě** (podsíť back-endového serveru): Do druhého řádku **mřížky podsítí** zadejte do sloupce **název podsítě** *myBackendSubnet.*

    - **Rozsah adres** (podsíť back-endového serveru): Do druhého řádku **mřížky podsítí** zadejte rozsah adres, který se nepřekrývá s rozsahem adres *myAGSubnet*. Pokud je například rozsah adres *myAGSubnet* 10.0.0.0/24, zadejte *10.0.1.0/24* pro rozsah adres *myBackendSubnet*.

    Výběrem **možnosti OK** zavřete okno **Vytvořit virtuální síť** a uložte nastavení virtuální sítě.

     ![Vytvoření nové aplikační brány: virtuální síť](../media/application-gateway-web-application-firewall-portal/application-gateway-create-vnet.png)
    
3. Na kartě **Základy** přijměte výchozí hodnoty pro ostatní nastavení a pak vyberte **Další: Frontendy**.

### <a name="frontends-tab"></a>Karta Front-endy

1. Na kartě **Frontendy** ověřte, zda je **typ ip adresy front-endu** nastaven na **veřejné**. <br>Front-endovou IP adresu můžete nakonfigurovat jako veřejnou nebo soukromou podle případu použití. V tomto příkladu zvolíte veřejnou ip adresu front-endu.
   > [!NOTE]
   > Pro aplikační bránu v2 SKU můžete zvolit pouze **veřejnou** front-endovou konfiguraci IP. Privátní front-endová konfigurace IP není aktuálně povolena pro tuto skladovou položku v2.

2. Zvolte **Vytvořit novou** pro **veřejnou IP adresu** a zadejte adresu *myAGPublicIPAddress* pro název veřejné IP adresy a pak vyberte **OK**. 

     ![Vytvořit novou aplikační bránu: front-endy](../media/application-gateway-web-application-firewall-portal/application-gateway-create-frontends.png)

3. Vyberte **další: Back-endy**.

### <a name="backends-tab"></a>Karta Back-endy

Back-endový fond se používá ke směrování požadavků na servery back-endu, které tento požadavek obsluhují. Back-endové fondy se můžou skládat z připojení k síťové karty, škálovacísady virtuálních počítačů, veřejných IP adres, interních IP adres, plně kvalifikovaných názvů domén (Plně kvalifikovaný název domény) a back-endů s více tenanty, jako je Služba Azure App Service. V tomto příkladu vytvoříte prázdný back-endový fond s vaší aplikační bránou a pak přidáte back-endové cíle do back-endového fondu.

1. Na kartě **Backends** vyberte **+Přidat fond back-endů**.

2. V okně **Přidat back-endový fond,** které se otevře, zadejte následující hodnoty pro vytvoření prázdného back-endového fondu:

    - **Název**: Zadejte *myBackendPool* pro název back-endového fondu.
    - **Přidat back-endový fond bez cílů**: Výběrem **možnosti Ano** vytvořte back-endový fond bez cílů. Po vytvoření brány aplikace přidáte back-endové cíle.

3. V okně **Přidat fond back-endu** vyberte **Přidat,** chcete-li uložit konfiguraci back-endového fondu a vrátit se na kartu **Back-endy.**

     ![Vytvořit novou aplikační bránu: back-endy](../media/application-gateway-web-application-firewall-portal/application-gateway-create-backends.png)

4. Na kartě **Back-endy** vyberte **Další: Konfigurace**.

### <a name="configuration-tab"></a>Karta Konfigurace

Na kartě **Konfigurace** připojíte front-endový a back-endový fond, který jste vytvořili, pomocí pravidla směrování.

1. Ve sloupci **Pravidla směrování** vyberte **Přidat pravidlo.**

2. V okně **Přidat směrovací pravidlo,** které se otevře, zadejte *myRoutingRule* pro **název pravidla**.

3. Pravidlo směrování vyžaduje naslouchací proces. Na kartě **Listener** v okně **Přidat pravidlo směrování** zadejte pro posluchače následující hodnoty:

    - **Název naslouchací proces**: Zadejte *myListener* pro název posluchače.
    - **Front-end IP**: Vyberte **Veřejné,** chcete-li zvolit veřejnou IP adresu, kterou jste pro front-end vytvořili.
  
      Přijměte výchozí hodnoty pro ostatní nastavení na kartě **Naslouchací proces** a pak vyberte kartu **Cíle back-endu** a nakonfigurujte zbytek pravidla směrování.

   ![Vytvořit novou aplikační bránu: naslouchací proces](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-listener.png)

4. Na kartě **Cíle back-endu** vyberte **myBackendPool** pro **cíl back-endu**.

5. Pro **nastavení HTTP**vyberte **Vytvořit nový** a vytvořte nové nastavení HTTP. Nastavení HTTP určí chování pravidla směrování. V okně **Přidat nastavení PROTOKOLU HTTP,** které se otevře, zadejte pro **název nastavení HTTP nastavení myHTTPSetting**. *myHTTPSetting* Přijměte výchozí hodnoty pro ostatní nastavení v **okně Přidat nastavení HTTP** a pak vyberte **Přidat** a vraťte se do okna Přidat **pravidlo směrování.** 

     ![Vytvořit novou aplikační bránu: Nastavení HTTP](../media/application-gateway-web-application-firewall-portal/application-gateway-create-httpsetting.png)

6. V okně **Přidat pravidlo směrování** vyberte **Přidat,** chcete-li uložit pravidlo směrování a vrátit se na kartu **Konfigurace.**

     ![Vytvořit novou aplikační bránu: pravidlo směrování](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-backends.png)

7. Vyberte **další: Značky** a pak **Další: Zkontrolujte + vytvořit**.

### <a name="review--create-tab"></a>Karta Revize + vytvoření

Zkontrolujte nastavení na kartě **Revize + vytvořit** a pak vyberte **Vytvořit,** chcete-li vytvořit virtuální síť, veřejnou IP adresu a bránu aplikace. Vytvoření brány aplikace azure může trvat několik minut. 

Počkejte, dokud nasazení úspěšně dokončí před přechodem na další část.

## <a name="add-backend-targets"></a>Přidání back-endových cílů

V tomto příkladu budete používat virtuální počítače jako cílový back-end. Můžete buď použít existující virtuální počítače, nebo vytvořit nové. Vytvoříte dva virtuální počítače, které Azure používá jako back-endové servery pro aplikační bránu.

Chcete-li to provést, budete:

1. Vytvořte dva nové virtuální servery, *myVM* a *myVM2*, které se použijí jako back-endové servery.
2. Nainstalujte službu IIS do virtuálních počítačů a ověřte, zda byla aplikační brána úspěšně vytvořena.
3. Přidejte back-endové servery do back-endového fondu.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. Na webu Azure Portal vyberte **Vytvořit prostředek**. Zobrazí se okno **Nový.**
2. V seznamu **Oblíbené** vyberte **Datové centrum Windows Serveru 2016.** Zobrazí se stránka **Vytvořit virtuální počítač.**<br>Aplikační brána může směrovat provoz na libovolný typ virtuálního počítače používaného v back-endovém fondu. V tomto příkladu použijete datové centrum Windows Server 2016.
3. Na kartu **Základy** zadejte následující nastavení virtuálního počítače:

    - **Skupina prostředků**: Vyberte **myResourceGroupAG** pro název skupiny prostředků.
    - **Název virtuálního počítače**: Zadejte *myVM* pro název virtuálního počítače.
    - **Uživatelské jméno**: Zadejte *azureuser* pro uživatelské jméno správce.
    - **Heslo:** Zadejte *Azure123456!* pro heslo správce.
4. Přijměte další výchozí hodnoty a pak vyberte **Další: Disky**.  
5. Přijměte výchozí nastavení karty **Disky** a pak vyberte **Další: Síť**.
6. Na kartě **Síť** ověřte, zda je pro **virtuální síť** vybraná **síť myVNet** a **podsíť** nastavena na **myBackendSubnet**. Přijměte ostatní výchozí hodnoty a pak vyberte **Další: Správa**.<br>Aplikační brána může komunikovat s instancemi mimo virtuální síť, ve které se nachází, ale musíte zajistit připojení IP.
7. Na kartě **Správa** nastavte **diagnostiku spouštění** na **Vypnuto**. Přijměte ostatní výchozí hodnoty a pak vyberte **Zkontrolovat + vytvořit**.
8. Na kartě **Revize + vytvoření** zkontrolujte nastavení, opravte všechny chyby ověření a pak vyberte **Vytvořit**.
9. Před pokračováním počkejte na dokončení vytvoření virtuálního počítače.

### <a name="install-iis-for-testing"></a>Instalace služby IIS pro testování

V tomto příkladu nainstalujete službu IIS na virtuální počítače pouze k ověření Azure úspěšně vytvořil aplikační bránu.

1. Otevřete [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Pokud tak chcete provést, vyberte **Cloud Shell** z horního navigačního panelu portálu Azure a pak v rozevíracím seznamu vyberte **PowerShell.** 

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

3. Vytvořte druhý virtuální počítač a nainstalujte službu IIS pomocí kroků, které jste dříve dokončili. *MyVM2* použijte pro název virtuálního počítače a pro nastavení **VMName** rutiny **Set-AzVMExtension.**

### <a name="add-backend-servers-to-backend-pool"></a>Přidání back-endových serverů do back-endového fondu

1. Vyberte **Všechny prostředky**a pak **vyberte myAppGateway**.

2. V levé nabídce vyberte **back-endové fondy.**

3. Vyberte **myBackendPool**.

4. V části **Cíle**vyberte v rozevíracím seznamu virtuální **počítač.**

5. V části **VIRTUÁLNÍ POČÍTAČ** a **SÍŤOVÁ ROZHRANÍ**vyberte virtuální počítače **myVM** a **myVM2** a jejich přidružená síťová rozhraní z rozevíracích seznamů.

    ![Přidání back-endových serverů](../media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

6. Vyberte **Uložit**.

7. Před dokončením nasazení počkejte na dokončení nasazení, než přebudete pokračovat k dalšímu kroku.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Vytvoření účtu úložiště a konfigurace diagnostiky

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Pro tento článek aplikace brána používá účet úložiště pro ukládání dat pro účely zjišťování a prevence. K záznamu dat můžete taky použít protokoly Azure Monitoru nebo Centrum událostí.

1. V levém horním rohu portálu Azure vyberte **Vytvořit prostředek.**
1. Vyberte **Úložiště**a pak vyberte **Účet úložiště**.
1. Ve *skupině Prostředků*vyberte pro skupinu prostředků **myResourceGroupAG.**
1. Zadejte *myagstore1* pro název účtu úložiště.
1. Přijměte výchozí hodnoty pro ostatní nastavení a pak vyberte **Zkontrolovat + Vytvořit**.
1. Zkontrolujte nastavení a pak vyberte **Vytvořit**.

### <a name="configure-diagnostics"></a>Konfigurace diagnostiky

Nakonfigurujte diagnostiku, aby se data zaznamenávala do protokolů ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog a ApplicationGatewayFirewallLog.

1. V levé nabídce vyberte **Všechny prostředky**a pak *vyberte myAppGateway*.
2. V části Monitoring vyberte **Nastavení diagnostiky**.
3. vyberte **Přidat nastavení diagnostiky**.
4. Zadejte *myDiagnosticsSettings* jako název nastavení diagnostiky.
5. Vyberte **Archivovat účet úložiště**a pak vyberte **Konfigurovat,** vyberte dříve vytvořený účet úložiště *myagstore1* a pak vyberte **OK**.
6. Vyberte protokoly brány aplikace, které chcete shromažďovat a uchovávat.
7. Vyberte **Uložit**.

    ![Konfigurace diagnostiky](../media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="create-and-link-a-web-application-firewall-policy"></a>Vytvoření a propojení zásad brány firewall webových aplikací

Všechna vlastní nastavení a nastavení WAF jsou v samostatném objektu, který se nazývá zásady WAF. Zásady musí být přidruženy k bráně aplikace. Chcete-li vytvořit zásady WAF, přečtěte si informace [o vytvoření zásad waf](create-waf-policy-ag.md). Po vytvoření můžete zásadu přidružit k vašemu WAF (nebo jednotlivému naslouchací procesu) ze zásad WAF na kartě **Přidružené brány aplikací.** 

![Přidružené aplikační brány](../media/application-gateway-web-application-firewall-portal/associated-application-gateways.png)

## <a name="test-the-application-gateway"></a>Testování brány Application Gateway

I když služba IIS není nutné k vytvoření aplikační brány, nainstalovali jste ji k ověření, zda Azure úspěšně vytvořil aplikační bránu. Pomocí služby IIS otestujte bránu aplikace:

1. Nastránce **Přehled** najděte veřejnou IP adresu aplikační brány. ![Záznam veřejné IP adresy aplikační brány](../media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png) 

   Nebo můžete vybrat **všechny zdroje**, do vyhledávacího pole zadejte adresu *myAGPublicIPAddress* a pak ji vyberte ve výsledcích hledání. Azure zobrazí veřejnou IP adresu na stránce **Přehled.**
1. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.
1. Zkontrolujte odpověď. Platná odpověď ověří, zda byla aplikační brána úspěšně vytvořena a může se úspěšně připojit k back-endu.

   ![Otestování aplikační brány](../media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili pomocí aplikační brány, odeberte skupinu prostředků. Odebráním skupiny prostředků také odeberete bránu aplikace a všechny související prostředky. 

Odebrání skupiny prostředků:

1. V levé nabídce portálu Azure vyberte **skupiny prostředků**.
2. Na stránce **Skupiny prostředků** vyhledejte **myResourceGroupAG** v seznamu a vyberte ji.
3. Na **stránce Skupina prostředků**vyberte **Odstranit skupinu prostředků**.
4. Zadejte *myResourceGroupAG* pro **typ názvu skupiny prostředků** a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o bráně firewall webových aplikací](../overview.md)
