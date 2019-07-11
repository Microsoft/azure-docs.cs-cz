---
title: Řešení potíží – Personalizer
titleSuffix: Azure Cognitive Services
description: Řešení potíží s dotazy týkající se Personalizer najdete v tomto článku.
author: edjez
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: edjez
ms.openlocfilehash: be6119d96b89622f45db1099a47e858a5893c2cb
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722244"
---
# <a name="personalizer-troubleshooting"></a>Řešení potíží s personalizer

Tento článek obsahuje odpovědi na časté otázky ke správě řešení problémů s Personalizer.

## <a name="learning-loop"></a>Učení smyčky

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Smyčka learning vypadá, že nepodporuje další. Jak to mohu napravit?

Smyčka learning musí několik tisíc volání Reward předtím, než pořadí volání upřednostnit efektivně. 

Pokud si nejste jisti, o jak smyčku learning aktuálně nepracuje, spusťte [offline hodnocení](concepts-offline-evaluation.md)a použijí tyto zásady opravený učení. 

## <a name="next-steps"></a>Další kroky

[Nastavte četnost aktualizace modelu](how-to-settings.md#model-update-frequency)