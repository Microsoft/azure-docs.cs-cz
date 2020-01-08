---
title: Připojení avere vFXT – Azure
description: Jak připojit klienty pomocí avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 39c4d6a77121e0b52a1da827ebb9e1976f609b30
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75415287"
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
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,nointr,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
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
> Pokud jste při vytváření clusteru avere vFXT nevytvořili nový kontejner objektů blob, postupujte podle kroků v části [Konfigurace úložiště](avere-vfxt-add-storage.md) předtím, než se pokusíte připojit klienty.

Z klienta příkaz ``mount`` mapuje virtuální server (VServer) v clusteru vFXT na cestu v místním systému souborů. Formát je ``mount <vFXT path> <local path> {options}``

Příkaz Mount obsahuje tři prvky:

* vFXT cesta – (kombinace IP adresy a cesty spojení s oborem názvů popsaná níže)
* místní cesta – cesta na klientovi
* parametry příkazu připojení – (uvedené v [příkazech připojit argumenty příkazu](#mount-command-arguments))

### <a name="junction-and-ip"></a>Spojení a IP adresa

Cesta VServer je kombinací své *IP adresy* a cesty k *oboru názvů*. Spojení oboru názvů je virtuální cesta, která byla definována při přidání systému úložiště.

Pokud byl cluster vytvořen s úložištěm objektů blob, cesta k oboru názvů je `/msazure`

Příklad: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Pokud jste po vytvoření clusteru přidali úložiště, cesta spojení oboru názvů odpovídá hodnotě, kterou jste nastavili v **cestě oboru názvů** při vytváření spojení. Pokud jste například jako cestu k oboru názvů použili ``/avere/files``, klienti by připojení *IP_address*:/avere/Files do svého místního přípojného bodu.

![Dialogové okno Přidat nový spojovací bod s/avere/Files v poli cesta oboru názvů](media/avere-vfxt-create-junction-example.png)

IP adresa je jedna z klientských IP adres definovaných pro VServer. Rozsah IP adres klientů můžete najít na dvou místech v ovládacím panelu avere:

* Tabulka **VServers** (karta řídicí panel) –

  ![Karta řídicí panel ovládacího panelu avere s kartou VServer vybranou v tabulce dat pod grafem a část IP adresy v kruhu](media/avere-vfxt-ip-addresses-dashboard.png)

* Stránka nastavení **sítě s přístupem klienta** –

  ![Nastavení > VServer > stránce konfigurace sítě klientské sítě s kruhem kolem části rozsah adres v tabulce pro konkrétní VServer](media/avere-vfxt-ip-addresses-settings.png)

Kromě cest uveďte [argumenty příkazu Mount](#mount-command-arguments) popsané níže při připojování jednotlivých klientů.

### <a name="mount-command-arguments"></a>Argumenty příkazu Mount

Chcete-li zajistit bezproblémové připojení klienta, předejte tato nastavení a argumenty do příkazu mount:

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Požadovaná nastavení | |
--- | ---
``hard`` | K selhání aplikací a možné ztrátě dat se připojí provizorní připojení k vFXT clusteru.
``proto=netid`` | Tato možnost podporuje odpovídající zpracování chyb sítě systému souborů NFS.
``mountproto=netid`` | Tato možnost podporuje odpovídající zpracování chyb sítě pro operace připojení.
``retry=n`` | Nastavte ``retry=30``, aby nedocházelo k přechodným chybám připojení. (V připojeních na popředí se doporučuje jiná hodnota.)

| Preferované nastavení  | |
--- | ---
``nointr``            | Možnost "nointr" je upřednostňována pro klienty se staršími jádry (před dubna 2008), které podporují tuto možnost. Všimněte si, že výchozí možností je "intr".

## <a name="next-steps"></a>Další kroky

Po připojení klientů je můžete použít k naplnění úložiště dat back-endu (Core souborového). Další informace o dalších úlohách nastavení najdete v těchto dokumentech:

* [Přesuňte data do clusteru Core souborového](avere-vfxt-data-ingest.md) – jak používat víc klientů a vláken k efektivnímu nahrávání vašich dat.
* [Přizpůsobení ladění clusteru](avere-vfxt-tuning.md) – přizpůsobení nastavení clusteru podle vašich úloh
* [Správa clusteru](avere-vfxt-manage-cluster.md) – jak spustit nebo zastavit cluster a spravovat uzly
