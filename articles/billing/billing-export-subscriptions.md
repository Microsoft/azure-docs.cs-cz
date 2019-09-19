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
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 8a3d1a3a6b1dce1d729942715bbe5962837ff093
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "60918652"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Export a zobrazení informací o předplatném nejvyšší úrovně
Pokud potřebujete zobrazit sadu ID předplatných přidružených k vašim přihlašovacím údajům uživatele, [stáhněte si soubor JSON s informacemi o předplatném z Centra účtů Azure](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Stažený soubor JSON poskytuje následující informace:
- Email: E-mailová adresa, která je přidružená vašemu účtu.
- PUID: Jedinečný identifikátor přidružený vašemu fakturačnímu účtu.
- SubscriptionIds: Seznam předplatných náležejících k vašemu účtu, která jsou uvedená podle ID předplatného.

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
