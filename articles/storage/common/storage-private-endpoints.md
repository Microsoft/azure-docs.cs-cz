---
title: Používání privátních koncových bodů s Azure Storage | Microsoft Docs
description: Přehled privátních koncových bodů pro zabezpečený přístup k účtům úložiště z virtuálních sítí.
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 09/25/2019
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: aec12cee7466e59389b28742bf66247751a0d22b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949466"
---
# <a name="using-private-endpoints-for-azure-storage-preview"></a>Použití privátních koncových bodů pro Azure Storage (Preview)

Azure Storage umožňuje použití [privátních koncových bodů](../../private-link/private-endpoint-overview.md) pro klienty ve virtuální síti (VNET) pro zabezpečený přístup k datům v účtu úložiště prostřednictvím [privátního propojení](../../private-link/private-link-overview.md). Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro službu účtu úložiště. Síťový provoz mezi klienty ve virtuální síti a účtem úložiště prochází přes virtuální síť a privátní odkaz na páteřní síti Microsoftu, což eliminuje expozici veřejného Internetu.

Použití privátních koncových bodů pro váš účet úložiště vám umožní:
- Zabezpečte svůj účet úložiště tak, že nakonfigurujete bránu firewall úložiště tak, aby blokovala všechna připojení na veřejném koncovém bodu služby úložiště.
- Zvyšuje zabezpečení virtuální sítě, protože umožňuje blokovat exfiltrace dat z virtuální sítě.
- Připojte se bezpečně k účtům úložiště z místních sítí, které se připojují k virtuální síti pomocí [sítě VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoutes](../../expressroute/expressroute-locations.md) s privátním partnerským vztahem.

