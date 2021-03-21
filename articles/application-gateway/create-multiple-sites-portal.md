---
title: 'Kurz: hostitelem několika webů pomocí Azure Portal'
titleSuffix: Azure Application Gateway
description: V tomto kurzu se naučíte, jak vytvořit Aplikační bránu, která hostuje několik webů pomocí Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: victorh
ms.openlocfilehash: cfbd5301bc2b24c4d5614e5f88c6ae18d4affc66
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721626"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Kurz: vytvoření a konfigurace aplikační brány pro hostování více webů pomocí Azure Portal

Můžete použít Azure Portal ke [konfiguraci hostování více](multiple-site-overview.md) webů při vytváření [aplikační brány](overview.md). V tomto kurzu nadefinujete fondy adres back-endu pomocí virtuálních počítačů. Pak můžete nakonfigurovat naslouchací procesy a pravidla na základě dvou domén, aby se zajistilo, že webový provoz přijde na příslušné servery ve fondech. V tomto kurzu se používají příklady *www.contoso.com* a *www.fabrikam.com*.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření brány Application Gateway
> * Vytváření virtuálních počítačů pro back-end servery
> * Vytváření back-end fondů se servery back-end
> * Vytvořit back-endové naslouchací procesy
> * Vytvořit pravidla směrování
> * Upravit soubor hostitelů pro překlad názvů

:::image type="content" source="./media/create-multiple-sites-portal/scenario.png" alt-text="Application Gateway více lokalit":::

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Vytvoření brány Application Gateway

1. V levé nabídce Azure Portal vyberte **vytvořit prostředek** . Zobrazí se **nové** okno.

2. Vyberte **sítě** a v seznamu **Doporučené** vyberte **Application Gateway** .

### <a name="basics-tab"></a>Karta základy

1. Na kartě **základy** zadejte tyto hodnoty pro následující nastavení služby Application Gateway:

   - **Skupina prostředků**: pro skupinu prostředků vyberte **myResourceGroupAG** . Pokud neexistuje, vyberte **vytvořit novou** a vytvořte ji.
   - **Název aplikační brány**: jako název služby Application Gateway zadejte *myAppGateway* .

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png" alt-text="Vytvořit Application Gateway":::

2.  Aby mohl Azure komunikovat mezi prostředky, které vytvoříte, potřebuje virtuální síť. Můžete buď vytvořit novou virtuální síť, nebo použít existující. V tomto příkladu vytvoříte novou virtuální síť ve stejnou chvíli, kdy vytvoříte Aplikační bránu. Instance Application Gateway se vytvářejí v oddělených podsítích. V tomto příkladu vytvoříte dvě podsítě: jednu pro aplikační bránu a druhou pro back-end servery.

    V části **Konfigurovat virtuální síť** vyberte **vytvořit novou** a vytvořte novou virtuální síť. V okně **vytvořit virtuální síť** , které se otevře, zadejte následující hodnoty pro vytvoření virtuální sítě a dvě podsítě:

    - **Název**: jako název virtuální sítě zadejte *myVNet* .

    - **Název podsítě** (Application Gateway podsíť): v mřížce **podsítě** se zobrazí podsíť s názvem *výchozí*. Změňte název této podsítě na *myAGSubnet*.<br>Podsíť aplikační brány může obsahovat jenom aplikační brány. Žádné další prostředky nejsou povoleny.

    - **Název podsítě** (podsíť back-end serveru): v druhém řádku mřížky **podsítě** zadejte *myBackendSubnet* do sloupce **název podsítě** .

    - **Rozsah adres** (podsíť back-end serveru): ve druhém řádku mřížky **podsítě** zadejte rozsah adres, který se nepřekrývá s rozsahem adres *myAGSubnet*. Pokud má například rozsah adres *myAGSubnet* 10.0.0.0/24, zadejte pro rozsah adres *myBackendSubnet* *10.0.1.0/24* .

    Výběrem **OK** zavřete okno **vytvořit virtuální síť** a uložte nastavení virtuální sítě.

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png" alt-text="Vytvořit virtuální síť":::
    
3. Na kartě **základy** přijměte výchozí hodnoty pro ostatní nastavení a potom vyberte **Další: front-endu**.

### <a name="frontends-tab"></a>Karta front-endu

