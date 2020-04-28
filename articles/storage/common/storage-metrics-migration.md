---
title: Migrace metrik Azure Storage | Microsoft Docs
description: Naučte se migrovat staré metriky na nové metriky, které jsou spravované pomocí Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: b5d8add293a2ba8f14dc2d2fb8ba3b4228f455b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176222"
---
# <a name="azure-storage-metrics-migration"></a>Azure Storage migrace metrik

V souladu se strategií sjednocení monitorování v Azure Azure Storage integruje metriky na Azure Monitor platformu. V budoucnu bude služba starých metrik končit počátečním oznámením na základě Azure Policy. Pokud spoléháte na staré metriky úložiště, je nutné před datem ukončení služby provést migraci, abyste zachovali informace o metrikách.

V tomto článku se dozvíte, jak migrovat ze starých metrik na nové metriky.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Vysvětlení starých metrik spravovaných službou Azure Storage

Azure Storage shromažďuje staré hodnoty metrik a agreguje je a ukládá v $Metricch tabulkách v rámci stejného účtu úložiště. K nastavení grafu monitorování můžete použít Azure Portal. Můžete také použít sady SDK Azure Storage ke čtení dat z $Metric tabulek, které jsou založeny na schématu. Další informace najdete v tématu [Analýza úložiště](./storage-analytics.md).

Staré metriky poskytují metriky kapacity pouze v úložišti objektů BLOB v Azure. Staré metriky poskytují transakční metriky pro úložiště objektů blob, úložiště tabulek, soubory Azure a úložiště front.

Staré metriky jsou navržené v plochém schématu. Výsledkem návrhu je nulová hodnota metriky, pokud nemáte vzory přenosů, které tuto metriku aktivovaly. Například hodnota **ServerTimeoutError** je v tabulkách $metric nastavena na 0, a to i v případě, že nedostanete žádné chyby při vypršení časového limitu serveru z živého provozu do účtu úložiště.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Vysvětlení nových metrik spravovaných službou Azure Monitor

