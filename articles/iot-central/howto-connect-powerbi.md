---
title: Vizualizace dat v Power BI řídicí panel Azure IoT Central | Dokumentace Microsoftu
description: Pomocí řešení Power BI pro Azure IoT Central umožňuje vizualizovat a analyzovat vaše data IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/15/2019
ms.topic: conceptual
ms.openlocfilehash: 322be1e13662d92a3cb0a805a9ccaacd05928f7d
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328091"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Vizualizace a analýza Azure IoT Central dat na řídicím panelu Power BI

*Toto téma se vztahuje na správce.*

![Kanál řešení Power BI](media/howto-connect-powerbi/iot-continuous-data-export.png)

Můžete vytvářet výkonné řídicí panel Power BI k monitorování výkonu zařízení IoT řešení Power BI pro Azure IoT Central. Na řídicím panelu Power BI můžete:
- Sledovat, kolik dat vaše zařízení odesílají v čase
- Porovnat objem dat mezi telemetrie, stavy a události
- Identifikovat zařízení, která se hlásí velké množství měření
- Sledovat historické trendy měření zařízení
- Identifikace problematické zařízení, které posílají velké množství kritické události

Toto řešení nastaví kanál, který přijímá data ve vašem účtu úložiště objektů Blob v Azure z [průběžný Export dat](howto-export-data.md). Tento tok dat do Azure Functions, Azure Data Factory a Azure SQL Database pro zpracování a transformaci dat. Výstup můžete vizualizovat a analyzovat v sestavě Power BI, který si můžete stáhnout jako soubor .PBIX. Všechny tyto prostředky se vytvoří ve vašem předplatném Azure, takže si můžete přizpůsobit tak, aby odpovídala vašim potřebám jednotlivých komponent.

## <a name="get-the-power-bi-solution-for-azure-iot-centralhttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Získejte [Power BI Solution pro Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate) z Microsoft AppSource.

## <a name="prerequisites"></a>Požadavky
Nastavení řešení vyžaduje následující:
- Přístup k předplatnému Azure
- Exportovat data s využitím [průběžný Export dat](howto-export-data.md) z vaší aplikace IoT Central. Doporučujeme že vám zapnout měření, zařízení a zařízení šablony streamování naplno využít řídicí panel Power BI.
- Power BI Desktopu (nejnovější verze)
- Power BI Pro (Pokud chcete řídicí panel sdílet s ostatními)

## <a name="reports"></a>Reports

Dvě sestavy jsou automaticky generovány. 

První zpráva zobrazí Historický přehled průběžně měření, která zařízení a rozdělí různé druhy měření a zařízení, které jste odeslali nejvyšší počet měření.

![Stránka sestavy v Power BI 1](media/howto-connect-powerbi/template-page1-hasdata.PNG)

Druhá sestava věnuje hlouběji do událostí a zobrazí Historický přehled průběžně chyby a varování hlášených. Profil také ukazuje, zařízení, která hlásí nejvyšší počet událostí shrnutí, a také konkrétně chybové události a události varování.

![Sestavy Power BI – stránka 2](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="architecture"></a>Architektura
Všechny prostředky, které byly vytvořeny je přístupný na webu Azure Portal. Všechno, co by měl být v rámci jedné skupiny prostředků.

![Zobrazení Azure Portal, skupiny prostředků](media/howto-connect-powerbi/azure-deployment.PNG)

Níže je popsána podrobností o jednotlivých prostředků a jak se používá.

### <a name="azure-functions"></a>Azure Functions
Aplikace Azure Function app se spustí pokaždé, když je nový soubor zapsán do úložiště objektů Blob. Funkce extrahovat pole v rámci každé měření, zařízení a zařízení soubor šablony a naplní několik převodní tabulky SQL, které má být použit službou Azure Data Factory.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory se připojí ke službě SQL database jako propojenou službu. Spuštění aktivity uložené procedury, které zpracování dat a uložit do tabulky analýzy.

### <a name="azure-sql-database"></a>Azure SQL Database
K naplnění výchozích sestav, které se automaticky vytvoří tyto tabulky. Prozkoumejte tato schémata v Power BI a můžete vytvářet vlastní vizualizace s těmito daty.

| Název tabulky |
|------------|
|[analytics]. [Měření]|
|[analytics]. [Zprávy]|
|[fáze]. [Měření]|
|[analytics]. [Vlastnosti]|
|[analytics].[PropertyDefinitions]|
|[analytics]. [MeasurementDefinitions]|
|[analytics]. [Zařízení]|
|[analytics]. [DeviceTemplates]|
|[dbo].[date]|
|[dbo].[ChangeTracking]|

## <a name="estimated-costs"></a>Odhadované náklady

Tady je zapojené odhad nákladů Azure (funkce Azure Functions, Data Factory, Azure SQL). Všechny ceny jsou uvedeny v USD. Mějte na paměti, že ceny se liší podle oblasti, takže by měl vždy vyhledat nejnovější cen jednotlivých služeb, které mají získat skutečné ceny.
Následující výchozí hodnoty jsou nastavené pro vás v šabloně (můžete upravit některé z těchto po věcí nastavit):

- Služba Azure Functions: Plán služby App Service S1, 74.40 $/ měsíc
- Azure SQL S1, ~$30/month

Doporučujeme vám seznámit se s různé cenové možnosti a upravit možnosti tak, aby odpovídala vašim potřebám.

## <a name="resources"></a>Zdroje a prostředky

Navštivte web AppSource a získat [řešení Power BI pro Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate).

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak vizualizace dat v Power BI, je zde navrhované další krok:

> [!div class="nextstepaction"]
> [Správa zařízení](howto-manage-devices.md)