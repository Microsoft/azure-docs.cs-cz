---
title: Vytvoření služby application gateway s hostování několika webů – portál Azure portal | Dokumentace Microsoftu
description: Informace o vytvoření služby application gateway, který je hostitelem více webů pomocí webu Azure portal.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: 84f88c40d8fe1d88e9d85da5ef6c9e7992b33cc4
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049894"
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-portal"></a>Vytvoření služby application gateway s více webu, který hostuje pomocí webu Azure portal

Na webu Azure portal můžete použít ke konfiguraci [hostování více webů](application-gateway-multi-site-overview.md) při vytváření [služba application gateway](application-gateway-introduction.md). V tomto kurzu vytvoříte back-endových fondů škálovací sady virtuálních počítačů. Pak na základě domén, které vám patří, nakonfigurujete naslouchací procesy a pravidla, aby se webový provoz přesměroval na příslušné servery ve fondech. V tomto kurzu se předpokládá, že vlastníte několik domén, a jako příklady se používají domény *www.contoso.com* a *www.fabrikam.com*.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření služby Application Gateway
> * Vytvoření virtuálního počítače pro back-end serverů
> * Vytvoření back-endové fondy s back-end serverů
> * Vytvoření naslouchacích procesů a pravidla směrování
> * Vytvořit záznam CNAME v doméně

