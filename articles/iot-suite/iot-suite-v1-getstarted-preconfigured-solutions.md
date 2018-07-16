---
title: Začínáme s předkonfigurovanými řešeními | Microsoft Docs
description: V tomto kurzu se dozvíte, jak nasadit předkonfigurované řešení Azure IoT Suite.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 6ab38d1a-b564-469e-8a87-e597aa51d0f7
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 12eb858f0be4220419300019497070a081e58249
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723013"
---
# <a name="get-started-with-the-preconfigured-solutions"></a>Začínáme s předkonfigurovanými řešeními

[Předkonfigurovaná řešení][lnk-preconfigured-solutions] pro sadu Azure IoT Suite kombinují více služeb Azure IoT, aby mohla poskytovat komplexní řešení implementující běžné obchodní scénáře IoT. Předkonfigurované řešení *vzdálené monitorování* se připojuje k zařízením a monitoruje je. Řešení můžete použít k analýze streamu dat ze všech zařízení a ke zlepšení obchodních výsledků díky tomu, že procesy mohou automaticky reagovat na tento stream dat.

V tomto kurzu se dozvíte, jak zřídit předkonfigurované řešení vzdáleného monitorování. Také se seznámíte se základními funkcemi předkonfigurovaného řešení. Mnohé z těchto funkcí jsou přístupné z *řídicího panelu* řešení, který se nasazuje jako součást předkonfigurovaného řešení:

![Řídicí panel předkonfigurovaného řešení vzdáleného monitorování][img-dashboard]

K dokončení tohoto kurzu potřebujete mít aktivní předplatné služby Azure.

> [!NOTE]
> Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure][lnk_free_trial].

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

## <a name="scenario-overview"></a>Přehled scénáře

Když nasadíte předkonfigurované řešení vzdáleného monitorování, bude předem naplněné prostředky, které vám umožní projít běžným scénářem vzdáleného monitorování. V tomto scénáři několik zařízení připojených k řešení hlásí neočekávané teplotní hodnoty. V následujících částech se dozvíte, jak:

* identifikovat zařízení, která odesílají neočekávané teplotní hodnoty,
* nakonfigurovat tato zařízení, aby odesílala podrobnější telemetrii,
* opravit problém pomocí aktualizace firmwaru na těchto zařízeních,
* ověřit, že jste touto akcí problém vyřešili.

Klíčovou vlastností tohoto scénáře je, že všechny tyto akce můžete provádět vzdáleně z řídicího panelu řešení. Nemusíte mít fyzický přístup k zařízením.

## <a name="view-the-solution-dashboard"></a>Zobrazení řídicího panelu řešení

Přes řídicí panel řešení můžete spravovat nasazené řešení. Můžete například zobrazit telemetrická data, přidat zařízení nebo konfigurovat pravidla.

1. Až bude zřizování dokončeno a dlaždice předkonfigurovaného řešení bude hlásit **Připraveno**, zvolte **Spustit**. Na nové kartě se otevře portál předkonfigurovaného řešení vzdáleného monitorování.

    ![Spuštění předkonfigurovaného řešení][img-launch-solution]

1. Ve výchozím nastavení se na portálu řešení zobrazuje *řídicí panel*. Do jiných oblastní portálu řešení můžete přecházet pomocí nabídky na levé straně stránky.

    ![Řídicí panel předkonfigurovaného řešení vzdáleného monitorování][img-menu]

Řídicí panel obsahuje tyto informace:

* Mapu, která zobrazuje umístění každého zařízení, které je připojené k řešení. Když řešení poprvé spustíte, zahrnuje 25 simulovaných zařízení. Simulovaná zařízení jsou implementována jako Azure WebJobs a řešení k vykreslování informací na mapě používá Bing Maps API. Pokud chcete mít mapu dynamickou, přečtěte si [Nejčastější dotazy][lnk-faq].
* Panel **Historie telemetrie**, na kterém se skoro v reálném čase zobrazuje telemetrie vlhkosti a teploty z vybraného zařízení a agregovaná data (například minimální, maximální a průměrná vlhkost).
* Panel **Historie alarmů**, který zobrazuje události, kdy v poslední době hodnota telemetrie překročila stanovenou mez. Kromě příkladů vytvořených předkonfigurovaným řešením můžete definovat i vlastní alarmy.
* Panel **Úlohy**, na kterém se zobrazují informace o plánovaných úlohách. Vlastní úlohy můžete plánovat na stránce **Úlohy správy**.

