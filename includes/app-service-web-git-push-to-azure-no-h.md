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
ms.openlocfilehash: 08f4e76869f124d946566f64da394c895d0af308
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102205999"
---
Zpět v okně místního terminálu přidejte vzdálené úložiště Azure do místního úložiště Git. Nahraďte *\<deploymentLocalGitUrl-from-create-step>* adresou URL vzdáleného úložiště Git, kterou jste uložili z části [Vytvoření webové aplikace](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Nasaďte aplikaci do vzdáleného úložiště Azure pomocí následujícího příkazu. Když vám správce přihlašovacích údajů Git vyzve k zadání přihlašovacích údajů, ujistěte se, že jste zadali přihlašovací údaje, které jste vytvořili v části **Konfigurace uživatele nasazení**, a ne přihlašovací údaje, které používáte k přihlášení k Azure Portal.

```bash
git push azure main
```

Spuštění tohoto příkazu může trvat několik minut. Při spuštění příkaz zobrazí podobné informace jako v následujícím příkladu:
