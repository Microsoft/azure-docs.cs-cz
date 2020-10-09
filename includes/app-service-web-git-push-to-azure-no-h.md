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
ms.openlocfilehash: 8539696f4521a1b4a2f56fe7d2936b45dec26ec9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88077649"
---
Zpět v okně místního terminálu přidejte vzdálené úložiště Azure do místního úložiště Git. Nahraďte *\<deploymentLocalGitUrl-from-create-step>* adresou URL vzdáleného úložiště Git, kterou jste uložili z části [Vytvoření webové aplikace](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Nasaďte aplikaci do vzdáleného úložiště Azure pomocí následujícího příkazu. Když vám správce přihlašovacích údajů Git vyzve k zadání přihlašovacích údajů, ujistěte se, že jste zadali přihlašovací údaje, které jste vytvořili v části **Konfigurace uživatele nasazení**, a ne přihlašovací údaje, které používáte k přihlášení k Azure Portal.

```bash
git push azure master
```

Spuštění tohoto příkazu může trvat několik minut. Při spuštění příkaz zobrazí podobné informace jako v následujícím příkladu:
