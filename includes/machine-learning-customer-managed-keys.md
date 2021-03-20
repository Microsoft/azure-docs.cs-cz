---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2020
ms.author: larryfr
ms.openlocfilehash: 2bba53410834aadce5627a34a759e65aa0e11c28
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94574239"
---
> [!IMPORTANT]
> Instance Cosmos DB se ve __vašem předplatném__ vytvoří v rámci skupiny prostředků spravované Microsoftem, spolu s případnými prostředky, které potřebuje. To znamená, že se vám bude účtovat tato Cosmos DB instance. Spravovaná skupina prostředků je pojmenována ve formátu `<AML Workspace Resource Group Name><GUID>` . Pokud váš pracovní prostor Azure Machine Learning používá privátní koncový bod, vytvoří se pro instanci Cosmos DB taky virtuální síť. Tato virtuální síť slouží k zabezpečení komunikace mezi Cosmos DB a Azure Machine Learning.
> 
> * __Neodstraňujte skupinu prostředků__ , která obsahuje tuto instanci Cosmos DB, nebo žádný z prostředků, které se automaticky vytvořily v této skupině. Pokud potřebujete odstranit skupinu prostředků, Cosmos DB instanci atd., je nutné odstranit Azure Machine Learning pracovní prostor, který ho používá. Skupina prostředků, instance Cosmos DB a další automaticky vytvořené prostředky se odstraní při odstranění přidruženého pracovního prostoru.
> * Výchozí [__jednotky žádostí__](../articles/cosmos-db/request-units.md) pro tento účet Cosmos DB jsou nastavené na __8000__.
> * __Pro použití s vytvořenou instancí Cosmos DB nemůžete zadat vlastní virtuální síť__ . __Nemůžete také upravovat virtuální síť__. Nemůžete například změnit rozsah IP adres, který používá.