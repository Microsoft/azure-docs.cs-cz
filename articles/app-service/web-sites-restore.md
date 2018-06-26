---
title: Obnovení aplikace v Azure
description: Zjistěte, jak vaše aplikace obnovit ze zálohy.
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
ms.openlocfilehash: d4b84904db703d1e8e78240cb971250672ef9615
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753281"
---
# <a name="restore-an-app-in-azure"></a>Obnovení aplikace v Azure
Tento článek ukazuje, jak obnovit aplikace v [Azure App Service](../app-service/app-service-web-overview.md) který jste dříve zálohovali (viz [zálohování aplikace v Azure](web-sites-backup.md)). Podle potřeby můžete obnovit aplikaci s připojenými databázemi do předchozího stavu nebo podle záloh původní aplikace vytvořit novou aplikaci. Aplikační služba Azure podporuje následující databáze pro zálohování a obnovení:
- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL v aplikaci](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

Obnovení ze zálohy je k dispozici pro aplikace běžící **standardní** a **Premium** vrstvy. Informace o vertikálním navýšení kapacity aplikace naleznete v tématu [škálování aplikace v Azure](web-sites-scale.md). **Premium** úroveň umožňuje větší počet denní zálohování než **standardní** vrstvy.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Obnovení aplikace z existující zálohy
1. Na **nastavení** stránky vaší aplikace na portálu Azure, klikněte na tlačítko **zálohování** zobrazíte **zálohování** stránky. Pak klikněte na tlačítko **obnovení**.
   
    ![Zvolte teď obnovení][ChooseRestoreNow]
2. V **obnovení** nejprve vyberte zálohování zdroje.
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    **Zálohování aplikace** možnost ukazuje všechny stávající zálohy aktuální aplikaci, a můžete snadno vybrat jednu.
    **Úložiště** možnost umožňuje vybrat všechny záložní soubor ZIP z jakékoli existující účet služby Azure Storage a kontejner v rámci vašeho předplatného.
    Pokud se pokoušíte obnovit zálohu jiné aplikaci, použijte **úložiště** možnost.
3. Zadejte cíl pro obnovení aplikace v **cíl obnovení**.
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Pokud se rozhodnete **přepsat**, všechny je vymazat a přepsat existující data v aktuální aplikaci. Před kliknutím na **OK**, ujistěte se, že je přesně co chcete udělat.
   > 
   > 
   
    Můžete vybrat **stávající aplikace** obnovení zálohy aplikace do jiné aplikace ve stejné skupině prostředků. Než použijete tuto možnost, měli jste již vytvořili jiné aplikace ve vaší skupině prostředků s zrcadlení konfigurace databáze na jednu definované v aplikaci zálohování. Můžete také vytvořit **nový** obnovit svůj obsah do aplikace.

4. Klikněte na **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Stáhnout nebo odstranit zálohy z účtu úložiště
1. Z hlavní **Procházet** stránce portálu Azure, vyberte **účty úložiště**. Zobrazí se seznam existující účty úložiště.
2. Vyberte účet úložiště, který obsahuje zálohu, kterou chcete stáhnout nebo odstranit. Zobrazí se stránka pro účet úložiště.
3. Na stránce účtu úložiště vyberte kontejner, který chcete
   
    ![Zobrazení kontejnery][ViewContainers]
4. Vyberte záložní soubor, který chcete stáhnout nebo odstranit.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Klikněte na tlačítko **Stáhnout** nebo **odstranit** v závislosti na tom, co chcete udělat.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Monitorování operací obnovení
Chcete-li zobrazit podrobnosti o úspěchu nebo neúspěchu operace obnovení aplikace, přejděte na **protokol aktivit** na portálu Azure.  
 

Přejděte dolů a najděte požadovanou operaci obnovení a klikněte na tlačítko ji vyberte.

Na stránce Podrobnosti zobrazuje dostupné informace související s operací obnovení.

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Můžete automatizovat správu záloh pomocí skriptů, pomocí [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) nebo [prostředí Azure PowerShell](/powershell/azure/overview).

Ukázky najdete v části:

- [Ukázek Azure CLI](app-service-cli-samples.md)
- [Ukázek Azure PowerShell](app-service-powershell-samples.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
