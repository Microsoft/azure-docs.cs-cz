---
title: Pořadí zpracování událostí a zpoždění v Azure Stream Analytics
description: Tento článek popisuje, jak Stream Analytics zpracovává události mimo pořadí nebo pozdní v datových proudů.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: e407a95d3ac858ea7180a75f9fbfc399860ad378
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="azure-stream-analytics-event-order-considerations"></a>Důležité informace pořadí událostí Azure Stream Analytics

## <a name="arrival-time-and-application-time"></a>Čas doručení a doba aplikace

V dočasné datovém proudu událostí je přiřazen každé události časové razítko. Azure Stream Analytics přiřadí časové razítko jednotlivých událostí pomocí buď příchodem času nebo času aplikace. **System.Timestamp** má sloupec časového razítka přiřazené k události. 

Čas doručení přiřazen za vstupní zdroj, když událost dosáhne zdroji. Máte přístup k čas doručení pomocí **EventEnqueuedTime** vlastnost pro událost rozbočovače vstup a použití [BlobProperties.LastModified](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) vlastnost pro vstup objektů blob. 

Čas aplikace je přiřazena, když se vygeneruje událost a je součástí datové části. Ke zpracování události doba aplikace, použijte **časové razítko podle** klauzuli select dotazu. Pokud **časové razítko podle** chybí klauzule, zpracovává události čas doručení. 

Azure Stream Analytics vytvoří výstupní v pořadí, časové razítko a poskytuje nastavení, jak nakládat s daty na více systémů pořadí.


## <a name="handling-of-multiple-streams"></a>Zpracování víc datových proudů

Úlohu služby Azure Stream Analytics kombinuje události z více časové osy v případech, jako jsou následující:

* Vytváření výstupu z více oddílů. Dotazy, které nemají explicitního **oddílu podle PartitionId** klauzule musíte kombinovat události ze všech oddílů.
* Sjednocení dvou nebo více různých vstupních zdrojů.
* Připojení vstupního zdroje.

Ve scénářích, kde jsou kombinovat více časových os, Azure Stream Analytics vytvoří výstupní pro časové razítko *t1* až po všechny zdroje, které jsou spojené se minimálně v době *t1*. Předpokládejme například, že dotaz načte z oddílu centra událostí, která má dva oddíly. Jeden z oddílů, *P1*, má událostí až při *t1*. Další oddíl *P2*, má událostí až při *t1 + x*. Potom výstupu až při *t1*. Ale pokud je explicitního **oddílu podle PartitionId** klauzuli oba oddíly průběhu nezávisle.

Nastavení pro pozdní příchodem tolerance se používá jak nakládat s absenci data v některé oddíly.

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>Konfigurace pozdní příchodem proti chybám a tolerance mimo pořadí
Vstupní datové proudy, které nejsou v pořadí jsou buď:
* Seřadit (a proto odložené)
* Upravit v systému podle zásad definovaných uživatelem

Stream Analytics toleruje pozdní a na více systémů pořadí událostí v případě, že jste zpracování podle času aplikace. Následující nastavení jsou k dispozici v **řazení událostí** možnost na portálu Azure: 