1. Na kartě **front-endu** ověřte, že **typ IP adresy front-end** je nastavený na **veřejné**. <br>Front-end IP adresu můžete nakonfigurovat tak, aby byla veřejná nebo soukromá jako na základě vašeho případu použití. V tomto příkladu zvolíte veřejnou front-end IP adresu.
   > [!NOTE]
   > V případě SKU Application Gateway v2 můžete zvolit jenom **veřejnou** konfiguraci IP adresy front-endu. V tuto chvíli není u této SKU verze V2 povolená soukromá konfigurace IP adresy front-endu.

2. Vyberte možnost **Přidat nový** pro **veřejnou IP adresu** a jako název veřejné IP adresy zadejte *myAGPublicIPAddress* a pak vyberte **OK**. 

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png" alt-text="Vytvoření další virtuální sítě":::

3. Vyberte **Další: back-endy**.

### <a name="backends-tab"></a>Karta back-endy

Back-end fond slouží ke směrování požadavků na servery back-end, které obsluhují požadavek. Back-endové fondy můžou být síťové adaptéry, sady škálování virtuálních počítačů, veřejné IP adresy, interní IP adresy, plně kvalifikované názvy domény (FQDN) a back-endy pro více tenantů, jako je Azure App Service. V tomto příkladu vytvoříte prázdný back-end fond s aplikační bránou a potom přidáte cíle back-end do fondu back-end.

1. Na kartě back- **endy** vyberte **Přidat back-end fond**.

2. V okně **Přidat fond back-end** , které se otevře, zadejte následující hodnoty a vytvořte prázdný back-end fond:

    - **Název**: jako název back-end fondu zadejte *contosoPool* .
    - **Přidat back-end fond bez cílů**: vyberte **Ano** , pokud chcete vytvořit back-end fond bez cílů. Po vytvoření aplikační brány přidáte cíle back-endu.

3. V okně **Přidat fond back-endu** vyberte **Přidat** a uložte konfiguraci fondu back-end a vraťte se na kartu back- **endy** .
4. Teď přidejte další back-end fond s názvem *fabrikamPool* stejným způsobem jako jste přidali předchozí fond.
1. Vyberte **Přidat**.

    :::image type="content" source="./media/create-multiple-sites-portal/backend-pools.png" alt-text="Vytvořit back-endy":::

4. Na kartě **back-endy** vyberte **Další: Konfigurace**.

### <a name="configuration-tab"></a>Karta konfigurace

Na kartě **Konfigurace** propojíte front-endové a back-endové fondy, které jste vytvořili pomocí pravidla směrování.

1. Ve sloupci **pravidla směrování** vyberte **Přidat pravidlo směrování** .

2. V okně **Přidat pravidlo směrování** , které se otevře, jako **název pravidla** zadejte *contosoRule* .

3. Pravidlo směrování vyžaduje naslouchací proces. Na kartě **naslouchací proces** v okně **Přidat pravidlo směrování** zadejte následující hodnoty pro naslouchací proces:

    - **Název pravidla**: *contosoRule*.
    - **Název naslouchacího procesu**: *contosoListener*.
    - **Front-end IP adresa**: vyberte **veřejné** a zvolte veřejnou IP adresu, kterou jste vytvořili pro front-end.

   V části **Další nastavení**:
   - **Typ naslouchacího procesu**: více lokalit
   - **Název hostitele**: **www.contoso.com**

   Přijměte výchozí hodnoty pro ostatní nastavení na kartě **naslouchací proces** a potom vyberte kartu **cílení na back-end** a nakonfigurujte zbývající část pravidla směrování.

   :::image type="content" source="./media/create-multiple-sites-portal/routing-rule.png" alt-text="Vytvořit pravidlo směrování":::

4. Na kartě **cílení na server back-end** vyberte **ContosoPool** pro **cíl back-endu**.

5. Pro **Nastavení http** vyberte **Přidat nový** a vytvořte nové nastavení http. Nastavením protokolu HTTP se určí chování pravidla směrování. V okně **Přidat nastavení protokolu HTTP** , které se otevře, zadejte *contosoHTTPSetting* pro **název nastavení http**. Přijměte výchozí hodnoty pro ostatní nastavení v okně **Přidat nastavení http** a pak vyberte **Přidat** a vraťte se do okna **Přidat pravidlo směrování** . 

