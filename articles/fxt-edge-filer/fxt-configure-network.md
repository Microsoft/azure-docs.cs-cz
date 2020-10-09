---
title: 'Kurz: konfigurace sítě v clusteru Azure FXT Edge souborového'
description: Přizpůsobení nastavení sítě po vytvoření clusteru Azure FXT Edge souborového
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 9b0154889544e0054e309cc5f43851b73b4396b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80754692"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Kurz: Konfigurace nastavení sítě v clusteru

Předtím, než použijete nově vytvořený cluster Azure FXT Edge souborového, byste měli kontrolovat a upravovat několik nastavení sítě pro váš pracovní postup. 

Tento kurz vysvětluje nastavení sítě, které může být potřeba upravit pro nový cluster. 

Co se dozvíte: 

> [!div class="checklist"]
> * Která síťová nastavení může být potřeba aktualizovat po vytvoření clusteru
> * Které případy použití Azure FXT Edge souborového vyžadují server AD nebo server DNS 
> * Postup konfigurace DNS (RRDNS) pro kruhové dotazování pro automatické vyrovnávání požadavků klientů na cluster FXT

Doba potřebná k provedení těchto kroků závisí na tom, kolik změn konfigurace je v systému potřeba:

* Pokud potřebujete přečíst kurz jenom v tomto kurzu a ověřit si několik nastavení, mělo by to trvat 10 až 15 minut. 
* Pokud potřebujete nakonfigurovat DNS pro kruhové dotazování, může tato úloha trvat hodinu nebo déle.

## <a name="adjust-network-settings"></a>Upravit nastavení sítě

Několik úkolů souvisejících se sítí je součástí nastavení nového clusteru Azure FXT Edge souborového. Podívejte se na tento seznam a rozhodněte, která z nich se vztahují na váš systém.

Pokud chcete získat další informace o nastavení sítě pro cluster, přečtěte si téma [konfigurace síťových služeb](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) v Průvodci konfigurací clusteru.

