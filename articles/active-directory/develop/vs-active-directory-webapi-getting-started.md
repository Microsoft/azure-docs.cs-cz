---
title: Začínáme s Azure AD v projektech Visual Studio WebApi | Microsoft Docs
description: Jak začít používat Azure Active Directory v projektech WebApi po připojení k nebo vytváření Azure AD pomocí sady Visual Studio připojené služby
services: active-directory
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: f44c60cc02ef93fb8bd08954d8b059889eb82df1
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Začínáme s Azure Active Directory (WebApi projekty)

> [!div class="op_single_selector"]
> - [Začínáme](vs-active-directory-webapi-getting-started.md)
> - [Co se přihodilo](vs-active-directory-webapi-what-happened.md)

Tento článek obsahuje další pokyny po přidání do projektu ASP.NET WebAPI prostřednictvím služby Active Directory **Projekt > připojené služby** příkaz sady Visual Studio. Pokud jste již přidali službu do projektu, můžete tak učinit kdykoli.

V tématu [co se stalo s projektu WebAPI?](vs-active-directory-webapi-what-happened.md) pro změny provedené při přidání připojené služby v projektu.

## <a name="requiring-authentication-to-access-controllers"></a>Vyžádání ověření řadičům přístup

Všechny řadiče ve vašem projektu byly ozdobené s `[Authorize]` atribut. Tento atribut vyžaduje, aby uživatel k ověření před přístupem k rozhraní API definované tyto řadiče. Chcete-li umožňují řadiči získat anonymní přístup, odeberte tento atribut z řadiče. Pokud chcete nastavit oprávnění na podrobnější úrovni, použijte atribut pro každou metodu, která vyžaduje ověření, namísto aplikace do třídy kontroleru.

## <a name="next-steps"></a>Další postup

- [Scénáře ověřování pro Azure Active Directory](active-directory-authentication-scenarios.md)
- [Přidání přihlášení se společností Microsoft do webové aplikace ASP.NET](guidedsetups/active-directory-aspnetwebapp-v1.md)
