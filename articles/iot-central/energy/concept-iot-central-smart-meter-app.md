---
title: Architektonické koncepty v Azure IoT Central – energetika | Dokumenty společnosti Microsoft
description: Tento článek představuje klíčové koncepty týkající se architektury šablony energetické aplikace Azure IoT Central
author: op-ravi
ms.author: omravi
ms.date: 10/22/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 8f3772c1d65780337c421cfaaa7b70d7ac7186cf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77024309"
---
# <a name="azure-iot-central---smart-meter-app-architecture"></a>Architektura aplikací Azure IoT Central – inteligentní měřič



Tento článek obsahuje přehled architektury šablony aplikace pro monitorování inteligentních měřičů. Následující diagram znázorňuje běžně používanou architekturu pro aplikaci inteligentních měřičů v Azure pomocí platformy IoT Central.

> [!div class="mx-imgBorder"]
> ![architektura inteligentních měřičů](media/concept-iot-central-smart-meter/smart-meter-app-architecture.png)

Tato architektura se skládá z následujících komponent. Některá řešení nemusí vyžadovat všechny zde uvedené součásti.

## <a name="smart-meters-and-connectivity"></a>Inteligentní měřiče a konektivita 

Inteligentní měřič je jedním z nejdůležitějších zařízení ze všech energetických aktiv. Zaznamenává a sděluje údaje o spotřebě energie nástrojům pro monitorování a další případy použití, jako je fakturace a odezva na straně poptávky. Na základě typu měřiče se může připojit k IoT Central buď pomocí bran, nebo pomocí jiných mezilehlých zařízení nebo systémů, jako jsou hraniční zařízení a head-end systémy. Sestavte most zařízení IoT Central pro připojení zařízení, která nelze připojit přímo. Most zařízení IoT Central je open source řešení a kompletní podrobnosti najdete [zde](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge). 


## <a name="iot-central-platform"></a>Platforma IoT Central

Azure IoT Central je platforma, která zjednodušuje vytváření vašeho řešení IoT a pomáhá snižovat zátěž a náklady na správu, provoz a vývoj IoT. S IoT Central můžete snadno připojit, monitorovat a spravovat své prostředky internetu věcí (IoT) ve velkém měřítku. Po připojení inteligentních měřičů k IoT Central používá šablona aplikace integrované funkce, jako jsou modely zařízení, příkazy a řídicí panely. Šablona aplikace také používá úložiště IoT Central pro scénáře teplých cest, jako je monitorování dat měřičů v reálném čase, analýzy, pravidla a vizualizace. 


## <a name="extensibility-options-to-build-with-iot-central"></a>Možnosti rozšiřitelnosti pro sestavení pomocí IoT Central
Platforma IoT Central poskytuje dvě možnosti rozšiřitelnosti: nepřetržitý export dat (CDE) a API. Zákazníci a partneři si mohou vybrat mezi těmito možnostmi založenými na přizpůsobení svých řešení specifickým potřebám. Například jeden z našich partnerů nakonfiguroval CDE s Azure Data Lake Storage (ADLS). Používají ADLS pro dlouhodobé uchovávání dat a další scénáře úložiště studených cest, jako je dávkové zpracování, auditování a vytváření sestav. 

## <a name="next-steps"></a>Další kroky

* Nyní, když jste se dozvěděli o architektuře, [vytvořte aplikaci inteligentního měřiče zdarma](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Další informace o IoT Central najdete v tématu [Přehled IoT Central](https://docs.microsoft.com/azure/iot-central/)
