---
title: Migrace z Custom Speech Service do služby Speech
titleSuffix: Azure Cognitive Services
description: Custom Speech Service je teď součástí Speech Service. Přepnout na Speech Service, abyste využili výhod nejnovější aktualizace kvality a funkcí.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 01b853c59723a8ed79cb32b0ee9c245c9c3ffb3f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562760"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrace ze služby Custom Speech Service na Speech Service

V tomto článku použijte k migraci aplikace z Custom Speech Service ve službě řeči.

Custom Speech Service je teď součástí Speech Service. Pokud chcete využít nejnovější aktualizace kvality a funkcí, přepněte na hlasové služby.

## <a name="migration-for-new-customers"></a>Migrace pro nové zákazníky

Cenový model je jednodušší, pomocí cenového modelu založeného na hodinu pro službu rozpoznávání řeči.  

1. Vytvořte prostředek Azure v jednotlivých oblastech, kde se vaše aplikace je k dispozici. Název prostředku Azure je **řeči**. U následujících služeb ve stejné oblasti, místo vytvoření samostatné prostředky, můžete použít jeden prostředek Azure:

    * Převod řeči na text
    * Vlastní převod řeči na text
    * Převod textu na řeč
    * Překlad řeči

2. Stáhněte si [řeči SDK](speech-sdk.md).

3. Postupujte podle příručky rychlý úvod a ukázky SDK použít správné rozhraní API. Pokud používáte rozhraní REST API, musíte taky použít správné koncové body a klíče prostředku.

4. Aktualizujte klientskou aplikaci tak, aby používala hlasové služby a rozhraní API.

## <a name="migration-for-existing-customers"></a>Migrace pro stávající zákazníky

Migrujte stávající klíče prostředků na řeč na portálu služby Speech Services. Použijte k tomu následující postup:

> [!NOTE]
> Klíče prostředku je možné migrovat pouze v rámci stejné oblasti.

1. Přihlaste se k [cris.ai](https://cris.ai/Home/CustomSpeech) portál a vyberte předplatné, v nabídce vpravo nahoře.

2. Vyberte **vybrané předplatné migrovat**.

3. V textovém poli zadejte klíč předplatného a vyberte **migrace**.

## <a name="next-steps"></a>Další postup

* [Vyzkoušejte si hlasové služby zdarma](get-started.md).
* Přečtěte si [převod řeči na text](./speech-to-text.md) koncepty.

## <a name="see-also"></a>Další informace najdete v tématech

* [Co je Speech Service](overview.md)
* [Dokumentace ke službám Speech a sadě Speech SDK](speech-sdk.md#get-the-sdk)
