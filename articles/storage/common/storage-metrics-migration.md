---
title: Migrace do Azure Storage metrics | Dokumentace Microsoftu
description: Zjistěte, jak migrovat na nové metriky, které se spravují přes Azure Monitor staré metriky.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/30/2018
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: f8a73bb6e86a187a504c2d36098001cfd90015f9
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454919"
---
# <a name="azure-storage-metrics-migration"></a>Migrace do Azure Storage metrics

V souladu s strategie sjednocujícího prostředí monitorování v Azure, Azure Storage integruje metriky na platformě Azure Monitor. V budoucnu službu staré metriky bude končit včasné oznámení na základě zásad Azure. Pokud se spoléháte na staré metrik úložiště, které potřebujete migrovat před datem ukončení služby zachování informace o metrikách.

V tomto článku se dozvíte, jak provést migraci ze staré metrik na novou metriku.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Pochopení původní metriky, které jsou spravovány službou Azure Storage

Úložiště Azure shromáždí staré hodnoty metrik a shromáždí a uloží je v tabulkách $Metric v rámci stejného účtu úložiště. Na webu Azure portal můžete použít k nastavení monitorování grafu. Se sadami SDK služby Azure Storage můžete použít také k načtení dat z $Metric tabulek, které jsou založené na schématu. Další informace najdete v tématu [Storage Analytics](./storage-analytics.md).

Staré metriky poskytují metriky kapacity pouze v úložišti objektů Blob v Azure. Staré metriky poskytují metriky transakcí v úložišti objektů Blob, Table storage, Azure Files a Queue storage.

Staré metriky jsou navržené tak, že v ploché schématu. Návrh výsledkem nula metriky hodnotu, pokud nemáte vzory provozu metriku aktivuje. Například **ServerTimeoutError** hodnota nastavená na 0 v tabulkách $Metric i v případě, že jste neobdrželi žádné chyby časového limitu serveru z živého provozu do účtu úložiště.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Vysvětlení nové metriky, které jsou spravované službou Azure Monitor

