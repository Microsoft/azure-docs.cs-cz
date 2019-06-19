---
title: Azure Container Instance předpisu
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak nasadit kontejnery Cognitive Services na instanci kontejneru Azure
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 06/12/2019
ms.author: dapine
ms.openlocfilehash: 5de11b62bb2a2302c247907627e27339400fcf25
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207488"
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

## <a name="step-2-launch-your-container-on-azure-container-instances-aci"></a>Krok 2: Spuštění kontejneru v Azure Container Instances (ACI)

**Vytváří se prostředek služby Azure Container Instance (ACI).**

1. Přejděte [vytvořit](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) stránky pro Container Instances.

1. Na **Základy** kartu, zadejte následující údaje:

    |Stránka|Nastavení|Hodnota|
    |--|--|--|
    |Základy|Předplatné|Vyberte své předplatné.|
    |Základy|Skupina prostředků|Vyberte skupinu prostředků nebo vytvořte novou, jako `cognitive-services`.|
    |Základy|Název kontejneru|Zadejte název, například `cognitive-container-instance`. Tento název musí být velkými písmeny nižší.|
    |Základy|Location|Vyberte oblast pro nasazení.|
    |Základy|Typ obrázku|`Public`|
    |Základy|Název Image|Zadejte umístění kontejneru Cognitive Services. To může být na stejném místě jste použili v `docker pull` příkazu _například_: <br>`mcr.microsoft.com/azure-cognitive-services/sentiment`|
    |Základy|Typ operačního systému|`Linux`|
    |Základy|Velikost|Změnit velikost na navrhované doporučení pro vaše konkrétní kontejner služby Cognitive Services.:<br>2 jádra<br>4 GB
    ||||
  
1. Na **sítě** kartu, zadejte následující údaje:

    |Stránka|Nastavení|Hodnota|
    |--|--|--|
    |Sítě|Porty|Upravit existující port pro protokol TCP ze `80` k `5000`. To znamená, že jsou vystavení kontejneru na portu 5000.|
    ||||

1. Na **Upřesnit** kartu, zadejte následující podrobnosti o předávání kontejneru vyžaduje fakturace nastavení prostředků Instance kontejneru:

    |Stránka Upřesnit klíč|Hodnota pokročilé stránky|
    |--|--|
    |`apikey`|Zkopírovány z **klíče** stránky prostředku. Budete potřebovat pouze dva klíče. Jedná se o 32 alfanumerický znak řetězec bez mezery, pomlčky, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Zkopírovány z **přehled** stránky prostředku. |
    |`eula`|`accept`|

    Pokud váš kontejner má další nastavení konfigurace, jako jsou vstupní připojení, připojí výstupu nebo protokolování, tato nastavení také muset přidat.

1. Vyberte **zkontrolovat a vytvořit**.
1. Jakmile úspěšně proběhne ověření, vyberte **vytvořit** na dokončení procesu vytváření.
1. Vyberte ikonu zvonku v horním navigačním panelu. Toto je okno oznámení. Zobrazí se modrý **přejít k prostředku** tlačítko, pokud je prostředek vytvořený. Vyberte toto tlačítko Přejít nový prostředek.

## <a name="use-the-container-instance"></a>Použijte instanci kontejneru

1. Vyberte **přehled** a zkopírujte IP adresu. Je číselný IP adresu, jako `55.55.55.55`.
1. Otevře nová karta prohlížeče a použijte IP adresu, například `http://<IP-address>:5000 (http://55.55.55.55:5000`). Zobrazí se domovská stránka kontejneru, oznamující, že je kontejner spuštěný.

1. Vyberte **popis rozhraní API služby** zobrazíte stránku swagger pro kontejner.

1. Vyberte některou z **příspěvek** rozhraní API a vyberte **vyzkoušet**.  Parametry jsou zobrazeny, včetně vstupu. Vyplňte parametry.

1. Vyberte **Execute** odešlete žádost instanci kontejneru.

    Úspěšně jste vytvořili a použili služby Cognitive Services kontejnery v Azure Container Instance.
