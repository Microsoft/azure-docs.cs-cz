---
title: Migrace metrik Azure Storage | Microsoft Docs
description: Zjistěte, jak migrovat staré metriky do nové metriky, které jsou spravovány nástrojem Azure monitorování.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 03/30/2018
ms.author: fryu
ms.openlocfilehash: c64061aee94e8c08a3f6bcae78cffca0b4172d97
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650668"
---
# <a name="azure-storage-metrics-migration"></a>Migrace metrik Azure Storage

V souladu s strategie sjednocujícího prostředí monitorování v Azure, Azure Storage integruje metriky pro platformu Azure monitorování. V budoucnu službu staré metrik bude končit včasné oznámení na základě zásad Azure. Pokud byste tedy spoléhat na staré metriky úložiště, budete muset migrovat před datem ukončení služby, aby byla zachována informace o metrikách.

Tento článek ukazuje, jak migrovat z původního metriky nové metriky.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Pochopení staré metriky, které jsou spravovány službou Azure Storage

Úložiště Azure shromažďuje staré metriky hodnoty a shromažďuje a ukládá je do $Metric tabulky ve stejném účtu úložiště. Na portálu Azure můžete použít k nastavení monitorování grafu. Sady SDK úložiště Azure můžete použít také ke čtení dat z $Metric tabulek, které jsou založeny na schéma. Další informace najdete v tématu [Storage Analytics](./storage-analytics.md).

Původní metriky zadejte metriky kapacity pouze v úložišti objektů Blob v Azure. Staré metriky uvádějí metriky transakce úložiště objektů Blob, Table storage, Azure soubory a Queue storage. 

Původní metriky jsou navrženy v ploché schématu. Návrh výsledkem hodnota nula metriky, pokud nemáte vzory přenosů dat aktivován metriku. Například **ServerTimeoutError** hodnota nastavena na 0 v tabulkách $Metric i v případě, že jste neobdrželi žádné chyby vypršení časového limitu serveru z provozu provoz na účet úložiště.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Pochopení nové metriky spravuje monitorování Azure

