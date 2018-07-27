---
title: Vizualizace dat v Power BI řídicí panel Azure IoT Central | Dokumentace Microsoftu
description: Pomocí šablony řešení Azure IoT Central analýzy Power BI umožňuje vizualizovat a analyzovat vaše data IoT Central.
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: peterpr
ms.openlocfilehash: 1298ba85a221368d9153fea4f0311935dd51ff0f
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285707"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Vizualizace a analýza Azure IoT Central dat na řídicím panelu Power BI

![Kanál šablony řešení Power BI](media/howto-connect-powerbi/iot-continuous-data-export.png)

Použijte šablonu řešení Azure IoT Central analýzy Power BI k vytvoření výkonné řídicí panel Power BI k monitorování výkonu zařízení IoT. Na řídicím panelu Power BI můžete:
- Sledovat, kolik dat vaše zařízení odesílají v čase
- Porovnat objem dat mezi telemetrie, stavy a události
- Identifikovat zařízení, která se hlásí velké množství měření
- Sledovat historické trendy měření zařízení
- Identifikace problematické zařízení, které posílají velké množství kritické události

Tato šablona řešení nastaví kanál, který přijímá data ve vašem účtu úložiště objektů Blob v Azure z [nepřetržitý export dat](howto-export-data.md). Tento tok dat do Azure Functions, Azure Data Factory a Azure SQL Database, které zpracovávat a transformovat data, která mají být vizualizovat a analyzovat v sestavě Power BI, který si můžete stáhnout jako soubor .PBIX. Všechny tyto prostředky se vytvoří ve vašem předplatném Azure, takže si můžete přizpůsobit tak, aby odpovídala vašim potřebám jednotlivých komponent. Tato šablona řešení je zcela open source, takže můžete další informace o architektuře a rozšířit řešení najdete [úložiště Github se vzorovými](https://aka.ms/iotcentralgithubpowerbisolutiontemplate).

**[Získání šablony řešení Azure IoT Central analýz z Microsoft AppSource.](https://aka.ms/iotcentralpowerbisolutiontemplate)**

## <a name="prerequisites"></a>Požadavky
Nastavení šablony vyžaduje následující:
- Přístup k předplatnému Azure
- Exportovat data s využitím [nepřetržitý export dat](howto-export-data.md) z vaší aplikace IoT Central. Doporučujeme že vám zapnout měření, zařízení a zařízení šablony datové proudy, chcete - li maximálně na řídicí panel Power BI.
- Power BI Desktopu (nejnovější verze)
- Power BI Pro (ke sdílení s ostatními šablony)

## <a name="reports"></a>Reports

Dvě sestavy jsou automaticky generovány. 

První zpráva zobrazí Historický přehled průběžně měření, která zařízení a rozdělí různé druhy měření a zařízení, které jste odeslali nejvyšší počet měření.

![Stránka sestavy v Power BI 1](media/howto-connect-powerbi/template-page1-hasdata.PNG)

Druhá sestava věnuje hlouběji do událostí a zobrazí Historický přehled průběžně chyby a varování hlášených. Profil také ukazuje, zařízení, která hlásí nejvyšší počet událostí shrnutí, a také konkrétně chybové události a události varování.

![Sestavy Power BI – stránka 2](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="resources"></a>Zdroje a prostředky

Navštivte web AppSource a získat [šablonu řešení Azure IoT Central Analytics](https://aka.ms/iotcentralpowerbisolutiontemplate).

Přejděte [úložiště Github se vzorovými](https://aka.ms/iotcentralgithubpowerbisolutiontemplate) Další informace o architektuře a rozšířit řešení.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak vizualizace dat v Power BI, je zde navrhované další krok:

> [!div class="nextstepaction"]
> [Správa zařízení](howto-manage-devices.md)
