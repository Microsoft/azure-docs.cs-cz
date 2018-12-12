---
title: Migrace ze služby Custom Speech Service pro hlasové služby
titlesuffix: Azure Cognitive Services
description: Custom Speech Service je teď součástí Speech Service. Přepnout na Speech Service, abyste využili výhod nejnovější aktualizace kvality a funkcí.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: b5879fabba70308c33101699dae4443e6b1b7070
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084528"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrace ze služby Custom Speech Service na Speech Service

V tomto článku použijte k migraci aplikace z Custom Speech Service ve službě řeči.

Custom Speech Service je teď součástí Speech Service. Přepnout na Speech Service, abyste využili výhod nejnovější aktualizace kvality a funkcí.

## <a name="migration-for-new-customers"></a>Migrace pro nové zákazníky

Cenový model je jednodušší, pomocí cenového modelu založeného na hodinu pro službu rozpoznávání řeči.  

1. Vytvořte prostředek Azure v jednotlivých oblastech, kde se vaše aplikace je k dispozici. Název prostředku Azure je **řeči**. U následujících služeb ve stejné oblasti, místo vytvoření samostatné prostředky, můžete použít jeden prostředek Azure:

    * Převod řeči na text
    * Vlastní převod řeči na text
    * Převod textu na řeč
    * Překlad řeči

2. Stáhněte si [řeči SDK](speech-sdk.md).

3. Postupujte podle příručky rychlý úvod a ukázky SDK použít správné rozhraní API. Pokud používáte rozhraní REST API, musíte taky použít správné koncové body a klíče prostředku.

4. Aktualizace klientské aplikace používat rozhraní API a Speech Service.

> [!NOTE]
> * Pokud jste povolili řeči v Language Understanding (LUIS), bude fungovat na jeden prostředek LUIS ve stejné oblasti pro LUIS, stejně jako hlasové služby. Další informace najdete v tématu [rozpoznat záměry z řeči](how-to-recognize-intents-from-speech-csharp.md).
> * Text – text překladu není součástí Speech Service. Tato funkce vyžaduje svou vlastní předplatné prostředků Azure.
 


## <a name="migration-for-existing-customers"></a>Migrace pro stávající zákazníky

Migrujte vaše stávající klíče prostředku na Speech Service na portálu Speech Service. Použijte k tomu následující postup:

> [!NOTE]
> Klíče prostředku je možné migrovat pouze v rámci stejné oblasti.

1. Přihlaste se k [cris.ai](http://www.cris.ai) portál a vyberte předplatné, v nabídce vpravo nahoře.

2. Vyberte **vybrané předplatné migrovat**.

3. V textovém poli zadejte klíč předplatného a vyberte **migrace**.

## <a name="next-steps"></a>Další postup

* [Vyzkoušejte zdarma službu řeči](get-started.md).
* Přečtěte si [převod řeči na text](./speech-to-text.md) koncepty.

## <a name="see-also"></a>Další informace najdete v tématech

* [Co je Speech Service](overview.md)
* [Dokumentace k Speech Service a sady SDK](speech-sdk.md#get-the-sdk)
