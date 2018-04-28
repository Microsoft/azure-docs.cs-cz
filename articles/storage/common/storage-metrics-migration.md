---
title: Migrace metrik Azure Storage | Microsoft Docs
description: Další informace o tom, jak migrovat starší verze metriky do nové metriky spravuje Azure monitorování.
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
ms.openlocfilehash: c4dc9b231668315af16c625314c737fee99d672d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="azure-storage-metrics-migration"></a>Migrace metrik Azure Storage

V souladu s strategie jednotných monitorování prostředí v Azure, Azure Storage na panely metriky pro platformu Azure monitorování. Službu starší verze metriky v budoucnu, bude ukončen s včasné upozornění na základě zásad Azure. Pokud byste tedy spoléhat na starší verze úložiště metriky, budete muset migrovat před datem ukončení služby, aby byla zachována informace o metrikách.

Tento článek vás provede migraci ze starší verze metriky na nové metriky.

## <a name="understand-legacy-metrics-managed-by-azure-storage"></a>Pochopení starší verze metriky spravuje Azure Storage

Úložiště Azure starší verze metriky hodnoty agreguje shromažďuje a ukládá je do $Metric tabulky ve stejném účtu úložiště. Portál Azure můžete použít k nastavení monitorování graf a sadami SDK služby Azure Storage můžete použít také přečíst data z tabulek $Metric na základě schématu. Další informace najdete na to [Storage Analytics](./storage-analytics.md).

Starší verze metriky poskytují metriky kapacity u objektu Blob jenom a transakce metriky do objektu Blob, Table, souboru a fronty.

Starší verze metriky jsou navrženy v ploché schématu. Návrh výsledkem hodnota nula metriky, pokud nemáte vzory přenosů dat aktivován metriku. Například **ServerTimeoutError** nastavena na 0 v tabulkách $Metric i v případě, že jste neobdrželi žádné chyby vypršení časového limitu serveru z provozu provoz k účtu úložiště.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Pochopení nové metriky spravuje monitorování Azure

