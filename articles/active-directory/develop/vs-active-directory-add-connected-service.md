---
title: Použití připojené služby Active Directory (Visual Studio)
description: Přidání služby Azure Active Directory pomocí dialogového okna Přidat připojené služby v sadě Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.custom: aaddev, vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.openlocfilehash: b3d068a8100cf78ccffac6d537fe71942239f383
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886156"
---
# <a name="add-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Přidání služby Azure Active Directory pomocí připojených služeb v sadě Visual Studio

Pomocí Služby Azure Active Directory (Azure AD) můžete podporovat jednotné přihlašování (SSO) pro ASP.NET webových aplikací MVC nebo ověřování služby Active Directory ve službách webového rozhraní API. Pomocí ověřování Azure AD můžou vaši uživatelé používat své účty z Azure Active Directory k připojení k webovým aplikacím. Mezi výhody ověřování Azure AD s webovým rozhraním API patří rozšířené zabezpečení dat při vystavení rozhraní API z webové aplikace. S Azure AD, není třeba spravovat samostatný ověřovací systém s vlastním účtem a správu uživatelů.

Tento článek a jeho doprovodné články obsahují podrobnosti o použití funkce připojení k aplikaci Visual Studio pro službu Active Directory. Tato funkce je k dispozici v sadě Visual Studio 2015 a novější.

V současné době služba připojená ke službě Active Directory nepodporuje aplikace ASP.NET Core.

## <a name="prerequisites"></a>Požadavky

- Účet Azure: Pokud nemáte účet Azure, můžete [si zaregistrovat bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) nebo [aktivovat výhody předplatitele Visual Studia](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
- **Visual Studio 2015** nebo novější. [Stáhnout Visual Studio nyní](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Připojení ke službě Azure Active Directory pomocí dialogového okna Připojené služby

1. V sadě Visual Studio vytvořte nebo otevřete ASP.NET projektu MVC nebo projektu ASP.NET webového rozhraní API. Můžete použít šablony MVC, Webové rozhraní API, Jednostránková aplikace, Aplikace Azure API, Mobilní aplikace Azure a Azure Mobile Service.

1. Vyberte **příkaz nabídky Přidat připojenou službu aplikace Project >...** nebo poklepejte na uzel Připojené **služby,** který se nachází pod projektem v Průzkumníku řešení.

1. Na stránce **Připojené služby** vyberte **Ověřování pomocí služby Azure Active Directory**.

    ![Stránka Připojené služby](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. Na stránce **Introduction** vyberte **Další**. Pokud se na této stránce zobrazují [chyby, přečtěte si téma Diagnostika chyb pomocí služby Azure Active Directory Connected Service](vs-active-directory-error.md).

    ![Úvodní stránka](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Na stránce **Jednotné přihlášení** vyberte doménu z rozevíracího seznamu **Doména.** Seznam obsahuje všechny domény přístupné účtům uvedeným v dialogovém okně Nastavení účtu v sadě Visual Studio **(Nastavení > účtu souboru...**). Jako alternativu můžete zadat název domény, pokud nenajdete ten, který hledáte, například `mydomain.onmicrosoft.com`. Můžete zvolit možnost vytvoření aplikace Azure Active Directory nebo použít nastavení z existující aplikace Azure Active Directory. Po dokončení vyberte **Další.**

    ![Jednotné přihlašování na stránce](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. Na stránce **Přístup k adresáři** vyberte podle potřeby možnost **Číst data adresáře.** Vývojáři obvykle zahrnují tuto možnost.

    ![Přístupová stránka adresáře](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Vyberte **Dokončit** a spusťte změny projektu, abyste povolili ověřování Azure AD. Visual Studio zobrazuje průběh během této doby:

    ![Průběh připojené služby služby Active Directory](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. Po dokončení procesu Visual Studio otevře prohlížeč do jednoho z následujících článků, podle typu projektu:

    - [Začínáme s projekty .NET MVC](vs-active-directory-dotnet-getting-started.md)
    - [Začínáme s projekty WebAPI](vs-active-directory-webapi-getting-started.md)

1. Doménu Služby Active Directory najdete také na [webu Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

## <a name="how-your-project-is-modified"></a>Jak se váš projekt mění

Když přidáte připojenou službu průvodce, Visual Studio přidá Azure Active Directory a přidružené odkazy na váš projekt. Konfigurační soubory a soubory kódu ve vašem projektu jsou také upraveny tak, aby přidat podporu pro Azure AD. Konkrétní změny, které visual studio provede závisí na typu projektu. Podrobnosti naleznete v následujících článcích:

- [Co se stalo s mým projektem .NET MVC?](vs-active-directory-dotnet-what-happened.md)
- [Co se stalo s mým projektem webového rozhraní API?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Další kroky

- [Scénáře ověřování pro Službu Azure Active Directory](authentication-scenarios.md)
- [Přidání přihlášení s Microsoftem do ASP.NET webové aplikace](quickstart-v2-aspnet-webapp.md)
