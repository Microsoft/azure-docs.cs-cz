---
title: Definování nového typu zařízení IoT v Azure IoT Central | Microsoft Docs
description: V tomto kurzu se dozvíte jako tvůrce, jak v aplikaci Azure IoT Central vytvořit novou šablonu zařízení Azure IoT. Definujete telemetrii, stav, vlastnosti a příkazy pro svůj typ.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 5642ce6065c4b76bdbd6d772c74fed894de0888f
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893351"
---
# <a name="tutorial-define-a-new-iot-device-type-in-your-azure-iot-central-application-preview-features"></a>Kurz: definování nového typu zařízení IoT v aplikaci Azure IoT Central (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

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

[IoT technologie Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) umožňuje IoT Central integrovat zařízení bez psaní jakéhokoli vloženého kódu zařízení. V jádru IoT technologie Plug and Play je schéma modelu schopností zařízení, které popisuje možnosti zařízení. V aplikaci IoT Central Preview používají šablony zařízení tyto modely schopností zařízení technologie Plug and Play IoT.

Jako tvůrce máte k dispozici několik možností pro vytváření šablon zařízení:

- Navrhněte šablonu zařízení v IoT Central a potom implementujte svůj model schopností zařízení do kódu zařízení.
- Naimportujte model schopností zařízení z [katalogu zařízení Azure Certified for IoT](https://aka.ms/iotdevcat) a pak přidejte jakékoli vlastnosti cloudu, vlastní nastavení a řídicí panely, které vaše IoT Central aplikace potřebuje.
- Vytvořte model schopností zařízení pomocí Visual Studio Code. Implementujte kód zařízení z modelu. Model schopností zařízení naimportujte ručně do aplikace IoT Central a pak přidejte jakékoli vlastnosti cloudu, přizpůsobení a řídicí panely, které aplikace IoT Central potřebuje.
- Vytvořte model schopností zařízení pomocí Visual Studio Code. Naimplementujte kód zařízení z modelu a připojte skutečné zařízení k vaší IoT Central aplikaci pomocí připojení zařízení s prvním zařízením. IoT Central najde a naimportuje model schopností zařízení z veřejného úložiště za vás. Pak můžete přidat libovolné vlastnosti cloudu, vlastní nastavení a řídicí panely, které vaše aplikace IoT Central potřebuje k šabloně zařízení.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete aplikaci Azure IoT Central. V tomto rychlém startu [vytvoříte aplikaci Azure IoT Central](quick-deploy-iot-central.md).

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
1. Stiskněte **Enter**. IoT Central vytvoří prázdnou šablonu zařízení.

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

### <a name="telemetry"></a>Telemetrická data

Telemetrie je proud hodnot odeslaných ze zařízení, typicky ze senzoru. Senzor může například ohlásit okolní teplotu.

Následující tabulka ukazuje nastavení konfigurace pro schopnost telemetrie:

| Pole | Popis |
| ----- | ----------- |
| Zobrazovaný název | Zobrazovaný název hodnoty telemetrie používané na řídicích panelech a formulářích |
| Name (Název) | Název pole ve zprávě telemetrie IoT Central vygeneruje hodnotu pro toto pole ze zobrazovaného názvu, ale v případě potřeby můžete zvolit vlastní hodnotu. |
| Typ schopnosti | Telemetrie. |
| Sémantický typ | Sémantický typ telemetrie, jako je například teplota, stav nebo událost. Volba sémantického typu Určuje, která z následujících polí je k dispozici. |
| Schéma | Datový typ telemetrie, například Double, String nebo Vector. Dostupné možnosti určují sémantický typ. Schéma není k dispozici pro sémantické typy události a stavu. |
| Severity | K dispozici pouze pro sémantický typ události. **Chyba**, **informace**nebo **Upozornění**. |
| Hodnoty stavu | K dispozici pouze pro sémantický typ State. Definujte hodnoty možných stavů, z nichž každá má zobrazované jméno, název, Výčtový typ a hodnotu. |
| Jednotka | Jednotka pro hodnotu telemetrie, například **mph**, **%** nebo **&deg;C**. |
| Zobrazit jednotku | Zobrazovací jednotka pro použití na řídicích panelech a formulářích. |
| Poznámka | Jakékoli komentáře k schopnosti telemetrie. |
| Popis | Popis schopnosti telemetrie. |

### <a name="properties"></a>Vlastnosti

Vlastnosti znázorňují hodnoty bodu v čase. Zařízení může například pomocí vlastnosti nahlásit cílovou teplotu, se kterou se snaží dosáhnout. Můžete nastavit zapisovatelné vlastnosti z IoT Central.

Následující tabulka ukazuje nastavení konfigurace pro schopnost vlastnosti:

| Pole | Popis |
| ----- | ----------- |
| Zobrazovaný název | Zobrazovaný název hodnoty vlastnosti používané na řídicích panelech a formulářích. |
| Name (Název) | Název vlastnosti. IoT Central vygeneruje hodnotu pro toto pole ze zobrazovaného názvu, ale v případě potřeby můžete zvolit vlastní hodnotu. |
| Typ schopnosti | majetek. |
| Sémantický typ | Sémantický typ vlastnosti, jako je například teplota, stav nebo událost. Volba sémantického typu Určuje, která z následujících polí je k dispozici. |
| Schéma | Datový typ vlastnosti, například Double, String nebo Vector. Dostupné možnosti určují sémantický typ. Schéma není k dispozici pro sémantické typy události a stavu. |
| Zapisovatelné | Pokud vlastnost není zapisovatelná, může zařízení nahlásit hodnoty vlastností IoT Central. Pokud je vlastnost zapisovatelná, může zařízení nahlásit hodnoty vlastností IoT Central a IoT Central může odesílat aktualizace vlastností do zařízení.
| Severity | K dispozici pouze pro sémantický typ události. **Chyba**, **informace**nebo **Upozornění**. |
| Hodnoty stavu | K dispozici pouze pro sémantický typ State. Definujte hodnoty možných stavů, z nichž každá má zobrazované jméno, název, Výčtový typ a hodnotu. |
| Jednotka | Jednotka pro hodnotu vlastnosti, například **mph**, **%** nebo **&deg;C**. |
| Zobrazit jednotku | Zobrazovací jednotka pro použití na řídicích panelech a formulářích. |
| Poznámka | Jakékoli komentáře k funkci vlastnosti. |
| Popis | Popis schopnosti vlastnosti. |

### <a name="commands"></a>Příkazy

Příkazy zařízení můžete volat z IoT Central. Příkazy volitelně předají do zařízení parametry a obdrží odpověď ze zařízení. Například můžete zavolat příkaz k restartování zařízení za 10 sekund.

Následující tabulka ukazuje nastavení konfigurace pro funkci příkazu:

| Pole | Popis |
| ----- | ----------- |
| Zobrazovaný název | Zobrazovaný název příkazu, který se používá na řídicích panelech a formulářích. |
| Name (Název) | Název příkazu IoT Central vygeneruje hodnotu pro toto pole ze zobrazovaného názvu, ale v případě potřeby můžete zvolit vlastní hodnotu. |
| Typ schopnosti | Příkaz |
| Příkaz | SynchronousExecutionType. |
| Poznámka | Jakékoli komentáře k funkci příkazu. |
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
| Name (Název) | Název vlastnosti cloudu IoT Central vygeneruje hodnotu pro toto pole ze zobrazovaného názvu, ale v případě potřeby můžete zvolit vlastní hodnotu. |
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

- **Příkazy** poskytnou zobrazení s příkazy zařízení a umožní operátorovi, aby je odesílal do vašeho zařízení.
- **Přehled** bude poskytovat zobrazení telemetrie zařízení, zobrazení grafů a metrik.
- **O produktu** se zobrazí informace o zařízení, které zobrazuje vlastnosti zařízení.

Jakmile vyberete možnost **Generovat výchozí zobrazení** , uvidíte, že se automaticky přidaly do části **zobrazení** v šabloně zařízení.

## <a name="add-dashboards"></a>Přidat řídicí panely

Přidejte řídicí panely do šablony zařízení a umožněte tak operátorům vizualizovat zařízení pomocí grafů a metrik. Pro šablonu zařízení můžete mít více řídicích panelů.

Přidání řídicího panelu do šablony zařízení:

- Otevřete šablonu zařízení a vyberte **zobrazení**.
- Pak zvolte **vizualizovat zařízení**.
- Do **název řídicího panelu**zadejte název řídicího panelu.
- Přidejte dlaždice na řídicí panel ze seznamu statických, vlastností, vlastností cloudu, telemetrie a příkazových dlaždic. Přetáhněte dlaždice, které byste chtěli přidat do řídicího panelu.
- Chcete-li vykreslit více hodnot telemetrie na dlaždici s jedním grafem, vyberte hodnoty telemetrie a pak vyberte **kombinovat**.
- Nakonfigurujte každou dlaždici, kterou přidáte, abyste mohli přizpůsobit způsob zobrazení dat, a to tak, že vyberete ikonu ozubeného kolečka nebo na dlaždici grafu vyberete tlačítko **změnit konfiguraci** .
- Uspořádejte a změňte velikost dlaždic na řídicím panelu.
- Uložte změny.

### <a name="configure-preview-device-to-view-dashboard"></a>Konfigurace zařízení Preview pro zobrazení řídicího panelu

Pokud chcete řídicí panel zobrazit a otestovat, můžete vybrat **Konfigurovat zařízení ve verzi Preview**, které vám po publikování uvidí řídicí panel jako váš operátor. Tato možnost umožňuje ověřit, že se v zobrazeních zobrazují správná data. Pomocí ID zařízení si můžete vybrat ze zařízení bez verze Preview, reálného testovacího zařízení, které jste nakonfigurovali pro šablonu zařízení, nebo existujícího zařízení v aplikaci.

## <a name="add-forms"></a>Přidat formuláře

Přidejte formuláře do šablony zařízení a umožněte tak operátorům spravovat zařízení zobrazením a nastavením vlastností. Operátoři můžou upravovat jenom vlastnosti cloudu a zapisovatelné vlastnosti zařízení. Pro šablonu zařízení můžete mít několik forem.

Přidání formuláře do šablony zařízení:

1. Otevřete šablonu zařízení a vyberte **zobrazení**.
1. Pak zvolte **Upravit zařízení a data v cloudu**.
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

Po publikování šablony zařízení může operátor přejít na stránku **zařízení** a přidat buď skutečná, nebo simulovaná zařízení, která používají šablonu zařízení. Můžete i nadále upravovat a ukládat šablonu zařízení, když provádíte změny, ale pokud chcete tyto změny předávat do operátoru, který se zobrazí na stránce **zařízení** , musíte vybrat **publikovat** pokaždé.

## <a name="define-a-new-iot-gateway-device-type-preview-features"></a>Definování nového typu zařízení brány IoT (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

V tomto kurzu se dozvíte jako tvůrce, jak použít šablonu zařízení brány k definování nového typu zařízení Azure IoT v aplikaci Azure IoT Central. 

V této části vytvoříte šablonu zařízení pro **inteligentní sestavení** . Zařízení inteligentní brány pro sestavování:

* Odesílá telemetrii, jako je například teplota a obsazení.
* Reaguje na zapisovatelné vlastnosti při aktualizaci v cloudu, jako je například interval odesílání telemetrie.
* Reaguje na příkazy, jako je resetování teploty.
* Umožňuje vztahy k jiným modelům schopností zařízení.

### <a name="create-iot-device-templates"></a>Vytvoření šablon zařízení IoT

Vytvoříte šablony zařízení IoT. 

V levém navigačním panelu klikněte na šablony zařízení, klikněte na **+ Nový**, vyberte dlaždici **zařízení IoT** a vyberte dlaždici snímače obsazení a klikněte na **Další: přizpůsobit**

![Zařízení IoT](./media/tutorial-define-iot-device-type/gateway-downstream-new.png)

Zobrazí se stránka pro kontrolu. Klikněte na tlačítko **vytvořit** . 

![Zařízení IoT](./media/tutorial-define-iot-device-type/gateway-downstream-review.png)

Vytvoří se nová šablona zařízení. 

![Zařízení IoT](./media/tutorial-define-iot-device-type/occupancy-sensor.png)

Vytvoří se šablona zařízení pro senzor S1. 

V levém navigačním panelu klikněte na šablony zařízení, klikněte na **+ Nový**, vyberte dlaždici **zařízení IoT** a vyberte dlaždici snímače obsazení a klikněte na **Další: přizpůsobit**

![Zařízení IoT](./media/tutorial-define-iot-device-type/s1-sensor.png)

Zobrazí se stránka pro kontrolu. Klikněte na tlačítko **vytvořit** . 

![Zařízení pro příjem dat](./media/tutorial-define-iot-device-type/s1-review.png)

Vytvoří se nová šablona zařízení. 

![Zařízení pro příjem dat](./media/tutorial-define-iot-device-type/s1-template.png)

## <a name="create-an-iot-gateway-device-template"></a>Vytvoření šablony zařízení brány IoT

Můžete zvolit vytvoření šablony zařízení brány IoT. Zařízení brány bude mít relace se zařízeními pro příjem dat, která se připojují k IoT Central prostřednictvím zařízení brány. 

### <a name="downstream-device-relationships-with-gateway-device"></a>Vztahy navazujících zařízení se zařízením brány

Zařízení IoT se můžou připojit ke službě Azure IoT Gateway. 

![Stránka centrální aplikace](./media/tutorial-define-iot-device-type/gatewaypattern.png)

Jako tvůrce můžete v aplikaci vytvořit a upravit šablony zařízení Azure IoT Gateway. Po publikování šablony zařízení můžete propojit skutečná zařízení, která implementují šablonu zařízení.

### <a name="select-device-template-type"></a>Vybrat typ šablony zařízení 

Chcete-li do aplikace přidat novou šablonu zařízení, otevřete stránku **šablony zařízení** . To provedete tak, že v levém podokně vyberete kartu **šablony zařízení** .

![Stránka centrální aplikace](./media/tutorial-define-iot-device-type/devicetemplate.png)

Kliknutím na **+ Nový** Začněte vytvářet novou šablonu zařízení.

![Šablony zařízení – nové](./media/tutorial-define-iot-device-type/devicetemplatenew.png)

![Výběr šablon zařízení – brána](./media/tutorial-define-iot-device-type/gateway-review.png)

Na stránce Výběr typu šablony zařízení se dostanete. Vyberte dlaždici **Azure IoT** a klikněte na **Další: tlačítko Přizpůsobit** v dolní části.

Zaškrtněte políčko Brána a klikněte na **vytvořit** . 

![Výběr šablon zařízení – brána](./media/tutorial-define-iot-device-type/gateway-customize.png)

Zobrazí se stránka s přehledem, klikněte na **vytvořit** . 

![Šablona zařízení-brána](./media/tutorial-define-iot-device-type/gateway-review.png)

Zadejte šablonu brány název šablony **Smart budova Gateway**. Klikněte na **vlastní** dlaždice.

Přidejte **informace o zařízení**standardního rozhraní.

### <a name="add-relationships"></a>Přidat relace

Pro zařízení, která se připojíte k zařízením brány, můžete přidat podřízené vztahy k modelům schopností zařízení.

Vytvořte vztahy k modelům schopností pro příjem dat z libovolného zařízení. Klikněte na **Uložit**.

![Šablona zařízení-brána](./media/tutorial-define-iot-device-type/gateway-occupancy-s1-rel.png)

### <a name="add-cloud-properties"></a>Přidat vlastnosti cloudu

Šablona zařízení může obsahovat vlastnosti cloudu. Vlastnosti cloudu existují jenom v aplikaci IoT Central a nikdy se neodesílají do zařízení nebo se z něj nepřijímají.

1. Vyberte **vlastnosti cloudu** a pak **+ přidat cloudovou vlastnost**. Pomocí informací v následující tabulce můžete přidat vlastnost cloudu do šablony zařízení.

    | Zobrazovaný název      | Sémantický typ | Schéma |
    | ----------------- | ------------- | ------ |
    | Last Service Date (Datum poslední údržby) | Žádný          | Datum   |
    | Jméno zákazníka     | Žádný          | Řetězec |

2. Kliknutím na **Uložit** uložte změny:

### <a name="add-customizations"></a>Přidat přizpůsobení

Vlastní nastavení použijte v případě, že potřebujete změnit rozhraní nebo přidat funkce specifické pro IoT Central, které nevyžadují, abyste si využívali verzi modelu schopností zařízení. Můžete přizpůsobit pole, když je model schopností v konceptu nebo publikovaném stavu. Můžete přizpůsobit pouze pole, která neruší kompatibilitu rozhraní. Můžete například provést následující věci:

- Přizpůsobení zobrazovaného názvu a jednotek schopnosti.
- Přidejte výchozí barvu, která se použije, když se hodnota zobrazí v grafu.
- Zadejte počáteční, minimální a maximální hodnoty vlastnosti.

Nemůžete přizpůsobit název schopnosti ani typ schopnosti. Klikněte na **Uložit**.

### <a name="create-views"></a>Vytváření zobrazení

Jako tvůrce můžete aplikaci přizpůsobit tak, aby zobrazovala relevantní informace o zařízení snímače životního prostředí pro operátora. Vlastní nastavení umožňuje operátorovi spravovat zařízení senzorů pro životní prostředí připojená k aplikaci. Můžete vytvořit dva typy zobrazení pro operátora pro práci se zařízeními:

* Formuláře pro zobrazení a úpravy vlastností zařízení a cloudu.
* Řídicí panely k vizualizaci zařízení.

### <a name="generate-default-views"></a>Generovat výchozí zobrazení

Pro tento kurz klikněte na vygenerovat výchozí zobrazení. Vygeneruje se přehled & o řídicích panelech. 

## <a name="publish-device-template"></a>Publikovat šablonu zařízení

Než budete moct vytvořit simulovaný senzor pro životní prostředí nebo připojit reálný senzor pro životní prostředí, budete muset publikovat šablonu zařízení.

Publikování šablony zařízení:

1. Na stránce **šablony zařízení** můžete přejít na šablonu zařízení.

2. Vyberte **Publikovat**.

3. V dialogovém okně **publikovat šablonu zařízení** vyberte **publikovat**:

Po publikování je šablona zařízení zobrazená na stránce **zařízení** a v operátoru. V publikované šabloně zařízení nemůžete upravovat model schopností zařízení bez vytváření nové verze. Můžete ale dělat aktualizace vlastností cloudu, přizpůsobení a zobrazení v publikované šabloně zařízení bez správy verzí. Po provedení změn vyberte **publikovat** , aby se tyto změny převedly do vašeho operátoru.

## <a name="create-gateway-simulated-device"></a>Vytvoření simulovaného zařízení brány

V Průzkumníku zařízení vytvořte simulovanou bránu inteligentního sestavení. 

![Šablona zařízení-brána](./media/tutorial-define-iot-device-type/smartbuildingdevice.png)

## <a name="create-downstream-simulated-devices"></a>Vytvoření navazujících simulovaných zařízení

V Průzkumníku zařízení vytvořte senzor simulace obsazení. 

![Šablona zařízení – obsazení](./media/tutorial-define-iot-device-type/occupancydevice.png)

V Průzkumníku zařízení se vytvoří simulovaný senzor S1. 

![Šablona zařízení – S1](./media/tutorial-define-iot-device-type/s1device.png)

## <a name="add-downstream-devices-relationships-to-gateway-device"></a>Přidat vztahy zařízení pro příjem dat do zařízení brány

Vyberte senzor S1 a obsazení a klikněte na **připojit k bráně**. 

![Šablona zařízení – S1](./media/tutorial-define-iot-device-type/connecttogateway.png)

Vyberte šablona zařízení brány, instance zařízení brány a klikněte na **připojit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

* Vytvoření nové brány IoT jako šablony zařízení
* Vytvořte vlastnosti cloudu.
* Vytvořte vlastní nastavení.
* Definujte vizualizaci pro telemetrii zařízení.
* Přidat relace
* Publikujte šablonu zařízení.

Tady je navrhovaný další krok:

> [!div class="nextstepaction"]
> [Připojení zařízení](tutorial-connect-pnp-device.md)
