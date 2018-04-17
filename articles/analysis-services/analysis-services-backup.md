---
title: Azure zálohování databáze služby Analysis Services a obnovení | Microsoft Docs
description: Popisuje, jak zálohovat a obnovit databázi služby Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ec213d5c223180825ea0eabe95881002432b92e9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="backup-and-restore"></a>Zálohování a obnovení

Zálohování databází tabulkový model v Azure Analysis Services je podobný jako místní služba Analysis Services. Základní rozdíl je, kam můžete ukládat soubory zálohy. Záložní soubory musí být uložena do kontejneru v [účtu úložiště Azure](../storage/common/storage-create-storage-account.md). Můžete použít účet úložiště a kontejneru, který už máte, nebo je lze vytvořit při konfiguraci nastavení úložiště pro svůj server.

> [!NOTE]
> Vytvoření účtu úložiště, může mít za následek začne fakturovat nová služba. Další informace najdete v tématu [Azure Storage – ceny](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

Zálohy se ukládají s příponou abf. Pro tabulkové modely v paměti jsou uloženy data modelu a metadata. Tabulkové modely DirectQuery ukládají pouze metadata modelu. Zálohy lze komprimovat a šifrování, v závislosti na možnostech, které zvolíte. 



## <a name="configure-storage-settings"></a>Konfigurovat nastavení úložiště
Před zahájením zálohování, musíte nakonfigurovat nastavení úložiště pro svůj server.


### <a name="to-configure-storage-settings"></a>Konfigurovat nastavení úložiště
1.  Na portálu Azure > **nastavení**, klikněte na tlačítko **zálohování**.

    ![Zálohy v nastavení](./media/analysis-services-backup/aas-backup-backups.png)

2.  Klikněte na tlačítko **povoleno**, pak klikněte na tlačítko **nastavení úložiště**.

    ![Povolení](./media/analysis-services-backup/aas-backup-enable.png)

3. Vyberte účet úložiště nebo vytvořte novou.

4. Vyberte kontejner nebo vytvořte novou.

    ![Vyberte kontejner](./media/analysis-services-backup/aas-backup-container.png)

5. Uložte nastavení zálohování.

    ![Uložit nastavení zálohování](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>K zálohování pomocí aplikace SSMS

1. V aplikaci SSMS, klikněte pravým tlačítkem na databázi > **zálohování**.

2. V **příkaz Backup Database** > **záložní soubor**, klikněte na tlačítko **Procházet**.

3. V **uložit soubor jako** dialogové okno, ověřte cestu ke složce a potom zadejte název souboru zálohy. 

4. V **příkaz Backup Database** dialogové okno, vyberte možnosti.

    **Povolit souboru přepsat** – vyberte tuto možnost, chcete-li přepsat záložní soubory se stejným názvem. Pokud není vybraná tato možnost, soubor, který chcete uložit nemůže mít stejný název jako soubor, který již existuje ve stejném umístění.

    **Použít komprese** – vyberte tuto možnost, chcete-li komprimovat soubor zálohy. Komprimované záložní soubory ušetřit místo na disku, ale vyžadují mírně zvýší využití procesoru. 

    **Šifrování záložní soubor** – vyberte tuto možnost, šifrování záložní soubor. Tato možnost vyžaduje uživatel zadal heslo pro záložní soubor zabezpečení. Heslo zabraňuje čtení zálohovaných dat jiným způsobem než operaci obnovení. Pokud zvolíte možnost šifrování záloh, uložte heslo na bezpečné místo.

5. Klikněte na tlačítko **OK** vytvořit a uložit záložní soubor.


### <a name="powershell"></a>PowerShell
Použití [zálohování ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet) rutiny.

## <a name="restore"></a>Obnovení
Při obnovování, záložní soubor musí být v účtu úložiště, kterou jste nakonfigurovali pro váš server. Pokud potřebujete přesunout soubor zálohy z místního umístění účtu úložiště, použijte [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) nebo [AzCopy](../storage/common/storage-use-azcopy.md) nástroj příkazového řádku. 



> [!NOTE]
> Pokud se obnovení ze serveru místní, musíte odebrat všechny domény uživatele z role modelu a přidat zpět role jako uživatelů Azure Active Directory.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Chcete-li obnovit pomocí aplikace SSMS

1. V aplikaci SSMS, klikněte pravým tlačítkem na databázi > **obnovení**.

2. V **příkaz Backup Database** dialogové okno, v **záložní soubor**, klikněte na tlačítko **Procházet**.

3. V **najít soubory databáze** dialogovém okně, vyberte soubor, který chcete obnovit.

4. V **obnovte databázi**, vyberte databázi.

5. Zadejte možnosti. Možnosti zabezpečení se musí shodovat možnosti zálohování, které jste použili při zálohování.


### <a name="powershell"></a>PowerShell

Použití [obnovení ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet) rutiny.


## <a name="related-information"></a>Související informace

[Účty úložiště Azure](../storage/common/storage-create-storage-account.md)  
[Vysoká dostupnost](analysis-services-bcdr.md)     
[Správa služby Azure Analysis Services](analysis-services-manage.md)
