---
title: Export vašeho předplatného Azure informace na nejvyšší úrovni | Dokumentace Microsoftu
description: Popisuje, jak zobrazit všechna ID předplatných Azure spojené s vaším účtem.
keywords: ''
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: adpick
ms.openlocfilehash: 7bc9f4df6e98dd86283c4389466b13b8f6bb4d15
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35897327"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Vyexportovat a zobrazit informace o předplatném nejvyšší úrovně
Pokud chcete zobrazit sady přidružené přihlašovací údaje uživatele, ID předplatného [stáhnout soubor .json, informace o vašem předplatném z centra účtů Azure](http://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Soubor stažený .json obsahuje následující informace:
- E-mailu: E-mailovou adresu spojenou s vaším účtem.
- Identifikátor PUID: Jedinečný identifikátor přidružený k fakturačnímu účtu.
- SubscriptionIds: Seznam předplatných, které patří ke svému účtu, uvedené podle ID předplatného.

### <a name="subscriptionsjson-sample"></a>Ukázka Subscriptions.JSON
~~~~
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
~~~~
