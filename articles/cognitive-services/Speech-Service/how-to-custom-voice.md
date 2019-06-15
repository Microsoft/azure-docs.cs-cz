---
title: Co je vlastní hlasové? – Hlasové služby
titlesuffix: Azure Cognitive Services
description: Vlastní hlasové je sada online nástrojů, které vám umožňují vytvářet rozpoznat, jeden z druhu hlas pro hodnotu značky. Všechno potřebné, abyste mohli začít několik zvukové soubory a související přepisů. Pomocí odkazů níže a začněte vytvářet vlastní prostředí speech to text.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 016dcf32f2f846e43362f17bc9f4627113908352
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075149"
---
# <a name="get-started-with-custom-voice"></a>Začínáme se službou Custom Voice

Vlastní hlasové je sada online nástrojů, které vám umožňují vytvářet rozpoznat, jeden z druhu hlas pro hodnotu značky. Všechno potřebné, abyste mohli začít několik zvukové soubory a související přepisů. Pomocí odkazů níže a začněte vytvářet vlastní převod textu na řeč prostředí.

## <a name="whats-in-custom-voice"></a>Co je vlastní hlasové?

Než začnete s vlastní hlasové, budete potřebovat účet Azure a hlasové služby předplatného. Po vytvoření účtu můžete přípravě dat, trénování a testování vašich modelů, hodnocení kvality zvuku a nakonec vlastní hlasové model nasadit.

Následující diagram ukazuje kroky k vytvoření vlastní hlasové modelů pomocí vlastního hlasového portálu. Pomocí odkazů na další informace.

![Diagram architektury vlastní hlasové](media/custom-voice/custom-voice-diagram.png)

1.  [Přihlášení odběru a vytvořte projekt](#set-up-your-azure-account) – vytvoření účtu Azure a vytvoření odběru hlasové služby. Toto jednotné předplatné poskytuje přístup k převodu řeči na text, převod textu na řeč, překlad řeči a vlastní hlasové portálu. Pomocí svého předplatného hlasové služby, pak vytvořte svůj první projekt vlastní hlasové.

2.  [Nahrání dat](how-to-custom-voice-create-voice.md#upload-your-datasets) – nahrání dat (zvuk a textem) pomocí portálu pro vlastní hlasové nebo hlasových vlastní rozhraní API. Z portálu můžete prozkoumat a vyhodnotit výslovnost skóre a poměry signálu šumu. Další informace najdete v tématu [jak připravit data pro vlastní hlasové](how-to-custom-voice-prepare-data.md).

3.  [Vyzkoušejte svůj model](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – vaše data použít k vytvoření vlastní řeč modelu. Můžete tréninku modelů v různých jazycích. Po školení, testování modelu, a pokud jste s výsledkem spokojeni, můžete nasadit model.

4.  [Nasazení modelu](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) – vytvoření vlastního koncového bodu pro váš model řeč a jeho použití pro syntézu řeči v produktů, nástrojů a aplikací.

## <a name="set-up-your-azure-account"></a>Nastavení účtu Azure

Vyžaduje se předplatné hlasové služby, než portál Custom Speech můžete použít k vytvoření vlastního modelu. Postupujte podle těchto pokynů a vytvořte předplatné hlasové služby v Azure. Pokud nemáte účet Azure, můžete zaregistrovat nové.  

Po vytvoření účtu Azure a hlasové služby předplatného, budete muset přihlásit k portálu vlastní hlasové a připojit vaše předplatné.

1. Získejte klíč předplatného hlasové služby z webu Azure portal.
2. Přihlaste se k [vlastní hlasové portál](https://aka.ms/custom-voice).
3. Vyberte své předplatné a vytvořte projekt řeči.
4. Pokud chcete přepnout do jiného předplatného řeči, použijte ikonu ozubeného kola v horním navigačním panelu.

> [!NOTE]
> Vlastní hlasové služby nepodporuje 30denní bezplatné zkušební verze klíče. Musíte mít F0 nebo S0 klíče vytvořené v Azure, abyste mohli používat službu.

## <a name="how-to-create-a-project"></a>Vytvoření projektu

Obsah, jako jsou data, modely, testy a koncové body jsou uspořádány do **projekty** vlastní hlasové portálu. Každý projekt je specifické pro zemi/jazyk a pohlaví hlasové, kterou chcete vytvořit. Můžete například vytvořit projekt u ženského hlasu pro volání centra chatovací roboty, používá angličtinu Spojené státy (en US).

Chcete-li vytvořit svůj první projekt, vyberte **Text-k-Speech/vlastní hlasové** kartu a potom klikněte na **nový projekt**. Postupujte podle pokynů v Průvodci vytvořením projektu. Po vytvoření projektu, uvidíte čtyři karty: **Data**, **školení**, **testování**, a **nasazení**. Pomocí odkazů uvedených v [další kroky](#next-steps) na další informace o použití každé kartě.

## <a name="next-steps"></a>Další postup

- [Příprava dat vlastní hlasové](how-to-custom-voice.md)
- [Vytvořit vlastní hlasový vstup](how-to-custom-voice-create-voice.md)
- [Průvodce: Záznam vašich ukázek hlasu](record-custom-voice-samples.md)
