---
title: Avere vFXT DNS – Azure
description: Konfigurace serveru DNS pro vyrovnávání zatížení s každým dotazováním pomocí Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 81b53904f85e2ac936195b1e39d7586fd1d47524
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153781"
---
# <a name="avere-cluster-dns-configuration"></a>Konfigurace DNS clusteru Avere

Tato část vysvětluje základy konfigurace systému DNS pro vyrovnávání zatížení clusteru Avere vFXT.

Tento dokument *neobsahuje* pokyny pro nastavení a správu serveru DNS v prostředí Azure.

Místo použití round-robin DNS k vyrovnávání zatížení vfxt clusteru v Azure, zvažte použití ruční metody přiřadit IP adresy rovnoměrně mezi klienty při jejich montáži. V [clusteru Mount the Avere](avere-vfxt-mount-clients.md)je popsáno několik metod .

Při rozhodování, zda použít server DNS, mějte na paměti tyto věci:

* Pokud je váš systém přístupný pouze klientům systému souborů NFS, použití DNS není vyžadováno - je možné zadat všechny síťové adresy pomocí číselných IP adres.

* Pokud váš systém podporuje přístup SMB (CIFS), je vyžadována služba DNS, protože je nutné zadat doménu DNS pro server služby Active Directory.

* Dns je vyžadováno, pokud chcete použít ověřování protokolem Kerberos.

## <a name="load-balancing"></a>Vyrovnávání zatížení

Chcete-li distribuovat celkové zatížení, nakonfigurujte doménu DNS tak, aby používala distribuci zatížení kruhového dotazování pro ip adresy orientované na klienta.

## <a name="configuration-details"></a>Podrobnosti o konfiguraci

Když klienti přistupují ke clusteru, RRDNS automaticky vyvažuje své požadavky mezi všechna dostupná rozhraní.

Chcete-li dosáhnout optimálního výkonu, nakonfigurujte server DNS tak, aby zpracovával adresy clusterů orientované na klienta, jak je znázorněno na následujícím diagramu.

Vlevo je zobrazen clusterový vserver a adresy IP se zobrazují uprostřed a vpravo. Nakonfigurujte každý klientský přístupový bod pomocí záznamů A a ukazatelů, jak je znázorněno na obrázku.

![Diagram DNS kruhového dotazování clusteru Avere](media/avere-vfxt-rrdns-diagram.png)
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

Každá adresa IP adresa orientovaná na klienta musí mít jedinečný název pro interní použití clusterem. (V tomto diagramu jsou ip adresy klienta pojmenovány vs1-client-IP-* pro přehlednost, ale v produkčním prostředí byste pravděpodobně měli použít něco stručnějšího, jako je klient*.)

Klienti připojit cluster pomocí názvu vserver jako argument serveru.

Upravte ``named.conf`` soubor serveru DNS tak, aby nastavoval cyklické pořadí dotazů na váš vserver. Tato možnost zajišťuje, že všechny dostupné hodnoty jsou cykinovány. Přidejte příkaz, jako je následující:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

Následující ``nsupdate`` příkazy poskytují příklad správné konfigurace služby DNS:

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

Zadejte server DNS, který cluster vFXT používá na stránce Nastavení**sítě pro správu** **clusteru.** >  Nastavení na této stránce zahrnují:

* Adresa serveru DNS
* Název domény DNS
* Vyhledávací domény DNS

Další podrobnosti o používání této stránky naleznete v průvodci konfigurací služby [DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) v Průvodci konfigurací clusteru Avere.
