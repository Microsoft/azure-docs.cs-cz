---
title: Registrace jednostránkovéch aplikací – Microsoft Identity Platform | Azure
description: Naučte se vytvářet jednostránkové aplikace (registrace aplikací).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f964d4b4c7032599cf8f74b285f819581fae907b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423658"
---
# <a name="single-page-application-app-registration"></a>Jednostránkové aplikace: registrace aplikace

Tato stránka vysvětluje konkrétní registraci aplikace pro jednostránkové aplikace (SPA).

Podle postupu [Zaregistrujte novou aplikaci s platformou Microsoft Identity](quickstart-register-app.md)a vyberte podporované účty pro vaši aplikaci. Scénář SPA může podporovat ověřování s účty ve vaší organizaci nebo v jakékoli organizaci a osobní účty Microsoft.

V dalším kroku se dozvíte, jaké jsou konkrétní aspekty registrace aplikace, které se vztahují k aplikacím s jednou stránkou.

## <a name="register-a-redirect-uri"></a>Registrovat identifikátor URI pro přesměrování

Implicitní tok odesílá tokeny v přesměrování na jednostránkovou aplikaci běžící ve webovém prohlížeči. Proto je důležité registrovat identifikátor URI přesměrování, kde vaše aplikace může získat tokeny. Zajistěte, aby identifikátor URI přesměrování přesně odpovídal identifikátoru URI vaší aplikace.

V [Azure Portal](https://go.microsoft.com/fwlink/?linkid=2083908)přejdete do vaší registrované aplikace. Na stránce **ověřování** aplikace vyberte **webovou** platformu. Do pole **identifikátor URI pro přesměrování** zadejte hodnotu identifikátoru URI přesměrování vaší aplikace.

## <a name="enable-the-implicit-flow"></a>Povolit implicitní tok

Na stejné stránce **ověřování** musíte v části **Upřesnit nastavení**také povolit **implicitní udělení**. Pokud se vaše aplikace přihlašuje jenom uživatelům a získáváte tokeny ID, stačí zaškrtnout políčko **tokeny ID** .

Pokud vaše aplikace také potřebuje získat přístupové tokeny pro volání rozhraní API, ujistěte se, že je zaškrtnuto i políčko **přístupové tokeny** . Další informace najdete v tématu [tokeny ID](./id-tokens.md) a [přístupové tokeny](./access-tokens.md).

## <a name="api-permissions"></a>Oprávnění rozhraní API

Jednostránkové aplikace mohou volat rozhraní API jménem přihlášeného uživatele. Potřebují požádat o delegovaná oprávnění. Podrobnosti najdete v tématu [Přidání oprávnění pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace kódu aplikace](scenario-spa-app-configuration.md)
