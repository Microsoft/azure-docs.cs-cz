---
title: Vytvořit automatické nasazení z webu Azure portal – Azure IoT Edge | Dokumentace Microsoftu
description: Pomocí webu Azure portal k vytvoření automatického nasazení pro skupiny služby IoT Edge zařízení
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/25/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 20f50e286e30e32f066fe3d214bfc4c1a155776e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53083916"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Nasazení a monitorování modulů IoT Edge ve velkém měřítku pomocí webu Azure portal

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

Azure IoT Edge umožňuje přesunout analytics na hraničních zařízeních a poskytuje cloudu rozhraní, takže můžete spravovat a monitorovat zařízení IoT Edge bez nutnosti fyzického přístupu každé z nich. Možnost vzdáleně spravovat zařízení se nabývá na důležitosti jako řešení Internetu věcí rostou větší a složitější. Azure IoT Edge je navržená pro podporu vašich obchodních cílů, bez ohledu na to, kolik zařízení je přidat.

Můžete spravovat jednotlivá zařízení a nasaďte na ně moduly postupně po jednom. Ale pokud budete chtít provést změny na zařízení ve velkém měřítku, můžete vytvořit **automatického nasazení IoT Edge**, který je součástí Automatická správa zařízení ve službě IoT Hub. Nasazení jsou dynamické procesy, které vám umožní nasadit více modulů najednou pro víc zařízení, sledovat stav a stav modulů a provést změny, pokud je to nezbytné. 

## <a name="identify-devices-using-tags"></a>Identifikace zařízení pomocí značek

Před vytvořením nasazení, budete muset mít k určení zařízení, která chcete ovlivnit. Azure IoT Edge identifikuje zařízení pomocí **značky** ve dvojčeti zařízení. Každé zařízení může mít více značek a můžete je definovat způsobem, který dává smysl pro vaše řešení. Například pokud spravujete areálu Chytré budovy, můžete přidat následující značky k zařízení:

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

