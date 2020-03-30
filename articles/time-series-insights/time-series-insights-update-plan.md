---
title: Plánování prostředí Preview – Přehledy Azure Time Series | Dokumenty společnosti Microsoft
description: Doporučené postupy pro konfiguraci, správu, plánování a nasazení prostředí Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: seodec18
ms.openlocfilehash: b16d78c9670d05fcec8126c5544d1dd97f6a03bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045722"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Plánování prostředí Azure Time Series Insights Preview

Tento článek popisuje osvědčené postupy pro plánování a rychlé zahájení pomocí Azure Time Series Insights Preview.

> [!NOTE]
> Doporučené postupy pro plánování instance Time Series Insights s obecnou dostupností najdete v [plánu prostředí obecné dostupnosti Azure Time Series Insights](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Osvědčené postupy pro plánování a přípravu

Osvědčené postupy týkající se plánování a přípravy prostředí jsou dále popsány v následujících článcích:

* Co získáte při [zřizování prostředí Time Series Insights Preview](#the-preview-environment).
* Jaká [jsou vaše ID časové řady a vlastnosti časového razítka](#configure-time-series-ids-and-timestamp-properties).
* Co je nový [model časové řady](#understand-the-time-series-model)a jak si vytvořit vlastní.
* Jak [efektivně odesílat události v JSON](#shape-your-events).
* Možnosti [obnovení obchodní chod impozičních řešení](#business-disaster-recovery)time series insights .

Azure Time Series Insights využívá obchodní model s průběžným platbaem. Další informace o poplatcích a kapacitě najdete v [článek Ceny Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>Prostředí náhledu

Když zřídíte prostředí Náhled přehledů časové řady, vytvoříte dva prostředky Azure:

* Prostředí Azure Time Series Insights Preview
* Účet Azure Storage pro obecné účely V1

Jako součást procesu zřizování určíte, zda chcete povolit teplé úložiště. Teplé úložiště poskytuje prostředí vrstvených dotazů. Pokud je tato možnost povolena, je nutné zadat dobu uchovávání mezi 7 a 30 dny. Dotazy prováděné v rámci období uchovávání tepléúložiště obecně poskytují rychlejší doby odezvy. Když dotaz zahrnuje období uchování teplého úložiště, je podáván z chladírenského úložiště.

Dotazy na teplé úložiště jsou zdarma, zatímco dotazy na chladírenské úložiště vznikají náklady. Je důležité pochopit vzory dotazů a podle toho naplánovat konfiguraci teplého obchodu. Doporučujeme, aby interaktivní analýzy nejnovějších dat byly umístěny ve vašem teplém úložišti a analýze vzorů a dlouhodobé trendy byly umístěny v chladu.

> [!NOTE]
> Další informace o tom, jak zadat dotaz na zahřívaná data, najdete v [referenční příručce rozhraní API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters).

Chcete-li začít, potřebujete tři další položky:

* [Model časové řady](./time-series-insights-update-tsm.md)
* [Zdroj událostí připojený k přehledům časové řady](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Události přetékající do zdroje událostí,](./time-series-insights-send-events.md) které jsou mapovány na model a jsou v platném formátu JSON

## <a name="review-preview-limits"></a>Kontrola limitů náhledu

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Konfigurace ID časových řad a vlastností časového razítka

Chcete-li vytvořit nové prostředí Time Series Insights, vyberte ID časové řady. To funguje jako logický oddíl pro vaše data. Jak již bylo uvedeno, ujistěte se, že máte připravené ID časové řady.

> [!IMPORTANT]
> ID časových řad *nelze později změnit*. Před konečným výběrem a prvním použitím ověřte každý z nich.

Můžete vybrat až tři klíče, které jednoznačně odliší vaše prostředky. Další informace naleznete v [doporučených postupech pro výběr ID časové řady](./time-series-insights-update-how-to-id.md) a [úložiště a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md).

**Vlastnost Časové razítko** je také důležité. Tuto vlastnost můžete určit při přidávání zdrojů událostí. Každý zdroj událostí má volitelnou vlastnost časové razítko, která se používá ke sledování zdrojů událostí v průběhu času. Hodnoty časového razítka rozlišují malá a velká písmena a musí být formátovány podle jednotlivých specifikací jednotlivých zdrojů událostí.

> [!TIP]
> Ověřte požadavky na formátování a analýzu zdrojů událostí.

Pokud je ponecháno prázdné, čas zařazení do fronty událostí zdroje události se používá jako časové razítko události. Pokud odesíláte historická data nebo dávkové události, přizpůsobení vlastnosti Časové razítko je užitečnější než výchozí čas zařazení do fronty událostí. Další informace našly o tom, jak [přidat zdroje událostí v Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Porozumět modelu časové řady

Nyní můžete nakonfigurovat model časové řady Time Series Insights. Nový model usnadňuje vyhledání a analýzu dat IoT. Umožňuje kurování, údržbu a obohacení dat časových řad a pomáhá připravit datové sady připravené pro spotřebitele. Model používá ID časových řad, které se mapují na instanci, která přidružuje jedinečný prostředek k proměnným, označovaným jako typy a hierarchie. Přečtěte si o novém [modelu časové řady](./time-series-insights-update-tsm.md).

Model je dynamický, takže jej lze kdykoli postavit. Chcete-li rychle začít, sestavte a nahrajte je před odesláním dat do přehledů time series. Chcete-li vytvořit model, přečtěte [si použití modelu časové řady](./time-series-insights-update-how-to-tsm.md).

Pro mnoho zákazníků se model časové řady mapuje na existující model aktiv nebo již existující systém ERP. Pokud nemáte existující model, předem vytvořené uživatelské prostředí je [k dispozici](https://github.com/Microsoft/tsiclient) pro rychlé spuštění. Chcete-li si představit, jak vám model může pomoci, prohlédněte si [ukázkové ukázkové prostředí](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Utvářejte své události

Způsob odesílání událostí do přehledů Time Series Insights můžete ověřit. V ideálním případě jsou vaše události denormalized dobře a efektivně.

Dobré pravidlo:

* Ukládejte metadata do modelu časové řady.
* Ujistěte se, že režim časových řad, pole instancí a události obsahují pouze nezbytné informace, například ID časové řady nebo vlastnost časové razítko.

Další informace naleznete v seznamu [Shape events](./time-series-insights-send-events.md#supported-json-shapes).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Další kroky

- Projděte si [Azure Advisor](../advisor/advisor-overview.md) a naplánujte si možnosti konfigurace obnovení podniku.
- Další informace o [ukládání a přenosu dat najdete ve](./time-series-insights-update-storage-ingress.md) verzi Time Series Insights Preview.
- Seznamte se s [datovým modelováním](./time-series-insights-update-tsm.md) v náhledu přehledů časové řady.
