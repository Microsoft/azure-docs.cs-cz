---
title: Plánování prostředí Azure Time Series Insights (preview) | Dokumentace Microsoftu
description: Plánování prostředí Azure Time Series Insights (preview)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 438d71997d2c92e377cd068615d274af6b8b5edb
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855852"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Plánování prostředí Azure Time Series Insights (preview)

Tento článek popisuje osvědčené postupy pro plánování a rychle začít s používáním Azure Time Series Insights (preview).

## <a name="best-practices-for-planning-and-preparation"></a>Osvědčené postupy pro plánování a příprava

Doporučujeme mít připravené před Začínáme následující položky:

* Když jste identifikovali vaše **čas řady ID**
* Máte vaše **časové razítko** vlastnost připravené
* Začlenění vaše **Model časové řady**
* Když chápete, jak odesílat události, které se efektivně Nenormalizovaná ve formátu JSON

Máte připravený pomáhá zjednodušit plánování a příprava tyto položky. Kromě toho je vhodné Plánujte dopředu a určit obchodní zotavení po havárii (BCDR) musí před vytvořením instance (a ne později). Tím předem pomáhá zajistit, že je vaše instance lze uchovávat připravený.

> [!TIP]
> Nakonfigurujte prostředí tak, aby odpovídala vašim potřebám BCDR před a po vytvoření vaší instance.

