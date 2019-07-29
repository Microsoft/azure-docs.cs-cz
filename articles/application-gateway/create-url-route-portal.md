---
title: Kurz – vytvoření aplikační brány s pravidly směrování na základě cesty URL – Azure Portal
description: V tomto kurzu se naučíte vytvářet pravidla směrování na základě cest URL pro službu Application Gateway a sadu škálování virtuálních počítačů pomocí Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: victorh
ms.openlocfilehash: 63a1faa79374e72eabfbee4ece454728c3b4cc05
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597581"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Kurz: Vytvoření aplikační brány s pravidly směrování založenými na cestách pomocí Azure Portal

Pomocí Azure Portal můžete nakonfigurovat [pravidla směrování na základě cest URL](url-route-overview.md) při vytváření [aplikační brány](overview.md). V tomto kurzu vytvoříte back-endové fondy s použitím virtuálních počítačů. Pak vytvoříte pravidla směrování, která zajistí, že webový provoz dorazí na příslušné servery ve fondech.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření služby Application Gateway
> * Vytváření virtuálních počítačů pro back-end servery
> * Vytváření back-end fondů se servery back-end
> * Vytvoření naslouchacího procesu back-endu
> * Vytvoření pravidla směrování založeného na cestách

![Příklad směrování na základě adresy URL](./media/create-url-route-portal/scenario.png)

