---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 11/22/2020
ms.topic: include
ms.openlocfilehash: 2112cde42ee00b78a82962ee46f53110068977c0
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2021
ms.locfileid: "98186969"
---
## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Všeobecně dostupná (GA)|
|Stanov|**Azure Defender pro Registry kontejnerů** se účtuje, jak je znázorněno na [stránce s cenami](../articles/security-center/security-center-pricing.md) .|
|Podporované registry a Image:|Image Linux v registrech ACR přístupné z veřejného Internetu s přístupem k prostředí|
|Nepodporované registry a Image:|Bitové kopie systému Windows<br>' Soukromé ' Registry<br>Registry s přístupem omezené pomocí brány firewall, koncového bodu služby nebo privátních koncových bodů, jako je například privátní odkaz Azure<br>Minimalist image, jako jsou [pomocné obrázky Docker](https://hub.docker.com/_/scratch/) nebo Image "Distroless", které obsahují jenom aplikaci a její závislosti modulu runtime bez správce balíčků, prostředí nebo operačního systému|
|Požadované role a oprávnění:|[Azure Container Registry role a oprávnění](../articles/container-registry/container-registry-roles.md) **pro čtenáře zabezpečení**|
|Cloud|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: Komerční cloudy<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: US Gov a Čína gov – v současné době se podporuje jenom funkce prověřování u nabízených oznámení. Další informace o tom, [kdy se naskenované obrázky prohledávají?](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)|
|||