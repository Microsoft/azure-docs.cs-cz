---
title: Nasazení modulů ve velkém měřítku v Azure Portal-Azure IoT Edge
description: Vytvoření automatických nasazení pro skupiny IoT Edge zařízení pomocí Azure Portal
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/21/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0c1d83c2dac0163cd9b9cbc07969103381e85471
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88855381"
---
# <a name="deploy-iot-edge-modules-at-scale-using-the-azure-portal"></a>Nasazení IoT Edgech modulů ve velkém měřítku pomocí Azure Portal

Vytvořte **IoT Edge automatické nasazení** v Azure Portal, abyste mohli spravovat průběžná nasazení pro mnoho zařízení najednou. Automatická nasazení pro IoT Edge jsou součástí funkce [automatické správy zařízení](/azure/iot-hub/iot-hub-automatic-device-management) v IoT Hub. Nasazení jsou dynamické procesy, které umožňují nasadit více modulů na více zařízení, sledovat stav a stav modulů a v případě potřeby provádět změny.

Další informace najdete v tématu [vysvětlení IoT Edge automatického nasazení pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identifikace zařízení pomocí značek

Než budete moct vytvořit nasazení, musíte být schopni určit, která zařízení mají mít vliv. Azure IoT Edge identifikuje zařízení pomocí **značek** v zařízení s dvojitou signalizací. Každé zařízení může mít několik značek, které můžete definovat jakýmkoli způsobem, který dává smysl pro vaše řešení.

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

Další informace o tom, jaké jsou vlákna a značky zařízení, najdete [v tématu pochopení a používání nevláken zařízení v IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Vytvoření nasazení

IoT Edge poskytuje dva různé typy automatických nasazení, které můžete použít k přizpůsobení scénáře. Můžete vytvořit standardní *nasazení*, které zahrnuje moduly runtime systému a všechny další moduly a trasy. Každé zařízení může použít jenom jedno nasazení. Můžete také vytvořit *vrstvené nasazení*, které zahrnuje pouze vlastní moduly a trasy, nikoli modul runtime systému. Mnoho vrstev nasazení lze v zařízení kombinovat na standardní nasazení. Další informace o tom, jak dva typy automatických nasazení vzájemně spolupracují, najdete v tématu [pochopení IoT Edge automatického nasazení pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

Postup vytvoření nasazení a navrstveného nasazení je velmi podobný. V následujících krocích jsou vyvolány jakékoli rozdíly.

1. V [Azure Portal](https://portal.azure.com)přejdete do IoT Hub.
1. V nabídce v levém podokně vyberte **IoT Edge** v části **Automatická správa zařízení**.
1. Na horním panelu vyberte **vytvořit nasazení** nebo **vytvořit vrstvené nasazení**.

Existuje pět kroků k vytvoření nasazení. V následujících částech si projdete každé z nich.

### <a name="step-1-name-and-label"></a>Krok 1: název a popisek

1. Dejte vašemu nasazení jedinečný název, který bude obsahovat až 128 malých písmen. Vyhněte se mezerám a následujícími neplatnými znaky: `& ^ [ ] { } \ | " < > /` .
1. Můžete přidat popisky jako páry klíč-hodnota, které vám pomůžou sledovat vaše nasazení. Například **HostPlatform** , **Linux**nebo **Version** a **3.0.1**.
1. Vyberte **Další: moduly** , které se mají přesunout na krok 2.

### <a name="step-2-modules"></a>Krok 2: moduly

Do nasazení můžete přidat až 50 modulů. Pokud vytvoříte nasazení bez modulů, odebere všechny aktuální moduly z cílových zařízení.

V nasazeních můžete spravovat nastavení pro IoT Edge agenta a IoT Edge moduly centra. Vyberte **nastavení modulu runtime** a nakonfigurujte dva běhové moduly. V vrstveném nasazení nejsou moduly runtime zahrnuty, takže není možné je nakonfigurovat.

Můžete přidat tři typy modulů:

* Modul IoT Edge
* Modul Marketplace
* Modul Azure Stream Analytics

#### <a name="add-an-iot-edge-module"></a>Přidat modul IoT Edge

Pokud chcete přidat vlastní kód jako modul nebo ručně přidat modul služby Azure, postupujte takto:

1. V části **Container Registry přihlašovací údaje** na stránce zadejte názvy a přihlašovací údaje pro všechny Registry privátních kontejnerů, které obsahují image modulů pro toto nasazení. Agent IoT Edge ohlásí chybu 500, pokud nemůže najít přihlašovací údaje registru kontejneru pro bitovou kopii Docker.
1. V části **IoT Edge moduly** na stránce klikněte na **Přidat**.
1. V rozevírací nabídce vyberte **modul IoT Edge** .
1. Dejte modulu **IoT Edge název modulu**.
1. Do pole **identifikátor URI image** zadejte image kontejneru pro váš modul.
1. Pomocí rozevírací nabídky vyberte **zásadu restartování**. Vybírat můžete z těchto možností:
   * **Always** – modul se vždy restartuje, pokud se z nějakého důvodu vypíná.
   * **nikdy** – modul se nikdy nerestartuje, pokud se z nějakého důvodu vypíná.
   * **při selhání** – modul se restartuje, pokud dojde k chybě, ale ne v případě, že se vypíná čistě.
   * stav není **v pořádku** – modul se restartuje, pokud dojde k chybě, nebo vrátí stav není v pořádku. Pro implementaci funkce stavu je to pro každý modul.
1. Pomocí rozevírací nabídky vyberte **požadovaný stav** pro modul. Vybírat můžete z těchto možností:
   * **spuštění** je výchozí možnost. Modul se spustí hned po nasazení.
   * **Zastaveno** – po nasazení přestane modul zůstat nečinný, dokud se nespustí a nezačne vámi nebo jiným modulem.
1. Zadejte všechny **možnosti vytvoření kontejneru** , které by měly být předány do kontejneru. Další informace najdete v tématu [Docker Create](https://docs.docker.com/engine/reference/commandline/create/).
1. Pokud chcete přidat značky nebo jiné vlastnosti do modulu s dvojitou výjimkou, vyberte možnost **Nastavení vláken modulu** .
1. Zadejte **proměnné prostředí** pro tento modul. Proměnné prostředí poskytují informace o konfiguraci modulu.
1. Vyberte **Přidat** a přidejte do nasazení modul.

#### <a name="add-a-module-from-the-marketplace"></a>Přidání modulu z Marketplace

Chcete-li přidat modul z Azure Marketplace, postupujte podle následujících kroků:

1. V části **IoT Edge moduly** na stránce klikněte na **Přidat**.
1. Z rozevírací nabídky vyberte **modul Marketplace** .
1. Vyberte modul na stránce **IoT Edge modulu pro Marketplace** . Vybraný modul se automaticky nakonfiguruje pro vaše předplatné, skupinu prostředků a zařízení. Pak se zobrazí v seznamu IoT Edge moduly. Některé moduly můžou vyžadovat další konfiguraci. Další informace najdete v tématu [nasazení modulů z Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace).

#### <a name="add-a-stream-analytics-module"></a>Přidat modul Stream Analytics

Chcete-li přidat modul z Azure Stream Analytics, postupujte podle následujících kroků:

1. V části **IoT Edge moduly** na stránce klikněte na **Přidat**.
1. V rozevírací nabídce vyberte **modul Azure Stream Analytics** .
1. V pravém podokně vyberte své **předplatné**.
1. Vyberte svoji **úlohu IoT Edge**.
1. Vyberte **Uložit** a přidejte do nasazení modul.

#### <a name="configure-module-settings"></a>Konfigurovat nastavení modulu

Po přidání modulu do nasazení můžete vybrat jeho název a otevřít tak stránku **aktualizovat IoT Edge modul** . Na této stránce můžete upravit nastavení modulu, proměnné prostředí, možnosti vytváření a vlákna v modulu. Pokud jste přidali modul z webu Marketplace, může již mít některé z těchto parametrů vyplněno.

Pokud vytváříte vrstvené nasazení, můžete nakonfigurovat modul, který existuje v jiných nasazeních, která cílí na stejná zařízení. Chcete-li aktualizovat modul s dvojím zápisem bez přepsání jiných verzí, otevřete kartu **nastavení s dvojitými možnostmi modulu** . Vytvoří novou **vlastnost s dvojitou** přesností modulu s jedinečným názvem pro dílčí oddíl v rámci požadovaných vlastností, například v modulu `properties.desired.settings` . Pokud definujete vlastnosti v rámci pouze `properties.desired` pole, přepíše se požadované vlastnosti pro modul definovaný v libovolném nasazení s nižší prioritou.

![Nastavit vlastnost s dvojitým modulem pro vrstvené nasazení](./media/how-to-deploy-monitor/module-twin-property.png)

Další informace o konfiguraci zdvojeného modulu v vrstveném nasazení najdete v tématu [vrstvené nasazení](module-deployment-monitoring.md#layered-deployment).

Jakmile budete mít nakonfigurované všechny moduly pro nasazení, vyberte **Další: trasy** , které chcete přesunout na krok 3.

### <a name="step-3-routes"></a>Krok 3: trasy

Trasy definují, jak vzájemně komunikují moduly v rámci nasazení. Průvodce ve výchozím nastavení poskytuje trasu **s názvem** **/Messages/ \* do $upstream**, což znamená, že do služby IoT Hub budou odesílány výstupy všech zpráv pomocí libovolných modulů.  

Přidejte nebo aktualizujte trasy s informacemi z [deklarace směrování](module-composition.md#declare-routes)a pak výběrem **Další** pokračujte do části přezkoumání.

Vyberte **Další: metriky**.

### <a name="step-4-metrics"></a>Krok 4: metriky

Metriky poskytují souhrnné počty různých stavů, které může zařízení nahlásit zpět v důsledku použití konfiguračního obsahu.

1. Zadejte název **metriky**.

1. Zadejte dotaz na **kritéria metriky**. Dotaz vychází z IoT Edge [nahlášených vlastností](module-edgeagent-edgehub.md#edgehub-reported-properties)modulu centra rozbočovače. Metrika představuje počet řádků vrácených dotazem.

   Například:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

Vyberte **Další: cílová zařízení**.

### <a name="step-5-target-devices"></a>Krok 5: cílová zařízení

Pomocí vlastnosti značky ze svých zařízení můžete cílit na konkrétní zařízení, která by měla přijmout toto nasazení.

Vzhledem k tomu, že více nasazení může cílit na stejné zařízení, měli byste každému nasazení přidělit prioritní číslo. Pokud dojde ke konfliktu, nasazení s nejvyšší prioritou (větší hodnoty označuje vyšší prioritu) WINS. Pokud má dvě nasazení stejné číslo priority, ten, který byl vytvořen naposledy službou WINS.

Pokud je pro stejné zařízení cíleno více nasazení, použije se pouze ta, která má vyšší prioritu. Pokud je pro stejné zařízení cíleno více vrstev nasazení, jsou všechny aplikovány. Pokud jsou však některé vlastnosti duplikovány, například pokud existují dvě trasy se stejným názvem, pak jedna z vrstev nasazení s vyšší prioritou přepíše zbytek.

Všechna vrstvená nasazení, která cílí na zařízení, musí mít vyšší prioritu než základní nasazení, aby se daly použít.

1. Zadejte kladné celé číslo pro **prioritu**nasazení.
1. Zadejte **cílovou podmínku** pro určení, která zařízení budou cílem tohoto nasazení.Podmínka je založena na nevyhovujících značkách zařízení nebo na dohlášených vlastnostech zařízení, které se musí shodovat s formátem výrazu.Příkladem je `tags.environment='test'` nebo `properties.reported.devicemodel='4000x'`.

Vyberte **Další: zkontrolovat + vytvořit** a přejděte k poslednímu kroku.

### <a name="step-6-review-and-create"></a>Krok 6: Kontrola a vytvoření

Zkontrolujte informace o svém nasazení a pak vyberte **vytvořit**.

Informace o monitorování nasazení najdete v tématu [monitorování nasazení IoT Edge](how-to-monitor-iot-edge-deployments.md).

## <a name="modify-a-deployment"></a>Úprava nasazení

Když upravíte nasazení, změny se okamžitě replikují na všechna cílová zařízení. Pro existující nasazení můžete upravit následující nastavení a funkce:

* Cílové podmínky
* Vlastní metriky
* Popisky
* Značky
* Požadované vlastnosti

### <a name="modify-target-conditions-custom-metrics-and-labels"></a>Upravit cílové podmínky, vlastní metriky a popisky

1. Ve službě IoT Hub vyberte v nabídce vlevo podokno **IoT Edge** .
1. Vyberte kartu **nasazení IoT Edge** a pak vyberte nasazení, které chcete nakonfigurovat.
1. Vyberte kartu **Podmínka cíle** . Změňte **cílovou podmínku** pro cílení na zamýšlená zařízení. Můžete také nastavit **prioritu**.  Vyberte **Uložit**.

    Pokud aktualizujete cílovou podmínku, dojde k následujícím aktualizacím:

    * Pokud zařízení nesplňuje starou cílovou podmínku, ale splňuje novou cílovou podmínku a toto nasazení je nejvyšší prioritou pro toto zařízení, pak se toto nasazení aplikuje na zařízení.
    * Pokud zařízení aktuálně spuštěné v tomto nasazení už nesplňuje cílovou podmínku, odinstaluje toto nasazení a provede nasazení s další nejvyšší prioritou.
    * Pokud zařízení aktuálně spuštěné toto nasazení už nesplňuje cílovou podmínku a nesplňuje cílovou podmínku žádného jiného nasazení, neproběhne na zařízení žádná změna. Zařízení pokračuje v běhu svých aktuálních modulů v aktuálním stavu, ale už se nespravuje jako součást tohoto nasazení. Jakmile splňuje cílovou podmínku jakéhokoli jiného nasazení, odinstaluje toto nasazení a provede nový.

1. Vyberte kartu **metriky** a klikněte na tlačítko **Upravit metriky** . Přidejte nebo upravte vlastní metriky pomocí příkladu syntaxe jako průvodce. Vyberte **Uložit**.

    ![Úprava vlastních metrik v nasazení](./media/how-to-deploy-monitor/metric-list.png)

1. Vyberte kartu **popisky** a proveďte požadované změny a vyberte **Uložit**.

## <a name="delete-a-deployment"></a>Odstranění nasazení

Při odstranění nasazení převezmou všechna nasazená zařízení nasazení s další nejvyšší prioritou. Pokud zařízení nesplňuje cílovou podmínku jakéhokoli jiného nasazení, moduly se po odstranění nasazení neodeberou.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte k IoT Hub.
1. Vyberte **IoT Edge**.
1. Vyberte kartu **nasazení IoT Edge** .

   ![Zobrazit IoT Edge nasazení](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Pomocí zaškrtávacího políčka vyberte nasazení, které chcete odstranit.
1. Vyberte **Odstranit**.
1. Výzva vás upozorní, že tato akce odstraní toto nasazení a vrátí se k předchozímu stavu pro všechna zařízení.Bude použito nasazení s nižší prioritou.Pokud není zacíleno žádné jiné nasazení, nebudou odebrány žádné moduly. Pokud chcete odebrat všechny moduly ze zařízení, vytvořte nasazení s nulovými moduly a nasaďte je do stejného zařízení.Vyberte **Ano**, abyste mohli pokračovat.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [nasazení modulů do zařízení IoT Edge](module-deployment-monitoring.md).
