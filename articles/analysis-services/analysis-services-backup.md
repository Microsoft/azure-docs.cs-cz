---
title: Azure Analysis Services databáze zálohování a obnovení | Dokumentace Microsoftu
description: Popisuje, jak zálohovat a obnovit databázi Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b88660548feaa1d3a8e83e633f798212c80ac6dc
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190302"
---
# <a name="backup-and-restore"></a>Zálohování a obnovení

Zálohování databází tabulkových modelů ve službě Azure Analysis Services je skoro stejné jako u místních Analysis Services. Hlavní rozdíl je, kam se ukládají záložní soubory. Záložní soubory musí být uložen do kontejneru v [účtu služby Azure storage](../storage/common/storage-create-storage-account.md). Můžete použít účet úložiště a kontejner, který už máte, nebo mohou být vytvořeny při konfiguraci nastavení úložiště pro váš server.

> [!NOTE]
> Vytváří se účet úložiště může mít za následek nové vám začne fakturovat služba. Další informace najdete v tématu [ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

Zálohování se uloží s příponou abf. U tabulkových modelů v paměti modelování dat a metadata ukládají. Pro tabelární modely DirectQuery se ukládají jenom metada modelu. Zálohy lze komprimované a zašifrovaný, v závislosti na možnostech, které zvolíte. 



## <a name="configure-storage-settings"></a>Konfigurovat nastavení úložiště
Před zahájením zálohování, musíte nakonfigurovat nastavení úložiště pro váš server.


### <a name="to-configure-storage-settings"></a>Konfigurovat nastavení úložiště
1.  Na webu Azure portal > **nastavení**, klikněte na tlačítko **zálohování**.

    ![Zálohy v nastavení](./media/analysis-services-backup/aas-backup-backups.png)

2.  Klikněte na tlačítko **povoleno**, pak klikněte na tlačítko **nastavení úložiště**.

    ![Povolení](./media/analysis-services-backup/aas-backup-enable.png)

3. Vyberte svůj účet úložiště nebo vytvořte novou.

4. Vyberte kontejner nebo vytvořte novou.

    ![Výběr kontejneru](./media/analysis-services-backup/aas-backup-container.png)

5. Uložte nastavení zálohování.

    ![Uložit nastavení zálohování](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>Zálohování pomocí aplikace SSMS

1. V aplikaci SSMS klikněte pravým tlačítkem na databázi > **zálohování**.

2. V **příkaz Backup Database** > **záložní soubor**, klikněte na tlačítko **Procházet**.

3. V **uložit soubor jako** dialogového okna, ověřte cestu ke složce a pak zadejte název souboru zálohy. 

4. V **příkaz Backup Database** dialogové okno, vyberte možnosti.

    **Povolit soubor přepsat** – vyberte tuto možnost, chcete-li přepsat záložní soubory se stejným názvem. Pokud není vybraná tato možnost, soubor, který uložíte nemůže mít stejný název jako soubor, který již existuje ve stejném umístění.

    **Použít kompresi** – vyberte tuto možnost, chcete-li komprimovat soubor zálohy. Komprimované soubory zálohy ušetřit místo na disku, ale vyžadují mírně vyšší využití výkonu procesoru. 

    **Zašifrovat soubor zálohy** – tuto možnost použijte k šifrování souboru zálohy. Tato možnost vyžaduje uživatelem zadané heslo k zabezpečení záložní soubor. Heslo brání čtení zálohovaných dat jiným způsobem než operaci obnovení. Pokud budete chtít zálohy taky šifrovat, uložte heslo na bezpečné místo.

5. Klikněte na tlačítko **OK** vytvoříte a uložíte soubor zálohy.


### <a name="powershell"></a>PowerShell
Použití [zálohování ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet) rutiny.

## <a name="restore"></a>Obnovení
Při obnovení, musí být záložní soubor v účtu úložiště, které jste nakonfigurovali pro váš server. Pokud potřebujete přesunout záložní soubor z místního umístění do vašeho účtu úložiště, použijte [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) nebo [AzCopy](../storage/common/storage-use-azcopy.md) nástroj příkazového řádku. 



> [!NOTE]
> Pokud se obnovení z místního serveru, musíte odebrat všechny domény uživatele z role modelu a jejich přidání zpět do rolí jako uživatelů Azure Active Directory.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Chcete-li obnovit pomocí aplikace SSMS

1. V aplikaci SSMS klikněte pravým tlačítkem na databázi > **obnovení**.

2. V **příkaz Backup Database** dialogového okna v **záložní soubor**, klikněte na tlačítko **Procházet**.

3. V **vyhledejte soubory databáze** dialogového okna, vyberte soubor, který chcete provést obnovení.

4. V **obnovení databáze**, vyberte databázi.

5. Zadejte možnosti. Možnosti zabezpečení se musí shodovat možnosti zálohování, které jste použili při zálohování.


### <a name="powershell"></a>PowerShell

Použití [obnovení ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet) rutiny.


## <a name="related-information"></a>Související informace

[Účty služby Azure storage](../storage/common/storage-create-storage-account.md)  
[Vysoká dostupnost](analysis-services-bcdr.md)     
[Správa služby Azure Analysis Services](analysis-services-manage.md)
