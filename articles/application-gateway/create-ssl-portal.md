---
title: 'Kurz: Konfigurace ukončení TLS na portálu – Azure Application Gateway'
description: V tomto kurzu se naučíte konfigurovat Aplikační bránu a přidat certifikát pro ukončení TLS pomocí Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: victorh
ms.openlocfilehash: c976ea236ae1d37cc0a543b10a9de55609035632
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986748"
---
# <a name="tutorial-configure-an-application-gateway-with-tls-termination-using-the-azure-portal"></a>Kurz: Konfigurace aplikační brány s ukončením TLS pomocí Azure Portal

Pomocí Azure Portal můžete nakonfigurovat [Aplikační bránu](overview.md) s certifikátem pro ukončení protokolu TLS, který používá virtuální počítače pro back-end servery.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření certifikátu podepsaného svým držitelem (self-signed certificate)
> * Vytvořit aplikační bránu s certifikátem
> * Vytvoření virtuálních počítačů používaných jako servery back-end
> * Otestování aplikační brány

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Přihlaste se k Azure Portal v [https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Vytvoření certifikátu podepsaného svým držitelem (self-signed certificate)

V této části použijete [příkaz New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) k vytvoření certifikátu podepsaného svým držitelem. Certifikát nahrajete do Azure Portal při vytváření naslouchacího procesu pro službu Application Gateway.

V místním počítači otevřete okno Windows PowerShellu jako správce. Spuštěním následujícího příkazu vytvořte certifikát:

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Mělo by se zobrazit něco podobného jako tato odpověď:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Použijte [příkaz Export-vybíráte](/powershell/module/pkiclient/export-pfxcertificate) s kryptografickým otiskem, který byl vrácen pro export souboru PFX z certifikátu. Ujistěte se, že heslo je 4-12 znaků:


```powershell
$pwd = ConvertTo-SecureString -String <your password> -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Vytvoření brány Application Gateway

1. V levé nabídce Azure Portal vyberte **vytvořit prostředek** . Zobrazí se **nové** okno.

2. Vyberte **sítě** a v seznamu **Doporučené** vyberte **Application Gateway** .

### <a name="basics-tab"></a>Karta základy

1. Na kartě **základy** zadejte tyto hodnoty pro následující nastavení služby Application Gateway:

   - **Skupina prostředků**: pro skupinu prostředků vyberte **myResourceGroupAG** . Pokud neexistuje, vyberte **vytvořit novou** a vytvořte ji.
   - **Název aplikační brány**: jako název služby Application Gateway zadejte *myAppGateway* .

        ![Vytvořit novou aplikační bránu: Základy](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Aby mohl Azure komunikovat mezi prostředky, které vytvoříte, potřebuje virtuální síť. Můžete buď vytvořit novou virtuální síť, nebo použít existující. V tomto příkladu vytvoříte novou virtuální síť ve stejnou chvíli, kdy vytvoříte Aplikační bránu. Instance Application Gateway se vytvářejí v oddělených podsítích. V tomto příkladu vytvoříte dvě podsítě: jednu pro aplikační bránu a druhou pro back-end servery.

    V části **Konfigurovat virtuální síť** vytvořte novou virtuální síť výběrem možnosti **vytvořit nový**. V okně **vytvořit virtuální síť** , které se otevře, zadejte následující hodnoty pro vytvoření virtuální sítě a dvě podsítě:

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
   > V případě SKU Application Gateway v2 můžete zvolit jenom **veřejnou** konfiguraci IP adresy front-endu. V tuto chvíli není u této SKU verze V2 povolená soukromá konfigurace IP adresy front-endu.

2. Zvolte možnost **Přidat nový** pro **veřejnou IP adresu** a jako název veřejné IP adresy zadejte *myAGPublicIPAddress* a pak vyberte **OK**. 

   ![Vytvořit novou aplikační bránu: front-endové](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Vyberte **Další: back-endy**.

### <a name="backends-tab"></a>Karta back-endy

Back-end fond slouží ke směrování požadavků na servery back-end, které obsluhují požadavek. Back-endové fondy se dají skládat ze síťových adaptérů, virtuálních počítačů a virtuálních IP adres, interních IP adres, plně kvalifikovaných názvů domény (FQDN) a back-endu s více klienty, jako je Azure App Service. V tomto příkladu vytvoříte prázdný back-end fond s aplikační bránou a potom přidáte cíle back-end do fondu back-end.

1. Na kartě back- **endy** vyberte **Přidat back-end fond**.

2. V okně **Přidat fond back-end** , které se otevře, zadejte následující hodnoty a vytvořte prázdný back-end fond:

    - **Název**: jako název back-end fondu zadejte *myBackendPool* .
    - **Přidat back-end fond bez cílů**: vyberte **Ano** , pokud chcete vytvořit back-end fond bez cílů. Po vytvoření aplikační brány přidáte cíle back-endu.

3. V okně **Přidat fond back-endu** vyberte **Přidat** a uložte konfiguraci fondu back-end a vraťte se na kartu back- **endy** .

   ![Vytvořit novou aplikační bránu: back-endy](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. Na kartě **back-endy** vyberte **Další: Konfigurace**.

### <a name="configuration-tab"></a>Karta konfigurace

Na kartě **Konfigurace** se připojíte k front-endovému a back-endovému fondu, který jste vytvořili pomocí pravidla směrování.

1. Ve sloupci **pravidla směrování** vyberte **Přidat pravidlo směrování** .

2. V okně **Přidat pravidlo směrování** , které se otevře, jako **název pravidla** zadejte *myRoutingRule* .

3. Pravidlo směrování vyžaduje naslouchací proces. Na kartě **naslouchací proces** v okně **Přidat pravidlo směrování** zadejte následující hodnoty pro naslouchací proces:

    - **Název naslouchacího procesu**: jako název naslouchacího procesu zadejte *MyListener* .
    - **Front-end IP adresa**: vyberte **veřejné** a zvolte veřejnou IP adresu, kterou jste vytvořili pro front-end.
    - **Protokol**: vyberte **https**.
    - **Port**: pro port se zadá ověření 443.

   V části **nastavení https**:

   - **Zvolte certifikát** – vyberte **Odeslat certifikát**.
   - **Soubor certifikátu PFX** – vyhledejte a vyberte soubor c:\appgwcert.pfx, který jste vytvořili dříve.
   - **Název certifikátu** – jako název certifikátu zadejte *mycert1* .
   - **Heslo** – zadejte heslo, které jste použili k vytvoření certifikátu.
  
        Přijměte výchozí hodnoty pro ostatní nastavení na kartě **naslouchací proces** a potom vyberte kartu **cílení na back-end** a nakonfigurujte zbývající část pravidla směrování.

   ![Vytvoření nové aplikační brány: naslouchací proces](./media/create-ssl-portal/application-gateway-create-rule-listener.png)

4. Na kartě **cílení na server back-end** vyberte **MyBackendPool** pro **cíl back-endu**.

5. Pro **Nastavení http** vyberte **Přidat nový** a vytvořte nové nastavení http. Nastavením protokolu HTTP se určí chování pravidla směrování. V okně **Přidat nastavení protokolu HTTP** , které se otevře, zadejte *myHTTPSetting* pro **název nastavení http**. Přijměte výchozí hodnoty pro ostatní nastavení v okně **Přidat nastavení http** a pak vyberte **Přidat** a vraťte se do okna **Přidat pravidlo směrování** . 

   :::image type="content" source="./media/create-ssl-portal/application-gateway-create-httpsetting.png" alt-text="Vytvořit novou aplikační bránu: nastavení HTTP":::

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

1. V Azure Portal vyberte **vytvořit prostředek**. Zobrazí se **nové** okno.
2. V seznamu **oblíbených** vyberte **Windows Server 2016 Datacenter** . Zobrazí se stránka **vytvořit virtuální počítač** .

   Application Gateway může směrovat provoz na libovolný typ virtuálního počítače, který se používá v jeho fondu back-endu. V tomto příkladu použijete Windows Server 2016 Datacenter.

1. Zadejte tyto hodnoty na kartě **základy** pro následující nastavení virtuálního počítače:

    - **Skupina prostředků**: pro název skupiny prostředků vyberte **myResourceGroupAG** .
    - **Název virtuálního počítače**: jako název virtuálního počítače zadejte *myVM* .
    - **Uživatelské jméno**: zadejte název uživatelského jména správce.
    - **Heslo**: zadejte heslo pro účet správce.
1. Přijměte ostatní výchozí hodnoty a potom vyberte **Další: disky**.  
2. Přijměte výchozí hodnoty na kartě **disky** a potom vyberte **Další: sítě**.
3. Na kartě **sítě** ověřte, že je pro **virtuální síť** vybraný **myVNet** a že **podsíť** je nastavená na **myBackendSubnet**. Přijměte ostatní výchozí hodnoty a potom vyberte **Další: Správa**.

   Application Gateway může komunikovat s instancemi mimo virtuální síť, ve které je, ale je potřeba zajistit připojení k IP adrese.
1. Na kartě **Správa** nastavte **diagnostiku spouštění** na **zakázáno**. Přijměte ostatní výchozí hodnoty a pak vyberte **zkontrolovat + vytvořit**.
2. Na kartě **Revize + vytvořit** zkontrolujte nastavení, opravte chyby ověřování a potom vyberte **vytvořit**.
3. Než budete pokračovat, počkejte, dokud nasazování neskončí.

### <a name="install-iis-for-testing"></a>Nainstalovat službu IIS pro testování

V tomto příkladu nainstalujete službu IIS na virtuální počítače jenom k úspěšnému ověření, že se brána Application Gateway úspěšně vytvořila.

1. Otevřete [Azure PowerShell](../cloud-shell/quickstart-powershell.md). Provedete to tak, že v horním navigačním panelu Azure Portal vyberete **Cloud Shell** a v rozevíracím seznamu vyberete **PowerShell** . 

    ![Instalace vlastního rozšíření](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Změňte nastavení umístění pro vaše prostředí a pak spusťte následující příkaz pro instalaci služby IIS na virtuálním počítači: 

   ```azurepowershell-interactive
          Set-AzVMExtension `
            -ResourceGroupName myResourceGroupAG `
            -ExtensionName IIS `
            -VMName myVM `
            -Publisher Microsoft.Compute `
            -ExtensionType CustomScriptExtension `
            -TypeHandlerVersion 1.4 `
            -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
            -Location <location>
   ```

3. Vytvořte druhý virtuální počítač a nainstalujte službu IIS pomocí dříve dokončených kroků. Pro název virtuálního počítače a pro nastavení **VMName** rutiny **set-AzVMExtension** použijte *myVM2* .

### <a name="add-backend-servers-to-backend-pool"></a>Přidání back-end serverů do fondu back-endu

1. Vyberte **všechny prostředky** a pak vyberte **myAppGateway**.

2. V nabídce vlevo vyberte **back-endové fondy** .

3. Vyberte **myBackendPool**.

4. V části **cílový typ** vyberte z rozevíracího seznamu možnost **virtuální počítač** .

5. V části **cíl** vyberte v rozevíracím seznamu v části **myVM** síťové rozhraní.

6. Opakujte pro přidání síťového rozhraní pro **myVM2**.

    ![Přidání back-endových serverů](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Vyberte **Uložit**.

7. Než budete pokračovat k dalšímu kroku, počkejte na dokončení nasazení.

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

1. Vyberte **všechny prostředky** a pak vyberte **myAGPublicIPAddress**.

    ![Záznam veřejné IP adresy aplikační brány](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Do panelu Adresa v prohlížeči zadejte *https:// \<your application gateway ip address\>*.

   Pokud chcete upozornění zabezpečení přijmout, pokud jste použili certifikát podepsaný svým držitelem, vyberte **Podrobnosti** (nebo **Upřesnit** na Chrome) a pak se na webovou stránku dostanete takto:

    ![Zabezpečené upozornění](./media/create-ssl-portal/application-gateway-secure.png)

    Potom se zobrazí váš zabezpečený web služby IIS, jak je znázorněno v následujícím příkladu:

    ![Testování základní adresy URL v aplikační bráně](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků a vyberete **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o Application Gateway podpoře TLS](ssl-overview.md)