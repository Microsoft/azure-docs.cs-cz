---
title: Zlepšete syntézu pomocí služby Custom Voice - Speech Service
titleSuffix: Azure Cognitive Services
description: Custom Voice je sada online nástrojů, které vám umožní vytvořit rozpoznatelný, jedinečný hlas pro vaši značku. Stačí začít, je několik zvukových souborů a související přepisy. Pomocí následujících odkazů začněte vytvářet vlastní prostředí převodu řeči na text.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: dapine
ms.openlocfilehash: 786cf687e569af172eabfaa9068ae61ffd6b2988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220476"
---
# <a name="get-started-with-custom-voice"></a>Začínáme se službou Custom Voice

[Custom Voice](https://aka.ms/customvoice) je sada online nástrojů, které vám umožní vytvořit rozpoznatelný, jedinečný hlas pro vaši značku. Stačí začít, je několik zvukových souborů a související přepisy. Chcete-li začít vytvářet vlastní prostředí pro převod textu na řeč, postupujte podle následujících odkazů.

## <a name="whats-in-custom-voice"></a>Co je v Custom Voice?

Než začnete s vlastním hlasem, budete potřebovat účet Azure a předplatné služby Speech. Po vytvoření účtu můžete připravit data, trénovat a testovat modely, vyhodnocovat kvalitu hlasu a nakonec nasadit vlastní hlasový model.

Níže uvedený diagram zvýrazňuje kroky k vytvoření vlastního hlasového modelu pomocí [portálu Vlastní hlas](https://aka.ms/customvoice). Další informace najdete v odkazech.

![Diagram vlastní hlasové architektury](media/custom-voice/custom-voice-diagram.png)

1. [Přihlásit se k odběru a vytvořit projekt](#set-up-your-azure-account) – vytvořte účet Azure a vytvořte předplatné služby Rozpoznávání řeči. Toto sjednocené předplatné umožňuje přístup k převodu řeči na text, převodu textu na řeč, překladu řeči a portálu Vlastní hlas. Potom pomocí předplatného služby Rozpoznávání řeči vytvořte svůj první vlastní hlasový projekt.

2. [Nahrávání dat](how-to-custom-voice-create-voice.md#upload-your-datasets) – Nahrávání dat (zvuku a textu) pomocí vlastního hlasového portálu nebo vlastního hlasového rozhraní API. Z portálu můžete zkoumat a vyhodnocovat skóre výslovnosti a poměry signálu k šumu. Další informace naleznete v tématu [Jak připravit data pro vlastní hlas](how-to-custom-voice-prepare-data.md).

3. [Trénování modelu](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – Pomocí dat můžete vytvořit vlastní model hlasu pro převod textu na řeč. Model můžete trénovat v různých jazycích. Po trénování otestujte model a pokud jste s výsledkem spokojeni, můžete model nasadit.

4. [Nasazení modelu](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) – vytvořte vlastní koncový bod pro váš model hlasu převodu textu na řeč a použijte jej pro syntézu řeči ve vašich produktech, nástrojích a aplikacích.

## <a name="custom-neural-voices"></a>Vlastní neurální hlasy

Funkce přizpůsobení neurálního hlasu je aktuálně ve verzi Public Preview, která je omezena na vybrané zákazníky. Chcete-li začít, vyplňte tento [formulář žádosti.](https://go.microsoft.com/fwlink/?linkid=2108737)

> [!NOTE]
> V rámci závazku společnosti Microsoft navrhnout odpovědnou umělou ai, naším záměrem je chránit práva jednotlivců a společnosti a podporovat transparentní interakce mezi člověkem a počítačem. Z tohoto důvodu vlastní neurální hlas není obecně k dispozici všem zákazníkům. Přístup k technologii můžete získat až poté, co jsou vaše žádosti přezkoumány a zavázali jste se k jejich používání v souladu s našimi etickými zásadami. Přečtěte si další informace o našem [procesu vzponění aplikací](https://aka.ms/custom-neural-gating-overview).

## <a name="set-up-your-azure-account"></a>Nastavení účtu Azure

Před vytvořením vlastního modelu je vyžadováno předplatné služby Rozpoznávání řeči. Podle těchto pokynů vytvořte předplatné služby Speech v Azure. Pokud nemáte účet Azure, můžete si zaregistrovat nový.  

Po vytvoření účtu Azure a předplatného služby Speech se budete muset přihlásit k portálu Vlastní hlas a připojit své předplatné.

1. Získejte klíč předplatného služby Speech z portálu Azure.
2. Přihlaste se k [portálu Vlastní hlas](https://aka.ms/custom-voice).
3. Vyberte předplatné a vytvořte projekt řeči.
4. Pokud chcete přepnout na jiné předplatné řeči, použijte ikonu kolečka umístěnou v horní navigaci.

> [!NOTE]
> Služba Vlastní hlas nepodporuje 30denní zkušební klíč zdarma. Před použitím služby musíte mít v Azure vytvořený klíč F0 nebo S0.

## <a name="how-to-create-a-project"></a>Jak vytvořit projekt

Obsah, jako jsou data, modely, testy a koncové body jsou **uspořádány** do projektů v portálu vlastní hlas. Každý projekt je specifický pro zemi/jazyk a pohlaví hlasu, který chcete vytvořit. Můžete například vytvořit projekt pro ženský hlas pro chatovací roboty vašeho call centra, kteří používají angličtinu ve Spojených státech (en-US).

Chcete-li vytvořit první projekt, vyberte kartu **Převod textu na řeč nebo Vlastní hlas** a klepněte na tlačítko Nový **projekt**. Podle pokynů průvodce vytvořte projekt. Po vytvoření projektu se zobrazí čtyři karty: **Data**, **Školení**, **Testování**a **Nasazení**. Pomocí odkazů uvedených v [dalším postupech](#next-steps) se dozvíte, jak používat jednotlivé karty.

> [!IMPORTANT]
> Portál [Custom Voice](https://aka.ms/custom-voice) byl nedávno aktualizován! Pokud jste vytvořili předchozí data, modely, testy a publikované koncové body na portálu CRIS.ai nebo pomocí rozhraní API, je třeba vytvořit nový projekt v novém portálu pro připojení k těmto starým entitám.

## <a name="next-steps"></a>Další kroky

- [Příprava vlastních hlasových dat](how-to-custom-voice-prepare-data.md)
- [Vytvoření vlastního hlasu](how-to-custom-voice-create-voice.md)
- [Průvodce: Záznam hlasových ukázek](record-custom-voice-samples.md)
