---
title: Latence v úložišti objektů Blob – Azure Storage
description: Seznamte se s latencí pro operace úložiště objektů Blob a mějte měření latence a zjistěte, jak navrhnout aplikace úložiště objektů Blob pro nízkou latenci.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 09/05/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 78440b8150a0992bed2e2a3e597fdac8e7a1c7b0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75749727"
---
# <a name="latency-in-blob-storage"></a>Latence v úložišti objektů blob

Latence, někdy odkazoval se na jako doba odezvy, je množství času, které aplikace musí čekat na dokončení požadavku. Latence může přímo ovlivnit výkon aplikace. Nízká latence je často důležité pro scénáře s lidmi ve smyčce, jako je například provádění transakcí kreditní kartou nebo načítání webových stránek. Systémy, které potřebují zpracovávat příchozí události vysokou rychlostí, jako je například protokolování telemetrie nebo události IoT, také vyžadují nízkou latenci. Tento článek popisuje, jak pochopit a měřit latenci pro operace s objekty BLOB bloku a jak navrhnout aplikace pro nízkou latenci.

Azure Storage nabízí dvě různé možnosti výkonu pro objekty BLOB bloku: premium a standard. Objekty BLOB premium bloku nabízejí výrazně nižší a konzistentnější latenci než objekty BLOB standardních bloků prostřednictvím vysoce výkonných disků SSD. Další informace najdete **v tématu Premium performance block blob storage** v [úložišti objektů Blob Azure: horké, studené a archivní úrovně přístupu](storage-blob-storage-tiers.md).

## <a name="about-azure-storage-latency"></a>O latenci úložiště Azure

Latence azure storage souvisí s tarify požadavků pro operace Azure Storage. Požadavky sazby jsou také označovány jako vstupní a výstupní operace za sekundu (VOPS).

Chcete-li vypočítat míru požadavků, nejprve určete dobu, kterou každý požadavek trvá dokončení, a poté vypočítejte, kolik požadavků lze zpracovat za sekundu. Předpokládejme například, že požadavek trvá 50 milisekund (ms) k dokončení. Aplikace používající jedno vlákno s jedním nevyřízeným čtením nebo zápisem by měla dosáhnout 20 VOPS (1 sekunda nebo 1000 ms / 50 ms na požadavek). Teoreticky, pokud je počet vláken zdvojnásoben na dva, pak by aplikace měla být schopna dosáhnout 40 VOPS. Pokud vynikající asynchronní čtení nebo zápis operace pro každé vlákno jsou zdvojnásobeny na dva, pak aplikace by měla být schopna dosáhnout 80 VOPS.

V praxi není míra požadavků vždy škálovat tak lineárně, z důvodu režie v klientovi z plánování úkolů, přepínání kontextu a tak dále. Na straně služby může být variabilita latence kvůli tlaku na systém Azure Storage, rozdílům v použitých médiích úložiště, šumu z jiných úloh, úlohám údržby a dalším faktorům. Nakonec síťové připojení mezi klientem a serverem může ovlivnit latenci úložiště Azure kvůli přetížení, přesměrování nebo jiným narušením.

Šířka pásma Azure Storage, označovaná také jako propustnost, souvisí s rychlostí požadavků a lze ji vypočítat vynásobením míry požadavků (IOPS) velikostí požadavku. Například za předpokladu, že 160 požadavků za sekundu, každý 256 KiB dat má za následek propustnost 40 960 KiB za sekundu nebo 40 MiB za sekundu.

## <a name="latency-metrics-for-block-blobs"></a>Metriky latence pro objekty BLOB bloku

Azure Storage poskytuje dvě metriky latence pro objekty BLOB bloku. Tyto metriky si můžete prohlédnout na webu Azure Portal:

- **End-to-end (E2E) latence měří** interval od kdy Azure Storage obdrží první paket požadavku, dokud Azure Storage obdrží potvrzení klienta na poslední paket odpovědi.

- **Latence serveru měří** interval od doby, kdy Azure Storage obdrží poslední paket požadavku, dokud se nevrátí první paket odpovědi z Azure Storage.

