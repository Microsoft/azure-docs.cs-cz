---
title: Přehled protokolu Azure AD .NET | Microsoft Docs
description: Jak používat zprávy HTTP k autorizaci přístupu k webovým aplikacím a webovým rozhraním API ve vašem tenantovi pomocí služby Azure AD
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/18/2018
ms.author: priyamo
ms.openlocfilehash: 72fb594265e69eb1dc16cb29ad4df6acb3a87720
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34663623"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Registrace aplikace pomocí tenanta AD
Nejprve budete muset aplikaci zaregistrovat u klienta služby Azure Active Directory (Azure AD). Pro svou aplikaci tak získáte ID a umožníte jí přijímat tokeny.

* Přihlaste se k webu [Azure Portal](https://portal.azure.com).
* Kliknutím na svůj účet v pravém horním rohu stránky zvolte tenanta Azure AD.
* V levém navigačním podokně klikněte na **Azure Active Directory**.
* Klikněte na **registrace aplikace** a klikněte na **nové registrace aplikace**.
* Postupujte podle zobrazených výzev a vytvořte novou aplikaci. Pro účely tohoto kurzu není důležité, jestli je to webová nebo nativní aplikace. Pokud ale chcete konkrétní příklady webových nebo nativních aplikací, podívejte se na naše [rychlé průvodce](../articles/active-directory/develop/active-directory-developers-guide.md).
  * Pro webové aplikace, zadejte **přihlašovací adresa URL**, což je základní adresu URL aplikace, kde uživatelé se mohou přihlásit v např `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Za nativních aplikací **identifikátor URI pro přesměrování**, které Azure AD použije k vrácení odpovědi tokenu. Zadejte konkrétní hodnotu pro vaši aplikace, např. `http://MyFirstAADApp`.
* Po dokončení registrace Azure AD bude přiřadit aplikaci identifikátor jedinečných klientských **ID aplikace**. Je třeba tuto hodnotu v další části, zkopírujte jej ze stránky aplikace.
* Chcete-li najít aplikaci na portálu Azure, klikněte na tlačítko **registrace aplikace**a potom klikněte na **zobrazit všechny aplikace**.