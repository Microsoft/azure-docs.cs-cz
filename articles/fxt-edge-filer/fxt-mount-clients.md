---
title: Připojení klientů v clusteru Microsoft Azure FXT Edge souborového
description: Jak můžou klientské počítače NFS připojit mezipaměť hybridního úložiště Azure FXT Edge souborového
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: fa1f9da2c60aaf4c552916d16c266e984bf08892
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92340508"
---
# <a name="tutorial-mount-the-cluster"></a>Kurz: připojení clusteru

V tomto kurzu se naučíte připojit klienty systému souborů NFS ke clusteru Azure FXT Edge souborového. Klienti připojí cesty k virtuálnímu oboru názvů, které jste přiřadili při přidání back-endu úložiště.

V tomto kurzu se naučíme:

> [!div class="checklist"]
>
> * Strategie pro vyrovnávání zatížení klientů v rámci rozsahu IP adres směřujících na klienta
> * Jak vytvořit cestu pro připojení z IP adresy a spojení s oborem názvů směřujícími na klienta
> * Argumenty, které se mají použít v příkazu Mount

Dokončení tohoto kurzu trvá přibližně 45 minut.

## <a name="steps-to-mount-the-cluster"></a>Postup připojení clusteru

Pomocí těchto kroků připojíte klientské počítače k vašemu clusteru Azure FXT Edge souborového.

1. Rozhodněte, jak vyrovnávat zatížení klientských přenosů mezi uzly clusteru. Podrobnosti o [Vyrovnávání zatížení klienta](#balance-client-load)najdete níže.
1. Identifikujte IP adresu clusteru a cestu spojení, která se má připojit.
1. Určete cestu k klientovi pro připojení.
1. Vydejte odpovídající argumenty pomocí [příkazu Mount](#use-recommended-mount-command-options).

## <a name="balance-client-load"></a>Vyvážit zatížení klienta

Aby bylo možné vyrovnávat požadavky klientů mezi všemi uzly v clusteru, měli byste připojit klienty k celé škále IP adres klientů. Existuje několik způsobů, jak tuto úlohu automatizovat.

Pokud se chcete dozvědět víc o vyrovnávání zatížení DNS v kruhovém dotazování, přečtěte si téma [Konfigurace DNS pro cluster Azure FXT Edge souborového](fxt-configure-network.md#configure-dns-for-load-balancing). Chcete-li použít tuto metodu, musíte udržovat server DNS, který není v těchto článcích vysvětlen.

Jednodušší způsob, jak malé instalace, je použít skript k přiřazení IP adres v celém rozsahu v době připojení klienta.

Jiné metody vyrovnávání zatížení můžou být vhodné pro velké nebo komplikované systémy. Požádejte o pomoc zástupce Microsoftu nebo otevřete [žádost o podporu](fxt-support-ticket.md) . (Azure Load Balancer se pro Azure FXT Edge souborového *aktuálně nepodporuje.* )

## <a name="create-the-mount-command"></a>Vytvoření příkazu Mount

Z klienta ``mount`` příkaz mapuje virtuální server (VServer) v clusteru Azure FXT Edge souborového na cestu v místním systému souborů.

Formát je ``mount <FXT cluster path> <local path> {options}``

Příkaz Mount obsahuje tři prvky:

* cesta clusteru – kombinace IP adresy a cesty pro spojení oboru názvů popsané níže
* místní cesta – cesta na klientovi
* parametry příkazu připojení – (uvedená v [příkazu použít Doporučené možnosti připojení](#use-recommended-mount-command-options))

### <a name="create-the-cluster-path"></a>Vytvořit cestu clusteru

Cesta clusteru je kombinací *IP adresy* VServer a cesty ke *spojení s oborem názvů*. Spojení oboru názvů je virtuální cesta, kterou jste definovali při [Přidání systému úložiště](fxt-add-storage.md#create-a-junction).

Pokud jste například použili ``/fxt/files`` jako cestu k oboru názvů, klienti budou připojeni *IP_address*:/FXT/Files do svého místního přípojného bodu.

![Dialogové okno Přidat nový spojovací bod s/avere/Files v poli cesta oboru názvů](media/fxt-mount/fxt-junction-example.png)

IP adresa je jedna z klientských IP adres definovaných pro VServer. Rozsah IP adres klientů můžete najít na dvou místech na ovládacím panelu clusteru:

* Tabulka **VServers** (karta řídicí panel) –

  ![Karta řídicí panel ovládacího panelu s kartou VServer vybranou v tabulce dat pod grafem a část IP adresa v kruhu](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* Stránka nastavení **sítě s přístupem klienta** –

  ![Nastavení > VServer > stránce konfigurace sítě klientské sítě s kruhem kolem části rozsah adres v tabulce pro konkrétní VServer](media/fxt-mount/fxt-ip-addresses-settings.png)

Zkombinujte IP adresu a cestu k oboru názvů, abyste pomohli vytvořit cestu clusteru pro příkaz Mount.

Příklad příkazu pro připojení klienta: ``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>Vytvoření místní cesty

Místní cesta pro příkaz Mount je pro vás. Můžete nastavit libovolnou strukturu cesty, kterou chcete mít v rámci virtuálního oboru názvů. Navrhněte obor názvů a místní cestu, která je vhodná pro váš klientský pracovní postup.

Další informace o oboru názvů směřujícím na klienta najdete v [přehledu oboru názvů](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)Průvodce konfigurací clusteru.

Kromě cest přidejte [Možnosti příkazu připojit](#use-recommended-mount-command-options) popsané níže při připojování jednotlivých klientů.

### <a name="use-recommended-mount-command-options"></a>Použití doporučených možností příkazu pro připojení

Chcete-li zajistit bezproblémové připojení klienta, předejte tato nastavení a argumenty do příkazu mount:

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Požadovaná nastavení | Description |
--- | ---
``hard`` | Do clusteru Azure FXT Edge souborového se dočasná připojení pojí s chybami aplikací a možnou ztrátou dat.
``proto=netid`` | Tato možnost podporuje odpovídající zpracování chyb sítě systému souborů NFS.
``mountproto=netid`` | Tato možnost podporuje odpovídající zpracování chyb sítě pro operace připojení.
``retry=n`` | Nastavte ``retry=30`` , aby nedocházelo k přechodným chybám připojení. (V připojeních na popředí se doporučuje jiná hodnota.)

| Preferované nastavení  | Description |
--- | ---
``nointr``            | Pokud klienti používají starší jádra operačního systému (do 15. dubna 2008), které podporují tuto možnost, použijte ji. Výchozím nastavením je možnost "intr".

## <a name="next-steps"></a>Další kroky

Po připojení klientů můžete otestovat pracovní postup a začít s clusterem.

Pokud potřebujete přesunout data do nového cloudového jádra souborového, využijte výhod struktury mezipaměti pomocí paralelní ingestování dat. Některé strategie jsou popsány v tématu [přesun dat do clusteru vFXT](../avere-vfxt/avere-vfxt-data-ingest.md). (Avere vFXT for Azure je cloudový produkt, který používá technologii pro ukládání do mezipaměti, která se velmi podobá Azure FXT Edge souborového.)

Pokud potřebujete řešit problémy s hardwarem, přečtěte si téma [monitorování stavu hardwaru Azure FXT Edge souborového](fxt-monitor.md) .