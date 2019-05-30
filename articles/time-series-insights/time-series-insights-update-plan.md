---
title: Plánování prostředí Azure čas Series Insights ve verzi Preview | Dokumentace Microsoftu
description: Plánování prostředí Azure čas Series Insights ve verzi Preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 435e5f2163270672ac5f1f5695ca2fe9be22ee6b
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388730"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Plánování prostředí Azure čas Series Insights ve verzi Preview

Tento článek popisuje osvědčené postupy pro plánování a rychlé zahájení práce s použitím Insights Azure čas řady ve verzi Preview.

> [!NOTE]
> Osvědčené postupy pro plánování instanci služby Time Series Insights obecné dostupnosti, najdete v části [plánování prostředí Azure Time Series Insights všeobecné dostupnosti](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Osvědčené postupy pro plánování a příprava

Abyste mohli začít s Time Series Insights, je vhodné, pokud rozumíte:

* Co dostanete, když jste [zřízení čas Series Insights ve verzi Preview prostředí](#the-preview-environment).
* Co vaše [vlastnosti ID řady času a časového razítka jsou](#configure-time-series-ids-and-timestamp-properties).
* Jaké nové [je Model časové řady](#understand-the-time-series-model)a jak vytvořit vlastní.
* Jak [efektivně odesílání událostí ve formátu JSON](#shape-your-events).
* Time Series Insights [obchodní možnosti zotavení po havárii](#business-disaster-recovery).

Azure Time Series Insights využívá modelu s průběžnými platbami firmy. Další informace o poplatky a kapacity najdete v tématu [Time Series Insights ceny](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>Prostředí ve verzi Preview

Při zřizování prostředí čas Series Insights ve verzi Preview, vytvoříte dva prostředky Azure:

* Prostředí Azure čas Series Insights ve verzi Preview
* Účet služby Azure Storage pro obecné účely V1

Pokud chcete začít, potřebujete tři další položky:

* A [časové řady modelu](./time-series-insights-update-tsm.md)
* [Připojený zdroj událostí k Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Události přenášejí do zdroje událostí](./time-series-insights-send-events.md) , které jsou namapovány na model a jsou v platném formátu JSON

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Konfigurovat vlastnosti ID řady čas a časové razítko

Pokud chcete vytvořit nové prostředí Time Series Insights, vyberte řady čas ID. To uděláte tak funguje jako logický oddíl pro vaše data. Jak je uvedeno, ujistěte se, že má vaše ID řady čas připraven.

> [!IMPORTANT]
> ID řady času jsou *neměnné* a *není možné později změnit*. Zkontrolujte každé z nich před posledním výběr a prvním použití.

Můžete vybrat až tři klíče jednoznačně rozlišit vašich prostředků. Další informace najdete v článku [osvědčené postupy pro výběr ID řady času](./time-series-insights-update-how-to-id.md) a [úložiště a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md).

Vlastnost časového razítka je také důležité. Tuto vlastnost můžete určit, při přidání zdroje událostí. Každý zdroj události má volitelná vlastnost časového razítka, která se používá ke zdrojům událostí sledovat v čase. Časové razítko hodnoty jsou malá a velká písmena a musí být naformátován jednotlivé specifikace každý zdroj událostí.

> [!TIP]
> Zkontrolujte požadavky na formátování a analýzy pro zdroje událostí.

Když necháte prázdnou, čas zařazení události ze zdroje událostí se používá jako časové razítko události. Pokud odešlete historická data nebo dávkové události, přizpůsobení vlastnost časového razítka je užitečnější než výchozí čas zařazení události. Další informace, přečtěte si o tom, jak [přidání zdroje událostí ve službě Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Pochopení časové řady modelu

Teď můžete nakonfigurovat prostředí Time Series Insights Model časové řady. Nový model usnadňuje vyhledání a analýza dat IoT. Umožňuje kurátorování, údržby a obohacení dat časových řad a umožňuje připravit spotřebiteli datových sad. Model využívá čas řady ID, které mapují na instanci, která přidruží jedinečný prostředek s proměnnými, označované jako typy a hierarchie. Přečtěte si informace o novém [modelu časové řady](./time-series-insights-update-tsm.md).

Model je dynamická, tak může být sestaven v každém okamžiku. Abyste mohli rychle začít, sestavení a nahrajte ho před doručením (push) dat do služby Time Series Insights. Pokud chcete vytvořit váš model, najdete v článku [použít Model časové řady](./time-series-insights-update-how-to-tsm.md).

Pro mnoho zákazníků Model časové řady mapuje existující model prostředku nebo systému ERP již na místě. Pokud nemáte existující model, předem připravených uživatelské prostředí je [poskytuje](https://github.com/Microsoft/tsiclient) zobrazíte rychle začít fungovat. Chcete-li představíte, že jak modelu může vám pomoct, podívejte se [ukázka ukázkovém prostředí](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Obrazce události

Můžete ověřit tak, jak odesílat události do služby Time Series Insights. V ideálním případě by se události denormalizovaný správně a efektivně.

Základním pravidlem:

* Store metadat v vašeho modelu časové řady.
* Časový režim řady, pole instancí a události obsahují jenom nezbytné informace, jako je například ID řady času nebo časového razítka.

Další informace najdete v tématu [obrazce události](./time-series-insights-send-events.md#json).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Další postup

- Další informace o [úložiště a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md) v čase Series Insights ve verzi Preview.
- Další informace o [modelování dat](./time-series-insights-update-tsm.md) v čase Series Insights ve verzi Preview.