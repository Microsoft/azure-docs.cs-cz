---
title: Plánování prostředí pro Azure Time Series Insights Preview | Microsoft Docs
description: Naplánujte si prostředí pro Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: seodec18
ms.openlocfilehash: dc4336629a4c3b9da906daefca160c5a305603dc
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990849"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Plánování prostředí pro Azure Time Series Insights Preview

Tento článek popisuje osvědčené postupy pro plánování a rychlé zprovoznění pomocí Azure Time Series Insights verze Preview.

> [!NOTE]
> Osvědčené postupy pro plánování obecné dostupnosti Time Series Insights instance najdete v tématu [plánování prostředí pro obecné dostupnosti Azure Time Series Insights](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Osvědčené postupy pro plánování a přípravu

Osvědčené postupy při plánování a přípravě prostředí jsou podrobněji popsány v následujících článcích:

* Co dostanete při [zřizování prostředí Time Series Insights Preview](#the-preview-environment).
* K čemu [slouží vaše ID časových řad a vlastnosti časového razítka](#configure-time-series-ids-and-timestamp-properties).
* Co je nový [model časové řady](#understand-the-time-series-model)a jak vytvořit vlastní.
* Jak [odesílat události efektivně ve formátu JSON](#shape-your-events).
* Time Series Insights [možností zotavení po havárii firmy](#business-disaster-recovery).

Azure Time Series Insights využívá obchodní model s průběžnými platbami. Další informace o nákladech a kapacitě najdete v tématu [Time Series Insights ceny](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>Prostředí verze Preview

Když zřizujete prostředí Time Series Insights Preview, vytvoříte dva prostředky Azure:

* Prostředí Azure Time Series Insights Preview
* Účet Azure Storage pro obecné účely v1

V rámci procesu zřizování určíte, jestli chcete povolit teplé úložiště. Úložiště pro poskytování vrstvených dotazů vám poskytuje vícevrstvé možnosti. Pokud je povoleno, je nutné zadat dobu uchování mezi 7 a 30 dny. Dotazy spouštěné v období uchovávání teplého úložiště obecně poskytují rychlejší odezvy. Když dotaz pokrývá dobu uchovávání teplého úložiště, je obsluhován z chladírenského úložiště.

Dotazy na teplém úložišti jsou bezplatné, zatímco dotazy na chladírenský sklad účtují náklady. Je důležité porozumět vašim vzorům dotazů a odpovídajícím způsobem naplánovat konfiguraci pro svůj záložní obchod. Doporučujeme, aby se interaktivní analýzy na nejaktuálnějších datech nacházely v provozním úložišti a analýze vzorků a dlouhodobé trendy se nachází v chladu.

> [!NOTE]
> V současné době podporujeme maximálně 1 000 vlastností s teplým úložištěm.

Začněte tím, že budete potřebovat tři další položky:

* [Model časové řady](./time-series-insights-update-tsm.md)
* [Zdroj události připojený k Time Series Insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* Události se přenášejí [do zdroje událostí](./time-series-insights-send-events.md) , které jsou namapované na model a jsou v platném formátu JSON.

## <a name="review-preview-limits"></a>Kontrola omezení verze Preview

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Konfigurace ID časových řad a vlastností časového razítka

Pokud chcete vytvořit nové prostředí Time Series Insights, vyberte ID časové řady. K tomu slouží jako logický oddíl pro vaše data. Jak je uvedeno, ujistěte se, že máte připravené identifikátory časových řad.

> [!IMPORTANT]
> ID časových řad *nelze později změnit*. Ověřte každý z nich před konečným výběrem a prvním použitím.

Můžete vybrat až tři klíče a jednoznačně odlišit vaše prostředky. Další informace najdete v článku [osvědčené postupy pro výběr ID](./time-series-insights-update-how-to-id.md) a [úložiště](./time-series-insights-update-storage-ingress.md)časových řad a příchozího přenosu dat.

Vlastnost **časového razítka** je také důležitá. Tuto vlastnost můžete určit při přidávání zdrojů událostí. Každý zdroj události má volitelnou vlastnost časového razítka, která se používá ke sledování zdrojů událostí v průběhu času. Hodnoty časového razítka rozlišují velká a malá písmena a musí být formátovány na jednotlivé specifikace jednotlivých zdrojů událostí.

> [!TIP]
> Ověřte požadavky na formátování a analýzu pro zdroje událostí.

Pokud je ponecháno prázdné, použije se čas zařazení do fronty události jako časové razítko události. Pokud odesíláte historická data nebo dávkové události, přizpůsobení vlastnosti časového razítka je vhodnější než výchozí doba zařazování událostí. Další informace najdete v tématu Postup [Přidání zdrojů událostí do Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Pochopení modelu časové řady

Nyní můžete nakonfigurovat model časové řady Time Series Insightsho prostředí. Nový model usnadňuje hledání a analýzu dat IoT. Umožňuje léčební, údržbu a obohacení dat časových řad a pomáhá připravit datové sady připravené pro uživatele. Model používá ID časových řad, které se mapují na instanci, která přidruží jedinečný prostředek k proměnným, označovaným jako typy a hierarchie. Přečtěte si o novém [modelu časové řady](./time-series-insights-update-tsm.md).

Model je dynamický, takže se dá vytvořit kdykoli. Aby bylo možné rychle začít, sestavte a nahrajte ho před vložením dat do Time Series Insights. Informace o sestavování modelu najdete v tématu [použití modelu časové řady](./time-series-insights-update-how-to-tsm.md).

Pro mnoho zákazníků se model časových řad mapuje na existující model assetů nebo systém ERP, který je už na svém místě. Pokud nemáte existující model, je k [dispozici](https://github.com/Microsoft/tsiclient) předem sestavené uživatelské prostředí, které vám umožní rychle začít pracovat. Chcete-li předvést, jak vám model může pomáhat, podívejte se do [ukázkového](https://insights.timeseries.azure.com/preview/demo)ukázkového prostředí.

## <a name="shape-your-events"></a>Natvarování událostí

Můžete ověřit způsob, jakým odesíláte události do Time Series Insights. V ideálním případě jsou vaše události dobře denormalizované a efektivně.

Dobré pravidlo pro palec:

* Uložte metadata v modelu časové řady.
* Zajistěte, aby režim časové řady, pole instancí a události zahrnoval pouze potřebné informace, například ID časové řady nebo vlastnost časového razítka.

Další informace najdete v tématu [události obrazce](./time-series-insights-send-events.md#supported-json-shapes).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Další kroky

- Přečtěte si [Azure Advisor](../advisor/advisor-overview.md) a naplánujte si možnosti konfigurace pro obnovení firmy.
- Přečtěte si další informace o službě [Storage a](./time-series-insights-update-storage-ingress.md) příchozím přenosu ve službě Time Series Insights Preview.
- Přečtěte si o [modelování dat](./time-series-insights-update-tsm.md) ve verzi Preview Time Series Insights.
