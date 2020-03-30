---
title: Vytvoření privátního koncového bodu pro zabezpečené připojení
titleSuffix: Azure Cognitive Search
description: Nastavení privátního koncového bodu ve virtuální síti pro zabezpečené připojení ke službě Azure Cognitive Search
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 2664b1abd4131cf1dca186c7b044e338bf1efa84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945827"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search-preview"></a>Vytvoření privátního koncového bodu pro zabezpečené připojení k Azure Cognitive Search (Preview)

V tomto článku použijte portál k vytvoření nové instance služby Azure Cognitive Search, ke které nelze získat přístup prostřednictvím veřejné IP adresy. Dále nakonfigurujte virtuální počítač Azure ve stejné virtuální síti a použijte ho pro přístup k vyhledávací službě prostřednictvím privátního koncového bodu.

> [!Important]
> Privátní endpoint podpora pro Azure Cognitive Search je k dispozici [na vyžádání](https://aka.ms/SearchPrivateLinkRequestAccess) jako náhled s omezeným přístupem. Funkce náhledu jsou k dispozici bez smlouvy o úrovni služeb a nejsou doporučeny pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
>
> Jakmile vám bude udělen přístup k verzi Preview, budete moct nakonfigurovat privátní koncové body pro vaši službu pomocí portálu Azure nebo [rozhraní API pro správu REST verze 2019-10-06-Preview](https://docs.microsoft.com/rest/api/searchmanagement/).
>   

## <a name="why-use-private-endpoint-for-secure-access"></a>Proč použít privátní koncový bod pro zabezpečený přístup?

[Privátní koncové body](../private-link/private-endpoint-overview.md) pro Azure Cognitive Search umožňují klientovi ve virtuální síti zabezpečit přístup k datům v indexu vyhledávání přes [privátní propojení](../private-link/private-link-overview.md). Privátní koncový bod používá IP adresu z [adresního prostoru virtuální sítě](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) pro vyhledávací službu. Síťový provoz mezi klientem a vyhledávací službou prochází přes virtuální síť a privátní propojení v páteřní síti společnosti Microsoft, což eliminuje expozici z veřejného internetu. Seznam dalších služeb PaaS, které podporují privátní odkaz, naleznete v [části dostupnosti](../private-link/private-link-overview.md#availability) v dokumentaci k produktu.

Soukromé koncové body pro vyhledávací službu umožňují:

- Blokovat všechna připojení na veřejný koncový bod pro vyhledávací službu.
- Zvyšte zabezpečení virtuální sítě tím, že vám umožní blokovat exfiltraci dat z virtuální sítě.
- Bezpečně se připojte k vyhledávací službě z místních sítí, které se připojují k virtuální síti pomocí [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoutes](../expressroute/expressroute-locations.md) s privátním partnerem.

> [!NOTE]
> V současné době existují určitá omezení v náhledu, které byste měli znát:
> * K dispozici pouze pro vyhledávací služby na úrovni **Basic.** 
> * K dispozici v oblastech Západní USA 2, Západní střed USA, Východní USA, Střední USA– jih, Austrálie – východ a Austrálie – jihovýchod.
> * Pokud je koncový bod služby soukromý, některé funkce portálu jsou zakázány. Budete moci zobrazit a spravovat informace o úrovni služby, ale přístup portálu k datům indexu a různým součástem ve službě, jako je index, indexer a definice dovedností, je z bezpečnostních důvodů omezen.
> * Pokud koncový bod služby je soukromý, musíte použít [rozhraní REST ROZHRANÍ PRO vyhledávání](https://docs.microsoft.com/rest/api/searchservice/) k nahrání dokumentů do indexu.
> * Chcete-li zobrazit možnost podpory privátního koncového bodu na webu Azure Portal, musíte použít následující odkaz:https://portal.azure.com/?feature.enablePrivateEndpoints=true



## <a name="request-access"></a>Vyžádání přístup 

Chcete-li se zaregistrovat k této funkci náhledu, klepněte na [tlačítko Požádat](https://aka.ms/SearchPrivateLinkRequestAccess) o přístup. Formulář požaduje informace o vás, vaší společnosti a obecné topologii sítě. Jakmile vaši žádost zkontrolujeme, obdržíte potvrzovací e-mail s dalšími pokyny.

## <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který se použije pro přístup k privátnímu koncovému bodu vaší vyhledávací služby.

1. Na kartě Domů na portálu Azure vyberte Vytvořit**Networking** > **virtuální síť** **prostředků** > .

1. V **možnosti Vytvořit virtuální síť**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte *MyVirtualNetwork* |
    | Adresní prostor | Zadejte *10.1.0.0/16* |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **Vytvořit nový**, zadejte *myResourceGroup*a pak vyberte **OK.** |
    | Umístění | Vyberte **Západní USA** nebo jakoukoli oblast, kterou používáte.|
    | Podsíť – název | Zadejte *mySubnet* |
    | Podsíť – Rozsah adres | Zadejte *10.1.0.0/24* |
    |||

1. Zbytek ponechejte jako výchozí a vyberte **Vytvořit**.


## <a name="create-a-search-service-with-a-private-endpoint"></a>Vytvoření vyhledávací služby se soukromým koncovým bodem

V této části vytvoříte novou službu Azure Cognitive Search s privátním koncovým bodem. 

1. Na levé horní straně obrazovky na portálu Azure vyberte **Vytvořit prostředek** > **web Azure** > **Cognitive Search**.

1. V **nové vyhledávací službě – základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **položku myResourceGroup**. Vytvořili jste to v předchozí části.|
    | **PODROBNOSTI INSTANCE** |  |
    | zprostředkovatele identity | Zadejte jedinečný název. |
    | Umístění | Vyberte oblast, kterou jste zadali při žádosti o přístup k této funkci náhledu. |
    | Cenová úroveň | Vyberte **Změnit cenovou úroveň** a zvolte **Základní**. Tato úroveň je vyžadována pro náhled. |
    |||
  
1. Vyberte **další: Měřítko**.

1. Ponechte hodnoty jako výchozí a vyberte **Další: Síť**.

1. V **části Nová vyhledávací služba – síť**vyberte možnost **Soukromé** pro **připojení (data) koncového bodu**.

1. V **nové vyhledávací službě – v síti**vyberte + **Přidat** v části Soukromý **koncový bod**. 

1. V **části Vytvořit soukromý koncový bod**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **položku myResourceGroup**. Vytvořili jste to v předchozí části.|
    | Umístění | Vyberte **možnost Západní USA**.|
    | Name (Název) | Zadejte *myPrivateEndpoint*.  |
    | Cílový dílčí zdroj | Ponechte výchozí **službu searchService**. |
    | **Sítí** |  |
    | Virtuální síť  | Vyberte *možnost MyVirtualNetwork* ze skupiny prostředků *myResourceGroup*. |
    | Podsíť | Vyberte *možnost mySubnet*. |
    | **PRIVÁTNÍ INTEGRACE DNS** |  |
    | Integrace se soukromou zónou DNS  | Ponechte výchozí **hodnotu Ano**. |
    | Zóna privátního DNS  | Ponechte výchozí ** (Nový) privatelink.search.windows.net**. |
    |||

1. Vyberte **OK**. 

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvoření,** kde Azure ověřuje vaši konfiguraci. 

1. Když se zobrazí zpráva **Ověření předáno,** vyberte **vytvořit**. 

1. Po dokončení zřizování nové služby přejděte k prostředku, který jste právě vytvořili.

1. Z levé nabídky obsahu vyberte **Klávesy.**

1. Zkopírujte **primární klíč správce** pro pozdější připojení ke službě.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. Na levé horní straně obrazovky na webu Azure Portal vyberte **Vytvořit prostředek** > **Výpočetní** > virtuální**počítač**.

1. V **části Vytvořit virtuální počítač – základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **položku myResourceGroup**. Vytvořili jste to v předchozí části.  |
    | **PODROBNOSTI INSTANCE** |  |
    | Název virtuálního počítače | Zadejte *myVm*. |
    | Region (Oblast) | Vyberte **Západní USA** nebo jakoukoli oblast, kterou používáte. |
    | Možnosti dostupnosti | Ponechte výchozí **Není vyžadována redundance žádné infrastruktury**. |
    | Image | Vyberte **Datové centrum Windows Server 2019**. |
    | Velikost | Ponechte výchozí **standardní DS1 v2**. |
    | **ÚČET SPRÁVCE** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno podle svého výběru. |
    | Heslo | Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potvrdit heslo | Znovu zadejte heslo. |
    | **PRAVIDLA PŘÍCHOZÍHO PORTU** |  |
    | Veřejné příchozí porty | Ponechte výchozí **povolit vybrané porty**. |
    | Výběr příchozích portů | Ponechte výchozí **rdp (3389)**. |
    | **UŠETŘETE PENÍZE** |  |
    | Máte už licenci na Windows? | Ponechte výchozí **ne**. |
    |||

1. Vyberte **další: Disky**.

1. V **části Vytvoření virtuálního počítače – disky**ponechte výchozí hodnoty a vyberte **Další: Síť**.

1. V **panelu Vytvořit virtuální počítač – Vytváření sítí**vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Virtuální síť | Ponechte výchozí **program MyVirtualNetwork**.  |
    | Adresní prostor | Ponechte výchozí **hodnotu 10.1.0.0/24**.|
    | Podsíť | Ponechte výchozí **mySubnet (10.1.0.0/24)**.|
    | Veřejná IP adresa | Ponechte výchozí **(nový) myVm-ip**. |
    | Veřejné příchozí porty | Vyberte **Povolit vybrané porty**. |
    | Výběr příchozích portů | Vyberte **možnosti HTTP** a **RDP**.|
    ||

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvoření,** kde Azure ověřuje vaši konfiguraci.

1. Když se zobrazí zpráva **Ověření předáno,** vyberte **vytvořit**. 


## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

Stáhněte si a připojte se k *myVm* virtuálního zařízení takto:

1. Na vyhledávacím panelu portálu zadejte *myVm*.

1. Klikněte na tlačítko **Připojit**. Po výběru tlačítka **Připojit** se otevře **možnost Připojit k virtuálnímu počítači.**

1. Vyberte **stáhnout soubor RDP**. Azure vytvoří soubor rdp (Remote Desktop Protocol *)* a stáhne ho do počítače.

1. Otevřete soubor Downloaded.rdp*.

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

        > [!NOTE]
        > Možná budete muset vybrat **Další volby:** > K zadání pověření, která jste zadali při vytváření virtuálního vztahu,**můžete zadat jiný účet**.

1. Vyberte **OK**.

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění na certifikát, vyberte **ano** nebo **pokračovat**.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji a vraťte se na místní plochu.  


## <a name="test-connections"></a>Testovací připojení

V této části ověříte přístup k vyhledávací službě privátní sítí a soukromě se připojíte k privátnímu koncovému bodu.

Připomeňme si z úvodu, že všechny interakce s vyhledávací službou vyžadují [rozhraní REST API vyhledávání](https://docs.microsoft.com/rest/api/searchservice/). Portál a sada .NET SDK nejsou v tomto náhledu podporovány.

1. Na vzdálené ploše *myVM*otevřete PowerShell.

1. Zadejte 'nslookup [název vyhledávací služby].search.windows.net'

    Zobrazí se zpráva podobná této:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. Z virtuálního virtuálního uživatele se připojte k vyhledávací službě a vytvořte index. Tento rychlý [start](search-get-started-postman.md) můžete vytvořit nový index vyhledávání ve vaší službě v Postman pomocí rozhraní REST API. Nastavení požadavků od Postman vyžaduje koncový bod vyhledávací služby (https://[název služby vyhledávání].search.windows.net) a klíč api správce, který jste zkopírovali v předchozím kroku.

1. Dokončení rychlého startu z virtuálního počítače je vaše potvrzení, že služba je plně funkční.

1. Zavřete připojení ke vzdálené ploše *myVM*. 

1. Chcete-li ověřit, že vaše služba není přístupná ve veřejném koncovém bodě, otevřete Postman na místní pracovní stanici a pokuste se o několik prvních úkolů v rychlém startu. Pokud se zobrazí chyba, že vzdálený server neexistuje, úspěšně jste nakonfigurovali soukromý koncový bod pro vyhledávací službu.

## <a name="clean-up-resources"></a>Vyčištění prostředků 
Až budete hotovi s privátním koncovým bodem, vyhledávací službou a virtuálním serverem, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:
1. Do pole **Hledat** v horní části portálu zadejte *myResourceGroup* a z výsledků hledání vyberte *myResourceGroup.*  
1. Vyberte **Odstranit skupinu prostředků**. 
1. Zadejte *myResourceGroup* pro **typ názvu skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky
V tomto článku jste vytvořili virtuální počítač ve virtuální síti a vyhledávací službu s privátním koncovým bodem. Připojili jste se k virtuálnímu virtuálnímu zařízení z internetu a bezpečně jste je přepojili na vyhledávací službu pomocí služby Private Link. Další informace o privátním koncovém bodu najdete v [tématu Co je Azure Private Endpoint?](../private-link/private-endpoint-overview.md).
