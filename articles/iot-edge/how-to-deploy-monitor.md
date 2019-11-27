---
title: Vytvořit automatické nasazení z webu Azure portal – Azure IoT Edge | Dokumentace Microsoftu
description: Pomocí webu Azure portal k vytvoření automatického nasazení pro skupiny služby IoT Edge zařízení
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 286bab7b7fdbe42190c32dabb42c59d6fc094b2a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457360"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Nasazení a monitorování modulů IoT Edge ve velkém měřítku pomocí webu Azure portal

Vytvořte **IoT Edge automatické nasazení** v Azure Portal, abyste mohli spravovat průběžná nasazení pro mnoho zařízení najednou. Automatická nasazení pro IoT Edge jsou součástí funkce [automatické správy zařízení](/azure/iot-hub/iot-hub-automatic-device-management) v IoT Hub. Nasazení jsou dynamické procesy, které umožňují nasadit více modulů na více zařízení, sledovat stav a stav modulů a v případě potřeby provádět změny. 

Další informace najdete v tématu [vysvětlení IoT Edge automatického nasazení pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identifikace zařízení pomocí značek

Před vytvořením nasazení, budete muset mít k určení zařízení, která chcete ovlivnit. Azure IoT Edge identifikuje zařízení pomocí **značek** v zařízení s dvojitou signalizací. Každé zařízení může mít několik značek, které můžete definovat jakýmkoli způsobem, který dává smysl pro vaše řešení. Například pokud spravujete areálu Chytré budovy, můžete přidat následující značky k zařízení:

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

1. V [Azure Portal](https://portal.azure.com)přejdete do služby IoT Hub. 
1. Vyberte **IoT Edge**.
1. Vyberte **Přidat nasazení IoT Edge**.

Existuje pět kroků pro vytvoření nasazení. V následujících částech se provedou v rámci každé z nich. 

### <a name="step-1-name-and-label"></a>Krok 1: Název a popisek

1. Zadejte jedinečný název, který je malá písmena až 128 vašeho nasazení. Vyhněte se mezerám a následujícími neplatnými znaky: `& ^ [ ] { } \ | " < > /`.
1. Můžete přidat popisky jako páry klíč-hodnota, které vám pomůžou sledovat vaše nasazení. Například **HostPlatform** , **Linux**nebo **Version** a **3.0.1**.
1. Vyberte **Další** a přejděte ke kroku 2. 

### <a name="step-2-add-modules-optional"></a>Krok 2: Přidání modulů (volitelné)

Do nasazení můžete přidat až 20 modulů. 

Pokud vytvoříte nasazení bez modulů, odebere všechny aktuální moduly z cílových zařízení. 

Přidat modul z Azure Stream Analytics, postupujte podle těchto kroků:

1. V části **moduly nasazení** na stránce klikněte na tlačítko **Přidat**.
1. Vyberte **Azure Stream Analytics modul**.
1. Z rozevírací nabídky vyberte své **předplatné** .
1. Z rozevírací nabídky vyberte svoji **úlohu IoT Edge** .
1. Vyberte **Uložit** a přidejte do nasazení modul. 

Chcete-li přidat vlastní kód jako modul, nebo chcete-li ručně přidat modul služby Azure, postupujte takto:

1. V části **nastavení Container Registry** stránky zadejte názvy a přihlašovací údaje pro všechny kontejnery privátních kontejnerů, které obsahují image modulů pro toto nasazení. Agent IoT Edge ohlásí chybu 500, pokud nemůže najít přihlašovací údaje registru kontejneru pro bitovou kopii Docker.
1. V části **moduly nasazení** na stránce klikněte na tlačítko **Přidat**.
1. Vyberte **IoT Edge modul**.
1. Zadejte **název**modulu.
1. Do pole **identifikátor URI image** zadejte image kontejneru pro váš modul. 
1. Zadejte všechny **možnosti vytvoření kontejneru** , které by měly být předány do kontejneru. Další informace najdete v tématu [Docker Create](https://docs.docker.com/engine/reference/commandline/create/).
1. Pomocí rozevírací nabídky vyberte **zásadu restartování**. Zvolte jednu z následujících možností: 
   * **Always** – modul se vždy restartuje, pokud se z nějakého důvodu vypíná.
   * **nikdy** – modul se nikdy nerestartuje, pokud se z nějakého důvodu vypíná.
   * **při selhání** – modul se restartuje, pokud dojde k chybě, ale ne v případě, že se vypíná čistě. 
   * stav není **v pořádku** – modul se restartuje, pokud dojde k chybě, nebo vrátí stav není v pořádku. Záleží jen na každý modul implementovat funkci zdravotní stav. 
1. Pomocí rozevírací nabídky vyberte **požadovaný stav** pro modul. Zvolte jednu z následujících možností:
   * **spuštění** je výchozí možnost. V modulu začne okamžitě po nasazení spustí.
   * **Zastaveno** – po nasazení přestane modul zůstat nečinný, dokud se nespustí a nezačne vámi nebo jiným modulem.
1. Pokud chcete přidat značky nebo jiné vlastnosti do modulu s dvojitou vlastností, vyberte **nastavit požadované vlastnosti modulu** .
1. Zadejte **proměnné prostředí** pro tento modul. Proměnné prostředí poskytují informace o konfiguraci modulu.
1. Vyberte **Uložit** a přidejte do nasazení modul. 

Jakmile budete mít nakonfigurované všechny moduly pro nasazení, klikněte na tlačítko **Další** a přejděte ke kroku tři.

### <a name="step-3-specify-routes-optional"></a>Krok 3: Určení tras (volitelné)

Trasy definovat, jak moduly navzájem komunikují v rámci nasazení. Ve výchozím nastavení průvodce poskytuje trasu nazvanou **Route** a definovanou jako **FROM/* into $upstream * *, což znamená, že všechny zprávy ve službě IoT Hub budou odesílány do výstupů všech modulů.  

Přidejte nebo aktualizujte trasy s informacemi z [deklarace směrování](module-composition.md#declare-routes)a pak výběrem **Další** pokračujte do části přezkoumání.

### <a name="step-4-specify-metrics-optional"></a>Krok 4: určení metrik (volitelné)

Metriky poskytují souhrnné počty různých stavů, které může zařízení nahlásit zpět v důsledku použití konfiguračního obsahu.

1. Zadejte název **metriky**.

1. Zadejte dotaz na **kritéria metriky**. Dotaz vychází z IoT Edge [nahlášených vlastností](module-edgeagent-edgehub.md#edgehub-reported-properties)modulu centra rozbočovače. Metrika představuje počet řádků vrácených dotazem.

   Příklad:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

### <a name="step-5-target-devices"></a>Krok 5: cílová zařízení

Můžete cílit na konkrétní zařízení, které by měl obdrží toto nasazení vlastnost značek z vašich zařízení. 

Protože více nasazení mohou být zaměřeny na stejném zařízení, je třeba přiřadit každého nasazení priorita. Pokud dojde ke konfliktu, nasazení s nejvyšší prioritou (větší hodnoty označuje vyšší prioritu) WINS. Pokud mají dvě nasazení stejnou prioritu číslo, ten, který byl vytvořen většina nedávno wins. 

1. Zadejte kladné celé číslo pro **prioritu**nasazení.
1. Zadejte **cílovou podmínku** pro určení, která zařízení budou cílem tohoto nasazení. Podmínka je založena na nevyhovujících značkách zařízení nebo na dohlášených vlastnostech zařízení, které se musí shodovat s formátem výrazu. Například `tags.environment='test'` nebo `properties.reported.devicemodel='4000x'`. 
1. Kliknutím na tlačítko **Další** přejdete k poslednímu kroku.

### <a name="step-6-review-deployment"></a>Krok 6: Kontrola nasazení

Zkontrolujte informace o svém nasazení a pak vyberte **Odeslat**.

## <a name="deploy-modules-from-azure-marketplace"></a>Nasadit moduly z Azure Marketplace

Azure Marketplace je tržiště aplikací a služeb online, kde můžete procházet široké spektrum podnikových aplikací a řešení, která jsou certifikovaná a optimalizovaná pro spouštění v Azure, včetně [modulů IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). K Azure Marketplace lze také přistupovat prostřednictvím Azure Portal v části **vytvoření prostředku**.

IoT Edge modul můžete nasadit buď z Azure Marketplace nebo Azure Portal:

1. Vyhledejte modul a zahajte proces nasazení.

   * Azure Portal: Najděte modul a vyberte **vytvořit**.

   * Azure Marketplace:

     1. Najděte modul a vyberte **získat**.
     1. Kliknutím na **pokračovat**potvrďte podmínkami použití a zásady ochrany osobních údajů poskytovatele.

1. Vyberte své předplatné a IoT Hub, ke kterému je cílové zařízení připojené.

1. Vyberte možnost **nasadit ve velkém měřítku**.

1. Vyberte, jestli se má modul přidat k novému nasazení nebo klonu existujícího nasazení. Pokud klonování, vyberte ze seznamu existující nasazení.

1. Vyberte **vytvořit** a pokračujte v procesu vytváření nasazení ve velkém měřítku. Budete moct zadat stejné podrobnosti jako při jakémkoli nasazení.

## <a name="monitor-a-deployment"></a>Monitorování nasazení

Chcete-li zobrazit podrobnosti o nasazení a monitorování zařízení, na kterých je spuštěná, použijte následující postup:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub. 
1. Vyberte **IoT Edge**.
1. Vyberte **nasazení IoT Edge**. 

   ![Zobrazení nasazení IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Zkontrolujte seznam nasazení. Pro každé nasazení si můžete zobrazit následující podrobnosti:
   * **ID** – název nasazení.
   * **Podmínka cíle** – značka používaná k definování cílových zařízení.
   * **Priorita** – číslo priority přiřazené k nasazení.
   * **Systémové metriky** - **cíle** určují počet nepodmíněných zařízení v IoT Hub, které odpovídají podmínce cíle, a **použije** se hodnota určuje počet zařízení, u kterých byl obsah nasazení použit v případě, že je v modulu IoT Hub. 
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

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub. 
1. Vyberte **IoT Edge**.
1. Vyberte **nasazení IoT Edge**. 

   ![Zobrazení nasazení IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Vyberte nasazení, které chcete upravit. 
1. Proveďte aktualizace na následující pole: 
   * Cílová podmínka
   * Metriky – můžete upravit nebo odstranit metriky, které jste definovali, nebo přidat nové.
   * Popisky
   * Priorita
1. Vyberte **Uložit**.
1. Pokud chcete sledovat změny, postupujte podle kroků v části [monitorování nasazení](#monitor-a-deployment) . 

## <a name="delete-a-deployment"></a>Odstranit nasazení

Při odstranění nasazení nějaká zařízení provést další nejvyšší prioritu nasazení. Pokud vaše zařízení nesplňuje cílová podmínka jakékoli jiné nasazení, moduly se neodeberou při nasazení se odstraní. 

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub. 
1. Vyberte **IoT Edge**.
1. Vyberte **nasazení IoT Edge**. 

   ![Zobrazení nasazení IoT Edge](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Pomocí zaškrtávacího políčka vyberte nasazení, které chcete odstranit. 
1. Vyberte **Odstranit**.
1. Řádku bude informovat, že tato akce bude toto nasazení odstranit a vrátit do předchozího stavu pro všechna zařízení.  To znamená, že bude platit nasazení s nižší prioritou.  Pokud není zacíleno žádné jiné nasazení, nebudou odebrány žádné moduly. Pokud chcete odebrat všechny moduly ze zařízení, vytvořit nasazení s nulovou moduly a nasadíte ho do stejného zařízení. Pokračujte výběrem **Ano** . 

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [nasazení modulů do zařízení IoT Edge](module-deployment-monitoring.md).
