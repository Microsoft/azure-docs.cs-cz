---
title: 'Kurz: Konfigurace ukončení protokolu SSL na portálu – Aplikační brána Azure'
description: V tomto kurzu se dozvíte, jak nakonfigurovat aplikační bránu a přidat certifikát pro ukončení SSL pomocí portálu Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: b4278fc6d44f32921713681cb094b659901cc87c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74012310"
---
# <a name="tutorial-configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Kurz: Konfigurace aplikační brány s ukončením SSL pomocí portálu Azure

Portál Azure můžete použít ke konfiguraci [aplikační brány](overview.md) s certifikátem pro ukončení SSL, který používá virtuální počítače pro back-endové servery.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)
> * Vytvořit aplikační bránu s certifikátem
> * Vytvoření virtuálních počítačů používaných jako back-endové servery
> * Testování brány Application Gateway

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na webu Azure portal na adrese[https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)

V této části použijete [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) k vytvoření certifikátu podepsaného svým držitelem. Při vytváření naslouchací proces pro aplikační bránu nahrajete certifikát na portál Azure.

V místním počítači otevřete okno prostředí Windows PowerShell jako správce. Chcete-li vytvořit certifikát, spusťte následující příkaz:

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Měli byste vidět něco takového odpověď:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Použijte [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) s kryptografickým otiskem, který byl vrácen k exportu souboru pfx z certifikátu:

> [!NOTE]
> V hesle souboru .pfx nepoužívejte žádné speciální znaky. Podporovány jsou pouze alfanumerické znaky.

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

1. V levé nabídce portálu Azure vyberte **Vytvořit prostředek.** Zobrazí se okno **Nový.**

2. Vyberte **Síť** a pak v seznamu **Doporučené** vyberte **Aplikační brána.**

### <a name="basics-tab"></a>Karta Základy

