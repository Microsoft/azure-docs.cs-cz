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
ms.date: 01/21/2016
ms.author: priyamo
ms.openlocfilehash: 42ce8dfd30cda7d4085778954350550fd9fdf13d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
## <a name="register-your-application-with-your-ad-tenant"></a>Registrace aplikace pomocí tenanta AD
Nejdřív si musíte zaregistrovat svoji aplikaci v tenantovi Azure Active Directory (Azure AD). Pro svou aplikaci tak získáte ID a umožníte jí přijímat tokeny.

* Přihlaste se k [portálu Azure](https://portal.azure.com).
* Kliknutím na svůj účet v pravém horním rohu stránky zvolte tenanta Azure AD.
* V levém navigačním podokně klikněte na **Azure Active Directory**.
* Klikněte na **Registrace aplikací** a potom klikněte na **Přidat**.
* Postupujte podle zobrazených výzev a vytvořte novou aplikaci. Pro účely tohoto kurzu není důležité, jestli je to webová nebo nativní aplikace. Pokud ale chcete konkrétní příklady webových nebo nativních aplikací, podívejte se na naše [rychlé průvodce](../articles/active-directory/develop/active-directory-developers-guide.md).
  * Pro webové aplikace zadejte **přihlašovací adresu URL**. Je to základní adresa URL aplikace, kde uživatelé se mohou přihlásit, například `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Pro nativní aplikace zadejte **identifikátor URI pro přesměrování**, který Azure AD použije k vrácení odpovědí týkajících se tokenu. Zadejte konkrétní hodnotu pro vaši aplikace, např. `http://MyFirstAADApp`.
* Po dokončení registrace Azure AD vaší aplikaci přiřadí jedinečný klientský identifikátor – ID aplikace. Tuto hodnotu budete potřebovat v následujících částech, proto si ji ze stránky aplikace zkopírujte.
