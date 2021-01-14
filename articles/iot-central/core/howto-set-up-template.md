---
title: Definování nového typu zařízení IoT v Azure IoT Central | Microsoft Docs
description: V tomto článku se dozvíte, jak můžete jako tvůrce řešení vytvořit novou šablonu zařízení Azure IoT v aplikaci Azure IoT Central. Definujete telemetrii, stav, vlastnosti a příkazy pro svůj typ.
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom:
- contperf-fy21q1
- device-developer
ms.openlocfilehash: 236acc2ded3fcb651295e0342ab4e1e88174be46
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202959"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Definování nového typu zařízení IoT v aplikaci Azure IoT Central

*Tento článek se týká tvůrců řešení a vývojářů zařízení.*

Šablona zařízení je podrobný plán, který definuje charakteristiky a chování typu zařízení, které se připojuje k [aplikaci Azure IoT Central](concepts-app-templates.md).

Tvůrce může například vytvořit šablonu zařízení pro připojený ventilátor, která má následující vlastnosti:

- Odesílá telemetrii teploty.
- Odeslat vlastnost Location
- Odesílá chybové události motocyklu pro ventilátor.
- Odešle provozní stav ventilátoru.
- Poskytuje vlastnost rychlosti ventilátoru s možností zápisu.
- Poskytuje příkaz k restartování zařízení.
- Poskytuje celkový přehled o zařízení prostřednictvím řídicího panelu.

