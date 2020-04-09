---
title: Začínáme s Azure AD v projektech Visual Studio WebApi
description: Jak začít používat Azure Active Directory v projektech WebApi po připojení nebo vytvoření Azure AD pomocí připojených služeb Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 9a9f8aaa9e3f90e78668d2294ea9959b480e3384
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886071"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Začínáme s Azure Active Directory (projekty WebApi)

> [!div class="op_single_selector"]
> - [Začínáme](vs-active-directory-webapi-getting-started.md)
> - [Co se přihodilo](vs-active-directory-webapi-what-happened.md)

Tento článek obsahuje další pokyny po přidání služby Active Directory do projektu ASP.NET WebAPI prostřednictvím příkazu **Project > Connected Services** sady Visual Studio. Pokud jste službu ještě do projektu nepřidali, můžete tak učinit kdykoli.

Informace o změnách provedených v projektu projektu při přidávání připojené služby naleznete v tématu [Co se stalo s projektem WebAPI?](vs-active-directory-webapi-what-happened.md)

## <a name="requiring-authentication-to-access-controllers"></a>Vyžadování ověřování pro přístup k řadičům

Všechny řadiče v projektu byly `[Authorize]` ozdobeny atributem. Tento atribut vyžaduje, aby byl uživatel ověřen před přístupem k rozhraním API definovaným těmito řadiči. Chcete-li povolit přístup k řadiči anonymně, odeberte tento atribut z řadiče. Pokud chcete nastavit oprávnění na podrobnější úrovni, použijte atribut pro každou metodu, která vyžaduje autorizaci namísto jeho použití na třídu kontroleru.

## <a name="next-steps"></a>Další kroky

- [Scénáře ověřování pro Službu Azure Active Directory](authentication-scenarios.md)
- [Přidání přihlášení s Microsoftem do ASP.NET webové aplikace](quickstart-v2-aspnet-webapp.md)
