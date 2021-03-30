---
title: Připojení avere vFXT – Azure
description: Naučte se, jak připojit klienty ke clusteru vFXT v avere vFXT pro Azure a jak vyrovnávat zatížení klientského provozu mezi uzly clusteru.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: 44a4e1293bc4c5a54e1e345d5cf95ba307a7b120
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88272567"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Připojení clusteru Avere vFXT

Pomocí těchto kroků připojíte klientské počítače ke clusteru vFXT.

1. Rozhodněte, jak vyrovnávat zatížení klientských přenosů mezi uzly clusteru. Podrobnosti o [Vyrovnávání zatížení klienta](#balance-client-load)najdete níže.
1. Identifikujte IP adresu a cestu spojení pro připojení.
1. Vydejte odpovídající argumenty pomocí [příkazu Mount](#mount-command-arguments).

## <a name="balance-client-load"></a>Vyvážit zatížení klienta

Aby bylo možné vyrovnávat požadavky klientů mezi všemi uzly v clusteru, měli byste připojit klienty k celé škále IP adres klientů. Je několik jednoduchých způsobů, jak tuto úlohu automatizovat.

> [!TIP]
> Další metody vyrovnávání zatížení můžou být vhodné pro velké nebo komplikované systémy. [otevřete lístek podpory](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) pro pomoc.)
>
> Pokud upřednostňujete použití serveru DNS pro automatické vyrovnávání zatížení na straně serveru, musíte nastavit a spravovat vlastní server DNS v rámci Azure. V takovém případě můžete nakonfigurovat DNS pro kruhové dotazování pro cluster vFXT podle tohoto dokumentu: [avere konfigurace DNS clusteru](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Ukázkový skript pro připojování klienta

Tento příklad kódu používá klientské IP adresy jako náhodné prvky pro distribuci klientů na všechny dostupné IP adresy clusteru vFXT.

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

Výše uvedená funkce je součástí příkladu dávky, který je k dispozici na webu [Příklady avere vFXT](https://github.com/Azure/Avere#tutorials) .

## <a name="create-the-mount-command"></a>Vytvoření příkazu Mount

> [!NOTE]
> Pokud jste při vytváření clusteru avere vFXT nevytvořili nový kontejner objektů blob, přidejte systémy úložiště, jak je popsáno v tématu [Konfigurace úložiště](avere-vfxt-add-storage.md) před pokusem o připojení klientů.

Z klienta ``mount`` příkaz mapuje virtuální server (VServer) v clusteru vFXT na cestu v místním systému souborů. Formát je ``mount <vFXT path> <local path> {options}``

Příkaz Mount má tři prvky:

* vFXT cesta – kombinace IP adresy a cesty pro spojení oboru názvů na clusteru 9described níže)
* místní cesta – cesta na klientovi
* možnosti příkazu připojit – uvedené v [argumentech příkazu Mount](#mount-command-arguments)

### <a name="junction-and-ip"></a>Spojení a IP adresa

Cesta VServer je kombinací své *IP adresy* a cesty k *oboru názvů*. Spojení oboru názvů je virtuální cesta, která byla definována při přidání systému úložiště.

Pokud byl cluster vytvořen s úložištěm objektů blob, cesta oboru názvů k tomuto kontejneru je `/msazure`

Příklad: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Pokud jste po vytvoření clusteru přidali úložiště, bude cesta pro spojení oboru názvů nastavena jako hodnota v **cestě oboru názvů** při vytváření spojení. Pokud jste například použili ``/avere/files`` jako cestu k oboru názvů, klienti budou připojeni *IP_address*:/avere/Files do svého místního přípojného bodu.

![Dialogové okno Přidat nový spojovací bod s/avere/Files v poli cesta oboru názvů](media/avere-vfxt-create-junction-example.png) <!-- to do - change example and screenshot to vfxt/files instead of avere -->

IP adresa je jedna z klientských IP adres definovaných pro VServer. Rozsah IP adres klientů můžete najít na dvou místech v ovládacím panelu avere:

* Tabulka **VServers** (karta řídicí panel) –

  ![Karta řídicí panel ovládacího panelu avere s kartou VServer vybranou v tabulce dat pod grafem a část IP adresy v kruhu](media/avere-vfxt-ip-addresses-dashboard.png)

* Stránka nastavení **sítě s přístupem klienta** –

  ![Nastavení > VServer > stránce konfigurace sítě klientské sítě s kruhem kolem části rozsah adres v tabulce pro konkrétní VServer](media/avere-vfxt-ip-addresses-settings.png)

Kromě cest uveďte [argumenty příkazu Mount](#mount-command-arguments) popsané níže při připojování jednotlivých klientů.

### <a name="mount-command-arguments"></a>Argumenty příkazu Mount

Chcete-li zajistit bezproblémové připojení klienta, předejte tato nastavení a argumenty do příkazu mount:

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Požadovaná nastavení | Popis |
--- | ---
``hard`` | K selhání aplikací a možné ztrátě dat se připojí provizorní připojení k vFXT clusteru.
``proto=netid`` | Tato možnost podporuje odpovídající zpracování chyb sítě systému souborů NFS.
``mountproto=netid`` | Tato možnost podporuje odpovídající zpracování chyb sítě pro operace připojení.
``retry=n`` | Nastavte ``retry=30`` , aby nedocházelo k přechodným chybám připojení. (V připojeních na popředí se doporučuje jiná hodnota.)

## <a name="next-steps"></a>Další kroky

Po připojení klientů je můžete použít ke zkopírování dat do nového kontejneru úložiště objektů BLOB v clusteru. Pokud nepotřebujete naplnit nové úložiště, přečtěte si další odkazy, kde najdete další informace o dalších úlohách nastavení:

* [Přesuňte data do cluster Core souborového](avere-vfxt-data-ingest.md) – jak používat víc klientů a vláken k efektivnímu nahrávání vašich dat do nového základního souborového
* [Přizpůsobení ladění clusteru](avere-vfxt-tuning.md) – přizpůsobení nastavení clusteru podle vašich úloh
* [Správa clusteru](avere-vfxt-manage-cluster.md) – jak spustit nebo zastavit cluster a spravovat uzly
