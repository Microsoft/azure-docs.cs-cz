---
title: Obnovení aplikace ze zálohy
description: Přečtěte si, jak obnovit aplikaci ze zálohy. Některé propojené databáze lze obnovit spolu s aplikací v jedné operaci.
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: 1295080d0eec7a4e88029cdadd85863f5f40d034
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74689250"
---
# <a name="restore-an-app-in-azure"></a>Obnovení aplikace v Azure
Tento článek ukazuje, jak obnovit aplikaci ve [službě Azure App Service,](../app-service/overview.md) kterou jste dříve zálohovali (viz [Zálohování aplikace v Azure).](manage-backup.md) Aplikaci s propojenými databázemi na vyžádání můžete obnovit do předchozího stavu nebo vytvořit novou aplikaci na základě jedné ze záloh původní aplikace. Služba Azure App Service podporuje následující databáze pro zálohování a obnovení:
- [Databáze SQL](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL v aplikaci](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

Obnovení ze záloh je k dispozici pro aplikace spuštěné na úrovni **Standard** a **Premium.** Informace o škálování aplikace najdete [v tématu Vertika navýšit kapacitu aplikace v Azure](manage-scale-up.md). **Úroveň Premium** umožňuje provádět větší počet denních záloh než úroveň **Standard.**

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Obnovení aplikace z existující zálohy
1. Na stránce **Nastavení** aplikace na webu Azure Portal klikněte na **Zálohy,** kde se zobrazí stránka **Zálohy.** Potom klepněte na tlačítko **Obnovit**.
   
    ![Zvolte nyní obnovit][ChooseRestoreNow]
2. Na stránce **Obnovení** nejprve vyberte záložní zdroj.
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    Možnost **zálohování aplikace** zobrazuje všechny existující zálohy aktuální aplikace a můžete je snadno vybrat.
    Možnost **Úložiště** umožňuje vybrat libovolný záložní soubor ZIP z libovolného existujícího účtu azure storage a kontejneru ve vašem předplatném.
    Pokud se pokoušíte obnovit zálohu jiné aplikace, použijte možnost **Úložiště.**
3. Potom zadejte cíl pro obnovení aplikace v **cíli obnovení**.
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Pokud zvolíte **Přepsat**, všechna existující data v aktuální aplikaci se smažou a přepíší. Než klepnete na tlačítko **OK**, ujistěte se, že je přesně to, co chcete udělat.
   > 
   > 
   
   > [!WARNING]
   > Pokud služba App Service zapisuje data do databáze při jejich obnovení, může to mít za následek příznaky, jako je porušení primárního klíče a ztráta dat. Doporučuje se nejprve zastavit službu App Service před zahájením obnovení databáze.
   > 
   > 
   
    Můžete vybrat **existující aplikace** pro obnovení zálohy aplikace do jiné aplikace ve stejné skupině prostředků. Než použijete tuto možnost, měli byste již vytvořit jinou aplikaci ve skupině prostředků s zrcadlení konfigurace databáze na jednu definovanou v záloze aplikace. Můžete také vytvořit **novou** aplikaci a obnovit tak svůj obsah.

4. Klikněte na tlačítko **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Stažení nebo odstranění zálohy z účtu úložiště
1. Na hlavní stránce **Procházení** na portálu Azure vyberte **Účty úložiště**. Zobrazí se seznam stávajících účtů úložiště.
2. Vyberte účet úložiště obsahující zálohu, kterou chcete stáhnout nebo odstranit. Zobrazí se stránka účtu úložiště.
3. Na stránce účtu úložiště vyberte požadovaný kontejner.
   
    ![Zobrazit kontejnery][ViewContainers]
4. Vyberte záložní soubor, který chcete stáhnout nebo odstranit.
   
    ![Zobrazit kontejnery](./media/web-sites-restore/03ViewFiles.png)
5. V závislosti na tom, co chcete udělat, klikněte na **Stáhnout** nebo **odstranit.**  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Sledování operace obnovení
Podrobnosti o úspěchu nebo neúspěchu operace obnovení aplikace zobrazíte na stránce **Protokol aktivit** na webu Azure Portal.  
 

Posuňte se dolů a najděte požadovanou operaci obnovení a klepnutím ji vyberte.

Na stránce podrobností se zobrazí dostupné informace související s operací obnovení.

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Správu zálohování můžete automatizovat pomocí skriptů pomocí [rozhraní Příkazového řádku Azure](/cli/azure/install-azure-cli) nebo Azure [PowerShellu](/powershell/azure/overview).

Ukázky viz:

- [Ukázky Azure CLI](samples-cli.md)
- [Ukázky Azure PowerShellu](samples-powershell.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
