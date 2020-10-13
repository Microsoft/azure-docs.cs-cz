---
title: Recept na instanci kontejneru Azure
titleSuffix: Azure Cognitive Services
description: Naučte se nasazovat kontejnery Cognitive Services ve službě Azure Container instance.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f247465c7e2c0a212df2821ebc7165d3ee5b15f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80876653"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Nasazení a spuštění kontejneru v instanci kontejneru Azure

Pomocí následujících kroků můžete snadno škálovat aplikace Cognitive Services Azure v cloudu s využitím Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Containering vám pomůže soustředit se na vytváření aplikací namísto správy infrastruktury. Další informace o používání kontejnerů najdete v tématu [funkce a výhody](../cognitive-services-container-support.md#features-and-benefits).

## <a name="prerequisites"></a>Předpoklady

Tento recept funguje s libovolným kontejnerem Cognitive Services. Před použitím tohoto receptu musí být prostředek služby vnímání vytvořen v Azure Portal. Každá služba pro rozpoznávání, která podporuje kontejnery, má dokument "Jak nainstalovat" speciálně pro instalaci a konfiguraci služby pro kontejner. Některé služby vyžadují jako vstup pro kontejner soubor nebo sadu souborů, je důležité, abyste před použitím tohoto řešení porozuměli a úspěšně použili kontejner.

* Prostředek služby pro rozpoznávání, vytvořený v Azure Portal.
* **Adresa URL koncového bodu** služby vnímání – zkontrolujte konkrétní službu "Jak nainstalovat" pro kontejner, abyste zjistili, kde adresa URL koncového bodu je v rámci Azure Portal a jak vypadá správný příklad adresy URL. Přesný formát se může změnit ze služby na službu.
* **Klíč** služby pro rozpoznávání – klíče jsou na stránce **klíče** pro prostředek Azure. Potřebujete jenom jeden ze dvou klíčů. Klíč je řetězec o 32 alfanumerických znaků.
* Jeden Cognitive Services kontejner na místním hostiteli (Váš počítač). Ujistěte se, že máte tyto:
  * Vyžádat si obrázek z části pomocí `docker pull` příkazu.
  * Spusťte místní kontejner úspěšně se všemi požadovanými nastaveními konfigurace pomocí `docker run` příkazu.
  * Zavolejte koncový bod kontejneru a dostanete odpověď HTTP 2xx a odpověď JSON zpátky.

Všechny proměnné v lomených závorkách, `<>` musí být nahrazeny vlastními hodnotami. Tato náhrada zahrnuje lomené závorky.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](includes/create-container-instances-resource.md)]

## <a name="use-the-container-instance"></a>Použití instance kontejneru

1. Vyberte **Přehled** a zkopírujte IP adresu. Bude to číselná IP adresa, třeba `55.55.55.55` .
1. Otevřete novou kartu prohlížeče a použijte IP adresu, například `http://<IP-address>:5000 (http://55.55.55.55:5000` ). Zobrazí se Domovská stránka kontejneru, která vám umožní zjistit, že je kontejner spuštěný.

1. Když vyberete **Popis rozhraní API služby** , zobrazí se stránka Swagger pro kontejner.

1. Vyberte libovolné rozhraní API pro **post** a vyberte **vyzkoušet**.  Zobrazí se parametry včetně vstupu. Zadejte parametry.

1. Výběrem příkazu **Spustit** odešlete požadavek do vaší instance kontejneru.

    Úspěšně jste vytvořili a použili kontejnery Cognitive Services ve službě Azure Container instance.
