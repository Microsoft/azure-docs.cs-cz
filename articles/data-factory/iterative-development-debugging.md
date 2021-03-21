---
title: Iterativní vývoj a ladění v Azure Data Factory
description: Naučte se vyvíjet a ladit kanály Data Factory v uživatelském prostředí ADF iterativním způsobem.
ms.date: 02/23/2021
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: kromerm
ms.author: makromer
ms.openlocfilehash: ef47d311f5f096db962ea27792e7871dbf0ef81a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712942"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Iterativní vývoj a ladění pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory umožňuje iterativní vývoj a ladění Data Factory kanálů při vývoji řešení pro integraci dat. Tyto funkce umožňují otestovat změny před vytvořením žádosti o přijetí změn nebo jejich publikování ve službě Data Factory. 

Po dobu osmi minut a ukázku této funkce se podívejte na toto video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="debugging-a-pipeline"></a>Ladění kanálu

Při vytváření plátna kanálu můžete své aktivity testovat pomocí možnosti **ladění** . Při spuštění testů není nutné publikovat změny do objektu pro vytváření dat před výběrem příkazu **ladit**. Tato funkce je užitečná ve scénářích, ve kterých se chcete ujistit, že změny fungují podle očekávání, než aktualizujete pracovní postup datové továrny.

![Možnost ladění na plátně kanálu](media/iterative-development-debugging/iterative-development-1.png)

Když je kanál spuštěný, můžete zobrazit výsledky každé aktivity na kartě **výstup** plátna kanálu.

Zobrazte výsledky testovacích běhů v okně **výstup** plátna kanálu.

![Okno výstup plátna kanálu](media/iterative-development-debugging/iterative-development-2.png)

Po úspěšném spuštění testu přidejte do kanálu další aktivity a pokračujte v ladění iterativním způsobem. Můžete také **Zrušit** testovací běh, dokud probíhá.

> [!IMPORTANT]
> Když vyberete **ladit** , kanál se ve skutečnosti spustí. Například pokud kanál obsahuje aktivitu kopírování, testovací běh zkopíruje data ze zdroje do cíle. V důsledku toho doporučujeme, abyste při ladění používali testovací složky v aktivitách kopírování a dalších aktivitách. Po ladění kanálu přepněte do vlastních složek, které chcete použít při normálním provozu.

### <a name="setting-breakpoints"></a>Nastavení zarážek

Azure Data Factory umožňuje ladit kanál, dokud nedosáhnete určité aktivity na plátně kanálu. Umístěte zarážku na aktivitu, do které chcete testovat, a vyberte **ladit**. Data Factory zajistí, že se test spustí pouze do aktivity zarážky na plátně kanálu. Tento *ladicí program* je použitelný, dokud nechcete testovat celý kanál, ale pouze podmnožinu aktivit uvnitř kanálu.

![Zarážky na plátně kanálu](media/iterative-development-debugging/iterative-development-3.png)

Chcete-li nastavit zarážku, vyberte element na plátně kanálu. *Ladění, dokud* se možnost neobjeví jako prázdný červený kruh v pravém horním rohu elementu.

![Před nastavením zarážky pro vybraný element](media/iterative-development-debugging/iterative-development-4.png)

Po výběru možnosti *ladit* do této možnosti se změní na vyplněný červený kroužek, aby označoval, že je povolená zarážka.

![Po nastavení zarážky u vybraného elementu](media/iterative-development-debugging/iterative-development-5.png)

## <a name="monitoring-debug-runs"></a>Monitorování běhů ladění

Když spustíte ladění kanálu, výsledky se zobrazí v okně **výstup** plátna kanálu. Karta výstup bude obsahovat jenom poslední běh, ke kterému došlo během aktuální relace prohlížeče. 

![Okno výstup plátna kanálu](media/iterative-development-debugging/iterative-development-2.png)

Chcete-li zobrazit historické zobrazení ladicích běhů nebo zobrazit seznam všech aktivních ladicích běhů, můžete přejít do prostředí **monitorování** . 

![Vyberte ikonu Zobrazit aktivní ladicí běhy.](media/iterative-development-debugging/view-debug-runs.png)

> [!NOTE]
> Služba Azure Data Factory zachovává jenom historii spuštění ladění po dobu 15 dnů. 

## <a name="debugging-mapping-data-flows"></a>Ladění toků dat mapování

Mapování toků dat vám umožní vytvořit logiku transformace dat bez kódu, která běží ve velkém měřítku. Při sestavování logiky můžete zapnout ladicí relaci pro interaktivní práci s daty pomocí clusteru Live Spark. Další informace najdete v článku o [mapování režimu ladění toku dat](concepts-data-flow-debug-mode.md).

Můžete monitorovat aktivní relace ladění toku dat napříč továrnou v prostředí **monitorování** .

![Zobrazit relace ladění toku dat](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

Data ve verzi Preview v Návrháři toku dat a ladění toků dat mají za cíl fungovat nejlépe s malými ukázkami dat. Pokud ale potřebujete testovat logiku v kanálu nebo toku dat proti velkým objemům dat, zvětšete velikost Azure Integration Runtime používaného v relaci ladění s více jádry a minimálními výpočetními prostředky pro obecné účely.
 
### <a name="debugging-a-pipeline-with-a-data-flow-activity"></a>Ladění kanálu s aktivitou toku dat

Při spouštění ladicího kanálu s tokem dat máte dvě možnosti, na kterých výpočetní prostředky použít. Můžete použít buď existující cluster ladění, nebo aktivovat nový cluster za běhu pro datové toky.

Použití existující relace ladění významně zkrátí čas spuštění toku dat, protože cluster už je spuštěný, ale nedoporučuje se pro komplexní nebo Paralelní úlohy, protože to může selhat při spuštění několika úloh najednou.

Pomocí modulu runtime aktivit se vytvoří nový cluster s použitím nastavení zadaného v modulu runtime integrace každé aktivity toku dat. To umožňuje izolovat každou úlohu a měla by se používat pro komplexní úlohy nebo testování výkonu. Hodnotu TTL můžete také řídit v Azure IR tak, aby prostředky clusteru používané pro ladění byly pro toto časové období k dispozici pro poskytování dalších požadavků na úlohy.

> [!NOTE]
> Máte-li kanál s toky dat spouštěnými paralelně nebo datovými toky, které je třeba testovat s velkými datovými sadami, vyberte možnost použít modul runtime aktivit, aby Data Factory mohli použít Integration Runtime, kterou jste vybrali v aktivitě toku dat. Tím umožníte, aby toky dat běžely na několika clusterech a mohli pojmout vaše paralelní provádění toku dat.

![Spuštění kanálu s tokem dat](media/iterative-development-debugging/iterative-development-dataflow.png)

## <a name="next-steps"></a>Další kroky

Po otestování změn je povýšíte na vyšší prostředí pomocí [průběžné integrace a nasazování v Azure Data Factory](continuous-integration-deployment.md).
