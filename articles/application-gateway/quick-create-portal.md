---
title: 'Úvodní příručka: Přímý webový provoz pomocí portálu'
titleSuffix: Azure Application Gateway
description: Zjistěte, jak pomocí portálu Azure vytvořit aplikační bránu Azure, která směruje webový provoz na virtuální počítače v back-endovém fondu.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/09/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 17adc800bd5a2ae53e27350c7e0d588eaeee4a8f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241399"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Rychlý start: Směrování webového provozu pomocí služby Azure Application Gateway – Azure Portal

V tomto rychlém startu použijete portál Azure k vytvoření aplikační brány. Pak jej otestujete, abyste se ujistili, že funguje správně. 

Aplikační brána směruje webový provoz aplikací na konkrétní prostředky v back-endovém fondu. Naslouchací procesy přiřazujete k portům, vytváříte pravidla a přidáváte prostředky do back-endového fondu. Z důvodu jednoduchosti tento článek používá jednoduché nastavení s veřejnou front-endovou IP adresou, základní naslouchací proces pro hostování jedné sítě na aplikační bráně, základní pravidlo směrování požadavků a dva virtuální počítače v back-endovém fondu.

Tento rychlý start můžete také dokončit pomocí [Azure PowerShellu](quick-create-powershell.md) nebo [Azure CLI](quick-create-cli.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]



## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Bránu aplikace vytvoříte pomocí karet na stránce **Vytvořit bránu aplikace.**

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**. Zobrazí se okno **Nový.**

2. Vyberte **Síť** a pak v seznamu **Doporučené** vyberte **Aplikační brána.**

### <a name="basics-tab"></a>Karta Základy

