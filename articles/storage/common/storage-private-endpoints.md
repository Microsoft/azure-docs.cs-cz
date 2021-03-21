---
title: Použití privátních koncových bodů
titleSuffix: Azure Storage
description: Přehled privátních koncových bodů pro zabezpečený přístup k účtům úložiště z virtuálních sítí.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 3fcc58f626622bcc728265e782906226859e1bf9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600458"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Použití privátních koncových bodů pro Azure Storage

Pro účty Azure Storage můžete použít [soukromé koncové body](../../private-link/private-endpoint-overview.md) , které umožní klientům ve virtuální síti (VNET) zabezpečený přístup k datům prostřednictvím [privátního propojení](../../private-link/private-link-overview.md). Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro službu účtu úložiště. Síťový provoz mezi klienty ve virtuální síti a účtem úložiště prochází přes virtuální síť a privátní odkaz na páteřní síti Microsoftu, což eliminuje expozici veřejného Internetu.

Použití privátních koncových bodů pro váš účet úložiště vám umožní:

- Zabezpečte svůj účet úložiště tak, že nakonfigurujete bránu firewall úložiště tak, aby blokovala všechna připojení na veřejném koncovém bodu služby úložiště.
- Zvyšte zabezpečení virtuální sítě tak, že povolíte blokování exfiltrace dat z virtuální sítě.
- Připojte se bezpečně k účtům úložiště z místních sítí, které se připojují k virtuální síti pomocí [sítě VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoutes](../../expressroute/expressroute-locations.md) s privátním partnerským vztahem.

## <a name="conceptual-overview"></a>Základní přehled