Pro nové metriky úložiště Azure Storage vysílá metriky data do back-endu Azure Monitor. Platforma Azure Monitor poskytuje jednotné možnosti monitorování, včetně dat z portálu a také přijímat data. Další podrobnosti najdete na tuto [článku](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Nové metriky poskytují metriky kapacity a metriku transakcí na objekt Blob, tabulky, soubor, frontu a storage úrovně premium.

Více dimenzí je jednou z funkcí, které poskytuje Azure Monitor. Azure Storage přijme návrhu při definování nové metriky schéma. Podporované rozměry na metriky, můžete najít podrobnosti v [metrik Azure Storage ve službě Azure Monitor](./storage-metrics-in-azure-monitor.md). Multidimenzionální design poskytuje efektivity nákladů na obou šířky pásma od přijetí a ukládat metriky kapacity. V důsledku toho, pokud je provoz nebylo spuštěno související metriky, související data metriky nevygeneruje. Například pokud se váš provoz nebyl aktivuje všechny chyby časového limitu serveru, Azure Monitor nevrací žádná data dotazu na hodnotu metriky **transakce** s dimenzí **hodnotu ResponseType** rovna **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Metriky mapování mezi metriky staré a nové metriky

Pokud načtete data metriky prostřednictvím kódu programu, musíte přijmout nové metriky schéma ve svých programech. Abyste lépe pochopili změny, můžete odkazovat na mapování uvedené v následující tabulce:

**Kapacitní metriky**

| Staré metrika | Novou metriku |
| ------------------- | ----------------- |
| **Kapacita**            | **BlobCapacity** s dimenzí **BlobType** rovna **BlockBlob** nebo **PageBlob** |
| **ObjectCount**        | **BlobCount** s dimenzí **BlobType** rovna **BlockBlob** nebo **PageBlob** |
| **ContainerCount**      | **ContainerCount** |

Nové nabídky, která nepodporují staré metriky jsou následující metriky:
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

**Metriku transakcí**

| Staré metrika | Novou metriku |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | Transakce s dimenzí **hodnotu ResponseType** rovna **AuthorizationError** a dimenze **ověřování** rovna **anonymní** |
| **AnonymousClientOtherError** | Transakce s dimenzí **hodnotu ResponseType** rovna **ClientOtherError** a dimenze **ověřování** rovna **anonymní** |
| **AnonymousClientTimeoutError** | Transakce s dimenzí **hodnotu ResponseType** rovna **ClientTimeoutError** a dimenze **ověřování** rovna **anonymní** |
| **AnonymousNetworkError** | Transakce s dimenzí **hodnotu ResponseType** rovna **NetworkError** a dimenze **ověřování** rovna **anonymní** |
| **AnonymousServerOtherError** | Transakce s dimenzí **hodnotu ResponseType** rovna **ServerOtherError** a dimenze **ověřování** rovna **anonymní** |
| **AnonymousServerTimeoutError** | Transakce s dimenzí **hodnotu ResponseType** rovna **ServerTimeoutError** a dimenze **ověřování** rovna **anonymní** |
| **AnonymousSuccess** | Transakce s dimenzí **hodnotu ResponseType** rovna **úspěch** a dimenze **ověřování** rovna **anonymní** |
| **AnonymousThrottlingError** | Transakce s dimenzí **hodnotu ResponseType** rovna **ClientThrottlingError** nebo **ServerBusyError** a dimenze **ověřování** rovna **anonymní** |
| **AuthorizationError** | Transakce s dimenzí **hodnotu ResponseType** rovna **AuthorizationError** |
| **Dostupnost** | **Dostupnost** |
| **Hodnotu AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **ClientOtherError** | Transakce s dimenzí **hodnotu ResponseType** rovna **ClientOtherError** |
| **ClientTimeoutError** | Transakce s dimenzí **hodnotu ResponseType** rovna **ClientTimeoutError** |
| **NetworkError** | Transakce s dimenzí **hodnotu ResponseType** rovna **NetworkError** |
| **PercentAuthorizationError** | Transakce s dimenzí **hodnotu ResponseType** rovna **AuthorizationError** |
| **PercentClientOtherError** | Transakce s dimenzí **hodnotu ResponseType** rovna **ClientOtherError** |
| **PercentNetworkError** | Transakce s dimenzí **hodnotu ResponseType** rovna **NetworkError** |
| **PercentServerOtherError** | Transakce s dimenzí **hodnotu ResponseType** rovna **ServerOtherError** |
| **PercentSuccess** | Transakce s dimenzí **hodnotu ResponseType** rovna **úspěch** |
| **PercentThrottlingError** | Transakce s dimenzí **hodnotu ResponseType** rovna **ClientThrottlingError** nebo **ServerBusyError** |
| **PercentTimeoutError** | Transakce s dimenzí **hodnotu ResponseType** rovna **ServerTimeoutError** nebo **hodnotu ResponseType** rovna **ClientTimeoutError** |
| **SASAuthorizationError** | Transakce s dimenzí **hodnotu ResponseType** rovna **AuthorizationError** a dimenze **ověřování** rovna **SAS** |
| **SASClientOtherError** | Transakce s dimenzí **hodnotu ResponseType** rovna **ClientOtherError** a dimenze **ověřování** rovna **SAS** |
| **SASClientTimeoutError** | Transakce s dimenzí **hodnotu ResponseType** rovna **ClientTimeoutError** a dimenze **ověřování** rovna **SAS** |
| **SASNetworkError** | Transakce s dimenzí **hodnotu ResponseType** rovna **NetworkError** a dimenze **ověřování** rovna **SAS** |
| **SASServerOtherError** | Transakce s dimenzí **hodnotu ResponseType** rovna **ServerOtherError** a dimenze **ověřování** rovna **SAS** |
| **SASServerTimeoutError** | Transakce s dimenzí **hodnotu ResponseType** rovna **ServerTimeoutError** a dimenze **ověřování** rovna **SAS** |
| **SASSuccess** | Transakce s dimenzí **hodnotu ResponseType** rovna **úspěch** a dimenze **ověřování** rovna **SAS** |
| **SASThrottlingError** | Transakce s dimenzí **hodnotu ResponseType** rovna **ClientThrottlingError** nebo **ServerBusyError** a dimenze **ověřování** rovna **SAS** |
| **ServerOtherError** | Transakce s dimenzí **hodnotu ResponseType** rovna **ServerOtherError** |
| **ServerTimeoutError** | Transakce s dimenzí **hodnotu ResponseType** rovna **ServerTimeoutError** |
| **Úspěch** | Transakce s dimenzí **hodnotu ResponseType** rovna **úspěch** |
| **ThrottlingError** | **Transakce** s dimenzí **hodnotu ResponseType** rovna **ClientThrottlingError** nebo **ServerBusyError**|
| **TotalBillableRequests** | **Transakce** |
| **TotalEgress** | **Výchozí přenos dat** |
| **TotalIngress** | **Příchozí přenos dat** |
| **TotalRequests** | **Transakce** |

## <a name="faq"></a>Nejčastější dotazy

### <a name="how-should-i-migrate-existing-alert-rules"></a>Jak můžu migrovat stávající pravidla upozornění?

Pokud jste vytvořili classic pravidla upozornění na základě metrik původní úložiště, musíte vytvořit nové upozornění. pravidla na základě nové metriky schématu.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Nové metriky data uložená v rámci stejného účtu úložiště ve výchozím nastavení?

Ne. Pokud chcete archivovat data metriky pro účet úložiště, použijte [diagnostické nastavení API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Další postup

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Metriky úložiště ve službě Azure Monitor](./storage-metrics-in-azure-monitor.md)