## <a name="conceptual-overview"></a>Koncepční přehled
![Přehled privátních koncových bodů pro Azure Storage](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

Privátní koncový bod je speciální síťové rozhraní ve vaší [Virtual Network](../../virtual-network/virtual-networks-overview.md) (VNET) pro službu Azure. Zajišťuje zabezpečené připojení mezi klienty ve vaší virtuální síti a vaším účtem úložiště. Privátnímu koncovému bodu je přiřazena IP adresa z rozsahu IP adres vaší virtuální sítě. Připojení mezi soukromým koncovým bodem a službou úložiště používá zabezpečený privátní odkaz.

Aplikace ve virtuální síti se můžou bez problémů připojit k službě úložiště přes soukromý koncový bod pomocí stejných připojovacích řetězců a mechanismů autorizace, které by jinak používaly. Soukromé koncové body lze použít se všemi protokoly podporovanými účtem úložiště, včetně REST a protokolu SMB.

Když vytvoříte privátní koncový bod pro službu úložiště ve vaší virtuální síti, pošle se žádost o souhlas ke schválení vlastníkem účtu úložiště. Pokud je uživatel žádající o vytvoření privátního koncového bodu také vlastníkem účtu úložiště, bude tato žádost o přijetí souhlasu automaticky schválena.

Vlastníci účtu úložiště můžou žádosti o souhlas schvalovat nebo odmítat a také si můžou zobrazit nebo spravovat soukromé koncové body přes kartu privátní koncové body pro účet úložiště v [Azure Portal](https://portal.azure.com).

Svůj účet úložiště můžete zabezpečit tak, aby přijímal jenom připojení z vaší virtuální sítě. [nakonfigurujete bránu firewall úložiště](storage-network-security.md#change-the-default-network-access-rule) tak, aby odepřela přístup prostřednictvím veřejného koncového bodu ve výchozím nastavení. Nepotřebujete pravidlo brány firewall pro úložiště, abyste mohli povolit provoz z virtuální sítě s privátním koncovým bodem, protože pravidla brány firewall úložiště platí jenom pro svůj veřejný koncový bod. Místo toho privátní koncové body spoléhají na tok souhlasu pro udělení přístupu ke službě úložiště pro podsítě.

### <a name="private-endpoints-for-storage-service"></a>Privátní koncové body služby úložiště

Při vytváření privátního koncového bodu musíte zadat účet úložiště a službu úložiště, ke které se připojí. Pro každou službu úložiště v účtu úložiště, ke kterému je potřeba mít přístup, se vyžaduje privátní koncový bod, konkrétně [objekty blob](../blobs/storage-blobs-overview.md), [Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md), [soubory](../files/storage-files-introduction.md), [fronty](../queues/storage-queues-introduction.md), [tabulky](../tables/table-storage-overview.md)nebo [statické weby](../blobs/storage-blob-static-website.md).

Pro zajištění dostupnosti čtení [účtu geograficky redundantního úložiště s přístupem pro čtení](storage-redundancy-grs.md#read-access-geo-redundant-storage)budete potřebovat samostatné koncové body pro primární i sekundární instanci služby.

#### <a name="resources"></a>Materiály

Podrobnější informace o vytvoření privátního koncového bodu pro účet úložiště najdete v následujících článcích:

- [Připojte soukromě k účtu úložiště z prostředí účtu úložiště v Azure Portal](../../private-link/create-private-endpoint-storage-portal.md)
- [Vytvoření privátního koncového bodu pomocí centra privátního spojení v Azure Portal](../../private-link/create-private-endpoint-portal.md)
- [Vytvoření privátního koncového bodu pomocí Azure CLI](../../private-link/create-private-endpoint-cli.md)
- [Vytvoření privátního koncového bodu pomocí Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)

### <a name="dns-changes-for-private-endpoints"></a>Změny DNS u privátních koncových bodů

Při vytváření privátního koncového bodu pro službu úložiště aktualizujeme záznam prostředku CNAME DNS pro tento koncový bod úložiště na alias v subdoméně s předponou '*privatelink*'. Ve výchozím nastavení vytvoříme také [privátní ZÓNU DNS](../../dns/private-dns-overview.md) připojenou k virtuální síti. Tato soukromá zóna DNS odpovídá subdoménám s předponou '*privatelink*' a obsahuje záznamy prostředků DNS pro privátní koncové body.

Při překladu adresy URL koncového bodu úložiště mimo virtuální síť, ve které je vytvořen privátní koncový bod, se stále přeloží na veřejný koncový bod služby úložiště. Při překladu z virtuální sítě hostující soukromý koncový bod se adresa URL koncového bodu úložiště přeloží na IP adresu privátního koncového bodu.

V zobrazeném příkladu se v záznamech o prostředcích DNS pro účet úložiště "StorageAccountA" po vyřešení mimo virtuální síť hostující soukromý koncový bod:

| Name (Název)                                                  | Typ  | Hodnota                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<veřejný koncový bod\>                                   |
| \<veřejný koncový bod\>                                   | A     | \<veřejné IP adresy služby úložiště\>                 |

Jak už jsme uvedli, můžete všem přístupům odepřít přes Veřejný koncový bod pomocí brány firewall úložiště.

Záznamy prostředků DNS pro StorageAccountA, když je klient ve virtuální síti hostující soukromý koncový bod, budou:

| Name (Název)                                                  | Typ  | Hodnota                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | zákazníka 10.1.1.5                                              |

Tento přístup umožňuje přístup k účtu úložiště pomocí stejného připojovacího řetězce z virtuální sítě, která je hostitelem privátních koncových bodů, i klientů mimo virtuální síť. Bránu firewall úložiště můžete použít k odepření přístupu všem klientům mimo virtuální síť.

## <a name="pricing"></a>Ceny

Podrobnosti o cenách najdete v tématu [ceny za privátní propojení Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Známé problémy

### <a name="copy-blob-failures"></a>Selhání kopírování objektů BLOB

Příkazy [objektů BLOB](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) vydané pro účty úložiště, které jsou dostupné prostřednictvím privátních koncových bodů, se v současné době nezdaří, když je zdrojový účet úložiště chráněný bránou firewall.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Omezení přístupu k úložišti pro klienty v virtuální sítě s privátními koncovými body

Klienti v virtuální sítě, kteří mají při přístupu k jiným účtům úložiště s privátními koncovými body k dispozici existující omezení privátního koncového bodu úložiště Předpokládejme například, že virtuální síť N1 má privátní koncový bod pro účet úložiště a1 pro službu blob, řekněme, že. Pokud má účet úložiště a2 privátní koncový bod ve virtuálním N2 pro službu BLOB Service, pak klienti ve virtuální síti N1 taky musí přistupovat ke službě BLOB Service v účtu a2 pomocí privátního koncového bodu. Pokud účet úložiště a2 nemá žádné privátní koncové body pro službu BLOB Service, klienti v síti VNet N1 mají přístup ke službě BLOB bez privátního koncového bodu.

Toto omezení je výsledkem změn DNS provedených při vytváření privátního koncového bodu z účtu a2.

### <a name="nsg-rules-on-subnets-with-private-endpoints"></a>Pravidla NSG v podsítích s privátními koncovými body

Pravidla [skupiny zabezpečení sítě](../../virtual-network/security-overview.md) (NSG) nelze konfigurovat pro podsítě s privátními koncovými body v tomto okamžiku. Omezené řešení tohoto problému je implementace pravidel přístupu pro privátní koncové body ve zdrojových podsítích, i když tento přístup může vyžadovat vyšší režijní náklady na správu.
