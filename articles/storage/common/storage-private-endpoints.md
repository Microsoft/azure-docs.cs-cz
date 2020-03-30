---
title: Použití soukromých koncových bodů
titleSuffix: Azure Storage
description: Přehled privátních koncových bodů pro zabezpečený přístup k účtům úložiště z virtuálních sítí.
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: c51f2db698f30368c9d4090d3d571fa0c131178a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299052"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Použití privátních koncových bodů pro Azure Storage

[Privátní koncové body](../../private-link/private-endpoint-overview.md) pro vaše účty Azure Storage můžete povolit klientům ve virtuální síti (VNet) k bezpečnému přístupu k datům přes [privátní propojení](../../private-link/private-link-overview.md). Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro službu účtu úložiště. Síťový provoz mezi klienty ve virtuální síti a účtem úložiště prochází přes virtuální síť a privátní propojení v páteřní síti Microsoftu, což eliminuje vystavení z veřejného internetu.

Použití soukromých koncových bodů pro váš účet úložiště umožňuje:

- Zabezpečte svůj účet úložiště konfigurací brány firewall úložiště tak, aby blokovala všechna připojení ve veřejném koncovém bodě pro službu úložiště.
- Zvyšte zabezpečení virtuální sítě (VNet) tím, že umožníte blokovat exfiltraci dat z virtuální sítě.
- Bezpečně se připojujte k účtům úložiště z místních sítí, které se připojují k virtuální síti pomocí [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoutes](../../expressroute/expressroute-locations.md) s privátním partnerského vztahu.

## <a name="conceptual-overview"></a>Koncepční přehled

