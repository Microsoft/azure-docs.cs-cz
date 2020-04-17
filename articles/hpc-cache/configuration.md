---
title: Konfigurace nastavení mezipaměti Azure HPC
description: Vysvětluje, jak nakonfigurovat další nastavení pro mezipaměť, jako je MTU a no-root-squash a jak získat přístup k expresním snímkům z cílů úložiště objektů blob Azure.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: 137e41a3f7d346bb612605660d7798ac2fc297d1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538814"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Konfigurace dalších nastavení mezipaměti Azure HPC

Stránka **Konfigurace** na webu Azure Portal obsahuje možnosti přizpůsobení několika nastavení. Většina uživatelů není nutné měnit z výchozích hodnot.

Tento článek také popisuje, jak používat funkci snímek pro cíle úložiště objektů blob Azure. Funkce snímek nemá žádné konfigurovatelné nastavení.

Nastavení zobrazíte tak, že otevřete stránku **Konfigurace** mezipaměti na webu Azure Portal.

![snímek obrazovky s konfigurační stránkou na Webu Azure Portal](media/configuration.png)

## <a name="adjust-mtu-value"></a>Upravit hodnotu MTU
<!-- linked from troubleshoot-nas article -->

Maximální velikost přenosové jednotky mezipaměti můžete vybrat pomocí rozevírací nabídky označené **velikosti jednotky MTU**.

Výchozí hodnota je 1500 bajtů, ale můžete ji změnit na 1400.

> [!NOTE]
> Pokud snížíte velikost mtu mezipaměti, ujistěte se, že klienti a systémy úložiště, které komunikují s mezipamětí, mají stejné nastavení MTU nebo nižší hodnotu.

Snížení hodnoty MTU mezipaměti vám může pomoci obejít omezení velikosti paketů ve zbytku sítě mezipaměti. Některé virtuální ny například nemohou úspěšně přenášet 1500 bajtové pakety plné velikosti. Zmenšení velikosti paketů odeslaných přes síť VPN může tento problém odstranit. Všimněte si však, že nižší nastavení MTU mezipaměti znamená, že každá jiná součást, která komunikuje s mezipamětí - včetně klientů a úložných systémů - musí mít také nižší nastavení, aby se zabránilo problémům s komunikací s mezipamětí.

Pokud nechcete měnit nastavení MTU u jiných systémových součástí, neměli byste snižovat nastavení mtu mezipaměti. Existují další řešení, která obejdou omezení velikosti paketů VPN. Další informace o diagnostice a řešení tohoto problému najdete v článku [Úprava velikosti paketů VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) v článku řešení potíží na SERVERU NAS.

Další informace o nastavení MTU ve virtuálních sítích Azure najdete v nastavení [výkonu TCP/IP pro virtuální počítače Azure](../virtual-network/virtual-network-tcpip-performance-tuning.md).

## <a name="configure-root-squash"></a>Konfigurace kořenové squash
<!-- linked from troubleshoot -->

Nastavení **Enable root squash** určuje, jak azure hpc cache umožňuje přístup ke kořenům. Root squash pomáhá zabránit přístupu na kořenové úrovni od neoprávněných klientů.

Toto nastavení umožňuje uživatelům řídit přístup root na úrovni mezipaměti, což může pomoci kompenzovat požadované ``no_root_squash`` nastavení pro systémy NAS používané jako cíle úložiště. (Přečtěte si více o [požadavcích na cíl úložiště systému NFS](hpc-cache-prereqs.md#nfs-storage-requirements).) Může také zlepšit zabezpečení při použití s cíli úložiště objektů blob Azure.

Výchozí nastavení je **Ano**. (Mezipaměti vytvořené před dubnem 2020 mohou mít výchozí nastavení **Ne**.) Pokud je tato funkce povolena, zabraňuje také použití bitů oprávnění set-UID v požadavcích klientů do mezipaměti.

## <a name="view-snapshots-for-blob-storage-targets"></a>Zobrazení snímků pro cíle úložiště objektů blob

Azure HPC Cache automaticky ukládá snímky úložiště pro cíle úložiště objektů blob Azure. Snímky poskytují rychlý referenční bod pro obsah kontejneru úložiště back-end. Snímky nejsou náhradou za zálohování dat a neobsahují žádné informace o stavu dat uložených v mezipaměti.

> [!NOTE]
> Tato funkce snímku se liší od funkce snímku obsažené v softwaru pro ukládání NetApp, Isilon nebo ZFS. Tyto implementace snímek vyprázdnění změny z mezipaměti do systému úložiště back-end před pořízením snímku.
>
> Z důvodu efektivity snímek mezipaměti Azure HPC není vyprázdnění změny jako první a pouze záznamy dat, která byla zapsána do kontejneru objektů Blob. Tento snímek nepředstavuje stav dat uložených v mezipaměti, takže nedávné změny mohou být vyloučeny.

Tato funkce je k dispozici jenom pro cíle úložiště objektů blob Azure a její konfiguraci nelze změnit.

Snímky jsou pořízeny každých osm hodin v UTC 0:00, 08:00 a 16:00.

Azure HPC Cache ukládá denní, týdenní a měsíční snímky, dokud nejsou nahrazeny novými. Limity jsou:

* až 20 snímků denně
* až 8 týdenních snímků
* až 3 snímky měsíčně

Přístup k snímkům `.snapshot` z adresáře v oboru názvů cíle úložiště objektů blob.
