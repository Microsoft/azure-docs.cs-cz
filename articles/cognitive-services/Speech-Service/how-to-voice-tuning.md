---
title: Doladit výstup hlasu pro převod textu na řeč
titleSuffix: Azure Cognitive Services
description: Povolte protokolování v sadě Speech SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 4cf2338d76ce31f44eaf3fb235e5f8796602d819
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562742"
---
# <a name="fine-tune-text-to-speech-output"></a>Vyladění výstupu převodu textu na řeč

Služba Azure Speech Services umožňuje upravit rychlost, výslovnost, hlasitost, rozteč a obrys výstupu textu na řeč pomocí [jazyka SSML (Speech syntézy)](speech-synthesis-markup.md). SSML je značkovací jazyk založený na jazyce XML, který pomocí značek informuje službu o tom, co funkce vyžaduje ladění. Zpráva SSML se pak pošle v těle každé žádosti na službu převodu textu na řeč. Pro zjednodušení procesu přizpůsobení teď služba Speech Services nabízí nástroj pro [vyladění hlasu](https://aka.ms/voicetuning) , který umožňuje vizuálně kontrolovat a vyladit výstupy textu na řeč v reálném čase.

Nástroj pro vyladění hlasu podporuje [standardní](language-support.md#standard-voices), [neuronové](language-support.md#text-to-speech)a [vlastní hlasy](how-to-customize-voice-font.md)Microsoftu.

## <a name="get-started-with-the-voice-tuning-tool"></a>Začínáme s nástrojem pro vyladění hlasu

Než budete moct začít vyladit výstup převodu textu na řeč pomocí nástroje pro vyladění hlasu, musíte provést tyto kroky:

1. Pokud ho ještě nemáte, vytvořte [bezplatné účet Microsoft](https://account.microsoft.com/account) .
2. Vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) , pokud ho ještě nemáte. Klikněte na **začít zdarma**a vytvořte nový účet Azure pomocí účet Microsoft.

3. Vytvořte předplatné služby Speech Services v Azure Portal. V této fázi jsou k dispozici podrobné pokyny k [vytvoření prostředku řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) .
   >[!NOTE]
   >Při vytváření prostředku řeči v Azure Portal musí být informace o umístění Azure stejné jako hlasová oblast TTS. Hlas neuronové TTS podporuje podmnožinu umístění Azure. Úplný seznam podpory najdete v tématu [oblasti](regions.md#text-to-speech).

   >[!NOTE]
   >Než budete moct službu používat, musíte mít v Azure Portal vytvořený klíč S0 nebo F0. **Ladění** hlasu nepodporuje [30denní bezplatný zkušební klíč](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial).

4. Přihlaste se k portálu [ladění hlasu](https://aka.ms/voicetuning) a připojte své předplatné služby Speech Services. Zvolte jedno předplatné služby Speech Services a pak vytvořte projekt.
5. Vyberte **nové ladění**. Pak postupujte podle těchto kroků:

   * Vyhledejte a vyberte možnost **všechna předplatná**.  
   * Vyberte **připojit stávající předplatné**.  
     ![Připojte stávající předplatné](./media/custom-voice/custom-voice-connect-subscription.png).
   * Zadejte klíč předplatného služby Azure Speech Services a pak vyberte **Přidat**. Klíče předplatného jsou k dispozici na portálu pro přizpůsobení řeči ze [stránky odběr](https://go.microsoft.com/fwlink/?linkid=2090458). Klíče můžete získat také z podokna Správa prostředků v [Azure Portal](https://portal.azure.com/).
   * Pokud máte více než jedno předplatné služby Speech Services, které plánujete použít, opakujte tento postup u každého předplatného.

## <a name="customize-the-text-to-speech-output"></a>Přizpůsobení výstupu textu na řeč

Teď, když jste vytvořili účty a propojili si předplatné, můžete začít ladit výstup textu na řeč.

1. Vyberte hlas.
2. Zadejte text, který chcete upravit.
3. Než začnete s úpravami, přehrajte zvuk, abyste získali dojem výstup.
4. Vyberte slovo/větu, kterou chcete upřesnit, a začněte experimentovat s různými funkcemi založenými na SSML.

>[!TIP]
> Podrobné informace o tom, jak upravit SSML a vyladit výstup hlasu, najdete v tématu [Speech syntéze Language (SSML)](speech-synthesis-markup.md).

## <a name="limitations"></a>Omezení

Ladění hlasu neuronové se mírně liší od ladění pro standardní a vlastní hlasy.

* Pro hlasy neuronové se nepodporuje výbuch.
* Funkce rozteč a Volume fungují jenom s úplnými větami. Tyto funkce nejsou k dispozici na úrovni aplikace Word.
* V případě míry je možné některé hlasy neuronové vyladit na základě slov, zatímco jiné vyžadují, abyste vybrali celé věty.

> [!TIP]
> Nástroj pro ladění hlasu poskytuje kontextové informace o funkcích a ladění.

## <a name="next-steps"></a>Další postup
* [Vytvoření prostředku řeči v Azure](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [Spustit ladění hlasu](https://speech.microsoft.com/app.html#/VoiceTuning)
* [Jazyk SSML (Speech syntézy)](speech-synthesis-markup.md)
