---
title: Vytvoření privátního koncového bodu pro zabezpečená připojení
titleSuffix: Azure Cognitive Search
description: V současnosti ve verzi Preview můžete omezit přístup ke koncovému bodu vyhledávací služby pomocí privátního koncového bodu a zabezpečeného připojení k virtuální síti.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 0ed6319a33fc3db0b3144d91f4be10b7ab6121d7
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75865009"
---
# <a name="restrict-access-to-azure-cognitive-search-using-private-endpoint-and-a-virtual-network-connection"></a>Omezte přístup k Azure Kognitivní hledání pomocí privátního koncového bodu a připojení k virtuální síti.

> [!IMPORTANT] 
> Podpora privátního koncového bodu je aktuálně dostupná jako verze Preview s omezeným přístupem. Tato verze Preview je dostupná jenom pro vyhledávací služby na **úrovni Basic**.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Tato funkce poskytuje [REST API verze 2019-10-01-Preview](search-api-preview.md) . V tuto chvíli není k dispozici žádný portál ani podpora sady .NET SDK.

V tomto článku se dozvíte, jak vytvořit novou vyhledávací službu, která je přístupná přes zabezpečená připojení bez přístupu z veřejných IP adres. Z virtuálních počítačů Azure nasazených ve stejné virtuální síti jako služba se povolují připojení klientů.

## <a name="about-private-endpoint-support"></a>O podpoře privátního koncového bodu

