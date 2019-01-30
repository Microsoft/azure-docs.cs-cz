---
title: Co jsou FPGA a Project Brainwave?
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak zrychlit modelů a s FPGA neuronových sítí v Azure. Tento článek obsahuje úvod do pole programmable gate Array (FPGA) a jak službu Azure Machine Learning poskytuje v reálném čase umělé inteligence (AI), při nasazení modelu do FPGA Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tedway
author: tedway
ms.reviewer: jmartens
ms.date: 10/24/2018
ms.custom: seodec18
ms.openlocfilehash: aaca3f346fafdc6ecde069676dbe0f089a26fca8
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242990"
---
# <a name="what-are-fpgas-and-project-brainwave"></a>Co jsou FPGA a Project Brainwave?

Tento článek obsahuje úvod do pole programmable gate Array (FPGA) a jak službu Azure Machine Learning poskytuje v reálném čase umělé inteligence (AI), při nasazení modelu do FPGA Azure.

FPGA obsahovat pole programovatelný logiky bloků a hierarchii znovupoužitelných propojení. Propojení povolit tyto bloky po výrobu nakonfigurovat různými způsoby. Ve srovnání s další čipy, FPGA poskytovat kombinaci programovatelnost a výkonu.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA vs. Procesor, grafický procesor a ASIC

Následující diagram a tabulka ukazují, jak porovnat FPGA do dalších procesorů.

![Diagram porovnání FPGA služeb Azure Machine Learning](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|Procesor||Popis|
|---|:-------:|------|
|Specifické pro aplikaci integrovaného okruhy|ASICs|Vlastní okruhů, jako je Google TensorFlow procesoru jednotek (TPU) zadejte maximální efektivitou. Nedá se změnit podle vašich aktuálních potřeb.|
|Pole programmable gate Array|FPGA|FPGA, jako jsou dostupné v Azure, poskytují výkon blízko ASICs. Jsou také znovupoužitelných a flexibilní v průběhu času implementovat nové logiku.|
|Grafické procesory|Grafické procesory|Oblíbené volba pro výpočty AI. GPU nabízejí možnosti paralelního zpracování, což rychlejší při vykreslování obrázků než procesory.|
|Jednotky zpracování – střed|Procesory|Pro obecné účely procesory, není ideální pro grafiku a zpracování videa výkon.|

## <a name="project-brainwave-on-azure"></a>Project Brainwave v Azure

[Project Brainwave](https://www.microsoft.com/en-us/research/project/project-brainwave/) je architektura hardware od společnosti Microsoft. Je založen na zařízeních společnosti Intel FPGA, které datových vědců a vývojářů, použijte k urychlení výpočtů v reálném čase AI. Tato architektura povolené FPGA nabízí výkon, flexibilitu a velikost a je k dispozici v Azure.

FPGA umožňují dosáhnout nízké latence pro požadavky v reálném čase odvozování. Asynchronní požadavků (dávkování) nejsou potřeba. Dávkové zpracování může způsobit latence, protože více dat je potřeba zpracovat. Project Brainwave implementace neuronových sítí zpracování jednotek nevyžadují dávkování; latence proto může být v mnoha případech nižší ve srovnání s procesory CPU a GPU.

### <a name="reconfigurable-power"></a>Znovupoužitelných napájení
Můžete změnit konfiguraci FPGA pro různé typy modelů strojového učení. Díky této flexibilitě usnadňuje zrychlení aplikací založených na nejvíce optimální číselná přesnost a používá model paměti. Vzhledem k tomu FPGA znovupoužitelných, abyste mohli zůstat aktuální s požadavky rychle se měnící algoritmy AI.

### <a name="whats-supported-on-azure"></a>Co je podporováno v Azure
Microsoft Azure je na světě největší cloudové investice do FPGA. Project Brainwave můžete spustit na škálování infrastruktury Azure.

V současné době podporuje Project Brainwave:
+ Scénáře klasifikace a rozpoznávání obrázků
+ TensorFlow nasazení
+ Dopředné: Modelem ResNet 50, modelem ResNet 152, VGG-16, SSD VGG a DenseNet 121
+ Hardware Intel FPGA 

Pomocí této architektury hardware podporující FPGA trénovaného neuronové sítě spusťte rychle a s nižší latencí. Project Brainwave můžete paralelizovat předem vytrénovaných hloubkových neuronových sítí (DNN) napříč FPGA horizontálně navýšit kapacitu vaší služby. Dopředné můžete předem školení, jako hloubkové featurizer pro přenos učení a doladíte s aktualizovanou váhy.

### <a name="scenarios-and-applications"></a>Scénáře a aplikace

Project Brainwave je integrovaná s Azure Machine Learning. Použití Microsoft FPGA DNN hodnocení, pořadí vyhledávání Bingu a softwaru definované sítě (SDN) akcelerace snížit latenci, při uvolnění procesorů pro jiné úlohy.

Následující scénáře použití FPGA na Project Brainwave architekturu:
+ [Automatizovaný systém optické kontroly](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Mapování titulní pozemního](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-to-fpgas-on-azure"></a>Nasazení do FPGA v Azure

K vytvoření služby rozpoznávání obrázků v Azure, můžete použít podporované dopředné jako featurizer pro nasazení na FPGA Azure:

1. Použití [Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk) k vytvoření definice služby. Definice služby je soubor s popisem kanálu grafy (vstup featurizer a třídění) podle TensorFlow. Příkaz nasazení automaticky komprimuje definice a grafy do souboru ZIP a nahrání souboru ZIP do úložiště objektů Blob v Azure. Už je nasazená DNN na Project Brainwave ke spuštění na FPGA.

1. Zaregistrujte model pomocí sady SDK se souborem ZIP v úložišti objektů Blob v Azure.

1. Nasazení služby s využitím registrovaného modelu s použitím sady SDK.

Nasazení trénovaného DNN modelů do FPGA v cloudu Azure, najdete v článku [nasazení modelu jako webové služby na FPGA](how-to-deploy-fpga-web-service.md).


## <a name="next-steps"></a>Další postup

Projděte si tyto videa a blogy:

+ [Hyperškálovatelného hardwaru: ML ve velkém měřítku nad Azure + FPGA: Build 2018 (video)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

+ [Uvnitř Microsoft FPGA cloudu založenému na FPGA (video)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Microsoft Office Project Brainwave pro AI v reálném čase: domovské stránce projektu](https://www.microsoft.com/research/project/project-brainwave/)
