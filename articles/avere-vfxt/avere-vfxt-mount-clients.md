---
title: Připojte vFXT Avere – Azure
description: Jak připojit klienti s Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 41065b4ac6bc486e204c2bfd72b78ba8722270c4
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55809157"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Připojení clusteru Avere vFXT  

Postupujte podle těchto kroků se připojíte ke svému clusteru vFXT klientské počítače.

1. Rozhodněte, jak vyrovnávat zatížení klientského provozu mezi uzly clusteru. Čtení [Vyrovnávání zatížení klienta](#balance-client-load)níže, pro podrobnosti. 
1. Určete IP adresu a spojení cestu pro připojení.
1. Problém [příkaz připojení](#mount-command-arguments), s příslušnými argumenty.

## <a name="balance-client-load"></a>Vyrovnání zatížení klienta

Abychom požadavky klientů rovnováhu mezi všechny uzly v clusteru, by měl připojení klientů k celému rozsahu IP adresy klienta. Existuje několik způsobů jednoduché pro automatizaci tohoto úkolu.

> [!TIP] 
> Jiné metody vyrovnávání zatížení může být vhodné pro velký nebo komplikovaný systémů: [vytvořit lístek podpory](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) nápovědu.)
> 
> Pokud chcete použít DNS server pro vyrovnávání zatížení automatické na straně serveru, musíte nastavit a spravovat vlastní server DNS v rámci Azure. V takovém případě můžete nakonfigurovat kruhové dotazování DNS pro cluster vFXT podle tohoto dokumentu: [Konfigurace DNS clusteru Avere](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Ukázka vyvážené klienta připojení skriptu

Tento příklad kódu používá klientských IP adres jako prvek randomizing distribuovat všechny vFXT clusteru k dispozici IP adresy klientům.

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

Výše uvedené funkce je součástí k dispozici v Batch příkladu [Avere vFXT příklady](https://github.com/Azure/Avere#tutorials) lokality.

## <a name="create-the-mount-command"></a>Vytvořit příkaz připojení 

> [!NOTE]
> Pokud při vytváření clusteru vFXT Avere nevytvoří nový kontejner objektů Blob, postupujte podle kroků v [konfigurace úložiště](avere-vfxt-add-storage.md) před pokusem o připojení klientů.

Z klienta ``mount`` příkazu mapování virtuálního serveru (vserver) na clusteru vFXT na cestu v místním systému souborů. Formát je ``mount <vFXT path> <local path> {options}``

Existují tři elementy pro tento příkaz: 

* Cesta vFXT - (kombinaci IP adresy a obor názvů spojovací cesty popsaných níže)
* místní cesta - cesta na straně klienta 
* příkaz Možnosti - připojení (uvedené v [připojit argumenty příkazu](#mount-command-arguments))

### <a name="junction-and-ip"></a>Spojení a IP

Cesta vserver je tvořená kombinací jeho *IP adresu* plus cestu k *obor názvů spojení*. Obor názvů spojení je virtuální cesta, která byla definována při přidání systému úložiště.

Pokud váš cluster byl vytvořen s úložištěm objektů Blob, je cestě k oboru názvů `/msazure`

Příklad: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Pokud jste přidali úložiště po vytvoření clusteru, cestě k oboru názvů spojení odpovídá hodnotu nastavenou v **Namespace cestu** při vytváření spojení. Například, pokud jste použili ``/avere/files`` jako vaší cestě k oboru názvů, vaši klienti připojují *IP_adresa*: / avere/soubory do jejich místní přípojného bodu.

!["Přidání nové spojení" dialogové okno s/avere/soubory do pole Cesta oboru názvů](media/avere-vfxt-create-junction-example.png)


IP adresa je jedním z IP adresy klienta, definována pro vserver. Můžete najít rozsah IP adres na dvou místech v Ovládacích panelech Avere přístupem klienta:

* **VServers** tabulku (kartu řídicí panel) – 

  ![Karta řídicí panel ovládacího panelu Avere kartou VServer vybrané v následující tabulce dat grafu a oddíl IP adresy v kruhu](media/avere-vfxt-ip-addresses-dashboard.png)

* **Síť směřující klienta** stránka nastavení - 

  ![Nastavení > VServer > Stránka Konfigurace síť směřující klienta v kroužku část tabulky pro konkrétní vserver rozsah adres](media/avere-vfxt-ip-addresses-settings.png)

Kromě cesty, zahrnout [připojit argumenty příkazu](#mount-command-arguments) je popsáno níže, když připojení každého klienta.

### <a name="mount-command-arguments"></a>Argumenty příkazu připojení

Zajistit bezproblémové klientských připojení předání těchto nastavení a argumenty v příkazu připojení: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``


| Požadovaná nastavení | |
--- | --- 
``hard`` | Dočasná připojení ke clusteru vFXT jsou přidruženy k selhání aplikace a ke ztrátě. 
``proto=netid`` | Tato možnost podporuje vhodné řešení chyb sítě systému souborů NFS.
``mountproto=netid`` | Tato možnost podporuje vhodné řešení chyb sítě pro operace připojení.
``retry=n`` | Nastavte ``retry=30`` aby se zabránilo selhání přechodné připojení. (Jiné hodnoty se doporučuje v popředí připojení).

| Nastavení  | |
--- | --- 
``nointr``            | Možnost "nointr" je upřednostněny klientům pomocí starší verze jádra (před duben 2008), které podporují tuto možnost. Všimněte si, že je možnost "intr" výchozí nastavení.


## <a name="next-steps"></a>Další postup

Po připojení klientů je můžete použít k naplnění back-endového úložiště dat (základní filtr). Přečtěte si k těmto dokumentům Další informace o úlohách další nastavení:

* [Přesun dat do jádra filer clusteru](avere-vfxt-data-ingest.md) – jak efektivně nahrát data pomocí více klientů a vlákna
* [Přizpůsobení clusteru ladění](avere-vfxt-tuning.md) -přizpůsobit nastavení clusteru tak, aby vyhovovala vaší úlohy
* [Správa clusteru](avere-vfxt-manage-cluster.md) – spuštění nebo zastavení clusteru a spravovat uzly
