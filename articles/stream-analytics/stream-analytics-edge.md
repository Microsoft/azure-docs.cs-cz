---
title: Azure Stream Analytics na hraničních zařízeních IoT
description: Vytvořte hraniční úlohy ve službě Azure Stream Analytics a nasazovat je do zařízení s Azure IoT Edge.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 9d5a0cf9fa4f9ad8b5a673cd2420416f92edda91
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994976"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics na hraničních zařízeních IoT
 
Azure Stream Analytics (ASA) v prostředí IoT Edge umožňuje vývojářům nasadit téměř v reálném čase inteligentní funkce analýzy blíže zařízením IoT tak, aby bylo možné odemknout plný potenciál dat generovaných zařízením. Služba Azure Stream Analytics je určená k zajištění nízké latence, odolnosti, efektivního využití šířky pásma a dodržování předpisů. Podniky nyní můžete nasadit logiky ovládacího prvku blízko průmyslové operací a doplňují analýzy velkých objemů dat v cloudu.  

Azure Stream Analytics na hraničních zařízeních IoT běží v rámci [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) rozhraní framework. Po vytvoření úlohy v Azure Stream Analytics můžete nasadit a spravovat ho pomocí služby IoT Hub.

## <a name="scenarios"></a>Scénáře
![Vysokoúrovňový diagram IoT Edge](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Příkaz s nízkou latencí a ovládací prvek**: Například výrobní bezpečnostních systémů musí odpovědět na provozních dat s mimořádně nízkou latencí. S Azure Stream Analytics na hraničních zařízeních IoT můžete analyzovat senzor, data v téměř reálném čase a vydávat příkazy při detekci anomálií zastavit na počítači nebo aktivovat upozornění.
*   **Omezené připojení ke cloudu**: Středisko důležité systémy, jako je například vzdáleného hornictví zařízení, připojených plavidel nebo poskytuje procházení, potřebujete k analýze a reagovat na data, i když je přerušované připojení ke cloudu. S Azure Stream Analytics streamování logiky běží nezávisle na připojení k síti a můžete zvolit, jaké můžete odesílat do cloudu pro další zpracování nebo úložiště.
* **Omezená šířka pásma**: Objem dat vytvářených jet moduly nebo auta připojená k síti může být tak velká, že data musí být filtrovaný nebo předem zpracovaných před odesláním do cloudu. Azure Stream Analytics můžete filtrovat nebo agregaci dat, kterou je potřeba odeslat do cloudu.
* **Dodržování předpisů**: Některá data místně anonymizované nebo agregovaná před odesláním do cloudu může vyžadovat dodržování legislativních předpisů.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Hraniční úlohy ve službě Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Co je "hraniční" úlohy?

Azure Stream Analytics hraniční úlohy spouštění v kontejnerech, které jsou nasazené na [zařízení Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Se skládají ze dvou částí:
1.  Cloudová část, která zodpovídá za definici úlohy: uživatelé definovat vstupů, výstup, dotazu a další nastavení (nefungujících událostí atd.) v cloudu.
2.  Modul, který běží na zařízení IoT. Obsahuje modul Azure Stream Analytics a přijímá definici úlohy z cloudu. 

Azure Stream Analytics používá k nasazení hraniční úlohy na zařízení služby IoT Hub. Další informace o [nasazení IoT Edge můžete vidět](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Azure Stream Analytics hraniční úloha](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Pokyny k instalaci
Hlavní kroky jsou popsány v následující tabulce. Další podrobnosti jsou popsány v následujících částech.
|      |Krok   | Poznámky   |
| ---   | ---   |  ---      |
| 1   | **Vytvoření kontejneru úložiště**   | Kontejnery úložiště se používají k uložení vaší definice úlohy, ve kterém jsou dostupné v zařízení IoT. <br>  Můžete znovu použít jakékoli existující kontejner úložiště.     |
| 2   | **Vytvoření hraniční úlohy Azure Stream Analytics**   |  Vytvořit novou úlohu, vyberte **hrany** jako **hostitelské prostředí**. <br> Tyto úlohy jsou vytvořili spravovaných z cloudu a spustit na zařízeních IoT Edge.     |
| 3   | **Nastavení prostředí IoT Edge na vaše zařízení**   | Pokyny, jak [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) nebo [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Nasazení Azure Stream Analytics na vaše zařízení IoT Edge**   |  Definice úlohy Azure Stream Analytics se exportují do kontejneru úložiště vytvořili dříve.       |
Můžete postupovat podle [Tento podrobný kurz](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) k nasazení vaší první úlohu Azure Stream Analytics na hraničních zařízeních IoT. V následujícím videu by vám pomůže porozumět procesu ke spuštění úlohy Stream Analytics na hraničním zařízením IoT:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Vytvoření kontejneru úložiště
Kontejner úložiště je potřeba, abyste mohli exportovat dotaz Azure Stream Analytics zkompilován a konfiguraci úloh. Používá se ke konfiguraci image Dockeru pro Azure Stream Analytics s konkrétní dotaz. 
1. Postupujte podle [tyto pokyny](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) k vytvoření účtu úložiště na webu Azure Portal. Abyste mohli všechny výchozí možnosti použití tento účet s Azure Stream Analytics.
2. V na nově vytvořený účet úložiště vytvořte kontejner úložiště objektů blob:
    1. Klikněte na **objekty BLOB**, pak **+ kontejner**. 
    2. Zadejte název a zachování provozu kontejneru jako **privátní**.

#### <a name="create-an-asa-edge-job"></a>Vytvořit úlohu Azure Stream Analytics Edge
> [!Note]
> Tento kurz se zaměřuje na vytvoření úlohy Azure Stream Analytics pomocí webu Azure portal. Můžete také [pomocí modulu plug-in Visual Studio můžete vytvořit úlohu služby Azure Stream Analytics Edge](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. Z portálu Azure portal vytvořte novou "Stream Analytics úlohu". [Přímý odkaz na vytvoření zde nové úlohy Azure Stream Analytics](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. V dialogovém okně Vytvořit zvolte **hrany** jako **hostitelské prostředí** (viz následující obrázek)

   ![Vytvoření úlohy Stream Analytics na hraničních zařízeních](media/stream-analytics-edge/create-asa-edge-job.png)
3. Definice úlohy
    1. **Definování vstupním datovým proudům**. Definujte jeden nebo několik vstupních datových proudů pro vaši úlohu.
    2. Definujte referenčních dat (volitelné).
    3. **Definujte výstupní datovým proudům**. Definujte jeden nebo několik datových proudů výstupy pro vaši úlohu. 
    4. **Definování dotazu**. Definování dotazu Azure Stream Analytics v cloudu s využitím editoru vnořeném do stránky. Kompilátor automaticky ověří syntaxi povolena pro Azure Stream Analytics edge. Můžete také testovat dotaz tak, že nahrajete ukázková data. 

4. Nastavení informací o kontejneru úložiště v **nastavení IoT Edge** nabídky.

5. Volitelná nastavení
    1. **Řazení událostí**. Na portálu můžete nakonfigurovat zásady mimo pořadí. Dokumentace je k dispozici [tady](https://msdn.microsoft.com/library/azure/mt674682.aspx?f=255&MSPPError=-2147217396).
    2. **Národní prostředí**. Nastavte formát internalization.



> [!Note]
> Po vytvoření nasazení Azure Stream Analytics exportuje definici úlohy do kontejneru úložiště. Tuto definici úlohy, zůstane během doby trvání nasazení. V důsledku toho pokud chcete aktualizovat úloha spuštěná na hraničních zařízeních, musíte upravit úlohy v Azure Stream Analytics, a pak vytvořte nové nasazení ve službě IoT Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Nastavení prostředí IoT Edge na vaše zařízení
Hraniční úlohy je možné nasadit na zařízení s Azure IoT Edge.
V takovém případě budete muset postupovat podle těchto kroků:
- Vytvoření služby Iot Hub.
- Nainstalujte modul runtime Dockeru a IoT Edge na hraničních zařízeních.
- Nastavit vaše zařízení jako **zařízení IoT Edge** ve službě IoT Hub.

Tyto kroky jsou popsané v dokumentaci k IoT Edge pro [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) nebo [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Nasazení Azure Stream Analytics na vaše zařízení IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Přidat Azure Stream Analytics k nasazení
- Na webu Azure Portal, otevřete službu IoT Hub, přejděte na **IoT Edge** a klikněte na zařízení, kterou chcete zacílit pro toto nasazení.
- Vyberte **nastavit moduly**a pak vyberte **+ přidat** a zvolte **modulem pro službu Azure Stream Analytics**.
- Vyberte předplatné a úlohy Azure Stream Analytics Edge, který jste vytvořili. Klikněte na Uložit.
![Přidat modul Azure Stream Analytics ve vašem nasazení](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Azure Stream Analytics během tohoto kroku, vytvoří složku s názvem "EdgeJobs" v kontejneru úložiště (pokud ho ještě neexistuje). U každého nasazení se vytvoří novou podsložku ve složce "EdgeJobs".
> Pokud chcete nasadit úlohy do hraničních zařízení, Azure Stream Analytics vytvoří sdílený přístupový podpis (SAS) pro soubor s definicí úlohy. Klíč SAS se bezpečně přenášejí do zařízení IoT Edge pomocí dvojčete zařízení. Doba platnosti tohoto klíče má tři roky od jeho vytvoření.


Další informace o nasazení IoT Edge najdete v tématu [na této stránce](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Nakonfigurujte trasy
IoT Edge poskytuje způsob, jak deklarativní směrování zpráv mezi moduly a mezi moduly a IoT Hub. Úplná syntaxe je popsán [tady](https://docs.microsoft.com/azure/iot-edge/module-composition).
Názvy vstupy a výstupy vytvořeny v úloze Azure Stream Analytics může sloužit jako koncové body pro směrování.  

###### <a name="example"></a>Příklad:

```json
{
"routes": {                                              
    "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
    "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream", 
    "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")" 
}
}   

```
Tento příklad ukazuje trasy pro scénář popsaný v následujícím obrázku. Obsahuje hraniční úlohy nazývá "**ASA**", se vstupem s názvem "**teploty**"a výstup s názvem"**výstraha**".
![Příklad diagramu směrování zpráv](media/stream-analytics-edge/edge-message-routing-example.png)

Tento příklad definuje následující trasy:
- Všechny zprávy z **tempSensor** posílá modul s názvem **ASA** se vstupem s názvem **teploty**,
- Všechny výstupy **ASA** modulu se odesílají do služby IoT Hub, který je spojený s tímto zařízením ($upstream),
- Všechny výstupy **ASA** modulu se odesílají do **ovládací prvek** koncový bod **tempSensor**.


## <a name="technical-information"></a>Technické informace
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Aktuální omezení pro úlohy IoT Edge ve srovnání s cloudové úlohy
Cílem je, aby parity mezi úlohy IoT Edge a cloudu. Většina funkcí jazyka SQL dotazu již jsou podporovány.
Ale tyto funkce zatím nepodporuje hraniční úlohy:
* Uživatelem definované funkce (UDF) v jazyce JavaScript. Jsou k dispozici v systému souborů UDF [ C# pro úlohy IoT Edge](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (preview).
* Uživatelem definované agregace (UDA).
* Funkce Azure ML
* Použití více než 14 agregací v jediném kroku.
* Formát AVRO pro vstup/výstup. V současné době jsou podporovány pouze CSV a JSON.
* Následující operátory SQL:
    * Geoprostorové operátory:
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
Ke spuštění Azure Stream Analytics na hraničních zařízeních IoT, je třeba zařízení, které můžou běžet [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

Azure Stream Analytics a Azure IoT Edge, použijte **Docker** kontejnery a poskytovat přenosné řešení, na kterém běží ve více hostitelských operačních systémech (Windows, Linux).

Azure Stream Analytics na hraničních zařízeních IoT je k dispozici jako Image Windows a Linuxem, spuštěné na x86 – x 64 nebo ARM (Advanced RISC počítače) architektury. 


### <a name="input-and-output"></a>Vstup a výstup
#### <a name="input-and-output-streams"></a>Vstupní a výstupní datové proudy
Azure Stream Analytics hraniční úlohy můžete získat vstupy a výstupy z jiných modulů běžící na zařízeních IoT Edge. Pro připojení z a do konkrétní moduly, můžete nastavit konfiguraci směrování v době nasazení. Další informace jsou uvedeny na [dokumentaci složení modulu IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition).

Pro vstupy a výstupy jsou podporované formáty CSV a JSON.

Pro každý vstupní a výstupní datový proud vytvoříte v úloze Azure Stream Analytics, se v nasazené modulu vytvoří odpovídající koncový bod. V trasách nasazení je možné tyto koncové body.

V současnosti jediný podporovaný vstupní datový proud a Centrum Edge jsou typy výstupu datového proudu. Odkaz na vstup podporuje referenční typ souboru. Jiné výstupy můžete získat přístup pomocí cloudové úlohy směru server-klient. Úlohu Stream Analytics hostované v Edgi například odešle výstup Edge Hub, který můžete výstup odeslat do služby IoT Hub. Druhý úlohu Azure Stream Analytics hostované v cloudu můžete použít se vstupem z centra IoT a výstup do Power BI nebo jiný typ výstupu.



##### <a name="reference-data"></a>Referenční data
Referenčními daty (označované také jako vyhledávací tabulky) je konečná datová sada, která jsou statická nebo pomalu, změna ze své podstaty. Slouží k vyhledávání a korelaci s datovým proudem. Chcete-li pomocí referenčních dat v úloze Azure Stream Analytics, budete obvykle používat [referenční Data připojení](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) v dotazu. Další informace najdete v tématu [pomocí referenčních dat pro vyhledávání ve službě Stream Analytics](stream-analytics-use-reference-data.md).

Je podporována pouze místní referenční data. Pokud nějaká úloha nasazená na zařízení IoT Edge, načte referenčních dat z cesty souborů definované uživatelem.

Chcete-li vytvořit úlohu s referenčními daty na hraničních zařízeních:

1. Vytvořte nový vstup pro úlohu.

2. Zvolte **odkazují na data** jako **typ zdroje**.

3. Máte odkaz na soubor dat připravené na zařízení. Pro kontejner Windows k referenčnímu datovému souboru na místním disku vložit a sdílet s kontejnerem Dockeru místním disku. Pro kontejner Linuxu vytvořte svazek Dockeru a naplnění souboru data na svazek.

4. Nastavte cestu k souboru. Hostitelským operačním systémem Windows a Windows container, použijte absolutní cestu: `E:\<PathToFile>\v1.csv`. Pro kontejner hostitelského operačního systému Windows a Linuxu nebo operačních systémů Linux a kontejner Linuxu, použijte cestu ve svazku: `<VolumeName>/file1.txt`.

![Nové referenčního datového vstupu pro úlohy Azure Stream Analytics na hraničních zařízeních IoT](./media/stream-analytics-edge/Reference-Data-New-Input.png)

Referenční data ve službě IoT Edge update se aktivuje nasazení. Po aktivaci, modulu Azure Stream Analytics vybere aktualizovaná data bez zastavení spuštěných úloh.

Existují dva způsoby, jak aktualizovat referenční data:
* Aktualizujte odkaz na cestu k datům v úloze Azure Stream Analytics z portálu Azure portal.
* Aktualizace nasazení IoT Edge.

## <a name="license-and-third-party-notices"></a>Licence a oznámení třetích stran
* [Azure Stream Analytics na hraničních zařízeních IoT licence](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Oznámení třetích stran pro Azure Stream Analytics na hraničních zařízeních IoT](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="get-help"></a>Podpora
Potřebujete další pomoc, zkuste [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Další postup

* [Další informace o Azure Iot Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [Azure Stream Analytics na hraničních zařízeních IoT kurz](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Vývoj úloh Stream Analytics Edge pomocí nástrojů sady Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [Implementujte CI/CD pro Stream Analytics pomocí rozhraní API](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
