---
title: Začínáme s Azure AD v projektech Visual Studio WebApi
description: Jak začít používat Azure Active Directory v projekty WebApi po připojení k nebo vytváření Azure AD pomocí sady Visual Studio připojené služby
services: active-directory
author: ghogen
manager: douge
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 662d958fc7ef6fe5c9d0e61a1d8e48983d10196f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198775"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Začínáme s Azure Active Directory (projekty WebApi)

> [!div class="op_single_selector"]
> - [Začínáme](vs-active-directory-webapi-getting-started.md)
> - [Co se přihodilo](vs-active-directory-webapi-what-happened.md)

Tento článek obsahuje další doprovodné materiály po přidání do projektu ASP.NET WebAPI prostřednictvím služby Active Directory **Projekt > připojené služby** příkazů sady Visual Studio. Pokud ještě není přidáte službu do projektu, provést kdykoli.

Zobrazit [co se stalo s mým projektem WebAPI?](vs-active-directory-webapi-what-happened.md) pro změny do svého projektu při přidání připojené služby.

## <a name="requiring-authentication-to-access-controllers"></a>Vyžádání ověření pro přístup k řadiči

Všechny řadiče ve vašem projektu byly opatřený s `[Authorize]` atribut. Tento atribut vyžaduje, aby uživatel ověřený před přístupem k rozhraní API definované tyto řadiče. Povolit kontroleru se dá přistupovat anonymně, odeberte tento atribut z kontroleru. Pokud chcete nastavit oprávnění na podrobnější úrovni, použijte atribut pro každou metodu, která vyžaduje ověření místo použití třídy kontroleru.

## <a name="next-steps"></a>Další postup

- [Scénáře ověřování pro Azure Active Directory](authentication-scenarios.md)
- [Přidání přihlašování s Microsoftem do webové aplikace ASP.NET](quickstart-v1-aspnet-webapp.md)
