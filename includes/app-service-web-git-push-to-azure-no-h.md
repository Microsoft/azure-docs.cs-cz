---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 125561d61afe0fb7f704144efa1c8c20ecf03db1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
Zpět v _okně místního terminálu_ přidejte vzdálené úložiště Azure do místního úložiště Git. Nahraďte _&lt;deploymentLocalGitUrl-from-create-step>_ adresou URL vzdáleného úložiště Git, kterou jste uložili v kroku [Vytvoření webové aplikace](#create).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Nasaďte aplikaci do vzdáleného úložiště Azure pomocí následujícího příkazu. Po zobrazení výzvy k zadání přihlašovacích údajů ve Správci přihlašovacích údajů Git se ujistěte, že zadáváte přihlašovací údaje, které jste vytvořili v části [Konfigurace uživatele nasazení](#configure-a-deployment-user), a ne přihlašovací údaje, se kterým se přihlašujete k webu Azure Portal.

```bash
git push azure master
```

Spuštění tohoto příkazu může trvat několik minut. Při spuštění příkaz zobrazí podobné informace jako v následujícím příkladu:
