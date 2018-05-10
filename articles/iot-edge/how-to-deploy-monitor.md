---
title: Nasazení, monitorování modulů pro hraniční Azure IoT | Microsoft Docs
description: Správa modulů, které se spouštějí na hraniční zařízení
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/07/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 6d024dfdd661d6bebe7d163b96659d6e169cc5cc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale---preview"></a>Nasazení a monitorování modulů IoT Edge ve velkém měřítku – náhled

Azure IoT Edge umožňuje přesunout analytics na okraj a poskytuje rozhraní, které je cloudu, takže můžete spravovat a monitorovat vaši IoT hraniční zařízení bez nutnosti fyzicky přístup každé z nich. Možnost vzdáleně spravovat zařízení je velmi důležité jako řešení Internetu věcí stále roste větší a složitější. Azure IoT okraj je navržen pro podporu obchodních cílů, bez ohledu na to, kolik zařízení přidáte.

Můžete spravovat jednotlivých zařízení a nasadit modulů na jeden po druhém. Ale pokud chcete provést změny zařízení ve velkém měřítku, můžete vytvořit **IoT okraj automatického nasazení**, který je součástí Automatická správa zařízení IoT hub. Nasazení jsou dynamické procesy, které vám umožní nasadit více modulů do více zařízení najednou, sledovat stav a stav moduly a provést změny podle potřeby. 

## <a name="identify-devices-using-tags"></a>Identifikovat zařízení pomocí značek

Před vytvořením nasazení, budete muset určit zařízení, která chcete ovlivnit. Azure IoT Edge identifikuje zařízení pomocí **značky** v dvojče zařízení. Každé zařízení může mít více značek a jejich můžete definovat způsobem, který dává smysl pro vaše řešení. Například pokud spravujete kanceláře inteligentní budov, můžete přidat těmito značkami k zařízení:

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

Další informace o značky a dvojčata zařízení najdete v tématu [Rady pro pochopení a použití dvojčata zařízení IoT hub][lnk-device-twin].

## <a name="create-a-deployment"></a>Vytvoření nasazení

1. V [portál Azure][lnk-portal], přejděte do služby IoT hub. 
1. Vyberte **IoT okraj (preview)**.
1. Vyberte **přidání okraj IoT nasazení**.

Existují pět kroky k vytvoření nasazení. V následujících částech provede každé z nich. 

### <a name="step-1-name-and-label"></a>Krok 1: Název a popisku

1. Zadejte jedinečný název vašeho nasazení. Vyhněte se mezery a následující neplatné znaky: `& ^ [ ] { } \ | " < > /`.
1. Přidejte popisky, abyste mohli snadněji sledovat vaše nasazení. Popisky jsou **název**, **hodnotu** páry, které popisují vaše nasazení. Například `HostPlatform, Linux` nebo `Version, 3.0.1`.
1. Vyberte **Další** přesunout do kroku 2. 

### <a name="step-2-add-modules-optional"></a>Krok 2: Přidání modulů (volitelné)

Existují dva typy modulů, které můžete přidat do nasazení. První je modul, na základě služby Azure, jako je účet úložiště nebo Stream Analytics. Druhá je modul, na základě vlastní kód. Více modulů buď typu můžete přidat do nasazení. 

Pokud vytvoříte nasazení s žádné moduly, odstraní všechny existující moduly ze zařízení. 

>[!NOTE]
>Azure Machine Learning a Azure Functions nepodporují ještě nasazení automatizované služby Azure. Nasazení vlastní modul používá tyto služby ručně přidat do vašeho nasazení. 

Chcete-li přidat modul ze služby Azure Stream Analytics, postupujte takto:
1. Vyberte **Import Azure Stream Analytics IoT Edge modulu**.
1. Pomocí rozevíracích nabídek vyberte instancí služby Azure, které chcete nasadit.
1. Vyberte **Uložit** přidat modul pro nasazení. 

Chcete-li přidat vlastní kód jako modul, nebo ručně přidat modul služby Azure, postupujte takto:
1. Vyberte **Add IoT Edge module** (Přidat modul IoT Edge).
1. Poskytnout modul **název**.
1. Pro **Image URI** zadejte bitovou kopii kontejner Docker pro modul. 
1. Zadejte všechny **možnosti vytvoření kontejneru** který mají být odeslány do kontejneru. Další informace najdete v tématu [docker vytvořit][lnk-docker-create].
1. Použijte rozevírací nabídky vyberte **restartujte zásad**. Vyberte z následujících možností: 
   * **Vždy** – modul vždy restartuje, pokud z nějakého důvodu ukončí.
   * **Nikdy** – modul nikdy restartuje, pokud z nějakého důvodu ukončí.
   * **Na selhalo** – modul restartuje, pokud ji dojde k chybě, ale není-li vypne ještě jednou. 
   * **V pořádku** – modul restartuje, pokud dojde k chybě nebo vrátí není v pořádku stav. Je to na každý modul implementovat funkci stav stavu. 
1. Použijte rozevírací nabídky vyberte **požadovaného stavu** pro modul. Vyberte z následujících možností:
   * **Spuštění** -Toto je výchozí možnost. V modulu se spustí hned po dokončení nasazení.
   * **Zastavit** – po nasazení, modul zůstane v nečinnosti, dokud není zavolána při spuštění vy nebo jiný modul.
1. Vyberte **povolit** Pokud chcete přidat do modulu twin všechny značky nebo požadované vlastnosti. 
1. Vyberte **Uložit** přidat modul pro nasazení. 

Až budete mít všechny moduly pro nasazení nakonfigurovat, vyberte **Další** přesunout do třetí krok.

### <a name="step-3-specify-routes-optional"></a>Krok 3: Zadejte trasy (volitelné)

Trasy definovat, jak moduly vzájemně komunikovat v rámci nasazení. Zadejte všechny trasy pro vaše nasazení a pak vyberte **Další** přesunout do čtvrtý krok. 

### <a name="step-4-target-devices"></a>Krok 4: Cílová zařízení

Použijte vlastnost značky z vašich zařízení pro konkrétní zařízení, které by měly dostávat toto nasazení. 

Vzhledem k tomu, že více nasazení mohou být zaměřeny na stejném zařízení, je třeba přiřadit každé nasazení číslem priority. Pokud někdy dojde ke konfliktu, služby wins nasazení s nejvyšší prioritou. Pokud dvěma nasazeními stejné číslo priority, ten, který byl vytvořen většina nedávno wins. 

1. Zadejte kladné celé číslo pro nasazení **s prioritou**.
1. Zadejte **cíle podmínku** k určení zařízení, která budou cílem v tomto nasazení. Podmínka je založený na zařízení twin značky a musí odpovídat formátu výraz. Například, `tags.environment='test'`. 
1. Vyberte **Další** přechod poslední krok.

### <a name="step-5-review-template"></a>Krok 5: Šablona kontrolní

Zkontrolujte informace o nasazení a pak vyberte **odeslání**.

## <a name="monitor-a-deployment"></a>Monitorování nasazení

Chcete-li zobrazit podrobnosti o nasazení a monitorování zařízení se systémem, použijte následující postup:

1. Přihlaste se k [portál Azure] [ lnk-portal] a přejděte do služby IoT hub. 
1. Vyberte **IoT okraj (preview)**.
1. Vyberte **IoT Edge nasazení**. 

   ![Zobrazení IoT Edge nasazení][1]

1. Kontrola seznamu nasazení. Pro každé nasazení můžete zobrazit následující podrobnosti:
   * **ID** -název nasazení.
   * **Cíl podmínku** -značky používá k definování cílová zařízení.
   * **Priorita** -priority číslo přiřazené k nasazení.
   * **Stav agenta IoT Edge** -počet zařízení, které přijaly nasazení a stavy jejich stavu. 
   * **Není v pořádku moduly** -počet modulů v nasazení zasílání zpráv o chybách. 
   * **Čas vytvoření** -časové razítko z vytvoření nasazení. Toto časové razítko se používá k přerušení ties, když se dvě nasazení mají stejnou prioritu. 
