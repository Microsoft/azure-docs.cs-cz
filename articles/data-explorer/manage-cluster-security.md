---
title: Zabezpečení clusteru v Azure Průzkumník dat
description: Tento článek popisuje, jak zabezpečit cluster v Azure Průzkumník dat v rámci Azure Portal.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 0f935999b68a7283c032d43c42d688b273d5c450
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720340"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>Zabezpečení clusteru v Azure Průzkumník dat – Azure Portal

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) pomáhá chránit a chránit vaše data, aby splňovala závazky zabezpečení vaší organizace a dodržování předpisů. Poskytuje šifrování svazku pro operační systém a datové disky virtuálních počítačů clusteru. Také se integruje s [Azure Key Vault](/azure/key-vault/), což nám umožňuje řídit a spravovat klíče a tajné kódy disku a zajistit, aby byla všechna data na discích virtuálních počítačů šifrovaná. 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>Povolit šifrování v klidovém Azure Portal
  
Nastavení zabezpečení clusteru umožňuje povolit šifrování disku v clusteru. Povolení [šifrování v klidovém](/azure/security/fundamentals/encryption-atrest) clusteru zajišťuje ochranu dat pro uložená data (v klidovém prostředí). 

1. V Azure Portal přejdete do svého prostředku clusteru Azure Průzkumník dat. V záhlaví **Nastavení** vyberte **zabezpečení**. 

    ![Zapnout šifrování v klidovém umístění](media/manage-cluster-security/security-encryption-at-rest.png)

1. V okně **zabezpečení** vyberte pro nastavení zabezpečení **šifrování disku** možnost **zapnuto** . 

1. Vyberte **Uložit**.
 
> [!NOTE]
> Výběrem **vypnuto** zakážete šifrování po jeho povolení.

## <a name="next-steps"></a>Další kroky

[Zkontroluje stav clusteru.](/azure/data-explorer/check-cluster-health)