TSI Azure (preview) využívá modelu s průběžnými platbami firmy. Další informace o poplatky a kapacity najdete v tématu [Time Series Insights ceny](https://azure.microsoft.com/pricing/details/time-series-insights).

## <a name="configure-your-time-series-ids-and-timestamp-properties"></a>Nakonfigurujte čas řady ID a časové razítko vlastnosti

Chcete-li vytvořit nové prostředí služby TSI, vyberte **ID řady času**. To uděláte tak funguje jako logický oddíl pro vaše data. Jak je uvedeno, ujistěte se, že má váš **čas řady ID** připraven.

> [!IMPORTANT]
> **Time Series ID** jsou **neměnné** a **není možné později změnit**. Ověřte každý před posledním výběr a prvním použití.

Můžete vybrat až **tři** (3) klíče jednoznačně rozlišit vašich prostředků. Přečtěte si [osvědčené postupy pro výběr ID řady času](./time-series-insights-update-how-to-id.md) najdete další informace.

Každý zdroj události má volitelnou **časové razítko** vlastnost, která se používá ke zdrojům událostí sledovat v čase. **Časové razítko** hodnoty jsou malá a velká písmena a musí být naformátován jednotlivé specifikace každý zdroj událostí.

> [!TIP]
> Zkontrolujte požadavky na formátování a analýzy pro zdroje událostí.

Pokud nevyplníte, **čas zařazení události** události zdroj se používá jako událost **časové razítko**. Pokud odesíláte historická data nebo dávkové události, pravděpodobně zjistíte, přizpůsobení **časové razítko** vlastnost má být užitečnější než výchozí **čas zařazení události**. Další informace, přečtěte si informace o [přidání zdroje událostí ve službě IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).  

## <a name="understand-the-time-series-model"></a>Pochopení časové řady modelu

Teď můžete nakonfigurovat prostředí TSI **modelu časové řady**. Nový model usnadňuje vyhledání a analýza dat IoT. Dosahuje tím, že umožňuje kurátorování, údržby a obohacení dat časových řad a umožňuje připravit spotřebiteli datových sad. Model využívá **čas řady ID**, které mapují na instanci jedinečný prostředek přidružení proměnné (označuje se jako typy) a hierarchie. Přečtěte si informace o novém [modelu časové řady](./time-series-insights-update-tsm.md).

Model je dynamické, takže můžete vytvořené v každém okamžiku. Ale budete moct začít pracovat rychleji, když ho má sestavena a nahrána před vás od pro zápis dat do služby TSI. Sestaví váš model, zkontrolujte [modelu časové řady](./time-series-insights-update-tsm.md) článku.

Pro mnoho zákazníků, Očekáváme, že **modelu časové řady** mapovat existující model prostředku nebo systému ERP již na místě. Pro zákazníky, které nemají existující model, předem připravené uživatelské prostředí je [poskytuje](https://github.com/Microsoft/tsiclient) zobrazíte rychle začít fungovat.

## <a name="shaping-your-events"></a>Strukturování události

Je důležité ověřit tak, jak odesílat události TSI. Události se v ideálním případě denormalizovat správně a efektivně.

Základním pravidlem:

* Metadata by měla být uložena ve vašich **Model časové řady**
* **Čas režimu řady.**  instance pole a události by měla pouze nezbytné informace jako například:
  * **ID číselné řady čas**
  * **Časové razítko**

Zkontrolujte [jak tvar události](./time-series-insights-update-how-to-shape-events.md) najdete víc podrobností.

## <a name="business-disaster-recovery"></a>Zotavení po havárii firmy

Jako službu Azure TSI je služba s vysokou dostupností (HA) pomocí redundance na úrovni oblasti Azure. Použít tyto vlastní funkce není nutná žádná konfigurace. Platforma Microsoft Azure také zahrnuje funkce, které vám pomůžou vytvářet řešení s možností zotavení po havárii nebo dostupnosti mezi oblastmi. Pokud chcete zadat globální, mezi různými oblastmi HA pro zařízení nebo uživatele, využijte tyto funkce zotavení po Havárii Azure. Tento článek [Azure obchodní kontinuity podnikových procesů technickou pomoc](https://docs.microsoft.com/azure/resiliency/resiliency-technical-guidance) popisuje předdefinované funkce v Azure pro provozní kontinuitu a zotavení po Havárii. [Po havárii](https://docs.microsoft.com/azure/architecture/resiliency/index) obnovení a vysoká dostupnost pro aplikace Azure dokument obsahuje pokyny k architektuře strategie pro aplikace v Azure zajistit vysokou dostupnost a zotavení po Havárii.

> [!NOTE]
> Azure Time Series Insights nemá žádné předdefinované BCDR.
> Ve výchozím nastavení mají obnovení vytvořené Azure Storage, Azure IoT Hub a Event Hubs.

Další informace:

* Přečtěte si informace o [redundance služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy).
* Přečtěte si informace o [zotavení po Havárii vysokou dostupnost služby IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr).
* Přečtěte si informace o [zásady centra událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).

Nicméně zákazníci, kteří vyžadují BCDR můžete stále implementovat strategii zotavení tak, že vytvoříte prostředí druhý TSI v záloze oblasti Azure. Zákazníci události odesílat do tohoto sekundárního prostředí ze zdroje události primární využívat druhou vyhrazenou skupinu spotřebitelů a pokyny pro tento zdroj události BCDR.

Konkrétní kroky k tomu jsou následující:

1. Vytvořte prostředí v druhé oblasti. Přečtěte si informace o [TSI prostředí](./time-series-insights-get-started.md).
1. Vytvoření druhého vyhrazenou skupinu spotřebitelů pro váš zdroj událostí a připojení tohoto zdroje událostí do nového prostředí. Nezapomeňte určit druhé, vyhrazená skupina příjemců. Další informace z [dokumentace ke službě IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) nebo [dokumentace k Centru událostí](./time-series-insights-data-access.md).
1. Pokud primární oblast to mělo dopad na během události po havárii, přesměrovat na zálohování prostředí TSI operací.

> [!IMPORTANT]
> * Všimněte si, že ke zpoždění může dojít v případě selhání.
> * Převzetí služeb při selhání také můžou způsobit mometary Špička ve zpracování zprávy, jako jsou přesměrovány operace.
> * Další informace najdete v tématu [zmírnění latence ve službě TSI](./time-series-insights-environment-mitigate-latency.md).

## <a name="next-steps"></a>Další postup

Přečtěte si [Azure TSI (preview) úložiště a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md).

Přečtěte si informace o novém [modelu časové řady](./time-series-insights-update-tsm.md).