Následující obrázek znázorňuje **průměrnou latenci e2E** a **latenci serveru s průměrným úspěchem** pro ukázkovou úlohu, která operaci volá: `Get Blob`

![Snímek obrazovky s metrikami latence pro operaci Získat objekt Blob](media/storage-blobs-latency/latency-metrics-get-blob.png)

Za normálních podmínek je malá mezera mezi latencí od konce na konec a latencí serveru, což je to, co obrázek zobrazuje pro ukázkové zatížení.

Pokud zkontrolujete metriky latence od konce do konce a serveru a zjistíte, že latence od konce do konce je výrazně vyšší než latence serveru, pak prozkoumejte a adresujte zdroj další latence.

Pokud je latence od konce do konce a serveru podobná, ale vyžadujete nižší latenci, zvažte migraci do úložiště objektů blob s prémiovými bloky.

## <a name="factors-influencing-latency"></a>Faktory ovlivňující latenci

Hlavním faktorem ovlivňujícím latenci je velikost operace. Dokončení větších operací trvá déle vzhledem k množství dat přenášených po síti a zpracovávaných službou Azure Storage.

Následující diagram znázorňuje celkový čas operací různých velikostí. Pro malé množství dat interval latence je převážně vyčerpány zpracování požadavku, nikoli přenos dat. Interval latence se zvyšuje pouze mírně, jak se zvětšuje velikost operace (označeno 1 v diagramu níže). Jak se velikost operace dále zvyšuje, více času se vynakládá na přenos dat, takže celkový interval latence je rozdělen mezi zpracování požadavků a přenos dat (označeno 2 v diagramu níže). S větší velikosti operací interval latence je téměř výhradně vynaložena na přenos dat a zpracování požadavků je do značné míry nevýznamné (označené 3 v diagramu níže).

![Snímek obrazovky zobrazující celkovou dobu provozu podle velikosti operace](media/storage-blobs-latency/operation-time-size-chart.png)

Faktory konfigurace klienta, jako je souběžnost a podprocesy také ovlivnit latenci. Celková propustnost závisí na tom, kolik požadavků na úložiště je v daném okamžiku v daném okamžiku a jak vaše aplikace zpracovává zřetězení. Klientské prostředky včetně procesoru, paměti, místního úložiště a síťových rozhraní mohou také ovlivnit latenci.

Zpracování požadavků úložiště Azure vyžaduje klientské procesory a paměťové prostředky. Pokud je klient pod tlakem kvůli poddajnému virtuálnímu počítači nebo nějakému runaway procesu v systému, je k dispozici méně prostředků pro zpracování požadavků služby Azure Storage. Jakékoli tvrzení nebo nedostatek prostředků klienta bude mít za následek zvýšení latence end-to-end bez zvýšení latence serveru, zvýšení mezery mezi těmito dvěma metrikami.

Neméně důležité je síťové rozhraní a síťové potrubí mezi klientem a Azure Storage. Fyzická vzdálenost sama o sobě může být významným faktorem, například pokud je virtuální počítač klienta v jiné oblasti Azure nebo místně. Celkové latence úložiště mohou ovlivnit další faktory, jako je síťový směrování, směrování poskytovatelů internetových serverů a stav internetu.

Chcete-li posoudit latenci, nejprve vytvořte základní metriky pro váš scénář. Metriky směrného plánu poskytují očekávanou latenci začátku do konce a serveru v kontextu aplikačního prostředí v závislosti na profilu pracovního vytížení, nastavení konfigurace aplikace, klientských prostředkůch, síťovém kanálu a dalších faktorech. Pokud máte základní metriky, můžete snadněji identifikovat abnormální versus normální podmínky. Základní metriky také umožňují sledovat účinky změněných parametrů, jako je konfigurace aplikace nebo velikosti virtuálních počítačů.

## <a name="next-steps"></a>Další kroky

- [Škálovatelnost a výkonnostní cíle pro úložiště objektů Blob](scalability-targets.md)
- [Kontrolní seznam výkonu a škálovatelnosti pro úložiště objektů Blob](storage-performance-checklist.md)
