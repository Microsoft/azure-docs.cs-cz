---
title: Správa účtů úložiště Azure zásobníku | Microsoft Docs
description: Zjistěte, jak hledat, spravovat, obnovit a získat účty úložiště Azure zásobníku
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 627d355b-4812-45cb-bc1e-ce62476dab34
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/22/2018
ms.author: mabrigg
ms.reviewer: anirudha
ms.openlocfilehash: ce61dec785d6207e2d2df21884525f76a6778f4a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="manage-storage-accounts-in-azure-stack"></a>Správa účtů úložiště v Azure zásobníku
Zjistěte, jak chcete spravovat účty úložiště v Azure zásobníku najít, obnovit a opětovné využití kapacity úložiště na základě obchodních potřeb.

## <a name="find"></a>Najít účet úložiště
Seznam účtů úložiště v oblasti lze zobrazit v zásobníku Azure podle:

1. V internetovém prohlížeči, přejděte na https://adminportal.local.azurestack.external.
2. Přihlaste se k portálu pro správu Azure zásobníku jako operátor cloudu (pomocí přihlašovacích údajů, které jste zadali při nasazení)
3. Na řídicím panelu Výchozí – najít **oblast správy** seznamu a klikněte na tlačítko oblasti, které chcete prozkoumat, například **(místní**).
   
   ![](media/azure-stack-manage-storage-accounts/image1.png)
4. Vyberte **úložiště** z **zprostředkovatelé prostředků** seznamu.
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
5. Nyní, v podokně Správce poskytovatele prostředků úložiště – přejděte dolů na **účty úložiště** kartě a klikněte na něj.
   
   ![](media/azure-stack-manage-storage-accounts/image3.png)
   
   Výsledná stránka je seznam účtů úložiště v této oblasti.
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

Ve výchozím nastavení se zobrazí prvních 10 účtů. Můžete načíst informace kliknutím **načtěte více** odkaz v dolní části seznamu.

NEBO

Pokud vás zajímají konkrétní účet úložiště – můžete **filtrovat a načtení příslušné účty** pouze.


**Chcete-li filtrovat účty:**

1. Klikněte na tlačítko **filtru** v horní části podokna.
2. V podokně filtru umožňuje, abyste zadali **název účtu**, ** ID předplatného, nebo **stav** a systém doladit seznam účtů úložiště, který se má zobrazit. Použijte je podle potřeby.
3. Klikněte na tlačítko **aktualizace**. V seznamu by měl aktualizovat odpovídajícím způsobem.
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. Chcete-li obnovit filtr: klikněte na tlačítko **filtru**, vymažte výběr a aktualizovat.

Do vyhledávacího pole text (horní podokno seznam účtů úložiště) umožňuje zvýrazněte v seznamu účtů vybraný text. To můžete použít, pokud úplný název nebo ID není snadno dostupné.

Můžete zde volné vám pomohou najít účet, který vás zajímá.

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Podívejte se na podrobnosti o účtu
Po vyhledání účty, že máte zájem zobrazení, klikněte na tlačítko konkrétní účet, který chcete zobrazit některé podrobnosti. Nové podokno otevře s podrobnostmi o účtu, jako: typ účtu, čas vytvoření, umístění atd.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Obnovení odstraněného účtu
Může být v situaci, kdy potřebujete obnovit odstraněného účtu.

V zásobníku Azure je jednoduchý způsob, jak to udělat:

