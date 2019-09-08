---
title: Přesun dat do cloudového kontejneru mezipaměti HPC Azure
description: Jak naplnit službu Azure Blob Storage pro použití s mezipamětí Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 07a97b1afa8049ace97f1589393cd76c24f21368
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775647"
---
# <a name="move-data-to-azure-blob-storage-for-azure-hpc-cache"></a>Přesun dat do Azure Blob Storage pro Azure HPC cache

Pokud váš pracovní postup zahrnuje přesun dat do úložiště objektů BLOB v Azure, ujistěte se, že používáte efektivní strategii pro kopírování dat prostřednictvím mezipaměti prostředí Azure HPC.

Tento článek vysvětluje nejlepší způsoby přesunu dat do úložiště objektů BLOB pro použití s mezipamětí Azure HPC.

Pamatujte na tyto skutečnosti:

* Mezipaměť prostředí Azure HPC používá pro uspořádání dat v úložišti objektů BLOB specializované formáty úložiště. To je důvod, proč cíl úložiště BLOB musí být buď nový prázdný kontejner, nebo kontejner objektů blob, který se dřív používal pro data mezipaměti Azure HPC. ([Avere vFXT pro Azure](https://azure.microsoft.com/services/storage/avere-vfxt/) taky používá tento cloudový systém souborů.)

* Kopírování dat prostřednictvím mezipaměti prostředí Azure HPC je nejvhodnější při použití více klientů a paralelních operací. Jednoduchý příkaz kopírování z jednoho klienta přesune data pomalu.

Nástroj založený na Pythonu je k dispozici pro načtení obsahu do kontejneru úložiště objektů BLOB. Další informace najdete [v tématu předběžné načtení dat v úložišti objektů BLOB](#pre-load-data-in-blob-storage-with-clfsload) .

Pokud nechcete použít nástroj pro načítání, nebo pokud chcete přidat obsah do existujícího cíle úložiště, postupujte podle tipů paralelního příjmu dat v [části kopírování dat prostřednictvím mezipaměti HPC Azure](#copy-data-through-the-azure-hpc-cache). 

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>Předběžné načtení dat v úložišti objektů BLOB pomocí CLFSLoad

Nástroj [avere CLFSLoad](https://aka.ms/avere-clfsload) můžete použít ke zkopírování dat do nového kontejneru úložiště objektů blob, než ho přidáte jako cíl úložiště. Tento nástroj se spouští na virtuálním počítači Linux a zapisuje data ve speciálním formátu potřebném pro mezipaměť prostředí Azure HPC. Toto je nejúčinnější způsob, jak naplnit kontejner úložiště objektů BLOB pro použití s mezipamětí.

Tato možnost funguje jenom s novými prázdnými kontejnery. Vytvořte kontejner před použitím avere CLFSLoad.

Podrobné informace jsou obsaženy v [souboru Readme avere CLFSLoad](https://github.com/microsoft/Avere-CLFSLoad/blob/master/README.md). <!-- caution literal link -->

Obecný přehled procesu:

1. Připravte systém Linux (fyzický nebo virtuální počítač) pomocí Pythonu verze 3,6 nebo novější. (Pro lepší výkon doporučujeme Python 3,7.)
1. Nainstalujte software avere-CLFSLoad do systému Linux.
1. Spusťte přenos z příkazového řádku systému Linux.

Nástroj avere CLFSLoad potřebuje následující informace:

* ID účtu úložiště, které obsahuje kontejner úložiště objektů BLOB
* Název prázdného kontejneru úložiště objektů BLOB
* Token sdíleného přístupového podpisu (SAS), který umožňuje nástroji zapisovat do kontejneru
* Místní cesta ke zdroji dat – buď místní adresář, který obsahuje data ke zkopírování, nebo místní cestu k připojenému vzdálenému systému s daty.

Požadavky jsou podrobně vysvětleny v [souboru Readme avere CLFSLoad](https://aka.ms/avere-clfsload).

## <a name="copy-data-through-the-azure-hpc-cache"></a>Kopírování dat prostřednictvím mezipaměti HPC Azure

Pokud nechcete použít nástroj avere CLFSLoad, nebo pokud chcete do existujícího cíle úložiště objektů BLOB přidat velké množství dat, můžete ho zkopírovat přes mezipaměť. Mezipaměť HPC Azure je navržená tak, aby sloužila více klientům současně, takže ke kopírování dat prostřednictvím mezipaměti byste měli použít paralelní zápisy z více klientů.

![Diagram znázorňující pohyb dat s více vlákny s více klienty: Vlevo nahoře je ikona pro místní hardwarové úložiště, která z něho přichází více šipek. Šipky ukazují na čtyři klientské počítače. Z každého klientského počítače tři šipky směřuje k mezipaměti HPC Azure. Z mezipaměti HPC Azure se několik šipek odkazuje na úložiště objektů BLOB.](media/hpc-cache-parallel-ingest.png) 

Příkazy ``cp`` nebo``copy`` , které obvykle slouží k přenosu dat z jednoho úložného systému do jiného, jsou procesy s jedním vláknem, které kopírují pouze jeden soubor v jednom okamžiku. To znamená, že souborový server bude v jednom okamžiku přijímat pouze jeden soubor, což je odpad z prostředků clusteru.

V této části se dozvíte o strategiích pro vytvoření vícevláknového systému kopírování souborů s více vlákny pro přesun dat do úložiště objektů BLOB s využitím Azure HPC cache. Vysvětluje koncepty přenosu souborů a body rozhodování, které lze použít k efektivnímu kopírování dat pomocí více klientů a jednoduchých příkazů kopírování.

Vysvětluje taky některé nástroje, které vám pomůžou. ``msrsync`` Nástroj lze použít k částečnému automatizaci procesu rozdělení datové sady do kontejnerů a používání příkazů rsync. Tento ``parallelcp`` skript je další nástroj, který čte zdrojový adresář a automaticky vystavuje příkazy kopírování.

### <a name="strategic-planning"></a>Strategické plánování

Při sestavování strategie pro paralelní kopírování dat byste měli pochopit kompromisy v velikosti souborů, počtu souborů a hloubkě adresáře.

* Když jsou soubory malé, je metrika zájmu soubory za sekundu.
* Když jsou soubory velké (10MiBi nebo větší), je metrika zájmu v bajtech za sekundu.

Každý proces kopírování má míru propustnosti a přenosovou rychlost přenosu souborů, která se dá změřit časováním příkazu pro kopírování a určením velikosti souboru a počtu souborů. Vysvětluje, jak změřit sazby jsou mimo rozsah tohoto dokumentu, ale je důležité pochopit, zda budete pracovat s malými nebo velkými soubory.

Strategie paralelního příjmu dat s mezipamětí služby Azure HPC cache zahrnují:

* Ruční kopírování – vícevláknové kopírování můžete vytvořit ručně na straně klienta spuštěním více než jednoho příkazu kopírování na pozadí v porovnání s předdefinovanými sadami souborů nebo cest. Přečtěte si informace v článku ingestování [cloudových dat Azure HPC – ruční kopírování](hpc-cache-ingest-manual.md) .

* Částečně automatizované kopírování s ``msrsync``  -  ``msrsync`` nástrojem je Obálkový nástroj, který spouští ``rsync`` více paralelních procesů. Podrobnosti si můžete přečíst v tématu [Azure HPC cache data ingestování – metoda msrsync](hpc-cache-ingest-msrsync.md).

* Skriptované kopírování pomocí ``parallelcp`` – Zjistěte, jak vytvořit a spustit skript paralelního kopírování v [Azure HPC cache – paralelní kopírování metody skriptu](hpc-cache-ingest-parallelcp.md).

## <a name="next-steps"></a>Další postup

Po nastavení úložiště se dozvíte, jak můžou klienti připojit mezipaměť.

* [Přístup k systému mezipaměti HPC Azure](hpc-cache-mount.md)
