---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8f1fe6ae38c708c5205f8c1230da05457d6b7010
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764295"
---
FTP a místní Git se můžou nasadit do webové aplikace Azure pomocí *uživatele nasazení*. Jakmile nakonfigurujete uživatele nasazení, můžete ho použít pro všechna nasazení Azure. Uživatelské jméno a heslo nasazení na úrovni účtu se liší od přihlašovacích údajů předplatného Azure. 

Pokud chcete nakonfigurovat uživatele nasazení, spusťte v Azure Cloud Shell příkaz [AZ WebApp Deployment User set](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) . Nahraďte \<username> a \<password> pomocí uživatelského jména a hesla pro nasazení. 

- Uživatelské jméno musí být v rámci Azure jedinečné a pro místní nabízená oznámení Git nesmí obsahovat \@ symbol. 
- Heslo musí mít délku alespoň osm znaků a dva z následujících tří prvků: písmena, číslice a symboly. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Výstup JSON zobrazuje heslo jako `null` . Pokud se zobrazí chyba `'Conflict'. Details: 409`, změňte uživatelské jméno. Pokud se zobrazí chyba `'Bad Request'. Details: 400`, použijte silnější heslo. 

Poznamenejte si uživatelské jméno a heslo, které chcete použít k nasazení webových aplikací.
