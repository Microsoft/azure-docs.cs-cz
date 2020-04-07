---
title: 'Kurz: Konfigurace sítě v clusteru Azure FXT Edge Filer'
description: Jak přizpůsobit nastavení sítě po vytvoření clusteru Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 9b0154889544e0054e309cc5f43851b73b4396b4
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754692"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Kurz: Konfigurace nastavení sítě clusteru

Před použitím nově vytvořeného clusteru Azure FXT Edge Filer byste měli zkontrolovat a přizpůsobit několik síťových nastavení pro váš pracovní postup. 

Tento kurz vysvětluje nastavení sítě, které může být nutné upravit pro nový cluster. 

Co se dozvíte: 

> [!div class="checklist"]
> * Nastavení sítě může být nutné aktualizovat po vytvoření clusteru
> * Které případy použití Azure FXT Edge Filer vyžadují server AD nebo DNS 
> * Jak nakonfigurovat round-robin DNS (RRDNS) pro automatické vyrovnávání zatížení klientských požadavků clusteru FXT

Doba potřebná k provedení těchto kroků závisí na tom, kolik změn konfigurace je v systému potřeba:

* Pokud potřebujete pouze přečíst výukový program a zkontrolovat několik nastavení, mělo by to trvat 10 až 15 minut. 
* Pokud potřebujete nakonfigurovat službu DNS kruhového dotazování, může tato úloha trvat hodinu nebo déle.

## <a name="adjust-network-settings"></a>Úprava nastavení sítě

Několik úloh souvisejících se sítí je součástí nastavení nového clusteru Azure FXT Edge Filer. Podívejte se do tohoto seznamu a rozhodněte se, které z nich se vztahují na váš systém.

Další informace o nastavení sítě pro cluster najdete v [části Konfigurace síťových služeb](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) v Průvodci konfigurací clusteru.

