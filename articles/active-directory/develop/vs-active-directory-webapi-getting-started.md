---
title: Začínáme s Azure AD v projektech Visual Studio WebApi
description: Jak začít používat Azure Active Directory v projektech WebApi po připojení k nebo vytvoření Azure AD pomocí připojených služeb sady Visual Studio
services: active-directory
ms.service: active-directory
ms.subservice: develop
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
ms.openlocfilehash: 662e0a3e85684287c636b8d0b512b072c978dd3f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320733"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Začínáme s Azure Active Directory (projekty WebApi)

> [!div class="op_single_selector"]
> - [Začínáme](vs-active-directory-webapi-getting-started.md)
> - [Co se přihodilo](vs-active-directory-webapi-what-happened.md)

Tento článek poskytuje další doprovodné materiály po přidání služby Active Directory do projektu ASP.NET WebAPI prostřednictvím příkazu **project > Connected Services** sady Visual Studio. Pokud jste ještě nepřidali službu do projektu, můžete to udělat kdykoli.

Podívejte se, [co se stalo s mým projektem WebApi?](vs-active-directory-webapi-what-happened.md) pro změny provedené v projektu při přidávání připojené služby.

## <a name="requiring-authentication-to-access-controllers"></a>Vyžadování ověřování pro přístup k řadičům

Všechny řadiče v projektu se napracovaly s `[Authorize]` atributem. Tento atribut vyžaduje, aby byl uživatel ověřen před přístupem k rozhraním API definovaným těmito řadiči. K povolení anonymního přístup k řadiči odeberte tento atribut z kontroleru. Chcete-li nastavit oprávnění na podrobnější úrovni, použijte atribut pro každou metodu, která vyžaduje autorizaci namísto použití pro třídu kontroleru.

## <a name="next-steps"></a>Další postup

- [Scénáře ověřování pro Azure Active Directory](authentication-scenarios.md)
- [Přidání přihlašování do webové aplikace ASP.NET pomocí Microsoftu](quickstart-v1-aspnet-webapp.md)