![Přehled privátních koncových bodů pro Azure Storage](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Privátní koncový bod je speciální síťové rozhraní pro službu Azure ve vaší [Virtual Network](../../virtual-network/virtual-networks-overview.md) (virtuální síť). Když vytvoříte privátní koncový bod pro svůj účet úložiště, zajistíte zabezpečené připojení mezi klienty ve vaší virtuální síti a vaším úložištěm. Privátnímu koncovému bodu je přiřazena IP adresa z rozsahu IP adres vaší virtuální sítě. Připojení mezi soukromým koncovým bodem a službou úložiště používá zabezpečený privátní odkaz.

Aplikace ve virtuální síti se můžou bez problémů připojit k službě úložiště přes soukromý koncový bod **pomocí stejných připojovacích řetězců a mechanismů autorizace, které by jinak používaly**. Soukromé koncové body lze použít se všemi protokoly podporovanými účtem úložiště, včetně REST a protokolu SMB.

V podsítích, které používají [koncové body služby](../../virtual-network/virtual-network-service-endpoints-overview.md), lze vytvořit privátní koncové body. Klienti v podsíti se proto mohou připojit k jednomu účtu úložiště pomocí privátního koncového bodu a při použití koncových bodů služby k přístupu k ostatním.

Když pro službu úložiště ve své virtuální síti vytvoříte privátní koncový bod, vlastníkovi účtu úložiště se odešle ke schválení žádost o vyjádření souhlasu. Pokud je uživatel žádající o vytvoření privátního koncového bodu také vlastníkem účtu úložiště, bude tato žádost o přijetí souhlasu automaticky schválena.

Vlastníci účtu úložiště můžou spravovat žádosti o souhlas a soukromé koncové body přes kartu *privátní koncové body* pro účet úložiště v [Azure Portal](https://portal.azure.com).

> [!TIP]
> Pokud chcete omezit přístup k účtu úložiště jenom prostřednictvím privátního koncového bodu, nakonfigurujte bránu firewall úložiště tak, aby odepřela nebo řízení přístupu prostřednictvím veřejného koncového bodu.

Svůj účet úložiště můžete zabezpečit tak, aby přijímal jenom připojení z vaší virtuální sítě. [nakonfigurujete bránu firewall úložiště](storage-network-security.md#change-the-default-network-access-rule) tak, aby odepřela přístup prostřednictvím veřejného koncového bodu ve výchozím nastavení. Nepotřebujete pravidlo brány firewall, abyste mohli povolit provoz z virtuální sítě s privátním koncovým bodem, protože brána firewall úložiště řídí přístup jenom prostřednictvím veřejného koncového bodu. Místo toho privátní koncové body spoléhají na tok souhlasu pro udělení přístupu ke službě úložiště pro podsítě.

> [!NOTE]
> Při kopírování objektů BLOB mezi účty úložiště musí mít váš klient přístup k síti obou účtů. Takže pokud se rozhodnete použít privátní odkaz jenom pro jeden účet (zdroj nebo cíl), ujistěte se, že má váš klient přístup k síti k druhému účtu. Další informace o dalších způsobech konfigurace přístupu k síti najdete v tématu [konfigurace Azure Storage bran firewall a virtuálních sítí](storage-network-security.md?toc=/azure/storage/blobs/toc.json). 

<a id="private-endpoints-for-azure-storage"></a>

## <a name="creating-a-private-endpoint"></a>Vytvoření privátního koncového bodu

Postup vytvoření privátního koncového bodu pomocí webu Azure Portal najdete [v tématu připojení soukromě k účtu úložiště z prostředí účtu úložiště v Azure Portal](../../private-link/tutorial-private-endpoint-storage-portal.md).

Pokud chcete vytvořit privátní koncový bod pomocí PowerShellu nebo rozhraní příkazového řádku Azure, přečtěte si některý z těchto článků. Oba z nich využívají webovou aplikaci Azure jako cílovou službu, ale kroky pro vytvoření privátního odkazu jsou pro účet Azure Storage stejné.

- [Vytvoření privátního koncového bodu pomocí Azure CLI](../../private-link/create-private-endpoint-cli.md)

- [Vytvoření privátního koncového bodu pomocí Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)



Při vytváření privátního koncového bodu je nutné zadat účet úložiště a službu úložiště, ke které se připojí. 

Potřebujete samostatný soukromý koncový bod pro každý prostředek úložiště, ke kterému potřebujete přistupovat, konkrétně [objekty blob](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md), [soubory](../files/storage-files-introduction.md), [fronty](../queues/storage-queues-introduction.md), [tabulky](../tables/table-storage-overview.md)nebo [statické weby](../blobs/storage-blob-static-website.md). V případě privátního koncového bodu jsou tyto služby úložiště definované jako **cílový dílčí prostředek** přidruženého účtu úložiště. 

Pokud vytvoříte privátní koncový bod pro prostředek úložiště Data Lake Storage Gen2, měli byste ho také vytvořit pro prostředek BLOB Storage. Důvodem je, že operace, které cílí na Data Lake Storage Gen2 koncový bod, mohou být přesměrovány na koncový bod objektu BLOB. Vytvořením privátního koncového bodu pro oba prostředky zajistíte úspěšné dokončení operací.

> [!TIP]
> Vytvořte samostatný privátní koncový bod pro sekundární instanci služby úložiště pro lepší výkon při čtení účtů RA-GRS.
> Ujistěte se, že jste vytvořili účet úložiště pro obecné účely v2 (Standard nebo Premium).

Pro přístup pro čtení do sekundární oblasti s účtem úložiště nakonfigurovaným pro geograficky redundantní úložiště musíte oddělit soukromé koncové body pro primární i sekundární instance služby. Nemusíte vytvářet privátní koncový bod pro sekundární instanci pro **převzetí služeb při selhání**. Po převzetí služeb při selhání se privátní koncový bod automaticky připojí k nové primární instanci. Další informace o možnostech redundance úložiště najdete v tématu [Azure Storage redundance](storage-redundancy.md).

<a id="connecting-to-private-endpoints"></a>

## <a name="connecting-to-a-private-endpoint"></a>Připojení k privátnímu koncovému bodu

Klienti ve virtuální síti s použitím privátního koncového bodu by měli použít stejný připojovací řetězec pro účet úložiště, jak se klienti připojují k veřejnému koncovému bodu. Pro automatické směrování připojení z virtuální sítě do účtu úložiště prostřednictvím privátního propojení spoléháme na překlad DNS.

> [!IMPORTANT]
> Použijte stejný připojovací řetězec pro připojení k účtu úložiště pomocí privátních koncových bodů, protože byste mohli jinak použít. Nepřipojujte se prosím k účtu úložiště pomocí `privatelink` adresy URL své subdomény.

Ve výchozím nastavení vytvoříme [privátní ZÓNU DNS](../../dns/private-dns-overview.md) připojenou k virtuální síti s nezbytnými aktualizacemi pro privátní koncové body. Pokud ale používáte vlastní server DNS, možná budete muset provést další změny v konfiguraci DNS. Oddíl o [změnách DNS](#dns-changes-for-private-endpoints) níže popisuje aktualizace vyžadované pro soukromé koncové body.

## <a name="dns-changes-for-private-endpoints"></a>Změny DNS u privátních koncových bodů

Při vytváření privátního koncového bodu se záznam prostředku CNAME DNS pro účet úložiště aktualizuje na alias v subdoméně s předponou `privatelink` . Ve výchozím nastavení vytvoříme také [soukromou ZÓNU DNS](../../dns/private-dns-overview.md), která odpovídá `privatelink` subdoméně, a záznamy prostředků DNS pro privátní koncové body.

Při překladu adresy URL koncového bodu úložiště mimo virtuální síť s privátním koncovým bodem se přeloží na veřejný koncový bod služby úložiště. Při překladu z virtuální sítě hostující soukromý koncový bod se adresa URL koncového bodu úložiště přeloží na IP adresu privátního koncového bodu.

V zobrazeném příkladu se v záznamech o prostředcích DNS pro účet úložiště "StorageAccountA" po vyřešení mimo virtuální síť hostující soukromý koncový bod:

| Název                                                  | Typ  | Hodnota                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<storage service public endpoint\>                   |
| \<storage service public endpoint\>                   | A     | \<storage service public IP address\>                 |

Jak už jsme uvedli, můžete odepřít nebo řídit přístup pro klienty mimo virtuální síť prostřednictvím veřejného koncového bodu pomocí brány firewall úložiště.

Záznamy prostředků DNS pro StorageAccountA, když je klient ve virtuální síti hostující soukromý koncový bod, budou:

| Název                                                  | Typ  | Hodnota                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | zákazníka 10.1.1.5                                              |

Tento přístup umožňuje přístup k účtu úložiště **pomocí stejného připojovacího řetězce** pro klienty ve virtuální síti, která je hostitelem privátních koncových bodů, a taky klientů mimo virtuální síť.

Pokud ve vaší síti používáte vlastní server DNS, klienti musí být schopni přeložit plně kvalifikovaný název domény pro koncový bod účtu úložiště na IP adresu privátního koncového bodu. Server DNS byste měli nakonfigurovat tak, aby delegoval subdoménu privátního propojení s privátní zónou DNS pro virtuální síť, nebo můžete nakonfigurovat záznamy pro *StorageAccountA.privatelink.blob.Core.Windows.NET* s IP adresou privátního koncového bodu.

> [!TIP]
> Pokud používáte vlastní nebo místní server DNS, měli byste nakonfigurovat server DNS tak, aby přeložil název účtu úložiště v `privatelink` subdoméně na IP adresu privátního koncového bodu. Můžete to udělat tak `privatelink` , že subdoménu delegujete do privátní zóny DNS virtuální sítě nebo nakonfigurujete ZÓNU DNS na serveru DNS a přidáte záznamy DNS.

Doporučené názvy zón DNS pro privátní koncové body služby úložiště a přidružené dílčí prostředky cílového koncového bodu jsou:

| Služba úložiště        | Cílový dílčí prostředek | Název zóny                            |
| :--------------------- | :------------------ | :----------------------------------- |
| Blob service           | blob                | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | (                 | `privatelink.dfs.core.windows.net`   |
| Souborová služba           |  – soubor                | `privatelink.file.core.windows.net`  |
| Služba front          | fronta               | `privatelink.queue.core.windows.net` |
| Table service          | tabulka               | `privatelink.table.core.windows.net` |
| Statické weby        | web                 | `privatelink.web.core.windows.net`   |

Další informace o konfiguraci vlastního serveru DNS pro podporu privátních koncových bodů najdete v následujících článcích:

- [Překlad názvů pro prostředky ve virtuálních sítích Azure](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [Konfigurace DNS pro privátní koncové body](../../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="pricing"></a>Ceny

Podrobnosti o cenách najdete v tématu [ceny za privátní propojení Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Známé problémy

Pamatujte na následující známé problémy týkající se privátních koncových bodů pro Azure Storage.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Omezení přístupu k úložišti pro klienty v virtuální sítě s privátními koncovými body

Klienti v virtuální sítě se stávajícími omezeními tváře privátních koncových bodů při přístupu k jiným účtům úložiště s privátními koncovými body. Předpokládejme například, že virtuální síť N1 má privátní koncový bod pro účet úložiště a1 pro úložiště objektů BLOB. Pokud má účet úložiště a2 privátní koncový bod ve virtuálním osobním formátu N2 pro úložiště objektů blob, pak klienti ve virtuální síti N1 mají také přístup k úložišti objektů BLOB v účtu a2 pomocí privátního koncového bodu. Pokud účet úložiště a2 nemá žádné privátní koncové body pro úložiště objektů blob, klienti v síti VNet N1 mají přístup k úložišti objektů BLOB v tomto účtu bez privátního koncového bodu.

Toto omezení je výsledkem změn DNS provedených při vytváření privátního koncového bodu z účtu a2.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Pravidla skupin zabezpečení sítě pro podsítě s privátními koncovými body

V současné době nemůžete konfigurovat pravidla [skupiny zabezpečení sítě](../../virtual-network/network-security-groups-overview.md) (NSG) a trasy definované uživatelem pro privátní koncové body. Pravidla NSG použitá pro podsíť hostující soukromý koncový bod se neaplikují na soukromý koncový bod. Používají se jenom pro jiné koncové body (například: řadiče síťového rozhraní). Omezené řešení tohoto problému je implementace pravidel přístupu pro privátní koncové body ve zdrojových podsítích, i když tento přístup může vyžadovat vyšší režijní náklady na správu.

### <a name="copying-blobs-between-storage-accounts"></a>Kopírování objektů BLOB mezi účty úložiště

Objekty blob mezi účty úložiště můžete kopírovat pomocí privátních koncových bodů jenom v případě, že používáte Azure REST API nebo nástroje, které používají REST API. Mezi tyto nástroje patří AzCopy, Průzkumník služby Storage, Azure PowerShell, Azure CLI a sady SDK pro Azure Blob Storage. 

Podporují se jenom privátní koncové body, které cílí na prostředek úložiště objektů BLOB. Privátní koncové body, které cílí na Data Lake Storage Gen2 nebo prostředek souboru, se zatím nepodporují. Také kopírování mezi účty úložiště pomocí protokolu NFS (Network File System) ještě není podporováno. 

## <a name="next-steps"></a>Další kroky

- [Konfigurace bran firewall Azure Storage a virtuálních sítí](storage-network-security.md)
- [Doporučení zabezpečení pro úložiště objektů BLOB](../blobs/security-recommendations.md)
