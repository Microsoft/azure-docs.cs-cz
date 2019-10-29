---
title: Přehled transformace
description: Informace o tom, jak transformovat
author: viv-liu
ms.author: viviali
ms.date: 10/11/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 45adcaab427d4e28570b573e913369879bc909c6
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992012"
---
# <a name="transform-your-iot-data-preview-features"></a>Transformace dat IoT (funkce verze Preview)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Jako aplikační platforma IoT Central poskytuje několik klíčových aspektů, které vám pomůžou transformovat data IoT do obchodních přehledů, které mají za úkol reagovat. IoT Central poskytuje možnosti pro rozšiřování dat IoT do externích systémů, aby bylo možné vytvářet integrace s podnikovými aplikacemi a vlastními aplikacemi. Stav zařízení a operace můžete monitorovat vytvořením pravidel, která upozorní techniky prostřednictvím mobilní služby. Můžete vygenerovat konkrétní obchodní přehledy pomocí vlastních analýz a strojového učení tím, že se do služeb v Azure exportují data nezpracovaných telemetrie. Můžete vytvářet služby a nástroje, které monitorují a ovládají zařízení a spravují IoT Central aplikace pomocí veřejných rozhraní API. 

![Přehled transformace v IoT Central](media/overview-iot-central-transform-pnp/transform.png)

## <a name="monitor-device-health-and-operations-using-rules"></a>Monitorování stavu zařízení a operací pomocí pravidel
Jakmile jsou vaše počítače připojené a odesílají data, můžete určit, u kterých počítačů dochází k potížím, nebo odesílat chybové zprávy, aby je bylo možné normálně spouštět s minimálními výpadky. V aplikaci IoT Central můžete vytvořit pravidla pro monitorování telemetrie, kterou vaše zařízení odesílá, a upozorní vás, když byla překročena prahová hodnota nebo byla odeslána konkrétní zpráva události. Pro vaše pravidla můžete nakonfigurovat akce, jako jsou e-mailové akce a Webhooky, které upozorňují na správné uživatele a správné systémy pro příjem dat. Tady najdete další informace o pravidlech.

## <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Spouštění vlastních analýz a zpracování u exportovaných dat
Můžete vygenerovat vysoce vlastní obchodní přehledy, jako je určení trendů efektivity počítače nebo předpověď budoucí spotřeby energie v produkčním prostředí, a to díky vytváření vlastních analytických kanálů pro zpracování vaší nezpracované telemetrie IoT a uložení konečného výsledku. Můžete vytvořit exporty dat v aplikaci IoT Central pro export telemetrie, vlastností zařízení a životního cyklu a šablony zařízení změny v jiných službách Azure, abyste mohli analyzovat, ukládat a vizualizovat data v nástrojích, které jsou pro vás nejdůležitější. Další informace o exportu dat najdete tady.

## <a name="build-custom-iot-solutions-and-integrations-with-apis"></a>Vytváření vlastních řešení IoT a integrace s rozhraními API
Můžete vytvářet řešení IoT, jako jsou mobilní doprovodné aplikace, které můžou vzdáleně řídit zařízení a nastavovat nová zařízení nebo vlastní integrace se stávajícími obchodními aplikacemi, abyste mohli pracovat se zařízeními IoT a daty, která jsou přizpůsobená vašemu podniku. IoT Central můžete použít jako páteřní síť pro modelování zařízení, připojování, správu a přístup k datům. Přečtěte si další informace o veřejném rozhraní API v tomto výukovém modulu.
