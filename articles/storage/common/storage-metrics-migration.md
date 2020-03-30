---
title: Migrace metrik Azure Storage | Dokumenty společnosti Microsoft
description: Zjistěte, jak migrovat staré metriky na nové metriky, které spravuje Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 537369c9466b1083723642ec9e93fcdf25056c5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68855345"
---
# <a name="azure-storage-metrics-migration"></a>Migrace metrik Azure Storage

V souladu se strategií sjednocení prostředí monitoru v Azure integruje Azure Storage metriky do platformy Azure Monitor. V budoucnu služba starých metrik skončí s včasné oznámení založené na zásadách Azure. Pokud spoléháte na staré metriky úložiště, musíte migrovat před datem ukončení služby, abyste mohli udržovat informace o metrikách.

Tento článek ukazuje, jak migrovat ze starých metrik na nové metriky.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Vysvětlení starých metrik spravovaných službou Azure Storage

Azure Storage shromažďuje staré hodnoty metrika a agreguje a ukládá je do $Metric tabulek v rámci stejného účtu úložiště. K nastavení monitorovacího grafu můžete použít portál Azure. Sady Azure Storage SDK můžete také použít ke čtení dat z $Metric tabulek, které jsou založené na schématu. Další informace naleznete v [tématu Storage Analytics](./storage-analytics.md).

Staré metriky poskytují metriky kapacity jenom v úložišti objektů blob Azure. Staré metriky poskytují metriky transakcí v úložišti objektů Blob, úložišti tabulek, azure ových souborech a úložišti front.

Staré metriky jsou navrženy v plochéschéma. Návrh má za následek nulovou hodnotu metriky, pokud nemáte vzory provozu, které by metriku spouštějí. Například hodnota **ServerTimeoutError** je nastavena na hodnotu 0 v $Metric tabulek, i když neobdržíte žádné chyby časového času serveru z živého provozu na účet úložiště.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Vysvětlení nových metrik spravovaných službou Azure Monitor