[Privátní koncové body](../private-link/private-endpoint-overview.md) pro Azure kognitivní hledání umožňují klientovi ve virtuální síti zabezpečený přístup k datům prostřednictvím [privátního propojení](../private-link/private-link-overview.md). Privátní koncový bod používá IP adresu z [adresního prostoru virtuální sítě](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) pro vaši vyhledávací službu. Síťový provoz mezi klientem a vyhledávací službou prochází přes virtuální síť a privátní odkaz na páteřní síti Microsoftu, což eliminuje expozici veřejnému Internetu. Seznam služeb PaaS Services, které podporují privátní propojení, najdete v [části dostupnost](../private-link/private-link-overview.md#availability) v dokumentaci k produktu.

Soukromý koncový bod pro vaši vyhledávací službu vám umožní:

+ Zablokuje všechna připojení na veřejném koncovém bodu pro vaši vyhledávací službu.
+ Zvyšte zabezpečení virtuální sítě tím, že povolíte blokování exfiltrace dat z virtuální sítě.
+ Připojte se bezpečně k vaší vyhledávací službě z místních sítí, které se připojují k virtuální síti pomocí [sítě VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoutes](../expressroute/expressroute-locations.md) s privátním partnerským vztahem.

> [!NOTE]
> Pokud je koncový bod služby privátní, některé funkce portálu jsou zakázané. Budete moct zobrazit a spravovat informace o úrovni služby, ale přístup k indexovým datům a různým součástem ve službě, jako je index, indexer a definice dovednosti, je z bezpečnostních důvodů omezený.

## <a name="request-access"></a>Vyžádání přístup 

Kliknutím na [požádat o přístup](https://aka.ms/SearchPrivateLinkRequestAccess) se můžete zaregistrovat k této funkci ve verzi Preview. Formulář požaduje informace o vaší společnosti, vaší společnosti a obecné síťové topologii. Po kontrole vaší žádosti obdržíte potvrzovací e-mail s dalšími pokyny.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače
V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který se použije pro přístup k privátnímu koncovému bodu služby Search.

### <a name="set-up-the-virtual-network"></a>Nastavení virtuální sítě
1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. V levém horním rohu vyberte **vytvořit prostředek** > **síť** > **virtuální síť**.
1. V nástroji **vytvořit virtuální síť**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte *MyVirtualNetwork* |
    | Adresní prostor | Zadejte *10.1.0.0/16* |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **vytvořit nový**, zadejte *myResourceGroup*a pak vyberte **OK** . |
    | Umístění | Vyberte **západní USA** nebo libovolnou oblast, kterou používáte.|
    | Název podsítě | Zadejte *mySubnet* |
    | Podsíť – Rozsah adres | Zadejte *10.1.0.0/24* |
    |||

1. Ponechte REST jako výchozí a vyberte **vytvořit**.


### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. V levém horním rohu stránky hlavní portál vyberte **vytvořit prostředek** > **COMPUTE** > **virtuální počítač**.

1. V nástroji **vytvořit virtuální počítač základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.  |
    | **PODROBNOSTI INSTANCE** |  |
    | Název virtuálního počítače | Zadejte *myVm*. |
    | Region (Oblast) | Vyberte **západní USA** nebo libovolnou oblast, kterou používáte. |
    | Možnosti dostupnosti | Nechte výchozí nastavení **bez nutnosti redundance infrastruktury**. |
    | Obrázek | Vyberte **Windows Server 2019 Datacenter**. |
    | Velikost | Ponechte výchozí hodnotu **Standard DS1 v2**. |
    | **ÚČET SPRÁVCE** |  |
    | Uživatelské jméno | Zadejte uživatelské jméno, které si zvolíte. |
    | Heslo | Zadejte libovolné heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potvrdit heslo | Zadejte znovu heslo. |
    | **PRAVIDLA PORTŮ PRO PŘÍCHOZÍ SPOJENÍ** |  |
    | Veřejné příchozí porty | Nechejte výchozí nastavení **žádné**. |
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
    | Podsíť | Ponechte výchozí **mySubnet (10.1.0.0/24)** .|
    | Veřejná IP adresa | Ponechte výchozí **(New) myVm-IP**. |
    | Veřejné příchozí porty | Vyberte možnost **Povolení vybraných portů**. |
    | Vyberte příchozí porty | Vyberte **http** a **RDP**.|
    ||

1. Vyberte **Zkontrolovat a vytvořit**. Přejdete na stránku **Revize + vytvořit** , kde Azure ověřuje vaši konfiguraci.

1. Když se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit**.


## <a name="create-your-search-service-with-a-private-endpoint"></a>Vytvoření vyhledávací služby pomocí privátního koncového bodu

V této části vytvoříte novou službu Azure Kognitivní hledání s privátním koncovým bodem. 

1. V levém horním rohu stránky hlavní portál vyberte **vytvořit prostředek** > **Web** > **Azure kognitivní hledání**.

1. V **New Search Service základy**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.|
    | **PODROBNOSTI INSTANCE** |  |
    | Adresa URL | Zadejte jedinečný název. |
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
    | Name (Název) | Zadejte *myPrivateEndpoint*.  |
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
1. Po vytvoření služby přejděte k prostředku, který jste právě vytvořili.
1. V nabídce v levém obsahu vyberte **klíče** .
1. Zkopírujte **primární klíč správce** pro použití v dalším kroku.

 
## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Připojte se k virtuálnímu počítači *myVm* z Internetu následujícím způsobem:

1. Na panelu hledání na portálu zadejte *myVm*.

1. Klikněte na tlačítko **Připojit**. Po výběru tlačítka **připojit** se **připojte k virtuálnímu počítači** .

1. Vyberte **stáhnout soubor RDP**. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) ( *. RDP*) a stáhne ho do vašeho počítače.

1. Otevřete stažený soubor. RDP *.

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

        > [!NOTE]
        > Možná budete muset vybrat **Další volby** > **použít jiný účet**a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

1. Vyberte **OK**.

1. Při přihlašování se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění certifikátu, vyberte **Ano** nebo **pokračovat**.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji tak, aby se vrátila k místnímu počítači.  


## <a name="access-the-search-service-privately-from-the-vm"></a>Přístup k vyhledávací službě soukromě z virtuálního počítače

V této části ověříte přístup ke službě Search pomocí privátní sítě a soukromě se připojíte k účtu úložiště pomocí privátního koncového bodu.

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
1. Pomocí tohoto [rychlého](search-get-started-postman.md) startu na virtuálním počítači vytvořte nový index vyhledávání ve vaší službě v poli pro odeslání pomocí REST API.

1. Vyzkoušejte v nástroji post na místní pracovní stanici několik stejných požadavků.

1. Pokud se z virtuálního počítače dokončí rychlé spuštění, ale dojde k chybě, že vzdálený server v místní pracovní stanici neexistuje, úspěšně jste nakonfigurovali soukromý koncový bod pro vaši vyhledávací službu.

1. Zavřete připojení ke vzdálené ploše pro *myVM*. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Po dokončení používání privátního koncového bodu, účtu služby Search a virtuálního počítače odstraňte skupinu prostředků a všechny prostředky, které obsahuje: 

1. Do **vyhledávacího** pole v horní části portálu zadejte *myResourceGroup* a ve výsledcích hledání vyberte *myResourceGroup* . 
1. Vyberte **Odstranit skupinu prostředků**. 
1. Zadejte *myResourceGroup* pro **typ název skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili virtuální počítač ve virtuální síti a vyhledávací službě s privátním koncovým bodem. Připojili jste se k virtuálnímu počítači z Internetu a bezpečně komunikovali se službou Search pomocí privátního odkazu. 

> [!div class="nextstepaction"]
> [Co je to privátní koncový bod Azure?](../private-link/private-endpoint-overview.md)
