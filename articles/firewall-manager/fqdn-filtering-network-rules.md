---
title: Filtrování Azure Firewallového správce v pravidlech sítě (Preview)
description: Jak používat filtrování plně kvalifikovaného názvu domény v síťových pravidlech
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 28cd26532ca5bdf83902854b7910f7d6c18a4eab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87460146"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>Filtrování plně kvalifikovaného názvu domény v pravidlech sítě (Preview)

> [!IMPORTANT]
> Filtrování plně kvalifikovaného názvu domény v síťových pravidlech je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Plně kvalifikovaný název domény (FQDN) představuje název domény hostitele nebo IP adresy. V závislosti na rozlišení DNS v Azure Firewall a zásadách brány firewall můžete použít plně kvalifikované názvy domén v pravidlech sítě. Tato funkce umožňuje filtrovat odchozí přenosy s jakýmkoli protokolem TCP/UDP (včetně NTP, SSH, RDP a dalších). Aby bylo možné ve svých síťových pravidlech používat plně kvalifikované názvy domény, musíte povolit proxy serveru DNS. Další informace najdete v tématu [nastavení DNS zásad Azure firewall (Preview)](dns-settings.md).

## <a name="how-it-works"></a>Jak to funguje

Jakmile určíte, který server DNS vaše organizace potřebuje (Azure DNS nebo vlastní DNS), Azure Firewall přeloží plně kvalifikovaný název domény na IP adresu na základě vybraného serveru DNS. K tomuto překladu dochází pro zpracování pravidel aplikace i sítě.

Jaký je rozdíl mezi použitím názvů domén v pravidlech aplikací v porovnání se sítí pravidel sítě? 

- Filtrování plně kvalifikovaného názvu domény v pravidlech aplikací pro HTTP/S a MSSQL je založené na transparentní proxy úrovni aplikace a v hlavičce SNI. V takovém případě se může nerozlišuje mezi dvěma plně kvalifikovanými názvy domén, které jsou přeloženy na stejnou IP adresu. Nejedná se o případ s filtrováním plně kvalifikovaného názvu domény v síťových pravidlech. Vždy používejte pravidla aplikace, pokud je to možné.
- V pravidlech aplikací můžete jako vybrané protokoly použít HTTP/S a MSSQL. V síťových pravidlech můžete pro své cílové plně kvalifikované názvy domény použít libovolný protokol TCP/UDP.

## <a name="next-steps"></a>Další kroky

[Azure Firewall nastavení DNS](dns-settings.md)
