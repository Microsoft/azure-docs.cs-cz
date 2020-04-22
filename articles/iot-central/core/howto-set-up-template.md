---
title: Definování nového typu zařízení IoT ve Službě Azure IoT Central | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jako tvůrce, jak vytvořit novou šablonu zařízení Azure IoT zařízení ve vaší aplikaci Azure IoT Central. Definujete telemetrii, stav, vlastnosti a příkazy pro váš typ.
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 2a99f261e1a834705d081e8197e4ae627cf1cb9f
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756643"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Definování nového typu zařízení IoT v aplikaci Azure IoT Central

*Tento článek se vztahuje na tvůrce řešení a vývojáře zařízení.*

Šablona zařízení je podrobný plán, který definuje charakteristiky a chování typu zařízení, které se připojuje k aplikaci Azure IoT Central.

Tvůrce může například vytvořit šablonu zařízení pro připojený ventilátor, která má následující charakteristiky:

- Odešle teplotní telemetrii
- Odešle vlastnost umístění
- Odešle události chyb motoru ventilátoru
- Odešle provozní stav ventilátoru
- Poskytuje zapisovatelnou vlastnost rychlosti ventilátoru
- Obsahuje příkaz k restartování zařízení.
- Poskytuje celkový přehled o zařízení prostřednictvím řídicího panelu

Z této šablony zařízení může operátor vytvářet a připojovat skutečná fanouškovská zařízení. Všechny tyto ventilátory mají měření, vlastnosti a příkazy, které operátoři používají ke sledování a správě. Operátoři používají řídicí panely zařízení a formuláře k interakci s fanouškovskými zařízeními.

> [!NOTE]
> Šablony zařízení mohou vytvářet, upravovat a odstraňovat pouze tvůrci a správci. Každý uživatel může vytvářet zařízení na stránce **Zařízení** z existujících šablon zařízení.

[IoT Plug and Play (preview)](../../iot-pnp/overview-iot-plug-and-play.md) umožňuje IoT Central integrovat zařízení, aniž byste museli psát nějaký vložený kód zařízení. Jádrem IoT Plug and Play (preview) je schéma modelu schopností zařízení, které popisuje možnosti zařízení. V aplikaci IoT Central používají šablony zařízení tyto modely funkcí zařízení IoT Plug and Play (preview).

Jako tvůrce máte několik možností pro vytváření šablon zařízení:

- Navrhněte šablonu zařízení v ioT central a pak implementujte model schopností zařízení v kódu zařízení.
- Importujte model schopností zařízení z [katalogu zařízení Azure Certified for IoT](https://aka.ms/iotdevcat). Pak přidejte všechny vlastnosti cloudu, vlastní nastavení a řídicí panely, které vaše aplikace IoT Central potřebuje.
- Vytvořte model schopností zařízení pomocí kódu sady Visual Studio. Implementujte kód zařízení z modelu. Ručně importujte model schopností zařízení do aplikace IoT Central a pak přidejte všechny vlastnosti cloudu, vlastní nastavení a řídicí panely, které vaše aplikace IoT Central potřebuje.
- Vytvořte model schopností zařízení pomocí kódu sady Visual Studio. Implementujte kód zařízení z modelu a připojte skutečné zařízení k aplikaci IoT Central pomocí připojení na začátku zařízení. IoT Central vyhledá a importuje model schopností zařízení z veřejného úložiště pro vás. Potom můžete do šablony zařízení přidat všechny vlastnosti cloudu, vlastní nastavení a řídicí panely, které vaše aplikace IoT Central potřebuje.

## <a name="create-a-device-template-from-the-device-catalog"></a>Vytvoření šablony zařízení z katalogu zařízení

Jako tvůrce můžete rychle začít vytvářet své řešení pomocí zařízení s certifikací IoT Plug and Play (preview). Podívejte se na seznam v [katalogu zařízení Azure IoT](https://catalog.azureiotsolutions.com/alldevices). IoT Central se integruje s katalogem zařízení, takže můžete importovat model schopností zařízení z některého z těchto zařízení s certifikací IoT Plug and Play (preview). Vytvoření šablony zařízení z jednoho z těchto zařízení v IoT Central:

1. Přejděte na stránku **Šablony zařízení** v aplikaci IoT Central.
1. Vyberte **+ Nový**a pak vyberte libovolné zařízení s certifikací IoT Plug and Play (preview) z katalogu. IoT Central vytvoří šablonu zařízení založenou na tomto modelu schopností zařízení.
1. Přidejte do šablony zařízení všechny vlastnosti cloudu, vlastní nastavení nebo zobrazení.
1. Vyberte **Publikovat,** chcete-li šablonu zpřístupnit operátorům k zobrazení a připojení zařízení.

## <a name="create-a-device-template-from-scratch"></a>Vytvoření šablony zařízení od začátku

Šablona zařízení obsahuje:

- _Model schopností zařízení,_ který určuje telemetrii, vlastnosti a příkazy, které zařízení implementuje. Tyto funkce jsou uspořádány do jednoho nebo více rozhraní.
- _Vlastnosti cloudu,_ které definují informace, které vaše aplikace IoT Central ukládá o vašich zařízeních. Například cloudová vlastnost může zaznamenat datum posledního servisu zařízení. Tyto informace se zařízením nikdy nesdílejí.
- _Vlastní nastavení_ umožňují tvůrce přepsat některé definice v modelu schopností zařízení. Tvůrce může například přepsat název vlastnosti zařízení. Názvy vlastností se zobrazují v řídicích panelech a formulářích ioT Central.
- _Řídicí panely a formuláře_ umožňují tvůrci vytvořit nové tlačítko, které umožňuje operátorům monitorovat a spravovat zařízení připojená k vaší aplikaci.

Vytvoření šablony zařízení v ioT central:

1. Přejděte na stránku **Šablony zařízení** v aplikaci IoT Central.
1. Vyberte **+ Nový** > **vlastní**.
1. Zadejte název šablony, například **Senzor prostředí**.
1. Stiskněte **Enter**. IoT Central vytvoří prázdnou šablonu zařízení.

## <a name="manage-a-device-template"></a>Správa šablony zařízení

Šablonu můžete přejmenovat nebo odstranit z domovské stránky šablony.

Po přidání modelu schopností zařízení do šablony jej můžete publikovat. Dokud šablonu nepublikujete, nemůžete připojit zařízení založené na této šabloně, aby ji operátoři viděli na stránce **Zařízení.**

## <a name="create-a-capability-model"></a>Vytvoření modelu schopností

Chcete-li vytvořit model schopností zařízení, můžete:

- Pomocí IoT Central vytvořte vlastní model od začátku.
- Importujte model ze souboru JSON. Tvůrce zařízení pravděpodobně použil kód Sady Visual Studio k vytvoření modelu schopností zařízení pro vaši aplikaci.
- Vyberte jedno ze zařízení z katalogu zařízení. Tato možnost importuje model schopností zařízení, který výrobce publikoval pro toto zařízení. Model schopností zařízení importovaný takto se automaticky publikuje.

## <a name="manage-a-capability-model"></a>Správa modelu schopností

Po vytvoření modelu schopností zařízení můžete:

- Přidejte rozhraní do modelu. Model musí mít alespoň jedno rozhraní.
- Upravte metadata modelu, například jeho ID, obor názvů a název.
- Odstraňte model.

## <a name="create-an-interface"></a>Vytvoření rozhraní

Funkce zařízení musí mít alespoň jedno rozhraní. Rozhraní je opakovaně použitelná kolekce funkcí.

Vytvoření rozhraní:

1. Přejděte do modelu schopností zařízení a zvolte **+ Přidat rozhraní**.

1. Na stránce **Vybrat rozhraní** můžete:

    - Vytvořte vlastní rozhraní od začátku.
    - Importujte existující rozhraní ze souboru. Tvůrce zařízení mohl použít visual studio kód k vytvoření rozhraní pro vaše zařízení.
    - Zvolte jedno ze standardních rozhraní, například informační rozhraní **zařízení.** Standardní rozhraní určují možnosti společné pro mnoho zařízení. Tato standardní rozhraní jsou publikována Azure IoT a nelze je upravovat verzí ani upravovat.

1. Po vytvoření rozhraní zvolte **Upravit identitu** a změňte zobrazovaný název rozhraní.

1. Pokud se rozhodnete vytvořit vlastní rozhraní od začátku, můžete přidat možnosti zařízení. Možnosti zařízení jsou telemetrie, vlastnosti a příkazy.

### <a name="telemetry"></a>Telemetrie

Telemetrie je proud hodnot odeslaných ze zařízení, obvykle ze senzoru. Senzor může například hlásit okolní teplotu.

V následující tabulce jsou uvedena nastavení konfigurace pro funkci telemetrie:

| Pole | Popis |
| ----- | ----------- |
| Zobrazovaný název | Zobrazovaný název pro hodnotu telemetrie použitou na řídicích panelech a formulářích. |
| Název | Název pole ve zprávě telemetrie. IoT Central generuje hodnotu pro toto pole ze zobrazovaného názvu, ale v případě potřeby můžete zvolit vlastní hodnotu. |
| Typ funkce | Telemetrie. |
| Sémantický typ | Sémantický typ telemetrie, například teplota, stav nebo událost. Volba sémantického typu určuje, která z následujících polí jsou k dispozici. |
| Schéma | Datový typ telemetrie, například double, řetězec nebo vektor. Dostupné volby jsou určeny sémantickým typem. Schéma není k dispozici pro sémantické typy událostí a stavu. |
| Severity | K dispozici pouze pro sémantický typ události. Severitáže jsou **Chyba**, **Informace**nebo **Varování**. |
| Hodnoty stavu | K dispozici pouze pro státní sémantický typ. Definujte možné hodnoty stavu, z nichž každá má zobrazovaný název, název, typ výčtu a hodnotu. |
| Jednotka | Jednotka pro hodnotu telemetrie, **%** například **mph**, nebo ** &deg;C**. |
| Zobrazovací jednotka | Zobrazovací jednotka pro použití na řídicích panelech a formulářích. |
| Poznámka | Jakékoli komentáře o funkci telemetrie. |
| Popis | Popis funkce telemetrie. |

### <a name="properties"></a>Vlastnosti

Vlastnosti představují hodnoty v čase. Zařízení může například použít vlastnost k vykazovací cílové teplotě, na kterou se pokouší dosáhnout. Můžete nastavit zapisovatelné vlastnosti z IoT Central.

V následující tabulce jsou uvedena nastavení konfigurace pro schopnost vlastností:

| Pole | Popis |
| ----- | ----------- |
| Zobrazovaný název | Zobrazovaný název hodnoty vlastnosti použité na řídicích panelech a formulářích. |
| Název | Název vlastnosti IoT Central generuje hodnotu pro toto pole ze zobrazovaného názvu, ale v případě potřeby můžete zvolit vlastní hodnotu. |
| Typ funkce | Vlastnost. |
| Sémantický typ | Sémantický typ vlastnosti, například teplota, stav nebo událost. Volba sémantického typu určuje, která z následujících polí jsou k dispozici. |
| Schéma | Datový typ vlastnosti, například double, string nebo vector. Dostupné volby jsou určeny sémantickým typem. Schéma není k dispozici pro sémantické typy událostí a stavu. |
| Writeable | Pokud vlastnost není zapisovatelná, zařízení může hlásit hodnoty vlastností ioT Central. Pokud je vlastnost zapisovatelná, zařízení může hlásit hodnoty vlastností ioT Central a IoT Central může odesílat aktualizace vlastností do zařízení.
| Severity | K dispozici pouze pro sémantický typ události. Severitáže jsou **Chyba**, **Informace**nebo **Varování**. |
| Hodnoty stavu | K dispozici pouze pro státní sémantický typ. Definujte možné hodnoty stavu, z nichž každá má zobrazovaný název, název, typ výčtu a hodnotu. |
| Jednotka | Jednotka pro hodnotu vlastnosti, **%** například **mph**, nebo ** &deg;C**. |
| Zobrazovací jednotka | Zobrazovací jednotka pro použití na řídicích panelech a formulářích. |
| Poznámka | Jakékoli komentáře k vlastnosti schopnosti. |
| Popis | Popis vlastnosti schopnosti. |

### <a name="commands"></a>Příkazy

Příkazy zařízení můžete volat ze zařízení IoT Central. Příkazy volitelně předávají parametry zařízení a přijímají odpověď ze zařízení. Můžete například zavolat příkaz k restartování zařízení za 10 sekund.

V následující tabulce jsou uvedena nastavení konfigurace funkce příkazu:

| Pole | Popis |
| ----- | ----------- |
| Zobrazovaný název | Zobrazovaný název příkazu použitého na řídicích panelech a formulářích. |
| Název | Název příkazu. IoT Central generuje hodnotu pro toto pole ze zobrazovaného názvu, ale v případě potřeby můžete zvolit vlastní hodnotu. |
| Typ funkce | Příkaz. |
| Příkaz | `SynchronousExecutionType`. |
| Poznámka | Jakékoli komentáře k funkci příkazu. |
| Popis | Popis funkce příkazu. |
| Žádost | Pokud je tato možnost povolena, definice parametru požadavku, včetně: název, zobrazovaný název, schéma, jednotka a zobrazovací jednotka. |
| Odpověď | Pokud je tato možnost povolena, definice příkazové odpovědi, včetně: název, zobrazovaný název, schéma, jednotka a zobrazovací jednotka. |

## <a name="manage-an-interface"></a>Správa rozhraní

Pokud jste rozhraní nepublikovali, můžete upravit možnosti definované rozhraním. Po publikování rozhraní, pokud chcete provést nějaké změny, budete muset vytvořit novou verzi šablony zařízení a verze rozhraní. V části **Přizpůsobit** můžete provádět změny, které nevyžadují správu verzí, například zobrazované názvy nebo jednotky.

Rozhraní můžete také exportovat jako soubor JSON, pokud jej chcete znovu použít v jiném modelu schopností.

## <a name="add-cloud-properties"></a>Přidání vlastností cloudu

Pomocí vlastností cloudu můžete ukládat informace o zařízeních ve službě IoT Central. Vlastnosti cloudu se nikdy neposílají do zařízení. Pomocí vlastností cloudu můžete například uložit jméno zákazníka, který zařízení nainstaloval, nebo datum poslední ho servisu zařízení.

V následující tabulce jsou uvedena nastavení konfigurace vlastnosti cloudu:

| Pole | Popis |
| ----- | ----------- |
| Zobrazovaný název | Zobrazovaný název hodnoty vlastnosti cloudu použitý na řídicích panelech a formulářích. |
| Název | Název vlastnosti cloud. IoT Central generuje hodnotu pro toto pole ze zobrazovaného názvu, ale v případě potřeby můžete zvolit vlastní hodnotu. |
| Sémantický typ | Sémantický typ vlastnosti, například teplota, stav nebo událost. Volba sémantického typu určuje, která z následujících polí jsou k dispozici. |
| Schéma | Datový typ cloudové vlastnosti, například double, string nebo vector. Dostupné volby jsou určeny sémantickým typem. |

## <a name="add-customizations"></a>Přidání vlastního nastavení

Vlastní nastavení použijte, když potřebujete upravit importované rozhraní nebo přidat do funkce funkce funkce specifické pro IoT Central. Můžete přizpůsobit pouze pole, která neporušují kompatibilitu rozhraní. Můžete například provést následující věci:

- Přizpůsobte zobrazovaný název a jednotky schopností.
- Přidejte výchozí barvu, která se použije, když se hodnota zobrazí v grafu.
- Zadejte počáteční, minimální a maximální hodnoty vlastnosti.

Název schopnosti nebo typ schopnosti nelze přizpůsobit. Pokud v části **Vlastní nastavení** nemůžete provést změny, budete muset tuto možnost upravit pomocí šablony a rozhraní zařízení.

### <a name="generate-default-views"></a>Generovat výchozí zobrazení

Generování výchozích zobrazení je rychlý způsob, jak vizualizovat důležité informace o zařízení. Pro šablonu zařízení máte vygenerována až tři výchozí zobrazení:

- **Příkazy** poskytují zobrazení s příkazy zařízení a umožňují operátorovi odeslat je do vašeho zařízení.
- **Přehled** poskytuje zobrazení s telemetrií zařízení, zobrazením grafů a metrik.
- **O** poskytuje zobrazení s informacemi o zařízení, zobrazení vlastností zařízení.

Po výběru **možnosti Generovat výchozí zobrazení**zjistíte, že byla automaticky přidána v části **Zobrazení** v šabloně zařízení.

## <a name="add-dashboards"></a>Přidání řídicích panelů

Přidejte řídicí panely do šablony zařízení, aby operátoři mohli vizualizovat zařízení pomocí grafů a metrik. Pro šablonu zařízení můžete mít více řídicích panelů.

Přidání řídicího panelu do šablony zařízení:

1. Přejděte do šablony zařízení a vyberte **Zobrazení**.
1. Zvolte **Vizualizace zařízení**.
1. Zadejte název řídicího panelu do **pole Název řídicího panelu**.
1. Přidejte dlaždice na řídicí panel ze seznamu statických, vlastností, vlastností cloudu, telemetrie a dlaždic příkazů. Přetáhněte dlaždice, které chcete přidat na řídicí panel.
1. Chcete-li vykreslit více hodnot telemetrie na jedné dlaždici grafu, vyberte hodnoty telemetrie a pak vyberte **Kombinovat**.
1. Nakonfigurujte každou přidanou dlaždici a přizpůsobte tak způsob zobrazení dat. Můžete to provést výběrem ikony ozubeného kola nebo výběrem **možnosti Změnit konfiguraci** na dlaždici grafu.
1. Uspořádejte a změňte velikost dlaždic na řídicím panelu.
1. Uložte změny.

### <a name="configure-preview-device-to-view-dashboard"></a>Konfigurace zařízení preview pro zobrazení řídicího panelu

Chcete-li řídicí panel zobrazit a otestovat, vyberte **konfigurovat zařízení náhledu**. To vám umožní zobrazit řídicí panel tak, jak ho vidí váš operátor po jeho publikování. Tuto možnost použijte k ověření, zda zobrazení zobrazují správná data. Na výběr jsou následující možnosti:

- Žádné zařízení náhledu.
- Skutečné testovací zařízení, které jste nakonfigurovali pro šablonu zařízení.
- Existující zařízení ve vaší aplikaci pomocí ID zařízení.

## <a name="add-forms"></a>Přidání formulářů

Přidáním formulářů do šablony zařízení umožníte operátorům spravovat zařízení zobrazením a nastavením vlastností. Operátoři mohou upravovat pouze vlastnosti cloudu a vlastnosti zapisovatelných zařízení. Pro šablonu zařízení můžete mít více formulářů.

Přidání formuláře do šablony zařízení:

1. Přejděte do šablony zařízení a vyberte **Zobrazení**.
1. Zvolte **Úpravy dat zařízení a cloudu**.
1. Zadejte název formuláře do **pole Název formuláře**.
1. Vyberte počet sloupců, které chcete použít k rozložení formuláře.
1. Přidejte vlastnosti do existujícího oddílu ve formuláři nebo vyberte vlastnosti a zvolte **Přidat oddíl**. Oddíly slouží k seskupení vlastností formuláře. Do oddílu můžete přidat název.
1. Nakonfigurujte jednotlivé vlastnosti ve formuláři přizpůsobit jeho chování.
1. Uspořádejte vlastnosti ve formuláři.
1. Uložte změny.

## <a name="publish-a-device-template"></a>Publikování šablony zařízení

Než budete moci připojit zařízení, které implementuje model schopností zařízení, musíte publikovat šablonu zařízení.

Po publikování šablony zařízení můžete provádět pouze omezené změny modelu schopností zařízení. Chcete-li upravit rozhraní, je třeba [vytvořit a publikovat novou verzi](./howto-version-device-template.md).

Pokud chcete šablonu zařízení publikovat, přejděte k šabloně zařízení a vyberte **Publikovat**.

Po publikování šablony zařízení může operátor přejít na stránku **Zařízení** a přidat skutečná nebo simulovaná zařízení, která používají šablonu vašeho zařízení. Šablonu zařízení můžete při provádění změn dále upravovat a ukládat. Pokud chcete tyto změny posunout do operátoru, abyste je viděli na stránce **Zařízení,** musíte pokaždé vybrat **Publikovat.**

## <a name="next-steps"></a>Další kroky

Pokud jste vývojář zařízení, je dalším krokem přečíst si informace o [správě verzí šablon zařízení](./howto-version-device-template.md).
