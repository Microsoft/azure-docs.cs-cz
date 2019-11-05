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
ms.openlocfilehash: 7772b3ee5d0e27c09e83f7d118eb9f67f17e0d07
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522540"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Registrace aplikace pomocí tenanta AD
Nejdřív svou aplikaci zaregistrujete u svého tenanta Azure Active Directory (Azure AD). Pro svou aplikaci tak získáte ID a umožníte jí přijímat tokeny.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
   
1. Zvolte svého tenanta Azure AD tak, že vyberete svůj účet v pravém horním rohu stránky a potom vyberete možnost navigace v **adresáři přepínače** a pak vyberete příslušného tenanta. 
   - Tento krok přeskočte, pokud máte ve svém účtu jenom jednoho tenanta Azure AD, nebo pokud jste už vybrali příslušného tenanta Azure AD.
   
1. V Azure Portal vyhledejte a vyberte **Azure Active Directory**.
   
1. V nabídce **Azure Active Directory** vlevo vyberte **Registrace aplikací**a pak vyberte **Nová registrace**.
   
1. Postupujte podle zobrazených výzev a vytvořte novou aplikaci. Nezáleží na tom, jestli se jedná o aplikaci webové aplikace nebo veřejného klienta (mobilní & Desktop) pro tento kurz, ale pokud byste chtěli mít konkrétní příklady pro webové aplikace nebo veřejné klientské aplikace, podívejte se na naše [rychlé starty](../articles/active-directory/develop/v1-overview.md).
   
   - **Název** je název aplikace, který aplikaci popisuje koncovým uživatelům.
   - V části **podporované typy účtů**vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft**.
   - Zadejte **identifikátor URI pro přesměrování**. U webových aplikací se jedná o základní adresu URL vaší aplikace, kde se uživatelé můžou přihlásit.  například `http://localhost:12345`. U veřejného klienta (Mobile & Desktop) Azure AD používá k vracení odpovědí na tokeny. Zadejte hodnotu specifickou pro vaši aplikaci.  například `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Po dokončení registrace vám Azure AD přiřadí aplikaci jedinečný identifikátor klienta ( **ID aplikace**). Tuto hodnotu budete potřebovat v dalších částech, proto je zkopírujte ze stránky aplikace.
   
1. Chcete-li najít aplikaci v Azure Portal, vyberte možnost **Registrace aplikací**a pak vyberte možnost **Zobrazit všechny aplikace**.