1. V [webu Azure portal](https://portal.azure.com), přejděte do služby IoT hub. 
1. Vyberte **IoT Edge**.
1. Vyberte **přidat nasazení IoT Edge**.

Existuje pět kroků pro vytvoření nasazení. V následujících částech se provedou v rámci každé z nich. 

### <a name="step-1-name-and-label"></a>Krok 1: Název a popisek

1. Zadejte jedinečný název, který je malá písmena až 128 vašeho nasazení. Vyhněte se mezery a následující neplatné znaky: `& ^ [ ] { } \ | " < > /`.
1. Přidejte popisky pro sledování vašich nasazení. Popisky jsou **název**, **hodnotu** dvojice, které popisují vaše nasazení. Například `HostPlatform, Linux` nebo `Version, 3.0.1`.
1. Vyberte **Další** přejděte ke kroku 2. 

### <a name="step-2-add-modules-optional"></a>Krok 2: Přidání modulů (volitelné)

Existují dva typy modulů, které přidáte do nasazení. První je modul na základě služby Azure, jako je účet úložiště nebo Stream Analytics. Druhá je modul na základě vlastního kódu. Můžete přidat více modulů jednoho z těchto typů nasazení. 

Pokud vytvoříte nasazení bez modulů, odebere všechny moduly, které aktuální ze zařízení. 

>[!NOTE]
>Azure Machine Learning a Azure Functions nasazení automatizovaných služeb Azure zatím nepodporují. Pomocí nasazení vlastního modulu ručně přidat do vašeho nasazení těchto služeb. 

Přidat modul z Azure Stream Analytics, postupujte podle těchto kroků:
1. V **moduly nasazení** části stránky klikněte na tlačítko **přidat**.
1. Vyberte **modulu Azure Stream Analytics**.
1. Zvolte vaši **předplatné** z rozevírací nabídky.
1. Zvolte vaši **hraniční úloha** z rozevírací nabídky.
1. Vyberte **Uložit** přidáte modul pro nasazení. 

Chcete-li přidat vlastní kód jako modul, nebo chcete-li ručně přidat modul služby Azure, postupujte takto:
1. V **nastavení registru** části stránky, zadejte názvy a přihlašovací údaje pro všechny privátní container registry, které obsahují bitové kopie modulu pro toto nasazení. Edge Agent Chyba 500 oznámí, pokud nemůže najít přihlašovací údaje registru kontejner pro image dockeru.
1. V **moduly nasazení** části stránky klikněte na tlačítko **přidat**.
1. Vyberte **modul IoT Edge**.
1. Zadejte modul **název**.
1. Pro **identifikátor URI Image** zadejte image kontejneru pro modul. 
1. Zadejte libovolné **možnosti vytvoření kontejneru** , který by měly být předány kontejneru. Další informace najdete v tématu [vytvořit docker](https://docs.docker.com/engine/reference/commandline/create/).
1. Pomocí rozevírací nabídky vyberte **zásady restartování**. Zvolte jednu z následujících možností: 
   * **Vždy** – modul vždy restartuje, pokud z nějakého důvodu vypne.
   * **Nikdy** – modul nikdy restartuje, pokud z nějakého důvodu vypne.
   * **Na úspěšná** – modul restartuje, pokud selže, ale ne-li ji vypne čistě. 
   * **Na poškozené** – modul restartuje, pokud dojde k chybě nebo vrátí stav není v pořádku. Záleží jen na každý modul implementovat funkci zdravotní stav. 
1. Pomocí rozevírací nabídky vyberte **požadovaného stavu** pro modul. Zvolte jednu z následujících možností:
   * **Spuštění** – Toto je výchozí možnost. V modulu začne okamžitě po nasazení spustí.
   * **Zastavit** – po nasazení, modul bude zůstat nečinné, dokud se volá při začít tím, že vy nebo jiný modul.
1. Vyberte **povolit** Pokud chcete přidat všechny značky nebo požadované vlastnosti dvojčete modulu. 
1. Zadejte **proměnné prostředí** pro tento modul. Proměnné prostředí obsahují informace dodatek k usnadnění procesu konfigurace modulu.
1. Vyberte **Uložit** přidáte modul pro nasazení. 

Jakmile budete mít všechny moduly pro nasazení nakonfigurované, vyberte **Další** přejděte ke kroku 3.

### <a name="step-3-specify-routes-optional"></a>Krok 3: Určení tras (volitelné)

Trasy definovat, jak moduly navzájem komunikují v rámci nasazení. Ve výchozím nastavení průvodce umožňuje volat trasu **trasy** a definovaná jako **FROM /* do $upstream **, což znamená, že všechny zprávy výstupu tak všechny moduly, které se odesílají do služby IoT hub.  

Přidat nebo aktualizovat trasy s informacemi z [trasy deklarovat](module-composition.md#declare-routes)a pak vyberte **Další** pokračujte k části revize.


### <a name="step-4-target-devices"></a>Krok 4: Cílová zařízení

Můžete cílit na konkrétní zařízení, které by měl obdrží toto nasazení vlastnost značek z vašich zařízení. 

Protože více nasazení mohou být zaměřeny na stejném zařízení, je třeba přiřadit každého nasazení priorita. Pokud někdy dojde ke konfliktu, wins nasazení s nejvyšší prioritou (vyšší hodnoty znamenat vyšší prioritou). Pokud mají dvě nasazení stejnou prioritu číslo, ten, který byl vytvořen většina nedávno wins. 

1. Zadejte kladné celé číslo pro nasazení **Priority**. V případě, že dvě nebo víc nasazení cílí na stejném zařízení, budou platit nasazení s nejvyšší číselnou hodnotou priority.
1. Zadejte **cílová podmínka** k určení zařízení, která budou cílem s tímto nasazením. Podmínka je založen na značky dvojčat zařízení nebo ohlášené vlastnosti dvojčete zařízení a by měl odpovídat formátu výrazu. Například `tags.environment='test'` nebo `properties.reported.devicemodel='4000x'`. 
1. Vyberte **Další** přejít k poslednímu kroku.

### <a name="step-5-review-template"></a>Krok 5: Zkontrolujte šablony

Zkontrolujte informace o nasazení a pak vyberte **odeslat**.

## <a name="monitor-a-deployment"></a>Monitorování nasazení

Chcete-li zobrazit podrobnosti o nasazení a monitorování zařízení, na kterých je spuštěná, použijte následující postup:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do služby IoT hub. 
1. Vyberte **IoT Edge**.
1. Vyberte **nasazení IoT Edge**. 

   ![Zobrazení nasazení IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Zkontrolujte seznam nasazení. U každého nasazení se zobrazí následující podrobnosti:
   * **ID** – název nasazení.
   * **Cílová podmínka** – značka sloužících k definování cílových zařízení.
   * **Priorita** -priority číslo přidružené k nasazení.
   * **Systémové metriky** - **cílové** určuje počet dvojčat zařízení ve službě IoT Hub, která splňují cílovou podmínku a **použito** určuje počet zařízení, která mají použili obsahu nasazení do jejich dvojčaty modulů ve službě IoT Hub. 
   * **Metriky zařízení** – počet hraničních zařízení v nasazení generování sestav úspěchu nebo chyby z modulu runtime IoT Edge klienta.
   * **Čas vytvoření** – časové razítko od vytvoření nasazení. Tímto časovým razítkem se používá pro přerušení vazby, pokud mají dvě nasazení stejnou prioritu. 
2. Vyberte nasazení, které chcete monitorovat.  
3. Zkontrolujte podrobnosti o nasazení. Chcete-li zkontrolovat podrobnosti nasazení můžete použít karty.

## <a name="modify-a-deployment"></a>Upravit nasazení

Při úpravě nasazení změny se okamžitě replikují do všechna cílová zařízení. 

Pokud aktualizujete cílovou podmínku, dojde k následující aktualizace:
* Pokud nesplnilo původní cílovou podmínku zařízení, ale splňuje novou cílovou podmínku a toto nasazení je nejvyšší prioritou pro dané zařízení, se použije toto nasazení do zařízení. 
* Pokud zařízení aktuálně s tímto nasazením již splňuje cílovou podmínku, dojde k odinstalování tohoto nasazení a provede na další nejvyšší prioritu nasazení. 
* Pokud se zařízení aktuálně s tímto nasazením již splňuje cílovou podmínku a nesplňuje cílová podmínka všechna nasazení, pak nedošlo k žádné změně na zařízení. Zařízení pokračuje její aktuální moduly v jejich aktuální stav, ale jako součást tohoto nasazení už nespravuje. Jakmile splňuje cílovou podmínku jakékoli jiné nasazení, dojde k odinstalování tohoto nasazení a provede na novou. 

Pokud chcete upravit nasazení, postupujte následovně: 

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do služby IoT hub. 
1. Vyberte **IoT Edge**.
1. Vyberte **nasazení IoT Edge**. 

   ![Zobrazení nasazení IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Vyberte nasazení, které chcete upravit. 
1. Proveďte aktualizace na následující pole: 
   * Cílová podmínka 
   * Popisky 
   * Priorita 
1. Vyberte **Uložit**.
1. Postupujte podle kroků v [monitorování nasazení](#monitor-a-deployment) sledovat změny zavádět. 

## <a name="delete-a-deployment"></a>Odstranit nasazení

Při odstranění nasazení nějaká zařízení provést další nejvyšší prioritu nasazení. Pokud vaše zařízení nesplňuje cílová podmínka jakékoli jiné nasazení, moduly se neodeberou při nasazení se odstraní. 

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do služby IoT hub. 
1. Vyberte **IoT Edge**.
1. Vyberte **nasazení IoT Edge**. 

   ![Zobrazení nasazení IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Pomocí zaškrtávacího políčka vyberte nasazení, které chcete odstranit. 
1. Vyberte **Odstranit**.
1. Řádku bude informovat, že tato akce bude toto nasazení odstranit a vrátit do předchozího stavu pro všechna zařízení.  To znamená, že se použít nasazení s nižší prioritou.  Pokud se nastaví cílení není žádné další nasazení, odebere se žádné moduly. Pokud chcete odebrat všechny moduly ze zařízení, vytvořit nasazení s nulovou moduly a nasadíte ho do stejného zařízení. Vyberte **Ano** pokračujte. 

## <a name="next-steps"></a>Další postup

Další informace o [moduly nasazení do hraničních zařízení](module-deployment-monitoring.md).
