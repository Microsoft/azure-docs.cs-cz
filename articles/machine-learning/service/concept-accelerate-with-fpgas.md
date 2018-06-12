---
title: Co je FPGA? – Projekt Brainwave – Azure Machine Learning
description: Zjistěte, jak zrychlit modely a hluboké neuronové sítě s FPGAs.
services: machine-learning
ms.service: machine-learning
ms.component: service
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/31/2018
ms.openlocfilehash: 5ed94c3b750c927ec48959c12388bd22de3d3df4
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261946"
---
# <a name="what-is-fpga-and-project-brainwave"></a>Co je FPGA a Brainwave projektu?

Tento článek obsahuje úvod do pole programovatelný brány pole (FPGA) a jak je FPGA integraci s Azure machine learningu zajistit AI v reálném čase.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGAs vs. ASIC, procesoru a GPU

FPGAs obsahovat pole programovatelný logiku bloky a hierarchie rekonfigurovatelné propojení umožňující bloky po výrobní nakonfigurovat různými způsoby.

FPGAs poskytovat kombinaci programovatelnosti a výkonu srovnáním jiných čipy:

![Azure Machine Learning FPGA porovnání](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

- **Střed zpracování jednotky (CPU)** jsou pro obecné účely procesory. Výkon procesoru není ideální pro grafika a zpracování videa.
- **Grafických procesorů (GPU)** nabízejí paralelní zpracování a jsou oblíbených volbou pro AI výpočty. Paralelní zpracování s grafickými procesory následek rychlejší vykreslování obrázků než procesory.
- **Specifické pro aplikaci integrované obvody (ASIC)**, jako je například Google TensorFlow procesoru jednotek, jsou přizpůsobené okruhů. Zatímco tyto čipy poskytují nejvyšší efektivitu, jsou speciální aplikační integrované obvody pevná.
- **FPGAs**, jsou dostupné v Azure, například výkon blízko ASIC, ale nabízí flexibilitu překonfigurovat později.

FPGAs jsou teď ve každý nový server Azure. Microsoft už používá FPGAs pro Bing vyhledávání hodnotící hluboké neuronové sítě (DNN) zkušební verze a softwarově definované sítě (SDN) acceleration. Tato implementace FPGA snížit latenci při uvolnění procesorů pro jiné úlohy.

## <a name="project-brainwave-on-azure"></a>Brainwave projektu v Azure

Brainwave projektu je architektura hardwaru, který je navržený tak podle společnosti Intel FPGA zařízení a umožňuje urychlit výpočtů v reálném čase AI. Tato ekonomické FPGA povolená architektura vyškolení neuronové sítě se může spouštět co nejrychleji a nižší latenci. Projekt Brainwave můžete paralelní předem vyškolení DNNs napříč FPGAs pro horizontální škálování služby.

- Výkon

    FPGAs umožňují dosáhnout s nízkou latencí pro požadavky v reálném čase inferencing. Dávkování znamená rozdělování požadavek na menší části a ke zlepšení hardwaru využití procesoru. Dávkování není efektivní a může způsobit latence. Brainwave nevyžaduje dávkování; latence je proto 10krát nižší ve srovnání s procesoru a GPU.

- Flexibilita

    FPGAs můžete překonfigurovat pro různé typy modelů machine learning. Tuto flexibilitu potřebují usnadňuje zrychlit aplikace na základě nejvíce optimální číselné přesnost a modelu v paměti používá.

    Nové techniky machine learning jsou vytvářena v pravidelných intervalech a návrh hardwaru projektu Brainwave také vyvíjejí rychle. Vzhledem k tomu, že jsou FPGAs rekonfigurovatelné, je možné držet krok s požadavky na rychle změna AI algoritmů.

- Měřítko

    Microsoft Azure je světově největší cloudu investice do FPGAs. Brainwave můžete spustit na infrastruktury Azure a škálování.

Náhled projektu Brainwave integrovat Azure Machine Learning je aktuálně k dispozici. A omezené preview je také možné přenese Brainwave projektu na hranici, tak, aby můžete využít výhod této výpočetní rychlosti podnikům a zařízení.

V aktuální verzi preview je omezený na TensorFlow nasazení a na základě ResNet50 neuronové sítě na Intel FPGA hardwaru pro klasifikaci bitové kopie a rozpoznávání Brainwave. Existují plány pro podporu více Galerie modely a jiné architektury.

Následující scénáře použijte FPGA na architektuře Brainwave projektu:

- Automatizované systém optické kontroly. V tématu [AI v reálném čase: Microsoft oznamuje preview projektu Brainwave](https://blogs.microsoft.com/ai/build-2018-project-brainwave/).
- Krajině titulní mapování. V tématu [použití FPGAs pro odvození hloubkové Learning k provedení krajině titulní mapování na terabajtů leteckou image](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/).

## <a name="how-to-deploy-a-web-service-to-an-fpga"></a>Postup nasazení webové služby na FPGA?

Podrobný postup pro vytvoření služby rozpoznávání bitové kopie v Azure pomocí ResNet50 featurizer vypadá takto:

1. ResNet50 je už nasazená v Brainwave. Můžete vytvořit další grafy (vstupní datum, klasifikace a tak dále) s TensorFlow a definovat kanál (vstup -> featurize -> klasifikovat) pomocí souboru json definice služby. Komprimovat definice a grafy do souboru zip a nahrajte soubor zip do úložiště objektů Blob Azure.
2. Registraci modelu Azure ML modelu Management API pomocí souboru zip v úložišti objektů Blob.
3. Nasazení služby s modelem registrovaný pomocí rozhraní API služby Azure ML modelu správy.

Další informace o tomto procesu v článku, [nasadit model jako webovou službu na FPGA pomocí Azure Machine Learning](how-to-deploy-fpga-web-service.md).


## <a name="start-using-fpga"></a>Začít používat FPGA

Azure Machine Learning hardwaru Accelerated modely umožňují nasadit trénované modely DNN FPGAs v cloudu Azure. Abyste mohli začít, najdete v části:

- [Nasadit model jako webovou službu na FPGA](how-to-deploy-fpga-web-service.md)
- [Microsoft Azure Machine Learning hardwaru Accelerated modely používá technologii projektu Brainwave](https://github.com/azure/aml-real-time-ai).

## <a name="next-steps"></a>Další postup

[Nasadit model jako webovou službu na FPGA](how-to-deploy-fpga-web-service.md)

[Zjistěte, jak nainstalovat sadu SDK FPGA](reference-fpga-package-overview.md)

[Hyperškálovatelný systém hardwaru: ML škálované nad Azure + FPGA: sestavení 2018 (video)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

[U Microsoftu na základě FPGA konfigurovat cloudu (video)](https://channel9.msdn.com/Events/Build/2017/B8063)

[Microsoft unveils Brainwave projektu pro v reálném čase AI](https://www.microsoft.com/research/blog/microsoft-unveils-project-brainwave/)