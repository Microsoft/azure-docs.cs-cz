---
title: Přehled protokolu Azure AD .NET | Microsoft Docs
description: Jak používat zprávy HTTP k autorizaci přístupu k webovým aplikacím a webovým rozhraním API ve vašem tenantovi pomocí služby Azure AD
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2019
ms.author: priyamo
ms.openlocfilehash: b6dd4cd55755ae2c92afd327ad72ffe6966b9a07
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121518"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Registrace aplikace pomocí tenanta AD
Nejprve budete muset zaregistrovat aplikaci v tenantu Azure Active Directory (Azure AD). Pro svou aplikaci tak získáte ID a umožníte jí přijímat tokeny.

* Přihlaste se k webu [Azure Portal](https://portal.azure.com).
* Kliknutím na váš účet v pravém horním rohu stránky, a potom kliknutím na zvolte tenanta Azure AD **přepnout adresář** navigace a pak vyberte příslušný tenant. 
  * Tento krok přeskočte, pokud v rámci účtu máte jenom jednoho tenanta Azure AD nebo pokud jste už odpovídajícího tenanta Azure AD vybrali.
* V levém navigačním podokně klikněte na **Azure Active Directory**.
* Klikněte na **registrace aplikací** a klikněte na **registrace nové**.
* Postupujte podle zobrazených výzev a vytvořte novou aplikaci. To není důležité, jestli je webová aplikace nebo aplikace veřejným klientem (mobilních a desktopových) pro účely tohoto kurzu, ale pokud chcete konkrétní příklady webových nebo veřejné klientské aplikace, prohlédněte si naše [rychlých startů](../articles/active-directory/develop/v1-overview.md).
  * **Název** je název aplikace, který aplikaci popisuje koncovým uživatelům.
  * V části **podporovaných typů účtu**vyberte **účty v jakékoli organizaci adresáři a osobní účty Microsoft**.
  * Zadejte **identifikátor URI pro přesměrování**. Pro webové aplikace Toto je základní adresa URL aplikace ve kterém se uživatel může přihlásit.  Například, `http://localhost:12345`. Pro veřejným klientem (mobilních a desktopových) Azure AD použije k vrácení odpovědí týkajících se tokenu. Zadejte hodnotu specifickou pro vaši aplikaci.  Například, `http://MyFirstAADApp`.
    <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
* Po dokončení registrace Azure AD vaší aplikaci přiřadí jedinečný klientský identifikátor ( **ID aplikace**). Tuto hodnotu budete potřebovat v dalších částech, proto zkopírujte ho ze stránky aplikace.
* K vyhledání aplikace na webu Azure Portal, klikněte na tlačítko **registrace aplikací**a potom klikněte na tlačítko **zobrazit všechny aplikace**.
