---
title: Azure Container Instance předpisu
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak nasadit kontejnery Cognitive Services na instanci kontejneru Azure
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 45a03a0912681b4fc33ef8df88fa00fd5458f720
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445821"
---
# <a name="deploy-and-run-container-on-azure-container-instance-aci"></a>Nasazení a spouštění kontejnerů v Azure Container Instance (ACI)

Pomocí následujících kroků škálování aplikací Azure Cognitive Services v cloudu snadno pomocí Azure [instanci kontejneru](https://docs.microsoft.com/azure/container-instances/) (ACI). Díky tomu se můžete soustředit na vytváření aplikací a nemusíte spravovat infrastrukturu.

## <a name="prerequisites"></a>Požadavky

Toto řešení funguje s kontejnerem všechny služby Cognitive Services. Na webu Azure Portal musíte před použitím této předpisu vytvořen prostředek služby Cognitive Services. Každé služby Cognitive Services, který podporuje kontejnery má dokument "Postup instalace" speciálně pro instalaci a konfiguraci služby kontejneru. Některé služby vyžadují soubor nebo sadu souborů jako vstup pro kontejner, a proto je důležité pochopit a použili kontejneru úspěšně před použitím tohoto řešení.

* Prostředek služby Cognitive Services vytvořili na webu Azure portal.
* Služby cognitive Services **adresu URL koncového bodu** – zkontrolujte vaše specifické služby "postup instalace" pro kontejner, chcete-li zjistit, kde je adresa URL koncového bodu z v rámci webu Azure portal a co správný Příklad adresy URL vypadá jako. Přesném formátu, který můžete změnit ze služby.
* Služby cognitive Services **klíč** -klíče jsou na **klíče** stránky pro prostředky Azure. Potřebujete jenom jeden ze dvou klíčů. Klíč je řetězec 32 alfanumerické znaky.
* Jediný Cognitive Services kontejner na místního hostitele (počítače). Ujistěte se, že můžete:
  * Stáhnout image `docker pull` příkazu.
  * S všechna požadovaná nastavení konfigurace se úspěšně spustit místního kontejneru `docker run` příkazu.
  * Volání koncového bodu kontejneru, návrat odpovědí 2xx a odpověď ve formátu JSON.

Všechny proměnné v lomených závorkách `<>`, je třeba nahradit vlastními hodnotami. Toto nahrazení zahrnuje ostrých závorek.

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances (ACI)](./includes/create-aci-resource.md)]

## <a name="use-the-container-instance"></a>Použijte instanci kontejneru

1. Vyberte **přehled** a zkopírujte IP adresu. Je číselný IP adresu, jako `55.55.55.55`.
1. Otevře nová karta prohlížeče a použijte IP adresu, například `http://<IP-address>:5000 (http://55.55.55.55:5000`). Zobrazí se domovská stránka kontejneru, oznamující, že je kontejner spuštěný.

1. Vyberte **popis rozhraní API služby** zobrazíte stránku swagger pro kontejner.

1. Vyberte některou z **příspěvek** rozhraní API a vyberte **vyzkoušet**.  Parametry jsou zobrazeny, včetně vstupu. Vyplňte parametry.

1. Vyberte **Execute** odešlete žádost instanci kontejneru.

    Úspěšně jste vytvořili a použili služby Cognitive Services kontejnery v Azure Container Instance.
