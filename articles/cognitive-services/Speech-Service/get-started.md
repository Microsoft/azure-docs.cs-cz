---
title: Vyzkoušejte si službu Speech Service zdarma
titleSuffix: Azure Cognitive Services
description: Začínáme se službou Speech je snadná a cenově dostupná. 30denní bezplatná zkušební verze vám umožní zjistit, co služba umožňuje, a rozhodnout se, jestli je pro potřeby vaší aplikace nejvhodnější.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: 46ada91060aa095b7c041ff75abb6256f3d05853
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464693"
---
# <a name="try-speech-services-for-free"></a>Vyzkoušejte si hlasové služby zdarma

Začínáme se službami Speech Services jsou snadno a cenově dostupné. 30denní bezplatná zkušební verze vám umožní zjistit, co služba umožňuje, a rozhodnout se, jestli je pro potřeby vaší aplikace nejvhodnější.

Pokud potřebujete víc času, zaregistrujte si účet Microsoft Azure – najdete ho v kreditu služby $200, který můžete uplatnit na placené předplatné služby Speech Services po dobu až 30 dnů.

Služba rozpoznávání řeči nakonec nabízí bezplatnou a nízkou úroveň, která je vhodná pro vývoj aplikací. Toto bezplatné předplatné si můžete nechat i po vypršení platnosti vašeho kreditu služby.

## <a name="free-trial"></a>Bezplatná zkušební verze

30denní bezplatná zkušební verze vám umožní přístup ke standardní cenové úrovni po dobu omezeného času.

Pokud se chcete zaregistrovat ke 30denní bezplatné zkušební verzi:

1. [Zkuste Cognitive Services vyzkoušet](https://azure.microsoft.com/try/cognitive-services/).

1. Vyberte kartu **rozhraní API pro rozpoznávání řeči** .

   ![Speech API – karta služby Speech Services](media/index/cognitive-services-speech-api-tab.png)

1. V části **řeč služby**vyberte **získat klíč rozhraní API**.

   ![Rozhraní Speech API – získat klíč rozhraní API](media/index/speech-api-get-api-key.png)

1. Odsouhlaste podmínky a vyberte své národní prostředí z rozevírací nabídky.

   ![API pro rozpoznávání řeči – Souhlasím s podmínkami](media/index/speech-api-agree-to-terms.png)

1. Přihlaste se pomocí účtu Microsoft, Facebooku, LinkedIn nebo GitHubu.

    Na [portálu účet Microsoft](https://account.microsoft.com/account)si můžete zaregistrovat bezplatný účet Microsoft. Chcete-li začít, vyberte možnost **Přihlásit se účtem Microsoft** a potom po zobrazení výzvy k přihlášení vyberte možnost **vytvořit.** Při vytváření a ověřování nových účet Microsoft postupujte podle pokynů.

Po přihlášení k pokusu o Cognitive Services se zahájí vaše bezplatné zkušební období. Zobrazená webová stránka obsahuje seznam všech služeb Azure Cognitive Services, pro které aktuálně máte zkušební předplatné. Vedle **služby Speech Services**jsou uvedeny dva klíče předplatného. Ve svých aplikacích můžete použít libovolný klíč.

> [!NOTE]
> Všechna bezplatná zkušební předplatná jsou v oblasti Západní USA. Při vytváření žádostí nezapomeňte použít koncový bod `westus`.

## <a name="new-azure-account"></a>Nový účet Azure

Nové účty Azure obdrží kredit služby $200, který je k dispozici po dobu až 30 dnů. Tento kredit můžete využít k dalšímu zkoumání služeb řeči nebo k zahájení vývoje aplikací.

Pokud si chcete zaregistrovat nový účet Azure, klikněte na [přihlašovací stránku Azure](https://azure.microsoft.com/free/ai/), vyberte **začít zdarma** a vytvořte nový účet Azure pomocí účet Microsoft.

Na [portálu účet Microsoft](https://account.microsoft.com/account)si můžete zaregistrovat bezplatný účet Microsoft. Chcete-li začít, vyberte možnost **Přihlásit se účtem Microsoft** a potom po zobrazení výzvy k přihlášení vyberte možnost **vytvořit.** Při vytváření a ověřování nových účet Microsoft postupujte podle pokynů.

Po vytvoření účtu Azure postupujte podle pokynů v následující části a spusťte předplatné služeb pro rozpoznávání řeči.

## <a name="create-a-speech-resource-in-azure"></a>Vytvoření prostředku řeči v Azure

Postup přidání prostředku služby Speech Services (bezplatné nebo placené úrovně) do účtu Azure:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účet Microsoft.

1. V levém horním rohu portálu vyberte **vytvořit prostředek** .

    ![Rozhraní Speech API – vytvoření prostředku](media/index/speech-api-create-resource.png)

1. V **novém** okně vyhledejte **řeč**.

1. Ve výsledcích hledání vyberte **řeč**.

    ![Rozhraní Speech API – výběr řeči](media/index/speech-api-select-speech.png)

1. V části **řeč**vyberte tlačítko **vytvořit** .

    ![Speech API – tlačítko vytvořit](media/index/speech-api-create-button.png)

1. V části **vytvořit**zadejte:

   * Název nového prostředku. Název vám pomůže rozlišovat mezi několika předplatnými stejné služby.
   * Vyberte předplatné Azure, ke kterému je přiřazen nový prostředek, abyste zjistili, jak se účtují poplatky.
   * Vyberte [oblast](regions.md) , ve které se prostředek použije.
   * Vyberte si bezplatnou nebo placenou cenovou úroveň. Pro úplné informace o cenách a kvótách využití pro jednotlivé úrovně vyberte **Zobrazit úplné podrobnosti o cenách** .
   * Vytvořte novou skupinu prostředků pro toto předplatné řeči nebo přiřaďte předplatné k existující skupině prostředků. Skupiny prostředků vám pomůžou udržovat vaše různá předplatná Azure uspořádaná.
   * Pro pohodlné přístup k vašemu předplatnému v budoucnu zaškrtněte políčko **Připnout na řídicí panel** .
   * Vyberte **vytvořit.**

     ![Rozhraní Speech API – vyberte vytvořit.](media/index/speech-api-select-create.png)

     Vytvoření a nasazení nového prostředku řeči chvíli trvá. Kliknutím na **rychlý Start** zobrazíte informace o novém prostředku.

     ![Rozhraní Speech API – nasazení prostředku](media/index/speech-api-deploy-resource.png)

1. V části **rychlý Start**vyberte v části Krok 1 odkaz **klíče** a zobrazte klíče předplatného. Každé předplatné má dva klíče; v aplikaci můžete použít libovolný klíč. Vyberte tlačítko vedle každého klíče a zkopírujte ho do schránky pro vložení do kódu.

> [!NOTE]
> V jedné nebo několika oblastech můžete vytvořit neomezený počet předplatných na úrovni Standard. Můžete ale vytvořit jenom jeden předplatné na bezplatné úrovni. Nasazení modelů na bezplatné úrovni, která zůstanou nevyužitá 7 dní, se vyřadil automaticky.

## <a name="switch-to-a-new-subscription"></a>Přepnout na nové předplatné

Pokud chcete přejít z jednoho předplatného do jiného, například když vyprší platnost vaší bezplatné zkušební verze nebo když publikujete aplikaci, nahraďte oblast a klíč předplatného v kódu s použitím oblasti a klíče předplatného nového prostředku Azure.

> [!NOTE]
> V oblasti Západní USA (`westus`) se vytvoří bezplatné zkušební klíče. Předplatné vytvořené prostřednictvím řídicího panelu Azure může být v některých jiných oblastech, pokud si zvolíte.

* Pokud vaše aplikace používá [sadu Speech SDK](speech-sdk.md), při vytváření konfigurace řeči zadáte kód oblasti, například `westus`.
* Pokud vaše aplikace používá jedno z [rozhraní REST API](rest-apis.md)služby Speech Services, je tato oblast součástí identifikátoru URI koncového bodu, který používáte při provádění požadavků.

Klíče vytvořené pro oblast jsou platné pouze v této oblasti. Při pokusu o jejich použití s ostatními oblastmi dojde k chybám ověřování.

## <a name="next-steps"></a>Další kroky

Projděte si jeden z našich rychlých startů po deseti minutách nebo se podívejte na naše ukázky sady SDK:

> [!div class="nextstepaction"]
> [Rychlý Start: rozpoznávání řeči C# v](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) 
> [ukázek sady Speech pro rozpoznávání](speech-sdk.md#get-the-samples) řeči
