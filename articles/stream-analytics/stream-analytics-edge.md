---
title: Azure Stream Analytics IoT hranu (preview)
description: Vytvoření úlohy edge v Azure Stream Analytics a nasadit je spuštěna zařízení Azure IoT okraj.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/16/2017
ms.openlocfilehash: 1c02f8f7b086d0441c2def0e92c54cce72fad85e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="azure-stream-analytics-on-iot-edge-preview"></a>Azure Stream Analytics IoT hranu (preview)

> [!IMPORTANT]
> Tato funkce je ve verzi preview. Nedoporučujeme použití v produkčním prostředí.
 
Azure Stream Analytics (ASA) na IoT Edge umožňuje vývojářům nasazení téměř v reálném čase blíže analytical intelligence do zařízení IoT tak, aby se můžete odemknout úplné hodnota generovaná zařízení data. Navržený pro s nízkou latencí, odolnost proti chybám, efektivní využití šířky pásma a dodržování předpisů, podniky teď můžete nasadit řízení logiku blízko průmyslových operace a doplňují analýzy velkých objemů dat v cloudu.  
Azure Stream Analytics IoT hranu běží v rámci [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) framework. Jakmile je vytvořen v ASA, deploym úlohy a spravovat úlohy ASA používání služby IoT Hub.
Tato funkce je ve verzi preview, pokud máte jakékoli otázku nebo připomínky můžete použít [tento průzkum](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) kontaktovat produktový tým. 

## <a name="scenarios"></a>Scénáře
![Vysokoúrovňový diagram](media/stream-analytics-edge/ASAedge_highlevel.png)

* **Příkaz s nízkou latencí a řízení**: například výrobní bezpečnostní systémy musí reagovat na provozních dat s velmi nízkou latencí. S ASA hranu IoT můžete analyzovat senzor, data v téměř v reálném čase a vydávat příkazy při zjišťovat anomálie počítače zastavit nebo výstrahy aktivovat.
*   **Omezené připojení ke cloudu**: mise rozhodujících systémů, jako je vzdálené dolování vybavení, připojené plavidel nebo příbřežních procházení, třeba k analýze a reagovat na data i v případě, že se přerušované připojení cloudu. S ASA streamování logiky běží nezávisle na síťové připojení a můžete zvolit, co můžete odeslat do cloudu pro další zpracování nebo úložiště.
* **Omezenou šířkou pásma**: objem dat vytvářených jet moduly nebo připojených aut může být tak velká, že data musí být filtrovaná nebo předem zpracovaných před odesláním do cloudu. ASA můžete filtrovat nebo agregovat data, která mají být odesílány do cloudu.
* **Dodržování předpisů**: předpisů může vyžadovat některé data místně anonymní nebo agregovat před odesláním do cloudu.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Hraniční úlohy v Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Co je úlohu "edge"?

Úlohy ASA Edge spustit jako moduly v rámci [runtime Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Se skládají ze dvou částí:
1.  Cloudová část, která je odpovědná za definici úlohy: uživatelé definovat vstupy, výstup, dotazů a další nastavení (mimo pořadí událostí atd.) v cloudu.
2.  ASA na IoT Edge modul, který běží místně. Obsahuje modul ASA komplexní zpracování událostí a přijímá definici úlohy z cloudu. 

ASA používá k nasazení edge úloh do zařízení IoT Hub. Další informace o [IoT Edge nasazení můžete zobrazit tady](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Hraniční úloha](media/stream-analytics-edge/ASAedge_job.png)


### <a name="installation-instructions"></a>Pokyny k instalaci
Hlavní kroky jsou popsané v následující tabulce. Další podrobnosti jsou uvedeny v následujících částech.
|      |Krok   | Místní     | Poznámky   |
| ---   | ---   | ---       |  ---      |
| 1   | **Vytvořit úlohu ASA edge**   | Azure Portal      |  Vytvořit novou úlohu, vyberte **Edge** jako **hostování prostředí**. <br> Tyto úlohy se vytvořit nebo spravovat z cloudu a spusťte na zařízení IoT okraj.     |
| 2   | **Vytvoření kontejneru úložiště**   | Azure Portal       | Kontejnery úložiště se používají k uložíte definici úlohy, které lze k nim vaše zařízení IoT. <br>  Všechny existující kontejner úložiště můžete znovu použít.     |
| 3   | **Nastavení prostředí IoT Edge na vašem zařízení**   | Zařízení      | Pokyny pro [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) nebo [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Nasazení ASA na vaše zařízení IoT Edge**   | Azure Portal      |  Definice úlohy ASA se exportují do kontejneru úložiště vytvořili dříve.       |
Můžete postupovat podle [Tento podrobný kurz](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) nasazení vaše první práce ASA hranu IoT. Následující video by vám pomůže porozumět procesu spustit úlohu služby Stream Analytics v hraniční zařízení IoT:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]



#### <a name="create-an-asa-edge-job"></a>Umožňuje vytvořit úlohu služby ASA Edge
1. Z portálu Azure vytvořte novou "úloha Stream Analytics". [Přímý odkaz na vytvoření zde nové úlohy ASA](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

> [!Note]
> Ve všech oblastech podporovaných ASA, můžete vytvořit úlohy Edge **kromě oblast "Západní USA 2"**.
> Toto omezení se odeberou za chvíli.

2. V dialogovém okně vytvoření vyberte **Edge** jako **hostování prostředí** (viz následující obrázek) ![vytvoření úlohy](media/stream-analytics-edge/ASAEdge_create.png)
3. Definice úlohy
    1. **Definování vstupní Stream(s)**. Zadejte jednu nebo několik vstupní datové proudy pro úlohu.
    2. Definujte referenční data (volitelné).
    3. **Definujte výstupní Stream(s)**. Zadejte jednu nebo několik datových proudů výstupy pro úlohu. 
    4. **Definování dotazu**. Zadejte dotaz ASA v cloudu pomocí editoru vložené. Kompilátor automaticky kontroluje syntaxe pro hraniční ASA povoleno. Můžete také otestovat váš dotaz tím, že nahrajete ukázková data. 
4. Volitelné nastavení
    1. **Řazení událostí**. Na portálu můžete nakonfigurovat zásady mimo pořadí. Dokumentace je k dispozici [zde](https://msdn.microsoft.com/library/azure/mt674682.aspx?f=255&MSPPError=-2147217396).
    2. **Národní prostředí**. Nastavte formát internalization.


#### <a name="create-a-storage-container"></a>Vytvoření kontejneru úložiště
Kontejner úložiště se vyžaduje, aby bylo možné exportovat ASA zkompilován dotaz a úlohy konfigurace. Umožňuje nakonfigurovat bitovou kopii ASA Docker specifického dotazu. 
1. Postupujte podle [tyto pokyny](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) chcete vytvořit účet úložiště z portálu Azure. Můžete ponechat všechny výchozí možnosti s ASA pomocí tohoto účtu.
2. V nově vytvořený účet úložiště vytvořte kontejner úložiště objektů blob:
    1. Klikněte na "Objekty BLOB", pak "+ kontejner". 
    2. Zadejte název a zachovat kontejneru jako "Privátní"


> [!Note]
> Při vytváření nasazení ASA exportuje definici úlohy na kontejner úložiště. Tato úloha definice zůstávají stejné po dobu trvání nasazení. V důsledku toho pokud chcete aktualizovat úloha spuštěna na hranici, budete muset upravit úlohu v ASA a pak vytvořte nové nasazení ve IoT Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Nastavení prostředí IoT Edge na vašem zařízení
Hraniční úlohy můžete nasadit na zařízení se systémem Azure IoT okraj.
V takovém případě je třeba postupovat podle těchto kroků:
- Vytvoření služby Iot Hub;
- Nainstalovat modul runtime Docker a IoT Edge na vaší hraniční zařízení;
- Vaše zařízení nastavte jako "IoT hraniční zařízení" v IoT Hub.

Tyto kroky jsou popsané v dokumentaci k hraniční IoT pro [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) nebo [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Nasazení ASA na vaše zařízení IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Přidání ASA k nasazení
- Na portálu Azure otevřete Centrum IoT, přejděte do Průzkumníka Edge IoT a otevřete okno vaše zařízení.
- Vyberte **nastavit moduly**, pak vyberte **Import Azure služby IoT Edge Module**.
- Vyberte předplatné a úlohy ASA okraj, který jste vytvořili. Pak vyberte svůj účet úložiště. Klikněte na tlačítko Uložit.
![Přidat modul ASA ve vašem nasazení](media/stream-analytics-edge/set_module.png)


> [!Note]
> V tomto kroku ASA požaduje přístup ke kontejneru vybrané úložiště a pak vytvoří složku s názvem "EdgeJobs". Pro každé nasazení se vytvoří novou podsložku ve složce "EdgeJobs".
> Aby bylo možné nasadit úlohu do hraniční zařízení, vytvoří ASA sdílený přístupový podpis (SAS) pro soubor definice úlohy. SAS klíč, který je bezpečně přenesou do zařízení IoT Edge pomocí dvojče zařízení. Vypršení platnosti tento klíč je tři roky dni jeho vytvoření.


Další podrobnosti o nasazení IoT Edge najdete v tématu [tuto stránku](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Nakonfigurujte trasy
Okraj IoT poskytuje způsob, jak deklarativně směrování zpráv mezi moduly a mezi moduly a IoT Hub. Úplná syntaxe je popsán [zde](https://docs.microsoft.com/azure/iot-edge/module-composition).
Názvy vstupy a výstupy vytvořené v úlohy ASA slouží jako koncové body pro směrování.  

###### <a name="example"></a>Příklad:
```
{
"routes": {                                              
    "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
    "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream", 
    "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")" 
}
}   

```
Tento příklad ukazuje trasy pro scénář popsaný v následující obrázek. Obsahuje úlohu edge názvem "**ASA**", se vstupem s názvem "**teploty**"a výstup s názvem"**výstraha**".
![Příklad směrování](media/stream-analytics-edge/RoutingExample.png)

Tento příklad definuje následující trasy:
- Každou zprávu od **tempSensor** posílá modulu s názvem **ASA** vstupu s názvem **teploty**,
- Všechny výstupy **ASA** modulu se odesílají do služby IoT Hub propojené s tímto zařízením (proti proudu$),
- Všechny výstupy **ASA** modulu se odesílají do **řízení** koncový bod **tempSensor**.


## <a name="technical-information"></a>Technické informace
### <a name="current-limitations-for-edge-jobs-compared-to-cloud-jobs"></a>Aktuální omezení pro úlohy edge ve srovnání s úlohami cloudu
Cílem je mít rozdíly mezi okraj úlohy a úlohy v cloudu. Většina funkcí naše dotazu jazyka SQL jsou již podporovány.
Ale nejsou pro úlohy edge ještě podporovány následující funkce:
* Uživatelem definované funkce (UDF) a uživatelem definované agregace (UDA).
* Azure ML funkce.
* Použití více než 14 agregace v jediném kroku.
* Formát AVRO pro vstupu a výstupu. V tuto chvíli jsou podporovány pouze sdíleného svazku clusteru a JSON.
* Komprese JSON vstupu.
* Následující operátory SQL:
    * AnomalyDetection
    * Operátory geoprostorové:
        * CreatePoint
        * CreatePolygon
        * CreateLineString
        * ST_DISTANCE
        * ST_WITHIN
        * ST_OVERLAPS
        * ST_INTERSECTS
    * ODDÍL
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Modul runtime a hardwarové požadavky
Aby bylo možné spustit ASA na IoT okraj, je nutné zařízení, které můžou běžet [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA a Azure IoT Edge použít **Docker** kontejnery k zajištění přenosné řešení, která běží na několika hostitele operačního systému (Windows, Linux).

ASA IoT hranu je k dispozici jako Image systému Windows a Linux, systémem x86-64 nebo architektury ARM. 


### <a name="input-and-output"></a>Vstup a výstup
#### <a name="input-and-output-streams"></a>Vstupní a výstupní datové proudy
Úlohy ASA Edge můžete získat vstupy a výstupy z ostatních modulů běžící na zařízení IoT okraj. Pro připojení z a do určitých modulech, můžete nastavit konfigurace směrování v době nasazení. Další informace najdete na [dokumentace IoT Edge modulu složení](https://docs.microsoft.com/azure/iot-edge/module-composition).

Pro vstupy a výstupy podporované jsou formáty sdíleného svazku clusteru a JSON.

Pro každý vstupní a výstupní datový proud vytvoříte ve vaší úloze ASA, vytvoří se na nasazených modul odpovídající koncový bod. Tyto koncové body lze použít v trasy vašeho nasazení.



##### <a name="reference-data"></a>Referenční data
Referenční data (také označované jako vyhledávací tabulky) je omezené datovou sadou, která jsou statická nebo zpomalení změna ve své podstatě. Používá se k provedení vyhledávání nebo ke korelaci s datového proudu. Chcete-li použít referenčních dat ve vaší úloze Azure Stream Analytics, obvykle použijete [referenční datové připojení](https://msdn.microsoft.com/library/azure/dn949258.aspx) v dotazu. Další informace najdete v tématu [ASA dokumentaci o referenční data](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data).

Chcete-li použít referenční data pro ASA hranu Iot, budete muset postupovat podle těchto kroků: 
1. Vytvořit nový vstupní pro úlohu
2. Zvolte **referenční data** jako **typ zdroje**.
3. Nastavte cestu k souboru. Cesta k souboru by měl být **absolutní** cesta k souboru na zařízení ![referenční data vytvoření](media/stream-analytics-edge/ReferenceData.png)
4. Povolit **sdílené disky** v Docker konfiguraci a ujistěte se, že je povolena jednotce před spuštěním nasazení.

Další informace najdete v tématu [Docker dokumentaci k systému Windows zde](https://docs.docker.com/docker-for-windows/#shared-drives).

> [!Note]
> V tuto chvíli se podporuje jenom místní referenční data.




## <a name="license-and-third-party-notices"></a>Licence a oznámení třetích stran
* [Azure Stream Analytics na IoT Edge preview licence](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Oznámení třetích stran pro Azure Stream Analytics na IoT Edge preview](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="get-help"></a>Podpora
O další pomoc, zkuste [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Další postup

* [Další informace o Azure Iot Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [ASA na IoT Edge kurzu](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Pošlete svůj názor týmu pomocí tohoto průzkumu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
