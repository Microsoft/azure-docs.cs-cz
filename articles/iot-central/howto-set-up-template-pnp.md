---
title: Nastavení šablony zařízení v aplikaci Azure IoT Central | Microsoft Docs
description: Naučte se, jak nastavit šablonu zařízení pomocí měření, nastavení, vlastností, pravidel a řídicího panelu.
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 982ebf28a0f99a6eace2448676c934855cd99d1f
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879731"
---
# <a name="set-up-and-manage-a-device-template-preview-features"></a>Nastavení a Správa šablony zařízení (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Šablona zařízení je podrobný plán, který definuje charakteristiky a chování typu zařízení, které se připojuje k aplikaci Azure IoT Central.

Tvůrce může například vytvořit šablonu zařízení pro připojený ventilátor, která má následující vlastnosti:

- Odesílá telemetrii teploty.
- Odeslat vlastnost Location
- Odesílá chybové události motocyklu pro ventilátor.
- Odešle provozní stav ventilátoru.
- Zapisovatelná vlastnost rychlosti ventilátoru
- Příkaz pro restartování zařízení
- Řídicí panel, který vám poskytne celkový přehled o zařízení

Z této šablony zařízení může operátor vytvářet a připojovat reálné ventilátory. Všechny tyto ventilátory mají měření, vlastnosti a příkazy, které operátory používají ke sledování a správě. Operátory používají řídicí panely a formuláře zařízení k interakci se zařízeními ventilátoru.

> [!NOTE]
> Šablony zařízení můžou vytvářet, upravovat a odstraňovat jenom tvůrci a správci. Každý uživatel může vytvořit zařízení na stránce **zařízení** z existujících šablon zařízení.

