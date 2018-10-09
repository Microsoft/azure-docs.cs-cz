---
title: Migrace ze služby Custom Speech Service na Speech service
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak migrace z Custom Speech service ke službě řeči.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 10/01/2018
ms.author: panosper
ms.openlocfilehash: 4b5d0b66c4d39320a082cb9e76252bfc00db6c3a
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854131"
---
# <a name="migrate-from-the-custom-speech-service-to-the-speech-service"></a>Migrace ze služby Custom Speech service na Speech service

V tomto článku použijte k migraci aplikace z Custom Speech service ve službě řeči.

Custom Speech Service je teď součástí Speech Service. Přepnout na Speech Service, abyste využili výhod nejnovější aktualizace kvality a funkcí.
 
## <a name="migration-for-new-customers"></a>Migrace pro nové zákazníky

Cenový model je jednodušší, Přesun do cenového modelu založeného na hodinu pro službu rozpoznávání řeči.   

1. Vytvořte prostředek Azure v jednotlivých oblastech, kde se vaše aplikace je k dispozici. Název prostředku Azure je **řeči**. U následujících služeb ve stejné oblasti, místo vytvoření samostatné prostředky, můžete použít jeden prostředek Azure:

    * Převod řeči na text
    * Vlastní převod řeči na text
    * Převod textu na řeč
    * Překlad řeči

2. Stáhněte si [řeči SDK](speech-sdk.md). 

3. Postupujte podle příručky rychlý úvod a ukázky SDK použít správné rozhraní API. Pokud používáte rozhraní REST API, musíte taky použít správné koncové body a klíče prostředku. 

4. Aktualizace klientské aplikace používat službu řeči a rozhraní API. 

> [!NOTE]
> * Služba LUIS – Pokud jste povolili řeči v Language Understanding (LUIS), jeden prostředek LUIS ve stejné oblasti fungují pro LUIS, stejně jako hlasové služby. Zobrazit [rozpoznat záměry z řeči](how-to-recognize-intents-from-speech-csharp.md) dokumentaci.
> * Text – text překladu není součástí Speech service. Rozpojuje své vlastní předplatné prostředků Azure.
  


## <a name="migration-for-existing-customers"></a>Migrace pro stávající zákazníky

Stávající zákazníci je potřeba migrovat své existující klíče prostředku Speech service na portálu Speech Service. Použijte k tomu následující postup: 

> [!NOTE] 
> Klíče prostředku je možné migrovat pouze v rámci stejné oblasti. 

1. Přihlaste se k [cris.ai](http://www.cris.ai) portál a vyberte předplatné, v nabídce vpravo nahoře. 

2. Vyberte **vybrané předplatné migrovat**.

3. V textovém poli zadejte klíč předplatného a vyberte **migrace**.

    ![Zadejte předplatné pro migraci](./media/migrate-from-custom-speech-service/migrate-dialog.png)

## <a name="next-steps"></a>Další postup

* [Vyzkoušejte zdarma službu rozpoznávání řeči](get-started.md)
* Přečtěte si [převod řeči na text](./speech-to-text.md) koncepty

## <a name="see-also"></a>Další informace najdete v tématech

* [Co je Speech service](overview.md)
* [Dokumentace k Speech Service a sady SDK](speech-sdk.md#get-the-sdk)