---
title: Migrace z StorSimple na Azure File Sync
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: fauhse
ms.subservice: files
description: StorSimple je kompletní produkt a Azure File Sync je řešení, na které se má migrovat. Přečtěte si o konceptu migrace a seznamte se s AzureFiles@microsoft.com pro vlastní nápovědu k migraci.
ms.openlocfilehash: 1cc88080522a62085d9a515223512ef25c20a9e4
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895084"
---
# <a name="storsimple-migration-to-azure-file-sync"></a>StorSimple migrace na Azure File Sync

StorSimple je ukončený produkt společnosti Microsoft. Rozšířená podpora pro tento produkt a jeho cloudová služba bude poslední až do konce 2022.
Je důležité zahájit plánování migrace z StorSimpleu hned.

Výchozí a strategické dlouhodobé StorSimple zařízení Azure Service je možné migrovat do, je Azure File Sync. Je to všeobecně dostupná služba Azure s nadmnožinou funkcí nad StorSimple.

## <a name="full-cloud-side-migration-with-limited-downtime"></a>Úplná migrace na straně cloudu s omezenými výpadky
Tento článek popisuje koncept zahájení migrace.
Je nutné poznamenat, že zákazníci potřebují migraci z StorSimple a na Azure File Sync nepotřebují bez dalšího postupu.

> [!IMPORTANT]
> Společnost Microsoft se zavazuje pomáhat zákazníkům při jejich migraci. E-mail AzureFiles@microsoft. com pro vlastní plán migrace a také pomoc při migraci.

## <a name="migration-approach"></a>Postup migrace
Migrace na Azure File Sync spustí cloudovou stranu s minimálním dopadem na místní a omezené výpadky.
Níže uvedený pojem je zaměřen na zařízení řady StorSimple 8000.
Pokud budete potřebovat migraci z řady StorSimple 7000, v prvním kroku bude k dispozici bezplatný upgrade na odpovídajících zařízení se zápůjčkou 8000 series od Microsoftu.
Přihlaste se na AzureFiles@microsoft.com a my vám pomůžeme organizovat příslušný počet zařízení, která jsou na něm zapůjčená.

### <a name="general-approach"></a>Obecný přístup
![ALT](media/storage-sync-files-storsimple-migration/storsimple-docs-overview-concept.png "Znázornění migrace na straně cloudu prostřednictvím dočasného virtuálního zařízení a Windows serveru na nový místní Windows Server, který nahrazuje místní zařízení StorSimple")

1. Naklonujte svazek místního zařízení StorSimple a připojte ho k dočasnému virtuálnímu zařízení s StorSimple.
2. Připojte virtuální zařízení přes iSCSI k dočasnému virtuálnímu počítači Azure.
3. Instalace Azure File Sync na dočasném virtuálním počítači s Windows serverem – před konfigurací synchronizace na serveru je taky potřeba nastavit konkrétní klíč registru pro tuto migraci.
    * V závislosti na počtu sdílených složek na svazku StorSimple nasaďte stejné množství sdílených složek Azure. (Doporučujeme pro každý účet úložiště nasadit jednu sdílenou složku Azure.)
    * Nakonfigurujte synchronizaci mezi jednotlivými sdílenými počítači v cloudovém virtuálním počítači s Windows serverem a sdílenou složkou souborů Azure. (mapování 1:1)
    * Volitelně můžete přidat místní server jako místní mezipaměť výkonu s povoleným vrstvením cloudu. Tento krok je nezbytný, pokud chcete nahradit místní StorSimple pomocí bohatých a místních mezipamětí využívajících Windows Server a cloudovou vrstvu Azure File Sync. Místní Windows Server může být fyzický počítač nebo cluster nebo virtuální počítač. Nemusí mít tolik úložiště, kolik je nasazené jako velikost datové sady. Potřebuje jenom dostatečné úložiště pro ukládání nejčastěji používaných souborů do mezipaměti.

