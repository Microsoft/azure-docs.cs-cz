---
title: Začínáme s Azure AD v projektech Visual Studio WebApi
description: Jak začít používat Azure Active Directory v projektech WebApi po připojení k nebo vytvoření Azure AD pomocí připojených služeb sady Visual Studio
author: ghogen
manager: jillfra
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 33894e237144628634c3b63393130eb0af5b9877
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159450"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Začínáme s Azure Active Directory (projekty WebApi)

> [!div class="op_single_selector"]
> - [Začínáme](vs-active-directory-webapi-getting-started.md)
> - [Co se přihodilo](vs-active-directory-webapi-what-happened.md)

Tento článek poskytuje další doprovodné materiály po přidání služby Active Directory do projektu ASP.NET WebAPI prostřednictvím příkazu **project > Connected Services** sady Visual Studio. Pokud jste ještě nepřidali službu do projektu, můžete to udělat kdykoli.

Podívejte se, [co se stalo s mým projektem WebApi?](vs-active-directory-webapi-what-happened.md) pro změny provedené v projektu při přidávání připojené služby.

## <a name="requiring-authentication-to-access-controllers"></a>Vyžadování ověřování pro přístup k řadičům

Všechny řadiče v projektu byly navýšené pomocí atributu `[Authorize]`. Tento atribut vyžaduje, aby byl uživatel ověřen před přístupem k rozhraním API definovaným těmito řadiči. K povolení anonymního přístup k řadiči odeberte tento atribut z kontroleru. Chcete-li nastavit oprávnění na podrobnější úrovni, použijte atribut pro každou metodu, která vyžaduje autorizaci namísto použití pro třídu kontroleru.

## <a name="next-steps"></a>Další kroky

- [Scénáře ověřování pro Azure Active Directory](authentication-scenarios.md)
- [Přidání přihlašování do webové aplikace ASP.NET pomocí Microsoftu](quickstart-v2-aspnet-webapp.md)
