---
title: Obnovení z neúspěšného selhání oblasti
description: Přečtěte si, jak Azure App Service pomáhá udržovat možnosti pro provozní kontinuitu a zotavení po havárii (BCDR). Obnovte svou aplikaci z oblasti selhání v Azure.
ms.topic: how-to
ms.date: 06/09/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 8c57cf5054bea898370cdccc7bea4243877d27b5
ms.sourcegitcommit: 51977b63624dfd3b4f22fb9fe68761d26eed6824
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84947035"
---
# <a name="move-an-app-service-app-to-another-region"></a>Přesunutí aplikace App Service do jiné oblasti

Tento článek popisuje, jak přenést prostředky App Service zpátky do online režimu v jiné oblasti Azure během havárie, která má dopad na celou oblast Azure. Když dojde k havárii celé oblasti Azure v režimu offline, všechny App Service aplikace hostované v této oblasti jsou umístěné v režimu zotavení po havárii. K dispozici jsou funkce, které vám pomůžou aplikaci obnovit do jiné oblasti nebo obnovit soubory z ovlivněné aplikace.

Prostředky App Service jsou specifické pro oblast a nelze je přesouvat mezi oblastmi. Aplikaci musíte obnovit do nové aplikace v jiné oblasti a pak vytvořit konfigurace zrcadlení nebo prostředky pro novou aplikaci.

## <a name="prerequisites"></a>Požadavky

- Žádné [Obnovení ze snímku](app-service-web-restore-snapshots.md) obvykle vyžaduje úroveň **Premium** , ale v režimu zotavení po havárii je automaticky povolen pro vaši ovlivněnou aplikaci, a to bez ohledu na to, ve které vrstvě se ovlivněná aplikace nachází.

## <a name="prepare"></a>Příprava

Identifikujte všechny App Service prostředky, které aktuálně používá ovlivněná aplikace. Příklad:

- Aplikace služby App Service
- [Plány služby App Service](overview-hosting-plans.md)
- [Nasazovací sloty](deploy-staging-slots.md)
- [Vlastní domény zakoupené v Azure](manage-custom-dns-buy-domain.md)
- [Certifikáty SSL](configure-ssl-certificate.md)
- [Integrace se službou Azure Virtual Network](web-sites-integrate-with-vnet.md)
- [Hybridní připojení](app-service-hybrid-connections.md).
- [Spravované identity](overview-managed-identity.md)
- [Nastavení zálohování](manage-backup.md)

Některé prostředky, jako jsou importované certifikáty nebo hybridní připojení, obsahují integraci s dalšími službami Azure. Informace o tom, jak tyto prostředky přesunout do různých oblastí, najdete v dokumentaci k příslušným službám.

## <a name="restore-app-to-a-different-region"></a>Obnovení aplikace do jiné oblasti

1. Vytvořte novou aplikaci App Service v *jiné* oblasti Azure, než jakou má ovlivněná aplikace. Toto je cílová aplikace ve scénáři zotavení po havárii.

1. V [Azure Portal](https://portal.azure.com)přejděte na stránku správy ovlivněné aplikace. V neúspěšné oblasti Azure se v ovlivněné aplikaci zobrazuje text upozornění. Klikněte na text upozornění.

    ![](media/manage-disaster-recovery/restore-start.png)

1. Na stránce **obnovit zálohu** konfigurujte operaci obnovení podle následující tabulky. Po dokončení klikněte na tlačítko **OK**.

   | Nastavení | Hodnota | Description |
   |-|-|-|
   | **Snímek (Preview)** | Vyberte snímek. | K dispozici jsou dva nejnovější snímky. |
   | **Cíl obnovení** | **Existující aplikace** | Klikněte na poznámku níže. Pokud **chcete změnit cílovou aplikaci obnovení** a vybrat cílovou aplikaci, klikněte sem. V případě havárie můžete obnovit jenom snímek do aplikace v jiné oblasti Azure. |
   | **Obnovit konfiguraci lokality** | **Ano** | |

    ![](media/manage-disaster-recovery/restore-configure.png)

3. Nakonfigurujte [všechno ostatní](#prepare) v cílové aplikaci tak, aby bylo možné zrcadlit ovlivněnou aplikaci a ověřit konfiguraci.

4. Až budete připraveni, aby vlastní doména odkazovala na cílovou aplikaci, [přemapujte název domény](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

## <a name="recover-app-content-only"></a>Obnovit jenom obsah aplikace

Pokud chcete jenom obnovit soubory z ovlivněné aplikace bez obnovení, použijte následující postup:

1. V [Azure Portal](https://portal.azure.com)přejděte na stránku správy ovlivněné aplikace a klikněte na **získat profil publikování**.

    ![](media/manage-disaster-recovery/get-publish-profile.png)

1. Otevřete stažený soubor a vyhledejte profil publikování, který obsahuje `ReadOnly - FTP` název. Toto je profil zotavení po havárii. Příklad:

    ```xml
    <publishProfile profileName="%app-name% - ReadOnly - FTP" publishMethod="FTP" publishUrl="ftp://%ftp-site%/site/wwwroot" ftpPassiveMode="True" userName="%app-name%\$%app-name%" userPWD="" destinationAppUrl="http://%app-name%.azurewebsites.net" SQLServerDBConnectionString="" mySQLDBConnectionString="" hostingProviderForumLink="" controlPanelLink="http://windows.azure.com" webSystem="WebSites">
        <databases />
    </publishProfile>
    ```
    
    Zkopírujte tři hodnoty atributu: 
        
    - `publishUrl`: název hostitele FTP
    - `userName`a `userPWD` : přihlašovací údaje FTP

1. Použijte klienta FTP podle vašeho výběru a připojte se k hostiteli FTP ovlivněné aplikace pomocí názvu hostitele a přihlašovacích údajů.

1. Po připojení Stáhněte celou složku */site/wwwroot* . Následující snímek obrazovky ukazuje, jak si můžete stáhnout v [FileZilly](https://filezilla-project.org/).

    ![](media/manage-disaster-recovery/download-content.png)

## <a name="next-steps"></a>Další kroky
[Obnovení aplikace v Azure ze snímku](app-service-web-restore-snapshots.md)
