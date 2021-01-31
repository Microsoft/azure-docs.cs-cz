---
title: Analýza úložiště Azure metriky (klasické)
description: Naučte se používat Analýza úložiště metriky v Azure Storage. Seznamte se s metrikami transakcí a kapacity, o tom, jak se ukládají metriky, umožňují metriky a další.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 8f698aadc24d0dc0691743f1d8dd54c5d5fd287e
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220952"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Analýza úložiště Azure metriky (klasické)

Od **31. srpna 2023** analýza úložiště metriky, označované také jako *klasické metriky* , budou vyřazeny. Další informace najdete v [oficiálním oznámení](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Pokud používáte klasické metriky, nezapomeňte před tímto datem přejít na metriky ve službě Azure Monitor. Tento článek vám s přechodem pomůže. 

Azure Storage používá řešení Analýza úložiště k ukládání metrik, které zahrnují agregovanou statistiku transakcí a data o kapacitě žádostí do služby úložiště. Transakce jsou hlášeny na úrovni operace rozhraní API a na úrovni služby úložiště. Kapacita se oznamuje na úrovni služby úložiště. Data metrik se dají použít k těmto akcím:
- Analýza využití služby úložiště
- Diagnostikujte problémy s požadavky provedenými u služby úložiště.
- Zvyšte výkon aplikací, které používají službu.

 Pro nové účty úložiště jsou ve výchozím nastavení povolené metriky Analýza úložiště. Metriky můžete nakonfigurovat v [Azure Portal](https://portal.azure.com/), pomocí PowerShellu nebo pomocí Azure CLI. Podrobné pokyny najdete v tématu [povolení a správa Azure Storagech analytických metrik (Classic)](./storage-monitor-storage-account.md). Analýza úložiště můžete také povolit programově prostřednictvím REST API nebo klientské knihovny. K povolení Analýza úložiště pro každou službu použijte operace set Service Properties.  

> [!NOTE]
> K dispozici jsou Analýza úložiště metriky pro Azure Blob Storage, Azure Queue Storage, Azure Table Storage a Azure Files.
> Metriky Analýza úložiště jsou teď klasickými metrikami. Místo Analýza úložištěch metrik doporučujeme použít [metriky úložiště v Azure monitor](../blobs/monitor-blob-storage.md) .

## <a name="transaction-metrics"></a>Transakční metriky  
 Robustní sada dat se zaznamenává v hodinovém nebo minutovém intervalu pro každou službu úložiště a požadovanou operaci rozhraní API, která zahrnuje příchozí a odchozí přenosy, dostupnost, chyby a procentní podíly požadavků na kategorizované. Úplný seznam podrobností o transakcích najdete v tématu [Analýza úložiště schéma tabulky metrik](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

 Data transakce se zaznamenávají na úrovni služby a na úrovni operace rozhraní API. Na úrovni služby jsou statistiky, které shrnují všechny požadované operace rozhraní API, zapisovány do entity tabulky každou hodinu, a to i v případě, že služba nevytvořila žádné požadavky. Na úrovni operace rozhraní API se Statistika zapisuje jenom na entitu, pokud se operace v této hodině vyžádala.  

 Pokud třeba ve službě BLOB Service provedete operaci **getblob** , analýza úložiště metriky protokolují požadavek a budou ho zahrnovat do agregovaných dat pro službu BLOB Service a operaci **getblob** . Pokud se během hodiny nepožaduje žádná operace **getblob** , pro tuto operaci není zadána entita pro *$MetricsTransactionsBlob* .  

 Pro požadavky uživatelů a požadavky vytvořené Analýza úložiště samotné se zaznamenávají transakční metriky. Například požadavky, které Analýza úložiště k zápisu protokolů a entit tabulek, se zaznamenávají.

## <a name="capacity-metrics"></a>Kapacitní metriky  

> [!NOTE]
>  V současné době jsou metriky kapacity k dispozici pouze pro službu BLOB Service.

 Data o kapacitě se zaznamenávají denně pro službu BLOB účtu úložiště a napíší se dvě entity tabulky. Jedna entita poskytuje statistiku pro uživatelská data a druhá poskytuje statistiku o `$logs` kontejneru objektů blob, který používá analýza úložiště. Tabulka *$MetricsCapacityBlob* obsahuje následující statistiky:  

- **Kapacita**: velikost úložiště využitá službou BLOB účtu úložiště v bajtech.  
- **ContainerCount**: počet kontejnerů objektů BLOB ve službě BLOB účtu úložiště.  
- **ObjectCount**: počet potvrzených a nepotvrzených objektů blob bloku nebo stránky ve službě BLOB účtu úložiště.  

  Další informace o metrikách kapacity najdete v tématu [Analýza úložiště schéma tabulky metrik](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Jak jsou uloženy metriky  

 Všechna data metrik pro každou ze služeb úložiště se ukládají ve třech tabulkách rezervovaných pro danou službu. Jedna tabulka je určena pro informace o transakcích, jedna tabulka je určena pro minutové informace o transakcích a další tabulka slouží k informacím o kapacitě. Informace o transakcích a minutách transakce se skládají z dat požadavků a odpovědí. Informace o kapacitě se skládají z dat využití úložiště. V tabulkách, které jsou popsány v následující tabulce, je k dispozici metrika hodin, minutové metriky a kapacita služby BLOB účtu úložiště.  

|Úroveň metrik|Názvy tabulek|Podporováno pro verze|  
|-------------------|-----------------|----------------------------|  
|Hodinové metriky, primární umístění|-$MetricsTransactionsBlob<br />-$MetricsTransactionsTable<br />-$MetricsTransactionsQueue|Verze starší než 15. srpna 2013. I když jsou tyto názvy stále podporovány, doporučujeme, abyste se přepnuli na použití tabulek, které následují.|  
|Hodinové metriky, primární umístění|-$MetricsHourPrimaryTransactionsBlob<br />-$MetricsHourPrimaryTransactionsTable<br />-$MetricsHourPrimaryTransactionsQueue<br />-$MetricsHourPrimaryTransactionsFile|Všechny verze. Podpora pro metriky souborové služby je k dispozici pouze ve verzi 5. dubna 2015 a novější.|  
|Minutové metriky, primární umístění|-$MetricsMinutePrimaryTransactionsBlob<br />-$MetricsMinutePrimaryTransactionsTable<br />-$MetricsMinutePrimaryTransactionsQueue<br />-$MetricsMinutePrimaryTransactionsFile|Všechny verze. Podpora pro metriky souborové služby je k dispozici pouze ve verzi 5. dubna 2015 a novější.|  
|Hodinové metriky, sekundární umístění|-$MetricsHourSecondaryTransactionsBlob<br />-$MetricsHourSecondaryTransactionsTable<br />-$MetricsHourSecondaryTransactionsQueue|Všechny verze. Musí být povolená geograficky redundantní replikace s přístupem pro čtení.|  
|Minutové metriky, sekundární umístění|-$MetricsMinuteSecondaryTransactionsBlob<br />-$MetricsMinuteSecondaryTransactionsTable<br />-$MetricsMinuteSecondaryTransactionsQueue|Všechny verze. Musí být povolená geograficky redundantní replikace s přístupem pro čtení.|  
|Kapacita (pouze služba BLOB Service)|$MetricsCapacityBlob|Všechny verze.|  

 Tyto tabulky se automaticky vytvoří, když je povolený Analýza úložiště pro koncový bod služby úložiště. Jsou k nim přistupované prostřednictvím oboru názvů účtu úložiště, například `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")` . Tabulky metrik se nezobrazují v operaci výpisu a musí být dostupné přímo prostřednictvím názvu tabulky.

## <a name="metrics-alerts"></a>Výstrahy metrik
Zvažte nastavení výstrah v [Azure Portal](https://portal.azure.com) , takže budete automaticky upozorňováni na důležité změny v chování vašich služeb úložiště. Podrobné pokyny najdete v tématu [vytváření výstrah metrik](storage-monitor-storage-account.md#create-metric-alerts).

Použijete-li nástroj Průzkumník služby Storage ke stažení těchto dat metrik ve formátu s oddělovači, můžete data analyzovat pomocí aplikace Microsoft Excel. Seznam dostupných Průzkumník služby Storage nástrojů najdete v tématu [Azure Storage klientských nástrojů](./storage-explorers.md).

> [!IMPORTANT]
> Může dojít ke zpoždění mezi událostí úložiště a při nahrávání odpovídajících hodinových dat metriky nebo minut. V případě minutové metriky je možné zapsat několik minut dat najednou. Tento problém může vést k tomu, že se transakce z předchozích minut agreguje do transakce pro aktuální minutu. Pokud k tomuto problému dojde, služba Výstrahy nemusí mít všechna dostupná data metriky pro nakonfigurovaný interval upozornění, což by mohlo vést k neočekávanému spouštění výstrah.
>

## <a name="billing-on-storage-metrics"></a>Fakturace v metrikách úložiště
Požadavky na zápis pro vytváření entit tabulek pro metriky se účtují podle standardních sazeb platných pro všechny operace Azure Storage.  

Žádosti o čtení a odstranění požadavků na metriky klienta jsou také Fakturovatelné se standardními sazbami. Pokud jste nakonfigurovali zásady uchovávání dat, neúčtují se vám Azure Storage odstraní stará data metrik. Pokud odstraníte analytické údaje, účtují se vám za operace odstranění účtu.  

Kapacita využívaná tabulkami metriky je také fakturovatelná. Pomocí následujících informací můžete odhadnout množství kapacity využité k ukládání dat metrik:  

-   Pokud je každé hodiny, kdy služba využívá každé z rozhraní API každé služby, v tabulkách transakcí metriky se každý den ukládá přibližně 148 KB dat, pokud jste povolili souhrn na úrovni služby a rozhraní API.  
-   Pokud v rámci každé hodiny služba využívá každé rozhraní API ve službě, v tabulkách transakcí metriky se v případě, že jste povolili jenom souhrn na úrovni služby, ukládá přibližně 12 KB dat každou hodinu.  
-   V tabulce Capacity pro objekty BLOB se každý den přidaly dva řádky, pro které jste se rozhodli o protokolech. Tento scénář předpokládá, že velikost této tabulky se zvyšuje až o přibližně 300 bajtů.

## <a name="next-steps"></a>Další kroky
* [Monitorování účtu úložiště](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Schéma tabulky Analýza úložiště metriky](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Analýza úložiště protokolované operace a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Protokolování Analýza úložiště](storage-analytics-logging.md)