---
title: Export informací nejvyšší úrovně předplatného Azure
description: Popisuje, jak můžete zobrazit všechna ID předplatných Azure, která jsou přidružená k vašemu účtu.
author: bandersmsft
ms.reviewer: matrive
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: b3b2e9b501f2ae103900a085e9b7a4b412efb78e
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686832"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Export a zobrazení informací o předplatném nejvyšší úrovně
Pokud potřebujete zobrazit sadu ID předplatných přidružených k vašim přihlašovacím údajům uživatele, [stáhněte si soubor JSON s informacemi o předplatném z Centra účtů Azure](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

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