1. Na kartě **Základy** zadejte tyto hodnoty pro následující nastavení brány aplikace:

   - **Skupina prostředků**: Vyberte **myResourceGroupAG** pro skupinu prostředků. Pokud neexistuje, vyberte **Vytvořit nový** a vytvořte ho.
   - **Název aplikační brány**: Zadejte *myAppGateway* pro název aplikační brány.

     ![Vytvořit novou aplikační bránu: Základy](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2. Pro Azure komunikovat mezi prostředky, které vytvoříte, potřebuje virtuální síť. Můžete buď vytvořit novou virtuální síť, nebo použít existující. V tomto příkladu vytvoříte novou virtuální síť současně s vytvořením aplikační brány. Instance aplikační brány se vytvářejí v samostatných podsítích. V tomto příkladu vytvoříte dvě podsítě: jednu pro aplikační bránu a druhou pro servery back-endu.

    V části **Konfigurace virtuální sítě**vytvořte novou virtuální síť výběrem **možnosti Vytvořit novou**. V okně **Vytvořit virtuální síť,** které se otevře, zadejte následující hodnoty pro vytvoření virtuální sítě a dvou podsítí:

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
   > Pro aplikační bránu v2 SKU musí existovat konfigurace IP **public** front-end. Stále můžete mít veřejnou i privátní front-endovou konfiguraci IP, ale konfigurace ip adres pouze private (pouze režim ILB) není aktuálně povolena pro skladovou položku v2. 

2. Zvolte **Vytvořit novou** pro **veřejnou IP adresu** a zadejte adresu *myAGPublicIPAddress* pro název veřejné IP adresy a pak vyberte **OK**. 

     ![Vytvořit novou aplikační bránu: front-endy](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Vyberte **další: Back-endy**.

### <a name="backends-tab"></a>Karta Back-endy

Back-endový fond se používá ke směrování požadavků na servery back-endu, které tento požadavek obsluhují. Back-endové fondy se můžou skládat z připojení k síťové karty, škálovacísady virtuálních počítačů, veřejných IP adres, interních IP adres, plně kvalifikovaných názvů domén (Plně kvalifikovaný název domény) a back-endů s více tenanty, jako je Služba Azure App Service. V tomto příkladu vytvoříte prázdný back-endový fond s vaší aplikační bránou a pak přidáte back-endové cíle do back-endového fondu.

1. Na kartě **Backends** vyberte **+Přidat fond back-endů**.

2. V okně **Přidat back-endový fond,** které se otevře, zadejte následující hodnoty pro vytvoření prázdného back-endového fondu:

    - **Název**: Zadejte *myBackendPool* pro název back-endového fondu.
    - **Přidat back-endový fond bez cílů**: Výběrem **možnosti Ano** vytvořte back-endový fond bez cílů. Po vytvoření brány aplikace přidáte back-endové cíle.

3. V okně **Přidat fond back-endu** vyberte **Přidat,** chcete-li uložit konfiguraci back-endového fondu a vrátit se na kartu **Back-endy.**

     ![Vytvořit novou aplikační bránu: back-endy](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. Na kartě **Back-endy** vyberte **Další: Konfigurace**.

### <a name="configuration-tab"></a>Karta Konfigurace

Na kartě **Konfigurace** připojíte front-endový a back-endový fond, který jste vytvořili, pomocí pravidla směrování.

1. Ve sloupci **Pravidla směrování** vyberte **Přidat pravidlo.**

2. V okně **Přidat směrovací pravidlo,** které se otevře, zadejte *myRoutingRule* pro **název pravidla**.

3. Pravidlo směrování vyžaduje naslouchací proces. Na kartě **Listener** v okně **Přidat pravidlo směrování** zadejte pro posluchače následující hodnoty:

    - **Název naslouchací proces**: Zadejte *myListener* pro název posluchače.
    - **Front-end IP**: Vyberte **Veřejné,** chcete-li zvolit veřejnou IP adresu, kterou jste pro front-end vytvořili.
  
      Přijměte výchozí hodnoty pro ostatní nastavení na kartě **Naslouchací proces** a pak vyberte kartu **Cíle back-endu** a nakonfigurujte zbytek pravidla směrování.

   ![Vytvořit novou aplikační bránu: naslouchací proces](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-listener.png)

4. Na kartě **Cíle back-endu** vyberte **myBackendPool** pro **cíl back-endu**.

5. Pro **nastavení HTTP**vyberte **Vytvořit nový** a vytvořte nové nastavení HTTP. Nastavení HTTP určí chování pravidla směrování. V okně **Přidat nastavení PROTOKOLU HTTP,** které se otevře, zadejte *myHTTPSetting* pro **název nastavení HTTP** a *80* pro **port Back-end**. Přijměte výchozí hodnoty pro ostatní nastavení v **okně Přidat nastavení HTTP** a pak vyberte **Přidat** a vraťte se do okna Přidat **pravidlo směrování.** 

     ![Vytvořit novou aplikační bránu: Nastavení HTTP](./media/application-gateway-create-gateway-portal/application-gateway-create-httpsetting.png)

6. V okně **Přidat pravidlo směrování** vyberte **Přidat,** chcete-li uložit pravidlo směrování a vrátit se na kartu **Konfigurace.**

     ![Vytvořit novou aplikační bránu: pravidlo směrování](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Vyberte **další: Značky** a pak **Další: Zkontrolujte + vytvořit**.

### <a name="review--create-tab"></a>Karta Revize + vytvoření

Zkontrolujte nastavení na kartě **Revize + vytvořit** a pak vyberte **Vytvořit,** chcete-li vytvořit virtuální síť, veřejnou IP adresu a bránu aplikace. Vytvoření brány aplikace azure může trvat několik minut. Počkejte, dokud nasazení úspěšně dokončí před přechodem na další část.

## <a name="add-backend-targets"></a>Přidání back-endových cílů

V tomto příkladu budete používat virtuální počítače jako cílový back-end. Můžete buď použít existující virtuální počítače, nebo vytvořit nové. Vytvoříte dva virtuální počítače jako back-endové servery pro aplikační bránu.

Chcete-li to provést, budete:

1. Vytvořte dva nové virtuální servery, *myVM* a *myVM2*, které se použijí jako back-endové servery.
2. Nainstalujte službu IIS do virtuálních počítačů a ověřte, zda byla aplikační brána úspěšně vytvořena.
3. Přidejte back-endové servery do back-endového fondu.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**. Zobrazí se okno **Nový.**
2. V seznamu **Oblíbené** vyberte **Datové centrum Windows Serveru 2016.** Zobrazí se stránka **Vytvořit virtuální počítač.**<br>Aplikační brána může směrovat provoz na libovolný typ virtuálního počítače používaného v back-endovém fondu. V tomto příkladu použijete datové centrum Windows Server 2016.
3. Na kartu **Základy** zadejte následující nastavení virtuálního počítače:

    - **Skupina prostředků**: Vyberte **myResourceGroupAG** pro název skupiny prostředků.
    - **Název virtuálního počítače**: Zadejte *myVM* pro název virtuálního počítače.
    - **Oblast**: Vyberte stejnou oblast, ve které jste vytvořili aplikační bránu.
    - **Uživatelské jméno**: Zadejte *azureuser* pro uživatelské jméno správce.
    - **Heslo**: Zadejte heslo.
4. Přijměte další výchozí hodnoty a pak vyberte **Další: Disky**.  
5. Přijměte výchozí nastavení karty **Disky** a pak vyberte **Další: Síť**.
6. Na kartě **Síť** ověřte, zda je pro **virtuální síť** vybraná **síť myVNet** a **podsíť** nastavena na **myBackendSubnet**. Přijměte ostatní výchozí hodnoty a pak vyberte **Další: Správa**.<br>Aplikační brána může komunikovat s instancemi mimo virtuální síť, ve které se nachází, ale musíte zajistit připojení IP.
7. Na kartě **Správa** nastavte **diagnostiku spouštění** na **Vypnuto**. Přijměte ostatní výchozí hodnoty a pak vyberte **Zkontrolovat + vytvořit**.
8. Na kartě **Revize + vytvoření** zkontrolujte nastavení, opravte všechny chyby ověření a pak vyberte **Vytvořit**.
9. Před pokračováním počkejte na dokončení vytvoření virtuálního počítače.

### <a name="install-iis-for-testing"></a>Instalace služby IIS pro testování

V tomto příkladu nainstalujete službu IIS na virtuální počítače pouze k ověření Azure úspěšně vytvořil aplikační bránu.

1. Otevřete Azure PowerShell. Z horního navigačního panelu portálu Azure vyberte **Cloud Shell** a v rozevíracím seznamu vyberte **PowerShell.** 

    ![Instalace vlastního rozšíření](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Chcete-li nainstalovat službu IIS do virtuálního počítače, spusťte následující příkaz. V případě potřeby změňte parametr *Umístění:* 

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

1. V nabídce Portál Azure vyberte **Všechny prostředky** nebo vyhledejte a vyberte *Všechny prostředky*. Pak vyberte **myAppGateway**.

2. V levé nabídce vyberte **back-endové fondy.**

3. Vyberte **myBackendPool**.

4. V části **Back-end ové cíle** **vyberte typ cíl**v rozevíracím seznamu virtuální **počítač.**

5. V části **Target**vyberte virtuální počítače **myVM** a **myVM2** a jejich přidružená síťová rozhraní z rozevíracích seznamů.


   > [!div class="mx-imgBorder"]
   > ![Přidání back-endových serverů](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Vyberte **Uložit**.

7. Před dokončením nasazení počkejte na dokončení nasazení, než přebudete pokračovat k dalšímu kroku.

## <a name="test-the-application-gateway"></a>Testování brány Application Gateway

I když služba IIS není nutné k vytvoření aplikační brány, nainstalovali jste ji v tomto rychlém startu k ověření, zda Azure úspěšně vytvořil aplikační bránu. Pomocí služby IIS otestujte bránu aplikace:

1. Nastránce **Přehled** najděte veřejnou IP adresu aplikační brány. ![Zaznamenejte](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) veřejnou IP adresu aplikační brány Nebo můžete do vyhledávacího pole vybrat **všechny prostředky**, zadat *adresu myAGPublicIPAddress* a poté ji vybrat ve výsledcích hledání. Azure zobrazí veřejnou IP adresu na stránce **Přehled.**
2. Zkopírujte veřejnou IP adresu a vložte ji do adresního řádku prohlížeče a projděte tuto IP adresu.
3. Zkontrolujte odpověď. Platná odpověď ověří, zda byla aplikační brána úspěšně vytvořena a může se úspěšně připojit k back-endu.

   ![Otestování aplikační brány](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Aktualizujte prohlížeč vícekrát a měli byste vidět připojení k myVM a myVM2.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili pomocí aplikační brány, odstraňte skupinu prostředků. Když odstraníte skupinu prostředků, odeberete také bránu aplikace a všechny související prostředky.

Odstranění skupiny prostředků:

1. V nabídce Portál Azure vyberte **skupiny prostředků** nebo vyhledejte a vyberte *skupiny prostředků*.
2. Na stránce **Skupiny prostředků** vyhledejte **myResourceGroupAG** v seznamu a vyberte ji.
3. Na **stránce Skupina prostředků**vyberte **Odstranit skupinu prostředků**.
4. Zadejte *myResourceGroupAG* pro **typ názvu skupiny zdrojů** a pak vyberte **Odstranit**

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa webového provozu pomocí aplikační brány a Azure CLI](./tutorial-manage-web-traffic-cli.md)
