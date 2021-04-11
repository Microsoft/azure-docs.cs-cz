---
title: Přehled služby Kudu
description: Seznamte se s modulem, který využívá průběžné nasazování v App Service a jeho funkcích.
ms.date: 03/17/2021
ms.topic: reference
ms.openlocfilehash: ad1456f1ca123127f3d84aa8195c99fc34872aee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609112"
---
# <a name="kudu-service-overview"></a>Přehled služby Kudu

Kudu je modul s řadou funkcí v [Azure App Service](overview.md) týkající se nasazení založeného na správě zdrojového kódu a dalších metod nasazení, jako je třeba synchronizace Dropboxu a OneDrivu. 

## <a name="access-kudu-for-your-app"></a>Přístup k Kudu pro vaši aplikaci
Kdykoli vytvoříte aplikaci, App Service pro ni vytvoří doprovodnou aplikaci, která je zabezpečená protokolem HTTPS. Tato aplikace Kudu je dostupná na:

- Aplikace není v izolované úrovni: `https://<app-name>.scm.azurewebsites.net`
- Aplikace v izolované úrovni (App Service Environment): `https://<app-name>.scm.<ase-name>.p.azurewebsites.net`

Další informace najdete v tématu [přístup ke službě Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).

## <a name="kudu-features"></a>Funkce Kudu

Kudu poskytuje užitečné informace o vaší aplikaci App Service, například:

- Nastavení aplikace
- Připojovací řetězce
- Proměnné prostředí
- Serverové proměnné
- Hlavičky protokolu HTTP

Poskytuje také další funkce, jako například:

- Spusťte příkazy v [konzole Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console).
- Stáhněte si diagnostické výpisy služby IIS nebo protokoly Docker.
- Správa procesů služby IIS a rozšíření webu.
- Přidejte Webhooky nasazení pro Windows APS.
- Povoluje uživatelské rozhraní nasazení ZIP s `/ZipDeploy` .
- Generuje [vlastní skripty nasazení](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
- Povolí přístup pomocí [REST API](https://github.com/projectkudu/kudu/wiki/REST-API).

## <a name="more-resources"></a>Další zdroje informací

Kudu je [Open source projekt](https://github.com/projectkudu/kudu)a má jeho dokumentaci na [wikiwebu Kudu](https://github.com/projectkudu/kudu/wiki).

