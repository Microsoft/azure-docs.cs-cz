---
title: Připojení mezipaměti HPC Azure
description: Postup připojení klientů ke službě Azure HPC cache Service
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 7f1d8d34d6351fc344fdb101ac8e9a96678df9d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91651424"
---
# <a name="mount-the-azure-hpc-cache"></a>Připojení služby Azure HPC Cache

Po vytvoření mezipaměti budou klienti systému souborů NFS k němu mít přístup pomocí jednoduchého `mount` příkazu. Příkaz připojí konkrétní cílovou cestu úložiště v mezipaměti HPC Azure do místního adresáře v klientském počítači.

Příkaz Mount se skládá z těchto prvků:

* Jedna z adres připojení ke službě cache (uvedená na stránce s přehledem mezipaměti)
* Cesta virtuálního oboru názvů, kterou jste nastavili pro cíl úložiště (uvedené na stránce oboru názvů mezipaměti)
* Místní cesta, která se má použít na klientovi
* Parametry příkazu, které optimalizují úspěch tohoto druhu připojení systému souborů NFS

Stránka **pokyny k připojení** pro vaši mezipaměť shromažďuje informace a Doporučené možnosti pro vás a vytvoří příkaz k připojení prototypu, který můžete zkopírovat. Podrobnosti najdete [v tématu použití nástroje pro připojení k pokyny](#use-the-mount-instructions-utility) .

## <a name="prepare-clients"></a>Příprava klientů

Zajistěte, aby klienti mohli připojit mezipaměť HPC Azure pomocí pokynů v této části.

### <a name="provide-network-access"></a>Poskytnutí přístupu k síti

Klientské počítače musí mít síťový přístup k virtuální síti mezipaměti a k privátní podsíti.

Můžete například vytvořit klientské virtuální počítače ve stejné virtuální síti nebo použít koncový bod, bránu nebo jiné řešení ve virtuální síti pro přístup zvenčí. (Mějte na paměti, že žádné jiné než samotná mezipaměť by se měla hostovat v podsíti mezipaměti.)

### <a name="install-utilities"></a>Nainstalovat nástroje

Nainstalujte příslušný softwarový nástroj pro Linux pro podporu příkazu Mount pro systém souborů NFS:

* Pro Red Hat Enterprise Linux nebo SuSE: `sudo yum install -y nfs-utils`
* Pro Ubuntu nebo Debian: `sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>Vytvořit místní cestu

Vytvořte cestu k místnímu adresáři pro každého klienta, abyste se připojili k mezipaměti. Vytvořte cestu pro každou cestu oboru názvů, kterou chcete připojit.

Příklad: `sudo mkdir -p /mnt/hpc-cache-1/target3`

Stránka [pokyny k připojení](#use-the-mount-instructions-utility) v Azure Portal obsahuje prototypový příkaz, který můžete zkopírovat.

Když připojíte klientský počítač k mezipaměti, přidružíte tuto cestu k cestě virtuálního oboru názvů, která představuje export cíle úložiště. Vytvořte adresáře pro každou cestu virtuálního oboru názvů, kterou bude klient používat.

## <a name="use-the-mount-instructions-utility"></a>Použití nástroje pro připojení k instrukci

Pomocí stránky s **pokyny k připojení** v Azure Portal můžete vytvořit kopírovací příkaz Mount. Otevřete stránku v části **Konfigurace** zobrazení mezipaměti na portálu.

Před použitím příkazu na klientovi se ujistěte, že klient splňuje požadavky a má software potřebný k použití příkazu NFS, `mount` jak je popsáno výše v tématu [Příprava klientů](#prepare-clients).

![snímek obrazovky instance mezipaměti prostředí Azure HPC na portálu se načtenou stránkou s pokyny ke konfiguraci >ho připojení](media/mount-instructions.png)

Pomocí tohoto postupu vytvořte příkaz Mount.

1. Přizpůsobení pole **cesta klienta** . Toto pole poskytuje příklad příkazu, který můžete použít k vytvoření místní cesty na klientovi. Klient přistupuje k obsahu z mezipaměti prostředí Azure HPC místně v tomto adresáři.

   Klikněte na pole a upravte příkaz tak, aby obsahoval název adresáře, který chcete. Název se zobrazí na konci řetězce po `sudo mkdir -p`

   ![snímek obrazovky s polem pro cestu klienta se kurzorem umístěným na konci](media/mount-edit-client.png)

   Po dokončení úprav pole se příkaz Mount v dolní části stránky aktualizuje o novou cestu klienta.

1. V seznamu vyberte **adresu připojení k mezipaměti** . Tato nabídka obsahuje seznam všech [přípojných bodů klienta](#find-mount-command-components)mezipaměti.

   Vyrovnává zatížení klientů napříč všemi dostupnými adresami připojení pro lepší výkon mezipaměti.

   ![snímek obrazovky s polem připojit k mezipaměti s selektorem zobrazující tři IP adresy, ze kterých si můžete vybrat](media/mount-select-ip.png)

1. Vyberte **cestu k virtuálnímu oboru názvů** , která se má použít pro klienta. Tyto cesty odkazují na exporty v systému back-end úložiště.

   ![Snímek obrazovky, který zobrazuje pole "cesta virtuálního oboru názvů" s otevřeným selektorem.](media/mount-select-target.png)

   Cesty k virtuálnímu oboru názvů můžete zobrazit a změnit na stránce portálu **oboru názvů** . Přečtěte si [Nastavení agregovaného oboru názvů](add-namespace-paths.md) , abyste viděli, jak.

   Další informace o funkci agregovaného oboru názvů mezipaměti HPC Azure najdete v článku [plánování agregovaného oboru názvů](hpc-cache-namespace.md).

1. Pole **Mount Command** v kroku 3 se automaticky naplní vlastním příkazem Mount, který používá adresu připojení, cestu k virtuálnímu oboru názvů a cestu klienta, kterou jste nastavili v předchozích polích.

   Klikněte na symbol kopírování na pravé straně pole a automaticky ho zkopírujte do schránky.

   ![snímek obrazovky s polem příkazu pro připojení prototypu, který zobrazuje text přechodu pro tlačítko Kopírovat do schránky](media/mount-command-copy.png)

1. Pomocí příkazu zkopírované připojení na klientském počítači ho připojte k mezipaměti HPC Azure. Příkaz můžete vydat přímo z příkazového řádku klienta nebo zahrnout příkaz Mount do skriptu nebo šablony pro instalaci klienta.

## <a name="understand-mount-command-syntax"></a>Vysvětlení syntaxe příkazu Mount

Příkaz Mount má následující tvar:

> sudo Mount {*Options*} *cache_mount_address*:/*namespace_path* *local_path*

Příklad:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

Po úspěšném provedení tohoto příkazu se obsah exportu úložiště zobrazí v ``hpccache`` adresáři na klientovi.

### <a name="mount-command-options"></a>Možnosti příkazu připojit

V případě robustního připojení klienta předejte tato nastavení a argumenty do příkazu mount:

> připojit-o pevný, proto = TCP, mountproto = TCP, opakovat = 30 $ {CACHE_IP_ADDRESS}:/$ {NAMESPACE_PATH} $ {LOCAL_FILESYSTEM_MOUNT_POINT}

| Doporučené nastavení příkazu pro připojení | Description |
--- | ---
``hard`` | Provizorní připojení k mezipaměti HPC Azure jsou spojená s chybami aplikací a možnou ztrátou dat.
``proto=tcp`` | Tato možnost podporuje odpovídající zpracování chyb sítě systému souborů NFS.
``mountproto=tcp`` | Tato možnost podporuje odpovídající zpracování chyb sítě pro operace připojení.
``retry=<value>`` | Nastavte ``retry=30`` , aby nedocházelo k přechodným chybám připojení. (V připojeních na popředí se doporučuje jiná hodnota.)

### <a name="find-mount-command-components"></a>Najít součásti příkazu Mount

Pokud chcete vytvořit příkaz Mount bez použití stránky s pokyny pro **připojení** , najdete adresy pro připojení na stránce s **přehledem** mezipaměti a na cestě virtuálního oboru názvů na stránce **oboru názvů** .

![snímek stránky s přehledem instance mezipaměti HPC v Azure se zvýrazněným políčkem v pravém dolním rohu seznamu adres připojení](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Adresy připojení mezipaměti odpovídají síťovým rozhraním v podsíti mezipaměti. Ve skupině prostředků jsou tyto síťové karty uvedené s názvy končícími na `-cluster-nic-` a číslo. Tato rozhraní neměňte ani neodstraňujte, jinak se mezipaměť nestane nedostupnou.

Cesty k virtuálnímu oboru názvů se zobrazí na stránce nastavení **oboru názvů** mezipaměti.

![snímek obrazovky s nastavením portálu > stránku oboru názvů s zvýrazňujícím polem kolem prvního sloupce tabulky: "cesta oboru názvů"](media/view-namespace-paths.png)

## <a name="next-steps"></a>Další kroky

* Pokud chcete přesunout data do cílů úložiště mezipaměti, přečtěte si téma [naplnění nového úložiště objektů BLOB v Azure](hpc-cache-ingest.md).
