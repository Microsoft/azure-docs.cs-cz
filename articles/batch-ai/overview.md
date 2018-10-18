---
title: Služba Azure Batch AI – trénování AI | Microsoft Docs
description: Zjistěte, jak pomocí spravované služby Azure Batch AI trénovat modely umělé inteligence (AI) a další modely strojového učení na clusterech procesorů (CPU) a grafických procesorů (GPU).
services: batch-ai
documentationcenter: ''
author: alexsuttonms
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/01/2018
ms.author: danlep
ms.openlocfilehash: 98497812e75d07fc153e0e351331c05484164fdd
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44052695"
---
# <a name="what-is-azure-batch-ai"></a>Co je Azure Batch AI?

Azure Batch AI je spravovaná služba umožňující odborníkům na data a výzkumníkům AI trénovat a testovat modely strojového učení a AI v Azure ve velkém měřítku, aniž by bylo potřeba spravovat složitou infrastrukturu. Stačí, když popíšete výpočetní prostředky, úlohy, které chcete spustit, a umístění pro uložení vstupů modelu a výstupů. Batch AI se postará o všechno ostatní.

Batch AI lze použít samostatně nebo k trénování modelu v rámci rozsáhlejšího pracovního postupu vývoje:

* K trénování, testování a dávkovému hodnocení modelů strojového učení a AI na clusterech [GPU](../virtual-machines/linux/sizes-gpu.md) nebo CPU používejte Batch AI samostatně. 

* V pracovním postupu s [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) nebo jinými [nástroji platformy Azure AI](https://azure.microsoft.com/overview/ai-platform/) používejte cílení na cluster Batch AI. Azure ML nabízí celou řadu možností pro přípravu dat, experimentování a historii úloh. Azure ML může také zabalit trénovaný model do kontejneru a nasadit model pro odvozování nebo do zařízení IoT.  

## <a name="train-machine-learning-and-ai-models"></a>Trénování modelů strojového učení a AI

K trénování modelů strojového učení lze použít Batch AI a také hloubkové neuronové sítě (hloubkové učení) a další přístupy AI. Batch AI má integrovanou podporu pro oblíbené open source AI knihovny a architektury, jako jsou například [TensorFlow](https://github.com/tensorflow/tensorflow), [PyTorch](https://github.com/pytorch/pytorch), [Chainer](https://github.com/chainer/chainer) a [Microsoft Cognitive Toolkit (CNTK)](https://github.com/Microsoft/CNTK).

Po identifikaci problémové oblasti a přípravě dat můžete začít s Batch AI interaktivně pracovat a testovat nápady modelu. Až budete připraveni k experimentování ve velkém měřítku, spusťte úlohy na více GPU s MPI nebo jinými komunikačními knihovnami a spusťte paralelně další experimenty.

Batch AI umožňuje trénovat modely ve velkém měřítku několika způsoby. Příklad: 

|  |  |
|---------|---------|
| **Distribuované trénování**<br/>![Distribuované trénování](./media/overview/distributed-training.png)  | Při trénování rozsáhlejších sítí s velkými objemy dat můžete kapacitu trénování pro jednu úlohu vertikálně navýšit použitím velkého počtu síťově propojených GPU.|
| **Experimentování**<br/>![Experimentování](./media/overview/experimentation.png) | Kapacitu trénování můžete horizontálně navýšit pomocí více úloh. Můžete spustit parametrické průchody pro testování nových nápadů nebo ladit hyperparametry pro optimalizaci přesnosti a výkonu. |
| **Paralelní provádění**![Paralelní provádění](./media/overview/parallel-execution.png) | Můžete současně trénovat nebo hodnotit velký počet modelů, které běží paralelně na více serverech, aby se úlohy provedly rychleji.|

Po natrénování modelu použijte Batch AI k otestování modelu, pokud to nebylo součástí trénovacího skriptu, nebo proveďte dávkové hodnocení.

## <a name="how-it-works"></a>Jak to funguje

Ke správě výpočetních prostředků a naplánování úloh pro trénování a testování AI se používají sady SDK služby Batch AI, skripty příkazového řádku nebo Azure Portal: 

* **Zřizování a škálování clusterů virtuálních počítačů:** Můžete zvolit počet uzlů (virtuálních počítačů) a vybrat velikost virtuálního počítače (s podporou GPU nebo jiného), která vyhovuje vašim potřebám pro trénování. Počet uzlů lze zvyšovat nebo snižovat automaticky nebo ručně, takže prostředky využíváte jenom v případě potřeby. 

* **Správa závislostí a kontejnerů:** Ve výchozím nastavení clustery Batch AI běží na imagích virtuálních počítačů s Linuxem, které mají předem nainstalované závislosti pro spouštění architektur trénování založených na kontejnerech na GPU nebo CPU. Pro další konfiguraci můžete používat vlastní image nebo spouštěcí skripty.

* **Distribuce dat:** Můžete zvolit jednu nebo několik možností úložiště pro správu vstupních dat a skriptů a výstup úlohy: Soubory Azure, Azure Blob Storage nebo spravovaný server systému souborů NFS. Batch AI podporuje také vlastní řešení úložiště včetně vysoce výkonných paralelních systémů souborů. Systémy souborů úložiště lze připojit k uzlům clusteru a kontejnerům úloh pomocí jednoduchých konfiguračních souborů.

* **Plánování úloh:** Můžete odesílat úlohy do fronty úloh založené na prioritě, která umožňuje sdílení prostředků clusterů a využívání virtuálních počítačů s nízkou prioritou a rezervovaných instancí.

* **Řešení při selháních:** Stav úloh můžete sledovat a v případě selhání virtuálních počítačů během potenciálně dlouhotrvajících úloh můžete úlohy restartovat.

* **Shromažďování výsledků:** Máte snadný přístup k protokolům výstupu, souboru Stdout, souboru Stderr a natrénovaným modelům. Můžete úlohy Batch AI nakonfigurovat tak, aby odesílaly výstup přímo do připojeného úložiště.

Batch AI je služba Azure, takže podporuje běžné nástroje, jako je třeba zabezpečení pomocí řízení přístupu na základě role (RBAC) a virtuálních sítí Azure.  

## <a name="next-steps"></a>Další kroky

* Získejte další informace o [prostředcích Batch AI](resource-concepts.md) pro trénování modelu strojového učení nebo AI.

* Začněte s [trénováním ukázkového modelu hloubkového učení](quickstart-tensorflow-training-cli.md) pomocí Batch AI.

* Prohlédněte si oblíbené architektury AI v ukázkových [návodech k trénování](https://github.com/Azure/BatchAI/blob/master/recipes).
