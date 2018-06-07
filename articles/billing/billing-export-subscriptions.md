---
title: Export vašeho předplatného Azure informace na nejvyšší úrovni | Microsoft Docs
description: Popisuje, jak můžete zobrazit všechny předplatné Azure spojené s vaším účtem ID.
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
ms.date: 10/9/2017
ms.author: adpick
ms.openlocfilehash: b995b0c7154faab66a44bef1a7d74eeb8404e5c1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654814"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Export a zobrazit informace z vašeho nejvyšší úrovně předplatného
Pokud potřebujete zobrazit sadu předplatné ID přidružených k pověření uživatele [stáhnout soubor .json s informace o vašem předplatném z centra účtů Azure](http://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Soubor stažený .json poskytuje následující informace:
- E-mailu: E-mailová adresa spojená s vaším účtem.
- Identifikátor PUID: Jedinečný identifikátor spojené s vaším účtem fakturace.
- SubscriptionIds: Seznam odběry, které patří k účtu, uvedené podle ID předplatného.

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
