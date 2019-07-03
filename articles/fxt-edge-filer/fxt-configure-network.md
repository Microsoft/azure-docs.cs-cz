---
title: Upravit nastavení sítě pro Microsoft Azure FXT hrany Filer clusteru
description: Jak přizpůsobit nastavení sítě po vytvoření clusteru Azure FXT hrany vyfiltrovat
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 36ed354304cb1c88e48088f4b36c1ad0350af0dc
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543020"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Kurz: Konfigurace nastavení sítě clusteru 

Před použitím nově vytvořený cluster Azure FXT hrany Filer by měl zkontrolovat a upravit několik nastavení sítě pro pracovní postup. 

Tento kurz vysvětluje nastavení sítě, které možná budete muset upravit pro nový cluster. 

Co se dozvíte: 

> [!div class="checklist"]
> * Nastavení sítě, které může být nutné aktualizovat po vytvoření clusteru
> * Které případy použití Azure FXT hrany Filer vyžadují server služby AD nebo serveru DNS 
> * Jak nakonfigurovat kruhové dotazování DNS (RRDNS) automaticky načíst žádostí vyvažovat mezi klienta FXT clusteru

Čas potřebný k dokončení těchto kroků závisí na tom, kolik změny v konfiguraci jsou potřeba ve vašem systému:

* Pokud potřebujete pouze ke čtení v průběhu kurzu a zkontrolovat několik nastavení, by měla trvat 10 až 15 minut. 
* Pokud je potřeba nakonfigurovat kruhové dotazování DNS, tato úloha může trvat hodinu i déle.

## <a name="adjust-network-settings"></a>Upravit nastavení sítě

Několik úlohy související se sítí jsou součástí nastavování nového clusteru Azure FXT hrany vyfiltrovat. Najdete v tomto seznamu a rozhodnout, které platí pro váš systém.

Další informace o nastavení sítě pro cluster, [konfiguraci síťové služby](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) v Průvodci konfigurace clusteru.

