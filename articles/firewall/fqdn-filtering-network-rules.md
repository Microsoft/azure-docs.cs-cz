---
title: Azure Firewall filtrování plně kvalifikovaného názvu domény v síťových pravidlech
description: Jak používat Azure Firewall filtrování plně kvalifikovaného názvu domény v síťových pravidlech
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: f7196c7715ad5d2c02759040b780b96218e1655e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94695941"
---
# <a name="use-fqdn-filtering-in-network-rules"></a>Použít filtrování plně kvalifikovaného názvu domény v síťových pravidlech

Plně kvalifikovaný název domény (FQDN) představuje název domény hostitele nebo IP adresy. V závislosti na rozlišení DNS v Azure Firewall a zásadách brány firewall můžete použít plně kvalifikované názvy domén v pravidlech sítě. Tato funkce umožňuje filtrovat odchozí přenosy s jakýmkoli protokolem TCP/UDP (včetně NTP, SSH, RDP a dalších). Aby bylo možné ve svých síťových pravidlech používat plně kvalifikované názvy domény, musíte povolit proxy serveru DNS. Další informace najdete v tématu [Azure firewall nastavení DNS](dns-settings.md).

> [!NOTE]
> Filtrování plně kvalifikovaného názvu domény v takovém případě nepodporuje zástupné znaky.

## <a name="how-it-works"></a>Jak to funguje

Jakmile určíte, který server DNS vaše organizace potřebuje (Azure DNS nebo vlastní DNS), Azure Firewall přeloží plně kvalifikovaný název domény na IP adresu na základě vybraného serveru DNS. K tomuto překladu dochází pro zpracování pravidel aplikace i sítě.

Když dojde k novému překladu DNS, přidají se do pravidel brány firewall nové IP adresy. Staré IP adresy, které server DNS již nevrací, vyprší za 15 minut. Pravidla Azure Firewall se aktualizují každých 15 sekund od překladu názvů DNS plně kvalifikovaných názvů domén v síťových pravidlech.

### <a name="differences-in-application-rules-vs-network-rules"></a>Rozdíly v pravidlech aplikací vs. Síťová pravidla

- Filtrování plně kvalifikovaného názvu domény v pravidlech aplikací pro HTTP/S a MSSQL je založené na transparentní proxy úrovni aplikace a v hlavičce SNI. V takovém případě se může nerozlišuje mezi dvěma plně kvalifikovanými názvy domén, které jsou přeloženy na stejnou IP adresu. Nejedná se o případ s filtrováním plně kvalifikovaného názvu domény v síťových pravidlech. 

   Vždy používat pravidla aplikací, pokud je to možné:
     - Pokud je protokol HTTP/S nebo MSSQL, použijte pravidla použití pro filtrování plně kvalifikovaného názvu domény.
   - Pro všechny ostatní protokoly kromě HTTP/S nebo MSSQL můžete použít pravidla použití aplikace nebo sítě pro filtrování plně kvalifikovaného názvu domény.

## <a name="next-steps"></a>Další kroky

[Azure Firewall nastavení DNS](dns-settings.md)