Pro nové metriky úložiště Azure Storage vyzařuje data metriky do back-endu Azure Monitoru. Azure Monitor poskytuje jednotné monitorování prostředí, včetně dat z portálu, jakož i přijím dat. Další podrobnosti naleznete v tomto [článku](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Nové metriky poskytují metriky kapacity a metriky transakcí na blob, tabulka, soubor, fronty a úložiště premium.

Multi dimenze je jednou z funkcí, které Azure Monitor poskytuje. Azure Storage přijímá návrh při definování nového schématu metriky. Pro podporované dimenze na metriky, můžete najít podrobnosti v [metriky Azure Storage v Azure Monitoru](./storage-metrics-in-azure-monitor.md). Vícerozměrný návrh zajišťuje nákladovou efektivitu jak šířky pásma z požití, tak kapacity z ukládání metrik. V důsledku toho, pokud váš provoz neaktivoval související metriky, související data metriky nebudou generovány. Například pokud váš provoz nevyvolala žádné chyby časového limitu serveru, Azure Monitor nevrátí žádná data při dotazu na hodnotu **metriky transakce** s dimenzí **ResponseType** rovná **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Mapování metrik mezi starými a novými metrikami

Pokud čtete data metriky programově, je třeba přijmout nové schéma metriky v programech. Chcete-li lépe porozumět změnám, můžete odkazovat na mapování uvedené v následující tabulce:

**Metriky kapacity**

| Stará metrika | Nová metrika |
| ------------------- | ----------------- |
| **Kapacita**            | **BlobCapacity** s dimenzí **Objekt blobTyp** rovná **BlockBlob** nebo **PageBlob** |
| **Počet objektů**        | **BlobCount** s dimenzí **Objekt blobTyp** rovný **BlockBlob** nebo **PageBlob** |
| **ContainerCount**      | **ContainerCount** |

Následující metriky jsou nové nabídky, které staré metriky nepodporují:
* **TabulkaKapacita**
* **Počet tabulek**
* **Počet tabulkových entií**
* **QueueCapacity**
* **Počet front**
* **QueueMessageCount**
* **Souborkapacita**
* **Počet souborů**
* **FileShareCount**
* **Usedcapacity**

**Metriky transakcí**

| Stará metrika | Nová metrika |
| ------------------- | ----------------- |
| **Chyba anonymníautorizace** | Transakce s dimenzí **ResponseType** rovnou **AuthorizationError** a dimenze **Ověřování** rovno **Anonymní** |
| **AnonymníChyba _Klienta OtherError** | Transakce s dimenzí **ResponseType** rovno **ClientOtherError** a **ověřování** dimenze rovné **anonymní** |
| **Chyba AnonymousClientTimeoutError** | Transakce s dimenzí **ResponseType** se rovná **ClientTimeoutError** a dimenze **Ověřování** rovná **anonymní** |
| **Chyba anonymní sítě** | Transakce s dimenzí **ResponseType** rovnou **networkerror** a dimenze **ověřování** rovná **anonymní** |
| **AnonymníServerOtherError** | Transakce s dimenzí **ResponseType** rovnou **serveremOtherError** a **ověřováním** dimenze rovným **anonymním** |
| **Chyba AnonymousServerTimeoutError** | Transakce s dimenzí **ResponseType** rovnou **serveremTimeoutError** a **ověřováním** dimenze se rovná **anonymnímu** |
| **AnonymníÚspěch** | Transakce s dimenzí **ResponseType** rovno **Úspěch** a **ověřování** dimenze rovné **anonymní** |
| **Chyba AnonymousThrottling** | Transakce s dimenzí **ResponseType** se rovnají **Chybě KlientTrottlingError** nebo **ServerBusyError** a **ověřování** dimenze rovné **anonymní** |
| **Chyba autorizace** | Transakce s dimenzí **Type responsetype** rovná **AuthorizationError** |
| **Dostupnost** | **Dostupnost** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **Chyba ClientOtherError** | Transakce s dimenzí **Type ResponseType** rovnoClientOtherError **ClientOtherError** |
| **Chyba ClientTimeoutError** | Transakce s dimenzí **Type ResponseType** rovná **ClientTimeoutError** |
| **Chyba sítě** | Transakce s dimenzí **ResponseType** rovno **Chybou NetworkError** |
| **Chyba procentaauthorization** | Transakce s dimenzí **Type responsetype** rovná **AuthorizationError** |
| **Chyba _A) _1000 000 00** | Transakce s dimenzí **Type ResponseType** rovnoClientOtherError **ClientOtherError** |
| **Chyba _a) je v procentech sítě** | Transakce s dimenzí **ResponseType** rovno **Chybou NetworkError** |
| **Chyba Procento_serveru OtherError** | Transakce s dimenzí **ResponseType** **rovnou_chybě_serveru_Serveru_MA** |
| **ProcentoÚspěch** | Transakce s dimenzí **ResponseType** rovná **úspěch** |
| **Chyba PercentThrottlingError** | Transakce s dimenzi **ResponseType** rovnou **chybě ClientThrottlingError** nebo **ServerBusyError** |
| **Chyba PercentTimeoutError** | Transakce s dimenzí **Type ResponseType** rovná **ServerTimeoutError** nebo **ResponseType** rovná **ClientTimeoutError** |
| **Chyba autorizace SAS** | Transakce s dimenzí **ResponseType** rovno **Chybám AuthorizationError** a **ověřováním** dimenze rovným **SAS** |
| **SASClientOtherError** | Transakce s dimenzí **ResponseType** rovno **ClientOtherError** a dimenze **Ověřování** rovno **SAS** |
| **Chyba SASClientTimeoutError** | Transakce s dimenzí **ResponseType** se rovnají **ClientTimeoutError** a dimenze **Ověřování** rovná **SAS** |
| **Chyba spřipisuje síť SAS** | Transakce s dimenzí **ResponseType** rovnou **networkerror** a dimenze **ověřování** rovná **SAS** |
| **SASServerOtherError** | Transakce s dimenzí **ResponseType** rovnou **serverem OtherError** a dimenze **Ověřování** rovná **SAS** |
| **Chyba SASServerTimeoutError** | Transakce s dimenzí **ResponseType** se rovnají **serveru ServerTimeoutError** a **ověřování** dimenze rovné **SAS** |
| **Úspěch SAS** | Transakce s dimenzí **ResponseType** rovno **Úspěch** a **ověřování** dimenze rovné **SAS** |
| **Chyba SASThrottlingError** | Transakce s dimenzí **ResponseType** se rovnají **Chybě KlientTrottlingError** nebo **ServerBusyError** a dimenze **Ověřování** rovná **SAS** |
| **Chyba_other_serveru** | Transakce s dimenzí **ResponseType** **rovnou_chybě_serveru_Serveru_MA** |
| **Chyba_časového_outu_** | Transakce s dimenzí **ResponseType** rovnou **chybě ServerTimeoutError** |
| **Úspěch** | Transakce s dimenzí **ResponseType** rovná **úspěch** |
| **ThrottlingError** | **Transakce** s dimenzi **ResponseType** rovnou **chybě ClientThrottlingError** nebo **ServerBusyError**|
| **TotalBillablePožadavky** | **Transakce** |
| **TotalEgress** | **Výchozí přenos dat** |
| **TotalIngress** | **Příchozí přenos dat** |
| **TotalRequests** | **Transakce** |

## <a name="faq"></a>Nejčastější dotazy

### <a name="how-should-i-migrate-existing-alert-rules"></a>Jak mám migrovat existující pravidla výstrah?

Pokud jste vytvořili klasická pravidla výstrah na základě starých metrik úložiště, musíte vytvořit nová pravidla výstrah na základě nového schématu metriky.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Jsou nová metrická data uložena ve stejném účtu úložiště ve výchozím nastavení?

Ne. Pokud chcete data metriky archivovat do účtu úložiště, použijte [rozhraní AZURE Monitor Diagnostic Setting API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Další kroky

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Metriky úložiště ve službě Azure Monitor](./storage-metrics-in-azure-monitor.md)
