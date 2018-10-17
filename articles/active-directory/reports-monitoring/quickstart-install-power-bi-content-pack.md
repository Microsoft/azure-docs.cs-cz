---
title: Instalace balíčku obsahu Azure AD Power BI | Microsoft Docs
description: Zjistěte, jak nainstalovat balíček obsahu Azure AD Power BI
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: fd5604eb-1334-4bd8-bfb5-41280883e2b5
ms.service: active-directory
ms.workload: identity
ms.component: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/23/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ba2784663a585aebb82ee149be3b54e73920f6dd
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816646"
---
# <a name="quickstart-install-azure-active-directory-power-bi-content-pack"></a>Rychlý start: Instalace balíčku obsahu Azure Active Directory Power BI

|  |
|--|
|V současné době balíček obsahu Azure AD Power BI používá rozhraní Azure AD Graph API k načtení dat z vašeho tenanta Azure AD. V důsledku toho můžete zaznamenat určitý nesoulad mezi daty, která jsou k dispozici v balíčku obsahu, a daty načtenými pomocí [rozhraní Microsoft Graph API pro generování sestav](concept-reporting-api.md). |
|  |

Balíček obsahu Power BI pro Azure Active Directory vám umožňuje získat podrobný přehled o tom, co se děje v rámci vašeho adresáře Active Directory. Můžete stáhnout předem sestavený balíček obsahu a použít ho k vykazování všech aktivit v rámci vašeho adresáře s použitím bohatého prostředí vizualizací, které nabízí služba Power BI. Můžete si vytvořit vlastní řídicí panel a snadno ho sdílet s kýmkoli ve vaší organizaci. 

V tomto rychlém startu se dozvíte, jak tento balíček obsahu nainstalovat.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

* Účet Power BI. Jde o stejný účet, jako je váš účet O365 nebo Azure AD. 
* ID vašeho tenanta Azure AD. Toto je **ID adresáře** vašeho adresáře, které najdete na [stránce vlastností](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) na webu Azure Portal.
* Licence Azure AD Premium (P1/P2). 

## <a name="install-azure-ad-power-bi-content-pack"></a>Instalace balíčku obsahu Power BI služby Azure AD 

1. Přihlaste se do služby [Power BI](https://app.powerbi.com/groups/me/getdata/services) pomocí svého účtu Power BI. Jde o stejný účet, jako je váš účet O365 nebo Azure AD.

2. Vyhledejte **Azure Active Directory Activity Logs** na stránce **Aplikace** a vyberte **Získat**. 

   ![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/quickstart-install-power-bi-content-pack/getitnow.png) 
    
3. V automaticky otevřeném okně zadejte ID tenanta služby Azure AD, jako počet dnů pro dotaz zadejte **7** a pak vyberte **Další**.
    
   ![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/quickstart-install-power-bi-content-pack/connect.png) 

4. Jakmile bude řídicí panel protokolů aktivity Azure Active Directory vytvořen, vyberte ho.

   ![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/quickstart-install-power-bi-content-pack/dashboard.png) 
    
## <a name="next-steps"></a>Další kroky

* [Použití balíčků obsahu Power BI](howto-power-bi-content-pack.md).
* [Řešení potíží s chybami balíčku obsahu](troubleshoot-content-pack.md).
