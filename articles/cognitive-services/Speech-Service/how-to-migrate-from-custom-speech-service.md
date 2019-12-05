---
title: Migrace z Custom Speech Service do služby Speech
titleSuffix: Azure Cognitive Services
description: Custom Speech Service je teď součástí služby pro rozpoznávání řeči. Pokud chcete využít nejnovější aktualizace kvality a funkcí, přepněte na službu Speech.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 9c93286329316d081f8fd99ebd360195931b7b09
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805922"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrace z Custom Speech Service do služby pro rozpoznávání řeči

Pomocí tohoto článku můžete migrovat aplikace z Custom Speech Service do služby pro rozpoznávání řeči.

Custom Speech Service je teď součástí služby pro rozpoznávání řeči. Pokud chcete využít nejnovější aktualizace kvality a funkcí, přepněte na službu Speech.

## <a name="migration-for-new-customers"></a>Migrace pro nové zákazníky

Cenový model je jednodušší pomocí cenového modelu založeného na hodinách pro službu Speech.  

1. Vytvořte prostředek Azure v jednotlivých oblastech, kde se vaše aplikace je k dispozici. Název prostředku Azure je **řeči**. U následujících služeb ve stejné oblasti, místo vytvoření samostatné prostředky, můžete použít jeden prostředek Azure:

    * Převod řeči na text
    * Vlastní převod řeči na text
    * Převod textu na řeč
    * Překlad řeči

2. Stáhněte si [řeči SDK](speech-sdk.md).

3. Postupujte podle příručky rychlý úvod a ukázky SDK použít správné rozhraní API. Pokud používáte rozhraní REST API, musíte taky použít správné koncové body a klíče prostředku.

4. Aktualizujte klientskou aplikaci tak, aby používala službu rozpoznávání řeči a rozhraní API.

## <a name="migration-for-existing-customers"></a>Migrace pro stávající zákazníky

Migrujte stávající klíče prostředků na službu rozpoznávání řeči na portálu služby pro rozpoznávání řeči. Použijte k tomu následující postup:

> [!NOTE]
> Klíče prostředku je možné migrovat pouze v rámci stejné oblasti.

1. Přihlaste se k [cris.ai](https://cris.ai/Home/CustomSpeech) portál a vyberte předplatné, v nabídce vpravo nahoře.

2. Vyberte **vybrané předplatné migrovat**.

3. V textovém poli zadejte klíč předplatného a vyberte **migrace**.

## <a name="next-steps"></a>Další kroky

* [Vyzkoušejte si službu Speech Service zdarma](get-started.md).
* Přečtěte si [převod řeči na text](./speech-to-text.md) koncepty.

## <a name="see-also"></a>Další informace najdete v tématech

* [Co je služba pro rozpoznávání řeči](overview.md)
* [Dokumentace ke službě Speech a sadě Speech SDK](speech-sdk.md#get-the-sdk)
