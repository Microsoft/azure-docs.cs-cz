---
title: Latence v úložišti objektů blob – Azure Storage
description: Pochopení a měření latence pro operace BLOB Storage a Naučte se navrhovat aplikace BLOB Storage pro nízkou latenci.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 09/05/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 78440b8150a0992bed2e2a3e597fdac8e7a1c7b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "75749727"
---
# <a name="latency-in-blob-storage"></a>Latence v úložišti objektů blob

Latence, někdy označovaná jako doba odezvy, je doba, po kterou musí aplikace čekat na dokončení žádosti. Latence může mít přímý vliv na výkon aplikace. Nízká latence je často důležitá pro scénáře s lidmi ve smyčce, jako je například provádění transakcí kreditních karet nebo načítání webových stránek. Systémy, které potřebují zpracovávat příchozí události s vysokými sazbami, jako je protokolování telemetrie nebo události IoT, vyžadují také nízkou latenci. Tento článek popisuje, jak pochopit a měřit latenci pro operace s objekty blob bloku a jak navrhovat aplikace pro nízkou latenci.

Azure Storage nabízí dvě různé možnosti výkonu pro objekty blob bloku: Premium a Standard. Objekty blob bloku úrovně Premium nabízejí výrazně nižší a jednotnější latenci než standardní objekty blob bloku prostřednictvím vysoce výkonných disků SSD. Další informace najdete v tématu věnovaném službě **Premium Performance Storage** v úložišti [objektů BLOB v Azure: horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md).

## <a name="about-azure-storage-latency"></a>O latenci Azure Storage

Azure Storage latence se týká sazeb požadavků pro operace Azure Storage. Sazby za požadavky se označují také jako vstupně-výstupní operace za sekundu (IOPS).

Chcete-li vypočítat míru požadavků, nejprve určete dobu, po kterou je každá žádost dokončena, a poté vypočítat počet požadavků, které mohou být zpracovány za sekundu. Předpokládejme například, že požadavek bude dokončen od 50 milisekund (MS). Aplikace, která používá jeden podproces s jednou zbývající operací čtení nebo zápisu, by měla dosáhnout 20 IOPS (1 sekunda nebo 1000 MS/50 MS za požadavek). Teoreticky, pokud je počet vláken dvojnásobný na dvě, aplikace by měla být schopná dosahovat 40 vstupně-výstupních operací. Pokud jsou nedokončené asynchronní operace čtení nebo zápisu pro každé vlákno dvojnásobné na dvě, aplikace by měla být schopná dosahovat 80 vstupně-výstupních operací.

V praxi se frekvence požadavků nikdy neškáluje tak lineárně, protože režie v klientovi při plánování úloh, přepínání kontextu a tak dále. Na straně služby se může při latenci vycházet z důvodu tlaku na Azure Storage systému, rozdílů v využitých úložných médiích, hluku z jiných úloh, úloh údržby a dalších faktorů. Připojení k síti mezi klientem a serverem může negativně ovlivnit Azure Storage latence v důsledku zahlcení, opětovného směrování nebo jiné přerušení.

Azure Storage šířka pásma, která se také označuje jako propustnost, se vztahuje na míru požadavků a je možné ji vypočítat vynásobením míry požadavků (IOPS) velikostí žádosti. Například předpokládá 160 požadavků za sekundu, přičemž každý 256 KiB dat vede k 40 960 propustnosti KiB za sekundu nebo 40 MiB za sekundu.

## <a name="latency-metrics-for-block-blobs"></a>Metriky latence pro objekty blob bloku

Azure Storage poskytuje dvě metriky latence pro objekty blob bloku. Tyto metriky lze zobrazit v Azure Portal:

- **Koncová latence (e2e)** měří interval z doby, kdy Azure Storage obdrží první paket požadavku, dokud Azure Storage neobdrží potvrzení klienta na posledním paketu odpovědi.

- **Latence serveru** měří interval z doby, kdy Azure Storage obdrží poslední paket požadavku, dokud se z Azure Storage nevrátí první paket odpovědi.