![Příklad směrování na více webů](./media/application-gateway-create-multisite-portal/scenario.png)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k portálu Azure Portal na adrese [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Vytvoření služby Application Gateway

Virtuální sítě je potřeba ke komunikaci mezi prostředky, které vytvoříte. V tomto příkladu jsou vytvořeny dvě podsítě: jedna pro aplikační bránu a druhá pro back-endové servery. Virtuální síť můžete vytvořit současně s aplikační bránou.

1. Klikněte na tlačítko **nový** v levém horním rohu webu Azure portal.
2. Vyberte **Sítě** a potom v seznamu Doporučené vyberte **Application Gateway**.
3. Pro aplikační bránu zadejte tyto hodnoty:

    - *myAppGateway* – tuto hodnotu zadejte jako název aplikační brány.
    - *myResourceGroupAG* – tuto hodnotu zadejte jako skupinu prostředků.

    ![Vytvoření nové aplikační brány](./media/application-gateway-create-multisite-portal/application-gateway-create.png)

4. U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.
5. Klikněte na tlačítko **zvolit virtuální síť**, klikněte na tlačítko **vytvořit nový**a potom zadejte tyto hodnoty pro virtuální síť:

    - *myVNet* – tuto hodnotu zadejte jako název virtuální sítě.
    - *10.0.0.0/16* – tuto hodnotu zadejte jako adresní prostor virtuální sítě.
    - *myBackendSubnet* – tuto hodnotu zadejte jako název podsítě.
    - *10.0.0.0/24* – tuto hodnotu zadejte jako adresní prostor podsítě.

    ![Vytvoření virtuální sítě](./media/application-gateway-create-multisite-portal/application-gateway-vnet.png)

6. Kliknutím na **OK** vytvořte virtuální síť a podsíť.
7. Klikněte na tlačítko **zvolte veřejnou IP adresu**, klikněte na tlačítko **vytvořit nový**a potom zadejte název veřejné IP adresy. V tomto příkladu se veřejná IP adresa nazývá *myAGPublicIPAddress*. U ostatních nastavení ponechejte výchozí hodnoty a potom klikněte na **OK**.
8. Přijměte výchozí hodnoty pro konfiguraci naslouchacího procesu, nechat zakázané brány firewall webových aplikací a pak klikněte na **OK**.
9. Zkontrolujte nastavení na stránce Souhrn a pak klikněte na tlačítko **OK** k vytvoření síťových prostředků a aplikační brány. Může trvat několik minut, než pro službu application gateway, chcete-li vytvořit, počkejte na úspěšné dokončení nasazení přejde k další části.

### <a name="add-a-subnet"></a>Přidání podsítě

1. V nabídce nalevo klikněte na **Všechny prostředky** a potom v seznamu prostředků klikněte na **myVNet**.
2. Klikněte na tlačítko **podsítě**a potom klikněte na tlačítko **podsítě**.

    ![Vytvoření podsítě](./media/application-gateway-create-multisite-portal/application-gateway-subnet.png)

3. Jako název podsítě zadejte *myBackendSubnet* a potom klikněte na **OK**.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

V tomto příkladu vytvoříte dva virtuální počítače, které se použijí jako servery back-end pro aplikační bránu. Také nainstalujte IIS na virtuálních počítačích, chcete-li ověřit, že je správně směrování provozu.

1. Klikněte na možnost **Nové**.
2. Klikněte na tlačítko **Compute** a pak vyberte **systému Windows Server 2016 Datacenter** v seznamu vybrané.
3. Zadejte pro virtuální počítač tyto hodnoty:

    - *názvy contosoVM* – název virtuálního počítače.
    - *azureuser* – uživatelské jméno správce.
    - *Azure123456!* – heslo.
    - Vyberte **Použít existující** a pak vyberte *myResourceGroupAG*.

4. Klikněte na **OK**.
5. Vyberte velikost virtuálního počítače **DS1_V2** a klikněte na **Vybrat**.
6. Zkontrolujte, že u virtuální sítě je vybrána možnost **myVNet** a u podsítě **myBackendSubnet**. 
7. Kliknutím na **Zakázáno** zakažte diagnostiku spouštění.
8. Klikněte na **OK**, na stránce souhrnu zkontrolujte nastavení a pak klikněte na **Vytvořit**.

### <a name="install-iis"></a>Instalace služby IIS

1. Otevřete interaktivní prostředí a zkontrolujte, že je nastaveno na **PowerShell**.

    ![Instalace vlastního rozšíření](./media/application-gateway-create-multisite-portal/application-gateway-extension.png)

2. Spuštěním následujícího příkazu nainstalujte službu IIS na virtuální počítač: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
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

3. Vytvoření druhého virtuálního počítače a instalace služby IIS pomocí kroky, které jste právě dokončili. Zadejte názvy *fabrikamVM* pro název a hodnotu VMName v Set-AzureRmVMExtension.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Vytvoření back-endové fondy s virtuálními počítači

1. Klikněte na tlačítko **všechny prostředky** a potom klikněte na tlačítko **myAppGateway**.
2. Klikněte na tlačítko **back-endové fondy**a potom klikněte na tlačítko **přidat**.
3. Zadejte název *contosoPool* a přidejte *contosoVM* pomocí **přidat cíl**.

    ![Přidání back-endových serverů](./media/application-gateway-create-multisite-portal/application-gateway-multisite-backendpool.png)

4. Klikněte na **OK**.
5. Klikněte na tlačítko **back-endové fondy** a potom klikněte na tlačítko **přidat**.
6. Vytvořte *fabrikamPool* s *fabrikamVM* pomocí kroků, které jste právě dokončili.

## <a name="create-listeners-and-routing-rules"></a>Vytvoření naslouchacích procesů a pravidla směrování

1. Klikněte na tlačítko **naslouchacích procesů** a potom klikněte na tlačítko **Multi-Site**.
2. Zadejte tyto hodnoty pro naslouchací proces:
    
    - *contosoListener* – název naslouchacího procesu.
    - *www.contoso.com* – v tomto příkladu název hostitele nahraďte název vaší domény.

3. Klikněte na **OK**.
4. Vytvoření druhého naslouchací proces, pomocí názvu *fabrikamListener* a používání názvů domén vaší druhé. V tomto příkladu *www.fabrikam.com* se používá.

Pravidla se zpracovávají v pořadí, v kterém jsou uvedena, a provoz se směruje pomocí prvního odpovídajícího pravidla bez ohledu na specifickou podobu. Pokud máte například na stejném portu pravidlo založené na základním naslouchacím procesu a pravidlo založené na naslouchacím procesu pro více webů, musí být pravidlo s naslouchacím procesem pro více webů uvedeno před pravidlem se základním naslouchacím procesem, aby fungovalo podle očekávání. 

V tomto příkladu vytvoříte dvě nová pravidla a odstraníte výchozí pravidlo, které se vytvořilo při vytvoření aplikační brány. 

1. Klikněte na tlačítko **pravidla** a potom klikněte na tlačítko **základní**.
2. Zadejte *contosoRule* pro název.
3. Vyberte *contosoListener* pro naslouchací proces.
4. Vyberte *contosoPool* pro back-endový fond.

    ![Vytvořit pravidlo na základě cest](./media/application-gateway-create-multisite-portal/application-gateway-multisite-rule.png)

5. Klikněte na **OK**.
6. Vytvořením druhého pravidla pomocí názvů *fabrikamRule*, *fabrikamListener*, a *fabrikamPool*.
7. Odstranit výchozí pravidlo s názvem *pravidla 1* kliknutím ji a pak levým na **odstranit**.

## <a name="create-a-cname-record-in-your-domain"></a>Vytvořit záznam CNAME v doméně

Jakmile vytvoříte aplikační bránu s veřejnou IP adresou, získáte adresu DNS, kterou můžete použít k vytvoření záznamu CNAME ve své doméně. Použití záznamů typu A se nedoporučuje, protože virtuální IP adresa se při restartování aplikační brány může změnit.

1. Klikněte na tlačítko **všechny prostředky**a potom klikněte na tlačítko **myAGPublicIPAddress**.

    ![Služba application gateway záznam DNS adresy](./media/application-gateway-create-multisite-portal/application-gateway-multisite-dns.png)

2. Zkopírujte adresu serveru DNS a použijte ji jako hodnotu pro nový záznam CNAME ve vaší doméně.

## <a name="test-the-application-gateway"></a>Otestování aplikační brány

1. Do adresního řádku prohlížeče zadejte název domény. Příklad: http://www.contoso.com.

    ![Testování webu Contoso v aplikační bráně](./media/application-gateway-create-multisite-portal/application-gateway-iistest.png)

2. Změňte adresu na jinou ze svých domén. Měli byste vidět něco podobného jako v následujícím příkladu:

    ![Testování webu Fabrikam v aplikační bráně](./media/application-gateway-create-multisite-portal/application-gateway-iistest2.png)

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvoření služby Application Gateway
> * Vytvoření virtuálního počítače pro back-end serverů
> * Vytvoření back-endové fondy s back-end serverů
> * Vytvoření naslouchacích procesů a pravidla směrování
> * Vytvořit záznam CNAME v doméně

> [!div class="nextstepaction"]
> [Další informace o možnostech aplikační brány](application-gateway-introduction.md)