---
title: Azure plánování čas Series Insights ve verzi Preview – plánování prostředí Azure čas Series Insights ve verzi Preview | Dokumentace Microsoftu
description: Plánování prostředí Azure čas Series Insights ve verzi Preview.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 1df4847f20329e924352adfe782faa43d10dde98
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53277042"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Plánování prostředí Azure čas Series Insights ve verzi Preview

Tento článek popisuje osvědčené postupy pro plánování a začít rychle používat Azure čas Series Insights ve verzi Preview.

## <a name="best-practices-for-planning-and-preparation"></a>Osvědčené postupy pro plánování a příprava

Abyste mohli začít s Time Series Insights, je vhodné, pokud rozumíte:

* Co všechno získáte při zřizování prostředí čas Series Insights ve verzi Preview.
* Jsou vaše ID řady čas a časové razítko vlastností.
* Co je nového modelu časové řady a jak vytvořit vlastní.
* Jak odesílat události efektivně ve formátu JSON. 
* Time Series Insights možnosti zotavení po havárii firmy.

Time Series Insights využívá modelu s průběžnými platbami firmy. Další informace o poplatky a kapacity najdete v tématu [Time Series Insights ceny](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-time-series-insights-preview-environment"></a>Čas Series Insights ve verzi Preview prostředí

Při zřizování prostředí čas Series Insights ve verzi Preview, vytvoříte dva prostředky Azure:

* Prostředí Time Series Insights ve verzi Preview
* Azure Storage pro obecné účely V1 účtu

Pokud chcete začít, potřebujete tři další položky:
 
- A [časové řady modelu](./time-series-insights-update-tsm.md) 
- [Připojený zdroj událostí k Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md) 
- [Události přenášejí do zdroje událostí](./time-series-insights-send-events.md) , které jsou namapovány na model a jsou v platném formátu JSON 

## <a name="configure-your-time-series-ids-and-timestamp-properties"></a>Nakonfigurujte čas řady ID a časové razítko vlastnosti

Pokud chcete vytvořit nové prostředí Time Series Insights, vyberte řady čas ID. To uděláte tak funguje jako logický oddíl pro vaše data. Jak je uvedeno, ujistěte se, že má vaše ID řady čas připraven.

> [!IMPORTANT]
> ID řady času jsou *neměnné* a *není možné později změnit*. Zkontrolujte každé z nich před posledním výběr a prvním použití.

Můžete vybrat až tři (3) klíče jednoznačně rozlišit vašich prostředků. Další informace najdete v článku [osvědčené postupy pro výběr ID řady času](./time-series-insights-update-how-to-id.md) a [úložiště a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md).

Vlastnost časového razítka je také důležité. Tuto vlastnost můžete určit, při přidání zdroje událostí. Každý zdroj události má volitelná vlastnost časového razítka, která se používá ke zdrojům událostí sledovat v čase. Časové razítko hodnoty jsou malá a velká písmena a musí být naformátován jednotlivé specifikace každý zdroj událostí.

> [!TIP]
> Zkontrolujte požadavky na formátování a analýzy pro zdroje událostí.

Když necháte prázdnou, čas zařazení události ze zdroje událostí se používá jako časové razítko události. Pokud odešlete historická data nebo dávkové události, přizpůsobení vlastnost časového razítka je užitečnější než výchozí čas zařazení události. Další informace, přečtěte si informace o [přidání zdroje událostí ve službě IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md). 

## <a name="understand-the-time-series-model"></a>Pochopení časové řady modelu

Teď můžete nakonfigurovat prostředí Time Series Insights Model časové řady. Nový model usnadňuje vyhledání a analýza dat IoT. Umožňuje kurátorování, údržby a obohacení dat časových řad a umožňuje připravit spotřebiteli datových sad. Model využívá **čas řady ID**, které mapují na instanci, která přidruží jedinečný prostředek s proměnnými, označované jako typy a hierarchie. Přečtěte si informace o novém [modelu časové řady](./time-series-insights-update-tsm.md).

Model je dynamická, tak může být sestaven v každém okamžiku. Abyste mohli rychle začít, sestavení a nahrajte ho před doručením (push) dat do služby Time Series Insights. Pokud chcete vytvořit váš model, najdete v článku [použít Model časové řady](./time-series-insights-update-how-to-tsm.md).

Pro mnoho zákazníků Model časové řady mapuje existující model prostředku nebo systému ERP již na místě. Pokud nemáte existující model, předem připravených uživatelské prostředí je [poskytuje](https://github.com/Microsoft/tsiclient) zobrazíte rychle začít fungovat. Chcete-li představíte, že jak modelu může vám pomoct, podívejte se [ukázka ukázkovém prostředí](https://insights.timeseries.azure.com/preview/demo). 

## <a name="shape-your-events"></a>Obrazce události

Můžete ověřit tak, jak odesílat události do služby Time Series Insights. V ideálním případě by se události denormalizovaný správně a efektivně.

Základním pravidlem:

* Store metadat v vašeho modelu časové řady
* Časový režim řady, pole instancí a události jenom potřebné informace, jako patří:
  * ID časové řady
  * Timestamp

Další informace najdete v tématu [obrazce události](./time-series-insights-send-events.md#json).

## <a name="business-disaster-recovery"></a>Zotavení po havárii firmy

Time Series Insights je vysoce dostupná služba, která používá redundance na úrovni oblasti Azure. Konfigurace není nutné používat tyto funkce přináší. Platforma Microsoft Azure také zahrnuje funkce, které vám pomůžou vytvářet řešení pomocí možnosti zotavení po havárii nebo dostupnosti mezi oblastmi. Poskytovat globální mezi různými oblastmi vysoká dostupnost pro zařízení nebo uživatele, využijte tyto funkce pro zotavení po havárii Azure. 

Informace o integrovaných funkcí v Azure pro provozní kontinuitu a zotavení po havárii (BCDR), najdete v části [technickou pomoc Azure obchodní kontinuity podnikových procesů](https://docs.microsoft.com/azure/resiliency/resiliency-technical-guidance). Doprovodné materiály k architektuře týkající se strategií pro aplikace Azure, abyste dosáhli vysoké dostupnosti a zotavení po havárii, naleznete dokumentu na [zotavení po havárii a vysoká dostupnost pro aplikace Azure](https://docs.microsoft.com/azure/architecture/resiliency/index).

> [!NOTE]

>  Time Series Insights nemá vestavěné BCDR.
> Ve výchozím nastavení mají obnovení vytvořené Azure Storage, Azure IoT Hub a Azure Event Hubs.

Další informace, přečtěte si informace o:

* [Azure redundance úložiště](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [Zotavení po havárii vysokou dostupnost služby IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr)
* [Zásady centra událostí.](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)

Pokud budete potřebovat BCDR, můžete stále implementovat strategii zotavení. Vytvoření druhého prostředí Time Series Insights ve službě backup oblasti Azure. Odesílání událostí do tohoto sekundárního prostředí ze zdroje událostí primární. Použijte druhý vyhrazenou skupinu spotřebitelů a pokyny pro tento zdroj události BCDR.

Postupujte podle těchto kroků k vytvoření a použití sekundárních prostředí Time Series Insights.

1. Vytvořte prostředí v druhé oblasti. Další informace najdete v tématu [prostředí Time Series Insights](./time-series-insights-get-started.md).
1. Vytvoření druhého vyhrazenou skupinu spotřebitelů pro váš zdroj událostí. Připojte tento zdroj událostí do nového prostředí. Nezapomeňte určit druhé skupině příjemců. Další informace najdete v tématu [dokumentace ke službě IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) nebo [dokumentace k Centru událostí](./time-series-insights-data-access.md).
1. Pokud primární oblast je ovlivněné incidentem po havárii, přesměrování operací zálohování prostředí Time Series Insights.

> [!IMPORTANT]
> * Všimněte si, že ke zpoždění může setkat v případě selhání.
> * Převzetí služeb při selhání také může způsobit přechodné špičky ve zpracování zprávy, jako jsou přesměrovány operace.
> * Další informace najdete v tématu [zmírnění latence v Time Series Insights](./time-series-insights-environment-mitigate-latency.md).

## <a name="next-steps"></a>Další postup

Další informace, přečtěte si informace o:

- [Azure storage čas Series Insights ve verzi Preview a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md)
- [Modelování dat](./time-series-insights-update-tsm.md)