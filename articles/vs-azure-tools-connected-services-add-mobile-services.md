---
title: Přidání Mobile Services pomocí připojené služby v sadě Visual Studio | Microsoft Docs
description: Přidání Mobile Services pomocí dialogu Visual Studio přidat připojení služby
services: visual-studio-online
documentationcenter: na
author: mlhoop
manager: douge
editor: ''
ms.assetid: 75c3cb93-88e1-476d-a416-f34caa3608e3
ms.service: visual-studio-online
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: mobile
ms.date: 12/16/2015
ms.author: mlearned
ms.openlocfilehash: d185fdafebad56f8970e390b2a0672c3fb84df8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23884213"
---
# <a name="adding-mobile-services-by-using-visual-studio-connected-services"></a>Přidání Mobile Services pomocí Visual Studio připojené Services
Pomocí sady Visual Studio 2015, můžete připojit pomocí Azure Mobile Services **přidat připojení službě** dialogové okno. Můžete připojit z každou klientskou aplikaci C#, žádné aplikace JavaScript nebo aplikace Cordova napříč platformami. Jakmile se připojíte, můžete vytvořit a přístup k datům, vytvořte vlastní rozhraní API a naplánované úlohy nebo přidat podporu pro nabízená oznámení.  Operaci připojených služeb přidá všechny příslušné odkazy a kód připojení. Můžete také využít výhod integrovanou podporu pro ověřování s různými schémata oblíbených identity, jako je Azure AD, Facebook, Twitter a Accounts Microsoft.

## <a name="supported-project-types"></a>Typy podporované projektů
> [!NOTE]
> Přidávání Azure Mobile Services do univerzální pro Windows (Windows 10) projektů pomocí dialogu přidat připojení služby není podporováno v sadě Visual Studio 2015. Azure Mobile Services můžete přidat nainstalováním vhodné balíčky pomocí Správce balíčků NuGet pro projekt.
> 
> 

Dialogové okno připojení služby můžete použít pro připojení k Azure Mobile Services v následující typy projektů.

* Rozhraní .NET Windows 8.1 Store, Phone a univerzální aplikace pro projekty
* JavaScript Windows 8.1 Store, Phone a univerzální aplikace pro projekty
* Projekty vytvořené pomocí nástroje sady Visual Studio pro Apache Cordova

## <a name="connect-to-azure-mobile-services-using-the-add-connected-services-dialog"></a>Připojení k Azure Mobile Services pomocí dialogu přidat připojení služby
1. Ujistěte se, že máte účet Azure. Pokud účet Azure nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](http://go.microsoft.com/fwlink/?LinkId=518146).
2. Otevřete **přidat připojení služby** dialogové okno.
   
   * Pro aplikace .NET, otevřete projekt v sadě Visual Studio, otevřete kontextovou nabídku **odkazy** uzlu v Průzkumníku řešení a potom zvolte **přidat připojení služby**
     
        ![Připojení k mobilní služby Azure](./media/vs-azure-tools-connected-services-add-mobile-services/IC797635.png)
   * Pro projekty aplikace Apache Cordova, otevřete projekt v sadě Visual Studio, otevřete v místní nabídce uzlu projektu v Průzkumníku řešení a zvolte **přidat připojení službě**.
3. V **přidat připojení službě** dialogovém okně vyberte **Azure Mobile Services**a potom zvolte **konfigurace** tlačítko. Můžete být vyzváni k přihlášení do Azure, pokud jste tak již neučinili.
   
    ![Přidání mobilní služby Azure](./media/vs-azure-tools-connected-services-add-mobile-services/IC797636.png)
4. V **Azure Mobile Services** dialogovém okně vyberte stávající mobilní službu, pokud nemáte. Pokud potřebujete k vytvoření nové mobilní služby Azure, pomocí následujícího postupu Uděláte to tak. Jinak přejděte k dalšímu kroku.
   
    Chcete-li vytvořit nový účet mobilní služby:
   
   1. Vyberte ** vytvořit službu ** odkaz ve spodní části dialogových oken.
       ![Přidejte novou mobilní službu připojené](./media/vs-azure-tools-connected-services-add-mobile-services/IC797637.png)
   2. Na **vytvoření mobilní služby** dialogové okno, můžete vybrat mobilní služby back-end JavaScript nebo rozhraní .NET back-end mobilní služby z **Runtime** rozevíracího seznamu. 
      
       ![Vytvoření mobilní služby](./media/vs-azure-tools-connected-services-add-mobile-services/IC797638.png)
      
       Služba back-end JavaScript je jednoduchá ale účinná. Pokud vytvoříte mobilní službu back-end JavaScript, kód JavaScript na straně serveru je uložená v cloudu, ale skripty serveru můžete upravit pomocí Průzkumníka serveru nebo portál pro správu Azure. 
      
       Rozhraní .NET back-end mobilní služby vám dává úplné výkon a flexibilitu webového rozhraní API a rozhraní Entity Framework. Pokud vytvoříte .NET back-end mobilní službu, projektu je vytvořená a přidat do vašeho řešení. 
   3. Vyberte **oblast** kde chcete na mobilní službu a pak zadejte uživatelské jméno a heslo pro server.
   4. Po zadání všech požadovaných informací, vyberte **vytvořit** tlačítko pro vytvoření mobilní služby.
   5. Nové mobilní služby by se měla objevit v seznamu služeb na **Azure Mobile Services** dialogové okno. Zvolte nové mobilní služby v seznamu a pak **přidat** tlačítko přidáte službu do projektu.
5. Přečtěte si úvodní stránce zobrazeném a zjistěte, jak byla změněna projektu. Na stránce Začínáme se zobrazí v prohlížeči vždy při přidání připojené služby. Můžete zkontrolovat navrhované další kroky a příklady kódu nebo přepněte na stránku co se stalo zobrazíte odkazy, které byly přidány do projektu a jak byly upraveny váš kód a konfiguračních souborů.
6. Pomocí ukázky kódu jako vodítko, začněte psaní kódu pro přístup k mobilní službě.

## <a name="how-your-project-is-modified"></a>Jak se mění projektu
Jak upraví projektu v sadě Visual Studio, závisí na typu projektu. C# klientských aplikací, najdete v části [co se stalo – projekty c](http://go.microsoft.com/fwlink/p/?LinkId=513119). JavaScript klientských aplikací, najdete v části [co se stalo – projekty JavaScript](http://go.microsoft.com/fwlink/p/?LinkId=513120). Pro aplikace Cordova, najdete v části [co se stalo – projekty Cordova](http://go.microsoft.com/fwlink/p/?LinkId=513116).

## <a name="next-steps"></a>Další kroky
Klást otázky a získat pomoc: 

* [Fórum MSDN: Azure Mobile Services](https://social.msdn.microsoft.com/forums/azure/home?forum=azuremobile)
* [Azure Mobile Services na Blog týmu Microsoft Azure](https://azure.microsoft.com/blog/topics/mobile/)
* [Azure Mobile Services na webu azure.microsoft.com](https://azure.microsoft.com/services/mobile-services/)
* [Azure Mobile Services dokumentaci na webu azure.microsoft.com](https://azure.microsoft.com/documentation/services/mobile-services/)

