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
ms.openlocfilehash: aa6f18d4f667862687083c5db3679ce9d8e188cd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66137464"
---
Zpět v _okně místního terminálu_ přidejte vzdálené úložiště Azure do místního úložiště Git. Nahraďte _&lt;deploymentLocalGitUrl-from-create-step>_ adresou URL vzdáleného úložiště Git, kterou jste uložili v kroku [Vytvoření webové aplikace](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Nasaďte aplikaci do vzdáleného úložiště Azure pomocí následujícího příkazu. Když se zobrazí výzva k zadání přihlašovacích údajů ve Správci přihlašovacích údajů Git, ujistěte se, abyste zadali přihlašovací údaje, které jste vytvořili v konfigurace uživatele nasazení, ne přihlašovací údaje, které používáte k přihlášení k webu Azure portal.

```bash
git push azure master
```

Spuštění tohoto příkazu může trvat několik minut. Při spuštění příkaz zobrazí podobné informace jako v následujícím příkladu:
