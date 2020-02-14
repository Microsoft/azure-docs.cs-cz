---
title: Azure Stream Analytics na hraničních zařízeních IoT
description: Vytvořte hraniční úlohy ve službě Azure Stream Analytics a nasazovat je do zařízení s Azure IoT Edge.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 02/14/2020
ms.custom: seodec18
ms.openlocfilehash: 7e4a1cf43931b56cfdb3c31ffe870a07dbaa75af
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201751"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics na hraničních zařízeních IoT
 
Azure Stream Analytics (ASA) na IoT Edge umožňuje vývojářům nasadit inteligentní funkce analýzy téměř v reálném čase blíže zařízením IoT, aby bylo možné odemknout plný potenciál dat generovaných zařízením. Služba Azure Stream Analytics je určená k zajištění nízké latence, odolnosti, efektivního využití šířky pásma a dodržování předpisů. Podniky teď můžou nasadit řídicí logiku v blízkosti průmyslových provozů jako doplněk analýzy velkých objemů dat prováděné v cloudu.  

Azure Stream Analytics na IoT Edge běží v rámci [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) architektury. Po vytvoření úlohy v Azure Stream Analytics můžete nasadit a spravovat ho pomocí služby IoT Hub.

## <a name="scenarios"></a>Scénáře
![Vysokoúrovňový diagram IoT Edge](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Příkaz a řízení s nízkou latencí**: například systémy zabezpečení výroby musí reagovat na provozní data s nízkou latencí. S Azure Stream Analytics na hraničních zařízeních IoT můžete analyzovat senzor, data v téměř reálném čase a vydávat příkazy při detekci anomálií zastavit na počítači nebo aktivovat upozornění.
*   **Omezené připojení ke cloudu**: důležité systémy, jako je vzdálené řešení dolování, připojená plavidla nebo vrtné plavby, potřebují analyzovat a reagovat na data, i když je cloudové připojení přerušované. S Azure Stream Analytics streamování logiky běží nezávisle na připojení k síti a můžete zvolit, jaké můžete odesílat do cloudu pro další zpracování nebo úložiště.
* **Omezená šířka pásma**: objem dat vytvářených stroji Jet nebo připojenými automobily může být velký, aby před odesláním do cloudu bylo nutné data filtrovat nebo před jejich zpracováním zpracovat. Azure Stream Analytics můžete filtrovat nebo agregaci dat, kterou je potřeba odeslat do cloudu.
* **Dodržování předpisů**: dodržování předpisů může vyžadovat, aby některá data byla před odesláním do cloudu místně anonymní nebo agregovaná.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Hraniční úlohy ve službě Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Co je "hraniční" úlohy?

Hraniční úlohy ASA se spouštějí v kontejnerech nasazených do [zařízení Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Se skládají ze dvou částí:
1.  Cloudová část, která zodpovídá za definici úlohy: uživatelé definovat vstupů, výstup, dotazu a další nastavení (nefungujících událostí atd.) v cloudu.
2.  Modul, který běží na zařízení IoT. Obsahuje modul Azure Stream Analytics a přijímá definici úlohy z cloudu. 

Azure Stream Analytics používá k nasazení hraniční úlohy na zařízení služby IoT Hub. Další informace o [nasazení IoT Edge najdete tady](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Azure Stream Analytics hraniční úloha](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Pokyny k instalaci
Hlavní kroky jsou popsány v následující tabulce. Další podrobnosti jsou popsány v následujících částech.

|      |Krok   | Poznámky:   |
| ---   | ---   |  ---      |
| 1   | **Vytvoření kontejneru úložiště**   | Kontejnery úložiště se používají k uložení vaší definice úlohy, ve kterém jsou dostupné v zařízení IoT. <br>  Můžete znovu použít jakékoli existující kontejner úložiště.     |
| 2   | **Vytvoření hraniční úlohy ASA**   |  Vytvořte novou úlohu, vyberte možnost **Edge** jako **hostitelské prostředí**. <br> Tyto úlohy jsou vytvořili spravovaných z cloudu a spustit na zařízeních IoT Edge.     |
| 3   | **Nastavení IoT Edge prostředí na vašich zařízeních**   | Pokyny pro [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) nebo [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Nasazení ASA na vašich zařízeních IoT Edge**   |  Definice úlohy Azure Stream Analytics se exportují do kontejneru úložiště vytvořili dříve.       |

Postup najdete v [tomto podrobném kurzu](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) nasazení první úlohy ASA na IoT Edge. V následujícím videu by vám pomůže porozumět procesu ke spuštění úlohy Stream Analytics na hraničním zařízením IoT:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Vytvoření kontejneru úložiště
Kontejner úložiště je potřeba, abyste mohli exportovat dotaz Azure Stream Analytics zkompilován a konfiguraci úloh. Používá se ke konfiguraci image Dockeru pro Azure Stream Analytics s konkrétní dotaz. 
1. Při vytváření účtu úložiště z Azure Portal postupujte podle [těchto pokynů](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) . Abyste mohli všechny výchozí možnosti použití tento účet s Azure Stream Analytics.
2. V na nově vytvořený účet úložiště vytvořte kontejner úložiště objektů blob:
    1. Klikněte na **objekty blob**, pak na **+ kontejner**. 
    2. Zadejte název a zachovejte kontejner jako **soukromý**.

#### <a name="create-an-asa-edge-job"></a>Vytvořit úlohu Azure Stream Analytics Edge
> [!Note]
> Tento kurz se zaměřuje na vytvoření úlohy Azure Stream Analytics pomocí webu Azure portal. [Pomocí modulu plug-in sady Visual Studio můžete také vytvořit hraniční úlohu ASA](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs) .

1. Z portálu Azure portal vytvořte novou "Stream Analytics úlohu". [Sem zadejte přímý odkaz pro vytvoření nové úlohy ASA](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. Na obrazovce vytváření vyberte možnost **Edge** jako **hostitelské prostředí** (viz následující obrázek).

   ![Vytvoření úlohy Stream Analytics na hraničních zařízeních](media/stream-analytics-edge/create-asa-edge-job.png)
3. Definice úlohy
    1. **Definujte vstupní datový proud (y)** . Definujte jeden nebo několik vstupních datových proudů pro vaši úlohu.
    2. Definujte referenčních dat (volitelné).
    3. **Definovat výstupní datový proud (y)** . Definujte jeden nebo několik datových proudů výstupy pro vaši úlohu. 
    4. **Definujte dotaz**. Definování dotazu Azure Stream Analytics v cloudu s využitím editoru vnořeném do stránky. Kompilátor automaticky ověří syntaxi povolena pro Azure Stream Analytics edge. Můžete také testovat dotaz tak, že nahrajete ukázková data. 

4. V nabídce **nastavení IoT Edge** nastavte informace kontejneru úložiště.

5. Volitelná nastavení
    1. **Řazení událostí**. Na portálu můžete nakonfigurovat zásady mimo pořadí. Dokumentace je k dispozici [zde](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
    2. **Národní prostředí**. Nastavte formát internalization.



> [!Note]
> Po vytvoření nasazení Azure Stream Analytics exportuje definici úlohy do kontejneru úložiště. Tuto definici úlohy, zůstane během doby trvání nasazení. V důsledku toho pokud chcete aktualizovat úloha spuštěná na hraničních zařízeních, musíte upravit úlohy v Azure Stream Analytics, a pak vytvořte nové nasazení ve službě IoT Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Nastavení prostředí IoT Edge na vaše zařízení
Hraniční úlohy je možné nasadit na zařízení s Azure IoT Edge.
V takovém případě budete muset postupovat podle těchto kroků:
- Vytvoření služby Iot Hub.
- Nainstalujte modul runtime Dockeru a IoT Edge na hraničních zařízeních.
- Nastavte zařízení jako **IoT Edge zařízení** v IoT Hub.

Tyto kroky jsou popsané v dokumentaci k IoT Edge pro [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) nebo [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Nasazení Azure Stream Analytics na vaše zařízení IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Přidat Azure Stream Analytics k nasazení
- V Azure Portal otevřete IoT Hub, přejděte na **IoT Edge** a klikněte na zařízení, které chcete pro toto nasazení cílit.
- Vyberte **nastavit moduly**a pak vybrat **+ přidat** a zvolte **Azure Stream Analytics modul**.
- Vyberte předplatné a úlohy Azure Stream Analytics Edge, který jste vytvořili. Klikněte na Uložit.
![přidat modul ASA do nasazení](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Azure Stream Analytics během tohoto kroku, vytvoří složku s názvem "EdgeJobs" v kontejneru úložiště (pokud ho ještě neexistuje). U každého nasazení se vytvoří novou podsložku ve složce "EdgeJobs".
> Když nasadíte úlohu do zařízení IoT Edge, ASA vytvoří pro soubor definice úlohy sdílený přístupový podpis (SAS). Klíč SAS se bezpečně přenášejí do zařízení IoT Edge pomocí dvojčete zařízení. Doba platnosti tohoto klíče má tři roky od jeho vytvoření. Když aktualizujete úlohu IoT Edge, změní se SAS, ale verze image se nezmění. Po **aktualizaci**použijte pracovní postup nasazení a na zařízení se zaprotokoluje oznámení o aktualizaci.


Další informace o nasazeních IoT Edge najdete na [této stránce](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Nakonfigurujte trasy
IoT Edge poskytuje způsob, jak deklarativní směrování zpráv mezi moduly a mezi moduly a IoT Hub. Úplná syntaxe je popsána [zde](https://docs.microsoft.com/azure/iot-edge/module-composition).
Názvy vstupy a výstupy vytvořeny v úloze Azure Stream Analytics může sloužit jako koncové body pro směrování.  

###### <a name="example"></a>Příklad

```json
{
    "routes": {
        "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
        "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream",
        "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")"
    }
}

```
Tento příklad ukazuje trasy pro scénář popsaný v následujícím obrázku. Obsahuje hraniční úlohu s názvem "**ASA**" se vstupem "**teplota**" a výstup s názvem "**Alert**".
Ukázka ![ho diagramu](media/stream-analytics-edge/edge-message-routing-example.png) směrování zpráv

Tento příklad definuje následující trasy:
- Každá zpráva z **tempSensor** se pošle do modulu s názvem **ASA** na vstup s pojmenovanou **teplotou**,
- Všechny výstupy modulu **ASA** se odesílají do IoT Hub připojeného k tomuto zařízení ($upstream),
- Všechny výstupy modulu **ASA** jsou odesílány do **řídicího bodu ovládacího prvku** **tempSensor**.


## <a name="technical-information"></a>Technické informace
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Aktuální omezení pro úlohy IoT Edge ve srovnání s cloudové úlohy
Cílem je, aby parity mezi úlohy IoT Edge a cloudu. Podporuje se většina funkcí jazyka SQL Query a umožňuje spouštět stejnou logiku jak v cloudu, tak i v IoT Edge.
Ale tyto funkce zatím nepodporuje hraniční úlohy:
* Uživatelem definované funkce (UDF) v jazyce JavaScript. Systém souborů UDF je k dispozici v [ C# pro IoT Edge úlohy](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (Preview).
* Uživatelem definované agregace (UDA).
* Funkce Azure ML.
* Použití více než 14 agregací v jediném kroku.
* Formát AVRO pro vstup/výstup. V současné době jsou podporovány pouze CSV a JSON.
* Následující operátory SQL:
    * ODDÍL
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>Modul runtime a hardwarové požadavky
Chcete-li spustit příkaz ASA na IoT Edge, potřebujete zařízení, která lze spustit [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA a Azure IoT Edge používají kontejnery **Docker** k poskytnutí přenosného řešení, které běží na více hostitelských operačních systémech (Windows, Linux).

ASA v IoT Edge jsou k dispozici jako image Windows a Linux, které běží na architekturách X86-64 nebo ARM (Advanced RISC Machine). 


### <a name="input-and-output"></a>Vstup a výstup
#### <a name="input-and-output-streams"></a>Vstupní a výstupní datové proudy
Azure Stream Analytics hraniční úlohy můžete získat vstupy a výstupy z jiných modulů běžící na zařízeních IoT Edge. Pro připojení z a do konkrétní moduly, můžete nastavit konfiguraci směrování v době nasazení. Další informace jsou popsány v [dokumentaci k kompozici modulu IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition).

Pro vstupy a výstupy jsou podporované formáty CSV a JSON.

Pro každý vstupní a výstupní datový proud vytvoříte v úloze Azure Stream Analytics, se v nasazené modulu vytvoří odpovídající koncový bod. V trasách nasazení je možné tyto koncové body.

V současnosti jediný podporovaný vstupní datový proud a Centrum Edge jsou typy výstupu datového proudu. Odkaz na vstup podporuje referenční typ souboru. Jiné výstupy můžete získat přístup pomocí cloudové úlohy směru server-klient. Úlohu Stream Analytics hostované v Edgi například odešle výstup Edge Hub, který můžete výstup odeslat do služby IoT Hub. Druhý úlohu Azure Stream Analytics hostované v cloudu můžete použít se vstupem z centra IoT a výstup do Power BI nebo jiný typ výstupu.



##### <a name="reference-data"></a>Referenční data
Referenčními daty (označované také jako vyhledávací tabulky) je konečná datová sada, která jsou statická nebo pomalu, změna ze své podstaty. Slouží k vyhledávání a korelaci s datovým proudem. Pokud chcete používat referenční data v úloze Azure Stream Analytics, obecně se v dotazu použije [referenční datová připojení](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) . Další informace najdete v tématu [použití referenčních dat pro vyhledávání v Stream Analytics](stream-analytics-use-reference-data.md).

Je podporována pouze místní referenční data. Pokud nějaká úloha nasazená na zařízení IoT Edge, načte referenčních dat z cesty souborů definované uživatelem.

Chcete-li vytvořit úlohu s referenčními daty na hraničních zařízeních:

1. Vytvořte nový vstup pro úlohu.

2. Jako **typ zdroje**vyberte **referenční data** .

3. Máte odkaz na soubor dat připravené na zařízení. Pro kontejner Windows k referenčnímu datovému souboru na místním disku vložit a sdílet s kontejnerem Dockeru místním disku. Pro kontejner Linuxu vytvořte svazek Dockeru a naplnění souboru data na svazek.

4. Nastavte cestu k souboru. V případě hostitelských operačních systémů Windows a kontejnerů Windows použijte absolutní cestu: `E:\<PathToFile>\v1.csv`. V případě hostitelského operačního systému Windows a kontejneru Linux nebo kontejneru se systémem Linux a Linux použijte cestu ve svazku: `<VolumeName>/file1.txt`.

![Nové referenčního datového vstupu pro úlohy Azure Stream Analytics na hraničních zařízeních IoT](./media/stream-analytics-edge/Reference-Data-New-Input.png)

Referenční data ve službě IoT Edge update se aktivuje nasazení. Po aktivaci, modulu Azure Stream Analytics vybere aktualizovaná data bez zastavení spuštěných úloh.

Existují dva způsoby, jak aktualizovat referenční data:
* Aktualizujte odkaz na cestu k datům v úloze Azure Stream Analytics z portálu Azure portal.
* Aktualizace nasazení IoT Edge.

## <a name="license-and-third-party-notices"></a>Licence a oznámení třetích stran
* [Azure Stream Analytics na IoT Edge licence](https://go.microsoft.com/fwlink/?linkid=862827) 
* [Oznámení třetích stran pro Azure Stream Analytics na IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Informace o imagi Azure Stream Analytics modulu 

Informace o této verzi byly naposledy aktualizovány v 2019-06-27:

- Obrázek: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-amd64`
   - základní Image: Microsoft/dotNET: 2.1.6-runtime-Alpine 3.7
   - platformy
      - Architektura: amd64
      - operační systém: Linux
  
- Obrázek: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-arm32v7`
   - základní Image: Microsoft/dotNET: 2.1.6-runtime-Bionic-arm32v7
   - platformy
      - Architektura: ARM
      - operační systém: Linux
  
- Obrázek: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-windows-amd64`
   - základní Image: Microsoft/dotNET: 2.1.6-runtime-nanoserver-1809
   - platformy
      - Architektura: amd64
      - operační systém: Windows
      
      
## <a name="get-help"></a>Podpora
Pokud potřebujete další pomoc, zkuste [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky

* [Další informace o Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [Kurz k ASA v IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Vývoj úloh Stream Analytics Edge pomocí nástrojů sady Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [Implementace CI/CD pro Stream Analytics pomocí rozhraní API](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
