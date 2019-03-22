---
title: Zálohování služby App Service ve službě Azure Stack | Dokumentace Microsoftu
description: Podrobné pokyny pro zálohování Azure stacku App Service.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: jeffgilb
ms.reviewer: apwestgarth
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: 66f1f1389a7e4ceebc38544f2eb9836fad2bd96a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340547"
---
# <a name="back-up-app-service-on-azure-stack"></a>Zálohování služby App Service ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*  

Tento dokument obsahuje pokyny týkající se zálohování služby App Service ve službě Azure Stack.

> [!IMPORTANT]
> App Service ve službě Azure Stack se nezálohovují jako součást [zálohování infrastruktury Azure stacku](azure-stack-backup-infrastructure-backup.md). Jako operátor Azure stacku je nutné provést kroky k zajištění, že služby App Service je možné úspěšně obnovit v případě potřeby.

Azure App Service ve službě Azure Stack zahrnuje čtyři hlavní součásti, které je třeba zvážit při plánování zotavení po havárii:
1. Infrastruktury poskytovatele prostředků; role serveru, vrstvy pracovních procesů, atd. 
2. Tajné kódy služby App Service
3. Hostování aplikace služby SQL Server a databáze měření
4. App Service uživatelské zatížení obsahu uložené ve sdílené složce služby App Service   

## <a name="back-up-app-service-secrets"></a>Zálohování tajné kódy služby App Service
Při obnovování služby App Service ze zálohy, je nutné zadat klíče služby App Service používá počátečního nasazení. Tyto informace uložit, jakmile App Service se úspěšně nasadila a uloženy do bezpečného umístění. Konfigurace infrastruktury poskytovatele prostředků se znovu vytvoří, ze zálohy při obnovení pomocí služby App Service obnovení rutiny prostředí PowerShell.

Použití portálu pro správu k zálohování tajných kódů aplikace služby pomocí následujících kroků: 

1. Přihlaste se k portálu pro správu služby Azure Stack jako správce služeb.

2. Přejděte do **služby App Service** -> **tajných kódů**. 

3. Vyberte **stáhnout tajné kódy**.

   ![Stáhnout tajné kódy](./media/app-service-back-up/download-secrets.png)

4. Když tajné kódy jsou připravené pro stahování, klikněte na tlačítko **Uložit** a ukládat tajné kódy služby App Service (**SystemSecrets.JSON**) soubor do bezpečného umístění. 

   ![Uložení tajných kódů](./media/app-service-back-up/save-secrets.png)

> [!NOTE]
> Tento postup opakujte, pokaždé, když jsou otočeny tajné kódy služby App Service.

## <a name="back-up-the-app-service-databases"></a>Zálohování databází služby App Service
Pokud chcete obnovit App Service, budete potřebovat **Appservice_hosting** a **Appservice_metering** záloh databází. Doporučujeme používat plány údržby systému SQL Server nebo Azure Backup serveru k zajištění těchto databází jsou zálohovány a bezpečně uložit v pravidelných intervalech. Nicméně jsou vytvořeny jakékoli metody objektu zajišťují pravidelné zálohování SQL lze použít.

Ruční zálohování databází při přihlášení do systému SQL Server, můžete použít následující příkazy Powershellu:

  ```powershell
  $s = "<SQL Server computer name>"
  $u = "<SQL Server login>" 
  $p = read-host "Provide the SQL admin password"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_hosting TO DISK = '<path>\hosting.bak'"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_metering TO DISK = '<path>\metering.bak'"
  ```

> [!NOTE]
> Pokud potřebujete zálohování databází SQL AlwaysOn, proveďte [tyto pokyny](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server?view=sql-server-2017). 

Po všech databází byly úspěšně zálohovány, zkopírujte soubory .bak do bezpečného umístění společně s informacemi tajné kódy služby App Service.

## <a name="back-up-the-app-service-file-share"></a>Zálohování sdílené služby App Service
App Service úložišť tenanta informace o aplikaci ve sdílené složce. To je nutné zálohovat v pravidelných intervalech spolu s databází služby App Service tak, aby jako málo dat co nefunguje, pokud se obnovení je povinný. 

Zálohování sdílené složky služby App Service soubor obsahu, že vám pomůže Azure Backup serveru nebo použijte jinou metodu pravidelné kopírování sdílené složky obsahu na umístění jste uložili všechny předchozí informace pro obnovení. 

Například pomocí nástroje robocopy z relace konzoly Windows Powershellu (ne prostředí PowerShell ISE), můžete tyto kroky:

```powershell
$source = "<file share location>"
$destination = "<remote backup storage share location>"
net use $destination /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy $source $destination
net use $destination /delete
```

## <a name="next-steps"></a>Další postup
[Obnovení služby App Service v Azure stacku](app-service-recover.md)