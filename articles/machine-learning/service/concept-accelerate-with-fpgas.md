---
title: Co je FPGA? Azure Machine Learning – project Brainwave –
description: Zjistěte, jak zrychlit modely a hluboké neuronové sítě pomocí FPGA.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/31/2018
ms.openlocfilehash: 5c8efcbb5f2e9014c9edabfc2aee37c8c29ea0b4
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35642627"
---
# <a name="what-is-fpga-and-project-brainwave"></a>Co je FPGA a Project Brainwave?

Tento článek obsahuje úvod do pole programmable gate Array (FPGA) a jak je FPGA integrovat s Azure machine learning k poskytování AI v reálném čase.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA vs. Procesor, grafický procesor a ASIC

FPGA obsahovat pole programovatelný logiky bloků a hierarchii znovupoužitelných propojení, díky kterým můžou bloky po výrobu nakonfigurovat různými způsoby.

FPGA zajišťoval programovatelnost a výkon srovnáním s další čipů po:

![Porovnání služby Azure Machine Learning FPGA](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

- **Centrální zpracování jednotky (CPU)** jsou procesorů pro obecné účely. Výkon procesoru není ideální pro grafiku a zpracování videa.
- **Grafických procesorů (GPU)** nabízí paralelní zpracování a jsou oblíbené volbou pro výpočty AI. Paralelní zpracování s výsledkem grafickými procesory než procesory rychlejší vykreslování obrázků.
- **Specifické pro aplikaci integrovaného okruhů (ASIC)**, jako je Google TensorFlow procesoru jednotek, jsou přizpůsobené okruhy. Tyto čipy nabízejí maximální efektivitou, ASICs jsou pevné.
- **FPGA**, jako je například, které jsou dostupné v Azure poskytují výkon blízko ASIC, ale nabízejí flexibilitu, třeba překonfigurovat později.

FPGA jsou teď v každé nový server Azure. Microsoft už používá FPGA hodnocení, hluboké neuronové sítě (DNN) hodnocení pro vyhledávání Bingu a softwarově definované sítě (SDN) akcelerace. Tato implementace FPGA snížit latenci při uvolnění procesorů pro jiné úlohy.

## <a name="project-brainwave-on-azure"></a>Project Brainwave v Azure

Project Brainwave je hardwarovou architekturou, která je navržena podle společnosti Intel FPGA zařízení a umožňuje zrychlit výpočtů v reálném čase AI. Díky této úsporné architektuře FPGA povolena lze spustit trénovaného neuronové sítě co nejrychleji a s nižší latencí. Project Brainwave můžete předem vytrénovaných dopředné paralelizovat napříč FPGA horizontálně navýšit kapacitu vaší služby.

- Výkon

    FPGA umožňují dosáhnout nízké latence pro požadavky v reálném čase odvozování. Dávkování znamená, že požadavek na menší části rozdělení a předáte do zpracovatele zlepšovat využití hardwaru. Dávkové zpracování není efektivní a může způsobit zpoždění. Brainwave nevyžaduje, aby dávkování; latence je proto 10krát nižší ve srovnání s konkrétním procesoru a GPU.

- Flexibilita

    Můžete třeba překonfigurovat FPGA pro různé typy modelů strojového učení. Díky této flexibilitě usnadňuje zrychlení aplikací založených na nejvíce optimální číselná přesnost a používá model paměti.

    Nové techniky machine learning jsou vytvářena v pravidelných intervalech a návrh hardwaru Project Brainwave se také rychle vyvíjejí. Protože jsou znovupoužitelných FPGA, je možné aktuální požadavky na rychle se měnící algoritmů AI.

- Měřítko

    Microsoft Azure je na světě největší cloudové investice do FPGA. Brainwave můžete spustit na škálování infrastruktury Azure.

Project Brainwave integrovaná s Azure Machine Learning ve verzi preview je teď dostupná. A je také k dispozici přenese Project Brainwave do hraničních zařízení tak, aby můžete využít výpočetní rychlosti firmy a zařízení ve verzi limited preview.

V aktuální verzi preview je omezený na TensorFlow nasazení a na základě ResNet50 neuronových sítí na hardware Intel FPGA klasifikace obrázků a rozpoznávání Brainwave. Existují plány, které podporují další galerie modelů a další architektury.

Následující scénáře použití FPGA na Project Brainwave architekturu:

- Automatizovaný systém optické kontroly. Zobrazit [AI v reálném čase: Microsoft představuje předběžnou verzi Project Brainwave](https://blogs.microsoft.com/ai/build-2018-project-brainwave/).
- Pozemního titulní mapování. Zobrazit [použití FPGA pro odvození hlubších provádět pozemního titulní mapování na terabajtech až letecké imagí](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/).

## <a name="how-to-deploy-a-web-service-to-an-fpga"></a>Jak nasadit webovou službu do FPGA?

Podrobný postup vytvoření služby rozpoznávání obrazu v Azure pomocí ResNet50 featurizer vypadá takto:

1. ResNet50 je už nasazená v Brainwave. Můžete vytvořit další grafy (vstupní datum, klasifikace a tak dále) s TensorFlow a definovat kanál (vstupní -> vytrénovaných -> klasifikovat) pomocí souboru json definice služby. Definice a grafy zkomprimujte do souboru zip a nahrajte soubor zip do úložiště objektů Blob v Azure.
2. Zaregistrujte model rozhraní API služby Azure ML Model správy pomocí souboru zip v úložišti objektů Blob.
3. Nasazení služby s modelem registrované pomocí rozhraní API správy služby Azure ML modelu.

Další informace o tomto procesu v následujícím článku [nasazení modelu jako webové služby na Azure Machine Learning FPGA](how-to-deploy-fpga-web-service.md).


## <a name="start-using-fpga"></a>Začněte používat FPGA

Azure modely strojového učení Hardware Accelerated umožňují nasazení trénovaného DNN modelů do FPGA v cloudu Azure. Abyste mohli začít, najdete v článku:

- [Nasazení modelu jako webové služby na FPGA](how-to-deploy-fpga-web-service.md)
- [Microsoft Azure Machine Learning Hardware Accelerated modely využívající Project Brainwave](https://github.com/azure/aml-real-time-ai).

## <a name="next-steps"></a>Další postup

[Nasazení modelu jako webové služby na FPGA](how-to-deploy-fpga-web-service.md)

[Zjistěte, jak nainstalovat sadu SDK FPGA](reference-fpga-package-overview.md)

[Hyperškálovatelného hardwaru: ML ve velkém měřítku nad Azure + FPGA: Build 2018 (video)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

[Uvnitř Microsoft FPGA cloudu založenému na FPGA (video)](https://channel9.msdn.com/Events/Build/2017/B8063)

[Aplikace Microsoft Project Brainwave unveils pro AI v reálném čase](https://www.microsoft.com/research/blog/microsoft-unveils-project-brainwave/)