[IoT technologie Plug and Play](https://aka.ms/iot-pnp-docs) umožňuje IoT Central integrovat zařízení bez psaní jakéhokoli vloženého kódu zařízení. V jádru IoT technologie Plug and Play je schéma modelu schopností zařízení, které popisuje možnosti zařízení. V aplikaci IoT Central Preview používají šablony zařízení tyto modely schopností zařízení technologie Plug and Play IoT.

Jako tvůrce máte k dispozici několik možností pro vytváření šablon zařízení:

- Navrhněte šablonu zařízení v IoT Central a potom implementujte svůj model schopností zařízení do kódu zařízení.
- Naimportujte model schopností zařízení z [katalogu zařízení Azure Certified for IoT](https://aka.ms/iotdevcat) a pak přidejte jakékoli vlastnosti cloudu, vlastní nastavení a řídicí panely, které vaše IoT Central aplikace potřebuje.
- Vytvořte model schopností zařízení pomocí Visual Studio Code. Implementujte kód zařízení z modelu. Model schopností zařízení naimportujte ručně do aplikace IoT Central a pak přidejte jakékoli vlastnosti cloudu, přizpůsobení a řídicí panely, které aplikace IoT Central potřebuje.
- Vytvořte model schopností zařízení pomocí Visual Studio Code. Naimplementujte kód zařízení z modelu a připojte skutečné zařízení k vaší IoT Central aplikaci pomocí připojení zařízení s prvním zařízením. IoT Central najde a naimportuje model schopností zařízení z veřejného úložiště za vás. Pak můžete přidat libovolné vlastnosti cloudu, vlastní nastavení a řídicí panely, které vaše aplikace IoT Central potřebuje k šabloně zařízení.

## <a name="create-a-device-template-from-the-device-catalog"></a>Vytvoření šablony zařízení z katalogu zařízení

Jako tvůrce můžete rychle začít vytvářet vaše řešení pomocí zařízení s certifikací IoT technologie Plug and Play certifikovaného zařízení, které je uvedené v [katalogu zařízení Azure IoT](https://catalog.azureiotsolutions.com/alldevices). IoT Central se integruje s katalogem zařízení a umožňuje importovat model schopností zařízení z některého z těchto zařízení s certifikací IoT technologie Plug and Play. Chcete-li vytvořit šablonu zařízení z jednoho z těchto zařízení v IoT Central:

1. V aplikaci IoT Central otevřete stránku **šablony zařízení** .
1. Vyberte **+ Nový**a potom z katalogu uvedeného níže vyberte libovolné zařízení s certifikací technologie Plug and Play IoT. IoT Central vytvoří šablonu zařízení založenou na tomto modelu schopností zařízení.
1. Přidejte do šablony zařízení všechny vlastnosti cloudu, vlastní nastavení nebo zobrazení.
1. Vyberte **publikovat** a publikujte tuto šablonu zařízení, abyste ji mohli k dispozici pro operátory pro zobrazení a připojení zařízení.

## <a name="create-a-device-template-from-scratch"></a>Vytvoření zcela nové šablony zařízení

Šablona zařízení obsahuje:

- _Model schopností zařízení_ , který určuje telemetrii, vlastnosti a příkazy, které zařízení implementuje. Tyto možnosti jsou uspořádány do jednoho nebo více rozhraní.
- _Vlastnosti cloudu_ , které definují informace, které vaše aplikace IoT Central ukládá na vaše zařízení. Například vlastnost cloudu může zaznamenat data, která byla naposledy obsluhovaná zařízením. Tyto informace se zařízení nikdy nesdílí.
- _Přizpůsobení_ umožní tvůrci přepsat některé definice v modelu schopností zařízení. Tvůrce může například přepsat název vlastnosti zařízení. Názvy vlastností se zobrazí v IoT Central řídicích panelech a formulářích.
- _Řídicí panely a formuláře_ umožňují tvůrci vytvořit uživatelské rozhraní, které umožňuje operátorům monitorovat a spravovat zařízení připojená k vaší aplikaci.

Vytvoření šablony zařízení v IoT Central:

1. V aplikaci IoT Central otevřete stránku **šablony zařízení** .
1. Vyberte **+ Nový**a pak vyberte **vlastní**.
1. Zadejte název šablony, například **snímač pro životní prostředí**.
1. Stisknutím klávesy **zadejte**. IoT Central vytvoří prázdnou šablonu zařízení.

## <a name="manage-a-device-template"></a>Správa šablony zařízení

Šablonu můžete přejmenovat nebo odstranit z domovské stránky šablony.

Až do šablony přidáte model schopností zařízení, můžete ho publikovat. Nemůžete připojit zařízení na základě této šablony pro vaše operátory, aby se zobrazila na stránce **zařízení** , dokud šablonu nepublikujete.

## <a name="create-a-capability-model"></a>Vytvoření modelu schopností

Pokud chcete vytvořit model schopností zařízení, můžete:

- Pomocí IoT Central vytvořit vlastní model od začátku.
- Importuje model ze souboru JSON. Tvůrce zařízení mohl použít Visual Studio Code k vytvoření modelu schopností zařízení pro vaši aplikaci.
- Vyberte jedno ze zařízení z katalogu zařízení. Tato možnost importuje model schopností zařízení, který výrobce pro toto zařízení publikoval. Automaticky se publikuje model schopností zařízení importovaný jako takový.

## <a name="manage-a-capability-model"></a>Správa modelu schopností

Po vytvoření modelu schopností zařízení můžete:

- Přidejte do modelu rozhraní. Model musí mít alespoň jedno rozhraní.
- Upravte metadata modelu, jako je jeho ID, obor názvů a název.
- Odstraňte model.

## <a name="create-an-interface"></a>Vytvoření rozhraní

Schopnost zařízení musí mít aspoň jedno rozhraní. Rozhraní je opakovaně použitelná kolekce funkcí.

Vytvoření rozhraní:

1. Přejdete do modelu schopností zařízení a zvolíte **+ Přidat rozhraní**.

1. Na stránce **Vybrat rozhraní** můžete:

    - Vytvořte vlastní rozhraní od začátku.
    - Importuje existující rozhraní ze souboru. Tvůrce zařízení mohl použít Visual Studio Code k vytvoření rozhraní pro vaše zařízení.
    - Vyberte jedno ze standardních rozhraní, jako je například rozhraní **informací o zařízení** . Standardní rozhraní určují možnosti společné pro mnoho zařízení. Tato standardní rozhraní jsou publikována pomocí Microsoft Azure IoT a nelze je upravovat ani upravovat.

1. Po vytvoření rozhraní změňte zobrazovaný název rozhraní výběrem možnosti **Upravit identitu** .

1. Pokud se rozhodnete vytvořit vlastní rozhraní od začátku, můžete přidat možnosti svého zařízení. Možnosti zařízení jsou telemetrie, vlastnosti a příkazy.

### <a name="telemetry"></a>Telemetrie

Telemetrie je proud hodnot odeslaných ze zařízení, typicky ze senzoru. Senzor může například ohlásit okolní teplotu.

Následující tabulka ukazuje nastavení konfigurace pro schopnost telemetrie:

| Pole | Popis |
| ----- | ----------- |
| Zobrazovaný název | Zobrazovaný název hodnoty telemetrie používané na řídicích panelech a formulářích |
| Name | Název pole ve zprávě telemetrie IoT Central vygeneruje hodnotu pro toto pole ze zobrazovaného názvu, ale v případě potřeby můžete zvolit vlastní hodnotu. |
| Typ schopnosti | Telemetrie. |
| Sémantický typ | Sémantický typ telemetrie, jako je například teplota, stav nebo událost. Volba sémantického typu Určuje, která z následujících polí je k dispozici. |
| Schéma | Datový typ telemetrie, například Double, String nebo Vector. Dostupné možnosti určují sémantický typ. Schéma není k dispozici pro sémantické typy události a stavu. |
| severity | K dispozici pouze pro sémantický typ události. **Chyba**, **informace**nebo **Upozornění**. |
| Hodnoty stavu | K dispozici pouze pro sémantický typ State. Definujte hodnoty možných stavů, z nichž každá má zobrazované jméno, název, Výčtový typ a hodnotu. |
| Jednotka | Jednotka pro hodnotu telemetrie, například **mph**, **%** nebo  **&deg;C**. |
| Jednotka zobrazení | Zobrazovací jednotka pro použití na řídicích panelech a formulářích. |
| Komentář | Jakékoli komentáře k schopnosti telemetrie. |
| Popis | Popis schopnosti telemetrie. |

### <a name="properties"></a>Vlastnosti

Vlastnosti znázorňují hodnoty bodu v čase. Zařízení může například pomocí vlastnosti nahlásit cílovou teplotu, se kterou se snaží dosáhnout. Můžete nastavit zapisovatelné vlastnosti z IoT Central.

Následující tabulka ukazuje nastavení konfigurace pro schopnost vlastnosti:

| Pole | Popis |
| ----- | ----------- |
| Zobrazovaný název | Zobrazovaný název hodnoty vlastnosti používané na řídicích panelech a formulářích. |
| Name | Název vlastnosti. IoT Central vygeneruje hodnotu pro toto pole ze zobrazovaného názvu, ale v případě potřeby můžete zvolit vlastní hodnotu. |
| Typ schopnosti | Majetek. |
| Sémantický typ | Sémantický typ vlastnosti, jako je například teplota, stav nebo událost. Volba sémantického typu Určuje, která z následujících polí je k dispozici. |
| Schéma | Datový typ vlastnosti, například Double, String nebo Vector. Dostupné možnosti určují sémantický typ. Schéma není k dispozici pro sémantické typy události a stavu. |
| Zapisovatelný | Pokud vlastnost není zapisovatelná, může zařízení nahlásit hodnoty vlastností IoT Central. Pokud je vlastnost zapisovatelná, může zařízení nahlásit hodnoty vlastností IoT Central a IoT Central může odesílat aktualizace vlastností do zařízení.
| severity | K dispozici pouze pro sémantický typ události. **Chyba**, **informace**nebo **Upozornění**. |
| Hodnoty stavu | K dispozici pouze pro sémantický typ State. Definujte hodnoty možných stavů, z nichž každá má zobrazované jméno, název, Výčtový typ a hodnotu. |
| Jednotka | Jednotka pro hodnotu vlastnosti, například **mph**, **%** nebo  **&deg;C**. |
| Jednotka zobrazení | Zobrazovací jednotka pro použití na řídicích panelech a formulářích. |
| Komentář | Jakékoli komentáře k funkci vlastnosti. |
| Popis | Popis schopnosti vlastnosti. |

### <a name="commands"></a>Příkazy

Příkazy zařízení můžete volat z IoT Central. Příkazy volitelně předají do zařízení parametry a obdrží odpověď ze zařízení. Například můžete zavolat příkaz k restartování zařízení za 10 sekund.

Následující tabulka ukazuje nastavení konfigurace pro funkci příkazu:

| Pole | Popis |
| ----- | ----------- |
| Zobrazovaný název | Zobrazovaný název příkazu, který se používá na řídicích panelech a formulářích. |
| Name | Název příkazu IoT Central vygeneruje hodnotu pro toto pole ze zobrazovaného názvu, ale v případě potřeby můžete zvolit vlastní hodnotu. |
| Typ schopnosti | Příkaz |
| Příkaz | SynchronousExecutionType. |
| Komentář | Jakékoli komentáře k funkci příkazu. |
| Popis | Popis funkce příkazu |
| Žádost | Pokud je povoleno, definice parametru požadavku, včetně názvu, zobrazovaného názvu, schématu, jednotky a zobrazované jednotky. |
| Odpověď | Pokud je povoleno, definice odpovědi příkazu, včetně: název, zobrazovaný název, schéma, jednotka a zobrazovaná jednotka. |

## <a name="manage-an-interface"></a>Správa rozhraní

Za předpokladu, že jste rozhraní nepublikovali, můžete upravit možnosti definované rozhraním. Po publikování rozhraní budete muset vytvořit novou verzi šablony zařízení a verzi rozhraní, abyste provedli změny. Změny, které nevyžadují správu verzí, například zobrazované názvy nebo jednotky, lze vytvořit v oddílu **přizpůsobení** .

Rozhraní můžete také exportovat jako soubor JSON, pokud ho chcete znovu použít v jiném modelu schopností.

## <a name="add-cloud-properties"></a>Přidat vlastnosti cloudu

Pomocí vlastností cloudu můžete ukládat informace o zařízeních v IoT Central. Vlastnosti cloudu se nikdy neodesílají do zařízení. Pomocí vlastností cloudu můžete například uložit jméno zákazníka, který má nainstalované zařízení, nebo datum poslední služby daného zařízení.

Následující tabulka ukazuje nastavení konfigurace pro cloudovou vlastnost:

| Pole | Popis |
| ----- | ----------- |
| Zobrazovaný název | Zobrazovaný název hodnoty vlastnosti cloudu používaný na řídicích panelech a formulářích. |
| Name | Název vlastnosti cloudu IoT Central vygeneruje hodnotu pro toto pole ze zobrazovaného názvu, ale v případě potřeby můžete zvolit vlastní hodnotu. |
| Sémantický typ | Sémantický typ vlastnosti, jako je například teplota, stav nebo událost. Volba sémantického typu Určuje, která z následujících polí je k dispozici. |
| Schéma | Datový typ cloudové vlastnosti, jako je například Double, String nebo Vector. Dostupné možnosti určují sémantický typ. |

## <a name="add-customizations"></a>Přidat přizpůsobení

Vlastní nastavení použijte v případě, že potřebujete upravit importované rozhraní nebo přidat funkce specifické pro IoT Central do funkce. Můžete přizpůsobit pouze pole, která neruší kompatibilitu rozhraní. Můžete například provést následující věci:

- Přizpůsobení zobrazovaného názvu a jednotek schopnosti.
- Přidejte výchozí barvu, která se použije, když se hodnota zobrazí v grafu.
- Zadejte počáteční, minimální a maximální hodnoty vlastnosti.

Nemůžete přizpůsobit název schopnosti ani typ schopnosti. Pokud existují změny, které nemůžete provést v části **přizpůsobení** , budete muset verzi šablony a rozhraní upravit tak, aby se tato možnost změnila.

### <a name="generate-default-views"></a>Generovat výchozí zobrazení

Generování výchozích zobrazení představuje rychlý způsob, jak začít s vizualizací důležitých informací o zařízení. Budete mít k dispozici až tři výchozí zobrazení vygenerovaná pro šablonu zařízení:

1. **Příkazy** poskytnou zobrazení s příkazy zařízení a umožní operátorovi, aby je odesílal do vašeho zařízení.
1. **Přehled** bude poskytovat zobrazení telemetrie zařízení, zobrazení grafů a metrik.
1. **O produktu** se zobrazí informace o zařízení, které zobrazuje vlastnosti zařízení.

Jakmile vyberete možnost **Generovat výchozí zobrazení** , uvidíte, že se automaticky přidaly do části **zobrazení** v šabloně zařízení.

## <a name="add-dashboards"></a>Přidat řídicí panely

Přidejte řídicí panely do šablony zařízení a umožněte tak operátorům vizualizovat zařízení pomocí grafů a metrik. Pro šablonu zařízení můžete mít více řídicích panelů.

Přidání řídicího panelu do šablony zařízení:

1. Otevřete šablonu zařízení a vyberte **zobrazení**.
1. Pak zvolte **vizualizovat zařízení**.
1. Do **název řídicího panelu**zadejte název řídicího panelu.
1. Přidejte dlaždice na řídicí panel ze seznamu statických, vlastností, vlastností cloudu, telemetrie a příkazových dlaždic. Přetáhněte dlaždice, které byste chtěli přidat do řídicího panelu.
1. Chcete-li vykreslit více hodnot telemetrie na dlaždici s jedním grafem, vyberte hodnoty telemetrie a pak vyberte **kombinovat**.
1. Nakonfigurujte každou dlaždici, kterou přidáte, abyste mohli přizpůsobit způsob zobrazení dat, a to tak, že vyberete ikonu ozubeného kolečka nebo na dlaždici grafu vyberete tlačítko **změnit konfiguraci** .
1. Uspořádejte a změňte velikost dlaždic na řídicím panelu.
1. Uložte změny.

### <a name="configure-preview-device-to-view-dashboard"></a>Konfigurace zařízení Preview pro zobrazení řídicího panelu

Pokud chcete řídicí panel zobrazit a otestovat, můžete vybrat **Konfigurovat zařízení ve verzi Preview**, které vám po publikování uvidí řídicí panel jako váš operátor. Tato možnost umožňuje ověřit, že se v zobrazeních zobrazují správná data. Pomocí ID zařízení si můžete vybrat ze zařízení bez verze Preview, reálného testovacího zařízení, které jste nakonfigurovali pro šablonu zařízení, nebo existujícího zařízení v aplikaci.

## <a name="add-forms"></a>Přidat formuláře

Přidejte formuláře do šablony zařízení a umožněte tak operátorům spravovat zařízení zobrazením a nastavením vlastností. Operátoři můžou upravovat jenom vlastnosti cloudu a zapisovatelné vlastnosti zařízení. Pro šablonu zařízení můžete mít několik forem.

Přidání formuláře do šablony zařízení:

1. Otevřete šablonu zařízení a vyberte **zobrazení**.
1. Pak zvolte **Upravit zařízení a data**v cloudu.
1. Do **názvu**formuláře zadejte název formuláře.
1. Vyberte počet sloupců, které se mají použít k rozložení formuláře.
1. Přidejte vlastnosti do existující části formuláře nebo vyberte vlastnosti a zvolte možnost **přidat oddíl**. K seskupení vlastností formuláře použijte oddíly. Do oddílu můžete přidat název.
1. Nakonfigurujte jednotlivé vlastnosti formuláře, abyste mohli přizpůsobit jeho chování.
1. Uspořádejte vlastnosti ve formuláři.
1. Uložte změny.

## <a name="publish-a-device-template"></a>Publikování šablony zařízení

Předtím, než budete moci připojit zařízení, které implementuje model schopností zařízení, je nutné publikovat šablonu zařízení.

Po publikování šablony zařízení můžete provádět pouze omezené změny modelu schopností zařízení. Chcete-li změnit rozhraní, je třeba [vytvořit a publikovat novou verzi](./howto-version-device-template-pnp.md).

Šablonu zařízení publikujete tak, že přejdete na šablonu zařízení a vyberete **publikovat**.

Po publikování šablony zařízení může operátor přejít na stránku **zařízení** a přidat buď skutečná, nebo simulovaná zařízení, která používají šablonu zařízení. Můžete i nadále upravovat a ukládat šablonu zařízení, když provádíte změny, ale pokud chcete tyto změny předávat do operátoru, který se zobrazí na stránce **zařízení** , musíte vybrat **publikovat** pokaždé.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak v aplikaci Azure IoT Central nastavit šablonu zařízení, můžete:

> [!div class="nextstepaction"]
> [Vytvoření nové verze](howto-version-device-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
> šablony zařízení[připojení zařízení IoT DevKit MXChip k aplikaci Azure IoT Central](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
