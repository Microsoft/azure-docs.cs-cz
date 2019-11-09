---
title: Začínáme – Translator Text API
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak se zaregistrovat do služby Azure Cognitive Services Translator Text API a získat klíč předplatného.
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
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835691"
---
# <a name="how-to-sign-up-for-the-translator-text-api"></a>Jak se zaregistrovat Translator Text API

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

- Nemáte účet? Můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free/) a experimentovat bez poplatků.
- Už máte účet? [Přihlásit se](https://ms.portal.azure.com/)

## <a name="create-a-subscription-to-the-translator-text-api"></a>Vytvoření předplatného pro Translator Text API

Po přihlášení k portálu můžete vytvořit odběr Translator Text API následujícím způsobem:

1. Vyberte **+ Vytvořit prostředek**.
1. Do **vyhledávacího pole Hledat na Marketplace** zadejte **Translator text** a pak ho vyberte z výsledků.
1. Vyberte **vytvořit** a definujte podrobnosti pro předplatné.
1. V seznamu **cenová úroveň** vyberte cenovou úroveň, která nejlépe vyhovuje vašim potřebám.
    1. Každé předplatné má bezplatnou úroveň. Úroveň Free má stejné funkce jako placené plány a jejich platnost nekončí.
    1. Pro váš účet můžete mít jenom jedno bezplatné předplatné.
1. Výběrem **vytvořit** dokončíte vytváření předplatného.

## <a name="authentication-key"></a>Ověřovací klíč

Když se přihlásíte k Translator Text, získáte vlastní přizpůsobený přístupový klíč, který je jedinečný pro vaše předplatné. Tento klíč je požadován při každém volání Translator Text API.

1. Načtěte ověřovací klíč tak, že nejdřív vyberete příslušné předplatné.
1. V části **Správa prostředků** v podrobnostech o vašem předplatném vyberte **klíče** .
1. Zkopírujte jeden z klíčů uvedených pro vaše předplatné.

## <a name="learn-test-and-get-support"></a>Naučte se, testujte a získáte podporu.

- [Příklady kódu na GitHubu](https://github.com/MicrosoftTranslator)
- [Fórum podpory Microsoft translatoru](https://www.aka.ms/TranslatorForum)

Microsoft Translator bude obecně moci předem předat vaše první pár požadavků, než ověří stav účtu předplatného. Pokud je prvních několik požadavků rozhraní API služby Microsoft Translator úspěšné, volání se nezdaří, odpověď na chybu indikuje problém. Zaprotokolujte prosím odpověď rozhraní API, abyste viděli důvod.

## <a name="pricing-options"></a>Cenové možnosti

- [Translator Text API](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)

## <a name="customization"></a>Přizpůsobení

Pomocí vlastního překladatele můžete přizpůsobit svoje překlady a vytvořit systém překladu, který se vyladěný na vlastní terminologii a styl, počínaje obecnými systémy Microsoft Translator neuronové machine translation. [Další informace](customization.md)

## <a name="additional-resources"></a>Další zdroje

- [Začínáme s Azure (3 – minutové video)](https://azure.microsoft.com/get-started/?b=16.24)
- [Jak platit pomocí faktury](https://azure.microsoft.com/pricing/invoicing/)