Pro nové metriky úložiště Azure Storage vysílá metriky dat do Azure monitorování back-end. Poskytuje jednotné monitorování prostředí, včetně dat z portálu Azure monitorování a také přijímat data. Další informace najdete na to [článku](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Nové metriky poskytovat metriky kapacity a metriky transakce na objekt Blob, Table, soubor, fronty a storage úrovně premium.

Více dimenze je jedna z funkcí, které poskytuje Azure monitorování. Úložiště Azure přijme návrhu při definování schématu metriky. Podporované dimenze na metriky můžete najít podrobnosti v [metrik Azure Storage v Azure monitorování](./storage-metrics-in-azure-monitor.md). Více dimenze design poskytuje efektivity nákladů na obou šířky pásma z přijímání a kapacity v ukládání metriky. V důsledku toho pokud provozu ještě aktivována související metriky, se nevygeneruje související data metriky. Například pokud provozu nebyla aktivována všechny chyby vypršení časového limitu serveru, monitorování Azure nevrací žádná data dotazu na hodnotu metriky **transakce** s dimenzí **ResponseType** rovno **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Metriky mapování mezi metriky staré a nové metriky

Pokud číst metriky data prostřednictvím kódu programu, budete muset přijmout nové metriky schéma ve vašich aplikacích. Abyste lépe pochopili změny, můžete odkazovat na mapování uvedené v následující tabulce:

**Metriky kapacity**

| Původní metrika | Nové metrika |
| ------------------- | ----------------- |
| **Kapacita**            | **BlobCapacity** s dimenzí **BlobType** rovna **BlockBlob** nebo **PageBlob** |
| **ObjectCount**        | **BlobCount** s dimenzí **BlobType** rovna **BlockBlob** nebo **PageBlob** |
| **ContainerCount**      | **ContainerCount** |

Následující metriky jsou nové nabídky, které nepodporují staré metriky:
* **TableCapacity**
* **TableCount**
* **TableEntityCount**
* **QueueCapacity**
* **QueueCount**
* **QueueMessageCount**
* **FileCapacity**
* **FileCount**
* **FileShareCount**
* **UsedCapacity**

**Metriky transakce**

| Původní metrika | Nové metrika |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | Transakce se dimenze **ResponseType** rovna **AuthorizationError** |
| **AnonymousClientOtherError** | Transakce se dimenze **ResponseType** rovna **ClientOtherError** |
| **AnonymousClientTimeoutError** | Transakce se dimenze **ResponseType** rovna **ClientTimeoutError** |
| **AnonymousNetworkError** | Transakce se dimenze **ResponseType** rovna **NetworkError** |
| **AnonymousServerOtherError** | Transakce se dimenze **ResponseType** rovna **ServerOtherError** |
| **AnonymousServerTimeoutError** | Transakce se dimenze **ResponseType** rovna **ServerTimeoutError** |
| **AnonymousSuccess** | Transakce se dimenze **ResponseType** rovna **úspěch** |
| **AnonymousThrottlingError** | Transakce se dimenze **ResponseType** rovna **ClientThrottlingError** nebo **ServerBusyError** |
| **AuthorizationError** | Transakce se dimenze **ResponseType** rovna **AuthorizationError** |
| **Dostupnost** | **Dostupnost** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **ClientOtherError** | Transakce se dimenze **ResponseType** rovna **ClientOtherError** |
| **ClientTimeoutError** | Transakce se dimenze **ResponseType** rovna **ClientTimeoutError** |
| **NetworkError** | Transakce se dimenze **ResponseType** rovna **NetworkError** |
| **PercentAuthorizationError** | Transakce se dimenze **ResponseType** rovna **AuthorizationError** |
| **PercentClientOtherError** | Transakce se dimenze **ResponseType** rovna **ClientOtherError** |
| **PercentNetworkError** | Transakce se dimenze **ResponseType** rovna **NetworkError** |
| **PercentServerOtherError** | Transakce se dimenze **ResponseType** rovna **ServerOtherError** |
| **PercentSuccess** | Transakce se dimenze **ResponseType** rovna **úspěch** |
| **PercentThrottlingError** | Transakce se dimenze **ResponseType** rovna **ClientThrottlingError** nebo **ServerBusyError** |
| **PercentTimeoutError** | Transakce se dimenze **ResponseType** rovna **ServerTimeoutError** nebo **ResponseType** rovna **ClientTimeoutError** |
| **SASAuthorizationError** | Transakce se dimenze **ResponseType** rovna **AuthorizationError** |
| **SASClientOtherError** | Transakce se dimenze **ResponseType** rovna **ClientOtherError** |
| **SASClientTimeoutError** | Transakce se dimenze **ResponseType** rovna **ClientTimeoutError** |
| **SASNetworkError** | Transakce se dimenze **ResponseType** rovna **NetworkError** |
| **SASServerOtherError** | Transakce se dimenze **ResponseType** rovna **ServerOtherError** |
| **SASServerTimeoutError** | Transakce se dimenze **ResponseType** rovna **ServerTimeoutError** |
| **SASSuccess** | Transakce se dimenze **ResponseType** rovna **úspěch** |
| **SASThrottlingError** | Transakce se dimenze **ResponseType** rovna **ClientThrottlingError** nebo **ServerBusyError** |
| **ServerOtherError** | Transakce se dimenze **ResponseType** rovna **ServerOtherError** |
| **ServerTimeoutError** | Transakce se dimenze **ResponseType** rovna **ServerTimeoutError** |
| **Úspěch** | Transakce se dimenze **ResponseType** rovna **úspěch** |
| **ThrottlingError** | **Transakce** s dimenzí **ResponseType** rovna **ClientThrottlingError** nebo **ServerBusyError**|
| **TotalBillableRequests** | **Transakce** |
| **TotalEgress** | **Výchozí přenos dat** |
| **TotalIngress** | **Příchozí přenos dat** |
| **TotalRequests** | **Transakce** |

## <a name="faq"></a>Nejčastější dotazy

### <a name="how-should-i-migrate-existing-alert-rules"></a>Jak mohu migrovat existující pravidla výstrah?

Pokud jste vytvořili na základě původní metriky úložiště classic pravidla výstrah, budete muset vytvořit nové oznámení pravidla založená na nové schéma metriky.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Je nová metriky data uložená ve stejném účtu úložiště ve výchozím nastavení?

Ne. K archivaci metriky dat do účtu úložiště, použijte [monitorování diagnostiky nastavení rozhraní API služby Azure](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Další postup

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Metriky úložiště v Azure monitorování](./storage-metrics-in-azure-monitor.md)
