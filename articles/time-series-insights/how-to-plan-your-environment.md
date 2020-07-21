---
title: Plánování prostředí Gen2 – Azure Time Series Insights | Microsoft Docs
description: Osvědčené postupy pro konfiguraci, správu, plánování a nasazení prostředí Azure Time Series Insights Gen2
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: seodec18
ms.openlocfilehash: a0a17f28f2a87ae42b552bfa07732b8b8232ef5e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531170"
---
# <a name="plan-your-azure-time-series-insights-gen2-environment"></a>Plánování prostředí Azure Time Series Insights Gen2

Tento článek popisuje osvědčené postupy pro plánování a rychlé zprovoznění pomocí Azure Time Series Insights Gen2.

## <a name="best-practices-for-planning-and-preparation"></a>Osvědčené postupy pro plánování a přípravu

Osvědčené postupy při plánování a přípravě prostředí jsou podrobněji popsány v následujících článcích:

* Co dostanete při [zřizování prostředí Azure Time Series Insights Gen2](#the-gen2-environment).
* K čemu [slouží vaše ID časových řad a vlastnosti časového razítka](#configure-time-series-ids-and-timestamp-properties).
* Co je nový [model časové řady](#understand-the-time-series-model)a jak vytvořit vlastní.
* Jak [odesílat události efektivně ve formátu JSON](#shape-your-events).
* Azure Time Series Insights [možností zotavení po havárii firmy](#business-disaster-recovery).

Azure Time Series Insights využívá obchodní model s průběžnými platbami. Pokud chcete získat další informace o nákladech a kapacitě, přečtěte si [Azure Time Series Insights ceny](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-gen2-environment"></a>Prostředí Gen2

Při zřizování prostředí Azure Time Series Insights Gen2 vytvoříte dva prostředky Azure:


* Prostředí Azure Time Series Insights Gen2
* Účet Azure Storage

V rámci procesu zřizování určíte, jestli chcete povolit teplé úložiště. Úložiště pro poskytování vrstvených dotazů vám poskytuje vícevrstvé možnosti. Pokud je povoleno, je nutné zadat dobu uchování mezi 7 a 30 dny. Dotazy spouštěné v období uchovávání teplého úložiště obecně poskytují rychlejší odezvy. Když dotaz pokrývá dobu uchovávání teplého úložiště, je obsluhován z chladírenského skladu.

Dotazy na teplém úložišti jsou bezplatné, zatímco dotazy na chladírenský sklad účtují náklady. Je důležité porozumět vašim vzorům dotazů a odpovídajícím způsobem naplánovat konfiguraci pro svůj záložní obchod. Doporučujeme, aby se interaktivní analýzy na nejaktuálnějších datech nacházely v provozním úložišti a analýze vzorků a dlouhodobé trendy se nachází v chladu.

> [!NOTE]
> Pokud si chcete přečíst další informace o tom, jak zadávat dotazy na zahřívání, přečtěte si [referenci rozhraní API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters).

Začněte tím, že budete potřebovat tři další položky:

* [Model časové řady](./concepts-model-overview.md)
* [Zdroj události připojený k Time Series Insights](./concepts-streaming-ingestion-event-sources.md)
* Události se přenášejí [do zdroje událostí](./time-series-insights-send-events.md) , které jsou namapované na model a jsou v platném formátu JSON.

## <a name="review-azure-time-series-insights-gen2-limits"></a>Kontrola Azure Time Series Insights omezení Gen2

[!INCLUDE [Review Azure Time Series Insights Gen2 limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Konfigurace ID časových řad a vlastností časového razítka

Pokud chcete vytvořit nové prostředí Azure Time Series Insights, vyberte ID časové řady. K tomu slouží jako logický oddíl pro vaše data. Jak je uvedeno, ujistěte se, že máte připravené identifikátory časových řad.

> [!IMPORTANT]
> ID časových řad *nelze později změnit*. Ověřte každý z nich před konečným výběrem a prvním použitím.

Můžete vybrat až tři klíče a jednoznačně odlišit vaše prostředky. Další informace najdete v tématu [osvědčené postupy pro výběr ID časových řad](./time-series-insights-update-how-to-id.md) a [pravidel pro přijímání](concepts-json-flattening-escaping-rules.md).

Vlastnost **časového razítka** je také důležitá. Tuto vlastnost můžete určit při přidávání zdrojů událostí. Každý zdroj události má volitelnou vlastnost časového razítka, která se používá ke sledování zdrojů událostí v průběhu času. Hodnoty časového razítka rozlišují velká a malá písmena a musí být formátovány na jednotlivé specifikace jednotlivých zdrojů událostí.

> [!TIP]
> Ověřte požadavky na formátování a analýzu pro zdroje událostí.

Pokud je ponecháno prázdné, použije se čas zařazení do fronty události jako časové razítko události. Pokud odesíláte historická data nebo dávkové události, přizpůsobení vlastnosti časového razítka je vhodnější než výchozí doba zařazování událostí. Další informace najdete v tématu Postup [Přidání zdrojů událostí do Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Pochopení modelu časové řady

Nyní můžete nakonfigurovat model časové řady Azure Time Series Insightsho prostředí. Nový model usnadňuje hledání a analýzu dat IoT. Umožňuje léčební, údržbu a obohacení dat časových řad a pomáhá připravit datové sady připravené pro uživatele. Model používá ID časových řad, které se mapují na instanci, která přidruží jedinečný prostředek k proměnným, označovaným jako typy a hierarchie. Další informace najdete v článku Přehled [modelu časové řady](./concepts-model-overview.md) .

Model je dynamický, takže se dá vytvořit kdykoli. Aby bylo možné rychle začít, sestavte a nahrajte ho před vložením dat do Azure Time Series Insights. Při sestavování modelu si přečtěte část [použití modelu časové řady](./time-series-insights-update-how-to-tsm.md).

Pro mnoho zákazníků se model časových řad mapuje na existující model assetů nebo systém ERP, který je už na svém místě. Pokud nemáte existující model, je k [dispozici](https://github.com/Microsoft/tsiclient) předem sestavené uživatelské prostředí, které vám umožní rychle začít pracovat. Chcete-li předvést, jak vám model může pomáhat, podívejte se do [ukázkového](https://insights.timeseries.azure.com/preview/demo)ukázkového prostředí.

## <a name="shape-your-events"></a>Natvarování událostí

Můžete ověřit způsob, jakým odesíláte události do Azure Time Series Insights. V ideálním případě jsou vaše události dobře denormalizované a efektivně.

Dobré pravidlo pro palec:

* Uložte metadata v modelu časové řady.
* Zajistěte, aby režim časové řady, pole instancí a události zahrnoval pouze potřebné informace, například ID časové řady nebo vlastnost časového razítka.

Další informace najdete v tématu čtení [událostí obrazce](./time-series-insights-send-events.md#supported-json-shapes).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Další kroky

- Přečtěte si [Azure Advisor](../advisor/advisor-overview.md) a naplánujte si možnosti konfigurace pro obnovení firmy.
- Přečtěte si [Azure Advisor](../advisor/advisor-overview.md) a naplánujte si možnosti konfigurace pro obnovení firmy.
- Přečtěte si další informace o [přijímání dat](./concepts-ingestion-overview.md) v Azure Time Series Insights Gen2.
- Přečtěte si článek o [úložišti dat](./concepts-storage.md) v Azure Time Series Insights Gen2.
- Přečtěte si o [modelování dat](./concepts-model-overview.md) v Azure Time Series Insights Gen2.
