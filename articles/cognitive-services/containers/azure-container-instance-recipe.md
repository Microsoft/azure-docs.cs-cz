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
ms.date: 12/18/2020
ms.author: aahi
ms.openlocfilehash: 003b4411ac791898f4a7467b9b03f29aadba2fc7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97704828"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Nasazení a spuštění kontejneru v instanci kontejneru Azure

Pomocí následujících kroků můžete snadno škálovat aplikace Cognitive Services Azure v cloudu s využitím Azure [Container Instances](../../container-instances/index.yml). Containering vám pomůže soustředit se na vytváření aplikací namísto správy infrastruktury. Další informace o používání kontejnerů najdete v tématu [funkce a výhody](../cognitive-services-container-support.md#features-and-benefits).

## <a name="prerequisites"></a>Předpoklady

Tento recept funguje s libovolným kontejnerem Cognitive Services. Před použitím tohoto receptu musí být vytvořen prostředek služby vnímání. Každá služba pro rozpoznávání, která podporuje kontejnery, má článek Jak nainstalovat produkt pro instalaci a konfiguraci služby pro kontejner. Některé služby vyžadují jako vstup pro kontejner soubor nebo sadu souborů, je důležité, abyste před použitím tohoto řešení porozuměli a úspěšně použili kontejner.

* Prostředek Azure pro službu pro rozpoznávání Azure, kterou používáte.
* **Adresa URL koncového bodu** služby vnímání – zkontrolujte konkrétní službu "Jak nainstalovat" pro kontejner, abyste zjistili, kde adresa URL koncového bodu je v rámci Azure Portal a jak vypadá správný příklad adresy URL. Přesný formát se může změnit ze služby na službu.
* **Klíč** služby pro rozpoznávání – klíče jsou na stránce **klíče** pro prostředek Azure. Potřebujete jenom jeden ze dvou klíčů. Klíč je řetězec o 32 alfanumerických znaků.

* Jeden Cognitive Services kontejner na místním hostiteli (Váš počítač). Ujistěte se, že máte tyto:
  * Vyžádat si obrázek z části pomocí `docker pull` příkazu.
  * Spusťte místní kontejner úspěšně se všemi požadovanými nastaveními konfigurace pomocí `docker run` příkazu.
  * Zavolejte koncový bod kontejneru a dostanete odpověď HTTP 2xx a odpověď JSON zpátky.

Všechny proměnné v lomených závorkách, `<>` musí být nahrazeny vlastními hodnotami. Tato náhrada zahrnuje lomené závorky.

> [!IMPORTANT]
> Kontejner LUIS vyžaduje `.gz` soubor modelu, který je načítán za běhu. Kontejner musí být schopný získat přístup k tomuto souboru modelu prostřednictvím připojení svazku z instance kontejneru. Chcete-li nahrát soubor modelu, postupujte podle následujících kroků:
> 1. [Vytvořte sdílenou složku Azure](../../storage/files/storage-how-to-create-file-share.md). Poznamenejte si název účtu Azure Storage, klíč a název sdílené složky, abyste je mohli později potřebovat.
> 2. [exportujte model Luis (zabalenou aplikaci) z portálu Luis](../LUIS/luis-container-howto.md#export-packaged-app-from-luis). 
> 3. V Azure Portal přejděte na stránku **Přehled** prostředku účtu úložiště a vyberte **sdílené složky**. 
> 4. Vyberte název sdílené složky, který jste nedávno vytvořili, a pak vyberte **Odeslat**. Pak nahrajte zabalenou aplikaci. 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

[!INCLUDE [Portal instructions for creating an ACI instance](includes/create-container-instances-resource.md)]

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/cli)

[!INCLUDE [CLI instructions for creating an ACI instance](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

---


## <a name="use-the-container-instance"></a>Použití instance kontejneru

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Vyberte **Přehled** a zkopírujte IP adresu. Bude to číselná IP adresa, třeba `55.55.55.55` .
1. Otevřete novou kartu prohlížeče a použijte IP adresu, například `http://<IP-address>:5000 (http://55.55.55.55:5000` ). Zobrazí se Domovská stránka kontejneru, která vám umožní zjistit, že je kontejner spuštěný.

    ![Domovská stránka kontejneru](../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

1. Když vyberete **Popis rozhraní API služby** , zobrazí se stránka Swagger pro kontejner.

1. Vyberte libovolné rozhraní API pro **post** a vyberte **vyzkoušet**.  Zobrazí se parametry včetně vstupu. Zadejte parametry.

1. Výběrem příkazu **Spustit** odešlete požadavek do vaší instance kontejneru.

    Úspěšně jste vytvořili a použili kontejnery Cognitive Services ve službě Azure Container instance.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/cli)

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

> [!NOTE]
> Pokud používáte Analýza textu pro kontejner stavu, odešlete dotazy pomocí následující adresy URL: `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health`

---
