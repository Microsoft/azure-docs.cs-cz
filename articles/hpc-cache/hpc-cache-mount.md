---
title: Připojení mezipaměti Azure HPC
description: Jak připojit klienty ke službě Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: f176e30cfaf9a52e4f58091b7fc76098a4c88a48
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657362"
---
# <a name="mount-the-azure-hpc-cache"></a>Připojení mezipaměti Azure HPC

Po vytvoření mezipaměti mohou klienti nfs přistupovat pomocí jednoduchého `mount` příkazu. Příkaz propojuje konkrétní cílovou cestu úložiště v mezipaměti Azure HPC s místním adresářem v klientském počítači.

Příkaz mount se skládá z těchto prvků:

* Jedna z adres připojení mezipaměti (uvedená na stránce přehledu mezipaměti)
* Cesta virtuálního oboru názvů, kterou jste nastavili při vytváření cíle úložiště
* Místní cesta, která má být v klientovi používána
* Parametry příkazů, které optimalizují úspěch tohoto druhu připojení nfs

Stránka **Pokyny k připojení** pro mezipaměť shromažďuje informace a doporučené možnosti a vytváří příkaz připojení prototypu, který můžete kopírovat. Přečtěte si podrobnosti o použití [nástroje pokyny k montáži.](#use-the-mount-instructions-utility)

## <a name="prepare-clients"></a>Příprava klientů

Ujistěte se, že vaši klienti jsou schopni připojit Azure HPC cache podle pokynů v této části.

### <a name="provide-network-access"></a>Poskytnutí přístupu k síti

Klientské počítače musí mít síťový přístup k virtuální síti mezipaměti a privátní podsíti.

Můžete například vytvořit klientské virtuální počítače v rámci stejné virtuální sítě nebo použít koncový bod, bránu nebo jiné řešení ve virtuální síti pro přístup zvenčí. (Nezapomeňte, že nic jiného než samotná mezipaměť by neměla být hostována uvnitř podsítě mezipaměti.)

### <a name="install-utilities"></a>Instalace nástrojů

Nainstalujte příslušný linuxový nástrojový software pro podporu příkazu připojení systému nfs:

* Pro Red Hat Enterprise Linux nebo SuSE:`sudo yum install -y nfs-utils`
* Pro Ubuntu nebo Debian:`sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>Vytvoření místní cesty

Vytvořte cestu k místnímu adresáři pro každého klienta pro připojení ke mezipaměti. Vytvořte cestu pro každý cíl úložiště, který chcete připojit.

Příklad: `sudo mkdir -p /mnt/hpc-cache-1/target3`

## <a name="use-the-mount-instructions-utility"></a>Použití nástroje pokyny pro montáž

Otevřete stránku **Pokyny pro připojení** v části **Konfigurovat** zobrazení mezipaměti na webu Azure Portal.

![Snímek obrazovky s instancí mezipaměti Azure HPC na portálu s načtenou stránkou Pokyny pro konfiguraci > připojení](media/mount-instructions.png)

Stránka příkazů připojení obsahuje informace o procesu připojení klienta a požadavky a pole, která můžete použít k vytvoření příkazu připojit.

Chcete-li použít tuto stránku, postupujte takto:

<!--1.  In step one of **Mounting your file system**, enter the path that the client will use to access the Azure HPC Cache storage target.

   * This path is local to the client.
   * After you provide the directory name, the field populates with a command you can copy. Use this command on the client directly or in a setup script to create the directory path on the client VM. -->

1. Zkontrolujte požadavky klienta a nainstalujte nástroje potřebné `mount` k použití příkazu NFS, jak je popsáno výše v [části Příprava klientů](#prepare-clients).

1. První krok **připojení systému souborů**<!-- label will change --> poskytuje příklad příkazu pro vytvoření místní cesty na straně klienta. Toto je cesta, kterou klient použije pro přístup k obsahu z mezipaměti Azure HPC.

   Poznamenejte si název cesty, abyste jej mohli v případě potřeby upravit v příkazu.

1. V kroku dva vyberte jednu z dostupných IP adres. Zde jsou uvedeny všechny [připojné body klienta](#find-mount-command-components) mezipaměti. Ujistěte se, že máte systém pro vyvážení zatížení mezi všemi ADRESAMI IP.

1. Pole v kroku tři se automaticky naplní příkazem pro připojení prototypu. Kliknutím na symbol kopírování na pravé straně pole ho automaticky zkopírujete do schránky.

   > [!NOTE]
   > Před použitím zkontrolujte příkaz kopírování. Možná budete muset přizpůsobit cestu připojení klienta a cílovou cestu virtuálního oboru názvů úložiště, které ještě nelze v tomto rozhraní vybrat. Měli byste také aktualizovat možnosti příkazu připojit tak, aby odrážely [doporučené možnosti](#mount-command-options) níže. Nápovědu načláneknete [na principsyntaxi příkazu připojení.](#understand-mount-command-syntax)

1. Pomocí zkopírovaného příkazu připojení (v případě potřeby s úpravami) v klientském počítači jej připojte k cíli úložiště v mezipaměti Azure HPC. Příkaz můžete vydat přímo z příkazového řádku klienta nebo jej zahrnout do skriptu nebo šablony nastavení klienta.

## <a name="understand-mount-command-syntax"></a>Principy syntaxe příkazu připojení

Příkaz mount má následující tvar:

> sudo mount *cache_mount_address*:/*namespace_path* *local_path* {*možnosti*}

Příklad:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -o hard,proto=tcp,mountproto=tcp,retry=30
root@test-client:/tmp#
```

Po úspěšném spuštění tohoto příkazu bude obsah exportu úložiště viditelný v adresáři ``hpccache`` v klientovi.

### <a name="mount-command-options"></a>Možnosti příkazu Připojit

Pro robustní připojení klienta předejte v příkazu připojení tato nastavení a argumenty:

> mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}

