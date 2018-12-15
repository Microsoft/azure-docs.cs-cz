---
title: Co se děje s Azure Batch AI? | Dokumenty Microsoft
description: Další informace o tom, co se děje na Azure Batch AI a službou Azure Machine Learning compute možnost.
services: batch-ai
author: garyericson
ms.service: batch-ai
ms.topic: overview
ms.date: 12/14/2018
ms.author: garye
ms.openlocfilehash: 6803a47ae77c072eff05df65e37156c90aabf3e6
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436870"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Co se děje s Azure Batch AI?

**Služba Azure Batch AI je vyřazení z provozu v březnu.** Ve velkém měřítku trénování a vyhodnocování funkcí služby Batch AI jsou teď dostupné v [služby Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md), která se stala všeobecně dostupná na 4. prosince 2018.

Spolu s mnoha jiných strojového učení zahrnuje službu Azure Machine Learning založené na cloudu spravované cílové výpočetní prostředí pro školení, nasazování a vyhodnocování modelů strojového učení. Tato cílového výpočetního prostředí se nazývá [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Spuštění migrace a jeho použití Dnes](#migrate). Můžete pracovat se službou Azure Machine Learning prostřednictvím jeho [sady SDK pro Python](../machine-learning/service/quickstart-create-workspace-with-python.md), rozhraní příkazového řádku a [webu Azure portal](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Časový plán podpory

| Datum | Podrobnosti o podpoře služby batch AI |
| ---- |-----------------|
| Prosinec&nbsp;14&#x2c;&nbsp;2018| Dál používat existující předplatných Azure Batch AI jako před. Registrace ale **nová předplatná** je možné, žádné déle a této služby nebudou provedeny žádné nové investice.|
| Březen&nbsp;31&#x2c;&nbsp;2019 | Po tomto datu se stávající předplatná služby Batch AI přestane fungovat. |

<a name="migrate"></a>
## <a name="how-do-i-migrate"></a>Jak provést migraci?

Vyhnete se tak přerušením pro vaše aplikace a využívat nejnovější funkce, proveďte následující kroky před 31. března 2019:

1. Vytvořit pracovní prostor služby Azure Machine Learning a začít:
    + [Rychlý start postavená na Pythonu](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Azure portal podle rychlého startu](../machine-learning/service/quickstart-get-started.md)

1. Nastavení [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute) k tréninku modelu.

1. Aktualizujte skripty, aby pomocí Azure Machine Learning Compute.

## <a name="support"></a>Podpora

Podpora je dostupná pro stávající zákazníky, kteří chtějí migrovat do Čím komplexní [služby Azure Machine Learning](https://aka.ms/aml-docs).

Pokud služba Azure Machine Learning nevyhovuje vašim požadavkům, kde podporované funkce existuje ve službě Batch AI, otevřete podpory služby Batch AI žádosti se na tým podpory na seznam povolených předplatného pro použití služby Batch AI až do vyřazení služby.

## <a name="next-steps"></a>Další postup

+ Přečtěte si [Přehled služby Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Konfigurace cílové výpočetní prostředí k tréninku modelu](../machine-learning/service/how-to-set-up-training-targets.md) službou Azure Machine Learning.

+ Zkontrolujte [plány Azure do budoucna](https://azure.microsoft.com/updates/) a získejte informace o další aktualizace služby Azure.
