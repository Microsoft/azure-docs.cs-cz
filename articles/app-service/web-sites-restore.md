---
title: Obnovit aplikaci ze zálohy
description: Přečtěte si, jak obnovit aplikaci ze zálohy. Některé propojené databáze je možné obnovit spolu s aplikací v jedné operaci.
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: c1b0ce62905424032c2100a1a032fa43ba97578f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999395"
---
# <a name="restore-an-app-in-azure"></a>Obnovení aplikace v Azure
V tomto článku se dozvíte, jak obnovit aplikaci v [Azure App Service](../app-service/overview.md) , kterou jste předtím zálohovali (viz [zálohování aplikace v Azure](manage-backup.md)). Můžete obnovit svou aplikaci s propojenými databázemi na vyžádání do předchozího stavu nebo vytvořit novou aplikaci založenou na jednom z záloh vaší původní aplikace. Azure App Service podporuje následující databáze pro zálohování a obnovení:
- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL v aplikaci](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

Obnovení ze zálohy je k dispozici pro aplikace běžící na úrovni **Standard** a **Premium** . Informace o škálování aplikace najdete v tématu [horizontální navýšení kapacity aplikace v Azure](manage-scale-up.md). Úroveň **Premium** umožňuje provést větší počet denních záloh než úroveň **Standard** .

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Obnovení aplikace ze stávající zálohy
1. Na stránce **Nastavení** aplikace v Azure Portal klikněte na **zálohování** . zobrazí se stránka **zálohy** . Pak klikněte na **obnovit**.
   
    ![Zvolit obnovit hned][ChooseRestoreNow]
2. Na stránce **obnovení** nejprve vyberte zdroj zálohy.
   
    ![Snímek obrazovky, který ukazuje, kde vybrat zdroj zálohy](./media/web-sites-restore/021ChooseSource1.png)
   
    Možnost **zálohování aplikace** zobrazuje všechny existující zálohy aktuální aplikace a můžete ji snadno vybrat.
    Možnost **úložiště** vám umožní vybrat libovolný záložní soubor zip z existujícího účtu Azure Storage a kontejneru v předplatném.
    Pokud se pokoušíte obnovit zálohu jiné aplikace, použijte možnost **úložiště** .
3. Pak zadejte cíl pro obnovení aplikace v **umístění obnovení**.
   
    ![Snímek obrazovky, který ukazuje, kde určit cíl pro obnovení aplikace](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Pokud zvolíte možnost **přepsat**, všechna existující data v aktuální aplikaci budou smazána a přepsána. Než kliknete na **OK**, ujistěte se, že je přesně to, co chcete udělat.
   > 
   > 
   
   > [!WARNING]
   > Pokud App Service zapisuje data do databáze během obnovování, může to vést k problémům, jako je například porušení primárního klíče a ztráty dat. Před zahájením obnovování databáze je navrženo, že nejprve zastaví App Service.
   > 
   > 
   
    Můžete vybrat **existující aplikaci** a obnovit zálohu aplikace do jiné aplikace ve stejné skupině prostředků. Předtím, než použijete tuto možnost, byste už měli mít ve skupině prostředků vytvořenou jinou aplikaci s konfigurací databáze zrcadlení, která je definovaná v záloze aplikace. Můžete také vytvořit **novou** aplikaci pro obnovení obsahu.

4. Klikněte na **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Stažení nebo odstranění zálohy z účtu úložiště
1. Na hlavní stránce pro **procházení** Azure Portal vyberte **účty úložiště**. Zobrazí se seznam stávajících účtů úložiště.
2. Vyberte účet úložiště, který obsahuje zálohu, kterou chcete stáhnout nebo odstranit. Zobrazí se stránka pro účet úložiště.
3. Na stránce účet úložiště vyberte kontejner, který chcete.
   
    ![Zobrazit kontejnery][ViewContainers]
4. Vyberte záložní soubor, který chcete stáhnout nebo odstranit.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. V závislosti na tom, co chcete udělat, klikněte na **Stáhnout** nebo **Odstranit** .  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Monitorování operace obnovení
Pokud chcete zobrazit podrobnosti o úspěchu nebo neúspěchu operace obnovení aplikace, přejděte na stránku **Protokol aktivit** v Azure Portal.  
 

Posuňte se dolů a vyhledejte požadovanou operaci obnovení a kliknutím ji vyberte.

Na stránce Podrobnosti se zobrazí dostupné informace týkající se operace obnovení.

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Správu zálohování můžete automatizovat pomocí skriptů pomocí rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) nebo [Azure PowerShell](/powershell/azure/).

Ukázky najdete v těchto tématech:

- [Ukázky Azure CLI](samples-cli.md)
- [Ukázky Azure PowerShell](samples-powershell.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