| Doporučená nastavení příkazů pro připojení | |
--- | ---
``hard`` | Měkké připojení k azure hpc mezipaměti jsou spojeny s selhání aplikací a možné ztráty dat.
``proto=tcp`` | Tato možnost podporuje vhodné zpracování chyb sítě NFS.
``mountproto=tcp`` | Tato možnost podporuje vhodné zpracování chyb sítě pro operace připojení.
``retry=<value>`` | Nastavte, ``retry=30`` aby se zabránilo přechodným selháním připojení. (V úchytech v popředí se doporučuje jiná hodnota.)

### <a name="find-mount-command-components"></a>Najít komponenty příkazu připojit

Pokud chcete vytvořit příkaz pro připojení bez použití stránky **Pokyny pro připojení,** můžete najít adresy připojení na stránce **Přehled** mezipaměti a cesty virtuálního oboru názvů na stránce **Cíle úložiště.**

![Snímek obrazovky s přehledem stránky instance Azure HPC Cache se zvýrazněním kolem seznamu přípojných adres v pravém dolním](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Adresy připojení mezipaměti odpovídají síťovým rozhraním uvnitř podsítě mezipaměti. Ve skupině prostředků jsou tyto síťové karty `-cluster-nic-` uvedeny s názvy končícími a číslem. Tato rozhraní neměňte ani neodstraňujte, jinak bude mezipaměť nedostupná.

Cesty virtuálního oboru názvů jsou zobrazeny na stránce **Cíle úložiště.** Kliknutím na konkrétní cílový název úložiště zobrazíte jeho podrobnosti, včetně agregovaných cest oboru názvů, které jsou k němu přidruženy.

![Snímek obrazovky s cílovým panelem úložiště mezipaměti s polem zvýraznění kolem položky ve sloupci Cesta v tabulce](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>Další kroky

* Pokud chcete přesunout data do cílů úložiště mezipaměti, [přečtěte si naplnění nového úložiště objektů blob Azure](hpc-cache-ingest.md).
