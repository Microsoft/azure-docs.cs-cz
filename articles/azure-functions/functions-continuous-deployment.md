---
title: Průběžné nasazování pro službu Azure Functions | Dokumentace Microsoftu
description: Průběžné nasazování zařízení služby Azure App Service použijte k publikování Azure Functions.
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
ms.openlocfilehash: 7529d20535eedab92d164df5a0435efeda83fca2
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301544"
---
# <a name="continuous-deployment-for-azure-functions"></a>Průběžné nasazování se službou Azure Functions
Služba Azure Functions umožňuje snadno nasadit aplikaci function app používáte průběžnou integraci služby App Service. Functions se integruje s BitBucket, Dropbox, GitHub a Azure DevOps. To umožňuje pracovní postupy s kde kód funkce aktualizace provedené pomocí jedné z těchto integrovaných služeb aktivační události nasazení do Azure. Pokud jste ještě do služby Azure Functions, začněte s [přehled Azure Functions](functions-overview.md).

Průběžné nasazování je skvělou možností pro projekty, u kterých se integruje více příspěvků nebo u kterých integrace probíhá často. Můžete ho taky udržovat správy zdrojového kódu na kód vaší funkce. Aktuálně jsou podporovány následující zdroje nasazení:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* Externí úložiště (Git nebo Mercurial)
* [Místní úložiště Git](../app-service/app-service-deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Služby Azure DevOps](https://www.visualstudio.com/team-services/)

Nasazení se konfigurují na základě jednotlivých funkcích aplikace. Po povolení průběžného nasazování přístup ke kódu funkce na portálu je nastavena na *jen pro čtení*.

## <a name="continuous-deployment-requirements"></a>Požadavky na průběžné nasazování

Ve zdroji nasazení před můžete nastavit průběžné nasazování musí mít zdroj nasazení nakonfigurované a kód služby functions. V nasazení aplikace s danou funkci jednotlivých funkcí se nachází v podadresáři s názvem, kde název adresáře je název funkce.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Aby bylo možné nasadit z Azure DevOps, musíte si nejdříve propojit organizaci Azure DevOps ve vašem předplatném Azure. Další informace najdete v tématu [nastavte fakturaci pro vaši organizaci Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs?view=vsts#set-up-billing-via-the-azure-portal).

## <a name="set-up-continuous-deployment"></a>Nastavení nepřetržitého nasazování
Pomocí tohoto postupu konfigurace průběžného nasazování pro existující aplikaci function app. Tyto kroky ukazují, integrace s úložišti GitHub, ale podobný postup použít pro Azure DevOps nebo jiné služby pro nasazení.

1. V aplikaci function app v [webu Azure portal](https://portal.azure.com), klikněte na tlačítko **funkce platformy** a **možnosti nasazení**. 
   
    ![Průběžné nasazování](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Pak v **nasazení** okně klikněte na **nastavení**.
 
    ![Průběžné nasazování](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. V **zdroj nasazení** okna, klikněte na tlačítko **zvolit zdroj**, potom vyplňte informace pro vybrané nasazení zdroj a klikněte na tlačítko **OK**.
   
    ![Vyberte zdroj nasazení](./media/functions-continuous-deployment/choose-deployment-source.png)

Po dokončení konfigurace průběžného nasazování, všechny změny souborů ve zdroji nasazení se zkopírují do aplikace function app a aktivuje nasazení plnou verzi webu. Při aktualizaci souborů ve zdroji se je znovu nasadil Web.

## <a name="deployment-options"></a>Možnosti nasazení

Následují některé typické nasazení scénáře:

- [Vytvořit pracovní nasazení](#staging)
- [Přesunout stávající funkce pro průběžné nasazování](#existing)

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Vytvořit pracovní nasazení

Aplikací Function App zatím nepodporuje sloty nasazení. Samostatná pracovní a provozní nasazení však můžete spravovat pomocí nepřetržité integrace.

Postup konfigurace a práce s pracovní nasazení vypadá obvykle takto:

1. Do vašeho předplatného, jeden pro produkční kód a jeden pro pracovní vytvořte dvě aplikace function App. 

2. Pokud ho ještě nemáte, vytvořte zdroj nasazení. Tento příklad používá [GitHub].

3. Pro vaše produkční aplikace function app, dokončení podle předchozích kroků **nastavení průběžného nasazování** a nastavit nasazení větve do hlavní větve z vašeho úložiště GitHub.
   
    ![Zvolit větev nasazení](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. Tento krok opakujte pro pracovní aplikace function app, ale místo toho vyberte pracovní větev v úložišti GitHub. Pokud váš zdroj nasazení nepodporuje, větvení, použijte jinou složku.
    
5. Provést aktualizace kódu v pracovní větev nebo složku a potom ověřte, že tyto změny se projeví v pracovní nasazení.

6. Po otestování sloučit změny z pracovní větve do hlavní větve. Nasazení do produkčního prostředí aplikace function app se aktivuje toto sloučení. Pokud váš zdroj nasazení nepodporuje větví, přepište soubory ve složce produkčního prostředí se soubory z pracovní složky.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Přesunout stávající funkce pro průběžné nasazování
Pokud máte existující funkce, které jste vytvořili a udržuje na portálu, budete muset stáhnout existující soubory kódu funkce pomocí FTP nebo místní úložiště Git, teprve potom můžete nastavit průběžné nasazování, jak je popsáno výše. Můžete to provést v nastavení služby App Service pro vaši aplikaci function app. Po stažení souborů, můžete je načíst zdroj zvolené průběžné nasazování.

> [!NOTE]
> Po dokončení konfigurace průběžné integrace, je již nebude moci upravit zdrojových souborů na portálu Functions.

- [Postupy: Konfigurace přihlašovacích údajů nasazení](#credentials)
- [Postupy: stahování souborů přes FTP](#downftp)
- [Postupy: stahování souborů pomocí místního úložiště Git](#downgit)

<a name="credentials"></a>
#### <a name="how-to-configure-deployment-credentials"></a>Postupy: Konfigurace přihlašovacích údajů nasazení
Předtím, než může stáhnout soubory z vaší aplikace function app s FTP nebo místní úložiště Git, je nutné nakonfigurovat přihlašovací údaje pro přístup k webu. Přihlašovací údaje jsou nastavené na úrovni aplikace funkce. Chcete-li nastavit přihlašovací údaje pro nasazení na webu Azure Portal postupujte následovně:

1. V aplikaci function app v [webu Azure portal](https://portal.azure.com), klikněte na tlačítko **funkce platformy** a **přihlašovací údaje pro nasazení**.
   
    ![Přihlašovací údaje pro místní nasazení](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. Zadejte uživatelské jméno a heslo a potom klikněte na **Uložit**. Nyní můžete tyto přihlašovací údaje pro přístup k aplikaci function app z FTP nebo integrované úložiště Git.

<a name="downftp"></a>
#### <a name="how-to-download-files-using-ftp"></a>Postupy: stahování souborů přes FTP

1. V aplikaci function app v [webu Azure portal](https://portal.azure.com), klikněte na tlačítko **funkce platformy** a **vlastnosti**, zkopírujte hodnoty **uživatel FTP/nasazení**, **Název hostitele FTP**, a **název hostitele FTPS**.  

    **Uživatel FTP/nasazení** musí být zadán jak se zobrazuje na portálu, včetně názvu aplikace k zajištění správného kontextu pro FTP server.
   
    ![Informace o nasazení](./media/functions-continuous-deployment/get-deployment-credentials.png)

2. Ze svého klienta FTP, použijte informace o připojení jste shromáždili pro připojení k vaší aplikace a stáhněte zdrojové soubory pro vaše funkce.

<a name="downgit"></a>
#### <a name="how-to-download-files-using-a-local-git-repository"></a>Postupy: stahování souborů pomocí místního úložiště Git

1. V aplikaci function app v [webu Azure portal](https://portal.azure.com), klikněte na tlačítko **funkce platformy** a **možnosti nasazení**. 
   
    ![Průběžné nasazování](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Pak v **nasazení** okně klikněte na **nastavení**.
 
    ![Průběžné nasazování](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. V **zdroj nasazení** okna, klikněte na tlačítko **místního úložiště Git** a potom klikněte na tlačítko **OK**.

3. V **funkce platformy**, klikněte na tlačítko **vlastnosti** a poznamenejte si hodnotu adresy URL pro Git. 
   
    ![Průběžné nasazování](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. Naklonujte úložiště na místním počítači pomocí s ohledem na Git příkazového řádku nebo vašeho oblíbeného nástroje Git. Klonovací příkaz Git vypadá takto:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. Načtení souborů z aplikace function app klonu v místním počítači, jako v následujícím příkladu:
   
        git pull origin master
   
    Pokud o to požádá zadat vaše [nakonfigurované přihlašovací údaje pro nasazení](#credentials).  

[GitHub]: https://github.com/

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