1. Na kartě **Základy** zadejte tyto hodnoty pro následující nastavení brány aplikace:

   - **Skupina prostředků**: Vyberte **myResourceGroupAG** pro skupinu prostředků. Pokud neexistuje, vyberte **Vytvořit nový** a vytvořte ho.
   - **Název aplikační brány**: Zadejte *myAppGateway* pro název aplikační brány.

        ![Vytvořit novou aplikační bránu: Základy](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Pro Azure komunikovat mezi prostředky, které vytvoříte, potřebuje virtuální síť. Můžete buď vytvořit novou virtuální síť, nebo použít existující. V tomto příkladu vytvoříte novou virtuální síť současně s vytvořením aplikační brány. Instance aplikační brány se vytvářejí v samostatných podsítích. V tomto příkladu vytvoříte dvě podsítě: jednu pro aplikační bránu a druhou pro servery back-endu.

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
   > Pro aplikační bránu v2 SKU můžete zvolit pouze **veřejnou** front-endovou konfiguraci IP. Privátní front-endová konfigurace IP není aktuálně povolena pro tuto skladovou položku v2.

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
    - **Protokol**: Vyberte **protokol HTTPS**.
    - **Port**: Ověřte, zda je pro port zadáno 443.

   V části **Certifikát HTTPS**:

   - **Soubor certifikátu PFX** – Vyhledejte a vyberte soubor c:\appgwcert.pfx, který jste vytvořili dříve.
   - **Název certifikátu** - Pro název certifikátu zadejte *mycert1.*
   - **Heslo** – pro heslo zadejte *Azure123456.*
  
        Přijměte výchozí hodnoty pro ostatní nastavení na kartě **Naslouchací proces** a pak vyberte kartu **Cíle back-endu** a nakonfigurujte zbytek pravidla směrování.

   ![Vytvořit novou aplikační bránu: naslouchací proces](./media/create-ssl-portal/application-gateway-create-rule-listener.png)

4. Na kartě **Cíle back-endu** vyberte **myBackendPool** pro **cíl back-endu**.

5. Pro **nastavení HTTP**vyberte **Vytvořit nový** a vytvořte nové nastavení HTTP. Nastavení HTTP určí chování pravidla směrování. V okně **Přidat nastavení PROTOKOLU HTTP,** které se otevře, zadejte pro **název nastavení HTTP nastavení myHTTPSetting**. *myHTTPSetting* Přijměte výchozí hodnoty pro ostatní nastavení v **okně Přidat nastavení HTTP** a pak vyberte **Přidat** a vraťte se do okna Přidat **pravidlo směrování.** 

   ![Vytvořit novou aplikační bránu: Nastavení HTTP](./media/create-ssl-portal/application-gateway-create-httpsetting.png)

6. V okně **Přidat pravidlo směrování** vyberte **Přidat,** chcete-li uložit pravidlo směrování a vrátit se na kartu **Konfigurace.**

   ![Vytvořit novou aplikační bránu: pravidlo směrování](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Vyberte **další: Značky** a pak **Další: Zkontrolujte + vytvořit**.

### <a name="review--create-tab"></a>Karta Revize + vytvoření

Zkontrolujte nastavení na kartě **Revize + vytvořit** a pak vyberte **Vytvořit,** chcete-li vytvořit virtuální síť, veřejnou IP adresu a bránu aplikace. Vytvoření brány aplikace azure může trvat několik minut. Počkejte, dokud nasazení úspěšně dokončí před přechodem na další část.

## <a name="add-backend-targets"></a>Přidání back-endových cílů

V tomto příkladu budete používat virtuální počítače jako cílový back-end. Můžete buď použít existující virtuální počítače, nebo vytvořit nové. Vytvoříte dva virtuální počítače, které Azure používá jako back-endové servery pro aplikační bránu.

Chcete-li to provést, budete:

1. Vytvořte dva nové virtuální servery, *myVM* a *myVM2*, které se použijí jako back-endové servery.
2. Nainstalujte službu IIS do virtuálních počítačů a ověřte, zda byla aplikační brána úspěšně vytvořena.
3. Přidejte back-endové servery do back-endového fondu.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. Na webu Azure Portal vyberte **Vytvořit prostředek**. Zobrazí se okno **Nový.**
2. V seznamu **Oblíbené** vyberte **Datové centrum Windows Serveru 2016.** Zobrazí se stránka **Vytvořit virtuální počítač.**

   Aplikační brána může směrovat provoz na libovolný typ virtuálního počítače používaného v back-endovém fondu. V tomto příkladu použijete datové centrum Windows Server 2016.

1. Na kartu **Základy** zadejte následující nastavení virtuálního počítače:

    - **Skupina prostředků**: Vyberte **myResourceGroupAG** pro název skupiny prostředků.
    - **Název virtuálního počítače**: Zadejte *myVM* pro název virtuálního počítače.
    - **Uživatelské jméno**: Zadejte *azureuser* pro uživatelské jméno správce.
    - **Heslo**: Zadejte *Azure123456* pro heslo správce.
4. Přijměte další výchozí hodnoty a pak vyberte **Další: Disky**.  
5. Přijměte výchozí nastavení karty **Disky** a pak vyberte **Další: Síť**.
6. Na kartě **Síť** ověřte, zda je pro **virtuální síť** vybraná **síť myVNet** a **podsíť** nastavena na **myBackendSubnet**. Přijměte ostatní výchozí hodnoty a pak vyberte **Další: Správa**.

   Aplikační brána může komunikovat s instancemi mimo virtuální síť, ve které se nachází, ale musíte zajistit připojení IP.
1. Na kartě **Správa** nastavte **diagnostiku spouštění** na **Vypnuto**. Přijměte ostatní výchozí hodnoty a pak vyberte **Zkontrolovat + vytvořit**.
2. Na kartě **Revize + vytvoření** zkontrolujte nastavení, opravte všechny chyby ověření a pak vyberte **Vytvořit**.
3. Před pokračováním počkejte na dokončení nasazení.

### <a name="install-iis-for-testing"></a>Instalace služby IIS pro testování

V tomto příkladu nainstalujete službu IIS na virtuální počítače pouze k ověření Azure úspěšně vytvořil aplikační bránu.

1. Otevřete [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Pokud tak chcete provést, vyberte **Cloud Shell** z horního navigačního panelu portálu Azure a pak v rozevíracím seznamu vyberte **PowerShell.** 

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

3. Vytvořte druhý virtuální počítač a nainstalujte službu IIS pomocí kroků, které jste dříve dokončili. *MyVM2* použijte pro název virtuálního počítače a pro nastavení **VMName** rutiny **Set-AzVMExtension.**

### <a name="add-backend-servers-to-backend-pool"></a>Přidání back-endových serverů do back-endového fondu

1. Vyberte **Všechny prostředky**a pak **vyberte myAppGateway**.

2. V levé nabídce vyberte **back-endové fondy.**

3. Vyberte **myBackendPool**.

4. V části **Cíle**vyberte v rozevíracím seznamu virtuální **počítač.**

5. V části **VIRTUÁLNÍ POČÍTAČ** a **SÍŤOVÁ ROZHRANÍ**vyberte virtuální počítače **myVM** a **myVM2** a jejich přidružená síťová rozhraní z rozevíracích seznamů.

    ![Přidání back-endových serverů](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Vyberte **Uložit**.

7. Před dokončením nasazení počkejte na dokončení nasazení, než přebudete pokračovat k dalšímu kroku.

## <a name="test-the-application-gateway"></a>Testování brány Application Gateway

1. Vyberte **Všechny prostředky**a potom vyberte **myAGPublicIPAddress**.

    ![Záznam veřejné IP adresy aplikační brány](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Do adresního řádku prohlížeče zadejte *https://\<\>ip adresu aplikační brány*.

   Pokud chcete přijmout upozornění zabezpečení, pokud jste použili certifikát podepsaný svým držitelem, vyberte **Podrobnosti** (nebo **Upřesnit v** Chromu) a přejděte na webovou stránku:

    ![Zabezpečené upozornění](./media/create-ssl-portal/application-gateway-secure.png)

    Potom se zobrazí váš zabezpečený web služby IIS, jak je znázorněno v následujícím příkladu:

    ![Testování základní adresy URL v aplikační bráně](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o podpoře SSL aplikační brány](ssl-overview.md)
