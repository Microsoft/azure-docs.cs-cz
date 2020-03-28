---
title: 'Kurz: Hostuje více webových stránek pomocí portálu Azure'
titleSuffix: Azure Application Gateway
description: V tomto kurzu se dozvíte, jak vytvořit aplikační bránu, která hostuje více webů pomocí portálu Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: victorh
ms.openlocfilehash: ca6be666a9b77532b4f1c61f6e3391c239e82c91
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74075151"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Kurz: Vytvoření a konfigurace aplikační brány pro hostování více webů pomocí portálu Azure

Portál Azure můžete použít ke [konfiguraci hostování více webových stránek](multiple-site-overview.md) při vytváření [aplikační brány](overview.md). V tomto kurzu definujete fondy back-endových adres pomocí virtuálních počítačů. Pak na základě domén, které vám patří, nakonfigurujete naslouchací procesy a pravidla, aby se webový provoz přesměroval na příslušné servery ve fondech. V tomto kurzu se předpokládá, že vlastníte několik domén, a jako příklady se používají domény *www.contoso.com* a *www.fabrikam.com*.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření služby Application Gateway
> * Vytvoření virtuálních počítačů pro back-endové servery
> * Vytvoření back-endových fondů s back-endovými servery
> * Vytvořit back-endové naslouchací procesy
> * Vytvořit pravidla směrování
> * Vytvoření záznamu CNAME v doméně

