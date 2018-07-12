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
ms.openlocfilehash: 9f865897ee478f25a44fe876d44aec253e84eb62
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38731892"
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
