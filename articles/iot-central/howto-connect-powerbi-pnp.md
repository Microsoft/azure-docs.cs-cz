---
title: Vizualizace dat IoT Central Azure v řídicím panelu Power BI | Microsoft Docs
description: K vizualizaci a analýze dat IoT Central použijte řešení Power BI pro Azure IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/09/2019
ms.topic: conceptual
ms.openlocfilehash: dcfabbfe5bf807e8820fa14b6b8234d276578dcc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880069"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard-preview-features"></a>Vizualizace a analýza dat IoT Central Azure v řídicím panelu Power BI (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Toto téma se týká správců.*

![Power BI kanál řešení](media/howto-connect-powerbi-pnp/iot-continuous-data-export.png)

K vytvoření výkonného řídicího panelu Power BI pro monitorování výkonu zařízení IoT použijte Power BI řešení pro Azure IoT Central. Na řídicím panelu Power BI můžete:
- Sledování množství dat, která zařízení odesílají v průběhu času
- Porovnání datových svazků mezi telemetrie, stavy a událostmi
- Identifikujte zařízení, která hlásí spoustu měření.
- Sledujte historické trendy měření zařízení.
- Identifikujte problematická zařízení, která odesílají spoustu kritických událostí.

Toto řešení nastaví kanál, který převezme data z účtu služby Azure Blob Storage ze [souvislého exportu dat](howto-export-data-blob-storage-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). Tato data se převedou do Azure Functions, Azure Data Factory a Azure SQL Database pro zpracování a transformaci dat. Výstup se dá vizuálně analyzovat a analyzovat v sestavě Power BI, kterou si můžete stáhnout jako soubor PBIX. Všechny tyto prostředky se vytvoří ve vašem předplatném Azure, takže můžete jednotlivé komponenty přizpůsobit tak, aby vyhovovaly vašim potřebám.

## <a name="get-the-power-bi-solution-for-azure-iot-centralhttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Získejte [řešení Power BI pro Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate) z Microsoft AppSource.

## <a name="prerequisites"></a>Požadavky
Nastavení řešení vyžaduje následující:
- Přístup k předplatnému Azure
- Exportovaná data pomocí [průběžného exportu dat](howto-export-data-blob-storage-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) z aplikace IoT Central. Pro maximum na Power BI řídicím panelu doporučujeme zapnout měření, zařízení a datové proudy šablon zařízení.
- Power BI Desktop (nejnovější verze)
- Power BI Pro (Pokud chcete řídicí panel sdílet s ostatními)

## <a name="reports"></a>Sestavy

Dvě sestavy se generují automaticky. 

První sestava zobrazuje historický přehled o měřeních hlášených zařízeními a rozděluje různé typy měření a zařízení, které odeslaly nejvyšší počet měření.

![Stránka sestavy Power BI 1](media/howto-connect-powerbi-pnp/template-page1-hasdata.PNG)

Druhá sestava komentáře hlouběji do událostí a zobrazuje historické zobrazení chyb a upozornění. Zobrazuje také zařízení, která hlásí nejvyšší počet událostí, a konkrétně události chyb a upozornění.

![Stránka sestavy Power BI 2](media/howto-connect-powerbi-pnp/template-page2-hasdata.PNG)

## <a name="architecture"></a>Architektura
Všechny prostředky, které byly vytvořeny, mohou být k dispozici v Azure Portal. Vše by mělo být v rámci jedné skupiny prostředků.

![Zobrazení skupiny prostředků na webu Azure Portal](media/howto-connect-powerbi-pnp/azure-deployment.PNG)

Konkrétní informace o jednotlivých prostředcích a způsobu jejich použití jsou popsány níže.

### <a name="azure-functions"></a>Azure Functions
Aplikace Azure Functions se aktivuje při každém zápisu nového souboru do úložiště objektů BLOB. Funkce rozbalí pole v rámci jednotlivých měření, zařízení a souborů šablon zařízení a naplní několik mezilehlých tabulek SQL, které bude Azure Data Factory používat.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory se připojí k databázi SQL jako propojená služba. Spouští aktivity uložených procedur, které zpracovávají data a ukládají je do tabulek analýzy.

### <a name="azure-sql-database"></a>Azure SQL Database
Tyto tabulky jsou automaticky vytvořeny pro naplnění výchozích sestav. Prozkoumejte tato schémata v Power BI a můžete na těchto datech vytvořit vlastní vizualizace.

| Název tabulky |
|------------|
|[analýza]. Jednotek|
|[analýza]. Zprávy|
|[fáze]. Jednotek|
|[analýza]. Vlastnosti|
|[analytics].[PropertyDefinitions]|
|[analýza]. [MeasurementDefinitions]|
|[analýza]. Signalizac|
|[analýza]. [DeviceTemplates]|
|[dbo].[date]|
|[dbo].[ChangeTracking]|

## <a name="estimated-costs"></a>Odhadované náklady

Tady je odhad nákladů na Azure (Azure Functions, Data Factory, Azure SQL). Všechny ceny jsou v USD. Mějte na paměti, že ceny se liší podle oblastí, takže byste měli vždycky vyhledat nejnovější ceny jednotlivých služeb, abyste získali skutečné ceny.
Následující výchozí hodnoty jsou nastaveny pro vás v šabloně (některé z těchto možností můžete po nastavení upravit):

- Azure Functions: App Service plán S1, $74.40/měsíc
- Azure SQL S1, ~ $30/měsíc

Doporučujeme, abyste se seznámili s různými cenovými možnostmi a vylepšit je podle vašich potřeb.

## <a name="resources"></a>Zdroje a prostředky

Navštivte AppSource a získejte [řešení Power BI pro Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate).

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak vizualizovat data v Power BI, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Správa zařízení](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)