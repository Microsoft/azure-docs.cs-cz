---
title: Konfigurace předvolby směrování sítě (Preview)
titleSuffix: Azure Storage
description: Nakonfigurujte předvolby směrování sítě (Preview) pro váš účet služby Azure Storage, abyste určili, jak se bude směrovat síťový provoz na váš účet od klientů přes Internet.
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: santoshc
ms.reviewer: tamram
ms.subservice: common
ms.custom: references_regions
ms.openlocfilehash: 601c8dfb4b4e2f16da5c560f67e2d251a5d3072a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362739"
---
# <a name="configure-network-routing-preference-for-azure-storage-preview"></a>Konfigurace předvolby směrování sítě pro Azure Storage (Preview)

Můžete nakonfigurovat [Předvolby směrování](../../virtual-network/routing-preference-overview.md) sítě (Preview) pro účet služby Azure Storage, abyste určili, jak bude síťový provoz směrován do svého účtu z klientů přes Internet. Ve výchozím nastavení je provoz z Internetu směrován do veřejného koncového bodu účtu úložiště přes [globální síť Microsoft](../../networking/microsoft-global-network.md). Azure Storage poskytuje další možnosti pro konfiguraci způsobu směrování provozu do svého účtu úložiště.

Při konfiguraci předvolby směrování získáte flexibilitu při optimalizaci provozu buď pro zvýšení výkonu sítě úrovně Premium, nebo pro náklady. Když nakonfigurujete předvolbu směrování, určíte, jak bude ve výchozím nastavení směrován z provozu na veřejný koncový bod vašeho účtu úložiště. Pro svůj účet úložiště můžete také publikovat koncové body specifické pro trasu.

> [!NOTE]
> Tato funkce není podporovaná v účtech úložiště, které jsou nakonfigurované tak, aby používaly úroveň výkonu Premium nebo úložiště redundantní v zóně (ZRS).

## <a name="microsoft-global-network-versus-internet-routing"></a>Směrování mezi globální sítí a internetem od Microsoftu

Ve výchozím nastavení klienti mimo prostředí Azure přistupují k vašemu účtu úložiště přes globální síť Microsoft. Globální síť Microsoft je optimalizovaná pro výběr cest s nízkou latencí a zajišťuje vysokou spolehlivost sítě úrovně Premium. Příchozí i odchozí provoz se směruje přes bod přítomnosti (POP), který je nejblíže klientovi. Tato výchozí konfigurace směrování zajišťuje, že provoz do a z vašeho účtu úložiště prochází přes globální síť Microsoftu za účelem hromadného výkonu, což maximalizuje výkon sítě.

Můžete změnit konfiguraci směrování pro svůj účet úložiště tak, aby příchozí i odchozí provoz byly směrovány do klientů mimo prostředí Azure prostřednictvím POP nejbližšího k účtu úložiště. Tato trasa minimalizuje průchod provozu v rámci globální sítě Microsoftu a vydává ho v nejbližší příležitosti pro tranzitního poskytovatele internetových služeb. Tato konfigurace směrování využívá nižší náklady na síť.

Následující diagram znázorňuje, jak přenos toků mezi klientem a účtem úložiště pro jednotlivé předvolby směrování:

![Přehled možností směrování pro Azure Storage](media/network-routing-preference/routing-options-diagram.png)

Další informace o předvolbách směrování v Azure najdete v tématu [co je předvolby směrování (Preview)?](../../virtual-network/routing-preference-overview.md).

## <a name="routing-configuration"></a>Konfigurace směrování

Jako výchozí předvolby směrování pro veřejný koncový bod vašeho účtu úložiště si můžete vybrat mezi globálním a internetovým směrováním od Microsoftu. Výchozí preference směrování se vztahuje na veškerý provoz od klientů mimo Azure a ovlivňuje koncové body Azure Data Lake Storage Gen2, BLOB Storage, souborů Azure a statických webů. Konfigurace předvoleb směrování není pro fronty Azure nebo tabulky Azure podporovaná.

