---
title: Avere vFXT DNS – Azure
description: Konfigurace serveru DNS pro vyrovnávání zatížení kruhového dotazování pomocí avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 81b53904f85e2ac936195b1e39d7586fd1d47524
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009020"
---
# <a name="avere-cluster-dns-configuration"></a>Konfigurace DNS clusteru Avere

V této části najdete základní informace o konfiguraci systému DNS pro vyrovnávání zatížení clusteru avere vFXT.

Tento dokument *neobsahuje* pokyny k nastavení a správě serveru DNS v prostředí Azure.

Místo používání služby DNS pro kruhové dotazování pro vyrovnávání zatížení clusteru vFXT v Azure zvažte použití ručních metod k přiřazování IP adres rovnoměrně mezi klienty, když jsou připojené. Několik metod je popsáno v tématu [připojení clusteru avere](avere-vfxt-mount-clients.md).

Při rozhodování, jestli chcete používat server DNS, pamatujte na tyto věci:

* Pokud k systému přistupovali pouze klienti systému souborů NFS, použití DNS není vyžadováno – je možné zadat všechny síťové adresy pomocí číselných IP adres.

* Pokud váš systém podporuje přístup přes protokol SMB (CIFS), vyžaduje se DNS, protože musíte zadat doménu DNS pro server služby Active Directory.

* Služba DNS je vyžadována, pokud chcete použít ověřování pomocí protokolu Kerberos.

## <a name="load-balancing"></a>Vyrovnávání zatížení

Pokud chcete distribuovat celkové zatížení, nakonfigurujte doménu DNS tak, aby používala distribuci zatížení kruhového dotazování pro klientské IP adresy.

## <a name="configuration-details"></a>Podrobnosti konfigurace

Když klienti přistupují ke clusteru, RRDNS automaticky vyrovnává požadavky mezi všemi dostupnými rozhraními.

Pro zajištění optimálního výkonu nakonfigurujte server DNS tak, aby zpracovávala adresy clusterů, jak je znázorněno v následujícím diagramu.

Na levé straně se zobrazí cluster VServer a IP adresy se zobrazí v centru a na pravé straně. Nakonfigurujte všechny klientské přístupové body pomocí záznamů a ukazatelů, jak je znázorněno.

![Diagram DNS pro kruhové dotazování clusteru avere](media/avere-vfxt-rrdns-diagram.png)
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

Každá IP adresa pro klienta musí mít jedinečný název pro interní použití clusterem. (V tomto diagramu se IP adresy klientů nazývají VS1-Client-IP-* pro přehlednost, ale v produkčním prostředí byste pravděpodobně použili něco výstižného, jako je třeba klient *.)

Klienti připojí cluster pomocí názvu VServer jako argumentu serveru.

Upravte soubor DNS serveru ``named.conf`` tak, aby se nastavila cyklická objednávka pro dotazy na vaši VServer. Tato možnost zajistí, že se všechny dostupné hodnoty cyklují cyklicky. Přidejte příkaz podobný následujícímu:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

Následující ``nsupdate`` příkazy poskytují příklad konfigurace služby DNS správně:

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

## <a name="cluster-dns-settings"></a>Nastavení DNS clusteru

Zadejte server DNS, který cluster vFXT používá na **Cluster**  >  stránce nastavení **sítě pro správu** clusteru. Nastavení na této stránce zahrnují:

* Adresa serveru DNS
* Název domény DNS
* Domény hledání DNS

Další podrobnosti o použití této stránky najdete v Průvodci konfigurací clusteru avere v tématu věnovaném [nastavení DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) .