1. Přejděte do seznamu účtů úložiště. V tématu [Najít účet úložiště](#find) v tomto tématu pro další informace.
2. V seznamu vyhledejte konkrétního účtu. Můžete filtrovat.
3. Zkontrolujte *stavu* účtu. By mělo být uvedeno **odstraněné**.
4. Klikněte na účet, který se otevře v podokně podrobností účtu.
5. V tomto podokně vyhledejte **obnovit** tlačítko a klikněte na něj.
6. Pro potvrzení klikněte na tlačítko **Ano**.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. Obnovení je teď ve *zpracovat... Počkejte* pro indikaci, že byla úspěšná.
   Můžete také kliknutím na ikonu "zvonku" v horní části portálu zobrazíte průběh označení.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   Po úspěšné synchronizaci obnovené účet můžete znovu použít.

### <a name="some-gotchas"></a>Některé Gotchas
* Odstraněného účtu ukazuje stav jako **mimo uchování**.
  
  Mimo znamená uchování, odstraněného účtu byla překročena doba uchování a nemusí být použitelná pro obnovení.
* Odstraněného účtu se nezobrazuje v seznamu účtů.
  
  Účet je nemusí zobrazit v seznamu účet při odstraněného účtu již bylo uvolnění z paměti. V takovém případě nelze obnovit. V tématu [opětovné využití kapacity](#reclaim) v tomto tématu.

## <a name="set-the-retention-period"></a>Nastavit dobu uchování.
Nastavení doby uchování umožňuje operátor cloudu k zadejte časové období ve dnech (0 až 9 999 dnů), během které potenciálně lze obnovit všechny odstraněné účtu. Výchozí dobu uchování nastavena na 0 dní. Nastavení hodnoty "0" znamená, že všechny odstraněné účet je okamžitě mimo uchovávání a označen pro pravidelné uvolňování paměti.

**Chcete-li změnit dobu uchování:**

1. V internetovém prohlížeči, přejděte na https://adminportal.local.azurestack.external.
2. Přihlaste se k portálu pro správu Azure zásobníku jako operátor cloudu (pomocí přihlašovacích údajů, které jste zadali při nasazení)
3. Na řídicím panelu Výchozí – najít **oblast správy** seznamu a klikněte na tlačítko oblasti, které chcete prozkoumat – například **(místní**).
4. Vyberte **úložiště** z **zprostředkovatelé prostředků** seznamu.
5. Klikněte na tlačítko **nastavení** v horní části a otevřete podokno nastavení.
6. Klikněte na tlačítko **konfigurace** pak upravte hodnotu doby uchování.

   Nastavte počet dní a pak ho uložte.
   
   Tato hodnota je hned platná a je nastaven pro vaši celou oblast.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Opětovné využití kapacity
Jedním z důsledky straně s dobou uchování je, že odstraněného účtu nadále spotřebovávají kapacitu, dokud pochází mimo dobu uchování. Jako operátor cloudu může být nutné způsob, jak uvolnění místa odstraněného účtu, i když ještě nevypršela dobu uchování.

Můžete získat kapacity buď pomocí portálu nebo pomocí prostředí PowerShell.

**Pro opětovné využití kapacity pomocí portálu:**
1. Přejděte do podokna účty úložiště. V tématu [Najít účet úložiště](#find).
2. Klikněte na tlačítko **uvolnění místa** v horní části podokna.
3. Tuto zprávu přečíst a pak klikněte na **OK**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Počkejte, než pro úspěch oznámení najdete na ikonu zvonku na portálu.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Aktualizujte stránku účty úložiště. Odstraněné účty jsou už uvedené v seznamu, protože byla odstraněna.

Můžete také pomocí prostředí PowerShell explicitně přepsat dobu uchování a okamžitě opětovné využití kapacity.

**Pro opětovné využití kapacity pomocí prostředí PowerShell:**   

1. Potvrďte, že máte Azure PowerShell nainstalovaný a nakonfigurovaný. Pokud ne, postupujte podle následujících pokynů: 
   * Nainstalujte nejnovější verzi prostředí Azure PowerShell a přidružit ho ke svému předplatnému Azure, najdete v tématu [postup instalace a konfigurace prostředí Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Další informace o rutinách Azure Resource Manager najdete v tématu [použití Azure Powershellu s Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkId=394767)
2. Spusťte následující rutinu:

> [!NOTE]
> Pokud tuto rutinu spustit, trvale odstraníte účet a její obsah. Není použitelná pro obnovení. Použijte dát pozor.


        Clear-ACSStorageAccount -ResourceGroupName system.local -FarmName <farm ID>


Další informace najdete v tématu [zásobník Azure powershell dokumentaci.](https://msdn.microsoft.com/library/mt637964.aspx)
 

## <a name="migrate-a-container"></a>Migrace kontejner
Z důvodu nerovnoměrné úložiště používané klienty operátor cloudu může být jeden nebo více základní klienta sdílených složek pomocí více místa než jiné. Pokud k tomu dojde, operátor cloudu se může pokusit uvolněte místo na disku ve sdílené složce přízvukový ručně migrací některé kontejnery objektů blob do jiné složky. 

Musíte použít PowerShell k migraci kontejnery.
> [!NOTE]
>Migrace kontejneru objektů BLOB nepodporuje migraci za provozu a aktuálně je offline operace. Během migrace a dokud se nedokončí základní objektů BLOB v kontejneru nelze použít a jsou "offline". 

**Pokud chcete migrovat kontejnery pomocí prostředí PowerShell:**

1. Potvrďte, že máte Azure PowerShell nainstalovaný a nakonfigurovaný. Pokud ne, postupujte podle následujících pokynů:
    * Nainstalujte nejnovější verzi prostředí Azure PowerShell a přidružit ho ke svému předplatnému Azure, najdete v tématu [postup instalace a konfigurace prostředí Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/). Další informace o rutinách Azure Resource Manager najdete v tématu [použití Azure Powershellu s Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkId=394767)
2. Získáte název farmy: 
      
      `$farm = Get-ACSFarm -ResourceGroupName system.local`
3. Získáte sdílené složky: 

   `$shares = Get-ACSShare -ResourceGroupName system.local -FarmName $farm.FarmName`

4. Získáte kontejnery pro danou sdílenou složku. Všimněte si, že počet a záměr jsou volitelné parametry:
            
   `$containers = Get-ACSContainer -ResourceGroupName system.local -FarmName $farm.FarmName -ShareName $shares[0].ShareName -Count 4 -Intent Migration`  

   Potom si prohlédněte $containers:

   `$containers`

    ![](media/azure-stack-manage-storage-accounts/image13.png)
5. Získáte nejlepší cílové složky pro migraci kontejneru:

    `$destinationshares= Get-ACSSharesForMigration  -ResourceGroupName system.local -FarmName $farm.farmname -SourceShareName $shares[0].ShareName`

    Potom si prohlédněte $destinationshares:

    `$destinationshares`

    ![](media/azure-stack-manage-storage-accounts/image14.png)
6. Ji migrace pro kontejner, Všimněte si, že toto je implementaci asynchronní, takže jeden cykly všechny kontejnery ve sdílené složce a sledovat stav pomocí ID vrácený úlohy.

    `$jobId = Start-ACSContainerMigration -ResourceGroupName system.local -FarmName $farm.farmname -ContainerToMigrate $containers[1] -DestinationShareUncPath $destinationshares.UncPath`

    Potom si prohlédněte $jobId:

   ```
   $jobId
   d1d5277f-6b8d-4923-9db3-8bb00fa61b65
   ```
7. Zkontrolujte stav úlohy migrace podle jeho ID úlohy. Po dokončení migrace kontejneru MigrationStatus je nastaven na "Dokončeno".

    `Get-ACSContainerMigrationStatus -ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image15.png)

8. Můžete zrušit úlohu v průběhu migrace. Toto znovu je asynchronní operace a lze sledovat pomocí $jobid:

    `Stop-ACSContainerMigration-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId-Verbose`

    ![](media/azure-stack-manage-storage-accounts/image16.png)

    Můžete zkontrolovat, že se stavy migrace zrušit znovu:

    `Get-ACSContainerMigrationStatus-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image17.png)




  
  
