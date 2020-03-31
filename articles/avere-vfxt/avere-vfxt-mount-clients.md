---
title: Připojení Avere vFXT – Azure
description: Jak připojit klienty pomocí Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: b8486b5a33226b1faa5e3874144129dbe7a1a2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153407"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Připojení clusteru Avere vFXT

Podle následujících kroků připojte klientské počítače ke clusteru vFXT.

1. Rozhodněte se, jak vyvážit zatížení klientského provozu mezi uzly clusteru. Přečtěte si [zůstatek zatížení klienta](#balance-client-load), níže, podrobnosti.
1. Identifikujte IP adresu a spojovací cestu k připojení.
1. Vystaví [příkaz mount](#mount-command-arguments)s příslušnými argumenty.

## <a name="balance-client-load"></a>Vyvažit zatížení klienta

Chcete-li pomoci vyvážit požadavky klientů mezi všechny uzly v clusteru, měli byste připojit klienty k plnému rozsahu adres IP klientů. Existuje několik jednoduchých způsobů, jak automatizovat tento úkol.

> [!TIP]
> Jiné metody vyrovnávání zatížení mohou být vhodné pro velké nebo složité systémy; [otevřete lístek podpory](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) pro pomoc.)
>
> Pokud dáváte přednost použití serveru DNS pro automatické vyrovnávání zatížení na straně serveru, musíte nastavit a spravovat vlastní SERVER DNS v rámci Azure. V takovém případě můžete nakonfigurovat round-robin DNS pro cluster vFXT podle tohoto dokumentu: [Konfigurace DNS clusteru Avere](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Ukázkový vyvážený klientský montážní skript

Tento příklad kódu používá IP adresy klienta jako náhodný prvek k distribuci klientů na všechny dostupné IP adresy clusteru vFXT.

```bash
function mount_round_robin() {
    # to ensure the nodes are spread out somewhat evenly the default
    # mount point is based on this node's IP octet4 % vFXT node count.
    declare -a AVEREVFXT_NODES="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
    OCTET4=$((`hostname -i | sed -e 's/^.*\.\([0-9]*\)/\1/'`))
    DEFAULT_MOUNT_INDEX=$((${OCTET4} % ${#AVEREVFXT_NODES[@]}))
    ROUND_ROBIN_IP=${AVEREVFXT_NODES[${DEFAULT_MOUNT_INDEX}]}

    DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

    # no need to write again if it is already there
    if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi
}
```

Výše uvedená funkce je součástí příkladu Batch, který je k dispozici v [příkladech Avere vFXT.](https://github.com/Azure/Avere#tutorials)

## <a name="create-the-mount-command"></a>Vytvoření příkazu připojit

> [!NOTE]
> Pokud jste při vytváření clusteru Avere vFXT nevytvořili nový kontejner objektů Blob, přidejte před pokusem o připojení klientů systémy úložiště, jak je popsáno v [seznamu Konfigurace úložiště.](avere-vfxt-add-storage.md)

Z vašeho klienta ``mount`` příkaz mapuje virtuální server (vserver) v clusteru vFXT na cestu v místním souborovém systému. Formát je``mount <vFXT path> <local path> {options}``

Příkaz mount má tři prvky:

* vFXT cesta - kombinace IP adresy a názvového prostoru spojovací cesty na clusteru 9popsané níže)
* místní cesta - cesta na straně klienta
* možnosti příkazu mount – uvedené v [argumentech příkazu Připojit](#mount-command-arguments)

### <a name="junction-and-ip"></a>Křižovatka a IP

Cesta vserver je kombinací jeho *IP adresy* a cesty ke spojení *oboru názvů*. Spojení oboru názvů je virtuální cesta, která byla definována při přidání systému úložiště.

Pokud byl cluster vytvořen pomocí úložiště objektů Blob, je cesta oboru názvů k tomuto kontejneru`/msazure`

Příklad: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Pokud jste po vytvoření clusteru přidali úložiště, je spojovací cesta oboru názvů hodnotou, kterou jste nastavili v **cestě oboru názvů** při vytváření křižovatky. Pokud jste například ``/avere/files`` použili jako cestu oboru názvů, klienti by se připojit *IP_address*:/avere/files na jejich místní přípojný bod.

![Dialogové okno Přidat novou křižovatku s parametrem /avere/files v poli cesty oboru názvů](media/avere-vfxt-create-junction-example.png) <!-- to do - change example and screenshot to vfxt/files instead of avere -->

IP adresa je jednou z ip adres určených pro klienta definovaných pro vserver. Rozsah IP adresy pro klienty naleznete na dvou místech v ovládacím panelu Avere:

* Tabulka **VServers** (karta Řídicí panel) -

  ![Karta Řídicí panel ovládacího panelu Avere s kartou VServer vybranou v tabulce dat pod grafem a sekce IP adresa byla zakroužkovaná](media/avere-vfxt-ip-addresses-dashboard.png)

* Stránka Nastavení **sítě směřující k klientům** -

  ![Nastavení > konfigurační stránku VServer > klientskou síť s kruhem kolem části Rozsah adres v tabulce pro konkrétní vserver](media/avere-vfxt-ip-addresses-settings.png)

Kromě cesty, patří mount [příkaz argumenty](#mount-command-arguments) popsané níže při montáži každého klienta.

### <a name="mount-command-arguments"></a>Argumenty příkazu Připojení

Chcete-li zajistit bezproblémové připojení klienta, předejte v příkazu připojení tato nastavení a argumenty:

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Požadovaná nastavení | |
--- | ---
``hard`` | Měkké připojení ke clusteru vFXT jsou spojeny s selháním aplikací a možnou ztrátou dat.
``proto=netid`` | Tato možnost podporuje vhodné zpracování chyb sítě NFS.
``mountproto=netid`` | Tato možnost podporuje vhodné zpracování chyb sítě pro operace připojení.
``retry=n`` | Nastavte, ``retry=30`` aby se zabránilo přechodným selháním připojení. (V úchytech v popředí se doporučuje jiná hodnota.)

## <a name="next-steps"></a>Další kroky

Po připojení klienti je můžete použít ke kopírování dat do nového kontejneru úložiště objektů Blob v clusteru. Pokud nepotřebujete naplnit nové úložiště, přečtěte si další odkazy, kde najdete další úkoly nastavení:

* [Přesunutí dat do základního fileru clusteru](avere-vfxt-data-ingest.md) – použití více klientů a vláken k efektivnímu nahrání dat do nového základního fileru
* [Přizpůsobení ladění clusteru](avere-vfxt-tuning.md) – přizpůsobení nastavení clusteru tak, aby vyhovovalo vaší pracovní zátěži
* [Správa clusteru](avere-vfxt-manage-cluster.md) – jak spustit nebo zastavit cluster a spravovat uzly
