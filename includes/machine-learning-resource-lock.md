---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/16/2020
ms.author: larryfr
ms.openlocfilehash: 25e304daf75b60a7d037640d496ed0972581f13a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92204454"
---
Azure umožňuje umístit _zámky_ na prostředky, aby se nemohly odstranit nebo jsou jen pro čtení. __Uzamykání prostředku může vést k neočekávaným výsledkům.__ Některé operace, které nevypadající za účelem úpravy prostředku, skutečně vyžadují akce blokované zámkem. 

Například použití zámku odstranění u skupiny prostředků pro váš pracovní prostor znemožní operace škálování pro výpočetní clustery Azure ML.

Další informace o uzamykání prostředků najdete v tématu [uzamčení prostředků, aby nedocházelo k neočekávaným změnám](../articles/azure-resource-manager/management/lock-resources.md).