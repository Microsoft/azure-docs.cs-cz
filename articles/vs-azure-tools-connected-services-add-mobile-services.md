---
title: Přidání Mobile Services pomocí připojené služby v sadě Visual Studio | Dokumentace Microsoftu
description: Přidání Mobile Services pomocí dialogovém okně Visual Studio přidání připojené služby
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
ms.custom: vs-azure
ms.date: 12/16/2015
ms.author: mlearned
ms.openlocfilehash: a1b2524b059601e1f6f999af7a9d9e063f7c0420
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233442"
---
# <a name="adding-mobile-services-by-using-visual-studio-connected-services"></a>Přidání Mobile Services pomocí připojených služeb sady Visual Studio
Pomocí sady Visual Studio 2015, můžete připojit pomocí Azure Mobile Services **přidat připojenou službu** dialogového okna. Můžete připojit z jakékoli klientské aplikace C#, všechny Javascriptové aplikace nebo aplikace Cordova pro různé platformy. Když se připojíte, můžete vytvořit a přístup k datům, vytvořte vlastní rozhraní API a naplánované úlohy nebo přidat podporu pro nabízená oznámení.  Operace připojené služby přidá všechny příslušné odkazy a připojovací kód. Můžete také využít výhody integrované podpory pro ověřování s řadou oblíbených schémat identity, jako je Azure AD, Facebook, Twitter a Microsoft Accounts.

## <a name="supported-project-types"></a>Podporované typy projektů
> [!NOTE]
> V sadě Visual Studio 2015 není podporováno přidávání pomocí dialogového okna připojené služby Azure Mobile Services pro projekty Windows Universal (Windows 10). Azure Mobile Services můžete přidat nainstalováním vhodné balíčky pomocí Správce balíčků NuGet pro projekt.
> 
> 

Dialogové okno připojené služby můžete použít pro připojení k Azure Mobile Services v následujících typech projektů.

* Projekty .NET Windows 8.1 Store, Telefon a univerzální aplikace
* Projekty jazyka JavaScript Windows 8.1 Store, Telefon a univerzální aplikace pro
* Projekty vytvořené pomocí nástrojů Visual Studio pro Apache Cordova

## <a name="connect-to-azure-mobile-services-using-the-add-connected-services-dialog"></a>Připojení k mobilním službám Azure pomocí dialogového okna připojené služby
1. Ujistěte se, že máte účet Azure. Pokud účet Azure nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](http://go.microsoft.com/fwlink/?LinkId=518146).
2. Otevřít **přidání připojené služby** dialogové okno.
   
   * Pro aplikace .NET, otevřete projekt v sadě Visual Studio, otevřete kontextovou nabídku **odkazy** uzel v Průzkumníku řešení a klikněte na tlačítko **přidat připojenou službu**
     
        ![Připojení k mobilní služby Azure](./media/vs-azure-tools-connected-services-add-mobile-services/IC797635.png)
   * Pro projekty aplikací pro Apache Cordova, otevřete projekt v sadě Visual Studio, otevřete místní nabídku pro uzel projektu v Průzkumníku řešení a klikněte na tlačítko **přidat připojenou službu**.
3. V **přidat připojenou službu** dialogového okna zvolte **Azure Mobile Services**a klikněte na tlačítko **konfigurovat** tlačítko. Můžete být vyzváni k přihlášení do Azure, pokud jste tak již neučinili.
   
    ![Přidání mobilní služby Azure](./media/vs-azure-tools-connected-services-add-mobile-services/IC797636.png)
4. V **Azure Mobile Services** dialogového okna zvolte existující mobilní služby, pokud nemáte. Pokud potřebujete k vytvoření nové mobilní služby Azure, postupujte podle tohoto postupu provedete to tak. Jinak přejděte k dalšímu kroku.
   
    Chcete-li vytvořit nový účet mobilních služeb:
   
   1. Zvolte ** vytvořit službu ** odkaz v dolní části dialogového okna.
       ![Přidat nové mobilní připojené služby](./media/vs-azure-tools-connected-services-add-mobile-services/IC797637.png)
   2. Na **vytvořit mobilní službu** dialogovém okně můžete vybrat mobilní služby back-endu JavaScriptu nebo .NET back-end mobilních služeb z **Runtime** rozevíracího seznamu. 
      
       ![Vytvoření mobilní služby](./media/vs-azure-tools-connected-services-add-mobile-services/IC797638.png)
      
       Back-end službu jazyka JavaScript je jednoduché a účinné. Pokud vytvoříte mobilní službu back-end JavaScript, kód jazyka JavaScript na straně serveru je uložen v cloudu, ale serverových skriptů můžete upravit pomocí Průzkumníku serveru nebo portálu pro správu Azure. 
      
       Mobilní služby back-end .NET poskytuje úplné výkonná a flexibilní webové rozhraní API a Entity Framework. Pokud vytvoříte mobilní službu back-endu .NET, projekt vytvořen a přidán do řešení. 
   3. Zvolte **oblasti** kde chcete na mobilní službu a pak zadejte uživatelské jméno a heslo pro server.
   4. Až zadáte všechny požadované informace, zvolte **vytvořit** tlačítko vytvoříte mobilní službu.
   5. Nové mobilní služby by se měla zobrazit v seznamu služeb na **Azure Mobile Services** dialogové okno. V seznamu vyberte novou mobilní službu a pak zvolte **přidat** tlačítko pro přidání služby do projektu.
5. Zkontrolujte na stránce Začínáme, který se zobrazí a zjistěte, jak se váš projekt změnil. Pokaždé, když přidáte připojenou službu ve vašem prohlížeči zobrazí stránku Začínáme. Můžete zkontrolovat další navrhované kroky a příklady kódu, nebo přepněte na stránku co se stalo a podívejte se, jaké odkazy byly přidány do projektu a jak váš kód a konfigurační soubory byly změněny.
6. Pomocí ukázky kódu a jako vodítko, začněte psát kód pro přístup k mobilní službě.

## <a name="how-your-project-is-modified"></a>Jak se váš projekt změnil
Jak Visual Studio změní projekt, závisí na typu projektu. Pro C# klientských aplikací, najdete v článku [co se stalo – C# projekty](http://go.microsoft.com/fwlink/p/?LinkId=513119). JavaScript klientských aplikací, najdete v části [co se stalo – projekty jazyka JavaScript](http://go.microsoft.com/fwlink/p/?LinkId=513120). Aplikace Cordova, naleznete v tématu [co se stalo – projekty Cordova](http://go.microsoft.com/fwlink/p/?LinkId=513116).

## <a name="next-steps"></a>Další postup
Klást otázky a nechat si pomoct: 

* [Fórum na webu MSDN: Azure Mobile Services](https://social.msdn.microsoft.com/forums/azure/home?forum=azuremobile)
* [Služba Azure Mobile Services na Blog týmu Microsoft Azure](https://azure.microsoft.com/blog/topics/mobile/)
* [Azure Mobile Services na azure.microsoft.com](https://azure.microsoft.com/services/mobile-services/)
* [Dokumentace ke službě Azure Mobile Services na azure.microsoft.com](https://azure.microsoft.com/documentation/services/mobile-services/)

