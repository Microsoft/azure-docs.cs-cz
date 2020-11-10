---
title: Registrovat aplikace démona, které volají webová rozhraní API – Microsoft Identity Platform | Azure
description: Informace o tom, jak vytvořit aplikaci démona, která volá webová rozhraní API – registrace aplikace
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 3b77cc93385efb0bbb8a9b87d29de9bad5cd4ceb
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443326"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Aplikace démona, která volá webová rozhraní API – registrace aplikace

V případě aplikace démona je zde uvedeno, co potřebujete znát při registraci aplikace.

## <a name="supported-account-types"></a>Podporované typy účtu

Aplikace démona mají smysl jenom v klientech Azure AD. Takže když vytvoříte aplikaci, musíte vybrat jednu z následujících možností:

- **Účty pouze v tomto organizačním adresáři**. Tato volba je nejběžnější, protože aplikace démona jsou obvykle zapisovány obchodními vývojáři (LOB).
- **Účty v libovolném organizačním adresáři**. Tuto možnost můžete vybrat, pokud jste nezávislý výrobce softwaru, který zákazníkům poskytuje nástroj pro nástroj. Budete potřebovat schválit správce tenanta vašich zákazníků.

## <a name="authentication---no-reply-uri-needed"></a>Ověřování-není potřeba žádný identifikátor URI pro odpověď

V případě, že vaše důvěrná klientská aplikace používá *pouze* tok přihlašovacích údajů klienta, identifikátor URI odpovědi nemusí být zaregistrován. Není nutné pro konfiguraci nebo konstrukci aplikace. Tok přihlašovacích údajů klienta ho nepoužívá.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Oprávnění rozhraní API – oprávnění aplikace a souhlas správce

Aplikace démona může vyžadovat pouze oprávnění aplikace pro rozhraní API (nejsou delegovaná oprávnění). Na stránce **oprávnění rozhraní API** pro registraci aplikace po výběru možnosti **Přidat oprávnění** a zvolení rodiny rozhraní API zvolte **oprávnění aplikace** a pak vyberte vaše oprávnění.

![Oprávnění aplikace a souhlas správce](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> Webové rozhraní API, které chcete volat, musí definovat *oprávnění aplikace (aplikační role)* , ne delegovaná oprávnění. Podrobnosti o tom, jak vystavit takové rozhraní API, najdete v tématu [Protected Web API: registrace aplikace – když je webové rozhraní API voláno aplikací démona](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).

Aplikace démona vyžaduje, aby správce tenanta předá aplikaci, která volá webové rozhraní API, předem svůj souhlas. Správci tenanta poskytují tento souhlas na stejné stránce **oprávnění rozhraní API** tak, že v ***naší organizaci* vyberete udělit souhlas správce** .

Pokud jste nezávislý výrobce softwaru, který vytváří víceklientské aplikace, měli byste si přečtěte část nasazení v případě víceklientské aplikace [démona](scenario-daemon-production.md#deployment---multitenant-daemon-apps).

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři, [Konfigurace kódu aplikace](./scenario-daemon-app-configuration.md).