Pro nové metriky úložiště Azure Storage vysílá data metriky na back-end Azure monitorování. Poskytuje jednotné monitorování prostředí, včetně dat z portálu Azure monitorování a také přijímat data. Další informace najdete na to [článku](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Nové metriky poskytovat metriky kapacity a metriky transakce na objekt Blob, Table, soubor, fronty a storage úrovně premium.

Více dimenze je jedním z funkce poskytované službou Azure monitorování. Úložiště Azure přijme návrhu při definování schématu metriky. Podporované dimenze na metriky můžete najít podrobnosti v tomto [metrik Azure Storage v Azure monitorování](./storage-metrics-in-azure-monitor.md). Více dimenze design poskytuje efektivity nákladů na obou šířky pásma z přijímání a kapacity v ukládání metriky. V důsledku toho pokud provozu ještě aktivována související metriky, se nevygeneruje související data metriky. Například pokud provozu nebyla aktivována všechny chyby vypršení časového limitu serveru, monitorování Azure nevrátí žádná data dotazu na hodnotu metriky **transakce** s dimenzí **ResponseType** rovna **ServerTimeoutError**.

## <a name="metrics-mapping-between-legacy-metrics-and-new-metrics"></a>Metriky mapování mezi starší verze metriky a nové metriky

Pokud číst metriky data prostřednictvím kódu programu, budete muset přijmout nové metriky schéma ve vašich aplikacích. Abyste lépe pochopili změny, můžete se podívat do mapování uvedené v následující tabulce:

**Metriky kapacity**

| Metrika starší verze | Nové metrika |
| ------------------- | ----------------- |
| Kapacita            | BlobCapacity s dimenzí BlobType rovna BlockBlob nebo PageBlob |
| ObjectCount         | BlobCount s dimenzí BlobType rovna BlockBlob nebo PageBlob |
| ContainerCount      | ContainerCount |

Následující metriky jsou nové nabídky, které nepodporují starší verze metriky:
* TableCapacity
* TableCount
* TableEntityCount
* QueueCapacity
* QueueCount
* QueueMessageCount
* FileCapacity
* FileCount
* FileShareCount
* UsedCapacity

**Metriky transakce**

| Metrika starší verze | Nové metrika |
| ------------------- | ----------------- |
| AnonymousAuthorizationError | Transakce s dimenzí ResponseType rovna AuthorizationError |
| AnonymousClientOtherError | Transakce s dimenzí ResponseType rovna ClientOtherError |
| AnonymousClientTimeoutError | Transakce s dimenzí ResponseType rovna ClientTimeoutError |
| AnonymousNetworkError | Transakce s dimenzí ResponseType rovna NetworkError |
| AnonymousServerOtherError | Transakce s dimenzí ResponseType rovna ServerOtherError |
| AnonymousServerTimeoutError | Transakce s dimenzí ResponseType rovna ServerTimeoutError |
| AnonymousSuccess | Transakce s dimenzí ResponseType rovna úspěch |
| AnonymousThrottlingError | Transakce s dimenzí ResponseType rovna ClientThrottlingError nebo ServerBusyError |
| AuthorizationError | Transakce s dimenzí ResponseType rovna AuthorizationError |
| Dostupnost | Dostupnost |
| AverageE2ELatency | SuccessE2ELatency |
| AverageServerLatency | SuccessServerLatency |
| ClientOtherError | Transakce s dimenzí ResponseType rovna ClientOtherError |
| ClientTimeoutError | Transakce s dimenzí ResponseType rovna ClientTimeoutError |
| NetworkError | Transakce s dimenzí ResponseType rovna NetworkError |
| PercentAuthorizationError | Transakce s dimenzí ResponseType rovna AuthorizationError |
| PercentClientOtherError | Transakce s dimenzí ResponseType rovna ClientOtherError |
| PercentNetworkError | Transakce s dimenzí ResponseType rovna NetworkError |
| PercentServerOtherError | Transakce s dimenzí ResponseType rovna ServerOtherError |
| PercentSuccess | Transakce s dimenzí ResponseType rovna úspěch |
| PercentThrottlingError | Transakce s dimenzí ResponseType rovna ClientThrottlingError nebo ServerBusyError |
| PercentTimeoutError | Transakce s dimenzí ResponseType rovna ServerTimeoutError nebo rovna hodnotě ClientTimeoutError ResponseType|
| SASAuthorizationError | Transakce s dimenzí ResponseType rovna AuthorizationError |
| SASClientOtherError | Transakce s dimenzí ResponseType rovna ClientOtherError |
| SASClientTimeoutError | Transakce s dimenzí ResponseType rovna ClientTimeoutError |
| SASNetworkError | Transakce s dimenzí ResponseType rovna NetworkError |
| SASServerOtherError | Transakce s dimenzí ResponseType rovna ServerOtherError |
| SASServerTimeoutError | Transakce s dimenzí ResponseType rovna ServerTimeoutError |
| SASSuccess | Transakce s dimenzí ResponseType rovna úspěch |
| SASThrottlingError | Transakce s dimenzí ResponseType rovna ClientThrottlingError nebo ServerBusyError |
| ServerOtherError | Transakce s dimenzí ResponseType rovna ServerOtherError |
| ServerTimeoutError | Transakce s dimenzí ResponseType rovna ServerTimeoutError |
| Úspěch | Transakce s dimenzí ResponseType rovna úspěch |
| ThrottlingError | Transakce s dimenzí ResponseType rovna ClientThrottlingError nebo ServerBusyError |
| TotalBillableRequests | Transakce |
| TotalEgress | Výchozí přenos |
| TotalIngress | Příchozí přenos dat |
| TotalRequests | Transakce |

## <a name="faq"></a>Nejčastější dotazy

* Jak mohu migrovat existující pravidla výstrah?

Odpověď: Pokud jste vytvořili classic výstraha pravidla založená na starší verze úložiště metriky, budete muset vytvořit pravidla založená na nové schéma metriky nová výstraha.

* Bude nové metriky data uložená ve stejném účtu úložiště ve výchozím nastavení?

Odpověď: Ne. Pokud potřebujete archivaci metriky dat k účtu úložiště, můžete použít [nastavení diagnostiky v Azure monitorování](https://azure.microsoft.com/blog/azure-monitor-multiple-diagnostic-settings/)

## <a name="next-steps"></a>Další postup

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Metriky úložiště v Azure monitorování](./storage-metrics-in-azure-monitor.md)