Následující obrázek ukazuje **průměrnou latenci úspěšnosti e2e** a **průměrnou latenci úspěšného serveru** pro ukázkovou úlohu, která volá `Get Blob` operaci:

![Snímek obrazovky znázorňující metriky latence pro operaci získání objektu BLOB](media/storage-blobs-latency/latency-metrics-get-blob.png)

Za běžných podmínek existuje málo mezer mezi koncovou latencí a latencí serveru, což je to, co obrázek zobrazuje pro ukázkovou úlohu.

Pokud provedete kompletní metriky a latence serveru a zjistíte, že se jedná o koncovou latenci výrazně vyšší než latence serveru, prozkoumejte a vyřešte zdroj další latence.

Pokud jsou vaše komplexní a latence serveru podobné, ale potřebujete nižší latenci, zvažte možnost migrace na úložiště objektů blob bloku Premium.

## <a name="factors-influencing-latency"></a>Faktory ovlivňující latenci

Hlavním faktorem, který má vliv na latenci, je velikost operace. Z důvodu množství dat přenášených přes síť a zpracovaných pomocí Azure Storage trvá déle, než se dokončí větší operace.

Následující diagram znázorňuje celkovou dobu provozu různých velikostí. V případě malých objemů dat je interval latence převážně strávený zpracováním žádosti místo přenosu dat. Interval latence se zvětšuje jenom mírně, protože se zvýší velikost operace (označená 1 v diagramu níže). Jak roste velikost operace, stráví více času přenášením dat, takže celkový interval latence je rozdělen mezi zpracováním požadavků a přenosem dat (označeno 2 v diagramu níže). Díky větší velikosti operací je interval latence skoro výhradně strávený na přenosu dat a zpracování žádosti je v podstatě nevýznamné (označeno 3 v diagramu níže).

![Snímek obrazovky znázorňující celkovou dobu operace podle velikosti operace](media/storage-blobs-latency/operation-time-size-chart.png)

Faktory konfigurace klienta, jako je souběžnost a dělení na vlákna, ovlivňují také latenci. Celková propustnost závisí na tom, kolik požadavků na úložiště se v daném časovém okamžiku a na tom, jak vaše aplikace zpracovává. Latence také může ovlivnit prostředky klienta včetně CPU, paměti, místního úložiště a síťových rozhraní.

Zpracování žádostí Azure Storage vyžaduje prostředky procesoru a paměti klienta. Pokud je u klienta tlak v důsledku neznámého virtuálního počítače nebo nějakého procesu navýšení v systému, je k dispozici méně prostředků pro zpracování Azure Storagech požadavků. Jakákoli kolizí nebo nedostatek prostředků klienta bude mít za následek zvýšení celkové latence bez zvýšení latence serveru, přičemž se zvyšuje mezera mezi dvěma metrikami.

Stejně důležitá je síťové rozhraní a síťové přesměrování mezi klientem a Azure Storage. Samotná fyzická vzdálenost může být významným faktorem, například pokud se virtuální počítač klienta nachází v jiné oblasti Azure nebo v místním prostředí. Další faktory, jako je směrování sítě, směrování poskytovatele internetových služeb a stav Internetu, můžou mít vliv na celkovou latenci úložiště.

Pro vyhodnocení latence nejprve vytvořte základní metriky pro váš scénář. Metriky standardních hodnot poskytují očekávanou latenci a latenci serveru v kontextu prostředí vaší aplikace, v závislosti na vašem profilu úlohy, nastavení konfigurace aplikace, klientských zdrojích, síťovém kanálu a dalších faktorech. Máte-li metriky standardních hodnot, můžete snadněji identifikovat neobvyklé srovnání normálních podmínek. Metriky směrného plánu také umožňují sledovat účinky změněných parametrů, jako je například konfigurace aplikace nebo velikosti virtuálních počítačů.

## <a name="next-steps"></a>Další kroky

- [Škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](scalability-targets.md)
- [Kontrolní seznam pro výkon a škálovatelnost pro úložiště objektů BLOB](storage-performance-checklist.md)
