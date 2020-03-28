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
ms.openlocfilehash: e5ba08da5c58ff486bc26f2c771dfef55452629a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75681015"
---
Zpět v okně místního terminálu přidejte vzdálené úložiště Azure do místního úložiště Git. Nahraďte * \<>deploymentLocalGitUrl-from-create-step* adresou URL vzdáleného ovladače Git, který jste uložili z [možnosti Vytvořit webovou aplikaci](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Nasaďte aplikaci do vzdáleného úložiště Azure pomocí následujícího příkazu. Když vás Správce přihlašovacích údajů Git vyzve k zadání přihlašovacích údajů, ujistěte se, že jste zadali přihlašovací údaje, které jste vytvořili v [části Konfigurace uživatele nasazení](/azure/app-service/containers/tutorial-python-postgresql-app#configure-a-deployment-user), nikoli přihlašovací údaje, které používáte k přihlášení k portálu Azure.

```bash
git push azure master
```

Spuštění tohoto příkazu může trvat několik minut. Při spuštění příkaz zobrazí podobné informace jako v následujícím příkladu:
