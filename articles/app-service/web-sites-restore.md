---
title: Obnovení aplikace v Azure
description: Zjistěte, jak obnovení aplikace ze zálohy.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.openlocfilehash: 69e0e8282ee0b8503fe11a57b8ba6037247822dd
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160497"
---
# <a name="restore-an-app-in-azure"></a>Obnovení aplikace v Azure
V tomto článku se dozvíte, jak obnovit aplikaci v [služby Azure App Service](../app-service/app-service-web-overview.md) , který jste dříve zálohovali (viz [zálohování vaší aplikace v Azure](web-sites-backup.md)). Podle potřeby můžete obnovit aplikaci s připojenými databázemi do předchozího stavu nebo podle záloh původní aplikace vytvořit novou aplikaci. Azure App Service podporuje pro zálohování a obnovení následujících databází:
- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL v aplikaci](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

Obnovení ze zálohy je k dispozici pro aplikace běžící **standardní** a **Premium** vrstvy. Informace o vertikálním navýšení kapacity aplikace naleznete v tématu [vertikální navýšení kapacity aplikace v Azure](web-sites-scale.md). **Premium** úroveň umožňuje větší počet denních záloh mají být provedeny než **standardní** vrstvy.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Obnovení aplikace z existující zálohy
1. Na **nastavení** stránky vaší aplikace na webu Azure Portal klikněte na tlačítko **zálohy** zobrazíte **zálohy** stránky. Pak klikněte na tlačítko **obnovení**.
   
    ![Tlačítko Obnovit][ChooseRestoreNow]
2. V **obnovení** nejprve vyberte možnost zálohování zdroje.
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    **Zálohování aplikace** možnost se zobrazí všechny existující zálohy aktuální aplikaci, a můžete snadno vybrat jednu.
    **Úložiště** možnost vám umožňuje vybrat libovolný soubor ZIP zálohy z jakékoli existující účet úložiště Azure a kontejnerů ve vašem předplatném.
    Pokud se snažíte obnovit zálohu jiné aplikace, použijte **úložiště** možnost.
3. Zadejte cíl pro obnovení aplikace v **cíl obnovení**.
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Pokud se rozhodnete **přepsat**, vše je vymazána a přepsat existující data ve vaší aktuální aplikaci. Před kliknutím na **OK**, ujistěte se, že je přesně co chcete udělat.
   > 
   > 
   
   > [!WARNING]
   > Pokud služby App Service je zápis dat do databáze, zatímco ji obnovíte, může to vést příznaky například porušení PRIMÁRNÍHO klíče a únikem informací. Doporučujeme, abyste před zahájením obnovení databáze nejprve zastavit služby App Service.
   > 
   > 
   
    Můžete vybrat **existující aplikaci** obnovení zálohy aplikace do jiné aplikace ve stejné skupině prostředků. Před použitím této možnosti by jste již vytvořili jinou aplikací ve vaší skupině prostředků s zrcadlení databáze konfigurace definovaná v zálohování aplikace. Můžete také vytvořit **nový** chcete obnovit obsah.

4. Klikněte na **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Stáhnout nebo odstranit zálohy z účtu úložiště
1. V hlavním **Procházet** stránky webu Azure portal, vyberte **účty úložiště**. Zobrazí se seznam existujících účtů úložiště.
2. Vyberte účet úložiště obsahující zálohu, kterou chcete stáhnout nebo odstranit. Zobrazí se stránka pro účet úložiště.
3. Na stránce účtu úložiště vyberte kontejner, který chcete, aby
   
    ![Zobrazení kontejnerů][ViewContainers]
4. Vyberte záložní soubor, který chcete stáhnout nebo odstranit.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Klikněte na tlačítko **Stáhnout** nebo **odstranit** v závislosti na tom, co chcete udělat.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Sledovat operaci obnovení
Chcete-li zobrazit podrobnosti o úspěchu nebo selhání operace obnovení aplikace, přejděte na **protokolu aktivit** stránky na webu Azure Portal.  
 

Přejděte dolů a najděte požadované operace obnovení a klikněte na tlačítko a vyberte ji.

Na stránce podrobností zobrazuje dostupné informace vztahující se k operaci obnovení.

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Můžete automatizovat správu záloh pomocí skriptů s využitím [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) nebo [prostředí Azure PowerShell](/powershell/azure/overview).

Ukázky najdete v tématu:

- [Ukázky Azure CLI](app-service-cli-samples.md)
- [Ukázky Azure Powershellu](app-service-powershell-samples.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
