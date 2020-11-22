---
title: Přizpůsobení překladu – Překladatel
titleSuffix: Azure Cognitive Services
description: Pomocí centra Microsoft Translator můžete vytvořit vlastní systém překladu počítačů s využitím preferované terminologie a stylu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 9c4410cb2b9550bc111da96204ada15313867fb1
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2020
ms.locfileid: "95238084"
---
# <a name="customize-your-text-translations"></a>Přizpůsobení překladů textu

Vlastní Překladatel je funkcí služby Translator, která umožňuje uživatelům přizpůsobit pokročilý neuronové strojového překladu Microsoft translatoru při překladu textu pomocí překladatele (jenom verze 3).

Tuto funkci můžete také použít k přizpůsobení překladu řeči při použití s [Cognitive Services Speech](../speech-service/index.yml).

## <a name="custom-translator"></a>Custom Translator

Pomocí vlastního překladatele můžete vytvářet neuronové překladatelské systémy, které rozumí terminologii používané ve vašem podniku a v průmyslu. Přizpůsobený systém překladu pak bude integrován do stávajících aplikací, pracovních postupů a webů.

### <a name="how-does-it-work"></a>Jak to funguje?

Pomocí dříve přeložených dokumentů (letáků, webových stránek, dokumentace atd.) sestavíte systém překladu, který odráží vaši terminologii a styl specifický pro doménu, a lepší než standardní systém překladu. Uživatelé můžou ukládat dokumenty TMX, XLIFF, TXT, DOCX a XLSX.  

Systém také přijímá data, která jsou paralelní na úrovni dokumentu, ale ještě není zarovnána na úrovni věty. Pokud mají uživatelé přístup k verzím stejného obsahu v různých jazycích, ale v samostatných dokumentech vlastní Překladatel bude moci automaticky rozlišovat věty mezi dokumenty.  Systém může také použít monolingual data v jednom nebo obou jazycích k doplnění dat paralelního školení pro zlepšení překladu.

Přizpůsobený systém je pak k dispozici prostřednictvím pravidelného volání překladatele pomocí parametru Category.

Vzhledem k odpovídajícímu typu a množství školicích dat není běžné očekávat zisky mezi 5 a 10 nebo ještě více BLEUch bodů v kvalitě překladu pomocí vlastního překladatele.

Další podrobnosti o různých úrovních přizpůsobení na základě dostupných dat najdete v [uživatelské příručce pro vlastní překladatele](./custom-translator/overview.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nastavení přizpůsobeného jazykového systému pomocí vlastního překladatele](./custom-translator/overview.md)
