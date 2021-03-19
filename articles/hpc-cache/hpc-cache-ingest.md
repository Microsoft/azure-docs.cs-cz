---
title: Přesun dat do cloudového kontejneru mezipaměti HPC Azure
description: Jak naplnit službu Azure Blob Storage pro použití s mezipamětí Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: 0da8a4fc1b59976c50cd96f2155715a4cb178cc9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87072772"
---
# <a name="move-data-to-azure-blob-storage"></a>Přesun dat do služby Azure Blob Storage

Pokud váš pracovní postup zahrnuje přesun dat do služby Azure Blob Storage, ujistěte se, že používáte efektivní strategii. Data můžete buď předem načíst do nového kontejneru objektů blob, abyste je mohli definovat jako cíl úložiště, nebo přidat kontejner a potom zkopírovat data pomocí Azure HPC cache.

Tento článek vysvětluje nejlepší způsoby přesunu dat do úložiště objektů BLOB pro použití s mezipamětí Azure HPC.

Pamatujte na tyto skutečnosti:

* Mezipaměť prostředí Azure HPC používá pro uspořádání dat v úložišti objektů BLOB specializované formáty úložiště. To je důvod, proč cíl úložiště BLOB musí být buď nový prázdný kontejner, nebo kontejner objektů blob, který se dřív používal pro data mezipaměti Azure HPC.

* Kopírování dat prostřednictvím mezipaměti prostředí Azure HPC do cílového záložního úložiště je efektivnější, pokud používáte více klientů a paralelních operací. Jednoduchý příkaz kopírování z jednoho klienta přesune data pomalu.

