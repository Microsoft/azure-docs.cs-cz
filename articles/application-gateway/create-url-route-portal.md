---
title: 'Kurz: Pravidla směrování založená na cestě URL pomocí portálu – Azure Application Gateway'
description: V tomto kurzu se dozvíte, jak vytvořit pravidla směrování založené na cestě URL pro aplikační bránu a škálovací sadu virtuálních strojů pomocí portálu Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: bc810ac7901d83f03d3f3ac2199561225326d261
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74048135"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Kurz: Vytvoření aplikační brány s pravidly směrování na základě cesty pomocí portálu Azure

Portál Azure můžete použít ke konfiguraci [pravidel směrování založených na cestě k adrese URL](application-gateway-url-route-overview.md) při vytváření aplikační [brány](application-gateway-introduction.md). V tomto kurzu vytvoříte back-endové fondy pomocí virtuálních počítačů. Potom vytvoříte pravidla směrování, která zajistí, že webový provoz dorazí na příslušné servery ve fondech.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření služby Application Gateway
> * Vytvoření virtuálních počítačů pro back-endové servery
> * Vytvoření back-endových fondů s back-endovými servery
> * Vytvoření naslouchací proces back-endu
> * Vytvoření pravidla směrování založeného na cestě

![Příklad směrování na základě adresy URL](./media/application-gateway-create-url-route-portal/scenario.png)

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na webu Azure portal na adrese[https://portal.azure.com](https://portal.azure.com)

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

V tomto příkladu vytvoříte tři virtuální počítače, které se použijí jako back-endové servery pro aplikační bránu. Službu IIS můžete také nainstalovat do virtuálních počítačů, abyste ověřili, že brána aplikace funguje podle očekávání.

1. Na webu Azure Portal vyberte **Vytvořit prostředek**.
2. V seznamu Oblíbené vyberte **Datové centrum Windows Serveru 2016.**
3. Zadejte pro virtuální počítač tyto hodnoty:

    - **Skupina prostředků**, vyberte **Vytvořit nový**a zadejte *příkaz myResourceGroupAG*.
    - **Název virtuálního počítače**: *myVM1*
    - **Region**: *(USA) – východ USA*
    - **Uživatelské jméno**: *azureuser*
    - **Heslo:** *Azure123456!*


4. Vyberte **Další:Disky**.
5. Vybrat **další:Vytváření sítí**
6. V **části Virtuální síť**vyberte Vytvořit **nový** a zadejte pro virtuální síť tyto hodnoty:

   - *myVNet* – tuto hodnotu zadejte jako název virtuální sítě.
   - *10.0.0.0/16* – tuto hodnotu zadejte jako adresní prostor virtuální sítě.
   - *myBackendSubnet* pro první název podsítě
   - *10.0.1.0/24* - pro adresní prostor podsítě.
   - *myGSubnet* - pro druhý název podsítě.
   - *10.0.0.0/24* – tuto hodnotu zadejte jako adresní prostor podsítě.
7. Vyberte **OK**.

8. Ujistěte se, že v části **Síťové rozhraní**je pro podsíť vybrána možnost **myBackendSubnet,** a pak vyberte **možnost Další: Správa**.
9. Chcete-li diagnostiku spouštění zakázat, vyberte **možnost Vypnuto.**
10. Klikněte na **Zkontrolovat + Vytvořit**, zkontrolujte nastavení na stránce souhrnu a pak vyberte **Vytvořit**.
11. Vytvořte další dva virtuální počítače, *myVM2* a *myVM3* a umístěte je do virtuální sítě *MyVNet* a podsítě *myBackendSubnet.*

### <a name="install-iis"></a>Instalace služby IIS

1. Otevřete interaktivní prostředí a ujistěte se, že je nastavena na **PowerShell**.

    ![Instalace vlastního rozšíření](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Spuštěním následujícího příkazu nainstalujte službu IIS na virtuální počítač: 

    ```azurepowershell
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

3. Vytvořte další dva virtuální počítače a nainstalujte službu IIS pomocí kroků, které jste právě dokončili. Zadejte názvy *myVM2* a *myVM3* pro názvy a hodnoty VMName v Set-AzVMExtension.

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

1. V levé nabídce portálu Azure vyberte **Vytvořit prostředek.** Zobrazí se okno **Nový.**

2. Vyberte **Síť** a pak v seznamu **Doporučené** vyberte **Aplikační brána.**

### <a name="basics-tab"></a>Karta Základy

1. Na kartě **Základy** zadejte tyto hodnoty pro následující nastavení brány aplikace:

   - **Skupina prostředků**: Vyberte **myResourceGroupAG** pro skupinu prostředků.
   - **Název aplikační brány**: Zadejte *myAppGateway* pro název aplikační brány.
   - **Oblast** – vyberte **(USA) – východ USA**.

        ![Vytvořit novou aplikační bránu: Základy](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  V části **Konfigurace virtuální sítě**vyberte **myVNet** pro název virtuální sítě.
3. Vyberte **myAGSubnet** pro podsíť.
3. Přijměte výchozí hodnoty pro ostatní nastavení a pak vyberte **Další: Frontendy**.

### <a name="frontends-tab"></a>Karta Front-endy

1. Na kartě **Frontendy** ověřte, zda je **typ ip adresy front-endu** nastaven na **veřejné**.

   > [!NOTE]
   > Pro aplikační bránu v2 SKU můžete zvolit pouze **veřejnou** front-endovou konfiguraci IP. Privátní front-endová konfigurace IP není aktuálně povolena pro tuto skladovou položku v2.

2. Zvolte **Vytvořit novou** pro **veřejnou IP adresu** a zadejte adresu *myAGPublicIPAddress* pro název veřejné IP adresy a pak vyberte **OK**. 
3. Vyberte **další: Back-endy**.

### <a name="backends-tab"></a>Karta Back-endy

Back-endový fond se používá ke směrování požadavků na servery back-endu, které tento požadavek obsluhují. Back-endové fondy se můžou skládat z připojení k síťové karty, škálovacísady virtuálních počítačů, veřejných IP adres, interních IP adres, plně kvalifikovaných názvů domén (Plně kvalifikovaný název domény) a back-endů s více tenanty, jako je Služba Azure App Service.

1. Na kartě **Backends** vyberte **+Přidat fond back-endů**.

2. V okně **Přidat back-endový fond,** které se otevře, zadejte následující hodnoty pro vytvoření prázdného back-endového fondu:

    - **Název**: Zadejte *myBackendPool* pro název back-endového fondu.
3. V části **Back-end Targets**, **Type Target**vyberte v rozevíracím seznamu **virtuální počítač.**

5. V části **Cíl** vyberte síťové rozhraní pro **myVM1**.
6. Vyberte **Přidat**.
7. Opakováním přidáte back-endový fond *obrázků* s *myVM2* jako cílem a fond back-endu *videa* s *myVM3* jako cílem.
8. Vyberte **Přidat,** chcete-li uložit konfiguraci back-endového fondu a vrátit se na kartu **Backends.**

4. Na kartě **Back-endy** vyberte **Další: Konfigurace**.

### <a name="configuration-tab"></a>Karta Konfigurace

Na kartě **Konfigurace** připojíte front-endový a back-endový fond, který jste vytvořili, pomocí pravidla směrování.

1. Ve sloupci **Pravidla směrování** vyberte **Přidat pravidlo.**

2. V okně **Přidat směrovací pravidlo,** které se otevře, zadejte *myRoutingRule* pro **název pravidla**.

3. Pravidlo směrování vyžaduje naslouchací proces. Na kartě **Listener** v okně **Přidat pravidlo směrování** zadejte pro posluchače následující hodnoty:

    - **Název naslouchací proces**: Zadejte *myListener* pro název posluchače.
    - **Front-end IP**: Vyberte **Veřejné,** chcete-li zvolit veřejnou IP adresu, kterou jste pro front-end vytvořili.
    - **Port**: Typ *8080*
  
        Přijměte výchozí hodnoty pro ostatní nastavení na kartě **Naslouchací proces** a pak vyberte kartu **Cíle back-endu** a nakonfigurujte zbytek pravidla směrování.

4. Na kartě **Cíle back-endu** vyberte **myBackendPool** pro **cíl back-endu**.

5. Pro **nastavení HTTP**vyberte **Vytvořit nový** a vytvořte nové nastavení HTTP. Nastavení HTTP určí chování pravidla směrování. 

6. V okně **Přidat nastavení PROTOKOLU HTTP,** které se otevře, zadejte pro **název nastavení HTTP nastavení myHTTPSetting**. *myHTTPSetting* Přijměte výchozí hodnoty pro ostatní nastavení v **okně Přidat nastavení HTTP** a pak vyberte **Přidat** a vraťte se do okna Přidat **pravidlo směrování.**
7. V části **Směrování založené na cestě**vyberte Přidat více cílů a **vytvořte pravidlo založené na cestě**.
8. Do **pole Cesta**zadejte */images/*\*.
9. Do **pole Path rule name**zadejte *Images*.
10. Pro **nastavení HTTP**vyberte **myHTTPSetting**
11. Pro **back-endový cíl**vyberte **Obrázky**.
12. Vyberte **Přidat,** chcete-li uložit pravidlo cesty a vrátit se na kartu **Přidat pravidlo směrování.**
13. Opakováním přidáte další pravidlo pro video.
14. Výběrem **možnosti Přidat** přidáte pravidlo směrování a vraťte se na kartu **Konfigurace.**
15. Vyberte **další: Značky** a pak **Další: Zkontrolujte + vytvořit**.

> [!NOTE]
> Není nutné přidávat vlastní */** cesta pravidlo pro zpracování výchozích případů. To je automaticky zpracována výchozí fond back-end.

### <a name="review--create-tab"></a>Karta Revize + vytvoření

Zkontrolujte nastavení na kartě **Revize + vytvořit** a pak vyberte **Vytvořit,** chcete-li vytvořit virtuální síť, veřejnou IP adresu a bránu aplikace. Vytvoření brány aplikace azure může trvat několik minut. Počkejte, dokud nasazení úspěšně dokončí před přechodem na další část.


## <a name="test-the-application-gateway"></a>Testování brány Application Gateway

1. Vyberte **Všechny prostředky**a pak **vyberte myAppGateway**.

    ![Záznam veřejné IP adresy aplikační brány](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. Například http:\//52.188.72.175:8080.

    ![Testování základní adresy URL v aplikační bráně](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

   Naslouchací proces na portu 8080 směruje tento požadavek do výchozího back-endového fondu.

3. Změňte adresu URL na *http://&lt;ip-adresu&gt;:8080/images/test.htm*, nahrazení &lt;ip adresy&gt; vaší IP adresou a měli byste vidět něco jako v následujícím příkladu:

    ![Testování adresy URL obrázků v aplikační bráně](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

   Naslouchací proces na portu 8080 směruje tento požadavek do back-endového fondu *Images.*

4. Změňte adresu URL na *http://&lt;ip-adresu&gt;:8080/video/test.htm*, nahrazení &lt;ip adresy&gt; vaší IP adresou a měli byste vidět něco jako v následujícím příkladu:

    ![Testování adresy URL videa v aplikační bráně](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

   Naslouchací proces na portu 8080 směruje tento požadavek do fondu back-endu *videa.*


## <a name="next-steps"></a>Další kroky

- [Povolení koncového ssl v bráně aplikace Azure](application-gateway-backend-ssl.md)
