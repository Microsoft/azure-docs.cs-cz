---
title: Prozkoumat uživatelů pomocí analýzy chování uživatelů ve verzi Preview Sentinelu Azure | Dokumentace Microsoftu
description: Další informace o tom, jak zjistit uživatelů pomocí analýzy chování uživatelů v Azure Sentinelu.
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 5b06aef0-16be-4ca0-83e4-a33795a82353
ms.service: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: b9944764345f97c561f3f82f9cce9d37e204f134
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992975"
---
# <a name="investigate-users-with-user-analytics"></a>Prozkoumat uživatelů pomocí analýzy chování uživatelů

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Uživatelé jsou entity, které chcete celý proces bedlivě sledujte. Se kterými získáte přehled o vašich uživatelů, Sentinelu Azure hladce integrují s Azure Advanced Threat Protection. Tato integrace umožňuje analyzovat chování uživatelů a stanovení priorit uživatelů, které byste měli prozkoumat nejdříve, na základě jejich výstrahy a vzory podezřelých aktivit v Azure Sentinelu a Microsoft 365.

Azure Sentinel vylepšuje uživatelská data pomocí analýz z Microsoft 365 umožňující analýza rizika a analýzu komplexní uživatelů pro všechny uživatele ve službě Azure Active Directory. 

## <a name="how-it-works"></a>Jak to funguje

1. Na základě zabezpečení je detekován analytické pravidla, je-li shodu, Sentinelu Azure odesílá výstrahy do služby Azure ATP.
1. Ochrana ATP v programu Azure ověří uživatele entit, které se vztahují k upozornění a vypočítá šetření prioritu pro tyto uživatele.
3. Ochrana ATP v programu Azure přepočítá skóre uživatelů po se rozšiřují o data z pravidla analytics pro Azure Sentinelu.


## <a name="prerequisites"></a>Požadavky

- Licence k rozšířené ochrany před internetovými útoky pro Azure 
- Chcete-li povolit tuto funkci, potřebujete oprávnění globálního správce pro tenanta, do které jste nainstalovali ověřovací Azure

> [!NOTE]
> - Pro každého klienta ochrany ATP v programu Azure se můžete připojit jednu instanci Sentinelu Azure.
> - Analýzy chování uživatelů je momentálně dostupná jenom pro uživatele Azure Active Directory. 

## <a name="enable-user-analytics"></a>Povolení analýzy chování uživatelů

1. Povolení analýzy chování uživatelů v ověřovacích Azure na portálu přejděte do **analýzy chování uživatelů** stránce a klikněte na tlačítko **povolit**. Tím se odešle informace o pracovním prostoru do služby Azure ATP.

1. Potom tím přejdete do služby Azure ATP. V části **rozšíření zabezpečení** v **Microsoft Azure Sentinelu** klikněte na tlačítko **+ plus** přidat a pak vyberte pracovní prostor. 
1. Klikněte na **Připojit**.

## <a name="investigate-a-user"></a>Prozkoumat uživatele

1. V nabídce Azure Sentinelu **analýzy chování uživatelů**, projděte si seznam uživatelů podle jejich priority šetření. Skóre je založen na Azure Sentinelu výstrahy a oznámení Microsoft 365.

2. Vyhledat uživatele, který chcete prozkoumat. Kliknutím na uživatele zobrazíte stránku uživatele. Zkontrolujte výstrahy a aktivity uživatele v průběhu času na časové ose. Můžete zobrazit všechny aktivity ze služeb Microsoft 365 a Azure Sentinelu. Na stránce uživatel také dostanete procházení k podrobnostem uživatelům v případě.
      
    ![Uživatelé](./media/user-analytics/user-investigation.png)

 
3. Aby to fungovalo správně, máte správně nastavený [vlastní pravidlo výstrah](tutorial-detect-threats.md) mapovat správné uživatelské identifikátory **účet** entity.

    - Pro události Windows, mapování **účet** k **SID**
    - Azure AD data (který se nachází v mnoha protokoly aktivit Azure včetně) nebo dat Office 365, mapování **účet** vlastnost **GUID**, nebo **hlavní název uživatele**. 

   ![Dotaz](./media/user-analytics/query-window.png)



Příklad: 
> [!NOTE]
> V protokolech aktivit Azure aktivity entita volající obsahuje hlavní název uživatele.

1. Tento dotaz vyhledává vytváření neobvyklé počet prostředků nebo nasazení aktivity v protokolu aktivit Azure.

        AzureActivity      
        | where TimeGenerated >= startofday(ago(7d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(7d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope > 0.2        
        | join kind=leftsemi (        
        // Last day's activity is anomalous        
        AzureActivity        
        | where TimeGenerated >= startofday(ago(1d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(1d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope >0.2        
        ) on Caller        
        | extend AccountCustomEntity = Caller
 
2. Ve vlastní pravidlo výstrah, mapování **účet** vlastnost **volající**.
   
   ![Dotaz](./media/user-analytics/query-window.png)

3. Prozkoumejte uživatele v okně uživatele šetření. Rady o tom, jak prozkoumat uživatele, najdete v části [kurzu: Prozkoumat uživatele](https://docs.microsoft.com/azure-advanced-threat-protection/investigate-a-user).



## <a name="next-steps"></a>Další postup

V tomto dokumentu jste zjistili, jak připojit poskytovatele analýzy hrozeb v Azure Sentinelu. Další informace o Azure Sentinelu, naleznete v následujících článcích.

- Abyste mohli začít s Azure Sentinelu, budete potřebovat předplatné Microsoft Azure. Pokud předplatné nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
- Zjistěte, jak [připojit vaše data do Azure Sentinelu](quickstart-onboard.md), a [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
