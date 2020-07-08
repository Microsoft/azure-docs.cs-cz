---
title: Azure Firewall filtrování plně kvalifikovaného názvu domény v pravidlech sítě (Preview)
description: Jak používat filtrování plně kvalifikovaného názvu domény v síťových pravidlech
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 46d1f2eae1342901a660f99891d1ac1529e8ad77
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568596"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>Filtrování plně kvalifikovaného názvu domény v pravidlech sítě (Preview)

> [!IMPORTANT]
> Filtrování plně kvalifikovaného názvu domény v síťových pravidlech je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Plně kvalifikovaný název domény (FQDN) představuje název domény hostitele. Název domény je přidružený k jedné nebo více IP adresám. V pravidlech aplikací můžete povolení nebo blokování plně kvalifikovaných názvů domény a značek plně kvalifikovaného názvu domény. Pomocí vlastního nastavení DNS a DNS proxy můžete také použít filtrování plně kvalifikovaného názvu domény v síťových pravidlech.

## <a name="how-it-works"></a>Jak to funguje

Azure Firewall přeloží plně kvalifikovaný název domény na IP adresu pomocí nastavení DNS a zpracovává pravidlo na základě Azure DNS nebo vlastní konfigurace DNS.

Pokud chcete v pravidlech sítě použít plně kvalifikované názvy domény, měli byste povolit proxy DNS. Pokud proxy server DNS nepovolíte, je zpracování spolehlivého pravidla ohrožené. Když je tato možnost povolená, směruje se provoz DNS na Azure Firewall, kde můžete nakonfigurovat vlastní server DNS. Pak brána firewall a klienti používají stejný nakonfigurovaný server DNS. Pokud proxy server DNS není povolený, Azure Firewall může vytvořit jinou odpověď, protože klient a brána firewall můžou k překladu názvů použít různé servery. Filtrování plně kvalifikovaného názvu domény v síťových pravidlech může být chybné nebo nekonzistentní, pokud klient a brána firewall obdrží jiné odpovědi DNS.

## <a name="next-steps"></a>Další kroky

[Azure Firewall nastavení DNS](dns-settings.md)
