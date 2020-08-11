---
title: Vyzkoušejte si službu Speech zdarma
titleSuffix: Azure Cognitive Services
description: Začínáme se službou Speech je snadná a cenově dostupná. K dispozici jsou dvě možnosti zdarma, abyste mohli zjistit, co služba může udělat, a rozhodnout se, jestli je to pro vaše potřeby správné.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: 5dc1f8ed31c8d7199a31643a76e611d828da77ac
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056461"
---
# <a name="try-the-speech-service-for-free"></a>Vyzkoušejte si službu Speech zdarma

V tomto článku zvolíte možnost snadného testování služby Speech Service zdarma, abyste mohli zjistit, co služba může dělat, a rozhodnout se, jestli je pro vaše potřeby nejvhodnější. V závislosti na vaší situaci a případu použití vyberte jednu z těchto dvou možností:

- [Možnost 1](#no-card): okamžitě Získejte **bezplatné zkušební** klíče rozhraní API bez zadání informací o kreditních kartách (musíte mít existující účet Azure). **Bezplatná zkušební verze** bude trvat 30 dnů a data se odstraní na konci. Tato možnost je vhodná pro rychlé experimenty se službou.
- [Možnost 2](#new-resource): vytvořte nový prostředek řeči v Azure bez poplatků za použití **bezplatného předplatného** (povinné informace o kreditních kartách). **Bezplatné předplatné** zahrnuje zejména přísnější omezení sazeb než placené předplatné. Tato možnost je vhodná, pokud chcete službu otestovat, ale také Naplánujte upgrade na placené předplatné v budoucnu a nechcete přijít o data.

## <a name="try-the-speech-service-without-credit-card-info"></a><a id="no-card"></a>Vyzkoušet službu Speech bez informací o kreditní kartě

Provedením následujících kroků aktivujete 30denní bezplatnou zkušební verzi a získáte klíče rozhraní API. Zkušební období se spustí hned po dokončení následujících kroků.

1. Přejít na [vyzkoušet službu rozpoznávání](https://azure.microsoft.com/try/cognitive-services/).
1. Vyberte kartu **rozhraní API pro rozpoznávání řeči** .
1. Vyberte **získat klíč rozhraní API**.

Budou vám nabídnuty možnosti fakturace. Zvolte možnost Free a pak si přečtěte a schvalte smlouvu uživatele. Zobrazí se klíče, pomocí kterých můžete vyzkoušet službu Speech Service zdarma po dobu 30 dnů.

## <a name="try-the-speech-service-by-creating-an-azure-resource"></a><a id="new-resource"></a>Vyzkoušejte službu rozpoznávání řeči vytvořením prostředku Azure

Pro následující kroky potřebujete účet Microsoft i účet Azure. Pokud nemáte účet Microsoft, můžete si na [portálu účet Microsoft](https://account.microsoft.com/account)zdarma zaregistrovat bezplatný poplatek. Vyberte **Přihlásit se účtem Microsoft** a po zobrazení výzvy k přihlášení vyberte **vytvořit účet Microsoft**. Při vytváření a ověřování nových účet Microsoft postupujte podle pokynů.

Jakmile budete mít účet Microsoft, navštivte [stránku registrace do Azure](https://azure.microsoft.com/free/ai/), vyberte **začít zdarma**a vytvořte nový účet Azure pomocí účet Microsoft.

> [!NOTE]
> Služba Speech má dvě úrovně služeb: Free a Subscription, které mají různá omezení a výhody. Když si zaregistrujte bezplatný účet Azure, najdete ho v kreditu služby $200, který můžete použít k placenému předplatnému služby Speech Service, které platí až po dobu až 30 dnů.
>
> Pokud používáte bezplatnou úroveň služby řeči s nízkým objemem, můžete si toto bezplatné předplatné ponechat i po vypršení bezplatné zkušební verze nebo kreditu služby.
>
> Další informace najdete v tématu [Cognitive Services Price-Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-resource"></a>Vytvoření prostředku

Postup přidání prostředku služby řeči (bezplatná nebo placená úroveň) do účtu Azure:

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com/) pomocí svého účtu Microsoft.

1. V levém horním rohu portálu vyberte **vytvořit prostředek** . Pokud se vám nepovede **vytvořit prostředek**, můžete ho vždycky najít tak, že v levém horním rohu vyberete sbalenou nabídku:

   ![sbalené navigační tlačítko](media/index/collapsed-nav.png)

1. V **novém** okně zadejte do vyhledávacího pole text "Speech" a stiskněte klávesu ENTER.

1. Ve výsledcích hledání vyberte **řeč**.

   ![výsledky hledání řeči](media/index/speech-search.png)

1. Vyberte **vytvořit**a potom:

   - Zadejte jedinečný název nového prostředku. Název vám pomůže rozlišovat mezi několika předplatnými vázanými na stejnou službu.
   - Vyberte předplatné Azure, ke kterému je přiřazen nový prostředek, abyste zjistili, jak se účtují poplatky.
   - Vyberte [oblast](regions.md) , ve které se prostředek použije.
   - Vyberte cenovou úroveň Free (F0) nebo Place (S0). Pokud chcete získat úplné informace o cenách a kvótách využití pro každou úroveň, vyberte **Zobrazit úplné podrobnosti o cenách**.
   - Vytvořte novou skupinu prostředků pro toto předplatné řeči nebo přiřaďte předplatné k existující skupině prostředků. Skupiny prostředků vám pomůžou udržovat vaše různá předplatná Azure uspořádaná.
   - Vyberte **Vytvořit**. Tím přejdete na přehled nasazení a zobrazíte zprávy o průběhu nasazení.

> [!NOTE]
> V jedné nebo několika oblastech můžete vytvořit neomezený počet předplatných na úrovni Standard. Můžete ale vytvořit jenom jeden předplatné na bezplatné úrovni. Nasazení modelů na bezplatné úrovni, která zůstanou nevyužitá po dobu 7 dní, se automaticky vyřadí z provozu.

Nasazení nového prostředku řeči chvíli trvá. Po dokončení nasazení vyberte **Přejít k prostředku** a v levém navigačním podokně vyberte **klíče** . zobrazí se klíče předplatného služby Speech. Každé předplatné má dva klíče; v aplikaci můžete použít libovolný klíč. Chcete-li rychle zkopírovat nebo vložit klíč do editoru kódu nebo jiné umístění, vyberte tlačítko Kopírovat vedle každého klíče, přepněte okna a vložte obsah schránky do požadovaného umístění.

> [!IMPORTANT]
> Tyto klíče předplatného se používají pro přístup ke službě rozpoznávání rozhraní API. Nesdílejte své klíče. Uložte je bezpečně – například pomocí Azure Key Vault. Tyto klíče doporučujeme také pravidelně obnovovat. K volání rozhraní API je potřeba jenom jeden klíč. Při opětovném generování prvního klíče můžete použít druhý klíč pro pokračování přístupu ke službě.

## <a name="switch-to-a-new-subscription"></a>Přepnout na nové předplatné

Pokud chcete přejít z jednoho předplatného do jiného, například když vyprší platnost vaší bezplatné zkušební verze nebo když publikujete aplikaci, nahraďte oblast a klíč předplatného v kódu s použitím oblasti a klíče předplatného nového prostředku Azure.

## <a name="about-regions"></a>O oblastech

- Pokud vaše aplikace používá [sadu Speech SDK](speech-sdk.md), při vytváření konfigurace řeči zadáte kód oblasti, například `westus` .
- Pokud vaše aplikace používá jedno z [rozhraní REST API](rest-apis.md)služby pro rozpoznávání řeči, je tato oblast součástí identifikátoru URI koncového bodu, který používáte při provádění požadavků.
- Klíče vytvořené pro oblast jsou platné pouze v této oblasti. Při pokusu o jejich použití s ostatními oblastmi dojde k chybám ověřování.

## <a name="next-steps"></a>Další kroky

Projděte si jeden z našich rychlých startů po deseti minutách nebo se podívejte na naše ukázky sady SDK:

> [!div class="nextstepaction"]
> [Rychlý Start: rozpoznávání řeči pomocí funkce Speech CLI](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programmer-tool-spx) 
>  [Ukázky sady Speech SDK](speech-sdk.md#sample-source-code)
