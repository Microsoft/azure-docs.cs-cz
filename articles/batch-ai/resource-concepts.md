---
title: O prostředcích služby Batch AI – Azure | Dokumentace Microsoftu
description: Přehled pracovní prostory, clustery, souborové servery, experimenty a úlohy služby Batch AI v Microsoft Azure.
services: batch-ai
documentationcenter: na
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/15/2018
ms.author: danlep
ms.openlocfilehash: 4a9e3529f9d68ecdc614ea69cffc6897891f4548
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44057040"
---
# <a name="overview-of-resources-in-batch-ai"></a>Přehled prostředků v rámci služby Batch AI

Při prvním spuštění pomocí služby Batch AI, bude nutné pochopit dostupné prostředky služby Batch AI. S ostatními službami Azure při vytváření prostředků služby Batch AI v Azure jednu nebo více *skupiny prostředků*. Vytvořte jeden nebo více služby Batch AI *pracovní prostory* ve skupině prostředků. Každý pracovní prostor obsahuje směs služby Batch AI *clustery*, *souborové servery*, a *experimenty*. Batch AI experiment zapouzdří skupinu *úlohy*.

Následující obrázek ukazuje hierarchii příklad prostředků služby Batch AI. 

![](./media/migrate-to-new-api/batch-ai-resource-hierarchy.png)

V následujících částech najdete další podrobnosti o prostředcích služby Batch AI.

## <a name="workspace"></a>Pracovní prostor

Pracovní prostor v Batch AI je nejvyšší úrovně kolekce zbývající prostředky služby Batch AI. Pracovní prostory umožňují samostatné činnosti patřící do různých skupin nebo projekty. Například může vytvořit vývojový a testovací pracovního prostoru.

## <a name="cluster"></a>Cluster

Cluster v Batch AI obsahuje výpočetních prostředků pro spouštění úloh. Všechny uzly v clusteru mají stejnou velikost virtuálního počítače a image operačního systému. Batch AI nabízí celou řadu možností pro vytváření clusterů, které jsou přizpůsobené pro různé potřeby. Obvykle je nastavit jiný cluster pro každou kategorii výpočetní výkon potřebný k dokončení projektu. Škálování, které clustery Batch AI směrem nahoru a dolů podle potřeby a rozpočet. Další informace najdete v tématu [fungují s clustery Batch AI](clusters.md).

## <a name="file-server"></a>Souborový server

Volitelně můžete vytvořte souborový server v Batch AI k ukládání dat, skripty, školení a výstupních protokolů. Souborový server Batch AI je spravovaná NFS jedním uzlem je možné automaticky připojit na uzlech clusteru kvůli umístění služby storage snadno a centrálně dostupná pro úlohy. Většině případů je potřeba jenom jeden souborový server v pracovním prostoru a můžete oddělit data pro vaše úlohy trénování do různých adresářích. Pokud není vhodné pro vaše úlohy systému souborů NFS, služby Batch AI podporuje další možnosti úložiště, včetně [služby Azure Storage](use-azure-storage.md) nebo vlastními řešeními, jako je například Gluster nebo Lustre systému souborů.

## <a name="experiment"></a>Experiment

Experiment seskupit sadu související úlohy, které dotazování a správu dohromady. Každý pracovní prostor může mít více experimentů, kde se pokusí každé experiment jeden konkrétní problém vyřešit.

## <a name="job"></a>Úloha

Úloha je skript, který je třeba provést, například k natrénování modelu obsáhlého learningu a jeden úkol. Každá úloha provede konkrétní skript na jeden cluster v pracovním prostoru. (Skript může být uložen na souborový server Batch AI nebo jiného řešení úložiště.) Typ framework s ním spojená má každá úloha služby Batch AI: TensorFlow, Horovod, CNTK, Caffe, Caffe2, pyTorch, Chainer, vlastní MPI nebo vlastní. Pro každé rozhraní služba Batch AI Nastaví požadovanou infrastrukturu a spravuje procesy úloh. Každý pokus může mít více úloh, které jsou podobné, kromě několik změn pro různé parametry.

## <a name="next-steps"></a>Další postup

* Spuštění prvního [trénovací úlohu Batch AI](quickstart-tensorflow-training-cli.md).

* Prohlédněte si různá rozhraní v ukázkových [návodech k trénování](https://github.com/Azure/BatchAI/tree/master/recipes).