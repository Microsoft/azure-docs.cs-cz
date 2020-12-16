---
title: Koncepty architektury v Azure IoT Central – sluneční panel | Microsoft Docs
description: Tento článek představuje klíčové koncepty, které se týkají architektury aplikace pro monitorování v Azure IoT Central slunečním panelu.
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: cd35381e4c2cdb849662ad134cfbef8229707eed
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516639"
---
# <a name="azure-iot-central---solar-panel-app-architecture"></a>Architektura aplikace panelu Azure IoT Central-slunečního panelu

Tento článek poskytuje přehled architektury šablon aplikací pro monitorování v panelu slunečního vstupu. Následující diagram ukazuje běžně použitou architekturu pro aplikaci slunečního panelu v Azure s využitím IoT Central platformy.

> [!div class="mx-imgBorder"]
> ![Architektura inteligentního měření](media/concept-iot-central-solar-panel/solar-panel-app-architecture.png)

Tato architektura se skládá z následujících komponent. Některé aplikace nemusí vyžadovat všechny zde uvedené komponenty.

## <a name="solar-panels-and-connectivity"></a>Sluneční panely a připojení

Sluneční panely jsou jedním z významných zdrojů obnovitelné energie. V závislosti na typu a nastavení panelu slunečního připojení se můžete připojit buď pomocí bran, nebo jiných zprostředkujících zařízení a proprietárních systémů. Možná budete muset vytvořit most IoT Central zařízení pro připojení zařízení, která se nedají připojit přímo. Most zařízení IoT Central je open source řešení a [tady](../core/howto-build-iotc-device-bridge.md)můžete najít kompletní podrobnosti. 

## <a name="iot-central-platform"></a>IoT Central platforma
Azure IoT Central je platforma, která zjednodušuje vytváření řešení IoT a pomáhá snižovat zatížení a náklady na správu, provoz a vývoj IoT. Díky IoT Central se můžete snadno připojit, monitorovat a spravovat vaše prostředky Internet věcí (IoT) ve velkém měřítku. Po připojení vašich slunečních panelů k IoT Central se v šabloně aplikace používají předdefinované funkce, jako jsou například modely zařízení, příkazy a řídicí panely. Šablona aplikace také používá IoT Central úložiště pro scénáře teplé cesty, jako jsou monitorování, analýzy, pravidla a vizualizace dat měřiče v reálném čase.


## <a name="extensibility-options-to-build-with-iot-central"></a>Možnosti rozšíření pro sestavení pomocí IoT Central
Platforma IoT Central poskytuje dvě možnosti rozšíření: průběžné exporty dat (CDE) a rozhraní API. Zákazníci a partneři si můžou vybrat mezi těmito možnostmi a přizpůsobit jejich řešení konkrétním potřebám. Například jeden z našich partnerů nakonfiguroval CDE pomocí Azure Data Lake Storage (ADLS). Používají ADLS pro dlouhodobé uchovávání dat a jiné scénáře úložiště s studenými cestami, jako jsou například dávkové zpracování, auditování a vytváření sestav. 

## <a name="next-steps"></a>Další kroky

* Teď, když jste se seznámili s architekturou, můžete [zdarma vytvořit aplikaci slunečního panelu](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring) .
* Další informace o IoT Central najdete v tématu [IoT Central Overview](../index.yml) .
