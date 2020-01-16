---
title: Export informací nejvyšší úrovně předplatného Azure | Microsoft Docs
description: Popisuje, jak můžete zobrazit všechna ID předplatných Azure, která jsou přidružená k vašemu účtu.
keywords: ''
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 553cc7fd27571ebc925e33f824060c023664a369
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75991773"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Export a zobrazení informací o předplatném nejvyšší úrovně
Pokud potřebujete zobrazit sadu ID předplatných přidružených k vašim přihlašovacím údajům uživatele, [stáhněte si soubor JSON s informacemi o předplatném z Centra účtů Azure](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

Stažený soubor JSON poskytuje následující informace:
- E-mail: e-mailová adresa přidružená k vašemu účtu.
- PUID: jedinečný identifikátor přidružený k vašemu fakturačnímu účtu.
- SubscriptionIds: seznam předplatných náležejících k vašemu účtu, který je výčtem ID předplatného.

### <a name="subscriptionsjson-sample"></a>Ukázka souboru subscriptions.json

```json
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
```