* Konfigurace round-robin DNS pro klientskou síť (volitelně)

  Provoz clusteru vyrovnávání zatížení konfigurací systému DNS, jak je popsáno v [části Konfigurace DNS pro cluster FXT Edge Filer](#configure-dns-for-load-balancing).

* Ověření nastavení protokolu NTP

* Konfigurace služby Active Directory a stahování názvu uživatelského jména/skupiny (v případě potřeby)

  Pokud hostitelé sítě používají službu Active Directory nebo jiný druh externí adresářové služby, je nutné upravit konfiguraci adresářových služeb clusteru a nastavit způsob stahování informací o uživatelském_ a skupinovém připojení clusteru. Podrobnosti naleznete**v** Průvodci konfigurací clusteru v průvodci clusterem. **Cluster** > 

  Pokud chcete podporu SMB, je vyžadován server Služby AD. Před zahájením nastavování protokolu SMB nakonfigurujte službu AD.

* Definovat vlan (volitelné)
  
  Před definováním serverů vclusteru a globálního oboru názvů nakonfigurujte všechny další sítě VLAN, které jsou potřeba. Další informace najdete v [části Práce s sítěmi VLAN](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) v Průvodci konfigurací clusteru.

* Konfigurace proxy serverů (v případě potřeby)

  Pokud váš cluster používá k oslovení externích adres proxy server, nastavte ho takto:

  1. Definování proxy serveru na stránce **Nastavení konfigurace proxy serveru**
  1. Použijte konfiguraci proxy serveru se stránkou**Obecné nastavení** **clusteru** > nebo se stránkou **Podrobnosti základního souborového souboru.**
  
  Další informace naleznete [v části Použití webových proxy serverů](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) v Průvodci konfigurací clusteru.

* Nahrání [šifrovacích certifikátů](#encryption-certificates) pro použití clusteru (volitelné)

### <a name="encryption-certificates"></a>Certifikáty šifrování

Cluster FXT Edge Filer používá certifikáty X.509 pro tyto funkce:

* Šifrování přenosů správy clusteru

* Ověření jménem klienta na serverech KMIP jiných výrobců

* Pro ověření certifikátů serverů poskytovatelů cloudu

Pokud potřebujete odeslat certifikáty do clusteru, použijte stránku Nastavení**certifikátů** **clusteru.** >  Podrobnosti jsou na stránce [Certifikáty clusteru >](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) průvodce konfigurací clusteru.

Chcete-li šifrovat komunikaci se správou clusteru, vyberte pomocí stránky**Nastavení obecného nastavení** **clusteru,** > který certifikát se má použít pro správce protokolu TLS.

> [!Note] 
> Přístupové klíče cloudové služby jsou uloženy pomocí konfigurační stránky **Cloud Credentials.** Výše uvedená část [Přidat základní filer](fxt-add-storage.md#add-a-core-filer) ukazuje příklad; Podrobnosti naleznete v části [Cloud pověření cloudového průvodce](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) konfigurací clusteru. 

## <a name="configure-dns-for-load-balancing"></a>Konfigurace služby DNS pro vyrovnávání zatížení

Tato část vysvětluje základy konfigurace systému RRDNS (ROUND-robin DNS) pro distribuci zatížení klienta mezi všechny ip adresy orientované na klienta v clusteru FXT Edge Filer. 

### <a name="decide-whether-or-not-to-use-dns"></a>Rozhodněte se, zda použít službu DNS

Vyrovnávání zatížení se vždy doporučuje, ale nemusíte vždy používat DNS. U některých typů klientských pracovních postupů může být například rozumnější použít skript k rovnoměrnému přiřazení ip adres clusteru mezi klienty při připojení clusteru. Některé metody jsou popsány v [připojení clusteru](fxt-mount-clients.md). 

Při rozhodování, zda použít server DNS, mějte na paměti tyto věci: 

* Pokud je váš systém přístupný pouze klientům systému souborů NFS, služba DNS není vyžadována. Je možné zadat všechny síťové adresy pomocí číselných IP adres. 

* Pokud váš systém podporuje přístup SMB (CIFS), je vyžadována služba DNS, protože je nutné zadat doménu DNS pro server služby Active Directory.

* Dns je vyžadováno, pokud chcete použít ověřování protokolem Kerberos.

### <a name="round-robin-dns-configuration-details"></a>Podrobnosti konfigurace DNS kruhového dotazování

Když klienti přistupují ke clusteru, RRDNS automaticky vyvažuje své požadavky mezi všechna dostupná rozhraní.

Chcete-li dosáhnout optimálního výkonu, nakonfigurujte server DNS tak, aby zpracovával adresy clusterů orientované na klienta, jak je znázorněno na následujícím diagramu.

Vlevo je zobrazen clusterový vserver a adresy IP se zobrazují uprostřed a vpravo. Nakonfigurujte každý klientský přístupový bod pomocí záznamů A a ukazatelů, jak je znázorněno na obrázku.

![Cluster round-robin DNS diagram - podrobný](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
alternativní textový odkaz následuje[obrázek podrobný textový popis](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html)

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

### <a name="enable-dns-in-the-cluster"></a>Povolení služby DNS v clusteru 

Zadejte server DNS, který cluster používá na stránce Nastavení**sítě pro správu** **clusteru.** >  Nastavení na této stránce zahrnují:

* Adresa serveru DNS
* Název domény DNS
* Vyhledávací domény DNS

Další podrobnosti najdete v [průvodci konfigurací](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) clusteru v části Nastavení DNS.

## <a name="next-steps"></a>Další kroky

Toto je poslední základní krok konfigurace pro cluster Azure FXT Edge Filer. 

* Informace o LED diodách systému a dalších indikátorech naleznete ve [stavu hardwaru monitoru](fxt-monitor.md).
* Přečtěte si další informace o tom, jak by měli klienti připojit cluster FXT Edge Filer v [připojení clusteru](fxt-mount-clients.md). 
* Další informace o provozu a správě clusteru FXT Edge Filer naleznete v [Průvodci konfigurací clusteru](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html). 