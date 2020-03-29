---
title: Začínáme – Překladač textového rozhraní API
titleSuffix: Azure Cognitive Services
description: V tomto článku se zobrazí, jak se zaregistrovat do textového rozhraní API překladače azure cognitive services a získat klíč předplatného.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: cfae5318213e8af164c8d0cc9ed378705a5644b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73835691"
---
# <a name="how-to-sign-up-for-the-translator-text-api"></a>Jak se zaregistrovat do překladače text API

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

- Nemáte účet? Můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free/) a experimentovat bez poplatků.
- Již máte účet? [Přihlásit](https://ms.portal.azure.com/)

## <a name="create-a-subscription-to-the-translator-text-api"></a>Vytvoření předplatného rozhraní Translator Text API

Po přihlášení k portálu můžete vytvořit odběr rozhraní Translator Text API následujícím způsobem:

1. Vyberte **+ Vytvořit prostředek**.
1. Do **vyhledávacího** pole Hledat na Marketplace zadejte **Text překladače** a vyberte ho z výsledků.
1. Vyberte **Vytvořit,** chcete-li definovat podrobnosti pro odběr.
1. Ze seznamu **Cenová úroveň** vyberte cenovou úroveň, která nejlépe vyhovuje vašim potřebám.
    1. Každé předplatné má bezplatnou úroveň. Bezplatná úroveň má stejné funkce a funkce jako placené plány a nevyprší.
    1. Pro svůj účet můžete mít pouze jedno bezplatné předplatné.
1. Chcete-li dokončit vytváření předplatného, vyberte **vytvořit.**

## <a name="authentication-key"></a>Ověřovací klíč

Když se zaregistrujete do programu Translator Text, získáte přizpůsobený přístupový klíč jedinečný pro vaše předplatné. Tento klíč je vyžadován při každém volání překladače textového rozhraní API.

1. Načtěte ověřovací klíč tak, že nejprve vyberete příslušné předplatné.
1. V podrobnostech o předplatném vyberte **Klíče** v části **Správa prostředků.**
1. Zkopírujte některý z klíčů uvedených pro vaše předplatné.

## <a name="learn-test-and-get-support"></a>Učit se, testovat a získat podporu

- [Příklady kódu na GitHubu](https://github.com/MicrosoftTranslator)
- [Fórum podpory překladatele společnosti Microsoft](https://www.aka.ms/TranslatorForum)

Microsoft Translator obvykle umožní vaše první pár požadavků projít před tím, než ověří stav účtu předplatného. Pokud prvních několik požadavků rozhraní API Microsoft Translator API úspěšné pak volání nezdaří, odpověď na chybu bude znamenat problém. Protokolujte odpověď rozhraní API, abyste viděli důvod.

## <a name="pricing-options"></a>Cenové možnosti

- [Translator Text API](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)

## <a name="customization"></a>Přizpůsobení

Pomocí vlastního překladače můžete přizpůsobit překlady a vytvořit překladový systém vyladěný podle vlastní terminologie a stylu, počínaje obecnými systémy neurálního strojového překladu Microsoft Translator. [Další informace](customization.md)

## <a name="additional-resources"></a>Další zdroje

- [Začínáme s Azure (3minutové video)](https://azure.microsoft.com/get-started/?b=16.24)
- [Jak platit fakturou](https://azure.microsoft.com/pricing/invoicing/)
