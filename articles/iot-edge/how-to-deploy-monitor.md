---
title: Nasazení modulů ve velkém měřítku v Azure Portal-Azure IoT Edge
description: Pomocí webu Azure portal k vytvoření automatického nasazení pro skupiny služby IoT Edge zařízení
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1e15f237bddd586f81c3b04483111f7e211bfb10
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563407"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Nasazení a monitorování modulů IoT Edge ve velkém měřítku pomocí webu Azure portal

Vytvořte **IoT Edge automatické nasazení** v Azure Portal, abyste mohli spravovat průběžná nasazení pro mnoho zařízení najednou. Automatická nasazení pro IoT Edge jsou součástí funkce [automatické správy zařízení](/azure/iot-hub/iot-hub-automatic-device-management) v IoT Hub. Nasazení jsou dynamické procesy, které umožňují nasadit více modulů na více zařízení, sledovat stav a stav modulů a v případě potřeby provádět změny.

Další informace najdete v tématu [vysvětlení IoT Edge automatického nasazení pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identifikace zařízení pomocí značek

Před vytvořením nasazení, budete muset mít k určení zařízení, která chcete ovlivnit. Azure IoT Edge identifikuje zařízení pomocí **značky** ve dvojčeti zařízení. Každé zařízení může mít několik značek, které můžete definovat jakýmkoli způsobem, který dává smysl pro vaše řešení. 

Pokud například spravujete areály inteligentních budov, můžete přidat umístění, typ místnosti a značky prostředí do zařízení:

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

Další informace o značky a dvojčata zařízení, najdete v části [principy a použití dvojčat zařízení ve službě IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Vytvoření nasazení

IoT Edge poskytuje dva různé typy automatických nasazení, které můžete použít k přizpůsobení scénáře. Můžete vytvořit standardní *nasazení*, které zahrnuje moduly runtime systému a všechny další moduly a trasy. Každé zařízení může použít jenom jedno nasazení. Můžete také vytvořit *vrstvené nasazení*, které zahrnuje pouze vlastní moduly a trasy, nikoli modul runtime systému. Mnoho vrstev nasazení lze v zařízení kombinovat na standardní nasazení. Další informace o tom, jak dva typy automatických nasazení vzájemně spolupracují, najdete v tématu [pochopení IoT Edge automatického nasazení pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

Postup vytvoření nasazení a navrstveného nasazení je velmi podobný. V následujících krocích jsou vyvolány jakékoli rozdíly.

1. V [Azure Portal](https://portal.azure.com)přejdete do IoT Hub.
1. V nabídce v levém podokně vyberte **IoT Edge** v části **Automatická správa zařízení**.
1. Na horním panelu vyberte **vytvořit nasazení** nebo **vytvořit vrstvené nasazení**.

Existuje pět kroků pro vytvoření nasazení. V následujících částech se provedou v rámci každé z nich.

### <a name="step-1-name-and-label"></a>Krok 1: název a popisek

1. Zadejte jedinečný název, který je malá písmena až 128 vašeho nasazení. Vyhněte se mezery a následující neplatné znaky: `& ^ [ ] { } \ | " < > /`.
1. Můžete přidat popisky jako páry klíč-hodnota, které vám pomůžou sledovat vaše nasazení. Například **HostPlatform** , **Linux**nebo **Version** a **3.0.1**.
1. Vyberte **Další: moduly** , které se mají přesunout na krok 2.

### <a name="step-2-modules"></a>Krok 2: moduly

Do nasazení můžete přidat až 20 modulů. Pokud vytvoříte nasazení bez modulů, odebere všechny aktuální moduly z cílových zařízení.

V nasazeních můžete spravovat nastavení pro IoT Edge agenta a IoT Edge moduly centra. Vyberte **nastavení modulu runtime** a nakonfigurujte dva běhové moduly. V vrstveném nasazení nejsou moduly runtime zahrnuty, takže není možné je nakonfigurovat. 

Můžete přidat tři typy modulů:

* Modul IoT Edge
* Modul Marketplace
* Modul Azure Stream Analytics

#### <a name="add-an-iot-edge-module"></a>Přidat modul IoT Edge

Chcete-li přidat vlastní kód jako modul, nebo chcete-li ručně přidat modul služby Azure, postupujte takto:

1. V části **Container Registry přihlašovací údaje** na stránce zadejte názvy a přihlašovací údaje pro všechny Registry privátních kontejnerů, které obsahují image modulů pro toto nasazení. Agent IoT Edge ohlásí chybu 500, pokud nemůže najít přihlašovací údaje registru kontejneru pro bitovou kopii Docker.
1. V části **IoT Edge moduly** na stránce klikněte na **Přidat**.
1. V rozevírací nabídce vyberte **modul IoT Edge** .
1. Dejte modulu **IoT Edge název modulu**.
1. Pro **identifikátor URI Image** zadejte image kontejneru pro modul.
1. Pomocí rozevírací nabídky vyberte **zásady restartování**. Zvolte jednu z následujících možností:
   * **Always** – modul se vždy restartuje, pokud se z nějakého důvodu vypíná.
   * **nikdy** – modul se nikdy nerestartuje, pokud se z nějakého důvodu vypíná.
   * **při selhání** – modul se restartuje, pokud dojde k chybě, ale ne v případě, že se vypíná čistě. 
   * stav není **v pořádku** – modul se restartuje, pokud dojde k chybě, nebo vrátí stav není v pořádku. Záleží jen na každý modul implementovat funkci zdravotní stav. 
1. Pomocí rozevírací nabídky vyberte **požadovaného stavu** pro modul. Zvolte jednu z následujících možností:
   * **spuštění** je výchozí možnost. V modulu začne okamžitě po nasazení spustí.
   * **Zastaveno** – po nasazení přestane modul zůstat nečinný, dokud se nespustí a nezačne vámi nebo jiným modulem.
1. Zadejte libovolné **možnosti vytvoření kontejneru** , který by měly být předány kontejneru. Další informace najdete v tématu [vytvořit docker](https://docs.docker.com/engine/reference/commandline/create/).
1. Pokud chcete přidat značky nebo jiné vlastnosti do modulu s dvojitou výjimkou, vyberte možnost **Nastavení vláken modulu** .
1. Zadejte **proměnné prostředí** pro tento modul. Proměnné prostředí poskytují informace o konfiguraci modulu.
1. Vyberte **Přidat** a přidejte do nasazení modul.

#### <a name="add-a-module-from-the-marketplace"></a>Přidání modulu z Marketplace

Chcete-li přidat modul z Azure Marketplace, postupujte podle následujících kroků:

1. V části **IoT Edge moduly** na stránce klikněte na **Přidat**.
1. Z rozevírací nabídky vyberte **modul Marketplace** .
1. Vyberte modul na stránce **IoT Edge modulu pro Marketplace** . Vybraný modul se automaticky nakonfiguruje pro vaše předplatné, skupinu prostředků a zařízení. Pak se zobrazí v seznamu IoT Edge moduly. Některé moduly můžou vyžadovat další konfiguraci. Další informace najdete v tématu [nasazení modulů z webu Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace).

#### <a name="add-a-stream-analytics-module"></a>Přidat modul Stream Analytics

Přidat modul z Azure Stream Analytics, postupujte podle těchto kroků:

1. V části **IoT Edge moduly** na stránce klikněte na **Přidat**.
1. V rozevírací nabídce vyberte **modul Azure Stream Analytics** .
1. V pravém podokně vyberte své **předplatné**.
1. Vyberte svoji **úlohu IoT Edge**.
1. Vyberte **Uložit** přidáte modul pro nasazení.

#### <a name="configure-module-settings"></a>Konfigurovat nastavení modulu

Po přidání modulu do nasazení můžete vybrat jeho název a otevřít tak stránku **aktualizovat IoT Edge modul** . Na této stránce můžete upravit nastavení modulu, proměnné prostředí, možnosti vytváření a vlákna v modulu. Pokud jste přidali modul z webu Marketplace, může již mít některé z těchto parametrů vyplněno. 

Pokud vytváříte vrstvené nasazení, můžete nakonfigurovat modul, který existuje v jiných nasazeních, která cílí na stejná zařízení. Pokud chcete modul bez přepsání jiných verzí aktualizovat, otevřete kartu nastavení s **dvojitou** přesností. Vytvořte novou **vlastnost s dvojitou** přesností modulu s jedinečným názvem pro dílčí oddíl v rámci požadovaných vlastností vlákna, například `properties.desired.settings`. Definujete-li vlastnosti pouze v poli `properties.desired`, přepíší se požadované vlastnosti pro modul definovaný v libovolném nasazení s nižší prioritou. 

![Nastavit vlastnost s dvojitým modulem pro vrstvené nasazení](./media/how-to-deploy-monitor/module-twin-property.png)

Další informace o konfiguraci zdvojeného modulu v vrstveném nasazení najdete v tématu [vrstvené nasazení](module-deployment-monitoring.md#layered-deployment).

Jakmile budete mít nakonfigurované všechny moduly pro nasazení, vyberte **Další: trasy** , které chcete přesunout na krok 3.

### <a name="step-3-routes"></a>Krok 3: trasy

Trasy definovat, jak moduly navzájem komunikují v rámci nasazení. Ve výchozím nastavení vám průvodce poskytne trasu s **názvem** **/messages/\* do $upstream**, což znamená, že všechny zprávy ve službě IoT Hub budou odesílány do výstupů.  

Přidat nebo aktualizovat trasy s informacemi z [trasy deklarovat](module-composition.md#declare-routes)a pak vyberte **Další** pokračujte k části revize.

Vyberte **Další: metriky**.

### <a name="step-4-metrics"></a>Krok 4: metriky

Metriky poskytují souhrnné počty různých stavů, které může zařízení nahlásit zpět v důsledku použití konfiguračního obsahu.

1. Zadejte název **metriky**.

1. Zadejte dotaz na **kritéria metriky**. Dotaz vychází z IoT Edge [nahlášených vlastností](module-edgeagent-edgehub.md#edgehub-reported-properties)modulu centra rozbočovače. Metrika představuje počet řádků vrácených dotazem.

   Příklad:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Vyberte **Další: cílová zařízení**.

### <a name="step-5-target-devices"></a>Krok 5: cílová zařízení

Můžete cílit na konkrétní zařízení, které by měl obdrží toto nasazení vlastnost značek z vašich zařízení.

Protože více nasazení mohou být zaměřeny na stejném zařízení, je třeba přiřadit každého nasazení priorita. Pokud dojde ke konfliktu, nasazení s nejvyšší prioritou (větší hodnoty označuje vyšší prioritu) WINS. Pokud mají dvě nasazení stejnou prioritu číslo, ten, který byl vytvořen většina nedávno wins.

Pokud je pro stejné zařízení cíleno více nasazení, bude použito pouze jedno s vyšší prioritou. Pokud je pro stejné zařízení cíleno více vrstev nasazení, jsou všechny aplikovány. Pokud jsou však některé vlastnosti duplikovány, například pokud existují dvě trasy se stejným názvem, pak jedna z vrstev nasazení s vyšší prioritou přepíše zbytek. 

Všechna vrstvená nasazení, která cílí na zařízení, musí mít vyšší prioritu než základní nasazení, aby se daly použít. 

1. Zadejte kladné celé číslo pro nasazení **Priority**.
1. Zadejte **cílová podmínka** k určení zařízení, která budou cílem s tímto nasazením. Podmínka je založena na nevyhovujících značkách zařízení nebo na dohlášených vlastnostech zařízení, které se musí shodovat s formátem výrazu. Například `tags.environment='test'` nebo `properties.reported.devicemodel='4000x'`.

Vyberte **Další: zkontrolovat + vytvořit** a přejděte k poslednímu kroku.

### <a name="step-6-review-and-create"></a>Krok 6: Kontrola a vytvoření

Zkontrolujte informace o svém nasazení a pak vyberte **vytvořit**.

## <a name="monitor-a-deployment"></a>Monitorování nasazení

Chcete-li zobrazit podrobnosti o nasazení a monitorování zařízení, na kterých je spuštěná, použijte následující postup:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte k IoT Hub.
1. Vyberte **IoT Edge**.
1. Vyberte kartu **nasazení IoT Edge** .

   ![Zobrazení nasazení IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Zkontrolujte seznam nasazení. Pro každé nasazení si můžete zobrazit následující podrobnosti:
   * **ID** – název nasazení.
   * **Typ** – **typ nasazení, nasazení nebo nasazení** v **vrstveném**prostředí. 
   * **Podmínka cíle** – značka používaná k definování cílových zařízení.
   * **Priorita** -priority číslo přidružené k nasazení.
   * **Systémové metriky** - **cílové** určuje počet dvojčat zařízení ve službě IoT Hub, která splňují cílovou podmínku a **použito** určuje počet zařízení, která mají použili obsahu nasazení do jejich dvojčaty modulů ve službě IoT Hub.
   * **Metriky zařízení** – počet IoT Edge zařízení v průběhu generování sestav o úspěchu nebo chybách z IoT Edge modulu runtime klienta.
   * **Vlastní metriky** – počet IoT Edgech zařízení v datech generování sestav nasazení pro jakékoli metriky, které jste definovali pro nasazení.
   * **Čas vytvoření** – časové razítko z doby, kdy bylo vytvořeno nasazení. Tímto časovým razítkem se používá pro přerušení vazby, pokud mají dvě nasazení stejnou prioritu.
1. Vyberte nasazení, které chcete monitorovat.  
1. Zkontrolujte podrobnosti o nasazení. Chcete-li zkontrolovat podrobnosti nasazení můžete použít karty.

## <a name="modify-a-deployment"></a>Upravit nasazení

Při úpravě nasazení změny se okamžitě replikují do všechna cílová zařízení.

Pokud aktualizujete cílovou podmínku, dojde k následující aktualizace:

* Pokud nesplnilo původní cílovou podmínku zařízení, ale splňuje novou cílovou podmínku a toto nasazení je nejvyšší prioritou pro dané zařízení, se použije toto nasazení do zařízení.
* Pokud zařízení aktuálně s tímto nasazením již splňuje cílovou podmínku, dojde k odinstalování tohoto nasazení a provede na další nejvyšší prioritu nasazení.
* Pokud se zařízení aktuálně s tímto nasazením již splňuje cílovou podmínku a nesplňuje cílová podmínka všechna nasazení, pak nedošlo k žádné změně na zařízení. Zařízení pokračuje její aktuální moduly v jejich aktuální stav, ale jako součást tohoto nasazení už nespravuje. Jakmile splňuje cílovou podmínku jakékoli jiné nasazení, dojde k odinstalování tohoto nasazení a provede na novou.

Pokud chcete upravit nasazení, postupujte následovně:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte k IoT Hub.
1. Vyberte **IoT Edge**.
1. Vyberte kartu **nasazení IoT Edge** .

   ![Zobrazení nasazení IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Vyberte nasazení, které chcete upravit.
1. Proveďte aktualizace na následujících kartách:
   * **Cílová podmínka**
   * **Metriky** – můžete upravit nebo odstranit metriky, které jste definovali, nebo přidat nové.
   * **Popisky**
   * **Moduly**
   * **Trasy**
   * **Nasazení**

1. Vyberte **Uložit**.
1. Postupujte podle kroků v [monitorování nasazení](#monitor-a-deployment) sledovat změny zavádět.

## <a name="delete-a-deployment"></a>Odstranit nasazení

Při odstranění nasazení převezmou všechna nasazená zařízení nasazení s další nejvyšší prioritou. Pokud vaše zařízení nesplňuje cílová podmínka jakékoli jiné nasazení, moduly se neodeberou při nasazení se odstraní.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte k IoT Hub.
1. Vyberte **IoT Edge**.
1. Vyberte kartu **nasazení IoT Edge** .

   ![Zobrazení nasazení IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Pomocí zaškrtávacího políčka vyberte nasazení, které chcete odstranit.
1. Vyberte **Odstranit**.
1. Řádku bude informovat, že tato akce bude toto nasazení odstranit a vrátit do předchozího stavu pro všechna zařízení.  To znamená, že bude platit nasazení s nižší prioritou. Pokud není zacíleno žádné jiné nasazení, nebudou odebrány žádné moduly. Pokud chcete odebrat všechny moduly ze zařízení, vytvořit nasazení s nulovou moduly a nasadíte ho do stejného zařízení. Pokračujte výběrem **Ano** .

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [nasazení modulů do zařízení IoT Edge](module-deployment-monitoring.md).
