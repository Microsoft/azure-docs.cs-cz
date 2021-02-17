---
title: Vytvoření prostředku překladatele
titleSuffix: Azure Cognitive Services
description: Tento článek vám ukáže, jak vytvořit prostředek služby Azure Cognitive Services Translator a získat klíč předplatného a adresu URL koncového bodu.
services: cognitive-services
author: laujan
ms.author: lajanuar
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: 37b9860546f9fd16045906ce9385a1590c46b970
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556340"
---
# <a name="create-a-translator-resource"></a>Vytvoření prostředku překladatele

V tomto článku se dozvíte, jak vytvořit prostředek překladatele v Azure Portal. [Azure Translator](translator-info-overview.md) je cloudová služba strojového překladu, která je součástí Cognitive Services řady rozhraní REST API pro [Azure](../what-are-cognitive-services.md) . Prostředky Azure jsou instance služby, které vytvoříte. Všechny požadavky rozhraní API na služby Azure vyžadují pro ověřování přístupu adresu URL **koncového bodu** a **klíč předplatného** jen pro čtení.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat aktivní [**účet Azure**](https://azure.microsoft.com/free/cognitive-services/).  Pokud ho nemáte, můžete si [**vytvořit bezplatné předplatné na 12 měsíců**](https://azure.microsoft.com/free/).

## <a name="translator-resource-types"></a>Typy prostředků překladatele

Ke službě Translator můžete přistupovat prostřednictvím dvou různých typů prostředků:

* Typy prostředků s **jednou službou** umožňují přístup k jedinému klíči rozhraní API služby a koncovému bodu.  

* Typy prostředků s **více službami** umožňují přístup k několika Cognitive Servicesům pomocí jediného klíče rozhraní API a koncového bodu. Prostředek Cognitive Services je v tuto chvíli k dispozici pro následující služby:
  * Language ([Translator](../translator/translator-info-overview.md); [Language Understanding (LUIS)](../luis/what-is-luis.md); [Analýza textu](../text-analytics/overview.md))  
  * Vision ([počítačové zpracování obrazu](../computer-vision/overview.md)), ([obličej](../face/overview.md))  
  * Rozhodnutí ([Content moderator](../content-moderator/overview.md))  

## <a name="create-your-resource"></a>Vytvoření prostředku

* Přejděte přímo na stránku [**vytvořit Překladatel**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) v Azure Portal, abyste mohli dokončit podrobnosti projektu.

* Přejděte přímo na stránku [**vytvořit Cognitive Services**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) v Azure Portal a dokončete podrobnosti projektu.

>[!TIP]
>Pokud chcete, můžete na domovské stránce portálu Azure začít proces **vytváření** následujícím způsobem:
>
> 1. Přejděte na domovskou stránku [**portálu Azure**](https://ms.portal.azure.com/#home) .
> 1. V nabídce služby Azure vyberte ➕**vytvořit prostředek**  .
>1. Do vyhledávacího pole **Hledat na Marketplace** zadejte a vyberte **Překladatel** (prostředek s jednou službou) nebo **Cognitive Services** (prostředek s více službami).  *Podívejte se na téma* [Výběr typu prostředku](#create-your-resource)výše.
> 1. Vyberte **vytvořit** a zobrazí se stránka s podrobnostmi projektu.
><br/><br/>

## <a name="complete-your-project-and-instance-details"></a>Dokončení projektu a podrobností instance

1. **Předplatné**. Vyberte jedno z dostupných předplatných Azure.

1. **Skupina prostředků:** Skupina prostředků Azure, kterou si zvolíte, slouží jako virtuální kontejner pro nový prostředek. Můžete vytvořit novou skupinu prostředků nebo přidat prostředek do již existující skupiny prostředků, která sdílí stejný životní cyklus, oprávnění a zásady.

1. **Oblast prostředků** Pokud vaše firma nebo aplikace nevyžaduje konkrétní oblast, vyberte možnost **globální** . Translator je neoblastní služba – neexistují žádné závislosti na konkrétní oblasti Azure. *Podívejte* se [na oblasti a zóny dostupnosti v Azure](/azure/availability-zones/az-overview).

1. **Název:** Zadejte název, který jste zvolili pro váš prostředek. Název, který zvolíte, musí být jedinečný v rámci Azure.

> [!NOTE]
> Pokud používáte funkci překladatele, která vyžaduje vlastní koncový bod domény, hodnota, kterou zadáte do pole název, bude parametr vlastní název domény pro koncový bod.

5. **Cenová úroveň:** Vyberte [cenovou úroveň](https://azure.microsoft.com/pricing/details/cognitive-services/translator) , která vyhovuje vašim potřebám:

   * Každé předplatné má bezplatnou úroveň.
   * Úroveň Free má stejné funkce jako placené plány a nevyprší jejich platnost.
   * Pro každý účet je povolený jenom jeden bezplatný odběr.</li></ul>

1. Pokud jste vytvořili prostředek s více službami, budete muset pomocí zaškrtávacích políček potvrdit další podrobnosti o použití.

1. Vyberte **Zkontrolovat a vytvořit**.

1. Zkontrolujte podmínek služby a vyberte **vytvořit** a nasaďte prostředek.

1. Po úspěšném nasazení prostředku vyberte **Přejít k prostředku**.

### <a name="authentication-keys-and-endpoint-url"></a>Ověřovací klíče a adresa URL koncového bodu

Všechny požadavky Cognitive Services API vyžadují pro ověřování adresu URL koncového bodu a klíč jen pro čtení.

* **Ověřovací klíče**. Váš klíč je jedinečný řetězec, který se předává při každém požadavku na službu překladu. Svůj klíč můžete předat pomocí parametru řetězce dotazu nebo zadáním v hlavičce požadavku HTTP.

* **Adresa URL koncového bodu** Pokud nepotřebujete určitou oblast Azure, použijte v žádosti rozhraní API globální koncový bod. *Viz* [základní adresy URL](reference/v3-0-reference.md#base-urls). Adresa URL globálního koncového bodu je `api.cognitive.microsofttranslator.com` .

## <a name="get-your-authentication-keys-and-endpoint"></a>Získání ověřovacích klíčů a koncového bodu

1. Po nasazení nového prostředku vyberte **Přejít k prostředku** nebo přejděte přímo na stránku prostředku.
1. V levé kolejnici v části *Správa prostředků* vyberte **klíče a koncový bod**.
1. Zkopírujte a vložte klíč předplatného a adresu URL koncového bodu do vhodného umístění, například do *programu Poznámkový blok společnosti Microsoft*.

:::image type="content" source="../media/cognitive-services-apis-create-account/get-cog-serv-keys.png" alt-text="Získejte klíč a koncový bod.":::

## <a name="how-to-delete-a--resource-or-resource-group"></a>Postup odstranění prostředku nebo skupiny prostředků

> [!Warning]
> Odstraněním skupiny prostředků se odstraní také všechny prostředky obsažené ve skupině.

Pokud chcete odebrat prostředek Cognitive Services nebo překladatele, můžete **prostředek odstranit** nebo **Odstranit skupinu prostředků**.

Odstranění prostředku:

1. Přejděte do skupiny prostředků v Azure Portal.
1. Výběrem sousedícího zaškrtávacího políčka vyberte prostředky, které chcete odstranit.
1. V horní nabídce u pravého okraje vyberte **Odstranit** .
1. V dialogovém okně **odstraněné prostředky** zadejte *Ano* .
1. Vyberte **Odstranit**.

Odstranění skupiny prostředků:

1. Přejděte do skupiny prostředků v Azure Portal.
1. Vyberte **Odstranit skupinu prostředků** z horního panelu nabídek poblíž levého okraje.
1. Potvrďte požadavek na odstranění zadáním názvu skupiny prostředků a vybráním možnosti **Odstranit**.

## <a name="how-to-get-started-with-translator"></a>Jak začít používat překladatele

V našem rychlém startu se dozvíte, jak používat službu Translator s rozhraním REST API.

> [!div class="nextstepaction"]
> [Začínáme se překladatelem](quickstart-translator.md)

## <a name="more-resources"></a>Další zdroje informací

* [Ukázky kódu Microsoft translatoru](https://github.com/MicrosoftTranslator)  Ukázky kódu pro více jazyků jsou k dispozici na GitHubu.
* [Fórum podpory Microsoft translatoru](https://www.aka.ms/TranslatorForum)
* [Začínáme s Azure (3 – minutové video)](https://azure.microsoft.com/get-started/?b=16.24)
