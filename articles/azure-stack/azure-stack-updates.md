---
title: Správa aktualizací v přehledu služby Azure Stack | Dokumentace Microsoftu
description: Další informace o správě aktualizací pro integrované systémy Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: mabrigg
ms.lastreviewed: 04/04/2019
ms.reviewer: justini
ms.openlocfilehash: bf797404b173d6febe133eacbb9d36310160aff6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281569"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Správa aktualizací v přehledu služby Azure Stack

*Týká se Integrované systémy pro službu Azure Stack*

Balíčky aktualizací Microsoftu pro Azure Stack integrované systémy obvykle release kolem čtvrtý úterý v měsíci. Výrobce vašeho (výrobcem OEM) požádejte o proces jejich konkrétní upozornění k zajištění oznámení o aktualizacích kontaktovat vaše organizace. Můžete zkontrolovat v této knihovně dokumentace v části **přehled** > **poznámky k verzi** informace o vydaných verzích, které jsou v aktivní technické podpory. 

Každá verze aktualizace softwaru Microsoftu se dodává v sadě jako jednu aktualizaci balíčku. Jako operátory Azure stacku můžete importovat, instalace a sledování průběhu instalace těchto balíčků z portálu správce aktualizací. 

Od dodavatele OEM také vydá aktualizace, jako je například aktualizace ovladače a firmware. Zatímco tyto aktualizace jsou dodávány jako samostatné balíčky podle dodavatele, že jsou importovat, nainstalované a spravovat stejným způsobem jako balíčky aktualizací od Microsoftu.

Aby váš systém v rámci podpory, je nutné zachovat aktualizovat na úroveň konkrétní verzi služby Azure Stack. Ujistěte se, abyste se seznámili [Azure Stack zásady obsluhy](azure-stack-servicing-policy.md).

> [!NOTE]
> Balíčky aktualizací pro Azure Stack nelze použít pro Azure Stack Development Kit. Balíčky aktualizací jsou navržené pro integrované systémy. Informace najdete v tématu [opětovné nasazení ASDK](https://docs.microsoft.com/azure/azure-stack/asdk).

## <a name="the-update-resource-provider"></a>Aktualizace poskytovatele prostředků

Azure Stack zahrnuje poskytovatele prostředků aktualizace, která zpracovává použití aktualizací softwaru společnosti Microsoft. Tento zprostředkovatel ověří, že se aktualizace napříč všechny fyzické hostitele, aplikace Service Fabric a moduly runtime a všechny virtuální počítače infrastruktury a jejich přidružené služby.

Jak nainstalovat aktualizace, můžete zobrazit informace na nejvyšší úrovni jako cíle procesu aktualizace různé subsystémy ve službě Azure Stack (například fyzické hostitele a infrastrukturu virtuálních počítačů).

## <a name="plan-for-updates"></a>Plán pro aktualizace

Důrazně doporučujeme, upozornění uživatelů na jakékoli operace údržby a, abyste naplánovali normální správu a údržbu během mimo pracovní dobu Pokud je to možné. Operace údržby může ovlivnit úlohy klientů a operací na portálu.

- Před instalací této aktualizace, spusťte [testovací AzureStack](azure-stack-diagnostic-test.md) s následujícími parametry do ověřte stav služby Azure Stack a vyřešte všechny provozní problémy zjištěné, včetně všech upozornění a chyby. Také aktivní výstrahy můžete zkontrolovat a vyřešit všechny, které vyžadují nějakou akci.  

  ```powershell
  Test-AzureStack -Group UpdateReadiness
  ``` 

## <a name="using-the-update-tile-to-manage-updates"></a>Správa aktualizací pomocí aktualizovat dlaždici

Správa aktualizací z portálu správce. Jako operátory Azure stacku můžete aktualizovat dlaždici v řídicím panelu:

- zobrazit důležité informace, jako je aktuální verze.
- instalace aktualizací a sledovat průběh.
- Kontrola historie aktualizace pro dříve nainstalované aktualizace.
- Zobrazit aktuální verze balíčku výrobce OEM v cloudu
 
## <a name="determine-the-current-version"></a>Zjistit aktuální verzi

Na dlaždici aktualizace můžete zobrazit aktuální verzi služby Azure Stack. Otevřete dlaždici:

1. Otevřete na portálu pro správu Azure Stack.
2. Vyberte **řídicí panel**. V **aktualizace** dlaždice, aktuální verze je uvedená. 

    ![Aktualizace dlaždice na výchozí řídicí panel](./media/azure-stack-updates/image1.png)

    Verze je na obrazovce 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Instalovat aktualizace a sledování průběhu


1. Otevřete na portálu pro správu Azure Stack.
2. Vyberte **řídicí panel**. Vyberte aktualizovat dlaždici.
3. Vyberte **aktualizovat**.

    ![Podrobnosti o spuštění aktualizace Azure Stack](media/azure-stack-updates/azure-stack-update-button.png)

4.  Si můžete zobrazit podrobný stav procesu aktualizace prochází různé subsystémy ve službě Azure Stack. Příklad subsystémy zahrnují fyzické hostitele, Service Fabric, infrastruktury virtuálních počítačů a služeb, které poskytují portálech pro správce i uživatele. Aktualizace poskytovatele prostředků v celém procesu aktualizace sestavy další podrobnosti o aktualizaci, jako je například počet kroků, které byly úspěšné, stejně jako číslo v průběhu.

5. Vyberte **stáhnout úplných protokolů** z aktualizace spusťte okno s podrobnostmi o stažení úplných protokolů.

    ![Podrobnosti o spuštění aktualizace Azure Stack](media/azure-stack-updates/update-run-details.png)

6. Po dokončení aktualizace poskytovatel prostředků nabízí **Succeeded** potvrzení s oznámením, že dokončení procesu aktualizace a jak dlouho to trvalo. Odtud můžete zobrazit informace o aktualizace, aktualizace nebo nainstalovaných aktualizací pomocí filtru, který.

    ![Aktualizace Azure zásobníku spouštění podrobnosti o úspěchu](media/azure-stack-updates/update-success.png)

   Pokud se aktualizace nezdaří, aktualizace dlaždice sestavy **vyžaduje pozornost**. Použití **stáhnout úplných protokolů** získat podrobný stav kde aktualizace se nezdařila. Shromažďování protokolů služby Azure Stack pomáhá usnadnit diagnostiku a řešení potíží.

## <a name="next-steps"></a>Další postup

- [Údržba zásad služby Azure Stack](azure-stack-servicing-policy.md) 
- [Správa oblastí ve službě Azure Stack](azure-stack-region-management.md)
