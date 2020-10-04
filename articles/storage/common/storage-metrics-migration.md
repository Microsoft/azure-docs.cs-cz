---
title: Přesun z Analýza úložiště metriky do Azure Monitor metriky | Microsoft Docs
description: Naučte se, jak přejít z Analýza úložiště metriky (klasické metriky) na metriky v Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 076f5573b599fbb83486087380174fc2da53986c
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708573"
---
# <a name="transition-to-metrics-in-azure-monitor"></a>Přechod na metriky v Azure Monitor

Od **31. srpna 2023** analýza úložiště metriky, označované také jako *klasické metriky* , budou vyřazeny. Další informace najdete v [oficiálním oznámení](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Pokud používáte klasické metriky, nezapomeňte přejít na metriky v Azure Monitor před tímto datem. Tento článek vám pomůže provést přechod. 

## <a name="steps-to-complete-the-transition"></a>Postup dokončení přechodu

Pro přechod na metriky v Azure Monitor doporučujeme následující postup.

1. Přečtěte si o některých [klíčových rozdílech](#key-differences-between-classic-metrics-and-metrics-in-azure-monitor) mezi klasickými metrikami a metrikami v Azure monitor. 

2. Zkompilujte seznam klasických metrik, které aktuálně používáte.

3. Určete, [Jaké metriky v Azure monitor](#metrics-mapping-between-old-metrics-and-new-metrics) poskytují stejná data jako metriky, které aktuálně používáte. 
   
4. Vytvořte [grafy](https://docs.microsoft.com/learn/modules/gather-metrics-blob-storage/2-viewing-blob-metrics-in-azure-portal) nebo [řídicí panely](https://docs.microsoft.com/learn/modules/gather-metrics-blob-storage/4-using-dashboards-in-the-azure-portal) , abyste zobrazili data metrik.

   > [!NOTE]
   > Metriky ve Azure Monitor jsou ve výchozím nastavení povolené, takže nemusíte nic dělat, abyste začali zachytávání metrik. Pokud ale chcete zobrazit tyto metriky, musíte vytvořit grafy nebo řídicí panely. 
 
5. Pokud jste vytvořili pravidla výstrah založená na klasických metrikách úložiště, [vytvořte pravidla výstrah](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview) založená na metrikách v Azure monitor. 

6. Až budete moct zobrazit všechny metriky v Azure Monitor, můžete vypnout klasické protokolování. 

<a id="key-differences-between-classic-metrics-and-metrics-in-azure-monitor"></a>

## <a name="classic-metrics-vs-metrics-in-azure-monitor"></a>Klasické metriky vs. metriky v Azure Monitor

Tato část popisuje několik klíčových rozdílů mezi těmito dvěma platformami metrik.

Hlavní rozdíl spočívá v tom, jak jsou metriky spravovány. Klasické metriky se spravují pomocí Azure Storage, že metriky v Azure Monitor jsou spravované pomocí Azure Monitor. U klasických metrik Azure Storage shromažďuje hodnoty metrik, agreguje je a pak je ukládá v tabulkách, které jsou umístěné v účtu úložiště. S metrikami v Azure Monitor odesílá Azure Storage data metriky do back-endu Azure Monitor. Azure Monitor poskytuje jednotné prostředí pro monitorování, které zahrnuje data z Azure Portal a také data, která se ingestují. 

V případě podpory metrik nabízí klasická metrika metriky **kapacity** pouze pro úložiště objektů BLOB v Azure. Metriky v Azure Monitor poskytují metriky kapacity pro úložiště objektů blob, tabulek, souborů, front a Premium. Klasické metriky poskytují **transakční** metriky pro úložiště objektů blob, tabulek, souborů Azure a front. Metriky v Azure Monitor do tohoto seznamu přidat úložiště úrovně Premium.

Pokud aktivita ve vašem účtu neaktivuje metriku, pro klasickou metriku se pro tuto metriku zobrazí hodnota nula (0). Metriky v Azure Monitor budou data zcela vynechat, což vede k čištění sestav. Například u klasických metrik, pokud nejsou hlášeny žádné chyby časového limitu serveru, pak `ServerTimeoutError` je hodnota v tabulce metrik nastavena na 0. Azure Monitor nevrátí žádná data při dotazování na hodnotu metriky `Transactions` s dimenzí `ResponseType` Equal `ServerTimeoutError` . 

Další informace o metrikách v Azure Monitor najdete v tématu [metriky v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

<a id="metrics-mapping-between-old-metrics-and-new-metrics"></a>

## <a name="map-classic-metrics-to-metrics-in-azure-monitor"></a>Mapování klasických metrik na metriky v Azure Monitor

 Pomocí těchto tabulek můžete určit, které metriky v Azure Monitor poskytují stejná data jako metriky, které aktuálně používáte. 

**Metriky kapacity**

| Klasická metrika | Metrika v Azure Monitor |
| ------------------- | ----------------- |
| `Capacity`            | `BlobCapacity` s dimenzí `BlobType` rovnou `BlockBlob` nebo `PageBlob` |
| `ObjectCount`        | `BlobCount` s dimenzí `BlobType` rovnou `BlockBlob` nebo `PageBlob` |
| `ContainerCount`      | `ContainerCount` |

> [!NOTE]
> K dispozici je také několik nových metrik kapacity, které nebyly k dispozici jako klasické metriky. Seznam kompletních zobrazení najdete v tématu [metriky](../common/monitor-storage-reference.md#metrics).

**Transakční metriky**

| Klasická metrika | Metrika v Azure Monitor |
| ------------------- | ----------------- |
| `AnonymousAuthorizationError` | Transakce s dimenzí `ResponseType` rovná se `AuthorizationError` a dimenze `Authentication` rovna `Anonymous` |
| `AnonymousClientOtherError` | Transakce s dimenzí `ResponseType` rovná se `ClientOtherError` a dimenze `Authentication` rovna `Anonymous` |
| `AnonymousClientTimeoutError` | Transakce s dimenzí `ResponseType` rovná se `ClientTimeoutError` a dimenze `Authentication` rovna `Anonymous` |
| `AnonymousNetworkError` | Transakce s dimenzí `ResponseType` rovná se `NetworkError` a dimenze `Authentication` rovna `Anonymous` |
| `AnonymousServerOtherError` | Transakce s dimenzí `ResponseType` rovná se `ServerOtherError` a dimenze `Authentication` rovna `Anonymous` |
| `AnonymousServerTimeoutError` | Transakce s dimenzí `ResponseType` rovná se `ServerTimeoutError` a dimenze `Authentication` rovna `Anonymous` |
| `AnonymousSuccess` | Transakce s dimenzí `ResponseType` rovná se `Success` a dimenze `Authentication` rovna `Anonymous` |
| `AnonymousThrottlingError` | Transakce s dimenzí `ResponseType` rovnající se `ClientThrottlingError` nebo `ServerBusyError` a dimenze `Authentication` rovny `Anonymous` |
| `AuthorizationError` | Transakce s dimenzí `ResponseType` rovná se `AuthorizationError` |
| `Availability` | `Availability` |
| `AverageE2ELatency` | `SuccessE2ELatency` |
| `AverageServerLatency` | `SuccessServerLatency` |
| `ClientOtherError` | Transakce s dimenzí `ResponseType` rovná se `ClientOtherError` |
| `ClientTimeoutError` | Transakce s dimenzí `ResponseType` rovná se `ClientTimeoutError` |
| `NetworkError` | Transakce s dimenzí `ResponseType` rovná se `NetworkError` |
| `PercentAuthorizationError` | Transakce s dimenzí `ResponseType` rovná se `AuthorizationError` |
| `PercentClientOtherError` | Transakce s dimenzí `ResponseType` rovná se `ClientOtherError` |
| `PercentNetworkError` | Transakce s dimenzí `ResponseType` rovná se `NetworkError` |
| `PercentServerOtherError` | Transakce s dimenzí `ResponseType` rovná se `ServerOtherError` |
| `PercentSuccess` | Transakce s dimenzí `ResponseType` rovná se `Success` |
| `PercentThrottlingError` | Transakce s dimenzí `ResponseType` rovnající se `ClientThrottlingError` nebo `ServerBusyError` |
| `PercentTimeoutError` | Transakce s dimenzí `ResponseType` rovná se `ServerTimeoutError` nebo `ResponseType` se rovná `ClientTimeoutError` |
| `SASAuthorizationError` | Transakce s dimenzí `ResponseType` rovná se `AuthorizationError` a dimenze `Authentication` rovna `SAS` |
| `SASClientOtherError` | Transakce s dimenzí `ResponseType` rovná se `ClientOtherError` a dimenze `Authentication` rovna `SAS` |
| `SASClientTimeoutError` | Transakce s dimenzí `ResponseType` rovná se `ClientTimeoutError` a dimenze `Authentication` rovna `SAS` |
| `SASNetworkError` | Transakce s dimenzí `ResponseType` rovná se `NetworkError` a dimenze `Authentication` rovna `SAS` |
| `SASServerOtherError` | Transakce s dimenzí `ResponseType` rovná se `ServerOtherError` a dimenze `Authentication` rovna `SAS` |
| `SASServerTimeoutError` | Transakce s dimenzí `ResponseType` rovná se `ServerTimeoutError` a dimenze `Authentication` rovna `SAS` |
| `SASSuccess` | Transakce s dimenzí `ResponseType` rovná se `Success` a dimenze `Authentication` rovna `SAS` |
| `SASThrottlingError` | Transakce s dimenzí `ResponseType` rovnající se `ClientThrottlingError` nebo `ServerBusyError` a dimenze `Authentication` rovny `SAS` |
| `ServerOtherError` | Transakce s dimenzí `ResponseType` rovná se `ServerOtherError` |
| `ServerTimeoutError` | Transakce s dimenzí `ResponseType` rovná se `ServerTimeoutError` |
| `Success` | Transakce s dimenzí `ResponseType` rovná se `Success` |
| `ThrottlingError` | `Transactions` s dimenzí `ResponseType` rovnou `ClientThrottlingError` nebo `ServerBusyError`|
| `TotalBillableRequests` | `Transactions` |
| `TotalEgress` | `Egress` |
| `TotalIngress` | `Ingress` |
| `TotalRequests` | `Transactions` |

## <a name="next-steps"></a>Další kroky

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)

