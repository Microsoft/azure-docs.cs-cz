---
title: Recenze textu v Azure Content Moderator – Content Moderator
description: Zjistěte, jak zkontrolovat text v Content Moderatoru zobrazíte jeho skóre a zjišťovat značky. Pomocí informací určit, zda je příslušný obsah.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 896956653ed924b7e30e63c1ae5cfa90dd0cc94d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219783"
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