6. V okně **Přidat pravidlo směrování** vyberte **Přidat** a uložte pravidlo směrování a vraťte se na kartu **Konfigurace** .
7. Vyberte **Přidat pravidlo směrování** a přidejte podobné pravidlo, naslouchací proces, cíl back-endu a nastavení HTTP pro Fabrikam.

     :::image type="content" source="./media/create-multiple-sites-portal/fabrikam-rule.png" alt-text="Pravidlo společnosti Fabrikam":::

7. Vyberte **Další: značky** a potom **Další: zkontrolovat + vytvořit**.

### <a name="review--create-tab"></a>Revize + vytvořit kartu

Zkontrolujte nastavení na kartě **Revize + vytvořit** a pak vyberte **vytvořit** k vytvoření virtuální sítě, veřejné IP adresy a aplikační brány. Vytvoření služby Application Gateway v Azure může trvat několik minut.

Před přechodem k další části počkejte na úspěšné dokončení nasazení.

## <a name="add-backend-targets"></a>Přidat cíle back-endu

V tomto příkladu budete jako cílový back-end používat virtuální počítače. Můžete buď použít stávající virtuální počítače, nebo vytvořit nové. Vytvoříte dva virtuální počítače, které Azure používá jako servery back-end pro službu Application Gateway.

Pokud chcete přidat cíle back-endu, postupujte takto:

1. Vytvořte dva nové virtuální počítače, *contosoVM* a *fabrikamVM*, které se budou používat jako servery back-end.
2. Nainstalujte službu IIS na virtuální počítače, abyste ověřili, že se služba Application Gateway úspěšně vytvořila.
3. Přidejte servery back-end do back-endu fondů.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. V Azure Portal vyberte **vytvořit prostředek**. Zobrazí se **nové** okno.
2. V seznamu **oblíbených** vyberte **Windows Server 2016 Datacenter** . Zobrazí se stránka **vytvořit virtuální počítač** .<br>Application Gateway může směrovat provoz na libovolný typ virtuálního počítače, který se používá v jeho fondu back-endu. V tomto příkladu použijete Windows Server 2016 Datacenter.
3. Zadejte tyto hodnoty na kartě **základy** pro následující nastavení virtuálního počítače:

    - **Předplatné**: Vyberte předplatné.
    - **Skupina prostředků**: pro název skupiny prostředků vyberte **myResourceGroupAG** .
    - **Název virtuálního počítače**: jako název virtuálního počítače zadejte *contosoVM* .
    - **Oblast**: Vyberte stejnou oblast, kterou jste použili dříve.
    - **Uživatelské jméno**: zadejte název uživatelského jména správce.
    - **Heslo**: zadejte heslo pro správce.
1. Přijměte ostatní výchozí hodnoty a potom vyberte **Další: disky**.  
2. Přijměte výchozí hodnoty na kartě **disky** a potom vyberte **Další: sítě**.
3. Na kartě **sítě** ověřte, že je pro **virtuální síť** vybraný **myVNet** a že **podsíť** je nastavená na **myBackendSubnet**. Přijměte ostatní výchozí hodnoty a potom vyberte **Další: Správa**.<br>Application Gateway může komunikovat s instancemi mimo virtuální síť, ve které je, ale je potřeba zajistit připojení k IP adrese.
4. Na kartě **Správa** nastavte **diagnostiku spouštění** na **zakázáno**. Přijměte ostatní výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.
5. Na kartě **Revize + vytvořit** zkontrolujte nastavení, opravte chyby ověřování a potom vyberte **vytvořit**.
6. Než budete pokračovat, počkejte na dokončení vytváření virtuálního počítače.

### <a name="install-iis-for-testing"></a>Nainstalovat službu IIS pro testování

V tomto příkladu nainstalujete službu IIS na virtuální počítače jenom k úspěšnému ověření, že se brána Application Gateway úspěšně vytvořila.

