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
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: 2ae2b628b2e61893a5289151c3b405e7412e7d13
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076907"
---
# <a name="manage-storage-accounts-in-azure-stack"></a>Správa účtů úložiště v Azure zásobníku
Zjistěte, jak chcete spravovat účty úložiště v Azure zásobníku najít, obnovit a opětovné využití kapacity úložiště na základě obchodních potřeb.

## <a name="find"></a>Najít účet úložiště
Seznam účtů úložiště v oblasti lze zobrazit v zásobníku Azure podle:

1. V internetovém prohlížeči, přejděte na https://adminportal.local.azurestack.external.
2. Přihlaste se k portálu pro správu Azure zásobníku jako operátor cloudu (pomocí přihlašovacích údajů, které jste zadali při nasazení)
3. Na řídicím panelu Výchozí – najít **oblast správy** seznam a vyberte oblast, kterou chcete prozkoumat, například **(místní**).
   
   ![](media/azure-stack-manage-storage-accounts/image1.png)
4. Vyberte **úložiště** z **zprostředkovatelé prostředků** seznamu.
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
5. Nyní, v podokně Správce poskytovatele prostředků úložiště – přejděte dolů na **účty úložiště** kartě a vyberte ho.
   
   ![](media/azure-stack-manage-storage-accounts/image3.png)
   
   Výsledná stránka je seznam účtů úložiště v této oblasti.
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

Ve výchozím nastavení se zobrazí prvních 10 účtů. Můžete načíst informace kliknutím **načtěte více** odkaz v dolní části seznamu.

NEBO

Pokud vás zajímají konkrétní účet úložiště – můžete **filtrovat a načtení příslušné účty** pouze.


**Chcete-li filtrovat účty:**

1. Vyberte **filtru** v horní části podokna.
2. V podokně filtru umožňuje, abyste zadali **název účtu**, ** ID předplatného, nebo **stav** a systém doladit seznam účtů úložiště, který se má zobrazit. Použijte je podle potřeby.
3. Vyberte **aktualizace**. V seznamu by měl aktualizovat odpovídajícím způsobem.
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. Chcete-li obnovit filtr: vyberte **filtru**, vymažte výběr a aktualizovat.

Do vyhledávacího pole text (horní podokno seznam účtů úložiště) umožňuje zvýrazněte v seznamu účtů vybraný text. To můžete použít, pokud úplný název nebo ID není snadno dostupné.

Můžete zde volné vám pomohou najít účet, který vás zajímá.

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Podívejte se na podrobnosti o účtu
Po vyhledání účty, že máte zájem zobrazení, můžete vybrat konkrétní účet, který chcete zobrazit některé podrobnosti. Nové podokno otevře s podrobnostmi o účtu, jako: typ účtu, čas vytvoření, umístění atd.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Obnovení odstraněného účtu
Může být v situaci, kdy potřebujete obnovit odstraněného účtu.

V zásobníku Azure je jednoduchý způsob, jak to udělat:

1. Přejděte do seznamu účtů úložiště. V tématu [Najít účet úložiště](#find) v tomto tématu pro další informace.
2. V seznamu vyhledejte konkrétního účtu. Můžete filtrovat.
3. Zkontrolujte *stavu* účtu. By mělo být uvedeno **odstraněné**.
4. Vyberte účet, který se otevře v podokně podrobností účtu.
5. V tomto podokně vyhledejte **obnovit** tlačítko a vyberte ho.
6. Vyberte **Ano** k potvrzení.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. Obnovení je teď ve *zpracovat... Počkejte* pro indikaci, že byla úspěšná.
   Můžete také vybrat na ikonu "zvonku" v horní části portálu zobrazíte průběh označení.
   
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
3. Na řídicím panelu Výchozí – najít **oblast správy** seznam a vyberte oblast, kterou chcete prozkoumat – například **(místní**).
4. Vyberte **úložiště** z **zprostředkovatelé prostředků** seznamu.
5. Vyberte **nastavení** v horní části a otevřete podokno nastavení.
6. Vyberte **konfigurace** pak upravte hodnotu doby uchování.

   Nastavte počet dní a pak ho uložte.
   
   Tato hodnota je hned platná a je nastaven pro vaši celou oblast.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Opětovné využití kapacity
Jedním z důsledky straně s dobou uchování je, že odstraněného účtu nadále spotřebovávají kapacitu, dokud pochází mimo dobu uchování. Jako operátor cloudu může být nutné způsob, jak uvolnění místa odstraněného účtu, i když ještě nevypršela dobu uchování.

Můžete získat kapacity buď pomocí portálu nebo pomocí prostředí PowerShell.

**Pro opětovné využití kapacity pomocí portálu:**
1. Přejděte do podokna účty úložiště. V tématu [Najít účet úložiště](#find).
2. Vyberte **uvolnění místa** v horní části podokna.
3. Tuto zprávu přečíst a pak vyberte **OK**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Počkejte, než pro úspěch oznámení najdete na ikonu zvonku na portálu.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Aktualizujte stránku účty úložiště. Odstraněné účty jsou už uvedené v seznamu, protože byla odstraněna.

Můžete také pomocí prostředí PowerShell explicitně přepsat dobu uchování a okamžitě opětovné využití kapacity.

**Pro opětovné využití kapacity pomocí prostředí PowerShell:**   

1. Potvrďte, že máte Azure PowerShell nainstalovaný a nakonfigurovaný. Pokud ne, postupujte podle následujících pokynů: 
   * Nainstalujte nejnovější verzi prostředí Azure PowerShell a přidružit ho ke svému předplatnému Azure, najdete v tématu [postup instalace a konfigurace prostředí Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Další informace o rutinách Azure Resource Manager najdete v tématu [použití Azure Powershellu s Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkId=394767)
2. Spusťte následující rutiny:

> [!NOTE]
> Pokud tyto rutiny spustit, trvale odstraníte účet a její obsah. Není použitelná pro obnovení. Použijte dát pozor.

```PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
````

Další informace najdete v tématu [dokumentace Azure PowerShell zásobníku.](https://msdn.microsoft.com/library/mt637964.aspx)
 

## <a name="next-steps"></a>Další postup

 - Informace o správě oprávnění najdete v části [řízení přístupu Manage Role-Based](azure-stack-manage-permissions.md).
 - Informace na spravovat kapacita úložiště pro zásobník Azure najdete v tématu [spravovat kapacita úložiště pro Azure zásobníku](azure-stack-manage-storage-shares.md).