---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 03ec8740a4cf36bf3d09dade8a24b155c09d1299
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56740950"
---
Nastavení kontejneru jsou hierarchické a všechny kontejnery v hostitelském počítači pomocí sdílených hierarchie.

Můžete použít jednu z následujících nastavení:

* [Proměnné prostředí](#environment-variable-settings)
* [Argumenty příkazového řádku](#command-line-argument-settings)

Hodnoty proměnných prostředí přepsat hodnoty argumentů příkazového řádku, které pak přepsat výchozí hodnoty pro image kontejneru. Pokud chcete zadat jiné hodnoty v proměnné prostředí a argument příkazového řádku pro stejné nastavení konfigurace, hodnota v proměnné prostředí používá vytvořenou instanci kontejneru.

|Priorita|Nastavení umístění|
|--|--|
|1|Proměnná prostředí| 
|2|Příkazový řádek|
|3|Výchozí hodnota image kontejneru|

### <a name="environment-variable-settings"></a>Nastavení proměnné prostředí

Výhody použití proměnné prostředí jsou:

* Je možné nakonfigurovat několik nastavení.
* Více kontejnerů můžete použít stejné nastavení.

### <a name="command-line-argument-settings"></a>Nastavení argument příkazového řádku

Výhodou použití argumentů příkazového řádku je, že každý kontejner může použít jiná nastavení.