1. Vyberte nasazení, které chcete monitorovat.  
1. Zkontrolujte podrobnosti o nasazení. Chcete-li zobrazit konkrétní podrobnosti o zařízení, které přijaly nasazení můžete karty: 
   * **Cílem** -hraniční zařízení, které odpovídají cílovou podmínku. 
   * **Použít** – cílových hraniční zařízení, které nejsou cílem jiné nasazení s vyšší prioritou. Jedná se o zařízení, které ve skutečnosti nasazení obdrží. 
   * **Vytváření sestav úspěch** – použít hraniční zařízení, kteří hlásili zpět ke službě, moduly byly úspěšně nasazeny. 
   * **Vytváření sestav selhání** – použité hraniční zařízení, hlášeny ve službě, že jeden nebo více modulů nebyla úspěšně nasazena. K hlubšímu prošetření chyba, musíte se vzdáleně připojit k těmto zařízením a zobrazit soubory protokolů. 
   * **Vytváření sestav není v pořádku moduly** – použité hraniční zařízení, hlášeny ve službě, že jeden nebo více modulů byly úspěšně nasazeny, ale jsou nyní hlášení chyb. 

## <a name="modify-a-deployment"></a>Upravit nasazení

Při úpravě nasazení změny okamžitě replikovat do všech cílových zařízení. 

Pokud aktualizujete cílovou podmínku, provedou se následující aktualizace:
* Pokud zařízení nesplnilo původní cílovou podmínku, ale splňuje novou cílovou podmínku a toto nasazení je nejvyšší prioritou pro toto zařízení, toto nasazení se použijí pro zařízení. 
* Pokud zařízení aktuálně spuštěných toto nasazení už splňuje cílovou podmínku, odinstaluje toto nasazení a trvá na další nasazení nejvyšší prioritou. 
* Pokud zařízení aktuálně spuštěných toto nasazení už splňuje cílovou podmínku a nesplňuje cílovou podmínku další nasazení, žádná změna probíhá na zařízení. Pokračuje její aktuální moduly v jejich aktuálního stavu zařízení, ale není spravován jako součást tohoto nasazení už. Jakmile splňuje cílovou podmínku další nasazení, odinstaluje toto nasazení a trvá v novém. 

Chcete-li upravit nasazení, použijte následující kroky: 

1. Přihlaste se k [portál Azure] [ lnk-portal] a přejděte do služby IoT hub. 
1. Vyberte **IoT okraj (preview)**.
1. Vyberte **IoT Edge nasazení**. 

   ![Zobrazení IoT Edge nasazení][1]

1. Vyberte nasazení, které chcete upravit. 
1. Proveďte aktualizace následující pole: 
   * Cílovou podmínku 
   * Popisky 
   * Priorita 
1. Vyberte **Uložit**.
1. Postupujte podle kroků v [monitorování nasazení] [ anchor-monitor] můžete sledovat změny zavedení. 

## <a name="delete-a-deployment"></a>Odstranění nasazení

Při odstranění nasazení aplikace na jejich další nejvyšší prioritou nasazení trvat žádná zařízení. Pokud vaše zařízení nesplňují cílovou podmínku další nasazení, moduly nejsou odebrány při odstranění nasazení. 

1. Přihlaste se k [portál Azure] [ lnk-portal] a přejděte do služby IoT hub. 
1. Vyberte **IoT okraj (preview)**.
1. Vyberte **IoT Edge nasazení**. 

   ![Zobrazení IoT Edge nasazení][1]

1. Vyberte nasazení, které chcete odstranit, použijte zaškrtávací políčko. 
1. Vyberte **Odstranit**.
1. Na řádku bude informovat, že tato akce bude toto nasazení odstranit a vrátit do předchozího stavu pro všechna zařízení.  To znamená, že bude použít nasazení s nižší prioritou.  Pokud je cílem není žádné další nasazení, odebere se žádné moduly. Pokud k tomu, aby zákazníci, potřebují k vytvoření nasazení s nulové moduly a nasaďte ho na stejné zařízení. Vyberte **Ano** Pokud chcete pokračovat. 

## <a name="next-steps"></a>Další postup

Další informace o [nasazení modulů do hraniční zařízení][lnk-deployments].

<!-- Images -->
[1]: ./media/how-to-deploy-monitor/iot-edge-deployments.png

<!-- Links -->
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-portal]: https://portal.azure.com
[lnk-docker-create]: https://docs.docker.com/engine/reference/commandline/create/
[lnk-deployments]: module-deployment-monitoring.md

<!-- Anchor links -->
[anchor-monitor]: #monitor-a-deployment
