---
title: Co je FPGA a Project Brainwave? -Azure Machine Learning service
description: Zjistěte, jak zrychlit modelů a s FPGA neuronových sítí v Azure. Tento článek obsahuje úvod do pole programmable gate Array (FPGA) a jak služba Azure Machine Learning poskytuje v reálném čase umělé inteligence (AI), při nasazení modelu do FPGA Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: tedway
author: tedway
ms.reviewer: jmartens
ms.date: 10/24/2018
ms.openlocfilehash: c40837baf66b39d98da17c244f462f356d2b1f26
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52864345"
---
# <a name="what-is-fpga-and-project-brainwave"></a>Co je FPGA a Project Brainwave?

Tento článek obsahuje úvod do pole programmable gate Array (FPGA) a jak službu Azure Machine Learning poskytuje v reálném čase umělé inteligence (AI), při nasazení modelu do FPGA Azure.

FPGA obsahovat pole programovatelný logiky bloků a hierarchii znovupoužitelných propojení. Propojení povolit tyto bloky v různých způsobů, jak po výrobu. FPGA zajišťoval programovatelnost a výkonu ve srovnání s další čipy.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA vs. Procesor, grafický procesor a ASIC

![Porovnání FPGA služby Azure Machine Learning](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|Procesor||Popis|
|---|:-------:|------|
|Specifické pro aplikaci integrovaného okruhy|ASICs|Vlastní okruhů, jako je Google TensorFlow procesoru jednotek (TPU) zadejte maximální efektivitou. Nedá se změnit podle vašich aktuálních potřeb.|
|Pole programmable gate Array|FPGA|FPGA, jako jsou dostupné v Azure, poskytují výkon blízko ASICs, ale jsou flexibilní a znovupoužitelných časem implementovat nové logiku.|
|Grafické procesory|Grafické procesory|Oblíbené volba pro výpočty AI nabízí možnosti paralelního zpracování díky tomu je rychlejší při vykreslování obrázků než procesory.|
|Jednotky zpracování – střed|Procesory|Pro obecné účely procesory, jehož výkon není ideální pro grafiku a zpracování videa.|

## <a name="project-brainwave-on-azure"></a>Project Brainwave v Azure

[Project Brainwave](https://www.microsoft.com/research/project/project-brainwave/) je úsporné hardwarovou architekturou společnosti Microsoft, založené na zařízení společnosti Intel FPGA, že datových vědců a vývojářů, použijte k urychlení výpočtů v reálném čase AI.  Tato architektura povolené FPGA nabízí **výkonu**, **flexibilitu**, a **škálování** a je k dispozici v Azure.

**FPGA umožňují dosáhnout nízké latence pro požadavky v reálném čase odvozování.** Nejsou potřeba asynchronní požadavků (batch). Dávkové zpracování může způsobit latence, protože je potřeba zpracovat víc dat, ale může se zvýšit propustnost v některých kontextech. Project Brainwave implementace neuronových sítí zpracování jednotek nevyžadují dávkování; latence může být proto v mnoha případech nižší ve srovnání s konkrétním procesoru a GPU.

### <a name="reconfigurable-power"></a>Znovupoužitelných napájení
**Můžete třeba překonfigurovat FPGA pro různé typy modelů strojového učení.** Díky této flexibilitě usnadňuje zrychlení aplikací založených na nejvíce optimální číselná přesnost a používá model paměti.

Nové techniky machine learning jsou vytvářena v pravidelných intervalech a návrh hardwaru Project Brainwave se také rychle vyvíjejí. Protože jsou znovupoužitelných FPGA, je možné aktuální požadavky na rychle se měnící algoritmů AI.

### <a name="whats-supported-on-azure"></a>Co je podporováno v Azure
**Microsoft Azure je na světě největší cloudové investice do FPGA.** Project Brainwave můžete spustit na škálování infrastruktury Azure.

V současné době podporuje Project Brainwave:
+ Scénáře klasifikace a rozpoznávání obrázků
+ TensorFlow nasazení
+ Dopředné: Modelem ResNet 50, modelem ResNet 152, VGG-16, SSD VGG a DenseNet 121
+ Hardware Intel FPGA 

Pomocí této architektury hardware podporující FPGA trénovaného neuronové sítě spusťte rychle a s nižší latencí. Project Brainwave můžete paralelizovat předem vytrénovaných hloubkových neuronových sítí (DNN) napříč FPGA horizontálně navýšit kapacitu vaší služby. Dopředné můžete předem školení, jako hloubkové featurizer pro přenos učení a doladíte s aktualizovanou váhy.

### <a name="scenarios-and-applications"></a>Scénáře a aplikace

Project Brainwave je integrovaná s Azure Machine Learning. Použití Microsoft FPGA DNN hodnocení, pořadí vyhledávání Bingu a softwaru definované sítě (SDN) akcelerace snížit latenci při uvolnění procesorů pro jiné úlohy.

Následující scénáře použití FPGA na Project Brainwave architekturu:
+ [Automatizovaný systém optické kontroly](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Mapování titulní pozemního](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-to-fpgas-on-azure"></a>Nasazení do FPGA v Azure

Tady je pracovní postup pro vytvoření služby rozpoznávání obrázků v Azure pomocí podporovaných dopředné featurizer pro nasazení na FPGA Azure:

1. Použití [Azure Machine Learning SDK pro Python](https://aka.ms/aml-sdk) k vytvoření definice služby, které je soubor s popisem kanálu grafy (vstup featurizer a třídění) podle TensorFlow. Příkaz nasazení bude automaticky komprimovat definice a grafy do souboru ZIP a nahrání souboru ZIP do úložiště objektů Blob v Azure.  Už je nasazená DNN na Project Brainwave ke spuštění na FPGA.

1. Zaregistrujte model pomocí sady SDK se souborem ZIP v úložišti objektů Blob v Azure.

1. Nasazení služby s modelem registrované pomocí sady SDK.

Můžete začít nasazením trénované modely DNN do FPGA ve službě Azure cloud s tímto článkem **"[nasazení modelu jako webové služby na FPGA](how-to-deploy-fpga-web-service.md)"**.


## <a name="next-steps"></a>Další postup

Projděte si tyto videa a blogy:

+ [Hyperškálovatelného hardwaru: ML ve velkém měřítku nad Azure + FPGA: Build 2018 (video)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

+ [Uvnitř Microsoft FPGA cloudu založenému na FPGA (video)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Microsoft Office Project Brainwave pro AI v reálném čase: domovské stránce projektu](https://www.microsoft.com/research/project/project-brainwave/)