Pro svůj účet úložiště můžete také publikovat koncové body specifické pro trasu. Při publikování koncových bodů specifických pro trasu Azure Storage vytvoří nové veřejné koncové body pro váš účet úložiště, který směruje provoz přes požadovanou cestu. Tato flexibilita vám umožní směrovat provoz do svého účtu úložiště přes konkrétní trasu beze změny výchozí předvolby směrování.

Například publikování koncového bodu specifického pro internetovou trasu pro StorageAccountA bude publikovat následující koncové body pro váš účet úložiště:

| Služba úložiště        | Koncový bod specifický pro trasu                                  |
| :--------------------- | :------------------------------------------------------- |
| Blob service           | `StorageAccountA-internetrouting.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting.dfs.core.windows.net`   |
| Souborová služba           | `StorageAccountA-internetrouting.file.core.windows.net`  |
| Statické weby        | `StorageAccountA-internetrouting.web.core.windows.net`   |

Pokud máte účet úložiště s přístupem pro čtení (RA-GRS) nebo geograficky redundantní úložiště s přístupem pro čtení (RA-GZRS), publikování koncových bodů specifických pro trasu také automaticky vytvoří odpovídající koncové body v sekundární oblasti pro přístup pro čtení.

| Služba úložiště        | Sekundární koncový bod určený jen pro čtení v trasách                        |
| :--------------------- | :----------------------------------------------------------------- |
| Blob service           | `StorageAccountA-internetrouting-secondary.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `StorageAccountA-internetrouting-secondary.dfs.core.windows.net`   |
| Souborová služba           | `StorageAccountA-internetrouting-secondary.file.core.windows.net`  |
| Statické weby        | `StorageAccountA-internetrouting-secondary.web.core.windows.net`   |

Připojovací řetězce pro koncové body, které jsou specifické pro trasu, se dají kopírovat prostřednictvím [Azure Portal](https://portal.azure.com). Tyto připojovací řetězce lze použít pro autorizaci pomocí sdíleného klíče se všemi existujícími Azure Storage SDK a rozhraními API.

## <a name="about-the-preview"></a>O verzi Preview

Předvolby směrování pro Azure Storage jsou k dispozici v následujících oblastech:

- USA – střed 
- Střed USA EUAP
- East US 
- USA – východ 2
- USA – východ 2 
- Východní USA 2 EUAP
- Středojižní USA
- USA – středozápad
- USA – západ 
- Západní USA 2 
- Francie – střed 
- Francie – jih 
- Německo – sever 
- Německo – středozápad 
- USA – středosever
- Severní Evropa 
- Norsko – východ 
- Švýcarsko – sever
- Švýcarsko – západ
- Spojené království – jih 
- Spojené království – západ 
- West Europe 
- Spojené arabské emiráty – střed
- Východní Asie 
- Southeast Asia 
- Japonsko – východ 
- Japonsko – západ 
- Západní Indie
- Austrálie – východ 
- Austrálie – jihovýchod 

Následující známé problémy mají vliv na verzi Preview předvolby směrování pro Azure Storage:

- Žádosti o přístup ke koncovému bodu specifického pro trasu pro globální síť Microsoft se nezdaří s chybou HTTP 404 nebo ekvivalentní. Směrování přes globální síť Microsoft funguje podle očekávání, pokud je nastavená jako výchozí priorita směrování pro veřejný koncový bod.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Podrobnosti o cenách a fakturaci najdete v části s **cenami** v tématu [co je to preference směrování (Preview)?](../../virtual-network/routing-preference-overview.md#pricing).

## <a name="next-steps"></a>Další kroky

- [Co je předvolby směrování (Preview)?](../../virtual-network/routing-preference-overview.md)
- [Konfigurace bran firewall Azure Storage a virtuálních sítí](storage-network-security.md)
- [Doporučení zabezpečení pro úložiště objektů BLOB](../blobs/security-recommendations.md)
