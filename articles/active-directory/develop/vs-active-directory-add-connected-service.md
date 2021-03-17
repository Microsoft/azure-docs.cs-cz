---
title: Používání služby Active Directory Connected (Visual Studio)
description: Přidání Azure Active Directory pomocí dialogového okna Přidat připojené služby v aplikaci Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.openlocfilehash: a1ba7db72743ac122a697bf271e783ec64e041e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88165477"
---
# <a name="add-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Přidání Azure Active Directory pomocí připojených služeb v aplikaci Visual Studio

Pomocí Azure Active Directory (Azure AD) můžete podporovat jednotné Sign-On (SSO) pro webové aplikace ASP.NET MVC nebo ověřování služby Active Directory ve službě webového rozhraní API. Pomocí ověřování Azure AD můžou vaši uživatelé používat svoje účty z Azure Active Directory pro připojení k vašim webovým aplikacím. Výhody ověřování Azure AD s webovým rozhraním API zahrnují vylepšené zabezpečení dat při vystavení rozhraní API z webové aplikace. S Azure AD nemusíte spravovat samostatný ověřovací systém s vlastním účtem a správou uživatelů.

Tento článek a jeho doprovodné články obsahují podrobné informace o používání funkce připojené služby sady Visual Studio pro službu Active Directory. Tato funkce je k dispozici v aplikaci Visual Studio 2015 nebo novější.

Služba Active Directory Connected v současné době nepodporuje ASP.NET Core aplikace.

## <a name="prerequisites"></a>Požadavky

- Účet Azure: Pokud ještě nemáte účet Azure, můžete si [zaregistrovat bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) nebo [aktivovat výhody pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
- **Visual Studio 2015** nebo novější. [Stáhněte si Visual Studio hned teď](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Připojení k Azure Active Directory pomocí dialogového okna připojené služby

1. V aplikaci Visual Studio vytvořte nebo otevřete projekt ASP.NET MVC nebo projekt webového rozhraní API ASP.NET. Můžete použít MVC, webové rozhraní API, Single-Page aplikaci, aplikace API Azure, mobilní aplikace Azure a šablony mobilních služeb Azure.

1. Vyberte **projekt > přidat připojenou službu...** nebo poklikejte na uzel **připojené služby** , který se nachází v projektu v Průzkumník řešení.

1. Na stránce **připojené služby** vyberte **ověřování pomocí Azure Active Directory**.

    ![Stránka připojené služby](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. Na stránce **Úvod** vyberte **Další**. Pokud se na této stránce zobrazí chyby, podívejte se na [diagnostiku chyb v připojené službě Azure Active Directory](vs-active-directory-error.md).

    ![Úvodní stránka](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Na stránce **jednotného přihlašování** vyberte v rozevíracím seznamu **doména** doménu. Seznam obsahuje všechny domény, které jsou přístupné z účtů uvedených v dialogovém okně nastavení účtu v aplikaci Visual Studio (**Nastavení účtu souborového >...**). Jako alternativu můžete zadat název domény, pokud nenajdete tu, kterou hledáte, například `mydomain.onmicrosoft.com` . Můžete zvolit možnost vytvoření aplikace Azure Active Directory nebo použít nastavení z existující aplikace Azure Active Directory. Po dokončení vyberte **Další** .

    ![Stránka jednotného přihlašování](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. Na stránce **přístup k adresáři** vyberte možnost **číst data adresáře** podle potřeby. Tato možnost obvykle zahrnuje vývojáři.

    ![Stránka přístup k adresáři](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Výběrem **Finish (Dokončit** ) spustíte změny projektu, aby se povolilo ověřování Azure AD. Visual Studio během této doby zobrazuje průběh:

    ![Průběh připojené služby Active Directory](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. Po dokončení procesu Visual Studio otevře prohlížeč v jednom z následujících článků, jak je to vhodné pro váš typ projektu:

    - [Začínáme s projekty .NET MVC](vs-active-directory-dotnet-getting-started.md)
    - [Začínáme s projekty WebAPI](vs-active-directory-webapi-getting-started.md)

1. Doménu služby Active Directory můžete zobrazit také na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

## <a name="how-your-project-is-modified"></a>Způsob úpravy projektu

Když přidáte připojenou službu průvodce, Visual Studio přidá Azure Active Directory a přidružené odkazy na váš projekt. Konfigurační soubory a soubory kódu v projektu jsou také upraveny tak, aby přidaly podporu pro Azure AD. Konkrétní změny, které Visual Studio provede, závisí na typu projektu. Podrobnosti najdete v následujících článcích:

- [Co se stalo s mým projektem .NET MVC?](vs-active-directory-dotnet-what-happened.md)
- [Co se stalo s mým projektem webového rozhraní API?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Další kroky

- [Scénáře ověřování pro Azure Active Directory](./authentication-vs-authorization.md)
- [Přidání přihlašování do webové aplikace ASP.NET pomocí Microsoftu](quickstart-v2-aspnet-webapp.md)