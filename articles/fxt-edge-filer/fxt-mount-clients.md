---
title: Připojení klientů do clusteru Microsoft Azure FXT Edge Filer
description: Jak mohou klientské počítače nfs připojit mezipaměť hybridního úložiště Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 43223db298e4ad170ea6d0687a342b3aee35500e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80130774"
---
# <a name="tutorial-mount-the-cluster"></a>Kurz: Připojení clusteru

Tento kurz vás naučí, jak připojit klienty systému souborů NFS do clusteru Azure FXT Edge Filer. Klienti připojují cesty virtuálního oboru názvů, které jste přiřadili při přidání back-endového úložiště.

Tento výukový program učí:

> [!div class="checklist"]
> * Strategie pro klienty vyrovnávání zatížení v rozsahu IP adres orientovaných na klienta
> * Jak vytvořit cestu připojení z klienta-čelí IP adresu a název oboru spojení
> * Které argumenty se mají použít v příkazu připojit

Tento kurz trvá přibližně 45 minut.

## <a name="steps-to-mount-the-cluster"></a>Kroky pro připojení clusteru

Podle těchto kroků připojte klientské počítače ke clusteru Azure FXT Edge Filer.

1. Rozhodněte se, jak vyvážit zatížení klientského provozu mezi uzly clusteru. Přečtěte si [zůstatek zatížení klienta](#balance-client-load), níže, podrobnosti.
1. Identifikujte adresu IP clusteru a spojovací cestu k připojení.
1. Určete cestu směřující ke klientovi pro připojení.
1. Vystaví [příkaz mount](#use-recommended-mount-command-options)s příslušnými argumenty.

## <a name="balance-client-load"></a>Vyvažit zatížení klienta

Chcete-li pomoci vyvážit požadavky klientů mezi všechny uzly v clusteru, měli byste připojit klienty k plnému rozsahu adres IP klientů. Existuje několik způsobů, jak tuto úlohu automatizovat.

Informace o kruhovém vyrovnávání zatížení DNS pro cluster najdete v části [Konfigurace DNS pro cluster Azure FXT Edge Filer](fxt-configure-network.md#configure-dns-for-load-balancing). Chcete-li použít tuto metodu, je nutné udržovat server DNS, který není vysvětlen v těchto článcích.

Jednodušší metodou pro malé instalace je použití skriptu pro přiřazení adres IP v celém rozsahu v době připojení klienta.

Jiné metody vyrovnávání zatížení mohou být vhodné pro velké nebo složité systémy. Obraťte se na zástupce společnosti Microsoft nebo otevřete [žádost o pomoc.](fxt-support-ticket.md) (Azure Load Balancer není momentálně *podporované* s Azure FXT Edge Filer.)

## <a name="create-the-mount-command"></a>Vytvoření příkazu připojit

Z vašeho klienta ``mount`` příkaz mapuje virtuální server (vserver) v clusteru Azure FXT Edge Filer na cestu v místním souborovém systému.

Formát je``mount <FXT cluster path> <local path> {options}``

Příkaz mount má tři prvky:

* cesta clusteru - kombinace IP adresy a cesty spojení oboru názvů popsaná níže
* místní cesta - cesta na straně klienta
* možnosti příkazu mount - (uvedeny v [části Použít doporučené možnosti příkazu připojit](#use-recommended-mount-command-options))

### <a name="create-the-cluster-path"></a>Vytvoření cesty k clusteru

Cesta k clusteru je kombinací *adresy IP* vserveru a cesty ke spojení oboru *názvů*. Spojení oboru názvů je virtuální cesta, kterou jste definovali při [přidání úložného systému](fxt-add-storage.md#create-a-junction).

Pokud jste například ``/fxt/files`` použili jako cestu oboru názvů, klienti by *IP_address*:/fxt/files do místního přípojného bodu.

![Dialogové okno Přidat novou křižovatku s parametrem /avere/files v poli cesty oboru názvů](media/fxt-mount/fxt-junction-example.png)

IP adresa je jednou z ip adres určených pro klienta definovaných pro vserver. Rozsah ip adresy orientovaných na klienta najdete na dvou místech v Ovládacích panelech clusteru:

* Tabulka **VServers** (karta Řídicí panel) -

  ![Karta Řídicí panel ovládacího panelu s kartou VServer vybranou v tabulce dat pod grafem a sekce IP adresa byla zakroužkovaná](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* Stránka Nastavení **sítě směřující k klientům** -

  ![Nastavení > konfigurační stránku VServer > klientskou síť s kruhem kolem části Rozsah adres v tabulce pro konkrétní vserver](media/fxt-mount/fxt-ip-addresses-settings.png)

Zkombinujte adresu IP a cestu oboru názvů a vytvořte cestu clusteru pro příkaz připojení.

Příklad příkazu připojení klienta:``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>Vytvoření místní cesty

Místní cesta pro příkaz připojení je na vás. Jako součást virtuálního oboru názvů můžete nastavit libovolnou strukturu cesty. Navrhněte obor názvů a místní cestu, která je vhodná pro pracovní postup klienta.

Další informace o oboru názvů směřujícím ke klientům naleznete v [přehledu oboru oboru oboru názvů](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)Průvodce konfigurací clusteru .

Kromě cest při montáži každého klienta zahrňte níže popsané [možnosti příkazu připojení.](#use-recommended-mount-command-options)

### <a name="use-recommended-mount-command-options"></a>Použití doporučených možností příkazu pro připojení

Chcete-li zajistit bezproblémové připojení klienta, předejte v příkazu připojení tato nastavení a argumenty:

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Požadovaná nastavení | |
--- | ---
``hard`` | Měkké připojení ke clusteru Azure FXT Edge Filer jsou spojeny s selháním aplikací a možnou ztrátou dat.
``proto=netid`` | Tato možnost podporuje vhodné zpracování chyb sítě NFS.
``mountproto=netid`` | Tato možnost podporuje vhodné zpracování chyb sítě pro operace připojení.
``retry=n`` | Nastavte, ``retry=30`` aby se zabránilo přechodným selháním připojení. (V úchytech v popředí se doporučuje jiná hodnota.)

| Upřednostňovaná nastavení  | |
--- | ---
``nointr``            | Pokud vaši klienti používají starší jádra operačního režimu (před dubnem 2008), která tuto možnost podporují, použijte ji. Možnost "intr" je výchozí.

## <a name="next-steps"></a>Další kroky

Po montáži klientů můžete otestovat pracovní postup a začít s clusterem.

Pokud potřebujete přesunout data do nového fileru jádra cloudu, využijte výhod struktury mezipaměti pomocí paralelního příjmu dat. Některé strategie jsou popsány v [přesunutí dat do clusteru vFXT](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-data-ingest). (Avere vFXT pro Azure je cloudový produkt, který používá technologii ukládání do mezipaměti velmi podobnou Azure FXT Edge Filer.)

Přečtěte si [monitor Azure FXT Edge Filer stav hardwaru,](fxt-monitor.md) pokud potřebujete řešit problémy s hardwarem.