* Konfigurace DNS kruhového dotazování pro síť s klientským přístupem (volitelné)

  Vyrovnávání zatížení clusteru pomocí konfigurace systému DNS, jak je popsáno v tématu [Konfigurace DNS pro cluster FXT Edge souborového](#configure-dns-for-load-balancing).

* Ověření nastavení NTP

* Konfigurace služby Active Directory a jména uživatele nebo názvu skupiny ke stažení (v případě potřeby)

  Pokud vaše síť používá službu Active Directory nebo jiný druh externí adresářové služby, musíte upravit konfiguraci adresářových služeb clusteru a nastavit, jak cluster stáhne informace o uživatelském jménu a skupině. **Cluster**  >  Podrobnosti najdete v Průvodci konfigurací clusterových**služeb** v clusteru.

  Pokud požadujete podporu protokolu SMB, je nutné zadat server služby AD. Před zahájením instalace protokolu SMB nakonfigurujte AD.

* Definování sítí VLAN (volitelné)
  
  Před definováním vservers a globálního oboru názvů clusteru nakonfigurujte další potřebné sítě VLAN. Další informace najdete v tématu [práce s sítěmi VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) v Průvodci konfigurací clusteru.

* Konfigurace proxy serverů (Pokud je potřeba)

  Pokud váš cluster používá proxy server k dosažení externích adres, postupujte podle těchto kroků a nastavte:

  1. Definování proxy server na stránce nastavení **konfigurace proxy serveru**
  1. Použijte konfiguraci proxy server na **Cluster**  >  stránce**Obecné nastavení** clusteru nebo na základní stránce s **podrobnostmi souborového** .
  
  Další informace najdete v tématu [použití webových proxy serverů](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) v Průvodci konfigurací clusteru.

* Nahrání [šifrovacích certifikátů](#encryption-certificates) , které má cluster používat (volitelné)

### <a name="encryption-certificates"></a>Certifikáty šifrování

Cluster FXT Edge souborového používá certifikáty X. 509 pro tyto funkce:

* Šifrování provozu správy clusteru

* Ověřování jménem klienta na servery KMIP třetích stran

* Ověření certifikátů serveru poskytovatelé cloudu

Pokud potřebujete do clusteru nahrávat certifikáty, použijte **Cluster**  >  stránku nastavení**certifikátů** clusteru. Podrobnosti najdete na stránce [> certifikáty clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) v Průvodci konfigurací clusteru.

Chcete-li zašifrovat komunikaci správy clusterů, použijte **Cluster**  >  stránku**Obecné** nastavení clusteru a vyberte certifikát, který chcete použít pro správu TLS.

> [!Note] 
> Přístupové klíče cloudové služby se ukládají pomocí stránky konfigurace **přihlašovacích údajů cloudu** . Příklad najdete v části [Add a Core souborového](fxt-add-storage.md#add-a-core-filer) . Podrobnosti najdete v části Průvodce konfigurací clusteru [Cloud přihlašovací údaje](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) . 

## <a name="configure-dns-for-load-balancing"></a>Konfigurace DNS pro vyrovnávání zatížení

V této části jsou vysvětleny základy konfigurace systému DNS (RRDNS) s kruhovým dotazováním, které distribuují zatížení klienta mezi všemi IP adresami klientů v clusteru souborového FXT Edge. 

### <a name="decide-whether-or-not-to-use-dns"></a>Rozhodnutí, jestli se má používat DNS

Vyrovnávání zatížení se vždycky doporučuje, ale nemusíte vždycky používat DNS. Například u některých typů klientských pracovních postupů může být vhodnější použít skript k přiřazování IP adres clusteru rovnoměrně mezi klienty při připojení clusteru. Některé metody jsou popsány v tématu [připojení clusteru](fxt-mount-clients.md). 

Při rozhodování, jestli chcete používat server DNS, pamatujte na tyto věci: 

* Pokud k systému přistupovali pouze klienti systému souborů NFS, není DNS nutné. Je možné zadat všechny síťové adresy pomocí číselných IP adres. 

* Pokud váš systém podporuje přístup přes protokol SMB (CIFS), vyžaduje se DNS, protože musíte zadat doménu DNS pro server služby Active Directory.

* Služba DNS je vyžadována, pokud chcete použít ověřování pomocí protokolu Kerberos.

### <a name="round-robin-dns-configuration-details"></a>Podrobnosti konfigurace DNS s kruhovým dotazováním

Když klienti přistupují ke clusteru, RRDNS automaticky vyrovnává požadavky mezi všemi dostupnými rozhraními.

Pro zajištění optimálního výkonu nakonfigurujte server DNS tak, aby zpracovávala adresy clusterů, jak je znázorněno v následujícím diagramu.

Na levé straně se zobrazí cluster VServer a IP adresy se zobrazí v centru a na pravé straně. Nakonfigurujte všechny klientské přístupové body pomocí záznamů a ukazatelů, jak je znázorněno.

![Diagram DNS pro kruhové dotazování v clusteru – podrobný odkaz na text následuje obrázek ](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
 [podrobný popis textu](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html)

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

### <a name="enable-dns-in-the-cluster"></a>Povolit DNS v clusteru 

Zadejte server DNS, který cluster používá, na **Cluster**  >  stránce nastavení**sítě pro správu** clusteru. Nastavení na této stránce zahrnují:

* Adresa serveru DNS
* Název domény DNS
* Domény hledání DNS

Další podrobnosti najdete v tématu [nastavení DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) v Průvodci konfigurací clusteru.

## <a name="next-steps"></a>Další kroky

Toto je poslední krok základní konfigurace pro cluster Azure FXT Edge souborového. 

* Přečtěte si o indikátorech LED a dalších indikátorech systému v [monitorování stavu hardwaru](fxt-monitor.md).
* Přečtěte si další informace o tom, jak by klienti měli připojit cluster FXT Edge souborového v tématu [připojení clusteru](fxt-mount-clients.md). 
* Další informace o tom, jak provozovat a spravovat cluster FXT Edge souborového, najdete v [Průvodci konfigurací clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html). 