1. Otevřete [Azure PowerShell](../cloud-shell/quickstart-powershell.md). Provedete to tak, že v horním navigačním panelu Azure Portal vyberete **Cloud Shell** a v rozevíracím seznamu vyberete **PowerShell** . 

    ![Instalace vlastního rozšíření](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Spuštěním následujícího příkazu nainstalujte IIS na virtuálním počítači a nahraďte oblast skupiny prostředků pro <umístění \> : 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location <location>
    ```

3. Vytvořte druhý virtuální počítač a nainstalujte službu IIS pomocí dříve dokončených kroků. Pro název virtuálního počítače a pro nastavení **VMName** rutiny **set-AzVMExtension** použijte *fabrikamVM* .

### <a name="add-backend-servers-to-backend-pools"></a>Přidání back-end serverů do back-endovéch fondů

1. Vyberte **všechny prostředky** a pak vyberte **myAppGateway**.

2. V nabídce vlevo vyberte **back-endové fondy** .

3. Vyberte **contosoPool**.

4. V části **cílový typ** vyberte z rozevíracího seznamu možnost **virtuální počítač** .

5. V části **cíl** vyberte v rozevíracím seznamu síťové rozhraní virtuálního počítače **contosoVM** .

    ![Přidání back-endových serverů](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. Vyberte **Uložit**.
7. Opakujte pro přidání *fabrikamVM* a rozhraní do *fabrikamPool*.

Než budete pokračovat k dalšímu kroku, počkejte na dokončení nasazení.

## <a name="edit-your-hosts-file-for-name-resolution"></a>Úprava souboru hostitelů pro překlad názvů

Po vytvoření služby Application Gateway s veřejnou IP adresou můžete získat IP adresu a použít ji k úpravám souboru hostitelů pro řešení `www.contoso.com` a `www.fabrikam.com` . V produkčním prostředí můžete `CNAME` pro překlad názvů vytvořit v DNS.

1. Klikněte na **všechny prostředky** a pak klikněte na **myAGPublicIPAddress**.

    ![Zaznamenat adresu DNS brány Application Gateway](./media/create-multiple-sites-portal/public-ip.png)

2. Zkopírujte IP adresu a použijte ji jako hodnotu pro nové položky v `hosts` souboru.
1. Na svém místním počítači otevřete příkazový řádek pro správu a přejděte na `c:\Windows\System32\drivers\etc` .
1. Otevřete `hosts` soubor a přidejte následující položky, kde `x.x.x.x` je veřejná IP adresa služby Application Gateway:
   ```dos
   # Copyright (c) 1993-2009 Microsoft Corp.
   #
   # This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
   #
   # This file contains the mappings of IP addresses to host names. Each
   # entry should be kept on an individual line. The IP address should
   # be placed in the first column followed by the corresponding host name.
   # The IP address and the host name should be separated by at least one
   # space.
   #
   # Additionally, comments (such as these) may be inserted on individual
   # lines or following the machine name denoted by a '#' symbol.
   #
   # For example:
   #
   #      102.54.94.97     rhino.acme.com          # source server
   #       38.25.63.10     x.acme.com              # x client host
   
   # localhost name resolution is handled within DNS itself.
   #    127.0.0.1       localhost
   #    ::1             localhost
   x.x.x.x www.contoso.com
   x.x.x.x www.fabrikam.com

   ```
1. Soubor uložte.
1. Spusťte následující příkazy, které načtou a zobrazí změny v souboru hosts:
   ```dos
    ipconfig/registerdns
    ipconfig/displaydns
   ```
   
## <a name="test-the-application-gateway"></a>Otestování aplikační brány

1. Do adresního řádku prohlížeče zadejte název domény. Například, `http://www.contoso.com`.

    ![Testování webu Contoso v aplikační bráně](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Změňte adresu na jinou doménu a měla by se zobrazit něco jako v následujícím příkladu:

    ![Testování webu Fabrikam v aplikační bráně](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili v rámci služby Application Gateway, odeberte skupinu prostředků. Když odeberete skupinu prostředků, odeberete také aplikační bránu a všechny související prostředky.

Odebrání skupiny prostředků:

1. V levé nabídce Azure Portal vyberte **skupiny prostředků**.
2. Na stránce **skupiny prostředků** vyhledejte v seznamu **myResourceGroupAG** a pak ho vyberte.
3. Na **stránce skupina prostředků** vyberte **Odstranit skupinu prostředků**.
4. Jako **název skupiny prostředků** zadejte *myResourceGroupAG* a pak vyberte **Odstranit**.

Postup obnovení souboru hostitelů:
1. Odstraňte `www.contoso.com` řádky a `www.fabrikam.com` ze souboru Hosts a spusťte `ipconfig/registerdns` příkaz a `ipconfig/flushdns` z příkazového řádku.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o tom, co můžete dělat s Azure Application Gateway](./overview.md)