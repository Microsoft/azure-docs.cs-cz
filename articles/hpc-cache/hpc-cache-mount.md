---
title: Připojení mezipaměti Azure HPC
description: Jak připojit klienty ke službě Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a44232f06b455e20530271723e816c2117b339a0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458345"
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

Vytvořte cestu k místnímu adresáři pro každého klienta pro připojení ke mezipaměti. Vytvořte cestu pro každou cestu oboru názvů, kterou chcete připojit.

Příklad: `sudo mkdir -p /mnt/hpc-cache-1/target3`

Stránka [Připojit pokyny](#use-the-mount-instructions-utility) na webu Azure portal obsahuje příkaz prototypu, který můžete zkopírovat.

Když připojíte klientský počítač k mezipaměti, přidružíte tuto cestu k cestě virtuálního oboru názvů, která představuje export cíle úložiště. Vytvořte adresáře pro každou cestu virtuálního oboru názvů, kterou bude klient používat.

## <a name="use-the-mount-instructions-utility"></a>Použití nástroje pokyny pro montáž

Pomocí stránky **Pokyny pro připojení** na webu Azure Portal můžete vytvořit příkaz připojit. Otevřete stránku z části **Konfigurovat** zobrazení mezipaměti na portálu.

Před použitím příkazu na straně klienta se ujistěte, že klient splňuje požadavky `mount` a má software potřebný k použití příkazu NFS, jak je popsáno výše v [části Příprava klientů](#prepare-clients).

![Snímek obrazovky s instancí mezipaměti Azure HPC na portálu s načtenou stránkou Pokyny pro konfiguraci > připojení](media/mount-instructions.png)

Chcete-li vytvořit příkaz připojení, postupujte podle tohoto postupu.

1. Přizpůsobte pole **Cesta klienta.** Toto pole uvádí příklad příkazu, který můžete použít k vytvoření místní cesty na straně klienta. Klient přistupuje k obsahu z mezipaměti Azure HPC místně v tomto adresáři.

   Klepněte na pole a upravte příkaz tak, aby obsahoval požadovaný název adresáře. Název se zobrazí na konci řetězce za`sudo mkdir -p`

   ![Snímek obrazovky s polem cesty klienta s kurzorem umístěným na konci](media/mount-edit-client.png)

   Po dokončení úprav pole se příkaz připojení v dolní části stránky aktualizuje novou cestou klienta.

1. Ze seznamu zvolte **adresu připojení mezipaměti.** V této nabídce jsou uvedeny všechny [přípojné body klienta mezipaměti](#find-mount-command-components).

   Vyvažte zatížení klienta napříč všemi dostupnými adresami připojení pro lepší výkon mezipaměti.

   ![snímek obrazovky s polem adresy připojení mezipaměti s voličem zobrazujícím tři IP adresy, ze kterých si můžete vybrat](media/mount-select-ip.png)

1. Zvolte **cestu virtuálního oboru názvů,** která se má použít pro klienta. Tyto cesty odkazují na exporty v back-endovém úložném systému.

   ![snímek obrazovky s polem cesty oboru názvů s otevřeným voličem](media/mount-select-target.png)

   Cesty virtuálního oboru názvů můžete zobrazit a změnit na stránce portálu Cíle úložiště. Přečtěte si [článek Přidání cílů úložiště,](hpc-cache-add-storage.md) abyste viděli, jak na to.

   Další informace o agregované funkci oboru názvů azure hpc cache najdete v části [Plánování agregovaného oboru názvů](hpc-cache-namespace.md).

1. **Příkazové** pole Připojení v kroku tři se automaticky naplní přizpůsobeným příkazem připojení, který používá adresu připojení, cestu virtuálního oboru názvů a cestu klienta, kterou nastavíte v předchozích polích.

   Kliknutím na symbol kopírování na pravé straně pole ho automaticky zkopírujete do schránky.

   ![snímek obrazovky s polem cesty oboru názvů s otevřeným voličem](media/mount-command-copy.png)

1. Pomocí zkopírovaného příkazu připojení v klientském počítači jej připojte ke mezipaměti Azure HPC. Příkaz můžete vydat přímo z příkazového řádku klienta nebo jej zahrnout do skriptu nebo šablony nastavení klienta.

## <a name="understand-mount-command-syntax"></a>Principy syntaxe příkazu připojení

Příkaz mount má následující tvar:

> sudo mount {*options*} *cache_mount_address*:/*namespace_path* *local_path*

Příklad:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
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

Pokud chcete vytvořit příkaz pro připojení bez použití stránky **Pokyny pro připojení,** můžete najít adresy připojení na stránce **Přehled** mezipaměti a cesty virtuálního oboru názvů na cílové stránce **úložiště.**

![Snímek obrazovky s přehledem stránky instance Azure HPC Cache se zvýrazněním kolem seznamu přípojných adres v pravém dolním](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Adresy připojení mezipaměti odpovídají síťovým rozhraním uvnitř podsítě mezipaměti. Ve skupině prostředků jsou tyto síťové karty `-cluster-nic-` uvedeny s názvy končícími a číslem. Tato rozhraní neměňte ani neodstraňujte, jinak bude mezipaměť nedostupná.

Cesty virtuálního oboru názvů jsou zobrazeny na stránce podrobností každého cíle úložiště. Kliknutím na konkrétní cílový název úložiště zobrazíte jeho podrobnosti, včetně agregovaných cest oboru názvů, které jsou k němu přidruženy.

![snímek obrazovky s podrobnou stránkou cíle úložiště (záhlaví "Aktualizovat cíl úložiště"). Kolem položky ve sloupci Cesta virtuálního oboru názvů v tabulce je pole zvýraznění.](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>Další kroky

* Pokud chcete přesunout data do cílů úložiště mezipaměti, [přečtěte si naplnění nového úložiště objektů blob Azure](hpc-cache-ingest.md).
