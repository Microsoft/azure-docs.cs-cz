---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2020
ms.author: larryfr
ms.openlocfilehash: 1fde2947719079e411bc233bcce426feec8fa996
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631035"
---
> [!IMPORTANT]
> Instance Cosmos DB se ve __vašem předplatném__ vytvoří v rámci skupiny prostředků spravované Microsoftem, spolu s případnými prostředky, které potřebuje. To znamená, že se vám bude účtovat tato Cosmos DB instance. Spravovaná skupina prostředků je pojmenována ve formátu `<AML Workspace Resource Group Name><GUID>` . Pokud váš pracovní prostor Azure Machine Learning používá privátní koncový bod, vytvoří se pro instanci Cosmos DB taky virtuální síť. Tato virtuální síť slouží k zabezpečení komunikace mezi Cosmos DB a Azure Machine Learning.
> 
> * __Neodstraňujte skupinu prostředků__ , která obsahuje tuto instanci Cosmos DB, nebo žádný z prostředků, které se automaticky vytvořily v této skupině. Pokud potřebujete odstranit skupinu prostředků, Cosmos DB instanci atd., je nutné odstranit Azure Machine Learning pracovní prostor, který ho používá. Skupina prostředků, instance Cosmos DB a další automaticky vytvořené prostředky se odstraní při odstranění přidruženého pracovního prostoru.
> * Výchozí [__jednotky žádostí__](../articles/cosmos-db/request-units.md) pro tento účet Cosmos DB jsou nastavené na __8000__ . __Změna této hodnoty není podporována__ .
> * __Pro použití s vytvořenou instancí Cosmos DB nemůžete zadat vlastní virtuální síť__ . __Nemůžete také upravovat virtuální síť__ . Nemůžete například změnit rozsah IP adres, který používá.