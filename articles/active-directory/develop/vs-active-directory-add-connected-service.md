---
title: Přidání Azure Active Directory pomocí připojené služby v sadě Visual Studio | Microsoft Docs
description: Přidat Azure Active Directory pomocí dialogu Visual Studio přidat připojení služby
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.service: active-directory
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2018
ms.author: ghogen
ms.openlocfilehash: 882ba1c7ea8ef6889bc9ad20031070cd54100026
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Přidání Azure Active Directory pomocí připojené služby v sadě Visual Studio

Pomocí služby Azure Active Directory (Azure AD), může podporovat jednotné přihlašování (SSO) pro webové aplikace ASP.NET MVC nebo ověřování Active Directory v webového rozhraní API služby. S Azure AD Authentication uživatelům jejich účty ze služby Azure Active Directory použít k připojení k webovým aplikacím. Výhody služby Azure AD Authentication pomocí webového rozhraní API zahrnují rozšířené zabezpečení při vystavení rozhraní API z webové aplikace. S Azure AD není nutné spravovat samostatného ověření systému se správou svůj vlastní účet a uživatele.

V tomto článku a jeho článcích doprovodné uveďte podrobné informace o použití funkce Visual Studio připojené služby pro službu Active Directory. Funkce je k dispozici v Visual Studio 2017 a Visual Studio 2015.

V současné době nepodporuje službu Active Directory, které jsou připojené aplikace ASP.NET Core.

## <a name="prerequisites"></a>Požadavky

- Účet Azure: Pokud nemáte účet Azure, můžete [zaregistrovat k bezplatné zkušební verzi](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) nebo [aktivovat výhody předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Připojení k Azure Active Directory v dialogovém okně připojení služby

1. V sadě Visual Studio vytvoření nebo otevření projektu aplikace ASP.NET MVC nebo projekt webového rozhraní API ASP.NET. Můžete použít MVC, webového rozhraní API, jedné stránky aplikace, aplikace Azure API, mobilní aplikace Azure a Azure Mobile Service šablony.

1. Vyberte **Projekt > Přidání připojené služby...**  příkaz nabídky, nebo klikněte dvakrát **připojené služby** uzlu byl nalezen v rámci projektu v Průzkumníku řešení.

1. Na **připojené služby** vyberte **ověřování s Azure Active Directory**.

    ![Připojená stránka služby](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. Na **ÚVOD** vyberte **Další**. Pokud se na této stránce se zobrazí chyby, přečtěte si [diagnostikování chyb pomocí služby Azure Active Directory připojené Service](vs-active-directory-error.md).

    ![Úvodní stránka](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Na **jednotné přihlašování na** vyberte k doméně z **domény** rozevíracího seznamu. Seznam obsahuje všechny domény, které jsou dostupné účty uvedené v dialogovém okně Nastavení účtu sady Visual Studio (**soubor > Nastavení účtu...** ). Jako alternativu, můžete zadat název domény, pokud zde nejsou tu, kterou hledáte, jako například `mydomain.onmicrosoft.com`. Můžete vytvořit aplikaci Azure Active Directory nebo používat nastavení z existující aplikaci Azure Active Directory. Vyberte **Další** po dokončení.

    ![Jednotné přihlašování na stránce](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. Na **přístup k adresářové** vyberte **čtení dat adresáře** možnost podle potřeby. Vývojáři obvykle obsahují tuto možnost.

    ![Stránka adresáře](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Vyberte **Dokončit** zahájíte změny do projektu povolení ověřování Azure AD. Visual Studio zobrazí průběh během této doby:

    ![Průběh služby Active Directory připojení](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. Po dokončení procesu Visual Studio otevře prohlížeč na jednu z následujících článků, v závislosti na typu vašeho projektu:

    - [Začínáme s projekty .NET MVC](vs-active-directory-dotnet-getting-started.md)
    - [Začínáme s projekty WebAPI](vs-active-directory-webapi-getting-started.md)

1. Domény služby Active Directory můžete také zjistit na [portál Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

## <a name="how-your-project-is-modified"></a>Jak se mění projektu

Při přidání připojené služby v průvodci, Visual Studio přidá Azure Active Directory a přidružené odkazy na projekt. Přidání podpory pro Azure AD jsou také upravit konfigurační soubory a soubory kódu v projektu. Určité změny, které sada Visual Studio provádí závisí na typu projektu. Najdete v následujících článcích podrobnosti:

- [Co se stalo s mým projektem .NET MVC?](vs-active-directory-dotnet-what-happened.md)
- [Co se stalo s Moje projekt webového rozhraní API?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Další postup

- [Scénáře ověřování pro Azure Active Directory](active-directory-authentication-scenarios.md)
- [Přidání přihlášení se společností Microsoft do webové aplikace ASP.NET](guidedsetups/active-directory-aspnetwebapp-v1.md)
