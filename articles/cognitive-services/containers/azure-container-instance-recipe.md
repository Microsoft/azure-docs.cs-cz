---
title: Recept na instanci kontejneru Azure
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak nasadit kontejnery služeb Cognitive Services v instanci kontejneru Azure
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f247465c7e2c0a212df2821ebc7165d3ee5b15f3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876653"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Nasazení a spuštění kontejneru v instanci kontejneru Azure

Pomocí následujících kroků můžete snadno škálovat aplikace Azure Cognitive Services v cloudu pomocí [instancí kontejnerů](https://docs.microsoft.com/azure/container-instances/)Azure . Kontejnerizace vám pomůže zaměřit se na vytváření aplikací namísto správy infrastruktury. Další informace o používání kontejnerů naleznete v [tématu funkce a výhody](../cognitive-services-container-support.md#features-and-benefits).

## <a name="prerequisites"></a>Požadavky

Recept funguje s libovolným kontejnerem služeb Cognitive Services. Prostředek služby Cognitive Service musí být vytvořen na webu Azure portal před použitím receptu. Každá služba Cognitive Service, která podporuje kontejnery, má dokument "Jak nainstalovat" speciálně pro instalaci a konfiguraci služby pro kontejner. Některé služby vyžadují soubor nebo sadu souborů jako vstup pro kontejner, je důležité, abyste pochopili a úspěšně použili kontejner před použitím tohoto řešení.

* Prostředek kognitivní služby, vytvořený na webu Azure Portal.
* Adresa **URL koncového bodu** služby Cognitive Service – zkontrolujte konkrétní služby "Jak nainstalovat" pro kontejner, chcete-li zjistit, kde je adresa URL koncového bodu z portálu Azure a co správný příklad adresy URL vypadá. Přesný formát se může změnit ze služby na službu.
* **Klíč** služby Cognitive Service – klíče jsou na stránce **Klíče** pro prostředek Azure. Potřebujete pouze jeden ze dvou klíčů. Klíč je řetězec 32 alfanumerických znaků.
* Jeden kontejner služeb Cognitive Services na místním hostiteli (počítači). Ujistěte se, že můžete:
  * Stáhněte obrázek `docker pull` příkazem dolů.
  * Spusťte místní kontejner úspěšně se všemi požadovanými nastaveními konfigurace pomocí příkazu. `docker run`
  * Volání koncového bodu kontejneru, získání odpovědi HTTP 2xx a odpověď JSON zpět.

Všechny proměnné v úhlových `<>`závorkách, , je třeba nahradit vlastními hodnotami. Tato náhrada zahrnuje úhlové držáky.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>Použití instance kontejneru

1. Vyberte **přehled** a zkopírujte adresu IP. Bude to číselná IP `55.55.55.55`adresa, například .
1. Otevřete novou kartu prohlížeče a použijte například IP adresu `http://<IP-address>:5000 (http://55.55.55.55:5000`). Uvidíte domovskou stránku kontejneru, abyste věděli, že kontejner běží.

1. Vyberte **Popis rozhraní API služby,** chcete-li zobrazit stránku chvástání pro kontejner.

1. Vyberte libovolné z api **POST** a vyberte **Vyzkoušet**.  Parametry jsou zobrazeny včetně vstupu. Vyplňte parametry.

1. Vyberte **Spustit,** chcete-li odeslat požadavek do instance kontejneru.

    Úspěšně jste vytvořili a použili kontejnery služeb Cognitive Services v instanci kontejneru Azure.
