---
title: Vytvoření privátního koncového bodu pro zabezpečené připojení
titleSuffix: Azure Cognitive Search
description: Nastavte privátní koncový bod ve virtuální síti pro zabezpečené připojení ke službě Azure Kognitivní hledání.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/19/2020
ms.openlocfilehash: 043020abd44bc1f8e671cf386149d6a818136de9
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700150"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search"></a>Vytvoření privátního koncového bodu pro zabezpečené připojení k Azure Kognitivní hledání

V tomto článku použijete Azure Portal k vytvoření nové instance služby Azure Kognitivní hledání, ke které nelze přistupovat prostřednictvím Internetu. Dále nakonfigurujete virtuální počítač Azure ve stejné virtuální síti a použijete ho k přístupu ke službě vyhledávání přes privátní koncový bod.

Soukromé koncové body poskytuje služba [Azure Private Link](../private-link/private-link-overview.md)jako samostatnou službu. Další informace o nákladech najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/private-link/).

> [!Important]
> Podporu privátního koncového bodu pro Azure Kognitivní hledání můžete nakonfigurovat pomocí Azure Portal nebo [REST API pro správu verze 2020-03-13](/rest/api/searchmanagement/). Pokud je koncový bod služby privátní, některé funkce portálu jsou zakázané. Budete moct zobrazit a spravovat informace o úrovni služby, ale přístup k indexovým datům a různým součástem ve službě, jako je index, indexer a definice dovednosti, je z bezpečnostních důvodů omezený.

## <a name="why-use-a-private-endpoint-for-secure-access"></a>Proč používat privátní koncový bod pro zabezpečený přístup?

