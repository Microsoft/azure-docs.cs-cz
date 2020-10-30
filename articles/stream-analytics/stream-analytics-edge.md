---
title: Azure Stream Analytics na hraničních zařízeních IoT
description: Vytvářejte hraniční úlohy v Azure Stream Analytics a nasaďte je do zařízení s Azure IoT Edge.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: how-to
ms.date: 10/29/2020
ms.custom: seodec18
ms.openlocfilehash: 7a084b2d0582f53d4372ba3332194629ad29a4ec
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041868"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics na hraničních zařízeních IoT
 
Azure Stream Analytics (ASA) na IoT Edge umožňuje vývojářům nasadit inteligentní funkce analýzy téměř v reálném čase blíže zařízením IoT, aby bylo možné odemknout plný potenciál dat generovaných zařízením. Služba Azure Stream Analytics je určená k zajištění nízké latence, odolnosti, efektivního využití šířky pásma a dodržování předpisů. Podniky teď můžou nasadit řídicí logiku v blízkosti průmyslových provozů jako doplněk analýzy velkých objemů dat prováděné v cloudu.  

Azure Stream Analytics na IoT Edge běží v rámci [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) architektury. Jakmile se úloha ve službě ASA vytvoří, můžete ji nasadit a spravovat pomocí IoT Hub.

