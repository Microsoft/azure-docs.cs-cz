---
title: "Přidání Azure Active Directory pomocí připojené služby v sadě Visual Studio | Microsoft Docs"
description: "Přidat Azure Active Directory pomocí dialogu Visual Studio přidat připojení služby"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.service: active-directory
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: kraigb
ms.openlocfilehash: a767c93fb271f3aa33d9556c69c511bcac7cb0d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Přidání Azure Active Directory pomocí připojené služby v sadě Visual Studio
Pomocí služby Azure Active Directory (Azure AD), může podporovat jednotné přihlašování (SSO) pro webové aplikace ASP.NET MVC nebo ověřování Active Directory v webového rozhraní API služby. S Azure Active Directory Authentication uživatelům jejich účty ze služby Azure Active Directory použít k připojení k webovým aplikacím. Výhody ověřování Azure Active Directory pomocí webového rozhraní API obsahují rozšířené zabezpečení při vystavení rozhraní API z webové aplikace. S Azure AD není nutné spravovat samostatného ověření systému se správou svůj vlastní účet a uživatele.

## <a name="prerequisites"></a>Požadavky
- Účet Azure – Pokud nemáte účet Azure, můžete [zaregistrovat k bezplatné zkušební verzi](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) nebo [aktivovat výhody předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Připojení k Azure Active Directory v dialogovém okně připojení služby
1. V sadě Visual Studio vytvoření nebo otevření projektu aplikace ASP.NET MVC nebo projekt webového rozhraní API ASP.NET.

1. V Průzkumníku řešení klikněte pravým tlačítkem **připojené služby** uzel a v místní nabídce vyberte **přidat připojení služby**.

1. Na **připojené služby** vyberte **ověřování s Azure Active Directory**.
   
    ![Připojená stránka služby](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. Na **ÚVOD** stránky **konfigurovat Azure AD Authentication** průvodci vyberte **Další**.
   
    ![Úvodní stránka](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1. Na **jednotné přihlašování na** stránky **konfigurovat Azure AD Authentication** průvodce, vyberte doménu z **domény** rozevíracího seznamu. Seznam domén obsahuje všechny domény, které jsou dostupné účty uvedené v dialogovém okně Nastavení účtu. Jako alternativu, můžete zadat název domény, pokud zde nejsou tu, kterou hledáte, jako například `mydomain.onmicrosoft.com`. Můžete vytvořit aplikaci Azure Active Directory nebo používat nastavení z existující aplikaci Azure Active Directory. Vyberte **Další** po dokončení.
   
    ![Jednotné přihlašování na stránce](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)

1. Na **přístup k adresářové** stránky **konfigurovat Azure AD Authentication** průvodce, ujistěte se, že **čtení dat adresáře** zaškrtnutá možnost. 
   
    ![Stránka adresáře](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. Vyberte **Dokončit** přidejte kód nezbytné konfigurace a odkazy na povolit svůj projekt pro ověřování Azure AD. Domény služby Active Directory můžete zobrazit na [portál Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Visual Studio se zobrazí [co se stalo](#how-your-project-is-modified) článku ukazují, jak byla změněna projektu. Pokud chcete zkontrolovat, zda vše funguje, otevřete ho upravený konfigurační soubory a ověřte, že nastavení uvedených v článku existují. 

## <a name="how-your-project-is-modified"></a>Jak se mění projektu
Když spustíte průvodce, Visual Studio přidá Azure Active Directory a přidružené odkazy na projekt. Přidání podpory pro Azure AD jsou také upravit konfigurační soubory a soubory kódu v projektu. Určité změny, které sada Visual Studio provádí závisí na typu projektu. Podrobné informace o tom, jak jsou upraveny projekty ASP.NET MVC najdete v tématu [jaké projekty MVC happened –](http://go.microsoft.com/fwlink/p/?LinkID=513809). Projekty webového rozhraní API, najdete v části [co se stalo – projekty webového rozhraní API](http://go.microsoft.com/fwlink/p/?LinkId=513810).

## <a name="next-steps"></a>Další kroky
* [Fórum MSDN pro Azure Active Directory](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)
* [Dokumentaci ke službě Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Příspěvek blogu: Úvod do služby Azure Active Directory](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

