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
ms.openlocfilehash: cb31bb91c80e4d5dd032b009b40d8e3fc435e0c8
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39359455"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Registrace aplikace pomocí tenanta AD
Nejprve budete muset zaregistrovat aplikaci v tenantu Azure Active Directory (Azure AD). Pro svou aplikaci tak získáte ID a umožníte jí přijímat tokeny.

* Přihlaste se k webu [Azure Portal](https://portal.azure.com).
* Kliknutím na váš účet v pravém horním rohu stránky, a potom kliknutím na zvolte tenanta Azure AD **přepnout adresář** navigace a pak vyberte příslušný tenant. 
  * Tento krok přeskočte, pokud jste v rámci vašeho účtu jenom k jednomu tenantovi Azure AD nebo pokud jste již vybrali odpovídající tenanta Azure AD.
* V levém navigačním podokně klikněte na **Azure Active Directory**.
* Klikněte na **registrace aplikací** a klikněte na **registrace nové aplikace**.
* Postupujte podle zobrazených výzev a vytvořte novou aplikaci. Pro účely tohoto kurzu není důležité, jestli je to webová nebo nativní aplikace. Pokud ale chcete konkrétní příklady webových nebo nativních aplikací, podívejte se na naše [rychlé průvodce](../articles/active-directory/develop/active-directory-developers-guide.md).
  * Pro webové aplikace, zadejte **přihlašovací adresa URL**, což je základní adresa URL aplikace, kde můžou uživatelé přihlásit, například `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Pro nativní aplikace poskytují **identifikátor URI pro přesměrování**, který Azure AD použije k vrácení odpovědí týkajících se tokenu. Zadejte konkrétní hodnotu pro vaši aplikace, např. `http://MyFirstAADApp`.
* Po dokončení registrace Azure AD vaší aplikaci přiřadí jedinečný klientský identifikátor **ID aplikace**. Tuto hodnotu budete potřebovat v dalších částech, proto zkopírujte ho ze stránky aplikace.
* K vyhledání aplikace na webu Azure Portal, klikněte na tlačítko **registrace aplikací**a potom klikněte na tlačítko **zobrazit všechny aplikace**.
