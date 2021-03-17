---
title: Co je služba Speech?
titleSuffix: Azure Cognitive Services
description: Služba rozpoznávání řeči je sjednocení převodu řeči na text, převod textu na řeč a rozpoznávání řeči do jednoho předplatného Azure. Pomocí sady Speech SDK, zařízení Speech SDK nebo rozhraní REST API můžete do svých aplikací, nástrojů a zařízení přidat řeč.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 11/23/2020
ms.author: trbye
ms.openlocfilehash: d3d9f41876cf1310fe25a275624f609031c05b00
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601878"
---
# <a name="what-is-the-speech-service"></a>Co je služba Speech?

Služba rozpoznávání řeči je sjednocení převodu řeči na text, převod textu na řeč a rozpoznávání řeči do jednoho předplatného Azure. Rozpoznávání řeči vám umožní používat aplikace, nástroje a zařízení pomocí rozpoznávání [řeči](spx-overview.md), [sady Speech SDK](./speech-sdk.md), [Speech Devices SDK](./speech-devices-sdk-quickstart.md?pivots=platform-android), [Speech Studio](https://speech.microsoft.com/)nebo [REST API](#reference-docs).

> [!IMPORTANT]
> Služba rozpoznávání řeči nahradila rozhraní API pro zpracování řeči Bingu a Translator Speech. Pokyny k migraci najdete v části věnované _migraci_ .

Následující funkce jsou součástí služby Speech. Pomocí odkazů v této tabulce se dozvíte víc o běžných případech použití pro jednotlivé funkce nebo o Projděte si referenční informace k rozhraní API.

| Služba | Funkce | Popis | Sada SDK | REST |
|---------|---------|-------------|-----|------|
| [Převod řeči na text](speech-to-text.md) | Převod řeči na text v reálném čase | Převod řeči na text transcribes nebo přeloží zvukové streamy nebo místní soubory na text v reálném čase, který mohou aplikace, nástroje nebo zařízení spotřebovat nebo zobrazit. Použití převodu řeči na text s [Language Understanding (Luis)](../luis/index.yml) k odvození záměrů uživatele z přepisu řeči a fungování hlasových příkazů. | [Ano](./speech-sdk.md) | [Ano](#reference-docs) |
| | [Dávkové zpracování řeči na text](batch-transcription.md) | Batch Speech-to-text umožňuje asynchronní převod řeči na text s velkými objemy zvukového zvuku, který je uložený v Azure Blob Storage. Kromě převodu zvukového zvuku na text, Batch Speech-to-text také umožňuje diarization a mínění-Analysis. | Ne | [Ano](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| | [Konverzace s více zařízeními](multi-device-conversation.md) | Připojení více zařízení nebo klientů v rámci konverzace pro odesílání hlasových a textových zpráv s jednoduchou podporou pro přepis a překlady| Ano | Ne |
| | [Přepis konverzace](./conversation-transcription.md) | Umožňuje rozpoznávání řeči v reálném čase, identifikaci mluvčího a diarization. Je ideální pro zdlouhavého přepisování schůzky na osobu s možností odlišit reproduktory. | Ano | Ne |
| | [Vytváření modelů Custom Speech](#customize-your-speech-experience) | Pokud používáte převod řeči na text pro rozpoznávání a přepis v jedinečném prostředí, můžete vytvořit a vyškolit vlastní modely akustického, jazyka a výslovnosti pro řešení hluku nebo slovníku specifického pro konkrétní obor. | Ne | [Ano](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) |
| [Převod textu na řeč](text-to-speech.md) | Převod textu na řeč | Převod textu na řeč převede vstupní text na syntetizované rozpoznávání řeči pomocí [jazyka SSML (Speech syntézy)](speech-synthesis-markup.md). Vyberte si ze standardních hlasů a hlasy neuronové (viz [Podpora jazyků](language-support.md)). | [Ano](./speech-sdk.md) | [Ano](#reference-docs) |
| | [Vytváření vlastních hlasů](#customize-your-speech-experience) | Vytvářejte vlastní hlasová písma, která jsou jedinečná pro vaši značku nebo produkt. | Ne | [Ano](#reference-docs) |
| [Překlad řeči](speech-translation.md) | Překlad řeči | Překlad řeči umožňuje překlady řeči v reálném čase pro aplikace, nástroje a zařízení v reálném čase. Použijte tuto službu pro překlad řeči a převod řeči na text. | [Ano](./speech-sdk.md) | Ne |
| [Hlasoví asistenti](voice-assistants.md) | Hlasoví asistenti | Hlasové asistenti, kteří používají službu pro rozpoznávání řeči, umožňují vývojářům vytvářet pro své aplikace a prostředí přirozené rozhraní pro konverzaci podobné člověku. Služba hlasového asistenta poskytuje rychlou a spolehlivou interakci mezi zařízením a implementací pomocníka, která používá kanál s přímým přístupem na platformě bot nebo integrovanou službu Custom Commands pro dokončení úkolu. | [Ano](voice-assistants.md) | Ne |
| [Rozpoznávání mluvčího](speaker-recognition-overview.md) | Identifikace & ověření mluvčího | Služba rozpoznávání mluvčího poskytuje algoritmy pro ověřování a identifikaci mluvčích podle jejich jedinečných vlastností hlasu. Rozpoznávání mluvčího slouží k zodpovězení otázky "kdo se mluví?". | Ano | [Ano](/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service-for-free"></a>Vyzkoušejte si službu Speech zdarma

Pro následující kroky potřebujete účet Microsoft i účet Azure. Pokud nemáte účet Microsoft, můžete si na [portálu účet Microsoft](https://account.microsoft.com/account)zdarma zaregistrovat bezplatný poplatek. Vyberte **Přihlásit se účtem Microsoft** a po zobrazení výzvy k přihlášení vyberte **vytvořit účet Microsoft**. Při vytváření a ověřování nových účet Microsoft postupujte podle pokynů.

Jakmile budete mít účet Microsoft, navštivte [stránku registrace do Azure](https://azure.microsoft.com/free/ai/), vyberte **začít zdarma** a vytvořte nový účet Azure pomocí účet Microsoft. Tady je video o [tom, jak se zaregistrovat k bezplatnému účtu Azure](https://www.youtube.com/watch?v=GWT2R1C_uUU).

> [!NOTE]
> Když si zaregistruje bezplatný účet Azure, najdete ho v kreditu služby $200, který můžete použít k placenému předplatnému služby Speech, a to po dobu až 30 dnů. Vaše služby Azure jsou zakázané, když váš kredit skončí nebo končí na konci 30 dnů. Pokud chcete dál využívat služby Azure, musíte upgradovat svůj účet. Další informace najdete v tématu [Postup upgradu bezplatného účtu Azure](../../cost-management-billing/manage/upgrade-azure-subscription.md). 
>
> Služba Speech má dvě úrovně služeb: Free (F0) a předplatné (S0), které mají různá omezení a výhody. Pokud používáte bezplatnou úroveň služby řeči s nízkým objemem, můžete si toto bezplatné předplatné ponechat i po vypršení bezplatné zkušební verze nebo kreditu služby. Další informace najdete v tématu [Cognitive Services Price-Speech Service](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="create-the-azure-resource"></a>Vytvoření prostředku Azure

Postup přidání prostředku služby řeči (bezplatná nebo placená úroveň) do účtu Azure:

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com/) pomocí svého účtu Microsoft.

1. V levém horním rohu portálu vyberte **vytvořit prostředek** . Pokud se vám nepovede **vytvořit prostředek**, můžete ho vždycky najít tak, že vyberete sbalenou nabídku v levém horním rohu obrazovky.

1. V **novém** okně zadejte do vyhledávacího pole text "Speech" a stiskněte klávesu ENTER.

1. Ve výsledcích hledání vyberte **řeč**.

   ![výsledky hledání řeči](media/index/speech-search.png)

1. Vyberte **vytvořit** a potom:

   - Zadejte jedinečný název nového prostředku. Název vám pomůže rozlišovat mezi několika předplatnými vázanými na stejnou službu.
   - Vyberte předplatné Azure, ke kterému je přiřazen nový prostředek, abyste zjistili, jak se účtují poplatky. Tady je úvodní informace o [tom, jak vytvořit předplatné Azure](../../cost-management-billing/manage/create-subscription.md#create-a-subscription-in-the-azure-portal) v Azure Portal.
   - Vyberte [oblast](regions.md) , ve které se prostředek použije. Azure je globální cloudová platforma, která je obecně dostupná v mnoha oblastech po celém světě. Pokud chcete dosáhnout nejlepšího výkonu, vyberte oblast, která je pro vás nejblíže nebo kde se vaše aplikace spouští. Služba Speech Service pro nákup se liší od různých oblastí. Ujistěte se, že jste vytvořili prostředek v podporované oblasti. Podívejte se [na podporu oblastí pro hlasové služby](./regions.md#speech-to-text-text-to-speech-and-translation).
   - Vyberte cenovou úroveň Free (F0) nebo Place (S0). Pokud chcete získat kompletní informace o cenách a kvótách využití pro každou úroveň, vyberte **Zobrazit úplné podrobnosti o cenách** nebo si prohlédněte [ceny služby Speech Services](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/). Omezení prostředků najdete v tématu [omezení pro Azure Cognitive Services](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cognitive-services-limits).
   - Vytvořte novou skupinu prostředků pro toto předplatné řeči nebo přiřaďte předplatné k existující skupině prostředků. Skupiny prostředků vám pomůžou udržovat vaše různá předplatná Azure uspořádaná.
   - Vyberte **Vytvořit**. Tím přejdete na přehled nasazení a zobrazíte zprávy o průběhu nasazení.  
<!--
> [!NOTE]
> You can create an unlimited number of standard-tier subscriptions in one or multiple regions. However, you can create only one free-tier subscription. Model deployments on the free tier that remain unused for 7 days will be decommissioned automatically.
-->
Nasazení nového prostředku řeči chvíli trvá. 

### <a name="find-keys-and-region"></a>Hledání klíčů a oblastí

Pokud chcete najít klíče a oblast dokončeného nasazení, postupujte podle těchto kroků:

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com/) pomocí svého účtu Microsoft.

2. Vyberte **všechny prostředky** a vyberte název prostředku Cognitive Services.

3. V levém podokně v části **Správa prostředků** vyberte **klíče a koncový bod**.

Každé předplatné má dva klíče; v aplikaci můžete použít libovolný klíč. Chcete-li zkopírovat nebo vložit klíč do editoru kódu nebo jiné umístění, vyberte tlačítko Kopírovat vedle každého klíče, přepněte okna a vložte obsah schránky do požadovaného umístění.

Dále zkopírujte `LOCATION` hodnotu, což je vaše ID vaší oblasti (např. `westus`, `westeurope` ) pro volání sady SDK.

> [!IMPORTANT]
> Tyto klíče předplatného se používají pro přístup ke službě rozpoznávání rozhraní API. Nesdílejte své klíče. Uložte je bezpečně – například pomocí Azure Key Vault. Tyto klíče doporučujeme také pravidelně obnovovat. K volání rozhraní API je potřeba jenom jeden klíč. Při opětovném generování prvního klíče můžete použít druhý klíč pro pokračování přístupu ke službě.

## <a name="complete-a-quickstart"></a>Dokončení rychlého startu

Nabízíme rychlé zprovoznění ve většině oblíbených programovacích jazyků, z nichž každá je navržena pro učení základních vzorů návrhu a spouštění kódu za méně než 10 minut. Seznam rychlých startů pro jednotlivé funkce najdete v následujícím seznamu.

* [Rychlé zprovoznění převodu řeči na text](get-started-speech-to-text.md)
* [Rychlé zprovoznění převodu textu na řeč](get-started-text-to-speech.md)
* [Rychlé zprovoznění překladu řeči](./get-started-speech-translation.md)
* [Rychlé zprovoznění rozpoznávání záměrů](quickstarts/intent-recognition.md)
* [Rychlé zprovoznění rozpoznávání mluvčího](./get-started-speaker-recognition.md)

Až budete mít možnost začít pracovat se službou Speech, vyzkoušejte naše kurzy, které ukazují, jak řešit různé scénáře.

- [Kurz: rozpoznávání záměrů z řeči pomocí sady Speech SDK a LUIS, C #](how-to-recognize-intents-from-speech-csharp.md)
- [Kurz: hlasový vstup pro robota pomocí sady Speech SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Kurz: sestavení aplikace v baňce pro překlad textu, analýza mínění a syntetizace přeloženého textu na řeč, REST](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json%252c%2fen-us%2fazure%2fbread%2ftoc.json&toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json%252c%2fen-us%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json)

## <a name="get-sample-code"></a>Získání vzorového kódu

Vzorový kód je k dispozici na GitHubu pro službu rozpoznávání řeči. Tyto ukázky se týkají běžných scénářů, jako je čtení zvuku ze souboru nebo datového proudu, průběžné a jednorázové rozpoznávání a práce s vlastními modely. Pomocí těchto odkazů můžete zobrazit ukázky sady SDK a REST:

- [Ukázky převodu řeči na text, převod textu na řeč a rozpoznávání řeči (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Ukázky pro dávková přepisy (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Ukázky převodu textu na řeč (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Ukázky hlasového asistenta (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Přizpůsobení prostředí pro rozpoznávání řeči

Služba Speech funguje dobře s integrovanými modely, ale možná budete chtít další přizpůsobení a vyladit prostředí pro svůj produkt nebo prostředí. Možnosti přizpůsobení jsou v rozsahu od ladění akustického modelu až po jedinečná hlasová písma pro vaši značku.

Další produkty nabízejí modely řeči optimalizované pro konkrétní účely, jako je zdravotní péče nebo pojištění, ale jsou k dispozici všem uživatelům současně. Přizpůsobení ve službě Azure Speech se stávají součástí *vaší jedinečné* konkurenční výhody, která není k dispozici pro žádného jiného uživatele nebo zákazníka. Jinými slovy, vaše modely jsou soukromé a vlastní – vyladěny jenom pro váš případ použití.

| Speech Service | Platforma | Popis |
| -------------- | -------- | ----------- |
| Převod řeči na text | [Custom Speech](https://aka.ms/customspeech) | Modely rozpoznávání řeči můžete přizpůsobit vašim potřebám a dostupným datům. Překonání překážek rozpoznávání řeči, například mluveného stylu, slovníku a hluku na pozadí. |
| Převod textu na řeč | [Vlastní hlas](https://aka.ms/customvoice) | Vytvořte si rozpoznatelný jedinečný hlas vašich aplikací pro převod textu na řeč pomocí vašich dostupných dat o hlasu. Hlasové výstupy můžete ještě více ladit úpravou sady hlasových parametrů. |

## <a name="deploy-on-premises-using-docker-containers"></a>Nasazení místně pomocí kontejnerů Docker

[Použijte kontejnery služby Speech](speech-container-howto.md) k nasazení funkcí rozhraní API místně. Tyto kontejnery Docker umožňují přiblížit službu k vašim datům pro účely dodržování předpisů, zabezpečení nebo jiných provozních důvodů. Služba rozpoznávání řeči nabízí tyto kontejnery:

* Standardní převod řeči na text
* Custom Speech na text
* Standardní převod textu na řeč
* Neuronové převodu textu na řeč
* Vlastní převod textu na řeč (Náhled)
* Rozpoznávání jazyka řeči (Preview)

## <a name="reference-docs"></a>Referenční dokumenty

- [Speech SDK](./speech-sdk.md)
- [Speech Devices SDK](speech-devices-sdk.md)
- [REST API: Převod řeči na text](rest-speech-to-text.md)
- [REST API: převod textu na řeč](rest-text-to-speech.md)
- [REST API: přepis a přizpůsobení Batch](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s převodem řeči na text](./get-started-speech-to-text.md) 
>  [Začínáme s převodem textu na řeč](get-started-text-to-speech.md)