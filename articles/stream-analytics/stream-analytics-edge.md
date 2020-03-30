---
title: Azure Stream Analytics na hraničních zařízeních IoT
description: Vytvářejte hraniční úlohy ve Službě Azure Stream Analytics a nasazujte je do zařízení s Azure IoT Edge.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: seodec18
ms.openlocfilehash: 8bb1bd018866bda9270b78507f0462b6c4d4ea17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475888"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure Stream Analytics na hraničních zařízeních IoT
 
Azure Stream Analytics (ASA) na IoT Edge umožňuje vývojářům nasadit inteligentní funkce analýzy téměř v reálném čase blíže zařízením IoT, aby bylo možné odemknout plný potenciál dat generovaných zařízením. Služba Azure Stream Analytics je určená k zajištění nízké latence, odolnosti, efektivního využití šířky pásma a dodržování předpisů. Podniky teď můžou nasadit řídicí logiku v blízkosti průmyslových provozů jako doplněk analýzy velkých objemů dat prováděné v cloudu.  

Azure Stream Analytics na IoT Edge běží v rámci [Azure IoT Edge.](https://azure.microsoft.com/campaigns/iot-edge/) Po vytvoření úlohy v ASA, můžete nasadit a spravovat pomocí služby IoT Hub.

## <a name="scenarios"></a>Scénáře
![Diagram na vysoké úrovni IoT Edge](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Příkaz a řízení s nízkou latencí**: Například výrobní bezpečnostní systémy musí reagovat na provozní data s velmi nízkou latencí. S ASA na IoT Edge, můžete analyzovat data senzorů v téměř reálném čase a vydávat příkazy při detekci anomálií zastavit počítač nebo aktivovat výstrahy.
*   **Omezené připojení ke cloudu**: Kritické systémy mise, jako jsou zařízení pro vzdálené těžbu, připojená plavidla nebo pobřežní vrty, musí analyzovat data a reagovat na ně, i když je připojení ke cloudu přerušované. S ASA, vaše logika streamování běží nezávisle na připojení k síti a můžete si vybrat, co odešlete do cloudu pro další zpracování nebo úložiště.
* **Omezená šířka pásma**: Objem dat vytvořených proudovými motory nebo připojenými automobily může být tak velký, že data musí být před odesláním do cloudu filtrována nebo předem zpracována. Pomocí ASA můžete filtrovat nebo agregovat data, která je potřeba odeslat do cloudu.
* **Dodržování předpisů**: Dodržování předpisů může vyžadovat, aby některá data byla před odesláním do cloudu místně anonymizována nebo agregována.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Hraniční úlohy v Azure Stream Analytics
### <a name="what-is-an-edge-job"></a>Co je to "okraj" práce?

Úlohy ASA Edge se spouštějí v kontejnerech nasazených do [zařízení Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Skládají se ze dvou částí:
1.  Cloudová část, která je zodpovědná za definici úlohy: uživatelé definují vstupy, výstup, dotaz a další nastavení (události mimo pořadí atd.) v cloudu.
2.  Modul spuštěný na vašich zařízeních IoT. Obsahuje modul ASA a přijímá definici úlohy z cloudu. 

ASA používá Službu IoT Hub k nasazení hraničních úloh do zařízení. Další informace o [nasazení IoT Edge naleznete zde](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Úloha Azure Stream Analytics Edge](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Pokyny k instalaci
Kroky vysoké úrovně jsou popsány v následující tabulce. Další podrobnosti jsou uvedeny v následujících částech.

|      |Krok   | Poznámky   |
| ---   | ---   |  ---      |
| 1   | **Vytvoření kontejneru úložiště**   | Kontejnery úložiště se používají k uložení definice úlohy, kde k nim mají přístup vaše zařízení IoT. <br>  Můžete znovu použít libovolný existující kontejner úložiště.     |
| 2   | **Vytvoření úlohy asa okraj**   |  Vytvořte novou úlohu, vyberte **Edge** jako **hostitelské prostředí**. <br> Tyto úlohy se vytvářejí a spravují z cloudu a běží na vlastních zařízeních IoT Edge.     |
| 3   | **Nastavení prostředí IoT Edge na vašich zařízeních**   | Pokyny pro [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) nebo [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Nasazení ASA na vašich zařízeních IoT Edge**   |  Definice úlohy ASA se exportuje do kontejneru úložiště vytvořeného dříve.       |

Pomocí [tohoto podrobného kurzu](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) můžete nasadit svou první úlohu ASA na IoT Edge. Následující video by vám mělo pomoci pochopit proces spuštění úlohy Stream Analytics na hraničním zařízení IoT:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Vytvoření kontejneru úložiště
Kontejner úložiště je nutné pro export ASA kompilovaný dotaz a konfigurace úlohy. Slouží ke konfiguraci image asa dockeru s konkrétním dotazem. 
1. Podle [těchto pokynů](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) vytvořte účet úložiště z webu Azure Portal. Můžete zachovat všechny výchozí možnosti pro použití tohoto účtu s ASA.
2. V nově vytvořeném účtu úložiště vytvořte kontejner úložiště objektů blob:
    1. Klikněte na **Objekty blobs**, pak **+ Kontejner**. 
    2. Zadejte název a ponechte kontejner jako **soukromý**.

#### <a name="create-an-asa-edge-job"></a>Vytvoření úlohy ASA Edge
> [!Note]
> Tento kurz se zaměřuje na vytváření úloh ASA pomocí portálu Azure. Můžete také [použít modul plug-in sady Visual Studio k vytvoření úlohy ASA Edge.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. Na webu Azure Portal vytvořte novou úlohu Stream Analytics. [Přímý odkaz na vytvoření nové úlohy ASA zde](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. Na obrazovce vytvoření vyberte **Edge** jako **hostitelské prostředí** (viz následující obrázek)

   ![Vytvoření úlohy Stream Analytics na hraně](media/stream-analytics-edge/create-asa-edge-job.png)
3. Definice úlohy
    1. **Definujte vstupní proud (y).** Definujte jeden nebo více vstupních datových proudů pro svou úlohu.
    2. Definujte referenční data (nepovinné).
    3. **Definujte výstupní proud (výstupy).** Definujte jeden nebo několik výstupů datových proudů pro vaši úlohu. 
    4. **Definujte dotaz**. Definujte dotaz ASA v cloudu pomocí vsazený editor. Kompilátor automaticky zkontroluje syntaxi povolenou pro okraj ASA. Dotaz můžete také otestovat nahráním ukázkových dat. 

4. Nastavte informace o kontejneru úložiště v nabídce **nastavení IoT Edge.**

5. Nastavení volitelných nastavení
    1. **Řazení událostí**. Zásady mimo pořadí můžete nakonfigurovat na portálu. Dokumentace je k dispozici [zde](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
    2. **Národní prostředí**. Nastavte formát internalizace.



> [!Note]
> Při vytvoření nasazení ASA exportuje definici úlohy do kontejneru úložiště. Tato definice úlohy zůstává stejná po dobu trvání nasazení. V důsledku toho pokud chcete aktualizovat úlohu spuštěnou na okraji, musíte upravit úlohu v ASA a pak vytvořit nové nasazení v centru IoT Hub.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Nastavení prostředí IoT Edge na svých zařízeních
Hraniční úlohy se můžou nasazovat na zařízeních se systémem Azure IoT Edge.
K tomu je třeba provést následující kroky:
- Vytvořte aplikaci Lho.A.SoCenter.
- Nainstalujte docker a ioT edge runtime na svých hraničních zařízeních.
- Nastavte svá zařízení jako **zařízení IoT Edge** v IoT Hubu.

Tyto kroky jsou popsány v dokumentaci k IoT Edge pro [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) nebo [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Nasazení ASA na vašich zařízeních IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Přidání asa do vašeho nasazení
- Na webu Azure Portal otevřete Službu IoT Hub, přejděte na **IoT Edge** a klikněte na zařízení, na které chcete pro toto nasazení cílit.
- Vyberte **Nastavit moduly**, pak vyberte **+ Přidat** a zvolte Modul Azure **Stream Analytics**.
- Vyberte předplatné a úlohu ASA Edge, kterou jste vytvořili. Klikněte na Uložit.
![Přidání modulu ASA do nasazení](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Během tohoto kroku ASA vytvoří složku s názvem "EdgeJobs" v kontejneru úložiště (pokud již neexistuje). Pro každé nasazení je vytvořena nová podsložka ve složce "EdgeJobs".
> Při nasazení úlohy do zařízení IoT Edge, ASA vytvoří sdílený přístupový podpis (SAS) pro soubor definice úlohy. Klíč SAS se bezpečně přenáší do zařízení IoT Edge pomocí dvojčete zařízení. Vypršení platnosti tohoto klíče je tři roky ode dne jeho vzniku. Při aktualizaci úlohy IoT Edge se změní SAS, ale verze image se nezmění. Po **aktualizaci**postupujte podle pracovního postupu nasazení a je do zařízení přihlášeno oznámení o aktualizaci.


Další informace o nasazeníi IoT Edge najdete na [této stránce](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Konfigurace tras
IoT Edge poskytuje způsob, jak deklarativně směrovat zprávy mezi moduly a mezi moduly a IoT Hub. Úplná syntaxe je popsána [zde](https://docs.microsoft.com/azure/iot-edge/module-composition).
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
Tento příklad ukazuje trasy pro scénář popsaný na následujícím obrázku. Obsahuje hraniční úlohu nazvanou **" ASA**", se vstupem s názvem "**teplota**" a výstupem s názvem "**výstraha**".
![Příklad diagramu směrování zpráv](media/stream-analytics-edge/edge-message-routing-example.png)

Tento příklad definuje následující trasy:
- Každá zpráva z **tempSensor** je odeslána do modulu s názvem **ASA** na vstup pojmenovanou **teplotu**,
- Všechny výstupy **modulu ASA** jsou odesílány do IoT Hubu připojeného k tomuto zařízení ($upstream),
- Všechny výstupy **ASA** modulu jsou odesílány do **řídicího** koncového bodu **tempSensor**.


## <a name="technical-information"></a>Technické informace
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Aktuální omezení pro úlohy IoT Edge ve srovnání s úlohami v cloudu
Cílem je mít parity mezi úlohami IoT Edge a cloudovými úlohami. Většina funkcí dotazovacího jazyka SQL jsou podporovány, povolení ke spuštění stejné logiky na cloudu i IoT Edge.
Následující funkce však ještě nejsou podporovány pro hraniční úlohy:
* Uživatelem definované funkce (UDF) v Jazyce JavaScript. UDF jsou k dispozici v [C# pro úlohy IoT Edge](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (náhled).
* Uživatelem definované agregace (UDA).
* Funkce Azure ML.
* Použití více než 14 agregátů v jednom kroku.
* Formát AVRO pro vstup/výstup. V současné době jsou podporovány pouze CSV a JSON.
* Následující operátory SQL:
    * ODDÍL PODLE
    * GetMetadataPropertyValue
* Zásady pozdního příjezdu

### <a name="runtime-and-hardware-requirements"></a>Požadavky na běhanu a hardware
Chcete-li spustit ASA na IoT Edge, potřebujete zařízení, která můžou spouštět [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

ASA a Azure IoT Edge používají kontejnery **Dockeru** k poskytování přenosného řešení, které běží na více hostitelských operačních systémech (Windows, Linux).

ASA na IoT Edge je k dispozici jako windows a linuxové image, běžící na architekturách x86-64 nebo ARM (Advanced RISC Machines). 


### <a name="input-and-output"></a>Vstup a výstup
#### <a name="input-and-output-streams"></a>Vstupní a výstupní datové proudy
Úlohy ASA Edge můžou získat vstupy a výstupy z jiných modulů spuštěných na zařízeních IoT Edge. Chcete-li se připojit z a k určitým modulům, můžete nastavit konfiguraci směrování v době nasazení. Další informace jsou popsány v dokumentaci ke [složení modulu IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition).

Pro vstupy i výstupy jsou podporovány formáty CSV a JSON.

Pro každý vstupní a výstupní datový proud, který vytvoříte v úloze ASA, se vytvoří odpovídající koncový bod ve vašem nasazeném modulu. Tyto koncové body lze použít v trasách nasazení.

V současné době jsou pouze podporované typy vstupu datového proudu a výstupu datového proudu Edge Hub. Referenční vstup podporuje typ souboru odkazu. Další výstupy lze dosáhnout pomocí cloudové úlohy po proudu. Například úloha Stream Analytics hostovaná v Edge odešle výstup do Edge Hubu, který pak může odesílat výstup do služby IoT Hub. Můžete použít druhou úlohu azure stream analytics hostovnou v cloudu se vstupem z IoT Hubu a výstupem do Power BI nebo jiného typu výstupu.



##### <a name="reference-data"></a>Referenční údaje
Referenční data (označovaná také jako vyhledávací tabulka) je omezená datová sada, která je statická nebo pomalu se měnící povahy. Používá se k provedení vyhledávání nebo ke korelaci s datovým proudem. Chcete-li využít referenční data v úloze Azure Stream Analytics, budete obecně používat [referenční data PŘIPOJIT](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) v dotazu. Další informace naleznete [v tématu Použití referenčních dat pro vyhledávání v Stream Analytics](stream-analytics-use-reference-data.md).

Jsou podporována pouze místní referenční data. Když je úloha nasazena do zařízení IoT Edge, načte referenční data z cesty uživatelem definovaného souboru.

Vytvoření úlohy s referenčními daty na hraničním zařízení:

1. Vytvořte nový vstup pro vaši úlohu.

2. Jako **typ zdroje**zvolte **Referenční data** .

3. Připravte si na zařízení referenční datový soubor. U kontejneru windows umístěte referenční datový soubor na místní jednotku a sdílejte místní jednotku s kontejnerem Dockeru. Pro linuxový kontejner vytvořte svazek Dockeru a naplňte datový soubor na svazku.

4. Nastavte cestu k souboru. Pro operační systém Windows Host a kontejner `E:\<PathToFile>\v1.csv`Windows použijte absolutní cestu: . Pro kontejner operačního systému Windows Host a Linux nebo kontejner u operačního systému Linux a Linux použijte cestu ve svazku: `<VolumeName>/file1.txt`.

![Nový vstup referenčních dat pro úlohu Azure Stream Analytics na IoT Edge](./media/stream-analytics-edge/Reference-Data-New-Input.png)

Referenční data o aktualizaci IoT Edge se aktivují nasazením. Po aktivaci modulASA vybere aktualizovaná data bez zastavení spuštěné úlohy.

Referenční data lze aktualizovat dvěma způsoby:
* Aktualizujte cestu referenčních dat v úloze ASA z webu Azure Portal.
* Aktualizujte nasazení IoT Edge.

## <a name="license-and-third-party-notices"></a>Oznámení o licencích a třetích stranách
* [Azure Stream Analytics na licenci IoT Edge](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Oznámení třetích stran pro Azure Stream Analytics na IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Informace o image modulu Azure Stream Analytics 

Tyto informace o verzi byly naposledy aktualizovány na 2019-06-27:

- Obrázek: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-amd64`
   - základní obrázek: microsoft/dotnet:2.1.6-runtime-alpine3.7
   - Platforma:
      - architektura: amd64
      - os: linux
  
- Obrázek: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-arm32v7`
   - základní obrázek: microsoft/dotnet:2.1.6-runtime-bionic-arm32v7
   - Platforma:
      - architektura: rameno
      - os: linux
  
- Obrázek: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-windows-amd64`
   - základní obrázek: microsoft/dotnet:2.1.6-runtime-nanoserver-1809
   - Platforma:
      - architektura: amd64
      - os: okna
      
      
## <a name="get-help"></a>Podpora
Další pomoc našlápnete na [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky

* [Další informace o Azure Iot Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [ASA na IoT Edge kurz](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Vývoj úloh Stream Analytics Edge pomocí nástrojů Visual Studia](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [Implementace CI/CD pro Stream Analytics pomocí API](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