![Příklad směrování na více webů](./media/create-multiple-sites-portal/scenario.png)

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na webu Azure portal na adrese[https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

1. V levé nabídce portálu Azure vyberte **Vytvořit prostředek.** Zobrazí se okno **Nový.**

2. Vyberte **Síť** a pak v seznamu **Doporučené** vyberte **Aplikační brána.**

### <a name="basics-tab"></a>Karta Základy

1. Na kartě **Základy** zadejte tyto hodnoty pro následující nastavení brány aplikace:

   - **Skupina prostředků**: Vyberte **myResourceGroupAG** pro skupinu prostředků. Pokud neexistuje, vyberte **Vytvořit nový** a vytvořte ho.
   - **Název aplikační brány**: Zadejte *myAppGateway* pro název aplikační brány.

     ![Vytvořit novou aplikační bránu: Základy](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Pro Azure komunikovat mezi prostředky, které vytvoříte, potřebuje virtuální síť. Můžete buď vytvořit novou virtuální síť, nebo použít existující. V tomto příkladu vytvoříte novou virtuální síť současně s vytvořením aplikační brány. Instance aplikační brány se vytvářejí v samostatných podsítích. V tomto příkladu vytvoříte dvě podsítě: jednu pro aplikační bránu a druhou pro servery back-endu.

    V části **Konfigurace virtuální sítě**vyberte Vytvořit **nový** a vytvořte novou virtuální síť . V okně **Vytvořit virtuální síť,** které se otevře, zadejte následující hodnoty pro vytvoření virtuální sítě a dvou podsítí:

    - **Název**: Zadejte *myVNet* pro název virtuální sítě.

    - **Název podsítě** (podsíť Aplikační brána): Mřížka **podsítí** zobrazí podsíť s názvem *Výchozí*. Změňte název této podsítě na *myAGSubnet*.<br>Podsíť aplikační brány může obsahovat pouze aplikační brány. Nejsou povoleny žádné další prostředky.

    - **Název podsítě** (podsíť back-endového serveru): Do druhého řádku **mřížky podsítí** zadejte do sloupce **název podsítě** *myBackendSubnet.*

    - **Rozsah adres** (podsíť back-endového serveru): Do druhého řádku **mřížky podsítí** zadejte rozsah adres, který se nepřekrývá s rozsahem adres *myAGSubnet*. Pokud je například rozsah adres *myAGSubnet* 10.0.0.0/24, zadejte *10.0.1.0/24* pro rozsah adres *myBackendSubnet*.

    Výběrem **možnosti OK** zavřete okno **Vytvořit virtuální síť** a uložte nastavení virtuální sítě.

     ![Vytvoření nové aplikační brány: virtuální síť](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Na kartě **Základy** přijměte výchozí hodnoty pro ostatní nastavení a pak vyberte **Další: Frontendy**.

### <a name="frontends-tab"></a>Karta Front-endy

1. Na kartě **Frontendy** ověřte, zda je **typ ip adresy front-endu** nastaven na **veřejné**. <br>Front-endovou IP adresu můžete nakonfigurovat jako veřejnou nebo soukromou podle případu použití. V tomto příkladu zvolíte veřejnou ip adresu front-endu.
   > [!NOTE]
   > Pro aplikační bránu v2 SKU můžete zvolit pouze **veřejnou** front-endovou konfiguraci IP. Privátní front-endová konfigurace IP není aktuálně povolena pro tuto skladovou položku v2.

2. Zvolte **Vytvořit novou** pro **veřejnou IP adresu** a zadejte adresu *myAGPublicIPAddress* pro název veřejné IP adresy a pak vyberte **OK**. 

     ![Vytvořit novou aplikační bránu: front-endy](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Vyberte **další: Back-endy**.

### <a name="backends-tab"></a>Karta Back-endy

Back-endový fond se používá ke směrování požadavků na servery back-endu, které tento požadavek obsluhují. Back-endové fondy můžou být síťové karty, škálovací sady virtuálních počítačů, veřejné IP adresy, interní IP adresy, plně kvalifikované názvy domén (FQDN) a back-endy s více tenanty, jako je Azure App Service. V tomto příkladu vytvoříte prázdný back-endový fond s vaší aplikační bránou a pak přidáte back-endové cíle do back-endového fondu.

1. Na kartě **Backends** vyberte **+Přidat fond back-endů**.

2. V okně **Přidat back-endový fond,** které se otevře, zadejte následující hodnoty pro vytvoření prázdného back-endového fondu:

    - **Název**: Zadejte *contosoPool* pro název back-endového fondu.
    - **Přidat back-endový fond bez cílů**: Výběrem **možnosti Ano** vytvořte back-endový fond bez cílů. Po vytvoření brány aplikace přidáte back-endové cíle.

3. V okně **Přidat fond back-endu** vyberte **Přidat,** chcete-li uložit konfiguraci back-endového fondu a vrátit se na kartu **Back-endy.**
4. Nyní přidejte další back-endový fond s názvem *fabrikamPool*.

     ![Vytvořit novou aplikační bránu: back-endy](./media/create-multiple-sites-portal/backend-pools.png)

4. Na kartě **Back-endy** vyberte **Další: Konfigurace**.

### <a name="configuration-tab"></a>Karta Konfigurace

Na kartě **Konfigurace** připojíte fondy front-enda a back-endu, které jste vytvořili pomocí pravidla směrování.

1. Ve sloupci **Pravidla směrování** vyberte **Přidat pravidlo.**

2. V okně **Přidat směrovací pravidlo,** které se otevře, zadejte *contosoRule* pro **název pravidla**.

3. Pravidlo směrování vyžaduje naslouchací proces. Na kartě **Listener** v okně **Přidat pravidlo směrování** zadejte pro posluchače následující hodnoty:

    - **Název posluchače**: Zadejte *contosoListener* pro název posluchače.
    - **Front-end IP**: Vyberte **Veřejné,** chcete-li zvolit veřejnou IP adresu, kterou jste pro front-end vytvořili.

   V části **Další nastavení**:
   - **Typ naslouchacího procesu**: Více webů
   - **Název hostitele**: **www.contoso.com**

   Přijměte výchozí hodnoty pro ostatní nastavení na kartě **Naslouchací proces** a pak vyberte kartu **Cíle back-endu** a nakonfigurujte zbytek pravidla směrování.

   ![Vytvořit novou aplikační bránu: naslouchací proces](./media/create-multiple-sites-portal/routing-rule.png)

4. Na kartě **Cíle back-endu** vyberte **contosoPool** pro **back-endový cíl**.

5. Pro **nastavení HTTP**vyberte **Vytvořit nový** a vytvořte nové nastavení HTTP. Nastavení HTTP určí chování pravidla směrování. V okně **Přidat nastavení PROTOKOLU HTTP,** které se otevře, zadejte *contosoHTTPSetting* pro **název nastavení HTTP**. Přijměte výchozí hodnoty pro ostatní nastavení v **okně Přidat nastavení HTTP** a pak vyberte **Přidat** a vraťte se do okna Přidat **pravidlo směrování.** 

6. V okně **Přidat pravidlo směrování** vyberte **Přidat,** chcete-li uložit pravidlo směrování a vrátit se na kartu **Konfigurace.**
7. Vyberte **Přidat pravidlo** a přidejte podobné pravidlo, naslouchací proces, back-endový cíl a nastavení HTTP pro fabrikam.

     ![Vytvořit novou aplikační bránu: pravidlo směrování](./media/create-multiple-sites-portal/fabrikamRule.png)

7. Vyberte **další: Značky** a pak **Další: Zkontrolujte + vytvořit**.

### <a name="review--create-tab"></a>Karta Revize + vytvoření

Zkontrolujte nastavení na kartě **Revize + vytvořit** a pak vyberte **Vytvořit,** chcete-li vytvořit virtuální síť, veřejnou IP adresu a bránu aplikace. Vytvoření brány aplikace azure může trvat několik minut.

Počkejte, dokud nasazení úspěšně dokončí před přechodem na další část.

## <a name="add-backend-targets"></a>Přidání back-endových cílů

V tomto příkladu budete používat virtuální počítače jako cílový back-end. Můžete buď použít existující virtuální počítače, nebo vytvořit nové. Vytvoříte dva virtuální počítače, které Azure používá jako back-endové servery pro aplikační bránu.

Chcete-li přidat back-endové cíle, budete:

1. Vytvořte dva nové virtuální servery, *contosoVM* a *fabrikamVM*, které se použijí jako back-endové servery.
2. Nainstalujte službu IIS do virtuálních počítačů a ověřte, zda byla aplikační brána úspěšně vytvořena.
3. Přidejte back-endové servery do back-endových fondů.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. Na webu Azure Portal vyberte **Vytvořit prostředek**. Zobrazí se okno **Nový.**
2. Vyberte **Výpočetní výkon** a pak v seznamu **Oblíbené** vyberte **Datové centrum Windows Serveru 2016.** Zobrazí se stránka **Vytvořit virtuální počítač.**<br>Aplikační brána může směrovat provoz na libovolný typ virtuálního počítače používaného v back-endovém fondu. V tomto příkladu použijete datové centrum Windows Server 2016.
3. Na kartu **Základy** zadejte následující nastavení virtuálního počítače:

    - **Skupina prostředků**: Vyberte **myResourceGroupAG** pro název skupiny prostředků.
    - **Název virtuálního počítače**: Zadejte *contosoVM* pro název virtuálního počítače.
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

    ![Instalace vlastního rozšíření](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Spuštěním následujícího příkazu nainstalujte službu IIS na virtuální počítač: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Vytvořte druhý virtuální počítač a nainstalujte službu IIS pomocí kroků, které jste dříve dokončili. Použijte *fabrikamVM* pro název virtuálního počítače a pro nastavení **VMName** rutiny **Set-AzVMExtension.**

### <a name="add-backend-servers-to-backend-pools"></a>Přidání back-endových serverů do back-endových fondů

1. Vyberte **Všechny prostředky**a pak **vyberte myAppGateway**.

2. V levé nabídce vyberte **back-endové fondy.**

3. Vyberte **contosoPool**.

4. V části **Cíle**vyberte v rozevíracím seznamu virtuální **počítač.**

5. V části **VIRTUÁLNÍ POČÍTAČ** a **SÍŤOVÁ ROZHRANÍ**vyberte virtuální počítač **contosoVM** a je to přidružené síťové rozhraní z rozevíracích seznamů.

    ![Přidání back-endových serverů](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. Vyberte **Uložit**.
7. Opakováním přidáte *fabrikamVM* a rozhraní do *fondu fabrikamPool*.

Před dokončením nasazení počkejte na dokončení nasazení, než přebudete pokračovat k dalšímu kroku.

## <a name="create-a-www-a-record-in-your-domains"></a>Vytvoření záznamu www A ve vašich doménách

Po vytvoření aplikační brány s veřejnou IP adresou můžete získat IP adresu a použít ji k vytvoření záznamu A ve vašich doménách. 

1. Klepněte na tlačítko **Všechny prostředky**a potom klepněte na **položku myAGPublicIPAddress**.

    ![Záznam adresy DNS brány aplikace](./media/create-multiple-sites-portal/public-ip.png)

2. Zkopírujte IP adresu a použijte ji jako hodnotu pro nový *záznam www* A ve vašich doménách.

## <a name="test-the-application-gateway"></a>Testování brány Application Gateway

1. Do adresního řádku prohlížeče zadejte název domény. Příklad: `http://www.contoso.com`.

    ![Testování webu Contoso v aplikační bráně](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Změňte adresu na jinou ze svých domén. Měli byste vidět něco podobného jako v následujícím příkladu:

    ![Testování webu Fabrikam v aplikační bráně](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili pomocí aplikační brány, odeberte skupinu prostředků. Při odebrání skupiny prostředků odeberete také bránu aplikace a všechny související prostředky.

Odebrání skupiny prostředků:

1. V levé nabídce portálu Azure vyberte **skupiny prostředků**.
2. Na stránce **Skupiny prostředků** vyhledejte **myResourceGroupAG** v seznamu a vyberte ji.
3. Na **stránce Skupina prostředků**vyberte **Odstranit skupinu prostředků**.
4. Zadejte *myResourceGroupAG* pro **typ názvu skupiny zdrojů** a pak vyberte **Odstranit**

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o tom, co můžete dělat s Azure Application Gateway](application-gateway-introduction.md)