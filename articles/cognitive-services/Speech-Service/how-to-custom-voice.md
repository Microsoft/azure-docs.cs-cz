---
title: Vylepšení syntézy pomocí vlastní hlasové služby pro hlasové řeči
titleSuffix: Azure Cognitive Services
description: Vlastní hlas je sada online nástrojů, která umožňuje vytvořit rozpoznatelný hlas pro vaši značku. Vše, co vše trvá, je několik zvukových souborů a přidružených přepisů. Pomocí odkazů níže můžete začít vytvářet vlastní prostředí pro text v textu.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 99af0ee46e2827ac8a5749d58bb19cf85f96aa46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577457"
---
# <a name="get-started-with-custom-voice"></a>Začínáme se službou Custom Voice

[Vlastní hlas](https://aka.ms/customvoice) je sada online nástrojů, která umožňuje vytvořit rozpoznatelný hlas pro vaši značku. Vše, co vše trvá, je několik zvukových souborů a přidružených přepisů. Pomocí odkazů níže můžete začít vytvářet vlastní prostředí pro převod textu na řeč.

## <a name="whats-in-custom-voice"></a>Co je ve vlastním hlasu?

Než začnete s vlastním hlasem, budete potřebovat účet Azure a předplatné služby Speech. Po vytvoření účtu můžete připravit vaše data, naučit a testovat vaše modely, hodnotit kvalitu hlasu a nakonec nasazovat svůj vlastní hlasový model.

Diagram níže popisuje kroky pro vytvoření vlastního hlasového modelu pomocí [vlastního hlasového portálu](https://aka.ms/customvoice). Další informace získáte pomocí odkazů.

![Vlastní diagram architektury hlasu](media/custom-voice/custom-voice-diagram.png)

1. [Přihlaste se k odběru a vytvořte projekt](#set-up-your-azure-account) – vytvořte účet Azure a vytvořte předplatné služby Speech. Toto jednotné předplatné vám umožní přístup k převodu řeči na text, převod textu na řeč, rozpoznávání řeči a vlastní hlasový portál. Pak pomocí svého předplatného služby Speech vytvořte svůj první vlastní hlasový projekt.

2. [Nahrajte data](how-to-custom-voice-create-voice.md#upload-your-datasets) – nahrajte data (zvuk a text) pomocí vlastního hlasového portálu nebo vlastního hlasového rozhraní API. Z portálu můžete prozkoumat a vyhodnotit jejich skóre a poměry hluku. Další informace najdete v tématu [Příprava dat pro vlastní hlas](how-to-custom-voice-prepare-data.md).

3. [Využijte svoje data](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) a vytvořte si vlastní hlasový model pro převod textu na řeč. Model můžete vyškolit v různých jazycích. Po školení, testování modelu a pokud jste s výsledkem spokojeni, můžete model nasadit.

4. [Nasazení modelu](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) – vytvořte si vlastní koncový bod pro hlasový model pro převod textu na řeč a použijte ho pro syntézu řeči ve svých produktech, nástrojích a aplikacích.

## <a name="custom-neural-voices"></a>Vlastní hlasy neuronové

Vlastní hlas v současné době podporuje úrovně Standard a neuronové. Vlastní neuronové hlas umožňuje uživatelům vytvářet vyšší kvality hlasových modelů a přitom přitom vyžadovat méně dat a poskytuje míry, které vám pomůžou nasadit AI zodpovědnou. Doporučujeme používat vlastní neuronové hlas pro vývoj realističtějších hlasů pro více přirozených konverzací a umožnit vašim zákazníkům a koncovým uživatelům těžit z nejnovější technologie pro převod textu na řeč, a to podle zodpovědného způsobu. [Přečtěte si další informace o vlastním neuronovéém hlasu](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext). 

> [!NOTE]
> V rámci snahy Microsoftu o návrh zodpovědného AI jsme omezili použití vlastního hlasu neuronové. Přístup k technologii můžete získat až po kontrole vašich aplikací a s potvrzením, že je budete používat v souladu s našimi zodpovědnými principy AI. Přečtěte si další informace o našich [zásadách pro omezení přístupu](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) a [použijte je tady](https://aka.ms/customneural). [Jazyky](language-support.md#customization) a [oblasti](regions.md#custom-voices) podporované standardem a neuronové verzí vlastního hlasu se liší. Než začnete, Projděte si podrobnosti.  

## <a name="set-up-your-azure-account"></a>Nastavení účtu Azure

Aby bylo možné použít portál Custom Speech k vytvoření vlastního modelu, je nutné předplatné služby Speech Service. Podle těchto pokynů vytvořte v Azure předplatné služby Speech. Pokud nemáte účet Azure, můžete se zaregistrovat k novému.  

Po vytvoření účtu Azure a předplatného služby pro rozpoznávání řeči se budete muset přihlásit k vlastnímu hlasovému portálu a připojit své předplatné.

1. Získejte klíč předplatného služby Speech z Azure Portal.
2. Přihlaste se k [vlastnímu hlasovému portálu](https://aka.ms/custom-voice).
3. Vyberte své předplatné a vytvořte projekt řeči.
4. Pokud byste chtěli přepnout na jiné předplatné řeči, použijte ikonu ozubeného kola umístěnou v horním navigačním panelu.

> [!NOTE]
> Než budete moct službu používat, musíte mít v Azure vytvořený klíč služby F0 nebo S0 Speech. Vlastní neuronové hlas podporuje pouze vrstvu S0. 

## <a name="how-to-create-a-project"></a>Jak vytvořit projekt

Obsah, který je podobný datům, modelům, testům a koncovým bodům, je uspořádán do **projektů** ve vlastním hlasovém portálu. Každý projekt je specifický pro zemi nebo jazyk a pohlaví hlasu, který chcete vytvořit. Můžete například vytvořit projekt pro žena Voice pro chat roboty vašeho centra volání, který používá angličtinu v USA (EN-US).

Pokud chcete vytvořit svůj první projekt, vyberte kartu **Převod textu na řeč/vlastní hlas** a pak klikněte na **Nový projekt**. Postupujte podle pokynů průvodce a vytvořte projekt. Po vytvoření projektu se zobrazí čtyři karty: **data**, **školení**, **testování** a **nasazení**. Pomocí odkazů uvedených v [následujících krocích](#next-steps) se dozvíte, jak používat jednotlivé karty.

> [!IMPORTANT]
> [Vlastní hlasový portál](https://aka.ms/custom-voice) se nedávno aktualizoval. Pokud jste vytvořili předchozí data, modely, testy a publikované koncové body na portálu CRIS.ai nebo pomocí rozhraní API, je potřeba vytvořit nový projekt na novém portálu pro připojení k těmto starým entitám.

## <a name="how-to-migrate-to-custom-neural-voice"></a>Postup migrace na vlastní neuronové hlas

Úroveň školení Standard/non-neuronové (adaptivní, statistické parametry, concacenative) vlastního hlasu je zastaralá. Oznámení byl odeslán všem existujícím předplatným řeči před 2/28/2021. Během období vyřazení (3/1/2021-2/29/2024) mohou stávající uživatelé úrovně Standard nadále používat své neuronové vytvořené modely. Všichni noví uživatelé/nové prostředky pro rozpoznávání řeči by se měly přesunout do neuronové úrovně nebo vlastního hlasu neuronové. Po 2/29/2024 se už nepodporují všechny vlastní hlasy Standard/neuronové. 

Pokud používáte vlastní hlas, který není neuronové nebo standardní, migrujte na vlastní neuronové hlas hned podle následujících kroků. Přechod na vlastní neuronové hlas vám pomůže vyvíjet realističtější hlasy ještě více přirozených konverzací a umožnit zákazníkům a koncovým uživatelům těžit z nejnovější technologie pro převod textu na řeč, a to podle zodpovědného způsobu. 

1. Přečtěte si další informace o našich [zásadách pro omezení přístupu](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) a [použijte je tady](https://aka.ms/customneural). Všimněte si, že přístup k vlastní hlasové službě neuronové se může vztahovat jenom na naše kritérium týkající se nároků Microsoftu. Zákazníci mohou získat přístup k technologii až po kontrole jejich aplikace a potvrdili, že ji budou používat v souladu se svými [odpovědnými principy AI](https://microsoft.com/ai/responsible-ai) a [kodexem chování](/legal/cognitive-services/speech-service/tts-code-of-conduct?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext). 
2. Jakmile je vaše aplikace schválená, budete mít přístup k funkci školení "neuronové". Ujistěte se, že se přihlašujete k [vlastnímu hlasovému portálu](https://speech.microsoft.com/customvoice) pomocí stejného předplatného Azure, které zadáte ve své aplikaci. 
    > [!IMPORTANT]
    > Abychom chránili hlasové talentůy a zabránili školením v hlasových modelech s neoprávněným záznamem nebo bez potvrzení ze služby Voice talentů, potřebujeme, aby zákazník nahrál zaznamenané prohlášení o hlasovém talentůu, které poskytuje svůj souhlas. Při přípravě skriptu nahrávání nezapomeňte zahrnout tuto větu. "I [stav vašeho jména a příjmení] si uvědomte, že nahrávky mého hlasu budou použity uživatelem [State název společnosti] k vytvoření a použití syntetické verze mého hlasu."
    > Tato věta se musí nahrát na kartu **Voice talentů** jako soubor ústního souhlasu. Použije se k ověření, jestli nahrávky v datových sadách pro školení provádí stejná osoba, která tento souhlas provádí.
3. Po vytvoření vlastního neuronové hlasového modelu nasaďte hlasový model do nového koncového bodu. Pokud chcete vytvořit nový vlastní hlasový koncový bod pomocí neuronové hlasového modelu, přečtěte si **text na řeč > vlastního nasazení hlasových >**. Vyberte **nasadit model** a zadejte **název** a **Popis** vlastního koncového bodu. Pak vyberte vlastní hlasový model neuronové, který chcete přidružit k tomuto koncovému bodu, a potvrďte nasazení.  
4. Pokud jste vytvořili nový koncový bod s novým modelem, aktualizujte si kód v aplikacích. 

## <a name="next-steps"></a>Další kroky

- [Příprava vlastních hlasových dat](how-to-custom-voice-prepare-data.md)
- [Vytvoření vlastního hlasu](how-to-custom-voice-create-voice.md)
- [Příručka: záznam ukázek hlasu](record-custom-voice-samples.md)
