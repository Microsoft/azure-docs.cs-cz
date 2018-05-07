---
title: Vytvoření služby application gateway s více hostování lokality - portálu Azure | Microsoft Docs
description: Postup vytvoření služby application gateway, který je hostitelem více lokalit pomocí portálu Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: f3dd092b2298bfc97cac30b8706e0588a466e1e0
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-portal"></a>Vytvoření služby application gateway s více lokality hostování pomocí portálu Azure

Na portálu Azure můžete použít ke konfiguraci [hostování více webů](application-gateway-multi-site-overview.md) při vytváření [Aplikační brána](application-gateway-introduction.md). V tomto kurzu vytvoříte back-endové fondy pomocí sady škálování virtuálních počítačů. Nakonfigurujete naslouchací procesy a pravidla založená na domény, které vlastníte a ujistěte se, že web přenos dorazí na příslušné servery ve fondech. V tomto kurzu se předpokládá, že vlastníte více domén a používá příklady *www.contoso.com* a *www.fabrikam.com*.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření služby Application Gateway
> * Vytváření virtuálních počítačů pro back-end serverů
> * Vytvoření back-endové fondy s back-end serverů
> * Vytvořte naslouchací procesy a pravidla směrování
> * Vytvořte záznam CNAME ve vaší doméně

