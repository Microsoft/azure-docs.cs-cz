---
title: Vizualizace dat IoT Central Azure v řídicím panelu Power BI | Microsoft Docs
description: K vizualizaci a analýze dat IoT Central použijte řešení Power BI pro Azure IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/4/2019
ms.topic: conceptual
ms.openlocfilehash: de22b4431da61af9dbd22ccc024cbd58b6ae4a89
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954302"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Vizualizace a analýza dat IoT Central Azure v řídicím panelu Power BI

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

*Toto téma se týká správců.*

![Power BI kanál řešení](media/howto-connect-powerbi/iot-continuous-data-export.png)

K vytvoření výkonného řídicího panelu Power BI pro monitorování výkonu zařízení IoT použijte Power BI řešení pro Azure IoT Central. Na řídicím panelu Power BI můžete:
- Sledování množství dat, která zařízení odesílají v průběhu času
- Porovnání datových svazků mezi telemetrie, stavy a událostmi
- Identifikujte zařízení, která hlásí spoustu měření.
- Sledujte historické trendy měření zařízení.
- Identifikujte problematická zařízení, která odesílají spoustu kritických událostí.

Toto řešení nastaví kanál, který převezme data z účtu služby Azure Blob Storage ze [souvislého exportu dat](howto-export-data-blob-storage.md). Tato data se převedou do Azure Functions, Azure Data Factory a Azure SQL Database pro zpracování a transformaci dat. Výstup se dá vizuálně analyzovat a analyzovat v sestavě Power BI, kterou si můžete stáhnout jako soubor PBIX. Všechny tyto prostředky se vytvoří ve vašem předplatném Azure, takže můžete jednotlivé komponenty přizpůsobit tak, aby vyhovovaly vašim potřebám.

> [!Note] 
> Řešení Power BI pro Azure IoT Central funguje s IoT Central aplikacemi, které nepodporují IoT technologie Plug and Play (Preview apps dnes).

## <a name="get-the-power-bi-solution-for-azure-iot-centralhttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Získejte [řešení Power BI pro Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate) z Microsoft AppSource.

## <a name="prerequisites"></a>Předpoklady
Nastavení řešení vyžaduje následující:
- Přístup k předplatnému Azure
- IoT Central aplikace, které nepodporují IoT technologie Plug and Play (Preview aplikací dnes)
- Nastavení průběžného exportu dat do Azure Blob Storage z aplikace IoT Central
    - Ujistěte se, že je formát dat Avro
    - Pro maximum na Power BI řídicím panelu doporučujeme zapnout měření, zařízení a datové proudy šablon zařízení.
    - Přečtěte si [, jak nastavit průběžný export dat](howto-export-data-blob-storage.md)
- Power BI Desktop (nejnovější verze)
- Power BI Pro (Pokud chcete řídicí panel sdílet s ostatními)

## <a name="reports"></a>Sestavy

Dvě sestavy se generují automaticky. 

První sestava zobrazuje historický přehled o měřeních hlášených zařízeními a rozděluje různé typy měření a zařízení, které odeslaly nejvyšší počet měření.

![Stránka sestavy Power BI 1](media/howto-connect-powerbi/template-page1-hasdata.PNG)

Druhá sestava komentáře hlouběji do událostí a zobrazuje historické zobrazení chyb a upozornění. Zobrazuje také zařízení, která hlásí nejvyšší počet událostí, a konkrétně události chyb a upozornění.

![Stránka sestavy Power BI 2](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="architecture"></a>Architektura
Všechny prostředky, které byly vytvořeny, mohou být k dispozici v Azure Portal. Vše by mělo být v rámci jedné skupiny prostředků.

![Zobrazení skupiny prostředků na webu Azure Portal](media/howto-connect-powerbi/azure-deployment.PNG)

Konkrétní informace o jednotlivých prostředcích a způsobu jejich použití jsou popsány níže.

### <a name="azure-functions"></a>Funkce Azure
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
|[analýza]. [PropertyDefinitions]|
|[analýza]. [MeasurementDefinitions]|
|[analýza]. Signalizac|
|[analýza]. [DeviceTemplates]|
|[dbo]. Datum|
|[dbo]. Sledování změn ve|

## <a name="estimated-costs"></a>Odhadované náklady

Tady je odhad nákladů na Azure (Azure Functions, Data Factory, Azure SQL). Všechny ceny jsou v USD. Mějte na paměti, že ceny se liší podle oblastí, takže byste měli vždycky vyhledat nejnovější ceny jednotlivých služeb, abyste získali skutečné ceny.
Následující výchozí hodnoty jsou nastaveny pro vás v šabloně (některé z těchto možností můžete po nastavení upravit):

- Azure Functions: App Service Plan S1, $74.40/month
- Azure SQL S1, ~ $30/měsíc

Doporučujeme, abyste se seznámili s různými cenovými možnostmi a vylepšit je podle vašich potřeb.

## <a name="resources"></a>Materiály

Navštivte AppSource a získejte [řešení Power BI pro Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate).

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak vizualizovat data v Power BI, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Správa zařízení](howto-manage-devices.md)