Pro nové metriky úložiště Azure Storage vygeneruje data metriky do back-endu Azure Monitor. Azure Monitor poskytuje jednotnou monitorovací prostředí, včetně dat z portálu a také přijímání dat. Další podrobnosti najdete v tomto [článku](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Nové metriky poskytují metriky kapacity a metriky transakcí u objektů blob, tabulek, souborů, front a prémiových úložišť.

Multi-Dimension je jedna z funkcí, které Azure Monitor poskytuje. Azure Storage přijímají návrh v části Definování nového schématu metriky. Pro podporované dimenze pro metriky můžete najít podrobnosti v [Azure Storage metriky v Azure monitor](./storage-metrics-in-azure-monitor.md). Design s více dimenzemi poskytuje cenovou efektivitu pro šířku pásma z příjmu a kapacitu z hlediska ukládání metrik. V důsledku toho se v případě, že váš provoz neaktivoval související metriky, nebudou vygenerována související data metriky. Pokud například váš provoz neaktivoval žádné chyby časového limitu serveru, Azure Monitor nevrátí žádná data při dotazování na hodnotu **transakcí** metriky s dimenzí **ResponseType** se rovná **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Mapování metrik mezi starými a novými metrikami

Pokud data metriky přečtete programově, musíte v programech přijmout nové schéma metriky. Chcete-li lépe pochopit změny, můžete se podívat na mapování uvedené v následující tabulce:

**Metriky kapacity**

| Stará metrika | Nová metrika |
| ------------------- | ----------------- |
| **Klíčivost**            | **BlobCapacity** s dimenzí **BlobType** se rovná **BlockBlob** nebo **PageBlob** |
| **ObjectCount**        | **BlobCount** s dimenzí **BlobType** se rovná **BlockBlob** nebo **PageBlob** |
| **ContainerCount**      | **ContainerCount** |

Následující metriky představují nové nabídky, které staré metriky nepodporují:
* **TableCapacity**
* **TableCount**
* **TableEntityCount**
* **QueueCapacity**
* **QueueCount**
* **QueueMessageCount**
* **Kapacita zařízení**
* **FileCount**
* **FileShareCount**
* **UsedCapacity**

**Metriky transakcí**

| Stará metrika | Nová metrika |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **AuthorizationError** a **ověřování** dimenzí se rovná **anonymnímu** . |
| **AnonymousClientOtherError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **ClientOtherError** a **ověřování** dimenzí se rovná **anonymnímu** . |
| **AnonymousClientTimeoutError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **ClientTimeoutError** a **ověřování** dimenzí se rovná **anonymnímu** . |
| **AnonymousNetworkError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **NetworkError** a **ověřování** dimenzí se rovná **anonymnímu** . |
| **AnonymousServerOtherError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **ServerOtherError** a **ověřování** dimenzí se rovná **anonymnímu** . |
| **AnonymousServerTimeoutError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **ServerTimeoutError** a **ověřování** dimenzí se rovná **anonymnímu** . |
| **AnonymousSuccess** | Transakce s dimenzí **ResponseType** se rovná **úspěchu** a **ověřování** dimenzí se rovná **anonymnímu** . |
| **AnonymousThrottlingError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **ClientThrottlingError** nebo **ServerBusyError** a **ověřování** dimenzí se rovná **anonymnímu** . |
| **AuthorizationError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **AuthorizationError** |
| **Dostupnost** | **Dostupnost** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **ClientOtherError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **ClientOtherError** |
| **ClientTimeoutError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **ClientTimeoutError** |
| **NetworkError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **NetworkError** |
| **PercentAuthorizationError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **AuthorizationError** |
| **PercentClientOtherError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **ClientOtherError** |
| **PercentNetworkError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **NetworkError** |
| **PercentServerOtherError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **ServerOtherError** |
| **PercentSuccess** | Transakce s dimenzí **ResponseType** se rovná **úspěchu** . |
| **PercentThrottlingError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **ClientThrottlingError** nebo **ServerBusyError** . |
| **PercentTimeoutError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **ServerTimeoutError** nebo **ResponseType** rovny hodnotě **ClientTimeoutError** |
| **SASAuthorizationError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **AuthorizationError** a **ověřování** dimenzí se rovná **SAS** . |
| **SASClientOtherError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **ClientOtherError** a **ověřování** dimenzí se rovná **SAS** . |
| **SASClientTimeoutError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **ClientTimeoutError** a **ověřování** dimenzí se rovná **SAS** . |
| **SASNetworkError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **NetworkError** a **ověřování** dimenzí se rovná **SAS** . |
| **SASServerOtherError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **ServerOtherError** a **ověřování** dimenzí se rovná **SAS** . |
| **SASServerTimeoutError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **ServerTimeoutError** a **ověřování** dimenzí se rovná **SAS** . |
| **SASSuccess** | Transakce s dimenzí **ResponseType** se rovná **úspěchu** a **ověřování** dimenzí se rovná **SAS** . |
| **SASThrottlingError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **ClientThrottlingError** nebo **ServerBusyError** a **ověřování** dimenzí se rovná **SAS** . |
| **ServerOtherError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **ServerOtherError** |
| **ServerTimeoutError** | Transakce s dimenzí **ResponseType** se rovnají hodnotě **ServerTimeoutError** |
| **Úspěch** | Transakce s dimenzí **ResponseType** se rovná **úspěchu** . |
| **ThrottlingError** | **Transakce** s dimenzí **ResponseType** se rovnají hodnotě **ClientThrottlingError** nebo **ServerBusyError** .|
| **TotalBillableRequests** | **Transakce** |
| **TotalEgress** | **Výchozí přenos dat** |
| **TotalIngress** | **Příchozí přenos dat** |
| **TotalRequests** | **Transakce** |

## <a name="faq"></a>Nejčastější dotazy

### <a name="how-should-i-migrate-existing-alert-rules"></a>Jak mám migrovat existující pravidla upozornění?

Pokud jste vytvořili pravidla pro klasické výstrahy na základě starých metrik úložiště, budete muset vytvořit nová pravidla upozornění založená na novém schématu metriky.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Ve výchozím nastavení jsou nová data metriky uložená ve stejném účtu úložiště?

Ne. Pokud chcete archivovat data metriky do účtu úložiště, použijte [rozhraní API pro nastavení diagnostiky Azure monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Další kroky

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Metriky úložiště v Azure Monitor](./storage-metrics-in-azure-monitor.md)
