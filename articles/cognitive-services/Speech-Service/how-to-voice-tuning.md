---
title: Vyladění převod textu na řeč výstup – hlasové služby
titleSuffix: Azure Cognitive Services
description: Povolte protokolování v sadě SDK řeči.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 94b58279b1a9fd4d9acdb4183f59b0a8579c17fd
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606449"
---
# <a name="fine-tune-text-to-speech-output"></a>Vyladění výstupu převodu textu na řeč

Hlasové služby Azure umožňují nastavit rychlost, výslovnost, svazek, výšku a rozvrh převod textu na řeč výstupních použití [řeči syntézu Markup Language (SSML)](speech-synthesis-markup.md). SSML je jazyk založený na formátu XML kód, který používá značky k informovat službu, o jaké funkce vyžaduje optimalizace. Zpráva SSML odeslaný v těle každý požadavek na převod textu na řeč služby. Pro zjednodušení procesu přizpůsobení, hlasové služby teď nabízí [hlasové ladění](https://aka.ms/voicetuning) vypíše nástroj, který vám umožní vizuálně kontrolovat a vyladění převod textu na řeč v reálném čase.

Nástroj optimalizace hlasové podporuje od Microsoftu [standardní](language-support.md#standard-voices), [neuronových sítí](language-support.md#text-to-speech), a [vlastní hlasy](how-to-customize-voice-font.md).

## <a name="get-started-with-the-voice-tuning-tool"></a>Začínáme s nástroj Optimalizace hlasu

Před zahájením dolaďování převod textu na řeč výstup pomocí nástroje pro ladění hlasové, budete potřebovat k dokončení těchto kroků:

1. Vytvoření [bezplatný účet Microsoft](https://account.microsoft.com/account) pokud ho ještě nemáte.
2. Vytvoření [bezplatný účet Azure](https://azure.microsoft.com/free/) pokud ho ještě nemáte. Klikněte na tlačítko **začít zdarma**a vytvořit nový účet Azure pomocí účtu Microsoft.

3. Vytvoření odběru hlasové služby na webu Azure Portal. Podrobné pokyny pro [tom, jak vytvořit prostředek řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) jsou k dispozici.
   >[!NOTE]
   >Informace o Azure umístění při vytváření prostředku řeči na webu Azure Portal, musí odpovídat s oblastí hlasové převod textu na ŘEČ. Neurální hlasové převod textu na ŘEČ podporuje dílčí sadu umístění Azure. Úplný seznam podpory najdete v tématu [oblastech](regions.md#text-to-speech).

   >[!NOTE]
   >Musíte mít F0 nebo klíč S0 vytvořili na webu Azure Portal, abyste mohli používat službu. Hlasové optimalizace **nebude** podporu [30denní bezplatné zkušební verze klíče](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial).

4. Přihlaste se k [hlasové ladění](https://aka.ms/voicetuning) portál a připojte vaše předplatné hlasové služby. Zvolte jedno předplatné hlasových služeb a pak vytvořte projekt.
5. Vyberte **nové optimalizace**. Pak postupujte podle těchto kroků:

   * Vyhledejte a vyberte **Všechna předplatná**.  
   * Vyberte **připojit stávající předplatné**.  
     ![Připojit stávající předplatné](./media/custom-voice/custom-voice-connect-subscription.png).
   * Zadejte klíč předplatného Azure hlasových služeb a pak vyberte **přidat**. Klíče předplatného jsou dostupné na portálu přizpůsobení řeči z [stránku odběru služby](https://go.microsoft.com/fwlink/?linkid=2090458). Můžete také získat klíče z podokna Správa prostředků [webu Azure portal](https://portal.azure.com/).
   * Pokud máte více než jedno předplatné hlasové služby, který chcete použít tento postup opakujte u každého odběru.

## <a name="customize-the-text-to-speech-output"></a>Vlastní převod textu na řeč výstupu

Teď, když jste vytvořili účty a propojené předplatné, můžete začít ladění převod textu na řeč výstup.

1. Zvolte hlasový vstup.
2. Zadejte text, který chcete upravit.
3. Před zahájením provádění úprav, přehrávání zvuku získat představu pro výstup.
4. Vyberte word/věty, kterou chcete upravit a začněte experimentovat s různými funkcemi na základě SSML.

>[!TIP]
> Podrobné informace o nastavení SSML a ladění výstupu hlasové, naleznete v tématu [řeči syntézu Markup Language (SSML)](speech-synthesis-markup.md).

## <a name="limitations"></a>Omezení

Neurální ladění hlasu se trochu liší od ladění pro standardní a vlastní hlasy.

* Intonací není podporována pro Neurální hlasy.
* Rozteč a svazek funkce fungovat jenom s úplných větách. Tyto funkce nejsou k dispozici na úrovni aplikace word.
* Pro frekvenci můžete ladit některé neuronových sítí hlasy podle slova, zatímco jiné vyžadují vyberte celou věty.

> [!TIP]
> Nástroj pro ladění hlasové poskytuje kontextové informace o funkcích a ladění.

## <a name="next-steps"></a>Další postup
* [Vytvořit prostředek řeči v Azure](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [Zahájit vyladění hlasu](https://speech.microsoft.com/app.html#/VoiceTuning)
* [Jazyk využívající značky syntézu řeči (SSML)](speech-synthesis-markup.md)