## <a name="view-alarms"></a>Zobrazení alarmů

Panel Historie alarmů ukazuje, že pět zařízení hlásí vyšší než očekávané hodnoty telemetrie.

![Historie alarmů na řídicím panelu řešení][img-alarms]

> [!NOTE]
> Tyto alarmy generuje pravidlo, které je součástí předkonfigurovaného řešení. Toto pravidlo generuje upozornění, když teplotní hodnota, kterou zařízení odešle, překročí 60. Pokud v nabídce vlevo zvolíte [Pravidla](#add-a-rule) nebo [Akce](#add-an-action), můžete definovat vlastní pravidla a akce.

## <a name="view-devices"></a>Zobrazení zařízení

V seznamu *zařízení* jsou uvedena všechna zařízení, která jsou zaregistrována v řešení. V seznamu zařízení můžete zobrazit a upravovat metadata zařízení, přidávat a odebírat zařízení a vyvolávat v zařízeních metody. Seznam zařízení můžete filtrovat a řadit. Můžete si také přizpůsobit, jaké sloupce se v seznamu zařízení zobrazí.

1. Zvolte **Zařízení** a zobrazte seznam zařízení pro toto řešení.

   ![Zobrazení seznamu zařízení na portálu řešení][img-devicelist]

1. V seznamu zařízení je zpočátku uvedeno 25 simulovaných zařízení, která byla vytvořena během procesu zřizování. Do řešení můžete přidat další simulovaná a fyzická zařízení.

1. Pokud chcete zobrazit podrobnosti o některém zařízení, vyberte ho v seznamu zařízení.

   ![Zobrazení podrobností o zařízení na portálu řešení][img-devicedetails]

Panel **Podrobnosti o zařízení** se skládá ze šesti částí:

* Kolekce odkazů, pomocí kterých si můžete přizpůsobit ikonu zařízení, zakázat zařízení, přidat pravidlo, vyvolat metodu nebo odeslat příkaz. Porovnání příkazů (zpráv typu zařízení-cloud) a metod (přímých metod) najdete v [doprovodných materiálech ke komunikaci typu cloud-zařízení][lnk-c2d-guidance].
* V části **Dvojče zařízení – Značky** můžete upravovat hodnoty značek pro zařízení. Hodnoty značek můžete zobrazit v seznamu zařízení a s jejich pomocí seznam zařízení filtrovat.
* V části **Dvojče zařízení – Požadované vlastnosti** můžete nastavit hodnoty vlastností, které se odešlou do zařízení.
* V části **Dvojče zařízení – Ohlášené vlastnosti** se zobrazují hodnoty vlastností odeslané ze zařízení.
* V části **Vlastnosti zařízení** se zobrazují informace z registru identit, jako například ID zařízení a ověřovací klíče.
* V části **Poslední úlohy** se zobrazují informace o všech úlohách, které nedávno cílily na příslušné zařízení.

## <a name="filter-the-device-list"></a>Filtrování seznamu zařízení

Pomocí filtru můžete zobrazit jenom zařízení, která odesílají neočekávané teplotní hodnoty. Předkonfigurované řešení vzdáleného monitorování obsahuje filtr **Poškozená zařízení**, který zobrazí zařízení, u nichž je střední hodnota teploty vyšší než 60. Můžete si také [vytvořit vlastní filtry](#add-a-filter).

1. Zvolte **Otevřít uložený filtr** a zobrazí se seznam dostupných filtrů. Potom zvolením aplikujte filtr **Poškozená zařízení**:

    ![Zobrazení seznamu filtrů][img-unhealthy-filter]

1. V seznamu zařízení teď budou uvedena pouze zařízení, u nichž je střední hodnota teploty vyšší než 60.

    ![Zobrazení filtrovaného seznamu zařízení, který ukazuje poškozená zařízení][img-filtered-unhealthy-list]

## <a name="update-desired-properties"></a>Aktualizace požadovaných vlastností

Nyní jste identifikovali sadu zařízení, která možná vyžadují opravu. Rozhodnete se ale, že frekvence odesílání dat každých 15 sekund není pro jasnou diagnostiku problému dostatečná. Změníte frekvenci odesílání telemetrie na 5 sekund, což vám poskytne více datových bodů pro lepší diagnostiku problému. Tuto změnu konfigurace můžete z portálu řešení bez vyžádání doručit do vzdálených zařízení. Můžete jednou provést změnu, vyhodnotit její dopad a na základě výsledků se potom rozhodnout.

Postupujte podle těchto kroků a spusťte úlohu, která změní požadovanou vlastnost **TelemetryInterval** pro příslušná zařízení. Zařízení po přijetí nové hodnoty vlastnosti **TelemetryInterval** změní vlastní konfiguraci a budou odesílat telemetrii každých pět sekund namísto každých 15 sekund:

1. Když je v seznamu zařízení zobrazený seznam poškozených zařízení, zvolte **Plánovač úloh** a potom **Upravit dvojče zařízení**.

1. Zavolejte úlohu **Změnit interval telemetrie**.

1. Změňte hodnotu **požadované vlastnosti** s názvem **desired.Config.TelemetryInterval** na pět sekund.

1. Zvolte **Naplánovat**.

    ![Změna vlastnosti TelemetryInterval na pět sekund][img-change-interval]

1. Průběh úlohy můžete sledovat na stránce **Úlohy správy** na portálu.

> [!NOTE]
> Pokud chcete změnit hodnotu požadované vlastnosti pro jednotlivá zařízení, místo spouštění úlohy použijte část **Požadované vlastnosti** na panelu **Podrobnosti o zařízení**.

Tato úloha nastaví hodnotu požadované vlastnosti **TelemetryInterval** ve dvojčeti zařízení pro všechna zařízení vybraná pomocí filtru. Zařízení si tuto hodnotu načtou z dvojčete zařízení a aktualizují vlastní chování. Když zařízení načte a zpracuje požadovanou vlastnost z dvojčete zařízení, nastaví odpovídající hodnotu ohlášené vlastnosti.

## <a name="invoke-methods"></a>Vyvolání metod

Zatímco je úloha spuštěná, v seznamu poškozených zařízení si všimnete, že všechna tato zařízení mají staré (nižší než 1.6) verze firmwaru.

![Zobrazení ohlášených verzí firmwaru pro poškozená zařízení][img-old-firmware]

Verze firmwaru může být hlavní příčinou neočekávaných teplotních hodnot, protože víte, že ostatní zařízení, která jsou v pořádku, byla nedávno aktualizována na verzi 2.0. Pomocí integrovaného filtru **Zařízení se starou verzí firmwaru** můžete identifikovat všechna zařízení se starými verzemi firmwaru. Z portálu potom můžete vzdáleně aktualizovat všechna zařízení, která ještě používají staré verze firmwaru:

1. Zvolte **Otevřít uložený filtr** a zobrazí se seznam dostupných filtrů. Potom zvolením aplikujte filtr **Zařízení se starou verzí firmwaru**:

    ![Zobrazení seznamu filtrů][img-old-filter]

1. V seznamu zařízení nyní budou uvedena pouze zařízení se starými verzemi firmwaru. Tento seznam obsahuje pět zařízení, která identifikoval filtr **Poškozená zařízení**, a tři další zařízení:

    ![Zobrazení filtrovaného seznamu zařízení, který ukazuje stará zařízení][img-filtered-old-list]

1. Zvolte **Plánovač úloh** a potom **Vyvolat metodu**.

1. Nastavte **Název úlohy** na **Aktualizace firmwaru na verzi 2.0**.

1. V poli **Metoda** zvolte **InitiateFirmwareUpdate**.

1. Nastavte parametr **FwPackageUri** na **https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin**.

1. Zvolte **Naplánovat**. Ve výchozím nastavení se úloha spustí nyní.

    ![Vytvoření úlohy pro aktualizaci firmwaru na vybraných zařízeních][img-method-update]

> [!NOTE]
> Pokud chcete vyvolat metodu na jednotlivých zařízeních, místo spouštění úlohy na panelu **Podrobnosti o zařízení** zvolte **Metody**.

Tato úloha vyvolá přímou metodu **InitiateFirmwareUpdate** na všech zařízeních vybraných pomocí filtru. Zařízení okamžitě odešlou reakci do služby IoT Hub a následně asynchronně zahájí proces aktualizace firmwaru. Zařízení poskytují informace o stavu procesu aktualizace firmwaru prostřednictvím hodnot ohlášených vlastností, jak je znázorněno na následujících snímcích obrazovky. Zvolte ikonu **Aktualizovat** a aktualizujte informace v seznamech zařízení a úloh:

![Seznam úloh ukazující spuštěnou úlohu aktualizace firmwaru][img-update-1]
![Seznam zařízení ukazující stav aktualizace firmwaru][img-update-2]
![Seznam úloh ukazující dokončenou úlohu aktualizace firmwaru][img-update-3]

> [!NOTE]
> V produkčním prostředí můžete úlohy plánovat tak, aby se spouštěly během určeného časového období údržby.

## <a name="scenario-review"></a>Revize scénáře

V tomto scénáři jste identifikovali potenciální problém s některými vzdálenými zařízeními pomocí historie alarmů na řídicím panelu a filtru. Pak jste pomocí filtru a úlohy vzdáleně nakonfigurovali zařízení, aby poskytovala více údajů a tak vám pomohla s diagnostikou problému. Nakonec jste pomocí filtru a úlohy naplánovali údržbu příslušných zařízení. Pokud se vrátíte na řídicí panel, můžete zkontrolovat, že ze zařízení ve vašem řešení již nepřichází žádné alarmy. Pomocí filtru můžete ověřit, že na všech zařízeních ve vašem řešení je aktuální firmware, a že v řešení již nejsou žádná poškozená zařízení:

![Filtr ukazující, že všechna zařízení mají aktuální firmware][img-updated]

![Filtr ukazující, že všechna zařízení jsou v pořádku][img-healthy]

## <a name="other-features"></a>Další funkce

Následující části popisují některé další funkce předkonfigurovaného řešení vzdáleného monitorování, které nebyly popsány v předchozím scénáři.

### <a name="customize-columns"></a>Přizpůsobení sloupců

Informace, které se zobrazují v seznamu zařízení, si můžete přizpůsobit tak, že zvolíte **Editor sloupců**. Můžete přidat a odebrat sloupce, které zobrazují hodnoty ohlašovaných vlastností a značek. Můžete také změnit pořadí sloupců a přejmenovat je:

   ![Ikona editoru sloupců v seznamu zařízení][img-columneditor]

### <a name="customize-the-device-icon"></a>Přizpůsobení ikony zařízení

Ikonu zařízení, která se zobrazuje v seznamu zařízení, si můžete přizpůsobit na panelu **Podrobnosti o zařízení** podle následujícího postupu:

1. Zvolením ikony tužky otevřete panel **Upravit obrázek** pro zařízení:

   ![Otevřený editor obrázku zařízení][img-startimageedit]

1. Nahrajte nový obrázek nebo použijte některý z existujících obrázků a potom zvolte **Uložit**:

   ![Editor Upravit obrázek zařízení][img-imageedit]

1. Obrázek, který jste vybrali, se teď zobrazí ve sloupci **Ikona** pro zařízení.

> [!NOTE]
> Obrázek se ukládá v úložišti objektů blob. Značka ve dvojčeti zařízení obsahuje odkaz na tento obrázek v úložišti objektů blob.

### <a name="add-a-device"></a>Přidání zařízení

Při nasazení předkonfigurovaného řešení je automaticky zřízeno 25 ukázkových zařízení, která se zobrazí v seznamu zařízení. Tato zařízení jsou *simulovaná zařízení*, která běží ve webové úloze Azure. Simulované zařízení umožňují snadno experimentovat s předkonfigurovaným řešením, aniž by bylo nutné nasazovat skutečná fyzická zařízení. Pokud chcete k řešení připojit skutečné zařízení, přečtěte si kurz [Připojení zařízení k předkonfigurovanému řešení vzdáleného monitorování][lnk-connect-rm].

Následující kroky ukazují, jak do řešení přidat simulované zařízení:

1. Vraťte se zpět k seznamu zařízení.

1. Pokud chcete přidat zařízení, zvolte **+ Přidat zařízení** vlevo dole.

   ![Přidání zařízení do předkonfigurovaného řešení][img-adddevice]

1. Na dlaždici **Simulované zařízení** zvolte **Přidat nové**.

   ![Nastavení podrobností o zařízení na řídicím panelu][img-addnew]

   Pokud se rozhodnete vytvořit**vlastní zařízení**, můžete kromě nového simulovaného zařízení vytvořit i zařízení fyzické. Další informace o připojení fyzických zařízení k řešení najdete v tématu [Připojení zařízení k předkonfigurovanému řešení vzdáleného monitorování sady IoT Suite][lnk-connect-rm].

1. Vyberte možnost **Ručně definovat vlastní ID zařízení** a zadejte jedinečný název zařízení, například **mydevice_01**.

1. Zvolte **Vytvořit**.

   ![Uložení nového zařízení][img-definedevice]

1. V kroku 3 **Přidání simulovaného zařízení** zvolte **Hotovo**. Vrátíte se do seznamu zařízení.

1. Zařízení nyní v seznamu uvidíte jako **Spuštěné**.

    ![Zobrazení nového zařízení v seznamu zařízení][img-runningnew]

1. Na řídicím panelu můžete také vidět simulovanou telemetrii z nového zařízení.

    ![Zobrazení telemetrie z nového zařízení][img-runningnew-2]

### <a name="disable-and-delete-a-device"></a>Zakázání a odstranění zařízení

Zařízení můžete zakázat, zakázané zařízení lze následně odebrat:

![Zakázání a odebrání zařízení][img-disable]

### <a name="add-a-rule"></a>Přidání pravidla

Pro zařízení, které jste právě přidali, ještě nejsou stanovena žádná pravidla. V této sekci přidáte pravidlo, které spustí alarm, jakmile teplota hlášená novým zařízením přesáhne 47 stupňů. Ještě než začnete, všimněte si, že historie telemetrie nového zařízení na řídicím panelu ukazuje, že teplota měřená tímto zařízením nikdy nepřesáhne 45 stupňů.

1. Vraťte se zpět k seznamu zařízení.

1. Pokud chcete přidat pravidlo pro zařízení, vyberte nové zařízení na panelu **Seznam zařízení** a potom zvolte **Přidat pravidlo**.

1. Vytvořte pravidlo, které jako datové pole používá **teplotu**, a pokud tato veličina přesáhne 47 stupňů, použije jako výstup **AlarmTemp** (teplotní alarm):

    ![Přidání pravidla pro zařízení][img-adddevicerule]

1. Pokud chcete uložit změny, zvolte **Uložit a zobrazit pravidla**.

1. V podokně podrobností o novém zařízení zvolte **Příkazy**.

    ![Přidání pravidla pro zařízení][img-adddevicerule2]

1. V seznamu příkazů vyberte **ChangeSetPointTemp** (Změnit mezní teplotu) a příkaz **SetPointTemp** (Nastavit mezní teplotu) nastavte na 45. Potom zvolte **Odeslat příkaz**:

    ![Přidání pravidla pro zařízení][img-adddevicerule3]

1. Vraťte se zpět na řídicí panel. Po chvilce se v podokně **Historie alarmů** ukáže nový záznam, když teplota hlášená tímto novým zařízením přesáhne hranici 47 stupňů:

    ![Přidání pravidla pro zařízení][img-adddevicerule4]

1. Všechna pravidla můžete kontrolovat a upravovat na stránce **Pravidla** na řídicím panelu:

    ![Zobrazení pravidel zařízení][img-rules]

1. Všechny akce, které se dají provést v reakci na některé pravidlo, můžete zkontrolovat a upravit na stránce **Akce** na řídicím panelu:

    ![Zobrazení akcí zařízení][img-actions]

> [!NOTE]
> Můžete definovat akce, které budou pomocí funkce [Logic Apps][lnk-logic-apps] odesílat zprávy e-mailem nebo jako SMS v reakci na pravidlo nebo v rámci integrace s obchodním systémem. Další informace najdete v tématu [Propojení funkce Logic Apps s předkonfigurovaným řešením vzdáleného monitorování sady Azure IoT Suite][lnk-logicapptutorial].

### <a name="manage-filters"></a>Správa filtrů

V seznamu zařízení můžete vytvořit, uložit a znovu načíst filtry, pomocí kterých zobrazíte přizpůsobený seznam zařízení připojených k vaší službě IoT Hub. Vytvoření filtru:

1. Zvolte ikonu filtru nad seznamem zařízení:

    ![Otevření editoru filtru][img-editfiltericon]

1. V **Editoru filtru** přidejte pole, operátory a hodnoty, podle kterých chcete filtrovat seznam zařízení. Filtr můžete upřesnit přidáním více klauzulí. Zvolením **Filtrovat** použijte filtr:

    ![Vytvoření filtru][img-filtereditor]

1. V tomto příkladu je seznam filtrovaný podle výrobce a čísla modelu:

    ![Filtrovaný seznam][img-filterelist]

1. Pokud chcete filtr uložit s vlastním názvem, zvolte ikonu **Uložit jako**:

    ![Uložení filtru][img-savefilter]

1. Pokud chcete znovu použít dříve uložený filtr, zvolte ikonu **Otevřít uložený filtr**:

    ![Otevření filtru][img-openfilter]

Můžete vytvořit filtry na základě ID zařízení, stavu zařízení, požadovaných vlastností, ohlášených vlastností a značek. Vlastní značky můžete k zařízení přidat v části **Značky** panelu **Podrobnosti o zařízení** nebo spuštěním úlohy, která aktualizuje značky na více zařízeních.

> [!NOTE]
> V **Editoru filtru** můžete pomocí **Rozšířeného zobrazení** upravit přímo text dotazu.

### <a name="commands"></a>Příkazy

Na panelu **Podrobnosti o zařízení** můžete odesílat příkazy do zařízení. Jakmile zařízení poprvé spustíte, odešle do řešení informace o příkazech, které podporuje. Diskuzi o rozdílech mezi příkazy a metodami najdete v tématu [Možnosti komunikace typu zařízení-cloud ve službě IoT Hub][lnk-c2d-guidance].

1. Na panelu **Podrobnosti o zařízení** pro vybrané zařízení zvolte **Příkazy**:

   ![Příkazy zařízení na řídicím panelu][img-devicecommands]

1. Ze seznamu příkazů vyberte **PingDevice** (Otestovat zařízení příkazem ping).

1. Zvolte **Odeslat příkaz**.

1. Stav příkazu uvidíte v historii příkazů.

   ![Stav příkazu na řídicím panelu][img-pingcommand]

Řešení sleduje stav každého příkazu, který odešle. Zpočátku je výsledek uveden jako **Čekající**. Když zařízení ohlásí, že příkaz provedlo, výsledek je nastaven na **Úspěch**.

## <a name="behind-the-scenes"></a>Informace pro pokročilé uživatele

Když nasadíte předkonfigurované řešení, proces nasazení vytvoří ve vybraném předplatném Azure několik prostředků. Tyto prostředky můžete zobrazit na webu [Azure Portal][lnk-portal]. Proces nasazení vytváří **skupinu prostředků**. Její název bude vycházet z názvu, který jste vybrali pro předkonfigurované řešení:

![Předkonfigurované řešení na portálu Azure Portal][img-portal]

Nastavení každého prostředku se zobrazí, když jej vyberete v seznamu ve skupině prostředků.

Můžete taky zobrazit zdrojový kód pro předkonfigurované řešení. Zdrojový kód předkonfigurovaného řešení vzdáleného monitorování najdete v úložišti GitHub [azure-iot-remote-monitoring][lnk-rmgithub]:

* Složka **DeviceAdministration** (Správa zařízení) obsahuje zdrojový kód pro řídicí panel.
* Složka **Simulator** (Simulátor) obsahuje zdrojový kód pro simulované zařízení.
* Složka **EventProcessor** (Procesor událostí) obsahuje zdrojový kód pro proces back-end, který zpracovává příchozí telemetrii.

Jakmile budete hotovi, můžete předkonfigurované řešení z vašeho předplatného Azure odstranit na webu [azureiotsuite.com][lnk-azureiotsuite]. Tento web umožňuje snadno odstranit všechny prostředky, které byly zřízeny při vytvoření předkonfigurovaného řešení.

> [!NOTE]
> Abyste zajistili, že jste odstranili opravdu všechno spojené s předkonfigurovaným řešením, odstraňte řešení na webu [azureiotsuite.com][lnk-azureiotsuite] – neodstraňujte jenom skupinu prostředků na portálu.

## <a name="next-steps"></a>Další kroky

Když jste teď nasadili fungující předkonfigurované řešení, můžete pokračovat v seznamování se sadou IoT Suite přečtením následujících článků:

* [Návod pro předkonfigurované řešení vzdáleného monitorování][lnk-rm-walkthrough]
* [Připojení zařízení k předkonfigurovanému řešení vzdáleného monitorování][lnk-connect-rm]
* [Oprávnění na webu azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-v1-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-v1-getstarted-preconfigured-solutions/dashboard.png
[img-menu]: media/iot-suite-v1-getstarted-preconfigured-solutions/menu.png
[img-devicelist]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicelist.png
[img-alarms]: media/iot-suite-v1-getstarted-preconfigured-solutions/alarms.png
[img-devicedetails]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-v1-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-v1-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-v1-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-v1-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-v1-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-v1-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-v1-getstarted-preconfigured-solutions/rules.png
[img-adddevicerule]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-v1-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-v1-getstarted-preconfigured-solutions/portal.png
[img-disable]: media/iot-suite-v1-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-v1-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-v1-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-v1-getstarted-preconfigured-solutions/imagedit2.png
[img-editfiltericon]: media/iot-suite-v1-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-v1-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-v1-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-v1-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-v1-getstarted-preconfigured-solutions/openfilter.png
[img-unhealthy-filter]: media/iot-suite-v1-getstarted-preconfigured-solutions/unhealthyfilter.png
[img-filtered-unhealthy-list]: media/iot-suite-v1-getstarted-preconfigured-solutions/unhealthylist.png
[img-change-interval]: media/iot-suite-v1-getstarted-preconfigured-solutions/changeinterval.png
[img-old-firmware]: media/iot-suite-v1-getstarted-preconfigured-solutions/noticeold.png
[img-old-filter]: media/iot-suite-v1-getstarted-preconfigured-solutions/oldfilter.png
[img-filtered-old-list]: media/iot-suite-v1-getstarted-preconfigured-solutions/oldlist.png
[img-method-update]: media/iot-suite-v1-getstarted-preconfigured-solutions/methodupdate.png
[img-update-1]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate1.png
[img-update-2]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate2.png
[img-update-3]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate3.png
[img-updated]: media/iot-suite-v1-getstarted-preconfigured-solutions/updated.png
[img-healthy]: media/iot-suite-v1-getstarted-preconfigured-solutions/healthy.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-v1-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-logicapptutorial]: iot-suite-v1-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-v1-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-v1-connecting-devices.md
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-faq]: iot-suite-v1-faq.md