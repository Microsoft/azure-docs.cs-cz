---
title: Správa účtů úložiště Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak najít, spravovat, obnovit a získat účty úložiště Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 01/18/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: dda27c79a975ae0b22b51d0eb9b1dad3d9382acd
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103883"
---
# <a name="manage-storage-accounts-in-azure-stack"></a>Správa účtů úložiště ve službě Azure Stack

Zjistěte, jak spravovat účty úložiště ve službě Azure Stack najít, obnovit a získat kapacity úložiště na základě obchodních potřeb.

## <a name="find-a-storage-account"></a>Najít účet úložiště
Seznam účtů úložiště v oblasti lze zobrazit ve službě Azure Stack podle:

1. Přihlaste se k [portál pro správu](https://adminportal.local.azurestack.external).

2. Vyberte **všechny služby** > **účty úložiště**.

   ![](media/azure-stack-manage-storage-accounts/image4.png)

Ve výchozím nastavení se zobrazí prvních 10 účtů. Můžete také načíst informace kliknutím **načíst další** odkaz v dolní části seznamu.

NEBO

Pokud vás zajímají konkrétní účet úložiště – můžete **filtrovat a načítat příslušné účty** pouze.


**Chcete-li filtrovat účty:**

1. Vyberte **filtr** v horní části podokna.
2. V podokně filtru umožňuje zadat **název účtu**, ** ID předplatného, nebo **stav** a systém doladit seznam účtů úložiště, který se má zobrazit. Podle potřeby použijte je.
3. Vyberte **aktualizace**. V seznamu by měl aktualizovat odpovídajícím způsobem.
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. Resetovat filtr: vyberte **filtr**, vymažte výběry a aktualizovat.

Textové pole hledání (nahoře v podokně seznamu účtů úložiště) umožňuje zvýraznit text vybraný v seznamu účtů. To můžete použít, pokud úplný název nebo ID není snadno k dispozici.

To vám umožní najít účet, který vás zajímá můžete použít libovolný text tady.

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Podívejte se na Podrobnosti účtu
Po otevření účty, že máte zájem o zobrazení, můžete vybrat konkrétní účtu a zobrazit některé podrobnosti. Nové podokno otevře s podrobnostmi o účtu jako například: typ účtu, čas vytvoření, umístění atd.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Obnovení odstraněného účtu
Je možné v situaci, kdy potřebujete k obnovení odstraněného účtu.

Ve službě Azure Stack je jednoduchý způsob, jak to udělat:

1. Přejděte do seznamu účtů úložiště. Zobrazit [Najít účet úložiště](#find) v tomto článku pro další informace.
2. V seznamu vyhledejte konkrétního účtu. Budete muset filtrovat.
3. Zkontrolujte, *stavu* účtu. By mělo být uvedeno **odstraněné**.
4. Vyberte účet, který se otevře podokno Podrobnosti účtu.
5. Na tomto podokně vyhledejte **obnovit** tlačítko a vyberte ji.
6. Odstranění potvrďte výběrem **Ano**.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. Obnovení je teď v *proces... Počkejte* pro indikaci, že byla úspěšná.
   Můžete také vybrat ikonu "zvonku" v horní části portálu, chcete-li zobrazit průběh označení.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   Po úspěšné synchronizaci obnovené účet můžete znovu použít.

### <a name="some-gotchas"></a>Některé možná úskalí
* Odstraněný účet zobrazuje stav jako **mimo uchování**.
  
  Mimo uchování znamená, že odstraněného účtu byla překročena doba uchování a nemusí nepůjde obnovit.
* Odstraněný účet není uveden v seznamu účtů.
  
  Účet se nemusí zobrazit v seznamu účtů, když odstraněný účet již byl uvolněn z paměti. V tomto případě nelze obnovit. Zobrazit [uvolnit kapacity](#reclaim) v tomto článku.

## <a name="set-the-retention-period"></a>Nastavit dobu uchování.
Nastavení doby uchování umožňuje operátor cloudu k zadejte časové období ve dnech (0 až 9 999 dnů) během které je potenciálně obnovit všechny odstraněné účty. Výchozí dobu uchování je nastavena na 0 dnů. Nastavením této hodnoty na "0" znamená, že všechny odstraněný účet je okamžitě mimo uchovávání informací se označen pro pravidelné uvolňování paměti.

**Chcete-li změnit dobu uchování:**

1. Přihlaste se k [portál pro správu](https://adminportal.local.azurestack.external).
2. Vyberte **všechny služby** > **Správa oblastí** pod **správu**.
3. Vyberte **úložiště** z **poskytovatelů prostředků** seznamu.
4. Vyberte **nastavení** v horní části stránky a otevřete tak podokno nastavení.
5. Vyberte **konfigurace** pak upravte hodnotu doby uchování.

   Nastavte počet dní a pak ho uložte.
   
   Tato hodnota je hned platná a je nastavena pro celou oblast.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Uvolnit kapacity
Jednou z vedlejší efekty s dobou uchování je, že odstraněný účet i nadále spotřebovávají kapacitu, dokud jde o mimo dobu uchování. Jako operátor cloudu může potřebovat způsob, jak uvolnění místa odstraněný účet, i když dosud nepozbylo platnosti doby uchování.

Můžete získat zpět kapacitu pomocí portálu nebo Powershellu.

**Získat kapacitu pomocí portálu:**
1. Přejděte do podokna s účty úložiště. Zobrazit [Najít účet úložiště](#find).
2. Vyberte **uvolnění místa** v horní části podokna.
3. Přečte zprávu a pak vyberte **OK**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Počkejte oznámením o úspěšné najdete na ikonu zvonu na portálu.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Aktualizujte stránku, účty úložiště. Odstraněné účty jsou již zobrazit v seznamu, protože byla odstraněna.

Můžete také pomocí prostředí PowerShell explicitně přepsat doby uchování a okamžitě získat kapacity.

**Získat kapacitu pomocí prostředí PowerShell:**   

1. Potvrďte, že máte nainstalovat a nakonfigurovat Azure PowerShell. V opačném případě postupujte podle následujících pokynů: 
   * Nainstalujte nejnovější verzi Azure Powershellu a přidružte ji k vašemu předplatnému Azure, přečtěte si téma [instalace a konfigurace Azure Powershellu](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Další informace o rutiny Azure Resource Manageru najdete v tématu [pomocí Azure Powershellu s Azure Resource Manageru](https://go.microsoft.com/fwlink/?LinkId=394767)
2. Spuštěním následující rutiny:

> [!NOTE]  
> Pokud spustíte tyto rutiny, trvale odstraníte účet a její obsah. Se nedá vrátit zpátky. Toto používejte obezřetně.

```PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
```

Další informace najdete v tématu [dokumentace ke službě Azure Stack Powershellu](https://docs.microsoft.com/powershell/azure/azure-stack/overview).
 

## <a name="next-steps"></a>Další postup

 - Informace o správě oprávnění naleznete v tématu [řízení přístupu Manage Role-Based](azure-stack-manage-permissions.md).
 - Informace, spravovat kapacitu úložiště pro službu Azure Stack najdete v tématu [spravovat kapacitu úložiště pro službu Azure Stack](azure-stack-manage-storage-shares.md).