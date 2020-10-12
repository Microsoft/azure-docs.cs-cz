---
title: Azure Analysis Services zálohování a obnovení databáze | Microsoft Docs
description: Tento článek popisuje, jak zálohovat a obnovit metadata modelu a data z databáze Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 66d09c2faa52cee3e94402be708d654b548c0de1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86506991"
---
# <a name="analysis-services-database-backup-and-restore"></a>Zálohování a obnovení databáze Analysis Services

Zálohování databází tabelárních modelů v Azure Analysis Services je v podstatě stejné jako u místních Analysis Services. Hlavním rozdílem je místo, kam ukládáte soubory zálohy. Záložní soubory musí být uložené do kontejneru v [účtu úložiště Azure](../storage/common/storage-create-storage-account.md). Můžete použít účet úložiště a kontejner, který už máte, nebo ho můžete vytvořit při konfiguraci nastavení úložiště pro váš server.

> [!NOTE]
> Vytvoření účtu úložiště může mít za následek novou fakturovatelnou službu. Další informace najdete v tématu [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

> [!NOTE]
> Pokud je účet úložiště v jiné oblasti, nakonfigurujte nastavení brány firewall účtu úložiště tak, aby povolovala přístup z **vybraných sítí**. V poli **Rozsah adres**brány firewall zadejte rozsah IP adres pro oblast, ve které je Server Analysis Services. Konfigurace nastavení brány firewall účtu úložiště tak, aby povolovala přístup ze všech sítí, ale výběr vybraných sítí a určení rozsahu IP adres jsou preferované. Další informace najdete v tématu [Nejčastější dotazy k síťovému připojení](analysis-services-network-faq.md#backup-and-restore).

Zálohy se ukládají s příponou. ABF. Pro tabulkové modely v paměti jsou uložená data modelů i metadata. Pro tabelární modely DirectQuery jsou uložena pouze metadata modelu. Zálohy se můžou komprimovat a šifrovat v závislosti na možnostech, které zvolíte.


## <a name="configure-storage-settings"></a>Konfigurace nastavení úložiště
Před zálohováním musíte nakonfigurovat nastavení úložiště pro váš server.


### <a name="to-configure-storage-settings"></a>Konfigurace nastavení úložiště
1.  V **nastavení**> Azure Portal klikněte na **zálohovat**.

    ![Zálohy v nastavení](./media/analysis-services-backup/aas-backup-backups.png)

2.  Klikněte na **povoleno**a pak na **Nastavení úložiště**.

    ![Povolit](./media/analysis-services-backup/aas-backup-enable.png)

3. Vyberte svůj účet úložiště nebo vytvořte nový.

4. Vyberte kontejner nebo vytvořte nový.

    ![Výběr kontejneru](./media/analysis-services-backup/aas-backup-container.png)

5. Uložte nastavení zálohování.

    ![Uložit nastavení zálohování](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>Zálohování pomocí SSMS

1. V SSMS klikněte pravým tlačítkem na databázi > **zálohovat**.

2. V záložním souboru zálohy **databáze**  >  **Backup file**klikněte na tlačítko **Procházet**.

3. V dialogovém okně **Uložit soubor jako** ověřte cestu ke složce a potom zadejte název záložního souboru. 

4. V dialogovém okně **zálohovat databázi** vyberte možnosti.

    **Povolení přepsání souboru** – tuto možnost vyberte, pokud chcete přepsat záložní soubory se stejným názvem. Pokud tato možnost není vybraná, soubor, který ukládáte, nemůže mít stejný název jako soubor, který už existuje ve stejném umístění.

    **Použít kompresi** – tuto možnost vyberte, když chcete zkomprimovat záložní soubor. Komprimované záložní soubory ukládají místo na disku, ale vyžadují mírně vyšší využití procesoru. 

    **Zašifrovat záložní soubor** – tuto možnost vyberte, pokud chcete záložní soubor zašifrovat. Tato možnost vyžaduje k zabezpečení záložního souboru heslo dodané uživatelem. Heslo zabraňuje čtení zálohovaných dat jiným způsobem než operace obnovení. Pokud se rozhodnete šifrovat zálohy, uložte heslo v bezpečném umístění.

5. Kliknutím na tlačítko **OK** vytvořte a uložte záložní soubor.


### <a name="powershell"></a>PowerShell
Použijte rutinu [Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase) .

## <a name="restore"></a>Obnovení
Při obnovení musí být váš záložní soubor v účtu úložiště, který jste nakonfigurovali pro váš server. Pokud potřebujete přesunout záložní soubor z místního umístění do svého účtu úložiště, použijte [Průzkumník služby Microsoft Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) nebo nástroj příkazového řádku [AzCopy](../storage/common/storage-use-azcopy.md) . 



> [!NOTE]
> Pokud provádíte obnovení z místního serveru, musíte odebrat všechny uživatele domény z rolí modelu a přidat je zpátky do rolí, jako Azure Active Directory uživatelé.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Obnovení pomocí SSMS

1. V SSMS klikněte pravým tlačítkem na databázi > **obnovení**.

2. V dialogovém okně **zálohovat databázi** klikněte v části **záložní soubor**na **Procházet**.

3. V dialogovém okně **Vyhledat soubory databáze** vyberte soubor, který chcete obnovit.

4. V části **obnovit databázi**vyberte databázi.

5. Zadejte možnosti. Možnosti zabezpečení se musí shodovat s možnostmi zálohování, které jste použili při zálohování.


### <a name="powershell"></a>PowerShell

Použijte rutinu [Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase) .


## <a name="related-information"></a>Související informace

[Účty služby Azure Storage](../storage/common/storage-create-storage-account.md)  
[Vysoká dostupnost](analysis-services-bcdr.md)      
[Nejčastější dotazy k Analysis Services připojení k síti](analysis-services-network-faq.md)