![Příklad směrování více lokalit](./media/application-gateway-create-multisite-portal/scenario.png)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k portálu Azure v [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Virtuální síť je požadován pro komunikaci mezi prostředky, které vytvoříte. V tomto příkladu jsou vytvořeny dvě podsítě: jednu pro aplikační bránu a druhou pro back-end serverů. Můžete vytvořit virtuální síť ve stejnou dobu, kterou vytvoříte službu application gateway.

1. Klikněte na tlačítko **nový** najít v levém horním rohu portálu Azure.
2. Vyberte **sítě** a pak vyberte **Application Gateway** v seznamu doporučený.
3. Pro aplikační bránu, zadejte tyto hodnoty:

    - *myAppGateway* – pro název služby application gateway.
    - *myResourceGroupAG* – pro novou skupinu prostředků.

    ![Vytvořte novou aplikační bránu](./media/application-gateway-create-multisite-portal/application-gateway-create.png)

4. Přijměte výchozí hodnoty u ostatních nastavení a potom klikněte na **OK**.
5. Klikněte na tlačítko **vyberte virtuální síť**, klikněte na tlačítko **vytvořit nový**a potom zadejte tyto hodnoty pro virtuální síť:

    - *myVNet* – pro název virtuální sítě.
    - *10.0.0.0/16* – pro adresní prostor virtuální sítě.
    - *myAGSubnet* – název podsítě.
    - *10.0.0.0/24* – adresního prostoru podsítě.

    ![Vytvoření virtuální sítě](./media/application-gateway-create-multisite-portal/application-gateway-vnet.png)

6. Klikněte na tlačítko **OK** k vytvoření virtuální sítě a podsítě.
7. Klikněte na tlačítko **zvolte veřejnou IP adresu**, klikněte na tlačítko **vytvořit nový**a potom zadejte název veřejné IP adresy. V tomto příkladu je název veřejné IP adresy *myAGPublicIPAddress*. Přijměte výchozí hodnoty u ostatních nastavení a potom klikněte na **OK**.
8. Přijměte výchozí hodnoty pro konfiguraci naslouchacího procesu nechte zakázáno brány firewall webových aplikací a pak klikněte na tlačítko **OK**.
9. Zkontrolujte nastavení na stránce Souhrn a pak klikněte na tlačítko **OK** vytvoření síťové prostředky a aplikační brány. Ho může trvat několik minut, než aplikační brány, aby lze vytvořit, počkejte na dokončení nasazení přejde k další části úspěšně.

### <a name="add-a-subnet"></a>Přidat podsíť

1. Klikněte na tlačítko **všechny prostředky** v levé nabídce a pak klikněte na tlačítko **myVNet** ze seznamu prostředků.
2. Klikněte na tlačítko **podsítě**a potom klikněte na **podsítě**.

    ![Vytvoření podsítě](./media/application-gateway-create-multisite-portal/application-gateway-subnet.png)

3. Zadejte *myBackendSubnet* pro název podsítě a pak klikněte na tlačítko **OK**.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

V tomto příkladu vytvoříte dva virtuální počítače, který se má použít jako back-end serverů pro službu application gateway. Na virtuální počítače k ověření, že je správně směrování provozu taky nainstalovat službu IIS.

1. Klikněte na možnost **Nové**.
2. Klikněte na tlačítko **výpočetní** a pak vyberte **Windows Server 2016 Datacenter** v seznamu doporučený.
3. Pro virtuální počítač, zadejte tyto hodnoty:

    - *contosoVM* – pro název virtuálního počítače.
    - *azureuser* – uživatelské jméno správce.
    - *Azure123456!* pro heslo.
    - Vyberte **použít existující**a potom vyberte *myResourceGroupAG*.

4. Klikněte na **OK**.
5. Vyberte velikost virtuálního počítače **DS1_V2** a klikněte na **Vybrat**.
6. Ujistěte se, že **myVNet** je vybraná pro virtuální síť a podsíť je **myBackendSubnet**. 
7. Kliknutím na **Zakázáno** zakažte diagnostiku spouštění.
8. Klikněte na **OK**, na stránce souhrnu zkontrolujte nastavení a pak klikněte na **Vytvořit**.

### <a name="install-iis"></a>Instalace služby IIS

1. Otevřete prostředí pro interaktivní a ujistěte se, že je nastavena na **prostředí PowerShell**.

    ![Instalace vlastní rozšíření](./media/application-gateway-create-multisite-portal/application-gateway-extension.png)

2. Spusťte následující příkaz pro instalaci služby IIS na virtuálním počítači: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/vhorne/samplescripts/master/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Vytvořit druhý virtuální počítač a nainstalujte IIS pomocí kroky, které právě dokončila. Zadejte názvy *fabrikamVM* pro název a hodnotu VMName v AzureRmVMExtension sady.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Vytvoření back-endové fondy s virtuálními počítači

1. Klikněte na tlačítko **všechny prostředky** a pak klikněte na **myAppGateway**.
2. Klikněte na tlačítko **back-endové fondy**a potom klikněte na **přidat**.
3. Zadejte název *contosoPool* a přidejte *contosoVM* pomocí **přidat cíl**.

    ![Přidat back-end serverů](./media/application-gateway-create-multisite-portal/application-gateway-multisite-backendpool.png)

4. Klikněte na **OK**.
5. Klikněte na tlačítko **back-endové fondy** a pak klikněte na **přidat**.
6. Vytvořte *fabrikamPool* s *fabrikamVM* pomocí kroky, které právě dokončila.

## <a name="create-listeners-and-routing-rules"></a>Vytvořte naslouchací procesy a pravidla směrování

1. Klikněte na tlačítko **naslouchací procesy** a pak klikněte na **Multi-Site**.
2. Pro naslouchací proces, zadejte tyto hodnoty:
    
    - *contosoListener* – název naslouchacího procesu.
    - *www.contoso.com* – v tomto příkladu název hostitele nahraďte název vaší domény.

3. Klikněte na **OK**.
4. Vytvořte druhý naslouchací proces, pomocí názvu *fabrikamListener* a používat název domény druhé. V tomto příkladu *www.fabrikam.com* se používá.

Pravidla se zpracovávají v pořadí, jsou uvedeny a provoz se přesměruje pomocí prvního pravidla, která odpovídá bez ohledu na specifické podobě. Například, pokud máte pravidlo pomocí základní naslouchací proces a pravidla pomocí více lokalit naslouchací proces obou na stejném portu, musí být uvedené pravidlo s několika lokalitami naslouchací proces před pravidlo základní naslouchací proces, aby pravidlo více lokalit a fungovat podle očekávání. 

V tomto příkladu vytvoříte dvě nová pravidla a odstranit výchozí pravidlo, které byla vytvořena, když jste vytvořili službu application gateway. 

1. Klikněte na tlačítko **pravidla** a pak klikněte na **základní**.
2. Zadejte *contosoRule* pro název.
3. Vyberte *contosoListener* pro naslouchací proces.
4. Vyberte *contosoPool* pro fond back-end.

    ![Vytvoření pravidla, na základě cesty](./media/application-gateway-create-multisite-portal/application-gateway-multisite-rule.png)

5. Klikněte na **OK**.
6. Vytvořením druhého pravidla pomocí názvy *fabrikamRule*, *fabrikamListener*, a *fabrikamPool*.
7. Odstranit výchozí pravidlo s názvem *rule1 New* na něj kliknete, a potom kliknutím na **odstranit**.

## <a name="create-a-cname-record-in-your-domain"></a>Vytvořte záznam CNAME ve vaší doméně

Po vytvoření služby application gateway s veřejnou IP adresu můžete získat adresu serveru DNS a používat ho vytvořit záznam CNAME ve vaší doméně. Použití záznamů A není doporučeno, protože VIP může změnit při restartování služby application gateway.

1. Klikněte na tlačítko **všechny prostředky**a potom klikněte na **myAGPublicIPAddress**.

    ![Záznam Aplikační brána adresu serveru DNS](./media/application-gateway-create-multisite-portal/application-gateway-multisite-dns.png)

2. Zkopírujte adresu serveru DNS a použijte ji jako hodnotu na nový záznam CNAME ve vaší doméně.

## <a name="test-the-application-gateway"></a>Testování služby application gateway

1. Zadejte název domény do panelu Adresa prohlížeče. Například http://www.contoso.com.

    ![Testování serveru contoso v aplikační brány](./media/application-gateway-create-multisite-portal/application-gateway-iistest.png)

2. Změna adresy k jiné doméně a měli byste vidět něco podobného jako v následujícím příkladu:

    ![Testování serveru fabrikam v aplikační brány](./media/application-gateway-create-multisite-portal/application-gateway-iistest2.png)

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli, jak:

> [!div class="checklist"]
> * Vytvoření služby Application Gateway
> * Vytváření virtuálních počítačů pro back-end serverů
> * Vytvoření back-endové fondy s back-end serverů
> * Vytvořte naslouchací procesy a pravidla směrování
> * Vytvořte záznam CNAME ve vaší doméně

> [!div class="nextstepaction"]
> [Další informace o co můžete dělat s aplikační brány](application-gateway-introduction.md)