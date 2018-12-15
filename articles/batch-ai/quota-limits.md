---
title: Kvóty a limity služby pro Azure Batch AI | Dokumentace Microsoftu
description: Další informace o výchozí Azure Batch AI kvóty, limity a omezení, a jak si vyžádat kvóta zvýší
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/08/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 16032ec5ba1e613462f92b86281ce93153b70923
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409713"
---
# <a name="batch-ai-service-quotas-and-limits"></a>Omezení a kvóty služby batch AI

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Jako s ostatními službami Azure, se vztahují omezení určitých prostředků služby Batch AI. V Batch AI, tato omezení jsou výchozí kvóty uplatňované na úrovni předplatného pro každou oblast, kde je služba [dostupné](https://azure.microsoft.com/global-infrastructure/services/). Tento článek popisuje tyto výchozí hodnoty a jak můžete požádat o kvóta zvýší.

Mějte tyto kvóty při navrhování a škálování nahoru prostředky služby Batch AI. Například pokud váš cluster nemá přístup do cílového počtu uzlů, které jste zadali, pak může dosáhli jste limitu jádra služby Batch AI pro vaše předplatné.

Pokud budete chtít spustit úlohy v produkčním prostředí v Batch AI, budete muset zvýšit nejméně jeden z kvót nad výchozí.

> [!NOTE]
> Kvóta je kreditního limitu záruku kapacity. Pokud máte velkou kapacitu, kontaktujte prosím podporu Azure.
> 
> 

## <a name="resource-quotas"></a>Kvóty prostředků

[!INCLUDE [azure-batch-ai-limits](../../includes/azure-batch-ai-limits.md)]

## <a name="other-limits"></a>Další omezení

Toto jsou striktní omezení, které nesmí být překročen jednou přístupů.

| **Prostředek** | **Maximální limit** |
| --- | --- |
| Maximální pracovních prostorů na skupinu prostředků | 800 |
| Maximální velikost clusteru | 100 uzlů |
| Maximální GPU MPI zpracovává jeden uzel | 1 – 4 |
| Maximální pracovních procesů GPU na uzlu | 1 – 4 |
| Maximální doba života | 7 dní<sup>1</sup> |
| Servery parametr maximálního počtu uzlů | 1 |

<sup>1</sup> maximální doba života odkazuje na čas, úloha začne spuštěný, a po dokončení. Dokončené úlohy zachovávají po neomezenou dobu; data pro úlohy se nedokončí v rámci maximální doby života není přístupný.

## <a name="view-batch-ai-quotas"></a>Zobrazit kvóty služby Batch AI

Vaše aktuální předplatné kvóty služby Batch AI v zobrazení [webu Azure portal][portal].

1. V levém podokně klikněte na **všechny služby**. Vyhledejte **služby Batch AI** a kliknutím otevřete službu.
2. Klikněte na **využití a kvóty** v nabídce služby Batch AI.
3. Vyberte předplatné, chcete-li zobrazit kvóty.

## <a name="increase-a-batch-ai-cores-quota"></a>Zvýšit kvótu pro jádra služby Batch AI

Postupujte podle těchto kroků žádost o kvótu zvýšit k předplatnému služby Batch AI pomocí [webu Azure portal][portal]. 

1. V levém podokně klikněte na **všechny služby**. Vyhledejte **služby Batch AI** a kliknutím otevřete službu.
2. Klikněte na **nová žádost o podporu** v nabídce služby Batch AI.
3. V **Základy**:
   
    a. **Typ problému** > **kvóty**
   
    b. **Předplatné** > vyberte své předplatné.
   
    c. **Typ kvóty** > **služby Batch AI**
   
    d. **Plán podpory** > vyberte váš plán podpory.

    Klikněte na tlačítko **Další**.
4. V **problém**:
   
    a. Vyberte **závažnost** podle vaší [dopad na chod firmy][support_sev].
   
    b. V **podrobnosti o kvótě**, zadejte umístění, typ kvóty a typ prostředku. Určete, které chcete požádat o nový limit. Klikněte na tlačítko **uložit a pokračovat**.

    c. Volitelné – nahrát všechny relevantní soubory s dalšími informacemi o váš důvod pro zvýšení.
   
    Klikněte na tlačítko **Další**.
5. V **kontaktní informace**:
   
    a. Vyberte **upřednostňovaný způsob kontaktování**.
   
    b. Zkontrolujte a zadejte požadované podrobnosti kontaktu.
   
    Kliknutím na **Vytvořit** odešlete žádost o podporu.

Jakmile jste odeslali žádost o podporu, bude vás kontaktovat podporu Azure. Dokončení žádosti může trvat až 2 pracovních dnů.


## <a name="next-steps"></a>Další postup

Po seznámení s maximální kvóty, projděte si následující články pro zahájení práce s využitím služby Batch AI.

> [!div class="nextstepaction"]
> [Rychlý úvodní kurz k batch AI](quickstart-tensorflow-training-cli.md)
> [služby Batch AI recepty](https://github.com/Azure/BatchAI/tree/master/recipes)
> [Další informace o prostředcích služby Batch AI](resource-concepts.md)

[portal]: https://portal.azure.com
[support_sev]: http://aka.ms/supportseverity