Nástroj založený na Pythonu je k dispozici pro načtení obsahu do kontejneru úložiště objektů BLOB. Další informace najdete [v tématu předběžné načtení dat v úložišti objektů BLOB](#pre-load-data-in-blob-storage-with-clfsload) .

Pokud nechcete použít nástroj pro načítání, nebo pokud chcete přidat obsah do existujícího cíle úložiště, postupujte podle tipů paralelního příjmu dat v [části kopírování dat prostřednictvím mezipaměti HPC Azure](#copy-data-through-the-azure-hpc-cache).

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>Předběžné načtení dat v úložišti objektů BLOB pomocí CLFSLoad

Nástroj avere CLFSLoad můžete použít ke zkopírování dat do nového kontejneru úložiště objektů blob, než ho přidáte jako cíl úložiště. Tento nástroj běží na jednom systému Linux a zapisuje data ve speciálním formátu potřebném pro mezipaměť prostředí Azure HPC. CLFSLoad je nejúčinnější způsob, jak naplnit kontejner úložiště objektů BLOB pro použití s mezipamětí.

Nástroj avere CLFSLoad je k dispozici na vyžádání od týmu Azure HPC cache. Požádejte o pomoc svého týmu, nebo otevřete [lístek podpory](hpc-cache-support-ticket.md) , který vám požádá o pomoc.

Tato možnost funguje jenom s novými prázdnými kontejnery. Vytvořte kontejner před použitím avere CLFSLoad.

Podrobné informace jsou součástí distribuce avere CLFSLoad, která je k dispozici na vyžádání od týmu Azure HPC cache.

Obecný přehled procesu:

1. Připravte systém Linux (virtuální počítač nebo fyzický) pomocí Pythonu verze 3,6 nebo novější. Pro lepší výkon doporučujeme Python 3,7.
1. Nainstalujte Avere-CLFSLoad software do systému Linux.
1. Spusťte přenos z příkazového řádku systému Linux.

Nástroj avere CLFSLoad potřebuje následující informace:

* ID účtu úložiště, které obsahuje kontejner úložiště objektů BLOB
* Název prázdného kontejneru úložiště objektů BLOB
* Token sdíleného přístupového podpisu (SAS), který umožňuje nástroji zapisovat do kontejneru
* Místní cesta ke zdroji dat – buď místní adresář, který obsahuje data ke zkopírování, nebo místní cestu k připojenému vzdálenému systému s daty

## <a name="copy-data-through-the-azure-hpc-cache"></a>Kopírování dat prostřednictvím mezipaměti HPC Azure

Pokud nechcete použít nástroj avere CLFSLoad, nebo pokud chcete do existujícího cíle úložiště objektů BLOB přidat velké množství dat, můžete ho zkopírovat přes mezipaměť. Mezipaměť HPC Azure je navržená tak, aby sloužila více klientům současně, takže ke kopírování dat prostřednictvím mezipaměti byste měli použít paralelní zápisy z více klientů.

![Diagram znázorňující pohyb vícevláknových dat s více klienty: vlevo nahoře je ikona pro místní hardwarové úložiště s více šipkami. Šipky ukazují na čtyři klientské počítače. Z každého klientského počítače tři šipky směřuje k mezipaměti HPC Azure. Z mezipaměti HPC Azure se několik šipek odkazuje na úložiště objektů BLOB.](media/hpc-cache-parallel-ingest.png)

``cp``Příkazy nebo ``copy`` , které obvykle slouží k přenosu dat z jednoho úložného systému do jiného, jsou procesy s jedním vláknem, které kopírují pouze jeden soubor v jednom okamžiku. To znamená, že souborový server bude v jednom okamžiku uchovávat pouze jeden soubor, což je odpad z prostředků mezipaměti.

V této části se dozvíte o strategiích pro vytvoření vícevláknového systému kopírování souborů s více vlákny pro přesun dat do úložiště objektů BLOB s využitím Azure HPC cache. Vysvětluje koncepty přenosu souborů a body rozhodování, které lze použít k efektivnímu kopírování dat pomocí více klientů a jednoduchých příkazů kopírování.

Vysvětluje taky některé nástroje, které vám pomůžou. ``msrsync``Nástroj lze použít k částečnému automatizaci procesu rozdělení datové sady do kontejnerů a používání příkazů rsync. Tento ``parallelcp`` skript je další nástroj, který čte zdrojový adresář a automaticky vystavuje příkazy kopírování.

### <a name="strategic-planning"></a>Strategické plánování

Při sestavování strategie pro paralelní kopírování dat byste měli pochopit kompromisy v velikosti souborů, počtu souborů a hloubkě adresáře.

* Když jsou soubory malé, je metrika zájmu soubory za sekundu.
* Když jsou soubory velké (10MiBi nebo větší), je metrika zájmu v bajtech za sekundu.

Každý proces kopírování má míru propustnosti a přenosovou rychlost přenosu souborů, která se dá změřit časováním příkazu pro kopírování a určením velikosti souboru a počtu souborů. Vysvětluje, jak změřit sazby jsou mimo rozsah tohoto dokumentu, ale je důležité pochopit, zda budete pracovat s malými nebo velkými soubory.

Strategie paralelního příjmu dat s mezipamětí služby Azure HPC cache zahrnují:

* Ruční kopírování – vícevláknové kopírování můžete vytvořit ručně na straně klienta spuštěním více než jednoho příkazu kopírování na pozadí v porovnání s předdefinovanými sadami souborů nebo cest. Přečtěte si podrobnosti v tématu [Azure HPC cache ingestování – metoda ručního kopírování](hpc-cache-ingest-manual.md) .

* Částečně automatizované kopírování s nástrojem ``msrsync``  -  ``msrsync`` je Obálkový nástroj, který spouští více paralelních ``rsync`` procesů. Podrobnosti si můžete přečíst v tématu [Azure HPC cache data ingestování – metoda msrsync](hpc-cache-ingest-msrsync.md).

* Skriptované kopírování pomocí ``parallelcp`` – Zjistěte, jak vytvořit a spustit skript paralelního kopírování v [Azure HPC cache – paralelní kopírování metody skriptu](hpc-cache-ingest-parallelcp.md).

## <a name="next-steps"></a>Další kroky

Po nastavení úložiště se dozvíte, jak můžou klienti připojit mezipaměť.

* [Přístup k systému mezipaměti HPC Azure](hpc-cache-mount.md)