## <a name="scenarios"></a>Scénáře
![Diagram vysoké úrovně IoT Edge](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Příkaz a řízení s nízkou latencí** : například systémy zabezpečení výroby musí reagovat na provozní data s nízkou latencí. Pomocí ASA v IoT Edge můžete analyzovat data senzorů téměř v reálném čase a příkazy vydávat se při detekci anomálií za účelem zastavení počítače nebo spuštění výstrah.
*   **Omezené připojení ke cloudu** : důležité systémy, jako je vzdálené řešení dolování, připojená plavidla nebo vrtné plavby, potřebují analyzovat a reagovat na data, i když je cloudové připojení přerušované. Pomocí ASA vaše logika streamování běží nezávisle na připojení k síti a vy můžete zvolit, co odesíláte do cloudu pro další zpracování nebo uložení.
* **Omezená šířka pásma** : objem dat vytvářených stroji Jet nebo připojenými automobily může být velký, aby před odesláním do cloudu bylo nutné data filtrovat nebo před jejich zpracováním zpracovat. Pomocí ASA můžete filtrovat nebo agregovat data, která je třeba odeslat do cloudu.
* **Dodržování předpisů** : dodržování předpisů může vyžadovat, aby některá data byla před odesláním do cloudu místně anonymní nebo agregovaná.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Hraniční úlohy v Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Co je to hraniční úloha?

Hraniční úlohy ASA se spouštějí v kontejnerech nasazených do [zařízení Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Skládají se ze dvou částí:
1.  Cloudová součást, která zodpovídá za definici úlohy: uživatelé definují vstupy, výstupy, dotazy a další nastavení (události mimo pořadí atd.) v cloudu.
2.  Modul spuštěný ve vašich zařízeních IoT. Obsahuje modul ASA a přijímá definici úlohy z cloudu. 

ASA používá IoT Hub k nasazení hraničních úloh do zařízení. Další informace o [nasazení IoT Edge najdete tady](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Úloha Azure Stream Analytics Edge](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Pokyny k instalaci
Kroky vysoké úrovně jsou popsány v následující tabulce. Další podrobnosti jsou uvedeny v následujících oddílech.

| Krok | Poznámky |
| --- | --- |
| **Vytvoření kontejneru úložiště** | Kontejnery úložiště slouží k uložení definice úlohy, kde k nim budou mít k dispozici zařízení IoT. <br>  Můžete znovu použít libovolný existující kontejner úložiště. |
| **Vytvoření hraniční úlohy ASA** | Vytvořte novou úlohu, vyberte možnost **Edge** jako **hostitelské prostředí** . <br> Tyto úlohy se vytvářejí/spravují z cloudu a spouštějí se na vlastních IoT Edge zařízeních. |
| **Nastavení IoT Edge prostředí na vašich zařízeních** | Pokyny pro [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) nebo [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).|
| **Nasazení ASA na vašich zařízeních IoT Edge** | Definice úlohy ASA se exportuje do kontejneru úložiště, který jste vytvořili dříve. |

Postup najdete v [tomto podrobném kurzu](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) nasazení první úlohy ASA na IoT Edge. Následující video by vám mělo porozumět procesu spuštění Stream Analytics úlohy na hraničním zařízení IoT:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Vytvoření kontejneru úložiště
Aby bylo možné exportovat kompilovaný dotaz ASA a konfiguraci úlohy, je vyžadován kontejner úložiště. Používá se ke konfiguraci obrázku programu ASA Docker s vaším konkrétním dotazem. 
1. Při vytváření účtu úložiště z Azure Portal postupujte podle [těchto pokynů](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) . U všech výchozích možností můžete zachovat použití tohoto účtu s ASA.
2. V nově vytvořeném účtu úložiště vytvořte kontejner úložiště objektů BLOB:
    1. Klikněte na **objekty blob** , pak na **+ kontejner** . 
    2. Zadejte název a zachovejte kontejner jako **soukromý** .

#### <a name="create-an-asa-edge-job"></a>Vytvoření hraniční úlohy ASA
> [!Note]
> Tento kurz se zaměřuje na vytvoření úlohy ASA pomocí Azure Portal. [Pomocí modulu plug-in sady Visual Studio můžete také vytvořit hraniční úlohu ASA](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs) .

1. Z Azure Portal vytvořte novou "Stream Analytics úlohu". [Sem zadejte přímý odkaz pro vytvoření nové úlohy ASA](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. Na obrazovce vytváření vyberte možnost **Edge** jako **hostitelské prostředí** (viz následující obrázek).

   ![Vytvořit úlohu Stream Analytics na hraničních zařízeních](media/stream-analytics-edge/create-asa-edge-job.png)
3. Definice úlohy
    1. **Definujte vstupní datový proud (y)** . Definujte jeden nebo několik vstupních datových proudů pro vaši úlohu.
    2. Definujte referenční data (volitelné).
    3. **Definovat výstupní datový proud (y)** . Definujte jeden nebo několik výstupních datových proudů pro vaši úlohu. 
    4. **Definujte dotaz** . Pomocí vloženého editoru definujte dotaz ASA v cloudu. Kompilátor automaticky kontroluje syntaxi povolenou pro inedge ASA. Dotaz můžete také otestovat nahráním ukázkových dat. 

4. V nabídce **nastavení IoT Edge** nastavte informace kontejneru úložiště.

5. Nastavit volitelná nastavení
    1. **Řazení událostí** . Zásady mimo pořadí můžete nakonfigurovat na portálu. Dokumentace je k dispozici [zde](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
    2. **Národní prostředí** . Nastavte formát pro Provisioning.



> [!Note]
> Po vytvoření nasazení služba ASA Exportuje definici úlohy do kontejneru úložiště. Tato definice úlohy zůstává stejná i po dobu trvání nasazení. V takovém případě je třeba v případě, že chcete aktualizovat úlohu běžící na hraničních zařízeních, Upravit úlohu v ASA a pak vytvořit nové nasazení v IoT Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Nastavení IoT Edge prostředí na vašich zařízeních
Hraniční úlohy je možné nasadit na zařízeních s Azure IoT Edge.
V takovém případě je třeba provést následující kroky:
- Vytvořte centrum IoT.
- Nainstalujte do hraničních zařízení Docker a IoT Edge runtime.
- Nastavte zařízení jako **IoT Edge zařízení** v IoT Hub.

Tyto kroky jsou popsané v dokumentaci k IoT Edge pro [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) nebo [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Nasazení ASA na vašich zařízeních s IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Přidat ASA do nasazení
- V Azure Portal otevřete IoT Hub, přejděte na **IoT Edge** a klikněte na zařízení, které chcete pro toto nasazení cílit.
- Vyberte **nastavit moduly** a pak vybrat **+ přidat** a zvolte **Azure Stream Analytics modul** .
- Vyberte předplatné a hraniční úlohu ASA, kterou jste vytvořili. Klikněte na Uložit.
![Přidat modul ASA do nasazení](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Během tohoto kroku služba ASA vytvoří v kontejneru úložiště složku s názvem "EdgeJobs" (Pokud již neexistuje). Pro každé nasazení je ve složce EdgeJobs vytvořena nová podsložka.
> Když nasadíte úlohu do zařízení IoT Edge, ASA vytvoří pro soubor definice úlohy sdílený přístupový podpis (SAS). Klíč SAS se bezpečně přenáší do zařízení IoT Edge pomocí vlákna zařízení. Vypršení platnosti tohoto klíče je tři roky od dne jeho vytvoření. Když aktualizujete úlohu IoT Edge, změní se SAS, ale verze image se nezmění. Po **aktualizaci** použijte pracovní postup nasazení a na zařízení se zaprotokoluje oznámení o aktualizaci.


Další informace o nasazeních IoT Edge najdete na [této stránce](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Konfigurace tras
IoT Edge poskytuje způsob deklarativního směrování zpráv mezi moduly a mezi moduly a IoT Hub. Úplná syntaxe je popsána [zde](https://docs.microsoft.com/azure/iot-edge/module-composition).
Názvy vstupů a výstupů vytvořených v úloze ASA lze použít jako koncové body pro směrování.  

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
Tento příklad ukazuje trasy pro scénář popsaný na následujícím obrázku. Obsahuje hraniční úlohu s názvem " **ASA** " se vstupem " **teplota** " a výstup s názvem " **Alert** ".
![Příklad diagramu směrování zpráv](media/stream-analytics-edge/edge-message-routing-example.png)

Tento příklad definuje následující trasy:
- Každá zpráva z **tempSensor** se pošle do modulu s názvem **ASA** na vstup s pojmenovanou **teplotou** ,
- Všechny výstupy modulu **ASA** se odesílají do IoT Hub připojeného k tomuto zařízení ($upstream),
- Všechny výstupy modulu **ASA** jsou odesílány do **řídicího bodu ovládacího prvku** **tempSensor** .


## <a name="technical-information"></a>Technické informace
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Aktuální omezení pro úlohy IoT Edge v porovnání s úlohami v cloudu
Cílem je mít paritu mezi IoT Edge úlohami a cloudovým úlohami. Podporuje se většina funkcí jazyka SQL Query a umožňuje spouštět stejnou logiku jak v cloudu, tak i v IoT Edge.
Pro úlohy Edge se zatím nepodporují následující funkce:
* Uživatelsky definované funkce (UDF) v JavaScriptu. Systém souborů UDF je k dispozici v [jazyce C# pro IoT Edge úlohy](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (Preview).
* Uživatelsky definované agregace (UDA).
* Funkce Azure ML.
* Použití více než 14 agregací v jednom kroku.
* Formát AVRO pro vstup a výstup. V současné době jsou podporovány pouze CSV a JSON.
* Následující operátory SQL:
    * ROZDĚLIT PODLE
    * GetMetadataPropertyValue
* Zásady opožděného doručení

### <a name="runtime-and-hardware-requirements"></a>Požadavky na modul runtime a hardware
Chcete-li spustit příkaz ASA na IoT Edge, potřebujete zařízení, která lze spustit [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA a Azure IoT Edge používají kontejnery **Docker** k poskytnutí přenosného řešení, které běží na více hostitelských operačních systémech (Windows, Linux).

ASA v IoT Edge jsou k dispozici jako image Windows a Linux, které běží na architekturách X86-64 nebo ARM (Advanced RISC Machine). 


### <a name="input-and-output"></a>Vstup a výstup
#### <a name="input-and-output-streams"></a>Vstupní a výstupní proudy
Hraniční úlohy ASA mohou získat vstupy a výstupy z jiných modulů spuštěných v zařízeních IoT Edge. Pokud se chcete připojit z a ke konkrétním modulům, můžete nakonfigurovat konfiguraci směrování v době nasazení. Další informace jsou popsány v [dokumentaci k kompozici modulu IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition).

Pro vstupy i výstupy se podporují formáty CSV a JSON.

Pro každý vstupní a výstupní datový proud, který vytvoříte v úloze ASA, se v nasazeném modulu vytvoří odpovídající koncový bod. Tyto koncové body lze použít v cestách nasazení.

V současné době jsou jedinými podporovanými typy výstupů streamu a výstupními datovými proudy hraniční centrum. Reference Input podporuje typ referenčního souboru. K dalším výstupům se dá dostat pomocí podřízené úlohy cloudu. Například úloha Stream Analytics hostovaná na Edge odesílá výstup do hraničního centra, které pak může odeslat výstup do IoT Hub. Můžete použít druhý cloud hostovaný Azure Stream Analytics úlohy se vstupem z IoT Hub a výstupem do Power BI nebo jiný typ výstupu.



##### <a name="reference-data"></a>Referenční data
Referenční data (označovaná také jako vyhledávací tabulka) jsou konečnou datovou sadu, která je statická nebo pomalá měnící se v podstatě. Slouží k vyhledávání nebo ke korelaci s datovým proudem. Pokud chcete používat referenční data v úloze Azure Stream Analytics, obecně se v dotazu použije [referenční datová připojení](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) . Další informace najdete v tématu [použití referenčních dat pro vyhledávání v Stream Analytics](stream-analytics-use-reference-data.md).

Podporovaná jsou jenom místní referenční data. Při nasazení úlohy do IoT Edge zařízení načte referenční data z uživatelsky definované cesty k souboru.

Vytvoření úlohy s referenčními daty na hraničních zařízeních:

1. Vytvořte nový vstup pro vaši úlohu.

2. Jako **typ zdroje** vyberte **referenční data** .

3. V zařízení musí být připravený referenční datový soubor. V případě kontejneru Windows umístěte soubor referenčních dat na místní disk a sdílejte místní disk s kontejnerem Docker. V případě kontejneru pro Linux vytvořte svazek Docker a naplňte do něj datový soubor.

4. Nastavte cestu k souboru. V případě hostitelského operačního systému Windows a kontejneru Windows použijte absolutní cestu: `E:\<PathToFile>\v1.csv` . V případě hostitelského operačního systému Windows a kontejneru Linux nebo kontejneru se systémem Linux a Linux použijte cestu ve svazku: `<VolumeName>/file1.txt` .

![Nový vstup referenčních dat pro úlohu Azure Stream Analytics v IoT Edge](./media/stream-analytics-edge/Reference-Data-New-Input.png)

Referenční data v IoT Edge aktualizaci spouští nasazení. Po aktivaci modul ASA vybere aktualizovaná data bez zastavení spuštěné úlohy.

Existují dva způsoby, jak aktualizovat referenční data:
* Aktualizujte cestu referenčních dat v úloze ASA z Azure Portal.
* Aktualizujte nasazení IoT Edge.

## <a name="license-and-third-party-notices"></a>Licence a oznámení třetích stran
* [Azure Stream Analytics na IoT Edge licence](https://go.microsoft.com/fwlink/?linkid=862827) 
* [Oznámení třetích stran pro Azure Stream Analytics na IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Informace o imagi Azure Stream Analytics modulu 

Informace o této verzi byly naposledy aktualizovány v 2019-06-27:

- Obrázek: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-amd64`
   - základní Image: mcr.microsoft.com/dotnet/core/runtime:2.1.13-alpine
   - platformy
      - Architektura: amd64
      - operační systém: Linux
 
- Obrázek: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm32v7`
   - základní Image: mcr.microsoft.com/dotnet/core/runtime:2.1.13-bionic-arm32v7
   - platformy
      - Architektura: ARM
      - operační systém: Linux
 
- Obrázek: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm64`
   - základní Image: mcr.microsoft.com/dotnet/core/runtime:3.0-bionic-arm64v8
   - platformy
      - Architektura: arm64
      - operační systém: Linux
      
      
## <a name="get-help"></a>Získání pomoci
Pokud chcete získat další pomoc, zkuste [Azure Stream Analytics na stránce s dotazem pro Microsoft Q&](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html).

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
