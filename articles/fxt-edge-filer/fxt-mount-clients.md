---
title: Připojení klientů v Microsoft Azure FXT hrany Filer clusteru
description: Jak připojit systému souborů NFS klientské počítače mezipaměť Azure FXT hrany Filer hybridní úložiště
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: b48d8d74843947c3e40dc80234560b0147be6eea
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542968"
---
# <a name="tutorial-mount-the-cluster"></a>Kurz: Připojení ke clusteru

Tento kurz vás naučí, jak připojit klienti NFS ke clusteru Azure FXT hrany vyfiltrovat. Klienti připojení oboru názvů virtuální cesty, které jste přiřadili při přidání back endové úložné. 

V tomto kurzu se naučíte: 

> [!div class="checklist"]
> * Strategie pro klienty Vyrovnávání zatížení napříč rozsah IP adres klienta
> * Jak vytvořit cestu připojení z klienta IP adres a oborem názvů spojení
> * Které argumenty použít v příkazu připojení

Tento kurz trvá přibližně 45 minut.

## <a name="steps-to-mount-the-cluster"></a>Postup připojení ke clusteru

Postupujte podle těchto kroků se připojíte ke svému clusteru Azure FXT hrany Filer klientské počítače.

1. Rozhodněte, jak vyrovnávat zatížení klientského provozu mezi uzly clusteru. Čtení [Vyrovnávání zatížení klienta](#balance-client-load)níže, pro podrobnosti. 
1. Identifikujte clusteru IP adresu a spojení cestu pro připojení.
1. Určete cestu klienta pro připojení.
1. Problém [příkaz připojení](#use-recommended-mount-command-options), s příslušnými argumenty.

## <a name="balance-client-load"></a>Vyrovnání zatížení klienta

Abychom požadavky klientů rovnováhu mezi všechny uzly v clusteru, by měl připojení klientů k celému rozsahu IP adresy klienta. Existuje několik způsobů, jak tento úkol automatizovat.

Další informace o clusteru pro vyrovnávání zatížení s kruhovým DNS, přečtěte si [konfigurace služby DNS pro cluster Azure FXT hrany Filer](fxt-configure-network.md#configure-dns-for-load-balancing). Chcete-li použít tuto metodu, musíte mít server DNS, které nejsou vysvětlené v těchto článcích.

Jednodušší metodu pro malé instalace je použití skriptu k přiřazování IP adres v celém rozsahu v době připojení klienta. 

Jiné metody vyrovnávání zatížení může být vhodné pro velký nebo komplikovaný systémy. Vyhledejte informace od zástupce Microsoftu nebo žádost o podporu o pomoc. (Nástroj azure Load Balancer je aktuálně *nepodporuje* s Azure FXT hrany vyfiltrovat.)

## <a name="create-the-mount-command"></a>Vytvořit příkaz připojení 

Z klienta ``mount`` příkazu mapování virtuálního serveru (vserver) v clusteru Azure FXT hrany vyfiltrovat na cestu v místním systému souborů. 

Formát je ``mount <FXT cluster path> <local path> {options}``

Existují tři elementy pro tento příkaz: 

* Cesta clusteru - kombinaci IP adresy a obor názvů spojovací cesty je popsáno níže
* místní cesta - cesta na straně klienta 
* příkaz Možnosti - připojení (uvedené v [doporučuje použít možnosti příkazu připojení](#use-recommended-mount-command-options))

### <a name="create-the-cluster-path"></a>Vytvoření clusteru cesty

Cesta clusteru je kombinací vserver *IP adresu* plus cestu k *obor názvů spojení*. Obor názvů spojení je, že je definováno, pokud virtuální cesta je [přidali úložný systém](fxt-add-storage.md#create-a-junction).

Například, pokud jste použili ``/fxt/files`` jako vaší cestě k oboru názvů, vaši klienti připojují *IP_adresa*: / fxt/soubory do jejich místní přípojného bodu. 

!["Přidání nové spojení" dialogové okno s/avere/soubory do pole Cesta oboru názvů](media/fxt-mount/fxt-junction-example.png)

IP adresa je jedním z IP adresy klienta, definována pro vserver. Můžete najít rozsah určených pro klientské IP adresy na dvou místech v Ovládacích panelech clusteru:

* **VServers** tabulku (kartu řídicí panel) – 

  ![Karta řídicí panel ovládacího panelu na kartě VServer vybrané v následující tabulce dat grafu a oddíl IP adresy v kruhu](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* **Síť směřující klienta** stránka nastavení - 

  ![Nastavení > VServer > Stránka Konfigurace síť směřující klienta v kroužku část tabulky pro konkrétní vserver rozsah adres](media/fxt-mount/fxt-ip-addresses-settings.png)

Kombinace IP adresy a cestě k oboru názvů k vytvoření clusteru cesty pro příkaz připojení. 

Příklad připojení klienta: ``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>Vytvořit místní cesta

Místní cesta pro tento příkaz je jenom na vás. Můžete nastavit strukturu všechny cesty, které chcete v rámci oboru názvů virtuální. Obor názvů a místní cestu, která je vhodné pro klienta pracovní postup návrhu. 

Další informace o oborech názvů klienta, přečtěte si Průvodce konfigurací clusteru [přehledu oboru názvů](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html).

Kromě cesty, zahrnout [příkaz Možnosti připojení](#use-recommended-mount-command-options) je popsáno níže, když připojení každého klienta.

### <a name="use-recommended-mount-command-options"></a>Použijte příkaz Možnosti doporučené připojení

Zajistit bezproblémové klientských připojení předání těchto nastavení a argumenty v příkazu připojení: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Požadovaná nastavení | |
--- | --- 
``hard`` | Dočasná připojení ke clusteru Azure FXT hrany Filer jsou přidruženy k selhání aplikace a ke ztrátě. 
``proto=netid`` | Tato možnost podporuje vhodné řešení chyb sítě systému souborů NFS.
``mountproto=netid`` | Tato možnost podporuje vhodné řešení chyb sítě pro operace připojení.
``retry=n`` | Nastavte ``retry=30`` aby se zabránilo selhání přechodné připojení. (Jiné hodnoty se doporučuje v popředí připojení).

| Nastavení  | |
--- | --- 
``nointr``            | Pokud vaši klienti používat starší jádra operačního systému (před duben 2008), které podporují tuto možnost, použijte ji. Výchozí hodnota je možnost "intr".

## <a name="next-steps"></a>Další postup

Po připojení klientů můžete otestovat pracovního postupu a začít pracovat s vaším clusterem.

Pokud potřebujete přesunout data do nové filer core cloudu, využijte výhod struktury mezipaměti pomocí paralelních dat ingestování. Několik strategií, které jsou popsány v [přesun dat do clusteru vFXT](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-data-ingest). (Avere vFXT pro Azure je produkt založené na cloudu, který využívá technologií ukládání do mezipaměti velmi podobný Azure FXT hrany vyfiltrovat.)

Čtení [stav hardwaru monitorování Azure FXT hrany Filer](fxt-monitor.md) Pokud potřebujete řešit jakékoli problémy s hardwarem. 
