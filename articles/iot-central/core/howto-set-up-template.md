---
title: Definování nového typu zařízení IoT v Azure IoT Central | Microsoft Docs
description: V tomto článku se dozvíte, jak vytvořit novou šablonu zařízení Azure IoT v aplikaci Azure IoT Central. Definujete telemetrii, stav, vlastnosti a příkazy pro svůj typ.
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom:
- contperfq1
- device-developer
ms.openlocfilehash: d6dd1bbf853a13948f55db4ae694b28cb7549c9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91803785"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Definování nového typu zařízení IoT v aplikaci Azure IoT Central

*Tento článek se týká tvůrců řešení a vývojářů zařízení.*

Šablona zařízení je podrobný plán, který definuje charakteristiky a chování typu zařízení, které se připojuje k [aplikaci Azure IoT Central](concepts-app-templates.md).

Tvůrce může například vytvořit šablonu zařízení pro připojený ventilátor, která má následující vlastnosti:

- Odesílá telemetrii teploty.
- Odeslat vlastnost Location
- Odesílá chybové události motocyklu pro ventilátor.
- Odešle provozní stav ventilátoru.
- Poskytuje vlastnost rychlosti ventilátoru umožňující zápis.
- Poskytuje příkaz k restartování zařízení.
- Poskytuje celkový přehled o zařízení prostřednictvím řídicího panelu.