[Privátní koncové body](../private-link/private-endpoint-overview.md) pro Azure kognitivní hledání umožňují klientovi ve virtuální síti zabezpečený přístup k datům v indexu vyhledávání prostřednictvím [privátního odkazu](../private-link/private-link-overview.md). Privátní koncový bod používá IP adresu z [adresního prostoru virtuální sítě](../virtual-network/private-ip-addresses.md) pro vaši vyhledávací službu. Síťový provoz mezi klientem a vyhledávací službou prochází přes virtuální síť a privátní odkaz na páteřní síti Microsoftu, což eliminuje expozici veřejného Internetu. Seznam dalších služeb PaaS Services, které podporují privátní propojení, najdete v [části dostupnost](../private-link/private-link-overview.md#availability) v dokumentaci k produktu.

Soukromé koncové body vaší vyhledávací služby vám umožní:

- Zablokuje všechna připojení na veřejném koncovém bodu pro vaši vyhledávací službu.
- Zvyšte zabezpečení virtuální sítě tím, že povolíte blokování exfiltrace dat z virtuální sítě.
- Připojte se bezpečně k vaší vyhledávací službě z místních sítí, které se připojují k virtuální síti pomocí [sítě VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoutes](../expressroute/expressroute-locations.md) s privátním partnerským vztahem.

## <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť a podsíť pro hostování virtuálního počítače, který se použije pro přístup k privátnímu koncovému bodu služby Search.

1. Na kartě Azure Portal domů vyberte **vytvořit prostředek**  >  **síť**  >  **virtuální síť**.

1. V nástroji **vytvořit virtuální síť** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **vytvořit nový**, zadejte *myResourceGroup* a pak vyberte **OK** . |
    | Name | Zadejte *MyVirtualNetwork* |
    | Oblast | Vyberte požadovanou oblast. |
    |||

1. Pro zbývající nastavení ponechte výchozí hodnoty. Klikněte na tlačítko **zkontrolovat + vytvořit** a pak **vytvořit** .

## <a name="create-a-search-service-with-a-private-endpoint"></a>Vytvoření vyhledávací služby s privátním koncovým bodem

V této části vytvoříte novou službu Azure Kognitivní hledání s privátním koncovým bodem. 

1. V levé horní části obrazovky Azure Portal vyberte **vytvořit prostředek**  >  **Web**  >  **Azure kognitivní hledání**.

1. V **New Search Service základy** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **PODROBNOSTI O PROJEKTU** | |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.|
    | **PODROBNOSTI INSTANCE** |  |
    | URL | Zadejte jedinečný název. |
    | Umístění | Vyberte požadovanou oblast. |
    | Cenová úroveň | Vyberte **změnit cenovou úroveň** a zvolte požadovanou úroveň služby. (Nepodporuje se na úrovni **Free** . Musí být **Basic** nebo vyšší.) |
    |||
  
1. Vyberte možnost **Další: škálovat**.

1. Ponechte hodnoty jako výchozí a vyberte **Další: sítě**.

1. V **New Search Service-Networking** vyberte pro **připojení koncového bodu (data)** možnost **privátní** .

1. V **New Search Service-Networking** vyberte **+ Přidat** pod **soukromým koncovým bodem**. 

1. V **Vytvoření privátního koncového bodu** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Předplatné | Vyberte své předplatné. |
    | Skupina prostředků | Vyberte **myResourceGroup**. Vytvořili jste ho v předchozí části.|
    | Umístění | Vyberte **západní USA**.|
    | Name | Zadejte *myPrivateEndpoint*.  |
    | Cílový dílčí prostředek | Ponechte výchozí **searchService**. |
    | **SÍTĚ** |  |
    | Virtuální síť  | Vyberte *MyVirtualNetwork* ze skupiny prostředků *myResourceGroup*. |
    | Podsíť | Vyberte *mySubnet*. |
    | **INTEGRACE S PRIVÁTNÍM DNS** |  |
    | Integrovat s privátní zónou DNS  | Ponechte výchozí **hodnotu Ano**. |
    | Zóna privátního DNS  | Ponechte výchozí * * (New) privatelink.search.windows.net * *. |
    |||

1. Vyberte **OK**. 

1. Vyberte **Zkontrolovat a vytvořit**. Budete přesměrováni na stránku **Zkontrolovat a vytvořit**, kde Azure ověří konfiguraci. 

1. Jakmile se zobrazí zpráva **Ověření proběhlo úspěšně**, vyberte **Vytvořit**. 

1. Po dokončení zřizování nové služby přejděte k prostředku, který jste právě vytvořili.

1. V nabídce v levém obsahu vyberte **klíče** .

1. Při připojování ke službě zkopírujte **primární klíč správce** pro pozdější verzi.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. V levé horní části obrazovky Azure Portal vyberte **vytvořit**  >  **Compute**  >  **virtuální počítač** Compute.

1. V nástroji **vytvořit virtuální počítač základy** zadejte nebo vyberte tyto informace:

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
    | Heslo | Zadejte libovolné heslo. Heslo musí mít délku aspoň 12 znaků a musí splňovat [definované požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potvrdit heslo | Zadejte znovu heslo. |
    | **PRAVIDLA PORTŮ PRO PŘÍCHOZÍ SPOJENÍ** |  |
    | Veřejné příchozí porty | Ponechte výchozí **povoleno vybrané porty**. |
    | Vyberte příchozí porty | Ponechte výchozí **RDP (3389)**. |
    | **UŠETŘETE PENÍZE** |  |
    | Máte už licenci na Windows? | Ponechte výchozí hodnotu **ne**. |
    |||

1. Vyberte **Další: disky**.

1. V okně **Vytvořit virtuální počítač – Disky** nechte vybrané výchozí hodnoty a vyberte **Další: Sítě**.

1. V nástroji **vytvořit virtuální počítač – síť** vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Virtuální síť | Ponechte výchozí **MyVirtualNetwork**.  |
    | Adresní prostor | Ponechte výchozí **10.1.0.0/24**.|
    | Podsíť | Ponechte výchozí **mySubnet (10.1.0.0/24)**.|
    | Veřejná IP adresa | Ponechte výchozí **(New) myVm-IP**. |
    | Veřejné příchozí porty | Vyberte možnost **Povolení vybraných portů**. |
    | Vyberte příchozí porty | Vyberte **http** a **RDP**.|
    ||

   > [!NOTE]
   > Adresy IPv4 lze vyjádřit ve formátu [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Nezapomeňte se vyhnout rozsahu IP adres rezervovanému pro privátní sítě, jak je popsáno v [dokumentu RFC 1918](https://tools.ietf.org/html/rfc1918):
   >
   > - `10.0.0.0 - 10.255.255.255  (10/8 prefix)`
   > - `172.16.0.0 - 172.31.255.255  (172.16/12 prefix)`
   > - `192.168.0.0 - 192.168.255.255 (192.168/16 prefix)`

1. Vyberte **Zkontrolovat a vytvořit**. Budete přesměrováni na stránku **Zkontrolovat a vytvořit**, kde Azure ověří konfiguraci.

1. Jakmile se zobrazí zpráva **Ověření proběhlo úspěšně**, vyberte **Vytvořit**. 

## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

Stáhněte si a pak se připojte k virtuálnímu počítači *myVm* následujícím způsobem:

1. Na portálu zadejte na panelu hledání *myVm*.

1. Klikněte na tlačítko **Připojit**. Po výběru tlačítka **připojit** se **připojte k virtuálnímu počítači** .

1. Vyberte **Stáhnout soubor RDP**. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) (*. RDP*) a stáhne ho do vašeho počítače.

1. Otevřete stažený soubor. RDP *.

    1. Pokud se zobrazí výzva, vyberte **Připojit**.

    1. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

        > [!NOTE]
        > Možná budete muset vybrat **Další volby**  >  **použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

1. Vyberte **OK**.

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění na certifikát, vyberte **Ano** nebo **Pokračovat**.

1. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji tak, aby se vrátila k místnímu počítači.  

## <a name="test-connections"></a>Test připojení

V této části ověříte přístup ke službě Search pomocí privátní sítě a soukromě se připojíte k privátnímu koncovému bodu.

Pokud je koncový bod vyhledávací služby privátní, některé funkce portálu jsou zakázané. Budete moct zobrazit a spravovat nastavení úrovně služby, ale přístup k indexovým datům a různým dalším součástem ve službě, jako je index, indexer a definice dovednosti, se z bezpečnostních důvodů omezuje.

1. Ve vzdálené ploše *myVM* otevřete PowerShell.

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

1. Z virtuálního počítače se připojte ke službě Search a vytvořte index. Můžete postupovat podle tohoto [rychlého](search-get-started-rest.md) startu a vytvořit nový index vyhledávání ve vaší službě pomocí REST API. Nastavování žádostí z nástroje Web API test Tool vyžaduje koncový bod vyhledávací služby (https://[název vyhledávací služby]. Search. Windows. NET) a klíč rozhraní API pro správu, který jste zkopírovali v předchozím kroku.

1. Dokončení rychlého startu z virtuálního počítače je vaším potvrzením, že služba je plně funkční.

1. Zavřete připojení ke vzdálené ploše pro *myVM*. 

1. Pokud chcete ověřit, že vaše služba není přístupná na veřejném koncovém bodu, otevřete ho na místní pracovní stanici a pokuste se o několik úkolů v rychlém startu. Pokud se zobrazí chyba, že vzdálený server neexistuje, úspěšně jste nakonfigurovali soukromý koncový bod pro vaši vyhledávací službu.

## <a name="clean-up-resources"></a>Vyčištění prostředků 
Po dokončení používání privátního koncového bodu, služby vyhledávání a virtuálního počítače odstraňte skupinu prostředků a všechny prostředky, které obsahuje:
1.  *myResourceGroup*   Do **vyhledávacího** pole v horní části portálu zadejte myResourceGroup a ve výsledcích hledání vyberte  *myResourceGroup*   . 
1. Vyberte **Odstranit skupinu prostředků**. 
1. Zadejte  *myResourceGroup*   pro **typ název skupiny prostředků** a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky
V tomto článku jste vytvořili virtuální počítač ve virtuální síti a vyhledávací službě s privátním koncovým bodem. Připojili jste se k virtuálnímu počítači z Internetu a bezpečně komunikovali se službou Search pomocí privátního odkazu. Další informace o privátním koncovém bodu najdete v tématu [co je privátní koncový bod Azure](../private-link/private-endpoint-overview.md).