## <a name="minimizing-downtime"></a>Minimalizace výpadků
Po výše uvedeném kroku 3 se StorSimple místní zařízení pořád aktivně používá pro uživatele a aplikace. Sada souborů synchronizovaných z prvotního klonování svazku je proto v době dokončení synchronizace mírně zastaralá.
Přístup k minimalizaci výpadku je opakování procesu klonování svazku, což znamená, že synchronizace je rychlejší a rychlejší s každou iterací, která je zase povolena tím, že změny mezi klony svazků přestanou méně a méně.
Tento postup můžete opakovat, dokud nebude možné dokončit synchronizaci z klonu svazku po dobu, kterou vyhledáte po dobu výpadku.
V takovém případě zablokuje uživatelům a aplikacím změny v zařízení StorSimple. Začíná výpadek.
Proveďte jiný klon svazku a umožněte synchronizaci s připojenými servery.
Navažte přístup k vašim uživatelům a aplikacím na nový Azure File Sync zálohovaný Windows Server.
Zvažte nasazení/úpravy oboru názvů DFS, aby bylo možné vyjmutí z původního zařízení StorSimple k novému Windows serveru transparentnímu pro aplikace a uživatele.
Migrace je hotová.

## <a name="migration-goal"></a>Cíl migrace
Po dokončení migrace se dá zrušit zřízení dočasného virtuálního zařízení StorSimple a virtuálního počítače Azure.

Kromě toho je možné zrušit zřízení místních zařízení StorSimple, protože uživatelé a aplikace už přistupují k Windows serveru.
To, co jste opustili, je znázorněno na obrázku níže. Standardní Azure File Sync nasazení nabízí několik sdílených složek Azure a serverů s Windows, které jsou k nim připojené přes Azure File Sync. Pamatujte, že jeden server může současně připojit různé místní složky k různým sdíleným složkám souborů.
Jedna sdílená složka Azure může také synchronizovat na mnoha různých serverech, a to v případě, že budete potřebovat data uložená v pobočkách. Také se podívejte, jestli můžete optimalizovat zásady cloudu a zefektivnit tak využívání místního prostoru úložiště.

![ALT](media/storage-sync-files-storsimple-migration/storsimple-docs-goal.PNG "Ilustrace znázorňující cíl po dokončení migrace. Znázorňuje množství sdílených složek, které se synchronizují s místním Windows serverem s uživateli a aplikacemi, které přistupují k souborům v cloudu nebo na Windows serveru.")

## <a name="next-steps"></a>Další kroky
Seznamte se s Azure Files a Azure File Sync. Je důležité pochopit Azure File Sync terminologie a vzor nasazení pro úspěšnou migraci. K dispozici jsou podrobnější informace pro každý krok v tomto přehledovém článku. Při plánování a provádění migrace se ujistěte, že jste se od Microsoftu dostali k přizpůsobené nápovědě.

> [!IMPORTANT]
> Společnost Microsoft se zavazuje pomáhat zákazníkům při jejich migraci. E-mail AzureFiles@microsoft. com pro vlastní plán migrace a také pomoc při migraci.

## <a name="additional-resources"></a>Další zdroje informací:
Azure File Sync, jako cílová služba, má dva základní dokumenty, které vám doporučujeme přečíst, pokud jste s Azure File Sync ještě nevytvořili.
* [Azure File Sync – přehled](storage-sync-files-planning.md)
* [Průvodce nasazením Azure File Sync](storage-sync-files-deployment-guide.md)

Azure File je služba úložiště v Azure, která nabízí sdílené složky jako službu. Není nutné platit ani spravovat virtuální počítač nebo přidružené úložiště virtuálního počítače.
* [Soubory Azure – přehled](storage-files-introduction.md)
* [Soubory Azure – jak nasadit sdílenou složku Azure](storage-how-to-create-file-share.md)