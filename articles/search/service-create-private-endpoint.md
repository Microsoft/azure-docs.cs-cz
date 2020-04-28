---
title: Vytvoření privátního koncového bodu pro zabezpečené připojení
titleSuffix: Azure Cognitive Search
description: Nastavení privátního koncového bodu ve virtuální síti pro zabezpečené připojení ke službě Azure Kognitivní hledání
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 2664b1abd4131cf1dca186c7b044e338bf1efa84
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75945827"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search-preview"></a>Vytvoření privátního koncového bodu pro zabezpečené připojení k Azure Kognitivní hledání (Preview)

V tomto článku vytvoříte pomocí portálu novou instanci služby Azure Kognitivní hledání, ke které nelze přistupovat prostřednictvím veřejné IP adresy. Dále nakonfigurujte virtuální počítač Azure ve stejné virtuální síti a použijte jej pro přístup k vyhledávací službě prostřednictvím privátního koncového bodu.

> [!Important]
> Podpora privátního koncového bodu pro Azure Kognitivní hledání je k dispozici [na vyžádání](https://aka.ms/SearchPrivateLinkRequestAccess) jako verze Preview s omezeným přístupem. Funkce ve verzi Preview se poskytují bez smlouvy o úrovni služeb a nedoporučují se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
>
> Po udělení přístupu ke službě Preview budete moci nakonfigurovat privátní koncové body pro vaši službu pomocí Azure Portal nebo [REST API správy verze 2019-10-06-Preview](https://docs.microsoft.com/rest/api/searchmanagement/).
>   

## <a name="why-use-private-endpoint-for-secure-access"></a>Proč používat privátní koncový bod pro zabezpečený přístup?

[Privátní koncové body](../private-link/private-endpoint-overview.md) pro Azure kognitivní hledání umožňují klientovi ve virtuální síti zabezpečený přístup k datům v indexu vyhledávání prostřednictvím [privátního odkazu](../private-link/private-link-overview.md). Privátní koncový bod používá IP adresu z [adresního prostoru virtuální sítě](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) pro vaši vyhledávací službu. Síťový provoz mezi klientem a vyhledávací službou prochází přes virtuální síť a privátní odkaz na páteřní síti Microsoftu, což eliminuje expozici veřejného Internetu. Seznam dalších služeb PaaS Services, které podporují privátní propojení, najdete v [části dostupnost](../private-link/private-link-overview.md#availability) v dokumentaci k produktu.

Soukromé koncové body vaší vyhledávací služby vám umožní:

- Zablokuje všechna připojení na veřejném koncovém bodu pro vaši vyhledávací službu.
- Zvyšte zabezpečení virtuální sítě tím, že povolíte blokování exfiltrace dat z virtuální sítě.
- Připojte se bezpečně k vaší vyhledávací službě z místních sítí, které se připojují k virtuální síti pomocí [sítě VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoutes](../expressroute/expressroute-locations.md) s privátním partnerským vztahem.

> [!NOTE]
> Ve verzi Preview jsou v současnosti nějaká omezení, o kterých byste měli vědět:
> * K dispozici pouze pro vyhledávací služby na úrovni **Basic** . 
> * K dispozici v oblastech Západní USA 2, Středozápadní USA, Východní USA, Střed USA – jih, Austrálie – východ a Austrálie – jihovýchod.
> * Pokud je koncový bod služby privátní, některé funkce portálu jsou zakázané. Budete moct zobrazit a spravovat informace o úrovni služby, ale přístup k indexovým datům a různým součástem ve službě, jako je index, indexer a definice dovednosti, je z bezpečnostních důvodů omezený.
> * Pokud je koncový bod služby privátní, musíte k odeslání dokumentů do indexu použít [REST API hledání](https://docs.microsoft.com/rest/api/searchservice/) .
> * K zobrazení možnosti podpora privátního koncového bodu v Azure Portal je nutné použít následující odkaz:https://portal.azure.com/?feature.enablePrivateEndpoints=true



## <a name="request-access"></a>Vyžádání přístup 

Kliknutím na [požádat o přístup](https://aka.ms/SearchPrivateLinkRequestAccess) se můžete zaregistrovat k této funkci ve verzi Preview. Formulář požaduje informace o vaší společnosti, vaší společnosti a obecné síťové topologii. Po kontrole vaší žádosti obdržíte potvrzovací e-mail s dalšími pokyny.

## <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který se použije pro přístup k privátnímu koncovému bodu služby Search.

1. Na kartě Azure Portal domů vyberte **vytvořit prostředek** > **síť** > **virtuální síť**.

1. V nástroji **vytvořit virtuální síť**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte *MyVirtualNetwork* |
    | Adresní prostor | Zadejte *10.1.0.0/16* |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **vytvořit nový**, zadejte *myResourceGroup*a pak vyberte **OK** . |
    | Umístění | Vyberte **západní USA** nebo libovolnou oblast, kterou používáte.|
    | Název podsítě | Zadejte *mySubnet* |
    | Podsíť – Rozsah adres | Zadejte *10.1.0.0/24* |
    |||

1. Ponechte REST jako výchozí a vyberte **vytvořit**.


## <a name="create-a-search-service-with-a-private-endpoint"></a>Vytvoření vyhledávací služby s privátním koncovým bodem

V této části vytvoříte novou službu Azure Kognitivní hledání s privátním koncovým bodem. 

1. V levé horní části obrazovky Azure Portal vyberte **vytvořit prostředek** > **Web** > **Azure kognitivní hledání**.

1. V **New Search Service základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.|
    | **PODROBNOSTI INSTANCE** |  |
    | zprostředkovatele identity | Zadejte jedinečný název. |
    | Umístění | Vyberte oblast, kterou jste zadali při žádosti o přístup k této funkci verze Preview. |
    | Cenová úroveň | Vyberte **změnit cenová úroveň** a zvolte **základní**. Tato úroveň je vyžadována pro verzi Preview. |
    |||
  
1. Vyberte možnost **Další: škálovat**.

1. Ponechte hodnoty jako výchozí a vyberte **Další: sítě**.

1. V **New Search Service-Networking**vyberte pro **připojení koncového bodu (data)** možnost **privátní** .

1. V **New Search Service-Networking**vyberte **+ Přidat** pod **soukromým koncovým bodem**. 

1. V **Vytvoření privátního koncového bodu**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.|
    | Umístění | Vyberte **západní USA**.|
    | Název | Zadejte *myPrivateEndpoint*.  |
    | Cílový dílčí prostředek | Ponechte výchozí **searchService**. |
    | **SÍTĚ** |  |
    | Virtuální síť  | Vyberte *MyVirtualNetwork* ze skupiny prostředků *myResourceGroup*. |
    | Podsíť | Vyberte *mySubnet*. |
    | **INTEGRACE PRIVÁTNÍ DNS** |  |
    | Integrace s privátní zónou DNS  | Ponechte výchozí **hodnotu Ano**. |
    | Zóna privátního DNS  | Ponechte výchozí * * (New) privatelink.search.windows.net * *. |
    |||

1. Vyberte **OK**. 

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvořit** , kde Azure ověřuje vaši konfiguraci. 

1. Když se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit**. 

1. Po dokončení zřizování nové služby přejděte k prostředku, který jste právě vytvořili.

1. V nabídce v levém obsahu vyberte **klíče** .

1. Při připojování ke službě zkopírujte **primární klíč správce** pro pozdější verzi.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. V levé horní části obrazovky Azure Portal vyberte **vytvořit** > **Compute** > **virtuální počítač**Compute.

1. V nástroji **vytvořit virtuální počítač základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.  |
    | **PODROBNOSTI INSTANCE** |  |
    | Název virtuálního počítače | Zadejte *myVm*. |
    | Oblast | Vyberte **západní USA** nebo libovolnou oblast, kterou používáte. |
    | Možnosti dostupnosti | Nechte výchozí nastavení **bez nutnosti redundance infrastruktury**. |
    | Image | Vyberte **Windows Server 2019 Datacenter**. |
    | Velikost | Ponechte výchozí hodnotu **Standard DS1 v2**. |
    | **ÚČET SPRÁVCE** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno, které si zvolíte. |
    | Heslo | Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potvrdit heslo | Zadejte znovu heslo. |
    | **PRAVIDLA PORTŮ PRO PŘÍCHOZÍ SPOJENÍ** |  |
    | Veřejné příchozí porty | Ponechte výchozí **povoleno vybrané porty**. |
    | Vybrat příchozí porty | Ponechte výchozí **RDP (3389)**. |
    | **ÚSPORA PENĚZ** |  |
    | Máte už licenci na Windows? | Ponechte výchozí hodnotu **ne**. |
    |||

1. Vyberte **Další: disky**.

1. V části **vytvořit virtuální počítač – disky**ponechte výchozí hodnoty a vyberte **Další: sítě**.

1. V nástroji **vytvořit virtuální počítač – síť**vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Virtuální síť | Ponechte výchozí **MyVirtualNetwork**.  |
    | Adresní prostor | Ponechte výchozí **10.1.0.0/24**.|
    | Podsíť | Ponechte výchozí **mySubnet (10.1.0.0/24)**.|
    | Veřejná IP adresa | Ponechte výchozí **(New) myVm-IP**. |
    | Veřejné příchozí porty | Vyberte možnost **Povolení vybraných portů**. |
    | Vybrat příchozí porty | Vyberte **http** a **RDP**.|
    ||

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvořit** , kde Azure ověřuje vaši konfiguraci.

1. Když se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit**. 


## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

Stáhněte si a pak se připojte k virtuálnímu počítači *myVm* následujícím způsobem:

1. Na panelu hledání na portálu zadejte *myVm*.

1. Klikněte na tlačítko **Připojit**. Po výběru tlačítka **připojit** se **připojte k virtuálnímu počítači** .

1. Vyberte **Stáhnout soubor RDP**. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) (*. RDP*) a stáhne ho do vašeho počítače.

1. Otevřete stažený soubor. RDP *.

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

        > [!NOTE]
        > Možná budete muset vybrat **Další volby** > **použít jiný účet**a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

1. Vyberte **OK**.

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění certifikátu, vyberte **Ano** nebo **pokračovat**.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji tak, aby se vrátila k místnímu počítači.  


## <a name="test-connections"></a>Test připojení

V této části ověříte přístup ke službě Search pomocí privátní sítě a soukromě se připojíte k privátnímu koncovému bodu.

V úvodu se seznámíte s tím, že všechny interakce se službou Search vyžadují [REST API vyhledávání](https://docs.microsoft.com/rest/api/searchservice/). Portál a sada .NET SDK nejsou v této verzi Preview podporované.

1. Ve vzdálené ploše *myVM*otevřete PowerShell.

1. Zadejte příkaz nslookup [Search [název služby]. Search. Windows. NET.

    Zobrazí se zpráva podobná této:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. Z virtuálního počítače se připojte ke službě Search a vytvořte index. Můžete postupovat podle tohoto [rychlého](search-get-started-postman.md) startu a pomocí REST API vytvořit nový index vyhledávání ve vaší službě. Nastavení požadavků od post vyžaduje koncový bod vyhledávací služby (https://[název vyhledávací služby]. Search. Windows. NET) a klíč rozhraní API pro správu, který jste zkopírovali v předchozím kroku.

1. Dokončení rychlého startu z virtuálního počítače je vaším potvrzením, že služba je plně funkční.

1. Zavřete připojení ke vzdálené ploše pro *myVM*. 

1. Pokud chcete ověřit, že vaše služba není přístupná na veřejném koncovém bodu, otevřete ho na místní pracovní stanici a pokuste se o několik úkolů v rychlém startu. Pokud se zobrazí chyba, že vzdálený server neexistuje, úspěšně jste nakonfigurovali soukromý koncový bod pro vaši vyhledávací službu.

## <a name="clean-up-resources"></a>Vyčištění prostředků 
Po dokončení používání privátního koncového bodu, služby vyhledávání a virtuálního počítače odstraňte skupinu prostředků a všechny prostředky, které obsahuje:
1. Do **vyhledávacího** pole v horní části portálu zadejte *myResourceGroup* a ve výsledcích hledání vyberte *myResourceGroup* . 
1. Vyberte **Odstranit skupinu prostředků**. 
1. Zadejte *myResourceGroup* pro **typ název skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky
V tomto článku jste vytvořili virtuální počítač ve virtuální síti a vyhledávací službě s privátním koncovým bodem. Připojili jste se k virtuálnímu počítači z Internetu a bezpečně komunikovali se službou Search pomocí privátního odkazu. Další informace o privátním koncovém bodu najdete v tématu [co je privátní koncový bod Azure](../private-link/private-endpoint-overview.md).