![Přehled privátních koncových bodů pro Azure Storage](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Privátní koncový bod je speciální síťové rozhraní pro službu Azure ve [vaší virtuální síti](../../virtual-network/virtual-networks-overview.md) (Virtuální síť). Když vytvoříte soukromý koncový bod pro váš účet úložiště, poskytuje zabezpečené připojení mezi klienty ve virtuální síti a úložištěm. Privátnímu koncovému bodu je přiřazena IP adresa z rozsahu IP adres vaší virtuální sítě. Připojení mezi privátním koncovým bodem a službou úložiště používá zabezpečené privátní propojení.

Aplikace ve virtuální síti se můžou bez problémů připojit ke službě úložiště přes privátní koncový bod **pomocí stejných připojovacích řetězců a autorizačních mechanismů, které by jinak používaly**. Soukromé koncové body lze použít se všemi protokoly podporovanými účtem úložiště, včetně REST a SMB.

Soukromé koncové body lze vytvořit v podsítích, které používají [koncové body služby](../../virtual-network/virtual-network-service-endpoints-overview.md). Klienti v podsíti se tak mohou připojit k jednomu účtu úložiště pomocí privátního koncového bodu, zatímco k přístupu k ostatním uživatelům používají koncové body služby.

Když pro službu úložiště ve své virtuální síti vytvoříte privátní koncový bod, vlastníkovi účtu úložiště se odešle ke schválení žádost o vyjádření souhlasu. Pokud je uživatel požadující vytvoření privátního koncového bodu také vlastníkem účtu úložiště, bude tato žádost o souhlas automaticky schválena.

Vlastníci účtu úložiště můžou spravovat žádosti o souhlas a privátní koncové body, a to prostřednictvím karty*Privátní koncové body*pro účet úložiště na [webu Azure Portal](https://portal.azure.com).

> [!TIP]
> Pokud chcete omezit přístup k účtu úložiště pouze prostřednictvím privátního koncového bodu, nakonfigurujte bránu firewall úložiště tak, aby odepřela nebo řídila přístup prostřednictvím veřejného koncového bodu.

Účet úložiště můžete zabezpečit tak, aby přijímal jenom připojení z vaší virtuální sítě, a to tak, že ve výchozím nastavení [nakonfigurujete bránu firewall úložiště](storage-network-security.md#change-the-default-network-access-rule) tak, aby odepřela přístup prostřednictvím veřejného koncového bodu. K povolení přenosu z virtuální sítě, která má soukromý koncový bod, nepotřebujete pravidlo brány firewall, protože brána firewall úložiště řídí přístup jenom prostřednictvím veřejného koncového bodu. Privátní koncové body místo toho spoléhají na tok souhlasu pro udělení přístupu podsítí ke službě úložiště.

### <a name="private-endpoints-for-azure-storage"></a>Privátní koncové body pro Azure Storage

Při vytváření privátní koncový bod, musíte zadat účet úložiště a službu úložiště, ke kterému se připojuje. Potřebujete samostatný soukromý koncový bod pro každou službu úložiště v účtu úložiště, ke kterému potřebujete získat přístup, konkrétně [objekty BLOB](../blobs/storage-blobs-overview.md), [úložiště datových jezer Gen2](../blobs/data-lake-storage-introduction.md), [soubory](../files/storage-files-introduction.md), [fronty](../queues/storage-queues-introduction.md), [tabulky](../tables/table-storage-overview.md)nebo [statické weby](../blobs/storage-blob-static-website.md).

> [!TIP]
> Vytvořte samostatný soukromý koncový bod pro sekundární instanci služby úložiště pro lepší výkon čtení na účtech RA-GRS.

Pro přístup pro čtení do sekundární oblasti s účtem úložiště nakonfigurovaným pro geograficky redundantní úložiště potřebujete samostatné soukromé koncové body pro primární i sekundární instance služby. Není nutné vytvořit soukromý koncový bod pro sekundární instanci pro **převzetí služeb při selhání**. Soukromý koncový bod se automaticky připojí k nové primární instanci po převzetí služeb při selhání. Další informace o možnostech redundance úložiště najdete v [tématu redundance úložiště Azure](storage-redundancy.md).

Podrobnější informace o vytvoření privátního koncového bodu pro váš účet úložiště najdete v následujících článcích:

- [Soukromé připojení k účtu úložiště ze zkušeností s účtem úložiště na webu Azure Portal](../../private-link/create-private-endpoint-storage-portal.md)
- [Vytvoření privátního koncového bodu pomocí Centra privátního propojení na webu Azure Portal](../../private-link/create-private-endpoint-portal.md)
- [Vytvoření privátního koncového bodu pomocí azure cli](../../private-link/create-private-endpoint-cli.md)
- [Vytvoření privátního koncového bodu pomocí Azure PowerShellu](../../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Připojení k privátním koncovým bodům

Klienti ve virtuální síti pomocí privátního koncového bodu by měli používat stejný připojovací řetězec pro účet úložiště, jako klienti připojení k veřejnému koncovému bodu. Spoléháme na rozlišení DNS k automatickému směrování připojení z virtuální sítě do účtu úložiště přes soukromé propojení.

> [!IMPORTANT]
> Použijte stejný připojovací řetězec pro připojení k účtu úložiště pomocí soukromých koncových bodů, jako byste jinak. Nepřipojujte se k účtu úložiště pomocí adresy URL subdomény*privátního*odkazu.

Ve výchozím nastavení vytvoříme [privátní zónu DNS](../../dns/private-dns-overview.md) připojenou k virtuální síti s nezbytnými aktualizacemi pro privátní koncové body. Pokud však používáte vlastní server DNS, bude pravděpodobně nutné provést další změny konfigurace DNS. Část [o změnách DNS](#dns-changes-for-private-endpoints) níže popisuje aktualizace požadované pro soukromé koncové body.

## <a name="dns-changes-for-private-endpoints"></a>Změny DNS pro soukromé koncové body

Při vytváření privátního koncového bodu se záznam o prostředku CNAME DNS pro účet úložiště aktualizuje na alias v subdoméně s předponou*privatelink*. Ve výchozím nastavení také vytvoříme [soukromou zónu DNS](../../dns/private-dns-overview.md)odpovídající subdoméně*privátního odkazu*se záznamy o prostředcích DNS A pro soukromé koncové body.

Když vyřešíte adresu URL koncového bodu úložiště z mimo virtuální síť s privátním koncovým bodem, vyřeší se do veřejného koncového bodu služby úložiště. Když se vyřeší z virtuální sítě hostující soukromý koncový bod, adresa URL koncového bodu úložiště překládá na IP adresu privátního koncového bodu.

Pro ilustrovaný příklad výše, záznamy o prostředcích DNS pro účet úložiště "StorageAccountA", když vyřešen z mimo virtuální síť hostování privátní koncový bod, bude:

| Name (Název)                                                  | Typ  | Hodnota                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<veřejný koncový bod služby úložiště\>                   |
| \<veřejný koncový bod služby úložiště\>                   | A     | \<veřejná IP adresa služby úložiště\>                 |

Jak již bylo zmíněno, můžete odepřít nebo řídit přístup pro klienty mimo virtuální síť prostřednictvím veřejného koncového bodu pomocí brány firewall úložiště.

Záznamy o prostředcích DNS pro StorageAccountA, když jsou vyřešeny klientem ve virtuální síti hostující privátní koncový bod, budou:

| Name (Název)                                                  | Typ  | Hodnota                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Tento přístup umožňuje přístup k účtu úložiště **pomocí stejného připojovacího řetězce** pro klienty na virtuální síti hostující privátní koncové body, stejně jako klienti mimo virtuální síť.

Pokud v síti používáte vlastní server DNS, musí být klienti schopni přeložit hlavní název domény pro koncový bod účtu úložiště na privátní IP adresu koncového bodu. Server DNS byste měli nakonfigurovat tak, aby delegoval subdoménu privátního propojení do privátní zóny DNS pro virtuální síť, nebo nakonfigurovat záznamy A pro*StorageAccountA.privatelink.blob.core.windows.net*s privátní IP adresou koncového bodu.

> [!TIP]
> Při použití vlastního nebo místního serveru DNS byste měli nakonfigurovat server DNS tak, aby překládal název účtu úložiště v subdoméně privatelink na privátní IP adresu koncového bodu. Můžete to provést delegováním subdomény "privatelink" do privátní zóny DNS virtuální sítě nebo konfigurací zóny DNS na serveru DNS a přidáním záznamů DNS A.

Doporučené názvy zón DNS pro soukromé koncové body pro služby úložiště jsou:

| Služba úložiště        | Název zóny                            |
| :--------------------- | :----------------------------------- |
| Blob service           | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `privatelink.dfs.core.windows.net`   |
| Souborová služba           | `privatelink.file.core.windows.net`  |
| Služba fronty          | `privatelink.queue.core.windows.net` |
| Stolní servis          | `privatelink.table.core.windows.net` |
| Statické weby        | `privatelink.web.core.windows.net`   |

Další informace o konfiguraci vlastního serveru DNS pro podporu privátníkoncové koncové body naleznete v následujících článcích:

- [Překlad názvů pro prostředky ve virtuálních sítích Azure](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [Konfigurace DNS pro soukromé koncové body](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="pricing"></a>Ceny

Podrobnosti o cenách najdete v článku [Ceny Azure Private Link](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Známé problémy

Mějte na paměti následující známé problémy týkající se privátní koncové body pro Azure Storage.

### <a name="copy-blob-support"></a>Podpora pro Copy Blob

Pokud je účet úložiště chráněn bránou firewall a účet je přístupný prostřednictvím soukromých koncových bodů, pak tento účet nemůže sloužit jako zdroj operace [kopírování objektů blob.](/rest/api/storageservices/copy-blob)

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Omezení přístupu k úložišti pro klienty ve virtuálních sítích s privátními koncovými body

Klienti ve virtuálních sítích s existujícími privátními koncovými body čelí omezením při přístupu k jiným účtům úložiště, které mají privátní koncové body. Předpokládejme například, že virtuální síť N1 má soukromý koncový bod pro účet úložiště A1 pro úložiště objektů blob. Pokud účet úložiště A2 má soukromý koncový bod ve virtuální síti N2 pro úložiště objektů blob, pak klienti ve virtuální síti N1 musí také přístup k úložišti objektů Blob v účtu A2 pomocí privátního koncového bodu. Pokud účet úložiště A2 nemá žádné soukromé koncové body pro úložiště objektů Blob, pak klienti ve virtuální síti N1 přístup k úložišti objektů Blob v tomto účtu bez privátní koncový bod.

Toto omezení je výsledkem změn DNS provedených při vytvoření soukromého koncového bodu účtu A2.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Pravidla skupin zabezpečení sítě pro podsítě s privátními koncovými body

V současné době nelze nakonfigurovat pravidla [skupiny zabezpečení sítě](../../virtual-network/security-overview.md) (NSG) a uživatelem definované trasy pro soukromé koncové body. Pravidla skupiny nsg použitá pro podsíť hostující soukromý koncový bod se použijí na soukromý koncový bod. Omezené řešení tohoto problému je implementovat pravidla přístupu pro soukromé koncové body ve zdrojových podsítích, i když tento přístup může vyžadovat vyšší režii správy.

## <a name="next-steps"></a>Další kroky

- [Konfigurace bran azure storage a virtuálních sítí](storage-network-security.md)
- [Doporučení zabezpečení pro úložiště objektů Blob](../blobs/security-recommendations.md)
