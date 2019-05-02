---
title: Průběžné nasazování pro službu Azure Functions | Dokumentace Microsoftu
description: Použití zařízení průběžného nasazování služby Azure App Service k publikování vašich funkcí.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: cb3f3ad3bb7b42429654ea4bf9b49f7e230db1da
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943887"
---
# <a name="continuous-deployment-for-azure-functions"></a>Průběžné nasazování se službou Azure Functions
Služba Azure Functions umožňuje snadno nasadit vaši aplikaci function app pomocí průběžné integrace. Functions se integruje s úložiště hlavní kódu a nasazení zdrojů. Tato integrace umožňuje pracovní postupy s kde kód funkce aktualizace provedené prostřednictvím jednoho z těchto služeb aktivační události nasazení do Azure. Pokud začínáte do služby Azure Functions, začněte tématem [přehled Azure Functions](functions-overview.md).

Průběžné nasazování je skvělá možnost pro projekty, kde máte integraci více a časté příspěvky. Můžete ho taky udržovat správy zdrojového kódu na kód vaší funkce. Služba Azure Functions podporuje následující zdroje nasazení:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* Externí úložiště (Git nebo Mercurial)
* [Místní úložiště Git](../app-service/deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Azure DevOps](https://azure.microsoft.com/services/devops/)

Nasazení se konfigurují na základě jednotlivých funkcích aplikace. Po povolení průběžného nasazování přístup ke kódu funkce na portálu je nastavena na *jen pro čtení*.

## <a name="requirements-for-continuous-deployment"></a>Požadavky pro průběžné nasazování

Před nastavením průběžného nasazování musí mít zdroj nasazení nakonfigurované a kód vaší funkce v zdroj nasazení. Při nasazení aplikací funkce je každá funkce v podadresáři s názvem, kde název adresáře je název funkce.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Aby bylo možné nasadit z Azure DevOps, musíte si nejdříve propojit organizaci Azure DevOps ve vašem předplatném Azure. Další informace najdete v tématu [nastavte fakturaci pro vaši organizaci Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs?view=vsts#set-up-billing-via-the-azure-portal).

## <a name="set-up-continuous-deployment"></a>Nastavení nepřetržitého nasazování
Pomocí tohoto postupu konfigurace průběžného nasazování pro existující aplikaci function app. Tyto kroky ukazují, integrace s úložišti GitHub, ale podobný postup použít pro Azure DevOps nebo jiné služby pro nasazení.

1. V aplikaci function app v [webu Azure portal](https://portal.azure.com)vyberte **funkce platformy** > **možnosti nasazení**. 
   
    ![Výběry pro otevření možnosti nasazení](./media/functions-continuous-deployment/setup-deployment.png)
 
1. Na **nasazení** okně vyberte **nastavení**.
 
    ![Okno nasazení](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. Na **zdroj nasazení** okně vyberte **zvolit zdroj**. Zadejte informace pro zdroj zvolené nasazení a pak vyberte **OK**.
   
    ![Výběr zdroj nasazení](./media/functions-continuous-deployment/choose-deployment-source.png)

Po nastavení průběžného nasazování, všechny změny souborů ve zdroji nasazení se zkopírují do aplikace function app a se aktivuje nasazení plnou verzi webu. Při aktualizaci souborů ve zdroji se je znovu nasadil Web.

## <a name="deployment-scenarios"></a>Scénáře nasazení

Typické nasazení scénáře zahrnují vytváření pracovní nasazení a přesouvání stávající funkce pro průběžné nasazování.

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Vytvořit pracovní nasazení

Aplikace Function App zatím nepodporují sloty nasazení. Ale můžete dál spravovat samostatná nasazení pracovního a produkčního prostředí s využitím průběžné integrace.

Postup konfigurace a práce s pracovní nasazení vypadá obvykle takto:

1. Vytvořit dvě aplikace function App v rámci vašeho předplatného: jeden pro produkční kód a jeden pro přípravu. 

1. Pokud ho ještě nemáte, vytvořte zdroj nasazení. Tento příklad používá [GitHub].

1. Pro vaše produkční aplikace function app, dokončení podle předchozích kroků [nastavení průběžného nasazování](#set-up-continuous-deployment) a nastavit nasazení větve do hlavní větve z vašeho úložiště GitHub.
   
    ![Tak, že vyberte větev nasazení](./media/functions-continuous-deployment/choose-deployment-branch.png)

1. Opakujte krok 3 pro pracovní aplikace function app, ale místo toho vyberte pracovní větev v úložišti GitHub. Pokud váš zdroj nasazení nepodporuje, větvení, použijte jinou složku.
    
1. Proveďte aktualizace kódu v pracovní větev nebo složku a potom ověřte, že pracovní nasazení odráží tyto změny.

1. Po otestování sloučit změny z pracovní větve do hlavní větve. Nasazení do produkčního prostředí aplikace function app se aktivuje toto sloučení. Pokud váš zdroj nasazení nepodporuje větví, přepište soubory ve složce produkčního prostředí se soubory z pracovní složky.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Přesunout stávající funkce pro průběžné nasazování
Pokud máte existující funkce, které jste vytvořili a udržuje na portálu, budete muset stáhnout soubory kódu funkce s využitím protokolu FTP nebo místní úložiště Git, teprve potom můžete nastavit průběžné nasazování, jak je popsáno výše. Můžete to provést v nastavení služby Azure App Service pro vaši aplikaci function app. Po stažení souborů, můžete je načíst zdroj zvolené průběžné nasazování.

> [!NOTE]
> Po dokončení konfigurace průběžné integrace, nemůžete nadále upravovat zdrojových souborů na portálu funkcí.

<a name="credentials"></a>
#### <a name="configure-deployment-credentials"></a>Nakonfigurujte přihlašovací údaje pro nasazení
Předtím, než si můžete stáhnout soubory z aplikace function app pomocí FTP a místního úložiště Git, je nutné nakonfigurovat přihlašovací údaje pro přístup k webu. Přihlašovací údaje jsou nastavené na úrovni aplikace funkce. Chcete-li nastavit přihlašovací údaje pro nasazení na webu Azure Portal postupujte následovně:

1. V aplikaci function app v [webu Azure portal](https://portal.azure.com)vyberte **funkce platformy** > **přihlašovací údaje pro nasazení**.
   
1. Zadejte uživatelské jméno a heslo a potom vyberte **Uložit**. 

   ![Tak, že přihlašovací údaje pro místní nasazení](./media/functions-continuous-deployment/setup-deployment-credentials.png)

Nyní můžete tyto přihlašovací údaje pro přístup k aplikaci function app z FTP nebo integrované úložiště Git.

<a name="downftp"></a>
#### <a name="download-files-by-using-ftp"></a>Stažení souborů pomocí protokolu FTP

1. V aplikaci function app v [webu Azure portal](https://portal.azure.com)vyberte **funkce platformy** > **vlastnosti**. Zkopírujte hodnoty **uživatel FTP/nasazení**, **název hostitele FTP**, a **název hostitele FTPS**.  

   **Uživatel FTP/nasazení** musí být zadán jak se zobrazuje na portálu, včetně názvu aplikace k zajištění správného kontextu pro FTP server.
   
   ![Tak, že informace o nasazení](./media/functions-continuous-deployment/get-deployment-credentials.png)

1. Ze svého klienta FTP použijte informace o připojení, které jste shromáždili pro připojení k vaší aplikace a stáhněte zdrojové soubory pro vaše funkce.

<a name="downgit"></a>
#### <a name="download-files-by-using-a-local-git-repository"></a>Stáhnout soubory pomocí místního úložiště Git

1. V aplikaci function app v [webu Azure portal](https://portal.azure.com)vyberte **funkce platformy** > **možnosti nasazení**. 
   
    ![Výběry pro otevření možnosti nasazení](./media/functions-continuous-deployment/setup-deployment.png)
 
1. Pak na **nasazení** okně vyberte **nastavení**.
 
    ![Okno nasazení](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. Na **zdroj nasazení** okně vyberte **místního úložiště Git** > **OK**.

1. V **funkce platformy**vyberte **vlastnosti** a poznamenejte si hodnotu adresy URL pro Git. 
   
    ![Výběry pro získání adresy URL pro Git](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

1. Naklonujte úložiště na místním počítači pomocí s ohledem na Git příkazového řádku nebo vašeho oblíbeného nástroje Git. Klonovací příkaz Git vypadá takto:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

1. Načtení souborů z aplikace function app klonu v místním počítači, jako v následujícím příkladu:
   
        git pull origin master
   
    Pokud o to požádá zadat vaše [nakonfigurované přihlašovací údaje pro nasazení](#credentials).  

[GitHub]: https://github.com/

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
