---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: b18b6e2fd5bb2071f57033dbe74ef87da028720b
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843007"
---
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Konfigurace webové aplikace ASP.NET s informace o registraci vaší aplikace

V tomto kroku nakonfigurujete svůj projekt na používání protokolu SSL a pak použijte adresu URL protokolu SSL a nakonfigurujte informace o registraci vaší aplikace. Po dokončení přidání aplikace ' informace o registraci do vašeho řešení prostřednictvím *web.config*.

1.  V Průzkumníku řešení vyberte projekt a podívejte se na `Properties` okna (Pokud se nezobrazí okno Vlastnosti, stisknutím klávesy F4)
2.  Změna `SSL Enabled` do `True`
3.  Zkopírujte hodnotu z `SSL URL` výše a vložte ji `Redirect URL` pole v horní části této stránky a pak klikněte na *aktualizace*:<br/><br/>![Vlastnosti projektu](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
4.  Přidejte následující kód do `web.config` soubor umístěný ve složce na kořenovém oddílu `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
