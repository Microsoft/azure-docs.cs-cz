---
title: Avere vFXT DNS – Azure
description: Konfigurace serveru DNS pro zatížení s kruhovým pomocí Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 9fd9eaf1e62d063026e0e656346baaaade87064f
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54187140"
---
# <a name="avere-cluster-dns-configuration"></a>Konfigurace DNS clusteru Avere

Tato část vysvětluje základy konfigurace systému DNS pro váš cluster vFXT Avere Vyrovnávání zatížení. 

Tento dokument *nezahrnuje* pokyny pro nastavení a Správa serveru DNS v prostředí Azure. 

Namísto použití kruhové dotazování DNS tak, aby Vyrovnávání zatížení vFXT clusteru v Azure, zvažte použití ruční metody k přiřazování IP adres rovnoměrně mezi klienty, když jsou připojené. Několik metod, které jsou popsány v [připojení clusteru Avere](avere-vfxt-mount-clients.md). 

Ponechte tyto věci v úvahu při rozhodování o tom, jestli se mají použít DNS server: 

* Pokud váš systém přistupuje pouze klienti NFS, pomocí DNS se nevyžaduje – je možné určit všechny síťové adresy pomocí číselného IP adres. 

* Pokud váš systém podporuje přístup k protokolu SMB (CIFS), se vyžaduje, DNS, protože je nutné zadat doménu DNS pro server služby Active Directory.

* Pokud chcete používat ověřování protokolem Kerberos se vyžaduje DNS.

## <a name="load-balancing"></a>Vyrovnávání zatížení

K distribuci celkové zatížení, nakonfigurujte svoje DNS domény pro účely klienta IP adres distribuci zatížení pomocí kruhového dotazování.

## <a name="configuration-details"></a>Podrobnosti konfigurace

Když klienti přístup ke clusteru, RRDNS automaticky vyrovnává jejich požadavky mezi všechny dostupné rozhraní.

Pro zajištění optimálního výkonu konfigurace serveru DNS pro zpracování klienta adresy clusteru, jak je znázorněno v následujícím diagramu.

Vserver clusteru se zobrazí na levé straně a IP adresy se zobrazí v centru a na pravé straně. Konfigurovat každý klientský přístupový bod a záznamy a ukazatele, jak je znázorněno.

![Diagram Avere clusteru kruhové dotazování DNS](media/avere-vfxt-rrdns-diagram.png) 
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

Každou IP adresu klienta musí mít jedinečný název pro interní použití v clusteru. (V tomto diagramu jsou klientských IP adres s názvem vs1 – klient - IP-* pro přehlednost, ale v produkčním prostředí je vhodné použít něco jako klient * stručnější.)

Klienti připojení ke clusteru pomocí názvu vserver jako argument serveru. 

Upravit DNS server ``named.conf`` souboru nastavit cyklické pořadí pro dotazy na vaše vserver. Tato možnost zajišťuje, že všechny dostupné hodnoty jsou cyklicky. Přidejte příkaz podobný tomuto:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

Následující příkazy nsupdate uveďte příklad konfigurace DNS správně:

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

Určení serveru DNS, která používá vFXT clusteru **clusteru** > **pro správu sítě** stránku nastavení. Nastavení na této stránce:

* Adresa serveru DNS
* Název domény DNS
* Domény hledání DNS

Čtení [nastavení DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) v Průvodci konfigurace clusteru Avere další podrobnosti o použití této stránky.


