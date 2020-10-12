---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: dc7849e52b3e00c87d271ee8aef8788d816dff7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90971018"
---
- Každá spravovaná identita se počítá s limitem kvóty objektu v tenantovi Azure AD, jak je popsáno v tématu [limity a omezení služby Azure AD](../articles/active-directory/users-groups-roles/directory-service-limits-restrictions.md).
-   Rychlost, s jakou je možné vytvořit spravované identity, má následující omezení:

    1. Na tenanta Azure AD na oblast Azure: 200 vytvoření operací za 20 sekund.
    2. Podle předplatného Azure na oblast Azure: 40 vytvoření operací za 20 sekund.

- Když vytváříte uživatelem přiřazené spravované identity, jsou podporovány pouze alfanumerické znaky (0-9, a-z, a a-Z) a spojovník (-). Aby přiřazení k virtuálnímu počítači nebo sadě škálování virtuálních počítačů fungovalo správně, název je omezený na 24 znaků.
