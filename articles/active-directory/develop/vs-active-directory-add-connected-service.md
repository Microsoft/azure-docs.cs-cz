---
title: Přidání služby Azure Active Directory pomocí připojené služby v sadě Visual Studio
description: Přidat službu Azure Active Directory s použitím v dialogovém okně Visual Studio přidání připojené služby
services: active-directory
author: ghogen
manager: douge
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbbef13d2e64a5d1b177d8232c5b6ef1e64a1da9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56179140"
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Přidání služby Azure Active Directory pomocí připojené služby v sadě Visual Studio

Pomocí Azure Active Directory (Azure AD), může podporovat jednotné přihlašování (SSO) pro webové aplikace ASP.NET MVC nebo ověřování Active Directory služby webového rozhraní API. Ověřování Azure AD uživatelé pomocí svých účtů ze služby Azure Active Directory pro připojení k webovým aplikacím. Výhody ověřování Azure AD s webovým rozhraním API například dokonalejší zabezpečení dat při zpřístupňování rozhraní API z webové aplikace. S Azure AD není nutné spravovat oddělený ověřovací systém s vlastní správou účtů a uživatelů.

V tomto článku a jeho doprovodné články poskytují podrobné informace o použití funkce připojené služby sady Visual Studio pro službu Active Directory. Možnost je k dispozici v sadě Visual Studio 2017 a Visual Studio 2015.

V současné době nepodporuje službu Active Directory, které jsou připojené aplikace ASP.NET Core.

## <a name="prerequisites"></a>Požadavky

- Účet Azure: Pokud nemáte účet Azure, můžete si [zaregistrujte si bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) nebo [aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
- **Visual Studio 2015** nebo novější. [Stáhněte si Visual Studio 2017](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Připojení k Azure Active Directory pomocí dialogového okna připojené služby

1. V sadě Visual Studio vytvoření nebo otevření projektu aplikace ASP.NET MVC nebo projektu aplikace ASP.NET Web API. Můžete použít MVC, webové rozhraní API, jednostránková aplikace, aplikace API Azure, mobilní aplikace Azure a šablony služeb Azure Mobile.

1. Vyberte **Projekt > Přidat připojenou službu...**  příkaz nabídky nebo dvojím kliknutím **připojené služby** uzel nalezen v rámci projektu v Průzkumníku řešení.

1. Na **připojené služby** stránce **ověřování pomocí Azure Active Directory**.

    ![Stránka připojené služby](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. Na **ÚVOD** stránce **Další**. Pokud se na této stránce se zobrazí chyby, přečtěte si [Diagnostika chyb ve službě Azure Active Directory připojené Service](vs-active-directory-error.md).

    ![Úvodní stránka](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Na **Single Sign On** stránky, vyberte doménu z **domény** rozevíracího seznamu. Seznam obsahuje všechny domény, které jsou přístupné účty uvedené v dialogovém okně Nastavení účtu sady Visual Studio (**soubor > Nastavení účtu...** ). Jako alternativu můžete zadat název domény, Pokud nenajdete ta, kterou hledáte, jako například `mydomain.onmicrosoft.com`. Můžete zvolit možnost vytvoření aplikace Azure Active Directory nebo použít nastavení z existující aplikace Azure Active Directory. Vyberte **Další** až budete hotovi.

    ![Jednotné přihlašování na stránce](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. Na **přístup k adresáři** stránky, vyberte **čtení dat adresáře** možnost podle potřeby. Vývojáři obvykle zahrnout tuto možnost.

    ![Stránka adresáře přístup](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Vyberte **Dokončit** zahájíte úpravy do projektu, aby umožňoval ověřování Azure AD. Visual Studio zobrazuje průběh během této doby:

    ![Servisním připojené služby Active Directory](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. Po dokončení procesu Visual Studio otevře prohlížeč na jednu z následujících článků, v závislosti na typu vašeho projektu:

    - [Začínáme s projekty .NET MVC](vs-active-directory-dotnet-getting-started.md)
    - [Začínáme s projekty WebAPI](vs-active-directory-webapi-getting-started.md)

1. Domény služby Active Directory můžete zobrazit také na [webu Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

## <a name="how-your-project-is-modified"></a>Jak se váš projekt změnil

Když přidáte připojenou službu v průvodci, Visual Studio přidá Azure Active Directory a přidružené odkazy do projektu. Přidání podpory pro Azure AD jsou také upravit konfigurační soubory a soubory kódu ve vašem projektu. Konkrétní změny, které vytvoří malou Visual Studio závisí na typu projektu. Zobrazit podrobnosti v následujících článcích:

- [Co se stalo s mým projektem .NET MVC?](vs-active-directory-dotnet-what-happened.md)
- [Co se stalo s mým projektem webového rozhraní API?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Další postup

- [Scénáře ověřování pro Azure Active Directory](authentication-scenarios.md)
- [Přidání přihlašování s Microsoftem do webové aplikace ASP.NET](quickstart-v1-aspnet-webapp.md)
