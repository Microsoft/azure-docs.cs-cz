---
title: Nasazení modulů ve velkém měřítku v Azure Portal-Azure IoT Edge
description: Vytvoření automatických nasazení pro skupiny IoT Edge zařízení pomocí Azure Portal
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9d03b6f4a512c22564480405ec0f0e0c0e62a958
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048419"
---
# <a name="deploy-iot-edge-modules-at-scale-using-the-azure-portal"></a>Nasazení IoT Edgech modulů ve velkém měřítku pomocí Azure Portal

Vytvořte **IoT Edge automatické nasazení** v Azure Portal, abyste mohli spravovat průběžná nasazení pro mnoho zařízení najednou. Automatická nasazení pro IoT Edge jsou součástí funkce [automatické správy zařízení](../iot-hub/iot-hub-automatic-device-management.md) v IoT Hub. Nasazení jsou dynamické procesy, které umožňují nasadit více modulů na více zařízení, sledovat stav a stav modulů a v případě potřeby provádět změny.

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

>[!NOTE]
>Kroky v tomto článku odrážejí nejnovější verzi schématu IoT Edge agenta a centra. Verze schématu 1,1 byla vydána společně s IoT Edge verze 1.0.10 a umožňuje funkce pořadí spouštění a stanovení priorit směrování.
>
>Pokud nasazujete na zařízení se spuštěnou verzí 1.0.9 nebo starší, upravte **nastavení modulu runtime** v kroku průvodce **moduly** na použití schématu verze 1,0.

### <a name="step-1-name-and-label"></a>Krok 1: název a popisek

1. Dejte vašemu nasazení jedinečný název, který bude obsahovat až 128 malých písmen. Vyhněte se mezerám a následujícími neplatnými znaky: `& ^ [ ] { } \ | " < > /` .
1. Můžete přidat popisky jako páry klíč-hodnota, které vám pomůžou sledovat vaše nasazení. Například **HostPlatform** , **Linux**nebo **Version** a **3.0.1**.
1. Vyberte **Další: moduly** , které se mají přesunout na krok 2.

### <a name="step-2-modules"></a>Krok 2: moduly

Do nasazení můžete přidat až 50 modulů. Pokud vytvoříte nasazení bez modulů, odebere všechny aktuální moduly z cílových zařízení.

V nasazeních můžete spravovat nastavení pro IoT Edge agenta a IoT Edge moduly centra. Vyberte **nastavení modulu runtime** a nakonfigurujte dva běhové moduly. V vrstveném nasazení nejsou moduly runtime zahrnuty, takže není možné je nakonfigurovat.

Pokud chcete přidat vlastní kód jako modul nebo ručně přidat modul služby Azure, postupujte takto:

1. V části **nastavení Container Registry** na stránce zadejte přihlašovací údaje pro přístup k jakýmkoli soukromým kontejnerům, které obsahují image modulu.
1. V části **IoT Edge moduly** na stránce vyberte **Přidat**.
1. Z rozevírací nabídky vyberte jeden ze tří typů modulů:

   * **IoT Edge modul** – zadejte název modulu a identifikátor URI image kontejneru. Například identifikátor URI image pro vzorový modul SimulatedTemperatureSensor je `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0` . Pokud je image modulu uložená v soukromém registru kontejnerů, přidejte přihlašovací údaje na této stránce pro přístup k imagi.
   * **Modul Marketplace** – moduly hostované ve Azure Marketplace. Některé moduly Marketplace vyžadují další konfiguraci, proto si Projděte podrobnosti o modulu v seznamu [Azure Marketplace IoT Edge moduly](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) .
   * **Azure Stream Analytics modul** – moduly generované z úlohy Azure Stream Analytics.

1. V případě potřeby zopakujte kroky 2 a 3 a přidejte do svého nasazení další moduly.

Po přidání modulu do nasazení můžete vybrat jeho název a otevřít tak stránku **aktualizovat IoT Edge modul** . Na této stránce můžete upravit nastavení modulů, proměnné prostředí, možnosti vytváření, pořadí spouštění a nevlákenný modul. Pokud jste přidali modul z webu Marketplace, může již mít některé z těchto parametrů vyplněno. Další informace o dostupných nastaveních modulů najdete v tématu [Konfigurace a Správa modulů](module-composition.md#module-configuration-and-management).

Pokud vytváříte vrstvené nasazení, můžete nakonfigurovat modul, který existuje v jiných nasazeních, která cílí na stejná zařízení. Chcete-li aktualizovat modul s dvojím zápisem bez přepsání jiných verzí, otevřete kartu **nastavení s dvojitými možnostmi modulu** . Vytvoří novou **vlastnost s dvojitou** přesností modulu s jedinečným názvem pro dílčí oddíl v rámci požadovaných vlastností, například v modulu `properties.desired.settings` . Pokud definujete vlastnosti v rámci pouze `properties.desired` pole, přepíše se požadované vlastnosti pro modul definovaný v libovolném nasazení s nižší prioritou.

![Nastavit vlastnost s dvojitým modulem pro vrstvené nasazení](./media/how-to-deploy-monitor/module-twin-property.png)

Další informace o konfiguraci zdvojeného modulu v vrstveném nasazení najdete v tématu [vrstvené nasazení](module-deployment-monitoring.md#layered-deployment).

Jakmile budete mít nakonfigurované všechny moduly pro nasazení, vyberte **Další: trasy** , které chcete přesunout na krok 3.

### <a name="step-3-routes"></a>Krok 3: trasy

Na kartě **trasy** definujete, jak jsou zprávy předávány mezi moduly a IoT Hub. Zprávy se vytvářejí pomocí párů název/hodnota.

Například trasa s názvem **trasa** a hodnotou **z/Messages/ \* do $upstream** by mohla přijmout výstup všech zpráv libovolnými moduly a odeslat je do služby IoT Hub.  

Parametry **priority** a **Time to Live** jsou volitelné parametry, které můžete zahrnout do definice trasy. Parametr priority vám umožní vybrat, které trasy mají své zprávy zpracovat jako první, nebo které trasy by se měly zpracovat jako poslední. Priorita je určena nastavením čísla 0-9, kde 0 je nejvyšší priorita. Parametr time to Live umožňuje deklarovat, jak dlouho mají být zprávy v této trase uchovávány, dokud je nezpracujete nebo neodeberete z fronty.

Další informace o tom, jak vytvořit trasy, naleznete v tématu [Declare Routes](module-composition.md#declare-routes).

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