Pokud budete chtít, můžete tento kurz dokončit pomocí [Azure CLI](tutorial-url-route-cli.md) nebo [Azure PowerShell](tutorial-url-route-powershell.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

1. V levé nabídce Azure Portal vyberte **vytvořit prostředek** . Zobrazí se **nové** okno.

2. Vyberte **sítě** a v seznamu **Doporučené** vyberte **Application Gateway** .

### <a name="basics-tab"></a>Karta základy

1. Na kartě **základy** zadejte tyto hodnoty pro následující nastavení služby Application Gateway:

   - **Skupina prostředků**: Jako skupinu prostředků vyberte **myResourceGroupAG** . Pokud neexistuje, vyberte **vytvořit novou** a vytvořte ji.
   - **Název aplikační brány**: Jako název služby Application Gateway zadejte *myAppGateway* .

     ![Vytvořit novou aplikační bránu: Základní informace](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Aby mohl Azure komunikovat mezi prostředky, které vytvoříte, potřebuje virtuální síť. Můžete buď vytvořit novou virtuální síť, nebo použít existující. V tomto příkladu vytvoříte novou virtuální síť ve stejnou chvíli, kdy vytvoříte Aplikační bránu. Instance Application Gateway se vytvářejí v oddělených podsítích. V tomto příkladu vytvoříte dvě podsítě: jednu pro aplikační bránu a druhou pro back-end servery.

    V části **Konfigurovat virtuální síť**vyberte **vytvořit novou** a vytvořte novou virtuální síť. V okně **vytvořit virtuální síť** , které se otevře, zadejte následující hodnoty pro vytvoření virtuální sítě a dvě podsítě:

    - **Název**: Jako název virtuální sítě zadejte *myVNet* .

    - **Název podsítě** (Application Gateway podsíť): V mřížce **podsítě** se zobrazí podsíť s názvem *výchozí*. Změňte název této podsítě na *myAGSubnet*.

      Podsíť aplikační brány může obsahovat jenom aplikační brány. Žádné další prostředky nejsou povoleny.

    - **Název podsítě** (podsíť back-end serveru): Ve druhém řádku mřížky **podsítě** zadejte *myBackendSubnet* do sloupce **název podsítě** .

    - **Rozsah adres** (podsíť back-end serveru): V druhém řádku mřížky **podsítě** zadejte rozsah adres, který se nepřekrývá s rozsahem adres *myAGSubnet*. Pokud má například rozsah adres *myAGSubnet* 10.0.0.0/24, zadejte pro rozsah adres *myBackendSubnet* *10.0.1.0/24* .

    Výběrem **OK** zavřete okno **vytvořit virtuální síť** a uložte nastavení virtuální sítě.

     ![Vytvořit novou aplikační bránu: virtuální síť](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Na kartě **základy** přijměte výchozí hodnoty pro ostatní nastavení a potom vyberte **další: Front-endové.**

### <a name="frontends-tab"></a>Karta front-endu

1. Na kartě **front-endu** ověřte, že **typ IP adresy front-end** je nastavený na **veřejné**. <br>Front-end IP adresu můžete nakonfigurovat tak, aby byla veřejná nebo soukromá jako na základě vašeho případu použití. V tomto příkladu zvolíte veřejnou front-end IP adresu.
   > [!NOTE]
   > V případě SKU Application Gateway v2 můžete zvolit jenom **veřejnou** konfiguraci IP adresy front-endu. V tuto chvíli není u této SKU verze V2 povolená soukromá konfigurace IP adresy front-endu.

2. Zvolte **vytvořit nový** pro **veřejnou IP adresu** a jako název veřejné IP adresy zadejte *myAGPublicIPAddress* a pak vyberte **OK**. 

     ![Vytvořit novou aplikační bránu: front-endové](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Vyberte **další: Back-** endy.

### <a name="backends-tab"></a>Karta back-endy

Back-end fond slouží ke směrování požadavků na servery back-end, které obsluhují požadavek. Back-endové fondy můžou být síťové adaptéry, sady škálování virtuálních počítačů, veřejné IP adresy, interní IP adresy, plně kvalifikované názvy domény (FQDN) a back-endy pro více tenantů, jako je Azure App Service. V tomto příkladu vytvoříte prázdný back-end fond s aplikační bránou a potom přidáte cíle back-end do fondu back-end.

1. Na kartě back- **endy** vyberte **+ Přidat back-end fond**.

2. V okně **Přidat fond back-end** , které se otevře, zadejte následující hodnoty a vytvořte prázdný back-end fond:

    - **Název**: Jako název back-end fondu zadejte *appGatewayBackendPool* .
    - **Přidat back-end fond bez cílů**: Vyberte **Ano** , pokud chcete vytvořit fond back-end bez cílů. Po vytvoření aplikační brány přidáte cíle back-endu.

3. V okně **Přidat fond back-endu** vyberte **Přidat** a uložte konfiguraci fondu back-end a vraťte se na kartu back- **endy** .
4. Teď přidejte dva další back-end fondy s názvem *imagesBackendPool*a *videoBackendPool*.

     ![Vytvořit novou aplikační bránu: back-endy](./media/create-url-route-portal/backends.png)

4. Na kartě **back-endy** vyberte **další: Konfigurace**.

### <a name="configuration-tab"></a>Karta Konfigurace

Na kartě **Konfigurace** propojíte front-endové a back-endové fondy, které jste vytvořili pomocí pravidel směrování.

1. Ve sloupci **pravidla směrování** vyberte **Přidat pravidlo** .
2. V okně **Přidat pravidlo směrování** , které se otevře, jako **název pravidla**zadejte *Rule1* .
3. Pravidlo směrování vyžaduje naslouchací proces. Na kartě **naslouchací proces** v okně **Přidat pravidlo směrování** zadejte následující hodnoty pro naslouchací proces:

    - **Název naslouchacího procesu**: Jako název naslouchacího procesu zadejte *DefaultListener* .
    - **IP adresa front-endu**: Vyberte **veřejné** a zvolte veřejnou IP adresu, kterou jste vytvořili pro front-end.

   Přijměte výchozí hodnoty pro ostatní nastavení na kartě **naslouchací proces** a potom vyberte kartu cílení na **back-end** a nakonfigurujte zbývající část pravidla směrování.
4. Na kartě **cílení na server back-end** vyberte **AppGatewayBackendPool** pro **cíl back-endu**.

5. Pro **Nastavení http**vyberte **vytvořit novou** a vytvořte nové nastavení http. Nastavením protokolu HTTP se určí chování pravidla směrování. V okně **Přidat nastavení protokolu HTTP** , které se otevře, zadejte *myHTTPSetting* pro **název nastavení http**. Přijměte výchozí hodnoty pro ostatní nastavení v okně **Přidat nastavení http** a pak vyberte **Přidat** a vraťte se do okna **Přidat pravidlo směrování** . 

6. V okně **Přidat pravidlo směrování** vyberte **Přidat** a uložte pravidlo směrování a vraťte se na kartu **Konfigurace** .



1. Ve sloupci **pravidla směrování** vyberte **Přidat pravidlo** .

2. V okně **Přidat pravidlo směrování** , které se otevře, jako **název pravidla**zadejte *Rule2* .

3. Pravidlo směrování vyžaduje naslouchací proces. Na kartě **naslouchací proces** v okně **Přidat pravidlo směrování** zadejte následující hodnoty pro naslouchací proces:

    - **Název naslouchacího procesu**: Jako název naslouchacího procesu zadejte *myBackendListener* .
    - **IP adresa front-endu**: Vyberte **veřejné** a zvolte veřejnou IP adresu, kterou jste vytvořili pro front-end.
    - **Port**: 8080

   V části **Další nastavení**:
   - **Typ naslouchacího procesu**: Basic

   Přijměte výchozí hodnoty pro ostatní nastavení na kartě **naslouchací proces** a potom vyberte kartu cílení na **back-end** a nakonfigurujte zbývající část pravidla směrování.

4. Na kartě **cílení na server back-end** vyberte **AppGatewayBackendPool** pro **cíl back-endu**.

5. Pro **Nastavení http**vyberte *myHTTPSetting*. Přijměte výchozí hodnoty pro ostatní nastavení v okně **Přidat nastavení http** a pak vyberte **Přidat** a vraťte se do okna **Přidat pravidlo směrování** . 

1. V části **směrování na základě cesty**vyberte **Přidat více cílů a vytvořte pravidlo na základě cesty**.
2. V okně **Přidat pravidlo cesty** zadejte následující hodnoty pro pravidlo cesty:

   - **Cesta**: */images/\**
   - **Název pravidla cesty**: *Obrázky*
   - **Nastavení http**: vyberte *myHTTPSetting*
   - **Cíl back-endu**: *imagesBackendPool*
9. Vyberte **Přidat**.
10. Přidejte další pravidlo cesty s názvem *video*s cestou */video/\**  a *videoBackendPool*.
11. Vyberte **Uložit změny a vraťte se zpět k pravidlům směrování**.

    ![Přidat pravidlo směrování](media/create-url-route-portal/add-routing-rule.png)

12. Vyberte **Přidat**.

7. Vyberte **další: Značky** a potom **další: Zkontrolovat + vytvořit**.

### <a name="review--create-tab"></a>Revize + vytvořit kartu

Zkontrolujte nastavení na kartě **Revize + vytvořit** a pak vyberte **vytvořit** k vytvoření virtuální sítě, veřejné IP adresy a aplikační brány. Vytvoření služby Application Gateway v Azure může trvat několik minut.

Před přechodem k další části počkejte na úspěšné dokončení nasazení.


## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

V tomto příkladu vytvoříte tři virtuální počítače, které se použijí jako servery back-end pro aplikační bránu. Službu IIS na virtuálních počítačích taky nainstalujete, abyste ověřili, že byla služba Application Gateway úspěšně vytvořená.

1. V Azure Portal vyberte **vytvořit prostředek**. Zobrazí se **nové** okno.
2. Vyberte **COMPUTE** a potom v seznamu **oblíbených** vyberte **Windows Server 2016 Datacenter** . Zobrazí se stránka **vytvořit virtuální počítač** .

   Application Gateway může směrovat provoz na libovolný typ virtuálního počítače, který se používá v jeho fondu back-endu. V tomto příkladu použijete Windows Server 2016 Datacenter.
1. Zadejte tyto hodnoty na kartě **základy** pro následující nastavení virtuálního počítače:

    - **Skupina prostředků**: Jako název skupiny prostředků vyberte **myResourceGroupAG** .
    - **Název virtuálního počítače**: Jako název virtuálního počítače zadejte *myVM1* .
    - **Uživatelské jméno**: Jako uživatelské jméno správce zadejte *azureuser* .
    - **Heslo**: Enter *Azure123456!* pro heslo správce.
4. Přijměte ostatní výchozí hodnoty a potom **vyberte Další: Disky**.  
5. Přijměte výchozí hodnoty na kartě **disky** a **potom vyberte Další: Sítě**.
6. Na kartě **sítě** ověřte, že je pro **virtuální síť** vybraný **myVNet** a že **podsíť** je nastavená na **myBackendSubnet**. Přijměte ostatní výchozí hodnoty a potom **vyberte Další: Správa**.

   Application Gateway může komunikovat s instancemi mimo virtuální síť, ve které je, ale je potřeba zajistit připojení k IP adrese.
1. Na kartě **Správa** nastavte diagnostiku **spouštění** na **vypnuto**. Přijměte ostatní výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.
2. Na kartě **Revize + vytvořit** zkontrolujte nastavení, opravte chyby ověřování a potom vyberte **vytvořit**.
3. Než budete pokračovat, počkejte na dokončení vytváření virtuálního počítače.

### <a name="install-iis"></a>Instalace služby IIS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Otevřete interaktivní prostředí a ujistěte se, že je nastavený na **PowerShell**.

    ![Instalace vlastního rozšíření](./media/create-url-route-portal/application-gateway-extension.png)

2. Spuštěním následujícího příkazu nainstalujte službu IIS na virtuální počítač: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName myVM1 `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Vytvořte dva další virtuální počítače a nainstalujte IIS pomocí kroků, které jste právě dokončili. Pro názvy virtuálních počítačů a pro hodnoty **VMName** v set-AzVMExtension použijte *myVM2* a *myVM3* .

## <a name="add-backend-servers-to-backend-pools"></a>Přidání back-end serverů do back-endovéch fondů

1. Vyberte **všechny prostředky**a pak vyberte **myAppGateway**.

2. V nabídce vlevo vyberte **back-endové fondy** .

3. Vyberte **appGatewayBackendPool**.

4. V části **cíle**vyberte v rozevíracím seznamu možnost **virtuální počítač** .

5. V části **virtuální počítač** a **Síťová rozhraní**vyberte virtuální počítač **myVM1** a toto přidružené síťové rozhraní v rozevíracích seznamech.

    ![Přidání back-endových serverů](./media/create-url-route-portal/backend-pool.png)

6. Vyberte **Uložit**.
7. Opakujte pro přidání *myVM2* a rozhraní do *ImagesBackendPool*a pak *myVM3* a rozhraní *videoBackendPool*.

Než budete pokračovat k dalšímu kroku, počkejte na dokončení nasazení.

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

1. Vyberte **všechny prostředky**a pak vyberte **myAGPublicIPAddress**.

    ![Záznam veřejné IP adresy aplikační brány](./media/create-url-route-portal/application-gateway-record-ag-address.png)

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. Příklad: http://40.121.222.19.

    ![Otestování základní adresy URL v aplikační bráně](./media/create-url-route-portal/application-gateway-iistest.png)

3. Změňte adresu URL na http://&lt;IP-address&gt;: 8080/images/test.htm&gt; , nahraďte &lt;IP adresu IP adresou a měli byste vidět něco jako v následujícím příkladu:

    ![Testování adresy URL obrázků v aplikační bráně](./media/create-url-route-portal/application-gateway-iistest-images.png)

4. Změňte adresu URL na http://&lt;IP-address&gt;: 8080/video/test.htm&gt; , nahraďte &lt;IP adresu IP adresou a měli byste vidět něco jako v následujícím příkladu:

    ![Testování adresy URL videa v aplikační bráně](./media/create-url-route-portal/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili v rámci služby Application Gateway, odeberte skupinu prostředků. Odebráním skupiny prostředků odeberete také aplikační bránu a všechny související prostředky. 

Odebrání skupiny prostředků:

1. V levé nabídce Azure Portal vyberte **skupiny prostředků**.
2. Na stránce **skupiny prostředků** vyhledejte v seznamu **myResourceGroupAG** a pak ho vyberte.
3. Na **stránce skupina prostředků**vyberte **Odstranit skupinu prostředků**.
4. Jako **název skupiny prostředků** zadejte *myResourceGroupAG* a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o tom, co můžete dělat s Azure Application Gateway](application-gateway-introduction.md)
