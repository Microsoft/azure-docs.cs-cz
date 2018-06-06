---
title: Průběžné nasazování pro Azure Functions | Microsoft Docs
description: Průběžné nasazování vlastnosti služby Azure App Service použijte k publikování Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: db10cd957f4dc59f787e2ac625355a96c888356e
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34735699"
---
# <a name="continuous-deployment-for-azure-functions"></a>Průběžné nasazování se službou Azure Functions
Azure Functions usnadňuje nasazení funkce aplikace pomocí průběžnou integraci služby App Service. Funkce se integruje s BitBucket, Dropbox, Githubu a Visual Studio Team Services (VSTS). To umožňuje pracovní kde kód funkce aktualizace provedené pomocí jedné z těchto integrovaných služeb aktivační událost nasazení do Azure. Pokud začínáte na Azure Functions, začněte s [přehled Azure Functions](functions-overview.md).

Průběžné nasazování je skvělou možností pro projekty, u kterých se integruje více příspěvků nebo u kterých integrace probíhá často. To také umožňuje udržovat zdrojového kódu na váš kód funkce. Následující zdroje nasazení jsou aktuálně podporovány:

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* Externího úložiště (Git nebo Mercurial)
* [Místní úložiště Git](../app-service/app-service-deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Visual Studio Team Services](https://www.visualstudio.com/team-services/)

Nasazení jsou nakonfigurované na základě aplikací na funkce. Po povolení průběžné nasazování přístup ke kódu funkce portálu je nastavena na *jen pro čtení*.

## <a name="continuous-deployment-requirements"></a>Průběžné nasazování požadavky

Váš zdroj nasazení nakonfigurované a váš kód funkce musí mít v zdroj nasazení před nastavit průběžné nasazování. V nasazení aplikace danou funkci jednotlivé funkce žije v podadresáři s názvem, kde název adresáře je název funkce.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Abyste mohli nasadit ze služby VSTS, je nutné nejprve propojit svůj účet služby VSTS s předplatným Azure. Další informace najdete v tématu [nastavit fakturace pro váš účet služby VSTS](https://docs.microsoft.com/vsts/billing/set-up-billing-for-your-account-vs?view=vsts#set-up-billing-via-the-azure-portal).

## <a name="set-up-continuous-deployment"></a>Nastavení nepřetržitého nasazování
Pomocí tohoto postupu ke konfiguraci průběžné nasazování pro existující aplikaci funkce. Tyto kroky ukazují integrace s úložišti GitHub, ale podobným způsobem platí pro Visual Studio Team Services nebo jiné služby pro nasazení.

1. V aplikaci funkce v [portál Azure](https://portal.azure.com), klikněte na tlačítko **funkce** a **možnosti nasazení**. 
   
    ![Nastavit průběžné nasazování.](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Potom v **nasazení** okně klikněte na tlačítko **instalační program**.
 
    ![Nastavit průběžné nasazování.](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. V **zdroj nasazení** okně klikněte na tlačítko **vybrat zdroj**, pak zadejte informace pro váš zdroj vybraný nasazení a klikněte na **OK**.
   
    ![Vyberte zdroj nasazení.](./media/functions-continuous-deployment/choose-deployment-source.png)

Po dokončení konfigurace průběžné nasazování, všechny změny souboru ve zdroji nasazení se zkopírují do aplikaci funkce a aktivaci nasazení plnou verzi webu. Když jsou aktualizovány soubory ve zdrojovém se je znovu nasadil webu.

## <a name="deployment-options"></a>Možnosti nasazení

Tady jsou některé typické nasazení scénáře:

- [Vytvořit pracovní nasazení](#staging)
- [Průběžné nasazování budou přesunuty existující funkce](#existing)

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>Vytvořit pracovní nasazení

Funkce aplikace ještě nepodporuje nasazovací sloty. Samostatná pracovní a provozní nasazení však můžete spravovat pomocí nepřetržité integrace.

Postup konfigurace a práce s pracovní nasazení se obecně vypadat třeba takto:

1. Vaše předplatné, jeden pro kód produkční a jeden pro pracovní vytvořte dvě funkce aplikace. 

2. Pokud jste ještě nemáte, vytvořte zdroj nasazení. Tento příklad používá [GitHub].

3. Pro vaše produkční funkce aplikace podle předchozích dokončení kroků **nastavit průběžné nasazování** a nastavit nasazení větve do hlavní větve ve vašem úložišti GitHub.
   
    ![Vyberte větev nasazení](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. Tento krok opakujte pro pracovní funkce aplikace, ale místo toho vyberte pracovní větev ve vašem úložišti GitHub. Pokud vaše zdrojová nasazení nepodporuje vytvoření větve, použijte jinou složku.
    
5. Provést aktualizace kódu v pracovní větev nebo složku a potom ověřte, že tyto změny se projeví v pracovní nasazení.

6. Po testování sloučit změny z pracovní větve do hlavní větve. Tato sloučení aktivuje nasazení na produkční aplikaci funkce. Pokud vaše zdrojová nasazení nepodporuje větví, přepište soubory ve složce produkční soubory z pracovní složky.

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Průběžné nasazování budou přesunuty existující funkce
Pokud máte existující funkce, které jste vytvořili a udržuje na portálu, budete muset stáhnout existující soubory s kódem funkce pomocí protokolu FTP, nebo místní úložiště Git předtím, než můžete nastavit průběžné nasazování, jak je popsáno výše. To provedete v nastavení služby App Service pro vaši aplikaci funkce. Po stažení jsou vaše soubory, můžete je načíst do zdroje zvolené průběžné nasazování.

> [!NOTE]
> Po dokončení konfigurace průběžnou integraci, jste už nebude moci upravit zdrojové soubory vašeho funkce portálu.

- [Postupy: konfigurace přihlašovací údaje nasazení.](#credentials)
- [Postupy: stažení souborů pomocí protokolu FTP](#downftp)
- [Postupy: stahovat soubory s použitím místní úložiště Git](#downgit)

<a name="credentials"></a>
#### <a name="how-to-configure-deployment-credentials"></a>Postupy: konfigurace přihlašovací údaje nasazení.
Než si můžete stáhnout soubory z funkce aplikace pomocí protokol FTP nebo místní úložiště Git, je nutné nakonfigurovat vaše přihlašovací údaje pro přístup k webu. Přihlašovací údaje jsou nastavené na úrovni aplikace funkce. Chcete-li nastavit přihlašovací údaje pro nasazení na portálu Azure pomocí následujících kroků:

1. V aplikaci funkce v [portál Azure](https://portal.azure.com), klikněte na tlačítko **funkce** a **přihlašovací údaje nasazení**.
   
    ![Nastavte přihlašovací údaje pro místní nasazení](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. Zadejte uživatelské jméno a heslo a pak klikněte na **Uložit**. Nyní můžete tyto přihlašovací údaje pro přístup k vaší aplikaci funkce z protokol FTP nebo integrované úložiště Git.

<a name="downftp"></a>
#### <a name="how-to-download-files-using-ftp"></a>Postupy: stažení souborů pomocí protokolu FTP

1. V aplikaci funkce v [portál Azure](https://portal.azure.com), klikněte na tlačítko **funkce** a **vlastnosti**, zkopírujte hodnoty **uživatel FTP/nasazení**, **název hostitele FTP**, a **FTPS název hostitele**.  

    **Uživatel FTP/nasazení** musí být zadané jako zobrazí na portálu, včetně názvu aplikace, zajistit správné kontextu pro FTP server.
   
    ![Získat informace o nasazení](./media/functions-continuous-deployment/get-deployment-credentials.png)

2. Z vašeho klienta FTP, použijte informace o připojení jste shromáždili pro připojení k vaší aplikace a stáhnout zdrojové soubory pro funkce.

<a name="downgit"></a>
#### <a name="how-to-download-files-using-a-local-git-repository"></a>Postupy: stahovat soubory s použitím místní úložiště Git

1. V aplikaci funkce v [portál Azure](https://portal.azure.com), klikněte na tlačítko **funkce** a **možnosti nasazení**. 
   
    ![Nastavit průběžné nasazování.](./media/functions-continuous-deployment/setup-deployment.png)
 
2. Potom v **nasazení** okně klikněte na tlačítko **instalační program**.
 
    ![Nastavit průběžné nasazování.](./media/functions-continuous-deployment/setup-deployment-1.png)
   
2. V **zdroj nasazení** okně klikněte na tlačítko **úložiště místní Git** a pak klikněte na **OK**.

3. V **funkce**, klikněte na tlačítko **vlastnosti** a poznamenejte si hodnotu adresy URL Git. 
   
    ![Nastavit průběžné nasazování.](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. Naklonujte úložiště na místním počítači pomocí Git využívající příkazový řádek nebo vaše oblíbené nástroje Git. Příkaz Git clone vypadá takto:
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. Načítání souborů z aplikace funkce klonu v místním počítači, jako v následujícím příkladu:
   
        git pull origin master
   
    Pokud požadovaný, zadejte vaše [nakonfigurovat přihlašovací údaje nasazení](#credentials).  

[GitHub]: https://github.com/

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
