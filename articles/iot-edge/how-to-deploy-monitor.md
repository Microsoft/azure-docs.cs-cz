---
title: Nasazení modulů ve velkém měřítku v Azure Portal-Azure IoT Edge
description: Vytvoření automatických nasazení pro skupiny IoT Edge zařízení pomocí Azure Portal
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 068845bf8cda7ce6abf11eefad0ed176688b34c5
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665844"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Nasazení a sledování IoT Edgech modulů pomocí Azure Portal

Vytvořte **IoT Edge automatické nasazení** v Azure Portal, abyste mohli spravovat průběžná nasazení pro mnoho zařízení najednou. Automatická nasazení pro IoT Edge jsou součástí funkce [automatické správy zařízení](/azure/iot-hub/iot-hub-automatic-device-management) v IoT Hub. Nasazení jsou dynamické procesy, které umožňují nasadit více modulů na více zařízení, sledovat stav a stav modulů a v případě potřeby provádět změny. 

Další informace najdete v tématu [vysvětlení IoT Edge automatického nasazení pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Identifikace zařízení pomocí značek

Než budete moct vytvořit nasazení, musíte být schopni určit, která zařízení mají mít vliv. Azure IoT Edge identifikuje zařízení pomocí **značek** v zařízení s dvojitou signalizací. Každé zařízení může mít několik značek, které můžete definovat jakýmkoli způsobem, který dává smysl pro vaše řešení. Pokud například spravujete areály inteligentních budov, můžete do zařízení přidat následující značky:

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

Existuje pět kroků k vytvoření nasazení. V následujících částech si projdete každé z nich. 

### <a name="step-1-name-and-label"></a>Krok 1: název a popisek

1. Dejte vašemu nasazení jedinečný název, který bude obsahovat až 128 malých písmen. Vyhněte se mezerám a následujícími neplatnými znaky: `& ^ [ ] { } \ | " < > /`.
1. Můžete přidat popisky jako páry klíč-hodnota, které vám pomůžou sledovat vaše nasazení. Například **HostPlatform** , **Linux**nebo **Version** a **3.0.1**.
1. Vyberte **Další** a přejděte ke kroku 2. 

### <a name="step-2-add-modules-optional"></a>Krok 2: přidání modulů (volitelné)

Do nasazení můžete přidat až 20 modulů. 

Pokud vytvoříte nasazení bez modulů, odebere všechny aktuální moduly z cílových zařízení. 

Chcete-li přidat modul z Azure Stream Analytics, postupujte podle následujících kroků:

1. V části **moduly nasazení** na stránce klikněte na tlačítko **Přidat**.
1. Vyberte **Azure Stream Analytics modul**.
1. Z rozevírací nabídky vyberte své **předplatné** .
1. Z rozevírací nabídky vyberte svoji **úlohu IoT Edge** .
1. Vyberte **Uložit** a přidejte do nasazení modul. 

Pokud chcete přidat vlastní kód jako modul nebo ručně přidat modul služby Azure, postupujte takto:

1. V části **nastavení Container Registry** stránky zadejte názvy a přihlašovací údaje pro všechny kontejnery privátních kontejnerů, které obsahují image modulů pro toto nasazení. Agent IoT Edge ohlásí chybu 500, pokud nemůže najít přihlašovací údaje registru kontejneru pro bitovou kopii Docker.
1. V části **moduly nasazení** na stránce klikněte na tlačítko **Přidat**.
1. Vyberte **IoT Edge modul**.
1. Zadejte **název**modulu.
1. Do pole **identifikátor URI image** zadejte image kontejneru pro váš modul. 
1. Zadejte všechny **možnosti vytvoření kontejneru** , které by měly být předány do kontejneru. Další informace najdete v tématu [Docker Create](https://docs.docker.com/engine/reference/commandline/create/).
1. Pomocí rozevírací nabídky vyberte **zásadu restartování**. Vyberte si z následujících možností: 
   * **Always** – modul se vždy restartuje, pokud se z nějakého důvodu vypíná.
   * **nikdy** – modul se nikdy nerestartuje, pokud se z nějakého důvodu vypíná.
   * **při selhání** – modul se restartuje, pokud dojde k chybě, ale ne v případě, že se vypíná čistě. 
   * stav není **v pořádku** – modul se restartuje, pokud dojde k chybě, nebo vrátí stav není v pořádku. Pro implementaci funkce stavu je to pro každý modul. 
1. Pomocí rozevírací nabídky vyberte **požadovaný stav** pro modul. Vyberte si z následujících možností:
   * **spuštění** je výchozí možnost. Modul se spustí hned po nasazení.
   * **Zastaveno** – po nasazení přestane modul zůstat nečinný, dokud se nespustí a nezačne vámi nebo jiným modulem.
1. Pokud chcete přidat značky nebo jiné vlastnosti do modulu s dvojitou vlastností, vyberte **nastavit požadované vlastnosti modulu** .
1. Zadejte **proměnné prostředí** pro tento modul. Proměnné prostředí poskytují informace o konfiguraci modulu.
1. Vyberte **Uložit** a přidejte do nasazení modul. 

Jakmile budete mít nakonfigurované všechny moduly pro nasazení, klikněte na tlačítko **Další** a přejděte ke kroku tři.

### <a name="step-3-specify-routes-optional"></a>Krok 3: určení tras (volitelné)

Trasy definují, jak vzájemně komunikují moduly v rámci nasazení. Ve výchozím nastavení průvodce poskytuje trasu nazvanou **Route** a definovanou jako **FROM/* into $upstream * *, což znamená, že všechny zprávy ve službě IoT Hub budou odesílány do výstupů všech modulů.  

Přidejte nebo aktualizujte trasy s informacemi z [deklarace směrování](module-composition.md#declare-routes)a pak výběrem **Další** pokračujte do části přezkoumání.

### <a name="step-4-specify-metrics-optional"></a>Krok 4: určení metrik (volitelné)

Metriky poskytují souhrnné počty různých stavů, které může zařízení nahlásit zpět v důsledku použití konfiguračního obsahu.

1. Zadejte název **metriky**.

1. Zadejte dotaz na **kritéria metriky**. Dotaz vychází z IoT Edge [nahlášených vlastností](module-edgeagent-edgehub.md#edgehub-reported-properties)modulu centra rozbočovače. Metrika představuje počet řádků vrácených dotazem.

   Například:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

### <a name="step-5-target-devices"></a>Krok 5: cílová zařízení

Pomocí vlastnosti značky ze svých zařízení můžete cílit na konkrétní zařízení, která by měla přijmout toto nasazení. 

Vzhledem k tomu, že více nasazení může cílit na stejné zařízení, měli byste každému nasazení přidělit prioritní číslo. Pokud dojde ke konfliktu, nasazení s nejvyšší prioritou (větší hodnoty označuje vyšší prioritu) WINS. Pokud má dvě nasazení stejné číslo priority, ten, který byl vytvořen naposledy službou WINS. 

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

Pokud chcete zobrazit podrobnosti o nasazení a monitorovat zařízení, která ho používají, postupujte podle následujících kroků:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub. 
1. Vyberte **IoT Edge**.
1. Vyberte **nasazení IoT Edge**. 

   ![Zobrazit IoT Edge nasazení](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Zkontrolujte seznam nasazení. Pro každé nasazení si můžete zobrazit následující podrobnosti:
   * **ID** – název nasazení.
   * **Podmínka cíle** – značka používaná k definování cílových zařízení.
   * **Priorita** – číslo priority přiřazené k nasazení.
   * **Systémové metriky** - **cíle** určují počet nepodmíněných zařízení v IoT Hub, které odpovídají podmínce cíle, a **použije** se hodnota určuje počet zařízení, u kterých byl obsah nasazení použit v případě, že je v modulu IoT Hub. 
   * **Metriky zařízení** – počet IoT Edge zařízení v průběhu generování sestav o úspěchu nebo chybách z IoT Edge modulu runtime klienta.
   * **Vlastní metriky** – počet IoT Edgech zařízení v datech generování sestav nasazení pro jakékoli metriky, které jste definovali pro nasazení.
   * **Čas vytvoření** – časové razítko z doby, kdy bylo vytvořeno nasazení. Toto časové razítko se používá k přerušení vztahů, pokud dvě nasazení mají stejnou prioritu. 
1. Vyberte nasazení, které chcete monitorovat.  
1. Zkontrolujte podrobnosti o nasazení. Pomocí karet můžete zkontrolovat podrobnosti nasazení.

## <a name="modify-a-deployment"></a>Úprava nasazení

Když upravíte nasazení, změny se okamžitě replikují na všechna cílová zařízení. 

Pokud aktualizujete cílovou podmínku, dojde k následujícím aktualizacím:

* Pokud zařízení nesplňuje starou cílovou podmínku, ale splňuje novou cílovou podmínku a toto nasazení je nejvyšší prioritou pro toto zařízení, pak se toto nasazení aplikuje na zařízení. 
* Pokud zařízení aktuálně spuštěné v tomto nasazení už nesplňuje cílovou podmínku, odinstaluje toto nasazení a provede nasazení s další nejvyšší prioritou. 
* Pokud zařízení aktuálně spuštěné toto nasazení už nesplňuje cílovou podmínku a nesplňuje cílovou podmínku žádného jiného nasazení, neproběhne na zařízení žádná změna. Zařízení pokračuje v běhu svých aktuálních modulů v aktuálním stavu, ale už se nespravuje jako součást tohoto nasazení. Jakmile splňuje cílovou podmínku jakéhokoli jiného nasazení, odinstaluje toto nasazení a provede nový. 

Chcete-li upravit nasazení, použijte následující postup: 

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub. 
1. Vyberte **IoT Edge**.
1. Vyberte **nasazení IoT Edge**. 

   ![Zobrazit IoT Edge nasazení](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Vyberte nasazení, které chcete upravit. 
1. Proveďte aktualizace následujících polí: 
   * Cílová podmínka
   * Metriky – můžete upravit nebo odstranit metriky, které jste definovali, nebo přidat nové.
   * Popisky
   * Priorita
1. Vyberte **Save** (Uložit).
1. Pokud chcete sledovat změny, postupujte podle kroků v části [monitorování nasazení](#monitor-a-deployment) . 

## <a name="delete-a-deployment"></a>Odstranění nasazení

Při odstranění nasazení převezmou všechna zařízení nasazení s další nejvyšší prioritou. Pokud zařízení nesplňuje cílovou podmínku jakéhokoli jiného nasazení, moduly se po odstranění nasazení neodeberou. 

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub. 
1. Vyberte **IoT Edge**.
1. Vyberte **nasazení IoT Edge**. 

   ![Zobrazit IoT Edge nasazení](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Pomocí zaškrtávacího políčka vyberte nasazení, které chcete odstranit. 
1. Vyberte **Odstranit**.
1. Výzva vás upozorní, že tato akce odstraní toto nasazení a vrátí se k předchozímu stavu pro všechna zařízení.  To znamená, že bude platit nasazení s nižší prioritou.  Pokud není zacíleno žádné jiné nasazení, nebudou odebrány žádné moduly. Pokud chcete odebrat všechny moduly ze zařízení, vytvořte nasazení s nulovými moduly a nasaďte je do stejného zařízení. Pokračujte výběrem **Ano** . 

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [nasazení modulů do zařízení IoT Edge](module-deployment-monitoring.md).
