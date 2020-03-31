---
title: Přesunutí dat do cloudového kontejneru azure hpc cache
description: Jak naplnit úložiště objektů blob Azure pro použití s mezipamětí Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: a5625341e3dd279d93a59c57cd3325245351723e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271873"
---
# <a name="move-data-to-azure-blob-storage"></a>Přesunutí dat do úložiště objektů blob Azure

Pokud váš pracovní postup zahrnuje přesun dat do úložiště objektů Blob Azure, ujistěte se, že používáte efektivní strategii. Můžete buď předem načíst data v novém kontejneru objektů Blob před definováním jako cíl úložiště, nebo přidat kontejner a pak zkopírovat data pomocí Azure HPC Cache.

Tento článek vysvětluje nejlepší způsoby, jak přesunout data do úložiště objektů Blob pro použití s Azure HPC Cache.

Mějte na paměti tyto skutečnosti:

* Azure HPC Cache používá specializovaný formát úložiště k uspořádání dat v úložišti objektů Blob. To je důvod, proč cíl úložiště objektů blob musí být buď nový, prázdný kontejner nebo kontejner objektů Blob, který byl dříve použit pro data mezipaměti Azure HPC. <!--([Avere vFXT for Azure](https://azure.microsoft.com/services/storage/avere-vfxt/) also uses this cloud file system.)-->

* Kopírování dat prostřednictvím mezipaměti Azure HPC do cíle back-endového úložiště je efektivnější, když používáte více klientů a paralelní operace. Jednoduchý příkaz kopírování z jednoho klienta bude přesouvat data pomalu.

Nástroj založený na Pythonu je k dispozici pro načtení obsahu do kontejneru úložiště objektů Blob. Přečtěte si [přednačtení dat v úložišti objektů Blob,](#pre-load-data-in-blob-storage-with-clfsload) kde se dozvíte víc.

Pokud nechcete používat nástroj načítání nebo pokud chcete přidat obsah do existujícího cíle úložiště, postupujte podle paralelních tipů pro ingestování dat v [kopírovat data prostřednictvím mezipaměti Azure HPC](#copy-data-through-the-azure-hpc-cache).

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>Předběžné načtení dat v úložišti objektů Blob pomocí CLFSLoad

Pomocí nastavení <!--[Avere CLFSLoad](https://aka.ms/avere-clfsload)--> Nástroj Avere CLFSLoad pro kopírování dat do nového kontejneru úložiště objektů Blob před přidáním jako cíl úložiště. Tento nástroj běží na jednom systému Linux a zapisuje data v proprietárním formátu potřebném pro Azure HPC Cache. CLFSLoad je nejúčinnější způsob, jak naplnit kontejner úložiště objektů Blob pro použití s mezipamětí.

Nástroj Avere CLFSLoad je k dispozici na vyžádání od vašeho týmu Azure HPC Cache. Požádejte o to kontakt svého týmu nebo otevřete [lístek podpory](hpc-cache-support-ticket.md) a požádejte o pomoc.

Tato možnost funguje pouze s novými prázdnými kontejnery. Vytvořte kontejner před použitím Avere CLFSLoad.

Podrobné informace jsou součástí distribuce Avere CLFSLoad, která je k dispozici na vyžádání od týmu Azure HPC Cache. <!-- [Avere CLFSLoad readme](https://github.com/microsoft/Avere-CLFSLoad/blob/master/README.md). --><!-- caution literal link -->

Obecný přehled procesu:

1. Připravte linuxový systém (VM nebo fyzický) s Pythonem verze 3.6 nebo novějším. Python 3.7 se doporučuje pro lepší výkon.
1. Nainstalujte software Avere-CLFSLoad do systému Linux.
1. Spusťte přenos z příkazového řádku Linuxu.

Nástroj Avere CLFSLoad potřebuje následující informace:

* ID účtu úložiště, které obsahuje kontejner úložiště objektů Blob
* Název prázdného kontejneru úložiště objektů Blob
* Token sdíleného přístupového podpisu (SAS), který umožňuje nástroji zapisovat do kontejneru
* Místní cesta ke zdroji dat – buď místní adresář, který obsahuje data ke kopírování, nebo místní cesta k připojenému vzdálenému systému s daty

<!-- The requirements are explained in detail in the [Avere CLFSLoad readme](https://aka.ms/avere-clfsload). -->

## <a name="copy-data-through-the-azure-hpc-cache"></a>Kopírování dat prostřednictvím mezipaměti Azure HPC

Pokud nechcete používat nástroj Avere CLFSLoad nebo pokud chcete přidat velké množství dat do existujícího cíle úložiště objektů Blob, můžete jej zkopírovat prostřednictvím mezipaměti. Azure HPC Cache je navržen tak, aby obsluhoval více klientů současně, takže ke kopírování dat prostřednictvím mezipaměti byste měli používat paralelní zápisy z více klientů.

![Diagram znázorňující přesun dat s více klienty a více vlákny: V levém horním rohu je na ní několik šipek, které z něj pocházejí. Šipky ukazují na čtyři klientské počítače. Z každého klientského počítače tři šipky ukazují na azure hpc mezipaměť. Z mezipaměti Azure HPC více šipek ukazuje na úložiště objektů Blob.](media/hpc-cache-parallel-ingest.png)

``cp`` Příkazy ``copy`` nebo, které obvykle používáte k přenosu dat z jednoho systému úložiště do jiného, jsou procesy s jedním podprocesem, které kopírují pouze jeden soubor najednou. To znamená, že souborový server ingestuje pouze jeden soubor najednou - což je plýtvání prostředků mezipaměti.

Tato část vysvětluje strategie pro vytvoření víceklientského systému kopírování souborů s více vlákny pro přesun dat do úložiště objektů Blob pomocí mezipaměti Azure HPC. Vysvětluje koncepty přenosu souborů a rozhodovací body, které lze použít pro efektivní kopírování dat pomocí více klientů a jednoduchých příkazů pro kopírování.

To také vysvětluje některé nástroje, které mohou pomoci. Nástroj ``msrsync`` lze částečně automatizovat proces rozdělení datové sady do bloků a pomocí příkazů rsync. Skript ``parallelcp`` je jiný nástroj, který čte zdrojový adresář a vydává příkazy kopírování automaticky.

### <a name="strategic-planning"></a>Strategické plánování

Při vytváření strategie pro paralelní kopírování dat byste měli pochopit kompromisy v velikosti souboru, počtu souborů a hloubce adresáře.

* Pokud jsou soubory malé, metrika zájmu je soubory za sekundu.
* Pokud jsou soubory velké (10MiBi nebo vyšší), metrika zájmu je bajtů za sekundu.

Každý proces kopírování má propustnost a rychlost přenesené soubory, kterou lze měřit časováním délky příkazu kopírování a faktoringem velikosti souboru a počtu souborů. Vysvětlení, jak měřit sazby, je mimo rozsah tohoto dokumentu, ale je nutné pochopit, zda se budete zabývat malými nebo velkými soubory.

Strategie pro paralelní ingestování dat s azure hpc mezipaměť patří:

* Ruční kopírování – můžete ručně vytvořit kopii s více vlákny na straně klienta spuštěním více než jednoho příkazu kopírování najednou na pozadí proti předdefinovaným masem souborů nebo cest. Přečtěte si [azure hpc cache data ingestestování – ruční kopírování metoda](hpc-cache-ingest-manual.md) podrobnosti.

* Částečně automatizované kopírování ``msrsync``  -  ``msrsync`` s je obálka nástroj, který běží více paralelních ``rsync`` procesů. Podrobnosti načtete na [číst data mezipaměti Azure HPC ingestiusární – msrsync metoda](hpc-cache-ingest-msrsync.md).

* Skriptované kopírování ``parallelcp`` s - Naučte se, jak vytvořit a spustit skript paralelní kopírování v [Azure HPC Cache data ingestování - paralelní kopírování skript metody](hpc-cache-ingest-parallelcp.md).

## <a name="next-steps"></a>Další kroky

Po nastavení úložiště se dozvíte, jak mohou klienti připojit mezipaměť.

* [Přístup k systému Azure HPC Cache](hpc-cache-mount.md)