Z této šablony zařízení může operátor vytvářet a připojovat reálné ventilátory. Všechny tyto ventilátory mají měření, vlastnosti a příkazy, které operátory používají pro monitorování a správu. Operátory používají [řídicí panely](#add-dashboards) a formuláře zařízení k interakci se zařízeními ventilátoru. Vývojář zařízení používá šablonu k pochopení, jak zařízení komunikuje s aplikací. Další informace najdete v tématu [telemetrie, vlastnosti a datové části příkazů](concepts-telemetry-properties-commands.md).

> [!NOTE]
> Šablony zařízení můžou vytvářet, upravovat a odstraňovat jenom tvůrci a správci. Každý uživatel může vytvořit zařízení na stránce **zařízení** z existujících šablon zařízení.

V IoT Central aplikaci šablona zařízení používá model zařízení k popisu možností zařízení. Jako tvůrce máte k dispozici několik možností pro vytváření šablon zařízení:

- Navrhněte šablonu zařízení v IoT Central a potom [do kódu zařízení implementujte svůj model zařízení](concepts-telemetry-properties-commands.md).
- Importujte šablonu zařízení z [katalogu zařízení Azure Certified for IoT](https://aka.ms/iotdevcat). Upravte šablonu zařízení podle požadavků v IoT Central.
> [!NOTE]
> IoT Central vyžaduje úplný model se všemi odkazovanými rozhraními ve stejném souboru, při importu modelu z úložiště modelu použijte k získání plné verze klíčové slovo Expanded.
Například. https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json

- Vytvoření modelu zařízení pomocí [jazyka DTDL (Digital vláknas Definition Language) verze 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). Visual Studio Code má rozšíření, které podporuje vytváření DTDL modelů. Další informace najdete v tématu [instalace a použití nástrojů pro vytváření DTDL](../../iot-pnp/howto-use-dtdl-authoring-tools.md). Pak model publikujte do úložiště veřejného modelu. Další informace najdete v tématu [úložiště modelů zařízení](../../iot-pnp/concepts-model-repository.md). Implementujte kód zařízení z modelu a připojte skutečné zařízení k vaší IoT Central aplikaci. IoT Central najde a naimportuje model zařízení z veřejného úložiště za vás a vygeneruje šablonu zařízení. Pak můžete přidat libovolné vlastnosti cloudu, vlastní nastavení a řídicí panely, které vaše aplikace IoT Central potřebuje k šabloně zařízení.
- Vytvořte model zařízení pomocí nástroje DTDL. Implementujte kód zařízení z modelu. Ručně importujte model zařízení do aplikace IoT Central a pak přidejte všechny vlastnosti cloudu, přizpůsobení a řídicí panely, které aplikace IoT Central potřebuje.

> [!TIP]
> IoT Central vyžaduje úplný model se všemi odkazovanými rozhraními ve stejném souboru. Při importu modelu z úložiště modelu použijte k získání plné verze klíčové slovo *Expanded* .
> Příklad: [https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json](https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json).

Můžete také přidat šablony zařízení do aplikace IoT Central pomocí [REST API](/learn/modules/manage-iot-central-apps-with-rest-api/) nebo rozhraní příkazového [řádku](howto-manage-iot-central-from-cli.md).

Některé [šablony aplikací](concepts-app-templates.md) již obsahují šablony zařízení, které jsou užitečné ve scénáři, který podporuje šablona aplikace. Například viz [Analytická architektura v úložišti](../retail/store-analytics-architecture.md).

## <a name="create-a-device-template-from-the-device-catalog"></a>Vytvoření šablony zařízení z katalogu zařízení

Jako tvůrce můžete rychle začít vytvářet vaše řešení pomocí certifikovaného zařízení. Podívejte se na seznam v [katalogu zařízení Azure IoT](https://catalog.azureiotsolutions.com/alldevices). IoT Central se integruje s katalogem zařízení, abyste mohli naimportovat model zařízení z některého z certifikovaných zařízení. Chcete-li vytvořit šablonu zařízení z jednoho z těchto zařízení v IoT Central:

1. V aplikaci IoT Central otevřete stránku **šablony zařízení** .
1. Vyberte **+ Nová** a potom z katalogu vyberte kterákoli z certifikovaných zařízení. IoT Central vytvoří šablonu zařízení založenou na tomto modelu zařízení.
1. Přidejte do šablony zařízení všechny vlastnosti cloudu, vlastní nastavení nebo zobrazení.
1. Vyberte **publikovat** a zpřístupněte tak šablonu pro operátory k zobrazení a připojení zařízení.

## <a name="create-a-device-template-from-scratch"></a>Vytvoření zcela nové šablony zařízení

Šablona zařízení obsahuje:

- _Model zařízení_ , který určuje telemetrii, vlastnosti a příkazy, které zařízení implementuje. Tyto možnosti jsou uspořádány do jedné nebo více součástí.
- _Vlastnosti cloudu_ , které definují informace, které vaše aplikace IoT Central ukládá na vaše zařízení. Například vlastnost cloudu může zaznamenat datum, kdy bylo zařízení naposledy obsluhované. Tyto informace se zařízení nikdy nesdílí.
- _Přizpůsobení_ umožní tvůrci přepsat některé definice v modelu zařízení. Tvůrce může například přepsat název vlastnosti zařízení. Názvy vlastností se zobrazí v IoT Central řídicích panelech a formulářích.
- _Řídicí panely a formuláře_ umožňují tvůrci vytvořit uživatelské rozhraní, které umožňuje operátorům monitorovat a spravovat zařízení připojená k vaší aplikaci.

Vytvoření šablony zařízení v IoT Central:

1. V aplikaci IoT Central otevřete stránku **šablony zařízení** .
1. Vyberte **+ nové**  >  **zařízení IoT**. Pak vyberte **Další: přizpůsobit**.
1. Zadejte název šablony, například **termostat**. Pak vyberte **Další: Zkontrolujte** a pak vyberte **vytvořit**.
1. IoT Central vytvoří prázdnou šablonu zařízení a umožní vám vytvořit vlastní model od začátku nebo importovat model DTDL.

## <a name="manage-a-device-template"></a>Správa šablony zařízení

Šablonu můžete přejmenovat nebo odstranit z domovské stránky šablony.

Po přidání modelu zařízení do šablony jej můžete publikovat. Dokud šablonu nepublikujete, nemůžete připojit zařízení založené na této šabloně pro vaše operátory a zobrazit je na stránce **zařízení** .

## <a name="create-a-capability-model"></a>Vytvoření modelu schopností

Pokud chcete vytvořit model zařízení, můžete:

- Pomocí IoT Central vytvořit vlastní model od začátku.
- Importujte model DTDL ze souboru JSON. Tvůrce zařízení mohl použít Visual Studio Code k vytvoření modelu zařízení pro vaši aplikaci.
- Vyberte jedno ze zařízení z katalogu zařízení. Tato možnost importuje model zařízení, který výrobce pro toto zařízení publikoval. Automaticky se publikuje model zařízení importovaný jako takový.

## <a name="manage-a-capability-model"></a>Správa modelu schopností

Po vytvoření modelu zařízení můžete:

- Přidejte součásti do modelu. Model musí mít alespoň jednu komponentu.
- Upravte metadata modelu, jako je jeho ID, obor názvů a název.
- Odstraňte model.

## <a name="create-a-component"></a>Vytvoření komponenty

Model zařízení musí mít alespoň jednu výchozí komponentu. Součást je opakovaně použitelná kolekce funkcí.

Vytvoření komponenty:

1. Přejít do modelu zařízení a vybrat **+ Přidat komponentu**.

1. Na stránce **Přidat rozhraní součásti** můžete:

    - Vytvořte vlastní komponentu od začátku.
    - Importuje existující součást ze souboru DTDL. Tvůrce zařízení mohl použít Visual Studio Code k vytvoření rozhraní komponenty pro vaše zařízení.

1. Po vytvoření součásti vyberte možnost **Upravit identitu** a změňte zobrazovaný název součásti.

1. Pokud se rozhodnete vytvořit vlastní komponentu od začátku, můžete přidat možnosti svého zařízení. Možnosti zařízení jsou telemetrie, vlastnosti a příkazy.

### <a name="telemetry"></a>Telemetrie

Telemetrie je proud hodnot odeslaných ze zařízení, typicky ze senzoru. Senzor může například ohlásit okolní teplotu.

Následující tabulka ukazuje nastavení konfigurace pro schopnost telemetrie:

| Pole | Description |
| ----- | ----------- |
| Zobrazovaný název | Zobrazovaný název hodnoty telemetrie používané na řídicích panelech a formulářích |
| Name | Název pole ve zprávě telemetrie IoT Central vygeneruje hodnotu pro toto pole ze zobrazovaného názvu, ale v případě potřeby můžete zvolit vlastní hodnotu. Toto pole musí být alfanumerické. |
| Typ funkce | Telemetrie. |
| Sémantický typ | Sémantický typ telemetrie, jako je například teplota, stav nebo událost. Volba sémantického typu Určuje, která z následujících polí je k dispozici. |
| Schéma | Datový typ telemetrie, například Double, String nebo Vector. Dostupné možnosti určují sémantický typ. Schéma není k dispozici pro sémantické typy události a stavu. |
| Závažnost | K dispozici pouze pro sémantický typ události. Závažnosti jsou **chyby**, **informace** nebo **Upozornění**. |
| Hodnoty stavu | K dispozici pouze pro sémantický typ State. Definujte hodnoty možných stavů, z nichž každá má zobrazované jméno, název, Výčtový typ a hodnotu. |
| Jednotka | Jednotka pro hodnotu telemetrie, například **mph**, **%** nebo **&deg; C**. |
| Zobrazit jednotku | Zobrazovací jednotka pro použití na řídicích panelech a formulářích. |
| Komentář | Jakékoli komentáře k schopnosti telemetrie. |
| Description | Popis schopnosti telemetrie. |

### <a name="properties"></a>Vlastnosti

Vlastnosti znázorňují hodnoty bodu v čase. Zařízení může například pomocí vlastnosti nahlásit cílovou teplotu, se kterou se snaží dosáhnout. Můžete nastavit vlastnosti s možností zápisu z IoT Central.

Následující tabulka ukazuje nastavení konfigurace pro schopnost vlastnosti:

| Pole | Description |
| ----- | ----------- |
| Zobrazovaný název | Zobrazovaný název hodnoty vlastnosti používané na řídicích panelech a formulářích. |
| Name | Název vlastnosti IoT Central vygeneruje hodnotu pro toto pole ze zobrazovaného názvu, ale v případě potřeby můžete zvolit vlastní hodnotu. Toto pole musí být alfanumerické. |
| Typ funkce | Majetek. |
| Sémantický typ | Sémantický typ vlastnosti, jako je například teplota, stav nebo událost. Volba sémantického typu Určuje, která z následujících polí je k dispozici. |
| Schéma | Datový typ vlastnosti, například Double, String nebo Vector. Dostupné možnosti určují sémantický typ. Schéma není k dispozici pro sémantické typy události a stavu. |
| Pro zápis | Pokud vlastnost není zapisovatelná, může zařízení nahlásit hodnoty vlastností do IoT Central. Pokud je vlastnost zapisovatelná, může zařízení nahlásit hodnoty vlastností IoT Central a IoT Central může odesílat aktualizace vlastností do zařízení.
| Závažnost | K dispozici pouze pro sémantický typ události. Závažnosti jsou **chyby**, **informace** nebo **Upozornění**. |
| Hodnoty stavu | K dispozici pouze pro sémantický typ State. Definujte hodnoty možných stavů, z nichž každá má zobrazované jméno, název, Výčtový typ a hodnotu. |
| Jednotka | Jednotka pro hodnotu vlastnosti, například **mph**, **%** nebo **&deg; C**. |
| Zobrazit jednotku | Zobrazovací jednotka pro použití na řídicích panelech a formulářích. |
| Komentář | Jakékoli komentáře k funkci vlastnosti. |
| Description | Popis schopnosti vlastnosti. |

### <a name="commands"></a>Příkazy

Příkazy zařízení můžete volat z IoT Central. Příkazy volitelně předají do zařízení parametry a obdrží odpověď ze zařízení. Například můžete zavolat příkaz k restartování zařízení za 10 sekund.

Následující tabulka ukazuje nastavení konfigurace pro funkci příkazu:

| Pole | Description |
| ----- | ----------- |
| Zobrazovaný název | Zobrazovaný název příkazu, který se používá na řídicích panelech a formulářích. |
| Name | Název příkazu IoT Central vygeneruje hodnotu pro toto pole ze zobrazovaného názvu, ale v případě potřeby můžete zvolit vlastní hodnotu. Toto pole musí být alfanumerické. |
| Typ funkce | Systému. |
| Komentář | Jakékoli komentáře k funkci příkazu. |
| Description | Popis funkce příkazu |
| Žádost | Pokud je povoleno, definice parametru Request, včetně názvu, zobrazovaného názvu, schématu, jednotky a zobrazované jednotky. |
| Odpověď | Pokud je povoleno, definice odpovědi příkazu, včetně názvu, zobrazovaného názvu, schématu, jednotky a zobrazované jednotky, je-li povolena. |

Další informace o tom, jak zařízení implementují příkazy, najdete v tématech [telemetrie, vlastnost a datové části příkazu > příkazy a dlouhé běžící příkazy](concepts-telemetry-properties-commands.md#commands).

#### <a name="offline-commands"></a>Offline příkazy

Příkazy Queue můžete zvolit, pokud je zařízení aktuálně offline. Pokud je v šabloně zařízení možnost offline pro příkaz, povolte **frontu** .

Tato možnost používá IoT Hub zpráv z cloudu na zařízení k odesílání oznámení do zařízení. Další informace najdete v článku IoT Hub [posílání zpráv z cloudu na zařízení](../../iot-hub/iot-hub-devguide-messages-c2d.md).

Zprávy z cloudu do zařízení:

- Jednosměrná oznámení k zařízení z vašeho řešení.
- Garantuje doručení zpráv alespoň jednou. IoT Hub zachovává zprávy z cloudu na zařízení v rámci front zařízení a zaručuje odolnost proti chybám připojení a selhání zařízení.
- Vyžaduje, aby zařízení implementovalo obslužnou rutinu zpráv pro zpracování zprávy typu cloud-zařízení.

> [!NOTE]
> Tato možnost je k dispozici pouze ve webovém uživatelském rozhraní IoT Central. Toto nastavení není zahrnuté při exportu modelu nebo součásti ze šablony zařízení.

## <a name="manage-a-component"></a>Správa součásti

Pokud jste tuto součást nepublikovali, můžete upravit možnosti definované součástí. Pokud chcete provést změny, musíte po publikování komponenty vytvořit novou verzi šablony zařízení a [její verzi](howto-version-device-template.md). V části **přizpůsobení** můžete provádět změny, které nevyžadují správu verzí, jako jsou názvy zobrazení nebo jednotky.

Komponentu můžete také exportovat jako soubor JSON, pokud ho chcete znovu použít v jiném modelu schopností.

## <a name="add-cloud-properties"></a>Přidání vlastností cloudu

Pomocí vlastností cloudu můžete ukládat informace o zařízeních v IoT Central. Vlastnosti cloudu se nikdy neodesílají do zařízení. Pomocí vlastností cloudu můžete například uložit jméno zákazníka, který má nainstalované zařízení, nebo datum poslední služby daného zařízení.

Následující tabulka ukazuje nastavení konfigurace pro cloudovou vlastnost:

| Pole | Description |
| ----- | ----------- |
| Zobrazovaný název | Zobrazovaný název hodnoty vlastnosti cloudu používaný na řídicích panelech a formulářích. |
| Name | Název vlastnosti cloudu IoT Central vygeneruje hodnotu pro toto pole ze zobrazovaného názvu, ale v případě potřeby můžete zvolit vlastní hodnotu. |
| Sémantický typ | Sémantický typ vlastnosti, jako je například teplota, stav nebo událost. Volba sémantického typu Určuje, která z následujících polí je k dispozici. |
| Schéma | Datový typ cloudové vlastnosti, jako je například Double, String nebo Vector. Dostupné možnosti určují sémantický typ. |

## <a name="add-customizations"></a>Přidat přizpůsobení

Vlastní nastavení použijte v případě, že potřebujete upravit importovanou součást nebo přidat funkce specifické pro IoT Central do funkce. Můžete přizpůsobit pouze pole, která neruší kompatibilitu součástí. Můžete například:

- Přizpůsobení zobrazovaného názvu a jednotek schopnosti.
- Přidejte výchozí barvu, která se použije, když se hodnota zobrazí v grafu.
- Zadejte počáteční, minimální a maximální hodnoty vlastnosti.

Nemůžete přizpůsobit název schopnosti ani typ schopnosti. Pokud se vyskytnou změny, nemůžete dělat v části **vlastní nastavení** , takže budete muset verzi šablony a součásti zařízení upravit.

### <a name="generate-default-views"></a>Generovat výchozí zobrazení

Generování výchozích zobrazení je rychlý způsob, jak vizualizovat důležité informace o zařízení. Máte k dispozici až tři výchozí zobrazení vygenerovaná pro šablonu zařízení:

- **Příkazy**: zobrazení s příkazy zařízení a umožňuje operátorovi, aby je odesílal do vašeho zařízení.
- **Přehled**: zobrazení telemetrie zařízení, zobrazení grafů a metrik.
- **O**: zobrazení s informacemi o zařízení, které zobrazuje vlastnosti zařízení.

Jakmile vyberete možnost **Generovat výchozí zobrazení**, uvidíte, že byly automaticky přidány do části **zobrazení** v šabloně zařízení.

## <a name="add-dashboards"></a>Přidat řídicí panely

Přidejte řídicí panely do šablony zařízení a umožněte tak operátorům vizualizovat zařízení pomocí grafů a metrik. Pro šablonu zařízení můžete mít více řídicích panelů.

Přidání řídicího panelu do šablony zařízení:

1. Otevřete šablonu zařízení a vyberte **zobrazení**.
1. Vyberte **vizualizaci zařízení**.
1. Do **název řídicího panelu** zadejte název řídicího panelu.
1. Přidejte dlaždice na řídicí panel ze seznamu statických, vlastností, vlastností cloudu, telemetrie a příkazových dlaždic. Přetáhněte dlaždice, které chcete přidat na řídicí panel.
1. Chcete-li vykreslit více hodnot telemetrie na dlaždici s jedním grafem, vyberte hodnoty telemetrie a pak vyberte **kombinovat**.
1. Nakonfigurujte každou dlaždici, kterou přidáte, abyste mohli přizpůsobit způsob zobrazení dat. Přístup k této možnosti získáte tak, že vyberete ikonu ozubeného kolečka nebo na dlaždici grafu vyberete **změnit konfiguraci** .
1. Uspořádejte a změňte velikost dlaždic na řídicím panelu.
1. Uložte změny.

### <a name="configure-preview-device-to-view-dashboard"></a>Konfigurace zařízení Preview pro zobrazení řídicího panelu

Řídicí panel zobrazíte a otestujete tak, že vyberete **Konfigurovat zařízení ve verzi Preview**. Tato funkce umožňuje zobrazit řídicí panel tak, jak ho váš operátor uvidí po publikování. Pomocí této funkce můžete ověřit, že se v zobrazeních zobrazují správná data. Máte na výběr z následujících možností:

- Žádné zařízení ve verzi Preview
- Reálné testovací zařízení, které jste nakonfigurovali pro šablonu zařízení.
- Existující zařízení v aplikaci pomocí ID zařízení.

## <a name="add-forms"></a>Přidat formuláře

Přidejte formuláře do šablony zařízení a umožněte tak operátorům spravovat zařízení zobrazením a nastavením vlastností. Operátoři můžou upravovat jenom vlastnosti cloudu a zapisovatelné vlastnosti zařízení. Pro šablonu zařízení můžete mít několik forem.

Přidání formuláře do šablony zařízení:

1. Otevřete šablonu zařízení a vyberte **zobrazení**.
1. Vyberte možnost **Upravit zařízení a data v cloudu**.
1. Do **názvu** formuláře zadejte název formuláře.
1. Vyberte počet sloupců, které se mají použít k rozložení formuláře.
1. Přidejte vlastnosti do existující části formuláře nebo vyberte vlastnosti a zvolte možnost **přidat oddíl**. K seskupení vlastností formuláře použijte oddíly. Do oddílu můžete přidat název.
1. Nakonfigurujte jednotlivé vlastnosti formuláře, abyste mohli přizpůsobit jeho chování.
1. Uspořádejte vlastnosti ve formuláři.
1. Uložte změny.

## <a name="publish-a-device-template"></a>Publikování šablony zařízení

Předtím, než budete moci připojit zařízení, které implementuje model zařízení, je nutné publikovat šablonu zařízení.

Po publikování šablony zařízení můžete provádět pouze omezené změny modelu zařízení. Chcete-li upravit komponentu, je nutné [vytvořit a publikovat novou verzi](./howto-version-device-template.md).

Šablonu zařízení publikujete tak, že přejdete na šablonu zařízení a vyberete **publikovat**.

Po publikování šablony zařízení může operátor přejít na stránku **zařízení** a přidat buď skutečná, nebo simulovaná zařízení, která používají šablonu zařízení. Můžete i nadále upravovat a ukládat šablonu zařízení, když provádíte změny. Pokud chcete tyto změny vložit do operátoru, který se zobrazí na stránce **zařízení** , je nutné vybrat **publikovat** pokaždé.

## <a name="next-steps"></a>Další kroky

Pokud jste vývojářem zařízení, je navržený další krok Přečtěte si o [verzích šablon zařízení](./howto-version-device-template.md).
