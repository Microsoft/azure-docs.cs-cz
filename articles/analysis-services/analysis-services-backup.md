---
title: Zálohování a obnovení databáze služby Azure Analysis Services | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak zálohovat a obnovovat metadata modelu a data z databáze Služby Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3f63ecf560a14248fed6dea53c30a27acdf9a938
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573453"
---
# <a name="backup-and-restore"></a>Zálohování a obnovení

Zálohování databází tabulkových modelů ve službě Azure Analysis Services je téměř stejné jako u místních analysis services. Hlavním rozdílem je, kde ukládáte záložní soubory. Záložní soubory musí být uloženy do kontejneru v [účtu úložiště Azure](../storage/common/storage-create-storage-account.md). Můžete použít účet úložiště a kontejner, který již máte, nebo je lze vytvořit při konfiguraci nastavení úložiště pro váš server.

> [!NOTE]
> Vytvoření účtu úložiště může mít za následek novou fakturovatelnou službu. Další informace najdete v [tématu Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

Zálohy jsou uloženy s příponou .abf. Pro tabulkové modely v paměti jsou uložena data modelu i metadata. U tabulkových modelů DirectQuery jsou uložena pouze metadata modelu. Zálohy mohou být komprimovány a šifrovány v závislosti na možnostech, které zvolíte.


## <a name="configure-storage-settings"></a>Konfigurace nastavení úložiště
Před zálohováním je třeba nakonfigurovat nastavení úložiště pro server.


### <a name="to-configure-storage-settings"></a>Konfigurace nastavení úložiště
1.  V > **nastavení**portálu Azure klikněte na **Zálohování**.

    ![Zálohy v nastavení](./media/analysis-services-backup/aas-backup-backups.png)

2.  Klepněte na **tlačítko Povoleno**a potom klepněte na **položku Nastavení úložiště**.

    ![Povolení](./media/analysis-services-backup/aas-backup-enable.png)

3. Vyberte svůj účet úložiště nebo vytvořte nový.

4. Vyberte kontejner nebo vytvořte nový.

    ![Výběr kontejneru](./media/analysis-services-backup/aas-backup-container.png)

5. Uložte nastavení zálohování.

    ![Uložení nastavení zálohování](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Zálohování

### <a name="to-backup-by-using-ssms"></a>Zálohování pomocí SSMS

1. V ssms klepněte pravým tlačítkem myši na databázi > **Zálohovat**.

2. V **souboru Backup Database** > **Backup**klepněte na **tlačítko Procházet**.

3. V dialogovém okně **Uložit soubor jako** ověřte cestu ke složce a zadejte název záložního souboru. 

4. V dialogovém okně **Zálohovat databázi** vyberte možnosti.

    **Povolit přepsání souboru** – Tuto možnost vyberte, chcete-li přepsat záložní soubory se stejným názvem. Pokud tato možnost není vybrána, soubor, který ukládáte, nemůže mít stejný název jako soubor, který již existuje ve stejném umístění.

    **Použít kompresi** – Tuto možnost vyberte, chcete-li komprimovat záložní soubor. Komprimované záložní soubory šetří místo na disku, ale vyžadují o něco vyšší využití procesoru. 

    **Šifrovat záložní soubor** – Tuto možnost vyberte, chcete-li záložní soubor zašifrovat. Tato možnost vyžaduje k zabezpečení záložního souboru heslo zadané uživatelem. Heslo zabraňuje čtení záložních dat jiným i jiným způsobem než operaci obnovení. Pokud se rozhodnete šifrovat zálohy, uložte heslo na bezpečném místě.

5. Chcete-li vytvořit a uložit záložní soubor, klepněte na tlačítko **OK.**


### <a name="powershell"></a>PowerShell
Použijte [rutinu Zálohování ASDatabase.](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)

## <a name="restore"></a>Obnovení
Při obnovování musí být záložní soubor v účtu úložiště, který jste nakonfigurovali pro server. Pokud potřebujete přesunout záložní soubor z místního umístění do vašeho účtu úložiště, použijte [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) nebo nástroj příkazového řádku [AzCopy.](../storage/common/storage-use-azcopy.md) 



> [!NOTE]
> Pokud obnovujete z místního serveru, musíte odebrat všechny uživatele domény z rolí modelu a přidat je zpět do rolí jako uživatelé služby Azure Active Directory.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Obnovení pomocí SSMS

1. V ssms klepněte pravým tlačítkem myši na databázi > **obnovit**.

2. V dialogovém okně **Zálohovat databázi** klepněte v **okně Záložní soubor**na tlačítko **Procházet**.

3. V dialogovém okně **Vyhledat soubory databáze** vyberte soubor, který chcete obnovit.

4. V **části Obnovit databázi**vyberte databázi.

5. Určete volby. Možnosti zabezpečení musí odpovídat možnostem zálohování, které jste použili při zálohování.


### <a name="powershell"></a>PowerShell

Použijte [rutinu Obnovení ASDatabáze.](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)


## <a name="related-information"></a>Související informace

[Účty služby Azure Storage](../storage/common/storage-create-storage-account.md)  
[Vysoká dostupnost](analysis-services-bcdr.md)     
[Správa služby Azure Analysis Services](analysis-services-manage.md)
