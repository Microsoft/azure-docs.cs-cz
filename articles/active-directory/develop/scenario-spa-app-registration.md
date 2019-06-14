---
title: Jednostránková aplikace (registrace aplikace) – platforma identit Microsoft
description: Další informace o vytváření jednostránkové aplikace (registrace aplikace)
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
ms.openlocfilehash: b1faf4669dca2aaaf3f873e66f859473ccd99f10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074827"
---
# <a name="single-page-application---app-registration"></a>Jednostránková aplikace – registrace aplikace

Tato stránka vysvětluje podrobnosti registrace aplikace pro jednostránkové aplikace (SPA).

Uvedený postup [registrace nové aplikace s platformou identity Microsoft](quickstart-register-app.md)a vyberte podporovaných účtů pro vaši aplikaci. Jednostránková aplikace scénář podporuje ověřování s účty ve vaší organizaci nebo každé společnosti a osobních účtů Microsoft.

Teď chcete zjistěte, konkrétní aspekty registrace aplikace, které platí pro jednostránkové aplikace.

## <a name="register-a-redirect-uri"></a>Registrace na identifikátor URI přesměrování

Implicitní tok odesílá tokeny v přesměrování jednostránkovou aplikaci běžící ve webovém prohlížeči. Proto je důležité požadavky pro registraci na identifikátor URI, kde aplikace může přijímat tokeny přesměrování. Ověřte prosím, že přesměrování, který odpovídá identifikátoru URI přesně s identifikátorem URI pro vaši aplikaci.

V [webu Azure portal](https://go.microsoft.com/fwlink/?linkid=2083908), přejděte k registrované aplikaci, na **ověřování** stránka aplikace, vyberte **webové** platformy a zadejte hodnotu identifikátor URI přesměrování pro vaši aplikaci v **identifikátor URI pro přesměrování** pole.

## <a name="enable-the-implicit-flow"></a>Povolit implicitní tok

Na stejném **ověřování** stránce v části **upřesňující nastavení**, musíte také povolit **implicitní grant**. Pokud aplikace provádí pouze přihlášení uživatelů a získání tokeny typu ID, je dostatečná povolení **tokeny typu ID** zaškrtávací políčko.

Pokud vaše aplikace potřebuje také získat přístupové tokeny pro volání rozhraní API, ujistěte se, že chcete povolit **přístupové tokeny** také zaškrtávací políčko. Další informace najdete v tématu [tokeny typu ID](./id-tokens.md) a [přístupové tokeny](./access-tokens.md).

## <a name="api-permissions"></a>Oprávnění k rozhraní API

Jednostránkové aplikace můžou zavolat rozhraní API jménem přihlášeného uživatele. Budou muset požadovat delegovaná oprávnění. Podrobnosti najdete v tématu [přidat oprávnění pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Konfigurace kódu aplikace](scenario-spa-app-configuration.md)
