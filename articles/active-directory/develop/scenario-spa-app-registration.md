---
title: Registrace jednostránkových aplikací – platforma identit Microsoftu | Azure
description: Přečtěte si, jak vytvořit jednostránkovou aplikaci (registrace aplikace)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 6f690a8b3436a45d434ccad2bbaa7d2a1b0b76aa
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882144"
---
# <a name="single-page-application-app-registration"></a>Jednostránková aplikace: Registrace aplikace

Tato stránka vysvětluje specifika registrace aplikace pro jednostránkovou aplikaci (SPA).

Podle pokynů [zaregistrujte novou aplikaci na platformě microsoft identity](quickstart-register-app.md)a vyberte podporované účty pro vaši aplikaci. Scénář SPA může podporovat ověřování s účty ve vaší organizaci nebo v jakékoli organizaci a osobních účtech Microsoft.

Dále se dozvíte konkrétní aspekty registrace aplikace, které se vztahují na jednostránkové aplikace.

## <a name="register-a-redirect-uri"></a>Registrace identifikátoru URI přesměrování

Implicitní tok odešle tokeny v přesměrování do jednostránkové aplikace spuštěné ve webovém prohlížeči. Proto je důležité zaregistrovat identifikátor URI přesměrování, kde může vaše aplikace přijímat tokeny. Ujistěte se, že identifikátor URI přesměrování přesně odpovídá identifikátoru URI pro vaši aplikaci.

Na [webu Azure Portal](https://go.microsoft.com/fwlink/?linkid=2083908)přejděte na registrovanou aplikaci. Na stránce **Ověřování** aplikace vyberte **webovou** platformu. Do pole **Identifikátor URI přesměrování** zadejte hodnotu identifikátoru URI přesměrování pro vaši aplikaci.

## <a name="enable-the-implicit-flow"></a>Povolit implicitní tok

Na stejné stránce **ověřování** je v části **Upřesnit nastavení**také nutné povolit **implicitní udělení**. Pokud vaše aplikace jenom přihlášení uživatelů a získání Tokeny ID, stačí zaškrtnout políčko **ID tokeny.**

Pokud vaše aplikace také potřebuje získat přístupové tokeny pro volání api, nezapomeňte také zaškrtnout políčko **Tokeny přístupu.** Další informace naleznete v tématu [ID tokeny](./id-tokens.md) a [přístupové tokeny](./access-tokens.md).

## <a name="api-permissions"></a>Oprávnění rozhraní API

Jednostránkové aplikace mohou volat rozhraní API jménem přihlášeného uživatele. Musí požádat o delegovaná oprávnění. Podrobnosti naleznete v tématu [Přidání oprávnění pro přístup k webovým sítím API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace kódu aplikace](scenario-spa-app-configuration.md)
