---
title: 'Kurz: pravidla směrování na základě cesty URL pomocí portálu – Azure Application Gateway'
description: V tomto kurzu se naučíte vytvářet pravidla směrování na základě cest URL pro službu Application Gateway a sadu škálování virtuálních počítačů pomocí Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 08/13/2020
ms.author: victorh
ms.openlocfilehash: 407bd5679c6afebf26c2e6b768e0f8513ac39123
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397582"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Kurz: vytvoření aplikační brány s pravidly směrování založenými na cestách pomocí Azure Portal

Pomocí Azure Portal můžete nakonfigurovat [pravidla směrování na základě cest URL](./url-route-overview.md) při vytváření [aplikační brány](./overview.md). V tomto kurzu vytvoříte back-endové fondy s použitím virtuálních počítačů. Pak vytvoříte pravidla směrování, která zajistí, že webový provoz dorazí na příslušné servery ve fondech.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření brány Application Gateway
> * Vytváření virtuálních počítačů pro back-end servery
> * Vytváření back-end fondů se servery back-end
> * Vytvoření naslouchacího procesu back-endu
> * Vytvoření pravidla směrování založeného na cestách

![Příklad směrování na základě adresy URL](./media/application-gateway-create-url-route-portal/scenario.png)

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

V tomto příkladu vytvoříte tři virtuální počítače, které se použijí jako servery back-end pro aplikační bránu. Nainstalujete také službu IIS na virtuální počítače a ověříte tak, že služba Application Gateway funguje podle očekávání.

1. V Azure Portal vyberte **vytvořit prostředek**.
2. V seznamu oblíbených vyberte **Windows Server 2016 Datacenter** .
3. Zadejte pro virtuální počítač tyto hodnoty:

    - **Skupina prostředků** , vyberte **vytvořit novou** a potom zadejte *myResourceGroupAG*.
    - **Název virtuálního počítače** : *myVM1*
    - **Oblast** : *(US) východní USA*
    - **Uživatelské jméno** : *azureuser*
    - **Heslo** : *Azure123456!*


4. Vyberte **Další: disky**.
5. Vybrat **Další: sítě**
6. Pro **virtuální síť** vyberte **vytvořit novou** a zadejte tyto hodnoty pro virtuální síť:

   - *myVNet* – tuto hodnotu zadejte jako název virtuální sítě.
   - *10.0.0.0/16* – tuto hodnotu zadejte jako adresní prostor virtuální sítě.
   - *myBackendSubnet* pro první název podsítě
   - *10.0.1.0/24* – pro adresní prostor podsítě.
   - *myAGSubnet* – pro druhý název podsítě.
   - *10.0.0.0/24* – tuto hodnotu zadejte jako adresní prostor podsítě.
7. Vyberte **OK**.

8. Ujistěte se, že v části **síťové rozhraní** je pro podsíť vybraná možnost **myBackendSubnet** a pak vyberte **Další: Správa**.
9. Výběrem možnosti **vypnuto** zakážete diagnostiku spouštění.
10. Klikněte na tlačítko **zkontrolovat + vytvořit** , zkontrolujte nastavení na stránce Souhrn a pak vyberte **vytvořit**.
11. Vytvořte dva další virtuální počítače, *myVM2* a *myVM3* a umístěte je do virtuální sítě *MyVNet* a podsítě *myBackendSubnet* .

### <a name="install-iis"></a>Instalace služby IIS

1. Otevřete interaktivní prostředí a ujistěte se, že je nastavený na **PowerShell**.

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

3. Vytvořte dva další virtuální počítače a nainstalujte IIS pomocí kroků, které jste právě dokončili. Zadejte názvy *myVM2* a *myVM3* pro názvy a pro hodnoty VMName v set-AzVMExtension.

## <a name="create-an-application-gateway"></a>Vytvoření brány Application Gateway

1. V levé nabídce Azure Portal vyberte **vytvořit prostředek** . Zobrazí se **nové** okno.

2. Vyberte **sítě** a v seznamu **Doporučené** vyberte **Application Gateway** .

### <a name="basics-tab"></a>Karta základy

1. Na kartě **základy** zadejte tyto hodnoty pro následující nastavení služby Application Gateway:

   - **Skupina prostředků** : pro skupinu prostředků vyberte **myResourceGroupAG** .
   - **Název aplikační brány** : jako název služby Application Gateway zadejte *myAppGateway* .
   - **Oblast** – výběr **(US) východní USA**.

        ![Vytvořit novou aplikační bránu: Základy](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  V části **Konfigurovat virtuální síť** vyberte **myVNet** pro název virtuální sítě.
3. Jako podsíť vyberte **myAGSubnet** .
3. Přijměte výchozí hodnoty pro ostatní nastavení a potom vyberte **Další: front-endu**.

### <a name="frontends-tab"></a>Karta front-endu

1. Na kartě **front-endu** ověřte, že **typ IP adresy front-end** je nastavený na **veřejné**.

   > [!NOTE]
   > V případě SKU Application Gateway v2 můžete zvolit jenom **veřejnou** konfiguraci IP adresy front-endu. V tuto chvíli není u této SKU verze V2 povolená soukromá konfigurace IP adresy front-endu.

2. Zvolte **vytvořit nový** pro **veřejnou IP adresu** a jako název veřejné IP adresy zadejte *myAGPublicIPAddress* a pak vyberte **OK**. 
3. Vyberte **Další: back-endy**.

### <a name="backends-tab"></a>Karta back-endy

Back-end fond slouží ke směrování požadavků na servery back-end, které obsluhují požadavek. Back-endové fondy se dají skládat ze síťových adaptérů, virtuálních počítačů a virtuálních IP adres, interních IP adres, plně kvalifikovaných názvů domény (FQDN) a back-endu s více klienty, jako je Azure App Service.

1. Na kartě back- **endy** vyberte **+ Přidat back-end fond**.

2. V okně **Přidat fond back-end** , které se otevře, zadejte následující hodnoty a vytvořte prázdný back-end fond:

    - **Název** : jako název back-end fondu zadejte *myBackendPool* .
3. V části **cíle do back-endu** , **cílový typ** vyberte **virtuální počítač** v rozevíracím seznamu.

5. V části **cíl** vyberte síťové rozhraní pro **myVM1**.
6. Vyberte **Add** (Přidat).
7. Opakujte, pokud chcete do cíle přidat back-end fond *imagí* s *myVM2* , a jako cíl se vytvoří fond back-endu *videa* s *myVM3* .
8. Vyberte **Přidat** a uložte konfiguraci back-end fondu a vraťte se na kartu back- **endy** .

4. Na kartě **back-endy** vyberte **Další: Konfigurace**.

### <a name="configuration-tab"></a>Karta konfigurace

Na kartě **Konfigurace** se připojíte k front-endovému a back-endovému fondu, který jste vytvořili pomocí pravidla směrování.

1. Ve sloupci **pravidla směrování** vyberte **Přidat pravidlo** .

2. V okně **Přidat pravidlo směrování** , které se otevře, jako **název pravidla** zadejte *myRoutingRule* .

3. Pravidlo směrování vyžaduje naslouchací proces. Na kartě **naslouchací proces** v okně **Přidat pravidlo směrování** zadejte následující hodnoty pro naslouchací proces:

    - **Název naslouchacího procesu** : jako název naslouchacího procesu zadejte *MyListener* .
    - **Front-end IP adresa** : vyberte **veřejné** a zvolte veřejnou IP adresu, kterou jste vytvořili pro front-end.
    - **Port** : typ *8080*
  
        Přijměte výchozí hodnoty pro ostatní nastavení na kartě **naslouchací proces** a potom vyberte kartu **cílení na back-end** a nakonfigurujte zbývající část pravidla směrování.

4. Na kartě **cílení na server back-end** vyberte **MyBackendPool** pro **cíl back-endu**.

5. Pro **Nastavení http** vyberte **vytvořit novou** a vytvořte nové nastavení http. Nastavením protokolu HTTP se určí chování pravidla směrování. 

6. V okně **Přidat nastavení protokolu HTTP** , které se otevře, zadejte *myHTTPSetting* pro **název nastavení http**. Přijměte výchozí hodnoty pro ostatní nastavení v okně **Přidat nastavení http** a pak vyberte **Přidat** a vraťte se do okna **Přidat pravidlo směrování** .
7. V části **směrování na základě cesty** vyberte **Přidat více cílů a vytvořte pravidlo na základě cesty**.
8. Jako **cestu** zadejte */images/* \* .
9. Jako **název pravidla cesty** zadejte *Image*.
10. V **Nastavení http** vyberte **myHTTPSetting**
11. V případě **cíle pro back-end** vyberte **obrázky**.
12. Vyberte **Přidat** a uložte pravidlo cesty a vraťte se na kartu **Přidat pravidlo směrování** .
13. Opakujte, pokud chcete přidat další pravidlo pro video.
14. Vyberte **Přidat** a přidejte pravidlo směrování a vraťte se na kartu **Konfigurace** .
15. Vyberte **Další: značky** a potom **Další: zkontrolovat + vytvořit**.

> [!NOTE]
> Nemusíte přidávat vlastní */* * pravidlo cesty pro zpracování výchozích případů. To se automaticky zpracuje ve výchozím fondu back-end.

### <a name="review--create-tab"></a>Revize + vytvořit kartu

Zkontrolujte nastavení na kartě **Revize + vytvořit** a pak vyberte **vytvořit** k vytvoření virtuální sítě, veřejné IP adresy a aplikační brány. Vytvoření služby Application Gateway v Azure může trvat několik minut. Před přechodem k další části počkejte na úspěšné dokončení nasazení.


## <a name="test-the-application-gateway"></a>Otestování aplikační brány

1. Vyberte **všechny prostředky** a pak vyberte **myAppGateway**.

    ![Záznam veřejné IP adresy aplikační brány](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. Například http: \/ /52.188.72.175:8080.

    ![Testování základní adresy URL v aplikační bráně](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

   Naslouchací proces na portu 8080 směruje tento požadavek na výchozí back-end fond.

3. Změňte adresu URL na *http:// &lt; IP-address &gt; : 8080/images/test.htm* , nahraďte IP adresu IP &lt; &gt; adresou a měla by se zobrazit něco jako v následujícím příkladu:

    ![Testování adresy URL obrázků v aplikační bráně](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

   Naslouchací proces na portu 8080 směruje tento požadavek do back-endového fondu *imagí* .

4. Změňte adresu URL na *http:// &lt; IP-address &gt; : 8080/video/test.htm* , nahraďte IP adresu IP &lt; &gt; adresou a měla by se zobrazit něco jako v následujícím příkladu:

    ![Testování adresy URL videa v aplikační bráně](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

   Naslouchací proces na portu 8080 směruje tento požadavek do fondu back-endu *videa* .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků a vyberete **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Povolení koncového šifrování TLS v Azure Application Gateway](./ssl-overview.md)