Z této šablony zařízení může operátor vytvářet a připojovat reálné ventilátory. Všechny tyto ventilátory mají měření, vlastnosti a příkazy, které operátory používají pro monitorování a správu. Operátory používají [řídicí panely](#add-dashboards) a formuláře zařízení k interakci se zařízeními ventilátoru. Vývojář zařízení používá šablonu k pochopení, jak zařízení komunikuje s aplikací. Další informace najdete v tématu [telemetrie, vlastnosti a datové části příkazů](concepts-telemetry-properties-commands.md).

> [!NOTE]
> Šablony zařízení můžou vytvářet, upravovat a odstraňovat jenom tvůrci a správci. Každý uživatel může vytvořit zařízení na stránce **zařízení** z existujících šablon zařízení.

V aplikaci IoT Central používá šablona zařízení model schopností zařízení k popisu možností zařízení. Jako tvůrce máte k dispozici několik možností pro vytváření šablon zařízení:

- Navrhněte šablonu zařízení v IoT Central a potom [v kódu zařízení implementujte svůj model schopností zařízení](concepts-telemetry-properties-commands.md).
- Importujte model schopností zařízení z [katalogu zařízení Azure Certified for IoT](https://aka.ms/iotdevcat). Pak přidejte libovolné vlastnosti cloudu, přizpůsobení a řídicí panely, které vaše aplikace IoT Central potřebuje.
- Vytvořte model schopností zařízení pomocí Visual Studio Code. Implementujte kód zařízení z modelu. Model schopností zařízení naimportujte ručně do aplikace IoT Central a pak přidejte všechny vlastnosti cloudu, přizpůsobení a řídicí panely, které aplikace IoT Central potřebuje.
- Vytvořte model schopností zařízení pomocí Visual Studio Code. Naimplementujte kód zařízení z modelu a připojte skutečné zařízení k vaší IoT Central aplikaci pomocí připojení zařízení, které se používá jako první. IoT Central najde a naimportuje model schopností zařízení z veřejného úložiště za vás. Pak můžete přidat libovolné vlastnosti cloudu, vlastní nastavení a řídicí panely, které vaše aplikace IoT Central potřebuje k šabloně zařízení.

Můžete také přidat šablony zařízení do aplikace IoT Central pomocí [REST API](https://docs.microsoft.com/learn/modules/manage-iot-central-apps-with-rest-api/) nebo rozhraní příkazového [řádku](howto-manage-iot-central-from-cli.md).

Některé [šablony aplikací](concepts-app-templates.md) již obsahují šablony zařízení, které jsou užitečné ve scénáři, který podporuje šablona aplikace. Například viz [Analytická architektura v úložišti](../retail/store-analytics-architecture.md).

## <a name="create-a-device-template-from-the-device-catalog"></a>Vytvoření šablony zařízení z katalogu zařízení

Jako tvůrce můžete rychle začít vytvářet vaše řešení pomocí certifikovaného zařízení IoT technologie Plug and Play (Preview). Podívejte se na seznam v [katalogu zařízení Azure IoT](https://catalog.azureiotsolutions.com/alldevices). IoT Central se integruje s katalogem zařízení, abyste mohli naimportovat model schopností zařízení z některého z těchto certifikovaných zařízení IoT technologie Plug and Play (Preview). Chcete-li vytvořit šablonu zařízení z jednoho z těchto zařízení v IoT Central:

1. V aplikaci IoT Central otevřete stránku **šablony zařízení** .
1. Vyberte **+ Nový**a potom z katalogu vyberte kterékoli z certifikovaných zařízení technologie Plug and Play IoT (Preview). IoT Central vytvoří šablonu zařízení založenou na tomto modelu schopností zařízení.
1. Přidejte do šablony zařízení všechny vlastnosti cloudu, vlastní nastavení nebo zobrazení.
1. Vyberte **publikovat** a zpřístupněte tak šablonu pro operátory k zobrazení a připojení zařízení.

## <a name="create-a-device-template-from-scratch"></a>Vytvoření zcela nové šablony zařízení

Šablona zařízení obsahuje:

- _Model schopností zařízení_ , který určuje telemetrii, vlastnosti a příkazy, které zařízení implementuje. Tyto možnosti jsou uspořádány do jednoho nebo více rozhraní.
- _Vlastnosti cloudu_ , které definují informace, které vaše aplikace IoT Central ukládá na vaše zařízení. Například vlastnost cloudu může zaznamenat datum, kdy bylo zařízení naposledy obsluhované. Tyto informace se zařízení nikdy nesdílí.
- _Přizpůsobení_ umožní tvůrci přepsat některé definice v modelu schopností zařízení. Tvůrce může například přepsat název vlastnosti zařízení. Názvy vlastností se zobrazí v IoT Central řídicích panelech a formulářích.
- _Řídicí panely a formuláře_ umožňují tvůrci vytvořit uživatelské rozhraní, které umožňuje operátorům monitorovat a spravovat zařízení připojená k vaší aplikaci.

Vytvoření šablony zařízení v IoT Central:

1. V aplikaci IoT Central otevřete stránku **šablony zařízení** .
1. Vyberte **+ Nový**  >  **vlastní**.
1. Zadejte název šablony, například **snímač pro životní prostředí**.
1.  Stiskněte **Enter**. IoT Central vytvoří prázdnou šablonu zařízení.

## <a name="manage-a-device-template"></a>Správa šablony zařízení

Šablonu můžete přejmenovat nebo odstranit z domovské stránky šablony.

Až do šablony přidáte model schopností zařízení, můžete ho publikovat. Dokud šablonu nepublikujete, nemůžete připojit zařízení založené na této šabloně pro vaše operátory a zobrazit je na stránce **zařízení** .

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
    - Importuje existující rozhraní ze souboru. Je možné, že Visual Studio Code k vytváření rozhraní pro vaše zařízení použít nástroj pro sestavovatele zařízení.
    - Vyberte jedno ze standardních rozhraní, například rozhraní **informací o zařízení** . Standardní rozhraní určují možnosti společné pro mnoho zařízení. Tato standardní rozhraní jsou publikována službou Azure IoT a nelze je upravovat ani upravovat.

1. Po vytvoření rozhraní změňte zobrazovaný název rozhraní výběrem možnosti **Upravit identitu** .

1. Pokud se rozhodnete vytvořit vlastní rozhraní od začátku, můžete přidat možnosti svého zařízení. Možnosti zařízení jsou telemetrie, vlastnosti a příkazy.

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
| Závažnost | K dispozici pouze pro sémantický typ události. Závažnosti jsou **chyby**, **informace**nebo **Upozornění**. |
| Hodnoty stavu | K dispozici pouze pro sémantický typ State. Definujte hodnoty možných stavů, z nichž každá má zobrazované jméno, název, Výčtový typ a hodnotu. |
| Jednotka | Jednotka pro hodnotu telemetrie, například **mph**, **%** nebo ** &deg; C**. |
| Zobrazit jednotku | Zobrazovací jednotka pro použití na řídicích panelech a formulářích. |
| Komentář | Jakékoli komentáře k schopnosti telemetrie. |
| Description | Popis schopnosti telemetrie. |

### <a name="properties"></a>Vlastnosti

Vlastnosti znázorňují hodnoty bodu v čase. Zařízení může například pomocí vlastnosti nahlásit cílovou teplotu, se kterou se snaží dosáhnout. Můžete nastavit zapisovatelné vlastnosti z IoT Central.

Následující tabulka ukazuje nastavení konfigurace pro schopnost vlastnosti:

| Pole | Description |
| ----- | ----------- |
| Zobrazovaný název | Zobrazovaný název hodnoty vlastnosti používané na řídicích panelech a formulářích. |
| Name | Název vlastnosti IoT Central vygeneruje hodnotu pro toto pole ze zobrazovaného názvu, ale v případě potřeby můžete zvolit vlastní hodnotu. Toto pole musí být alfanumerické. |
| Typ funkce | Majetek. |
| Sémantický typ | Sémantický typ vlastnosti, jako je například teplota, stav nebo událost. Volba sémantického typu Určuje, která z následujících polí je k dispozici. |
| Schéma | Datový typ vlastnosti, například Double, String nebo Vector. Dostupné možnosti určují sémantický typ. Schéma není k dispozici pro sémantické typy události a stavu. |
| Writeable | Pokud vlastnost není zapisovatelná, může zařízení nahlásit hodnoty vlastností IoT Central. Pokud je vlastnost zapisovatelná, může zařízení nahlásit hodnoty vlastností IoT Central a IoT Central může odesílat aktualizace vlastností do zařízení.
| Závažnost | K dispozici pouze pro sémantický typ události. Závažnosti jsou **chyby**, **informace**nebo **Upozornění**. |
| Hodnoty stavu | K dispozici pouze pro sémantický typ State. Definujte hodnoty možných stavů, z nichž každá má zobrazované jméno, název, Výčtový typ a hodnotu. |
| Jednotka | Jednotka pro hodnotu vlastnosti, například **mph**, **%** nebo ** &deg; C**. |
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
| Příkaz | `SynchronousExecutionType`. |
| Komentář | Jakékoli komentáře k funkci příkazu. |
| Description | Popis funkce příkazu |
| Žádost | Pokud je povoleno, definice parametru Request, včetně názvu, zobrazovaného názvu, schématu, jednotky a zobrazované jednotky. |
| Odpověď | Pokud je povoleno, definice odpovědi příkazu, včetně názvu, zobrazovaného názvu, schématu, jednotky a zobrazované jednotky, je-li povolena. |

#### <a name="offline-commands"></a>Offline příkazy

Příkazy Queue můžete zvolit, pokud je zařízení aktuálně offline. Pokud je v šabloně zařízení možnost offline pro příkaz, povolte **frontu** .

Tato možnost používá IoT Hub zpráv z cloudu na zařízení k odesílání oznámení do zařízení. Další informace najdete v článku IoT Hub [posílání zpráv z cloudu na zařízení](../../iot-hub/iot-hub-devguide-messages-c2d.md).

Zprávy z cloudu do zařízení:

- Jednosměrná oznámení k zařízení z vašeho řešení.
- Garantuje doručení zpráv alespoň jednou. IoT Hub zachovává zprávy z cloudu na zařízení v rámci front zařízení a zaručuje odolnost proti chybám připojení a selhání zařízení.
- Vyžaduje, aby zařízení implementovalo obslužnou rutinu zpráv pro zpracování zprávy typu cloud-zařízení.

> [!NOTE]
> Tato možnost je k dispozici pouze ve webovém uživatelském rozhraní IoT Central. Toto nastavení není zahrnuté, pokud model nebo rozhraní exportujete ze šablony zařízení.

## <a name="manage-an-interface"></a>Správa rozhraní

Pokud jste rozhraní nepublikovali, můžete upravit možnosti definované rozhraním. Pokud chcete provést změny, budete po publikování rozhraní muset vytvořit novou verzi šablony zařízení a verzi rozhraní. V části **přizpůsobení** můžete provádět změny, které nevyžadují správu verzí, jako jsou názvy zobrazení nebo jednotky.

Rozhraní můžete také exportovat jako soubor JSON, pokud ho chcete znovu použít v jiném modelu schopností.

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

Vlastní nastavení použijte v případě, že potřebujete upravit importované rozhraní nebo přidat funkce specifické pro IoT Central do funkce. Můžete přizpůsobit pouze pole, která neruší kompatibilitu rozhraní. Můžete například:

- Přizpůsobení zobrazovaného názvu a jednotek schopnosti.
- Přidejte výchozí barvu, která se použije, když se hodnota zobrazí v grafu.
- Zadejte počáteční, minimální a maximální hodnoty vlastnosti.

Nemůžete přizpůsobit název schopnosti ani typ schopnosti. Pokud existují změny, které nemůžete provést v části **přizpůsobení** , budete muset verzi šablony a rozhraní upravit tak, aby se tato možnost změnila.

### <a name="generate-default-views"></a>Generovat výchozí zobrazení

Generování výchozích zobrazení je rychlý způsob, jak vizualizovat důležité informace o zařízení. Máte k dispozici až tři výchozí zobrazení vygenerovaná pro šablonu zařízení:

- **Příkazy** poskytují zobrazení pomocí příkazů zařízení a umožňují operátorovi, aby je odesílal do vašeho zařízení.
- **Přehled** nabízí zobrazení telemetrie zařízení, zobrazení grafů a metrik.
- **O produktu** poskytuje zobrazení s informacemi o zařízení a zobrazuje vlastnosti zařízení.

Jakmile vyberete možnost **Generovat výchozí zobrazení**, uvidíte, že byly automaticky přidány do části **zobrazení** v šabloně zařízení.

## <a name="add-dashboards"></a>Přidat řídicí panely

Přidejte řídicí panely do šablony zařízení a umožněte tak operátorům vizualizovat zařízení pomocí grafů a metrik. Pro šablonu zařízení můžete mít více řídicích panelů.

Přidání řídicího panelu do šablony zařízení:

1. Otevřete šablonu zařízení a vyberte **zobrazení**.
1. Vyberte **vizualizaci zařízení**.
1. Do **název řídicího panelu**zadejte název řídicího panelu.
1. Přidejte dlaždice na řídicí panel ze seznamu statických, vlastností, vlastností cloudu, telemetrie a příkazových dlaždic. Přetáhněte dlaždice, které chcete přidat na řídicí panel.
1. Chcete-li vykreslit více hodnot telemetrie na dlaždici s jedním grafem, vyberte hodnoty telemetrie a pak vyberte **kombinovat**.
1. Nakonfigurujte každou dlaždici, kterou přidáte, abyste mohli přizpůsobit způsob zobrazení dat. To můžete provést tak, že vyberete ikonu ozubeného kolečka nebo na dlaždici grafu vyberete **změnit konfiguraci** .
1. Uspořádejte a změňte velikost dlaždic na řídicím panelu.
1. Uložte změny.

### <a name="configure-preview-device-to-view-dashboard"></a>Konfigurace zařízení Preview pro zobrazení řídicího panelu

Řídicí panel zobrazíte a otestujete tak, že vyberete **Konfigurovat zařízení ve verzi Preview**. To vám umožní zobrazit řídicí panel tak, jak ho váš operátor uvidí po publikování. Tuto možnost použijte, pokud chcete ověřit, jestli se v zobrazeních zobrazují správná data. Na výběr jsou následující možnosti:

- Žádné zařízení ve verzi Preview
- Reálné testovací zařízení, které jste nakonfigurovali pro šablonu zařízení.
- Existující zařízení v aplikaci pomocí ID zařízení.

## <a name="add-forms"></a>Přidat formuláře

Přidejte formuláře do šablony zařízení a umožněte tak operátorům spravovat zařízení zobrazením a nastavením vlastností. Operátoři můžou upravovat jenom vlastnosti cloudu a zapisovatelné vlastnosti zařízení. Pro šablonu zařízení můžete mít několik forem.

Přidání formuláře do šablony zařízení:

1. Otevřete šablonu zařízení a vyberte **zobrazení**.
1. Vyberte možnost **Upravit zařízení a data v cloudu**.
1. Do **názvu**formuláře zadejte název formuláře.
1. Vyberte počet sloupců, které se mají použít k rozložení formuláře.
1. Přidejte vlastnosti do existující části formuláře nebo vyberte vlastnosti a zvolte možnost **přidat oddíl**. K seskupení vlastností formuláře použijte oddíly. Do oddílu můžete přidat název.
1. Nakonfigurujte jednotlivé vlastnosti formuláře, abyste mohli přizpůsobit jeho chování.
1. Uspořádejte vlastnosti ve formuláři.
1. Uložte změny.

## <a name="publish-a-device-template"></a>Publikování šablony zařízení

Předtím, než budete moci připojit zařízení, které implementuje model schopností zařízení, je nutné publikovat šablonu zařízení.

Po publikování šablony zařízení můžete provádět pouze omezené změny modelu schopností zařízení. Chcete-li změnit rozhraní, je třeba [vytvořit a publikovat novou verzi](./howto-version-device-template.md).

Šablonu zařízení publikujete tak, že přejdete na šablonu zařízení a vyberete **publikovat**.

Po publikování šablony zařízení může operátor přejít na stránku **zařízení** a přidat buď skutečná, nebo simulovaná zařízení, která používají šablonu zařízení. Můžete i nadále upravovat a ukládat šablonu zařízení, když provádíte změny. Pokud chcete tyto změny vložit do operátoru, který se zobrazí na stránce **zařízení** , je nutné vybrat **publikovat** pokaždé.

## <a name="next-steps"></a>Další kroky

Pokud jste vývojářem zařízení, je navržený další krok Přečtěte si o [verzích šablon zařízení](./howto-version-device-template.md).
