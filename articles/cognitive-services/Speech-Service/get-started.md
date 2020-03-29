---
title: Vyzkoušejte si službu Speech zdarma
titleSuffix: Azure Cognitive Services
description: Začínáme se službou Speech je snadné a cenově dostupné. K dispozici jsou zdarma dvě možnosti, takže můžete zjistit, co může služba dělat, a rozhodnout se, zda je to správné pro vaše potřeby.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: f74e3ea3d20ad2666b434e009cf62add6f88d200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219662"
---
# <a name="try-the-speech-service-for-free"></a>Vyzkoušejte si službu Speech zdarma

V tomto článku zvolíte možnost snadno otestovat službu Řeči zdarma, abyste mohli zjistit, co služba může dělat, a rozhodnout, zda je to správné pro vaše potřeby. Vyberte jednu ze dvou následujících možností v závislosti na vaší situaci a případu použití:

- [Možnost 1:](#no-card)Okamžitě získejte **bezplatné zkušební** klíče API bez poskytnutí informací o kreditní kartě (musíte mít existující účet Azure). **Bezplatná zkušební verze** trvá 30 dní a data jsou na konci smazána. Tato možnost je nejvhodnější pro rychlé experimentování se službou.
- [Možnost 2:](#new-resource)Vytvořte nový prostředek řeči v Azure, a to zdarma pomocí **bezplatného předplatného** (je vyžadována informace o kreditní kartě). **Bezplatné předplatné** má hlavně přísnější sazby než placené předplatné. Tato možnost je nejlepší, pokud chcete otestovat službu, ale také plánujete upgrade na placené předplatné v budoucnu a nechcete ztratit data.

## <a name="try-the-speech-service-without-credit-card-info"></a><a id="no-card"></a>Vyzkoušejte službu Řeč bez informací o kreditní kartě

Chcete-li aktivovat 30denní bezplatnou zkušební verzi a získat klíče rozhraní API, proveďte následující kroky. Zkušební doba začíná okamžitě po dokončení následujících kroků.

1. Přejděte na [try Cognitive Service](https://azure.microsoft.com/try/cognitive-services/).
1. Vyberte kartu **Řečová api.**
1. Zvolte **Získat klíč rozhraní API**.

Zobrazí se vám možnosti fakturace. Zvolte bezplatnou možnost a pak si přečtěte a schvalte smlouvu s uživatelem. Zobrazí se s klíči, které můžete použít k vyzkoušení služby Speech zdarma po dobu 30 dnů.

## <a name="try-the-speech-service-by-creating-an-azure-resource"></a><a id="new-resource"></a>Vyzkoušejte službu Řeč vytvořením prostředku Azure

Pro následující kroky potřebujete účet Microsoft i účet Azure. Pokud nemáte účet Microsoft, můžete si ho zdarma zaregistrovat na [portálu účtu Microsoft](https://account.microsoft.com/account). Vyberte **Přihlásit se u Microsoftu** a potom po tonku k přihlášení vyberte **Vytvořit účet Microsoft**. Podle pokynů vytvořte a ověřte nový účet Microsoft.

Až budete mít účet Microsoft, přejděte na [stránku registrace do Azure](https://azure.microsoft.com/free/ai/), vyberte **Spustit zdarma**a vytvořte nový účet Azure pomocí účtu Microsoft.

> [!NOTE]
> Služba Speech má dvě úrovně služby: free a subscription, které mají různá omezení a výhody. Když si zaregistrujete bezplatný účet Azure, je dodáván s kreditem služby $200, který můžete použít na placené předplatné služby Speech, které platí až 30 dní.
>
> Pokud používáte bezplatnou úroveň služby Speech s nízkým objemem, můžete si toto bezplatné předplatné ponechat i po vypršení bezplatné zkušební verze nebo kreditu služby.
>
> Další informace naleznete [v tématu Oceňování služeb Cognitive Services – služba Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-resource"></a>Vytvoření zdroje

Přidání prostředku služby Speech (bezplatné nebo placené úrovně) do účtu Azure:

1. Přihlaste se na [portál Azure](https://portal.azure.com/) pomocí svého účtu Microsoft.

1. V levém horním rohu portálu vyberte **Vytvořit zdroj.** Pokud nevidíte **Vytvořit prostředek**, můžete ho vždy najít výběrem sbalené nabídky v levém horním rohu:

   ![sbalené navigační tlačítko](media/index/collapsed-nav.png)

1. Do **vyhledávacího** pole zadejte "řeč" a stiskněte klávesu ENTER.

1. Ve výsledcích hledání vyberte **možnost Řeč**.

   ![výsledky hledání řeči](media/index/speech-search.png)

1. Vyberte **Vytvořit**, pak:

   - Pojmenujte nový zdroj. Název vám pomůže rozlišovat mezi více předplatnými vázanými na stejnou službu.
   - Zvolte předplatné Azure, ke kterému je nový prostředek přidružen, a určete, jak se budou poplatky účtovat.
   - Zvolte [oblast,](regions.md) kde bude zdroj použit.
   - Zvolte bezplatnou (F0) nebo placenou (S0) cenovou úroveň. Úplné informace o cenách a kvótách využití pro každou úroveň vyberte **Zobrazit úplné podrobnosti o cenách**.
   - Vytvořte novou skupinu prostředků pro toto předplatné řeči nebo přiřaďte předplatné existující skupině prostředků. Skupiny prostředků vám pomůžou uspořádat různá předplatná Azure.
   - Vyberte **Vytvořit**. Tím přejdete na přehled nasazení a zobrazíte zprávy o průběhu nasazení.

> [!NOTE]
> Můžete vytvořit neomezený počet předplatných standardní úrovně v jedné nebo více oblastech. Můžete však vytvořit pouze jedno bezplatné předplatné. Nasazení modelu na volné úrovni, které zůstanou nevyužité po dobu 7 dnů, budou automaticky vyřazena z provozu.

Nasazení nového prostředku řeči trvá několik okamžiků. Po dokončení nasazení vyberte **Přejít na prostředek** a v levém navigačním podokně vyberte **Klávesy,** chcete-li zobrazit klíče předplatného služby Speech. Každé předplatné má dva klíče. můžete použít buď klíč v aplikaci. Chcete-li rychle zkopírovat/vložit klíč do editoru kódu nebo do jiného umístění, vyberte tlačítko kopírovat vedle každé klávesy, přepněte okna a vložte obsah schránky do požadovaného umístění.

> [!IMPORTANT]
> Tyto klíče předplatného se používají pro přístup k rozhraní API služby Cognitive Service. Nesdílejte své klíče. Ukláňte je bezpečně, například pomocí azure key vaultu. Doporučujeme také pravidelně regenerovat tyto klíče. K volání rozhraní API je nutný pouze jeden klíč. Při obnově prvního klíče můžete použít druhý klíč pro trvalý přístup ke službě.

## <a name="switch-to-a-new-subscription"></a>Přechod na nové předplatné

Pokud chcete přepnout z jednoho předplatného na druhé, například když vyprší vaše bezplatná zkušební verze nebo když publikujete aplikaci, nahraďte oblast a klíč předplatného v kódu pomocí oblasti a klíče předplatného nového prostředku Azure.

## <a name="about-regions"></a>O oblastech

- Pokud vaše aplikace používá [sadu Speech SDK](speech-sdk.md), `westus`zadáte kód oblasti, například , při vytváření konfigurace řeči.
- Pokud vaše aplikace používá jeden z renestik služby [RENA API](rest-apis.md), oblast je součástí identifikátoru URI koncového bodu, který používáte při vytváření požadavků.
- Klíče vytvořené pro oblast jsou platné pouze v této oblasti. Pokus o jejich použití s jinými oblastmi bude mít za následek chyby ověřování.

## <a name="next-steps"></a>Další kroky

Dokončete jeden z našich 10minutových rychlých startů nebo se podívejte na naše ukázky sady SDK:

> [!div class="nextstepaction"]
> [Úvodní příručka: Rozpoznávání řeči v](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [ukázkách sady C# Speech SDK](speech-sdk.md#get-the-samples)
