---
title: Recenze textu v Azure Content Moderator – Content Moderator
description: Zjistěte, jak zkontrolovat text v Content Moderatoru zobrazíte jeho skóre a zjišťovat značky. Pomocí informací určit, zda je příslušný obsah.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 005e6ed853cbc6013d74e7ff479097dcbb0a1043
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259559"
---
# <a name="let-human-reviewers-review-text"></a>Recenzenti mohou lidské recenze textu

Azure Content Moderator můžete použít ke kontrole textu s použitím skóre a zjištěna značky. Pomocí informací k určení, zda je příslušný obsah. 

## <a name="select-or-enter-the-text-to-review"></a>Vyberte nebo zadejte text, který má zkontrolovat

V Content Moderatoru, vyberte **zkuste** kartu. Vyberte **Text** možnost Přejít na úvodní obrazovce moderování textu. Zadejte libovolný text nebo odeslání výchozí ukázkový text pro text automatizované moderování. Můžete zadat maximálně 1 024 znaků.

## <a name="get-ready-to-review-results"></a>Příprava a zkontrolovat výsledky kontroly

Nástroj pro recenze nejprve zavolá rozhraní API pro moderování textu. Poté vygeneruje recenze textu s využitím zjištěných značek. Nástroj pro recenze odpovídá výsledků skóre pro váš tým pozornost.

## <a name="review-text-results"></a>Zkontrolujte výsledky text

Podrobné výsledky se zobrazí v ovládacím prvku windows. Výsledky obsahují zjištěných značek a podmínky, které byly vráceny pomocí rozhraní API pro moderování textu. Chcete-li přepnout stav výběru značky, vyberte značku. Můžete také pracovat s všechny vlastní značky, které jste vytvořili.

![Snímek obrazovky znázorňující nástroj pro revize označený text v okně prohlížeče Chrome](../images/reviewresults_text.png)