![Stream Analytics zpracování událostí](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

### <a name="late-arrival-tolerance"></a>Pozdní příchodem toleranci
Pozdní tolerance příchodem lze použít pouze v případě, že jste zpracování podle času aplikace. Nastavení, jinak hodnota se ignoruje.

Pozdní příchodem tolerance je maximální rozdíl mezi čas doručení a aplikace. Pokud události dorazí později než pozdní tolerance příchodem (například doba aplikace *app_t* < čas doručení *arr_t* -pozdní tolerance zásady doručení *late_t*), událost se upraví na maximální tolerance pozdní příchodem (*arr_t* - *late_t*). Okno pozdní příchodem je maximální zpoždění mezi vytvořením událostí a přijetí události v vstupního zdroje. 

Když se kombinuje více oddílů ze stejného vstupního datového proudu nebo více vstupní datové proudy, pozdní tolerance příchodem je maximální množství času, kterou každý oddíl čeká na nová data. 

Nejprve se stane úpravu podle pozdní příchodem tolerance. Úpravy na tolerance pořadí se na základě bude následovat. **System.Timestamp** sloupce obsahuje poslední časové razítko přiřazené k události.

### <a name="out-of-order-tolerance"></a>Tolerance mimo pořadí
Události, které dorazí mimo pořadí, ale v rámci interval tolerance pořadí se na sadu přeuspořádají podle časového razítka. Jsou události, které přicházejí později než interval tolerance buď:
* **Upraví**: upravený tak, aby se zobrazí určeny na nejnovější přijatelný čas. 
* **Vyřadit**: zahozeny.

Když Stream Analytics změní události, které jsou přijaty v rámci interval tolerance pořadí se na více systémů, výstup tohoto dotazu zpožděn interval tolerance mimo pořadí.

### <a name="early-events"></a>Časná události
Při zpracování aplikace doby, události, jejichž doba aplikace je více než 5 minut před jejich čas doručení jsou vyřazeny nebo upravena podle možnosti konfigurace vybrané.

### <a name="example"></a>Příklad:

* Pozdní tolerance příchodem = 10 minut<br/>
* Tolerance mimo pořadí = 3 minut<br/>
* Zpracování doba aplikace<br/>
* Události:
   1. **Doba aplikace** = 00:00:00, **čas doručení** = 00:10:01 **System.Timestamp** = 00:00:01, upravit, protože (**čas doručení - doba aplikace**) je více než pozdní příchodem tolerance.
   2. **Doba aplikace** = 00:00:01 **čas doručení** = 00:10:01 **System.Timestamp** = 00:00:01, není upraven, protože doba aplikace se v okně pozdní doručení.
   3. **Doba aplikace** = 00:10:00, **čas doručení** = 00:10:02 **System.Timestamp** = 00:10:00, není upraven, protože doba aplikace se v okně pozdní doručení.
   4. **Doba aplikace** = 00:09:00, **čas doručení** = 00:10:03 **System.Timestamp** = 00:09:00, přijato s původní časové razítko, protože doba aplikace je v rámci na více systémů z pořadí tolerance.
   5. **Doba aplikace** = 00:06:00, **čas doručení** = 00:10:04 **System.Timestamp** = 00:07:00, upravit, protože doba aplikace je starší než tolerance mimo pořadí.

### <a name="practical-considerations"></a>Praktické informace
Jak už bylo zmíněno dříve, pozdní příchodem tolerance je maximální rozdíl mezi aplikací čas a čas doručení. Při zpracování aplikace doby jsou události, které jsou novější než nakonfigurovanou toleranci pozdní příchodem upraví před použitím nastavení tolerance mimo pořadí. Ano platné mimo pořadí je minimum pozdní příchodem proti chybám a tolerance mimo pořadí.

Pro události mimo pořadí v rámci datového proudu z těchto důvodů:
* Posun mezi odesílatelé hodin.
* Variabilní latence mezi odesílatele a zdroj vstupní události.

Pro opožděné předání z těchto důvodů:
* Odesílatelé dávkování a odesílání události pro časový interval, později, po uplynutí intervalu.
* Latence mezi událost odesílatel odesílání a příjmu událostí v vstupního zdroje.

Když konfigurujete pozdní příchodem proti chybám a tolerance mimo pořadí v rámci konkrétní úlohy, zvažte správnost, požadavky na latenci a předchozí faktorů.

Následuje několik příkladů.

#### <a name="example-1"></a>Příklad 1 
Dotaz musí **oddílu podle PartitionId** klauzule. V rámci jednoho oddílu jsou odesílány události prostřednictvím metody synchronní odesílání. Synchronní odesílání metody blok dlouho, dokud se neodešle události.

V takovém případě mimo pořadí totiž nula události jsou zasílány v pořadí s explicitní potvrzení před odesláním následující události. Opožděné předání je maximální zpoždění mezi generování události a odesílá události plus maximální latence mezi odesílateli a vstupního zdroje.

#### <a name="example-2"></a>Příklad 2
Dotaz musí **oddílu podle PartitionId** klauzule. V rámci jednoho oddílu jsou odesílány události prostřednictvím metody asynchronní odesílání. Asynchronní odesílání metody můžete spustit více zasílá ve stejnou dobu, což může způsobit události mimo pořadí.

V tomto případě doručení mimo pořadí a pozdní jsou alespoň Maximální zpoždění mezi generování události a odesílá události plus maximální latence mezi odesílateli a vstupního zdroje.

#### <a name="example-3"></a>Příklad 3
Dotaz nemá **oddílu podle PartitionId** klauzule, a nejsou alespoň dva oddíly.

Konfigurace je stejný jako příklad 2. Neexistence data v jedné z oddílů však zpozdit výstup v závislosti další interval tolerance pozdního.

## <a name="handling-event-producers-with-differing-timelines"></a>Zpracování událostí producenti s odlišnými časové osy
Datový proud s jedna vstupní událost často obsahují události, které pocházejí z více událostí výrobců, jako jsou například jednotlivých zařízení. Tyto události nemusí být dodány mimo pořadí z důvodů uvedených dříve. V těchto scénářích nepořádku napříč producenti události může být velký, nepořádku v rámci událostí od jednoho výrobce sice malých (nebo i neexistující).

Azure Stream Analytics poskytuje obecné mechanismy pro práci s událostmi mimo pořadí. Tyto mechanismy výsledek zpracování zpoždění (při čekání na straggling události k dosažení systému), vyřadit nebo upravena události nebo obojí.

Ještě v mnoha scénářích požadovaného dotazu je zpracování události z různých událostí producenti nezávisle. Například ho agregování událostí za období, za zařízení. V těchto případech je potřeba zpoždění výstupu, který odpovídá jedné události producent při čekání na jiných výrobců událostí k zachycení. Jinými slovy není třeba řešit zkosení mezi producenti čas. Můžete ji ignorovat.

Samozřejmě to znamená, že jsou mimo pořadí s ohledem na jejich časová razítka výstupních událostech sami. Podřízené příjemce musí být schopen řešit takové chování. Ale každé události ve výstupu je správný.

Azure Stream Analytics implementuje pomocí této funkce [TIMESTAMP BY OVER](https://msdn.microsoft.com/library/azure/mt573293.aspx) klauzule.

## <a name="summary"></a>Souhrn
* Nakonfigurujte pozdní příchodem proti chybám a okno mimo pořadí na základě správnost a požadavky na latenci. Také zvážit, jak jsou odesílány události.
* Doporučujeme vám, že tolerance pořadí se na více systémů je menší než pozdní příchodem tolerance.
* Když jste kombinování více časových os, nedostatku dat v jednom ze zdroje nebo oddíly zpozdit výstup v závislosti další interval tolerance pozdního.

## <a name="get-help"></a>Podpora
O další pomoc, zkuste [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup
* [Úvod do služby Stream Analytics](stream-analytics-introduction.md)
* [Začínáme s Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování úlohy Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka jazyka dotazů Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics správu odkazu k REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