* Konfigurace kruhové dotazování DNS v síti klienta (volitelné)

  Vyrovnávat zatížení clusteru provozu podle konfigurace systému DNS, jak je popsáno v [konfigurace služby DNS pro cluster FXT hrany Filer](#configure-dns-for-load-balancing).

* Ověřte nastavení NTP

* Konfigurace služby Active Directory a soubory ke stažení název uživatelského jména nebo skupiny (v případě potřeby)

  Pokud hostitele v místní síti pomocí služby Active Directory nebo jiný typ externí adresářové služby, je třeba upravit konfigurace clusteru directory services nastavit jak clusteru soubory ke stažení informací o uživatelské jméno a skupinách. Čtení **clusteru** > **Directory Services** v Průvodci konfigurace clusteru podrobnosti.

  Server služby AD je vyžadována, pokud chcete, aby podpora protokolu SMB. Konfigurace služby AD před zahájením nastavení protokolu SMB.

* Definování sítě VLAN (volitelné)
  
  Nakonfigurujte všechny další virtuální místní sítě potřeba před definováním vservers a globální obor názvů vašeho clusteru. Čtení [práce se sítěmi VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) v Průvodci konfigurace clusteru Další informace.

* Konfigurace proxy serverů (v případě potřeby)

  Pokud váš cluster používá proxy server k dosažení externí adresy, nastavte ho pomocí těchto kroků:

  1. Definování proxy serveru v **konfiguraci proxy serveru** nastavení stránky
  1. Použít konfiguraci proxy serveru se **clusteru** > **obecné nastavení** stránky nebo **Core Filer podrobnosti** stránky.
  
  Další informace najdete v článku [pomocí webových proxy serverů](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) v Průvodci konfigurace clusteru.

* Nahrát [šifrovací certifikáty](#encryption-certificates) pro cluster používat (volitelné)

### <a name="encryption-certificates"></a>Certifikáty šifrování

Cluster FXT hrany Filer používá certifikátů X.509 pro tyto funkce:

* K šifrování přenosů na clusteru správy

* Chcete-li ověřit jménem klienta na servery třetích stran KMIP

* Pro ověřování certifikátu serveru poskytovatelů cloudu

Pokud potřebujete nahrát certifikáty do clusteru, použijte **clusteru** > **certifikáty** stránku nastavení. Podrobnosti najdete v [clusteru > Certifikáty](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) stránky Průvodce konfigurací clusteru.

Chcete-li šifrovat komunikaci v rámci správy clusteru, použijte **clusteru** > **obecné nastavení** stránku nastavení a vyberte který certifikát se má použít pro protokol SSL pro správu.

> [!Note] 
> Cloudové služby přístupové klíče jsou uložené pomocí **přihlašovací údaje ke cloudu** stránka konfigurace. [Přidat filtr core](fxt-add-storage.md#add-a-core-filer) části výše ukazuje příklad, přečtěte si Průvodce konfigurací clusteru [přihlašovací údaje ke cloudu](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) podrobné informace. 

## <a name="configure-dns-for-load-balancing"></a>Konfigurace DNS pro vyrovnávání zatížení.

Tato část vysvětluje základy konfigurace systému DNS (RRDNS) kruhové dotazování distribuovat klienta zatížení mezi všechny IP adresy klienta ve vašem clusteru Filer FXT hrany. 

### <a name="decide-whether-or-not-to-use-dns"></a>Rozhodněte, jestli se mají použít DNS

Vyrovnávání zatížení se vždy doporučuje, ale není nutné vždy použít DNS. Například u některých typů pracovní postupy klienta může mít více smysl použít skript k přiřazování IP adres clusteru rovnoměrně mezi klienty, když se připojení clusteru. Některé metody jsou popsané v [připojení clusteru](fxt-mount-clients.md). 

Ponechte tyto věci v úvahu při rozhodování o tom, jestli se mají použít DNS server: 

* Pokud váš systém přistupuje pouze klienti NFS, DNS se nevyžaduje. Je možné určit všechny síťové adresy pomocí číselného IP adres. 

* Pokud váš systém podporuje přístup k protokolu SMB (CIFS), se vyžaduje, DNS, protože je nutné zadat doménu DNS pro server služby Active Directory.

* Pokud chcete používat ověřování protokolem Kerberos se vyžaduje DNS.

### <a name="round-robin-dns-configuration-details"></a>Podrobnosti o konfiguraci kruhové dotazování DNS

Když klienti přístup ke clusteru, RRDNS automaticky vyrovnává jejich požadavky mezi všechny dostupné rozhraní.

Pro zajištění optimálního výkonu konfigurace serveru DNS pro zpracování klienta adresy clusteru, jak je znázorněno v následujícím diagramu.

Vserver clusteru se zobrazí na levé straně a IP adresy se zobrazí v centru a na pravé straně. Konfigurovat každý klientský přístupový bod a záznamy a ukazatele, jak je znázorněno.

![Diagram kruhové dotazování DNS clusteru - podrobné alternativní text odkazu následuje image](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
[podrobný popis textu](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html)

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

Následující ``nsupdate`` příkazy uveďte příklad konfigurace DNS správně:

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

### <a name="enable-dns-in-the-cluster"></a>Povolit DNS v clusteru 

Určení serveru DNS, využívající v clusteru **clusteru** > **pro správu sítě** stránku nastavení. Nastavení na této stránce:

* Adresa serveru DNS
* Název domény DNS
* DNS search domains

Další podrobnosti najdete v článku [nastavení DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) v Průvodci konfigurace clusteru.

## <a name="next-steps"></a>Další postup

Toto je poslední krok základní konfigurace pro cluster Azure FXT hrany vyfiltrovat. 

* Další informace o systému LED a další indikátory v [monitorovat stav hardwaru](fxt-monitor.md).
* Další informace o jak by klienti připojit Filer FXT hrany v clusteru [připojení clusteru](fxt-mount-clients.md). 
* Další informace o provozování a správě clusteru FXT hrany vyfiltrovat, najdete v článku [Průvodce konfigurací clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html). 