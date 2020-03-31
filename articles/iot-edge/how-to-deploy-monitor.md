---
title: Nasazení modulů ve velkém měřítku na webu Azure Portal – Azure IoT Edge
description: Použití portálu Azure k vytvoření automatických nasazení pro skupiny zařízení IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0a20ea4236683e26c51bc75309435c65e24271d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271431"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Nasazení a monitorování modulů IoT Edge ve velkém měřítku pomocí portálu Azure

Vytvořte **automatické nasazení IoT Edge** na webu Azure Portal pro správu probíhajících nasazení pro mnoho zařízení najednou. Automatická nasazení pro IoT Edge jsou součástí funkce [automatické správy zařízení](/azure/iot-hub/iot-hub-automatic-device-management) služby IoT Hub. Nasazení jsou dynamické procesy, které umožňují nasadit více modulů do více zařízení, sledovat stav a stav modulů a v případě potřeby provádět změny.

Další informace najdete [v tématu Principy automatického nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identifikace zařízení pomocí značek

Před vytvořením nasazení musíte být schopni určit, která zařízení chcete ovlivnit. Azure IoT Edge identifikuje zařízení pomocí **značek** v dvojčeti zařízení. Každé zařízení může mít více značek, které definujete jakýmkoli způsobem, který dává smysl pro vaše řešení.

Pokud například spravujete kampus inteligentních budov, můžete do zařízení přidat značky polohy, typu místnosti a prostředí:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Další informace o dvojčata zařízení a značky, [najdete v tématu Principy a použití dvojčatzařízení v centru IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Vytvoření nasazení

IoT Edge poskytuje dva různé typy automatických nasazení, které můžete použít k přizpůsobení scénáře. Můžete vytvořit standardní *nasazení*, které zahrnuje moduly modulů modulů modulů runtime systému a všechny další moduly a trasy. Každé zařízení může použít pouze jedno nasazení. Nebo můžete vytvořit *vrstvené nasazení*, které zahrnuje pouze vlastní moduly a trasy, nikoli modul runtime systému. Mnoho vrstvených nasazení lze kombinovat na zařízení, nad standardní nasazení. Další informace o tom, jak dva typy automatických nasazení spolupracují, naleznete [v tématu Principy automatických nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

Kroky pro vytvoření nasazení a vrstvené nasazení jsou velmi podobné. Všechny rozdíly jsou volány v následujících krocích.

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do služby IoT Hub.
1. V nabídce v levém podokně vyberte v části **Automatická správa zařízení** **položku IoT Edge** .
1. Na horním panelu vyberte **Vytvořit nasazení** nebo Vytvořit nasazení **s vrstvami**.

Existuje pět kroků k vytvoření nasazení. Následující části procházejí každou z nich.

### <a name="step-1-name-and-label"></a>Krok 1: Název a popisek

1. Pojmenujte nasazení jedinečný název, který je až 128 malá písmena. Vyhněte se mezerám `& ^ [ ] { } \ | " < > /`a následujícím neplatným znakům: .
1. Štítky můžete přidat jako páry klíč-hodnota, které vám pomohou sledovat vaše nasazení. Například **HostPlatform** a **Linux**nebo **Version** a **3.0.1**.
1. Vyberte **Další: Moduly** se přesunete ke kroku dva.

### <a name="step-2-modules"></a>Krok 2: Moduly

Do nasazení můžete přidat až 20 modulů. Pokud vytvoříte nasazení bez modulů, odebere všechny aktuální moduly z cílových zařízení.

V nasazenímůžete spravovat nastavení pro moduly agenta IoT Edge a Centra IoT Edge. Chcete-li nakonfigurovat dva moduly modulů runtime, vyberte **možnost Nastavení modulu runtime.** V nasazení vrstvené moduly runtime nejsou zahrnuty, takže nelze nakonfigurovat.

Můžete přidat tři typy modulů:

* Modul IoT Edge
* Modul marketplace
* Modul Azure Stream Analytics

#### <a name="add-an-iot-edge-module"></a>Přidání modulu IoT Edge

Chcete-li přidat vlastní kód jako modul nebo ručně přidat modul služby Azure, postupujte takto:

1. V části **Pověření registru kontejneru** na stránce zadejte názvy a pověření pro všechny privátní kontejnerové registry, které obsahují ibitové kopie modulu pro toto nasazení. Agent IoT Edge ohlásí chybu 500, pokud nemůže najít pověření registru kontejneru pro bitovou kopii Dockeru.
1. V části **Moduly okrajů IoT** na stránce klikněte na **Přidat**.
1. V rozevírací nabídce vyberte **Modul IoT Edge.**
1. Zadejte svému modulu **název modulu IoT Edge**.
1. Do pole **Identifikátor URI bitové kopie** zadejte obrázek kontejneru pro modul.
1. V rozevírací nabídce vyberte **zásadu restartování**. Vybírat můžete z těchto možností:
   * **vždy** - Modul se vždy restartuje, pokud se z nějakého důvodu vypne.
   * **never** - Modul se nikdy nerestartuje, pokud se z nějakého důvodu vypne.
   * **on-failure** - Modul se restartuje, pokud dojde k chybě, ale ne, pokud se vypne čistě.
   * **on-nev pořádku** - modul se restartuje, pokud dojde k chybě nebo vrátí stav není v pořádku. Je na každém modulu k implementaci funkce stavu stavu.
1. Pomocí rozevírací nabídky vyberte **požadovaný stav** modulu. Vybírat můžete z těchto možností:
   * **běh** - Spuštění je výchozí možnost. Modul se spustí ihned po nasazení.
   * **zastaveno** – po nasazení zůstane modul nečinný, dokud nebude vyzván ke spuštění vámi nebo jiným modulem.
1. Zadejte všechny **možnosti vytvoření kontejneru,** které by měly být předány do kontejneru. Další informace naleznete v [tématu docker create](https://docs.docker.com/engine/reference/commandline/create/).
1. Pokud chcete do dvojčete modulu přidat značky nebo jiné vlastnosti, vyberte **nastavení dvojčete** modulu.
1. Zadejte **proměnné prostředí** pro tento modul. Proměnné prostředí poskytují informace o konfiguraci modulu.
1. Chcete-li přidat modul do nasazení, vyberte **přidat.**

#### <a name="add-a-module-from-the-marketplace"></a>Přidání modulu z webu Marketplace

Pokud chcete přidat modul z Azure Marketplace, postupujte takto:

1. V části **Moduly okrajů IoT** na stránce klikněte na **Přidat**.
1. V rozevírací nabídce vyberte **Modul Marketplace.**
1. Zvolte modul ze stránky **Webu Marketplace modulu IoT Edge.** Vybraný modul se automaticky nakonfiguruje pro vaše předplatné, skupinu prostředků a zařízení. Pak se zobrazí v seznamu modulů IoT Edge. Některé moduly mohou vyžadovat další konfiguraci. Další informace najdete [v tématu Nasazení modulů z Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace).

#### <a name="add-a-stream-analytics-module"></a>Přidání modulu Stream Analytics

Pokud chcete přidat modul z Azure Stream Analytics, postupujte takto:

1. V části **Moduly okrajů IoT** na stránce klikněte na **Přidat**.
1. V rozevírací nabídce vyberte **modul Azure Stream Analytics.**
1. V pravém podokně zvolte **předplatné**.
1. Vyberte si **úlohu**IoT Edge .
1. Výběrem **možnosti Uložit** přidáte modul do nasazení.

#### <a name="configure-module-settings"></a>Konfigurace nastavení modulu

Po přidání modulu do nasazení můžete vybrat jeho název a otevřít stránku **Modul u aktualizace IoT Edge.** Na této stránce můžete upravit nastavení modulu, proměnné prostředí, vytvářet možnosti a dvojče modulu. Pokud jste přidali modul z trhu, může již mít některé z těchto parametrů vyplněny.

Pokud vytváříte vrstvené nasazení, je možné, že konfigurujete modul, který existuje v jiných nasazeních zaměřených na stejná zařízení. Chcete-li aktualizovat dvojče modulu bez přepsání jiných verzí, **Module Twin Property** otevřete kartu Nastavení `properties.desired.settings` **dvojčete modulu.** Pokud definujete vlastnosti `properties.desired` v rámci pouze pole, přepíše požadované vlastnosti pro modul definovaný v jakékoli nasazení s nižší prioritou.

![Nastavit vlastnost dvojčete modulu pro nasazení vrstvené](./media/how-to-deploy-monitor/module-twin-property.png)

Další informace o konfiguraci dvojčete modulu v nasazeních s vrstvami naleznete v tématu [Layered deployment](module-deployment-monitoring.md#layered-deployment).

Jakmile budete mít všechny moduly pro nasazení nakonfigurováno, vyberte **Další: Trasy** přesunout do kroku tři.

### <a name="step-3-routes"></a>Krok 3: Trasy

Trasy definují, jak moduly vzájemně komunikují v rámci nasazení. Ve výchozím nastavení průvodce poskytuje trasu snázvem **upstream** a definované jako **FROM\* /messages/ INTO $upstream**, což znamená, že všechny zprávy výstupní všechny moduly jsou odesílány do centra IoT.  

Přidejte nebo aktualizujte trasy informacemi z [deklarovat trasy](module-composition.md#declare-routes)a pak vyberte **Další** a pokračujte v oddílu revize.

Vyberte **další: Metriky**.

### <a name="step-4-metrics"></a>Krok 4: Metriky

Metriky poskytují souhrnpočty různých stavů, které zařízení může hlásit zpět v důsledku použití obsahu konfigurace.

1. Zadejte název pro **Název metriky**.

1. Zadejte dotaz na **kritéria metriky**. Dotaz je založen na vlastnostech modulu modulu centra IoT Edge [.](module-edgeagent-edgehub.md#edgehub-reported-properties) Metrika představuje počet řádků vrácených dotazem.

   Například:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Vyberte **další: Cílová zařízení**.

### <a name="step-5-target-devices"></a>Krok 5: Cílová zařízení

Pomocí vlastnosti tags ze svých zařízení zacilte na konkrétní zařízení, která by měla toto nasazení přijmout.

Vzhledem k tomu, že více nasazení může cílit na stejné zařízení, měli byste každému nasazení udělit číslo priority. Pokud někdy dojde ke konfliktu, nasazení s nejvyšší prioritou (větší hodnoty označují vyšší prioritu) vyhrává. Pokud dvě nasazení mají stejné číslo priority, vyhrává ten, který byl vytvořen naposledy.

Pokud více nasazení cílí na stejné zařízení, použije se pouze jedno s vyšší prioritou. Pokud více vrstvených nasazení cílí na stejné zařízení, pak jsou všechny použity. Pokud jsou však duplikovány všechny vlastnosti, například pokud existují dvě trasy se stejným názvem, přepíše zbytek z nich.

Jakékoli vrstvené nasazení zaměřené na zařízení musí mít vyšší prioritu než základní nasazení, aby bylo možné použít.

1. Zadejte kladné celé číslo pro **prioritu**nasazení .
1. Zadejte **cílovou podmínku,** která určí, která zařízení budou s tímto nasazením zaměřena.Podmínka je založena na značky dvojčete zařízení nebo dvojče zařízení hlášené vlastnosti a by měla odpovídat formátu výrazu.Příkladem je `tags.environment='test'` nebo `properties.reported.devicemodel='4000x'`.

Vyberte **Další: Kontrola + Vytvořit** pro přechod na poslední krok.

### <a name="step-6-review-and-create"></a>Krok 6: Kontrola a vytvoření

Zkontrolujte informace o nasazení a vyberte **Vytvořit**.

## <a name="monitor-a-deployment"></a>Sledování nasazení

Chcete-li zobrazit podrobnosti o nasazení a sledovat zařízení, která jej spouštějí, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) a přejděte do služby IoT Hub.
1. Vyberte **IoT Edge**.
1. Vyberte kartu **Nasazení IoT Edge.**

   ![Zobrazení nasazení IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Zkontrolujte seznam nasazení.Pro každé nasazení můžete zobrazit následující podrobnosti:
   * **ID** - název nasazení.
   * **Typ** – typ nasazení, **nasazení** nebo **nasazení s vrstvami**.
   * **Cílová podmínka** - značka používaná k definování cílových zařízení.
   * **Priorita** - číslo priority přiřazené nasazení.
   * **Systémové metriky** - **Cílené** určuje počet dvojčat zařízení v centru IoT Hub, které odpovídají podmínku cílení a **Applied** určuje počet zařízení, která měla obsah nasazení použít na jejich dvojčata modulu v centru IoT.
   * **Metriky zařízení** – počet zařízení IoT Edge v nasazení hlásí úspěch nebo chyby z runtime klienta IoT Edge.
   * **Vlastní metriky** – počet zařízení IoT Edge v datech sestav nasazení pro všechny metriky, které jste definovali pro nasazení.
   * **Čas vytvoření** - časové razítko od okamžiku, kdy bylo vytvořeno nasazení. Toto časové razítko se používá k přerušení vazeb, pokud dvě nasazení mají stejnou prioritu.
1. Vyberte nasazení, které chcete sledovat.  
1. Zkontrolujte podrobnosti nasazení. Pomocí karet můžete zkontrolovat podrobnosti o nasazení.

## <a name="modify-a-deployment"></a>Úprava nasazení

Když upravíte nasazení, změny se okamžitě replikují do všech cílených zařízení.

Pokud aktualizujete cílovou podmínku, dojde k následujícím aktualizacím:

* Pokud zařízení nesplnilo starou cílovou podmínku, ale splňuje novou cílovou podmínku a toto nasazení je pro toto zařízení nejvyšší prioritou, bude toto nasazení použito pro zařízení.
* Pokud zařízení, které aktuálně používá toto nasazení, již nesplňuje cílovou podmínku, odinstaluje toto nasazení a převezme další nasazení s nejvyšší prioritou.
* Pokud zařízení aktuálně spuštěné toto nasazení již nesplňuje cílovou podmínku a nesplňuje cílovou podmínku jiných nasazení, nedojde v zařízení k žádné změně. Zařízení pokračuje ve spuštění svých aktuálních modulů v aktuálním stavu, ale již není spravováno jako součást tohoto nasazení. Jakmile splní cílovou podmínku jiného nasazení, odinstaluje toto nasazení a převezme nové.

Chcete-li upravit nasazení, použijte následující kroky:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) a přejděte do služby IoT Hub.
1. Vyberte **IoT Edge**.
1. Vyberte kartu **Nasazení ioT edge.**

   ![Zobrazení nasazení IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Vyberte nasazení, které chcete upravit.
1. Aktualizace na následujících kartách:
   * **Cílová podmínka**
   * **Metriky** – můžete upravit nebo odstranit metriky, které jste definovali, nebo přidat nové.
   * **Popisky**
   * **Moduly**
   * **Trasy**
   * **Nasazení**

1. Vyberte **Uložit**.
1. Postupujte podle kroků v [monitoru nasazení](#monitor-a-deployment) sledovat změny zaváděcí.

## <a name="delete-a-deployment"></a>Odstranění nasazení

Když odstraníte nasazení, všechna nasazená zařízení převezmou další nasazení s nejvyšší prioritou. Pokud vaše zařízení nesplňují cílovou podmínku jiného nasazení, moduly se při odstranění nasazení neodeberou.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) a přejděte do služby IoT Hub.
1. Vyberte **IoT Edge**.
1. Vyberte kartu **Nasazení ioT edge.**

   ![Zobrazení nasazení IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Pomocí zaškrtávacího políčka vyberte nasazení, které chcete odstranit.
1. Vyberte **Odstranit**.
1. Výzva vás bude informovat, že tato akce odstraní toto nasazení a vrátí se do předchozího stavu pro všechna zařízení.To znamená, že bude platit nasazení s nižší prioritou.Pokud není cílem žádné jiné nasazení, nebudou odebrány žádné moduly. Pokud chcete odebrat všechny moduly ze zařízení, vytvořte nasazení s nulovými moduly a nasaďte je do stejných zařízení.Chcete-li pokračovat, vyberte **možnost Ano.**

## <a name="next-steps"></a>Další kroky

Další informace o [nasazení modulů do zařízení IoT Edge](module-deployment-monitoring.md).
