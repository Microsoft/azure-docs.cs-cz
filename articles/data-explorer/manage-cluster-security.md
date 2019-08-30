---
title: Zabezpečení clusteru v Azure Průzkumník dat
description: Tento článek popisuje, jak zabezpečit cluster v Azure Průzkumník dat v rámci Azure Portal.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: c6f7e921886a6acdaa31d0f69f57119c339c0b8b
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172599"
---
# <a name="secure-your-cluster-in-azure-data-explorer"></a>Zabezpečení clusteru v Azure Průzkumník dat

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) pomáhá chránit a chránit vaše data, aby splňovala závazky zabezpečení vaší organizace a dodržování předpisů. Poskytuje šifrování svazku pro operační systém a datové disky virtuálních počítačů clusteru. Integruje se také s [Azure Key Vault](/azure/key-vault/) , které nám umožňují řídit a spravovat klíče a tajné kódy disku a zajistit, aby všechna data na discích virtuálních počítačů byla v klidovém stavu zašifrovaná a přitom byla v Azure Storage. 

Nastavení zabezpečení clusteru umožňuje povolit šifrování disku v clusteru.
  
## <a name="enable-encryption-at-rest"></a>Povolit šifrování v klidovém umístění
  
Povolení [šifrování](/azure/security/fundamentals/encryption-atrest) v klidovém clusteru zajišťuje ochranu dat pro uložená data (v klidovém prostředí). 

1. V Azure Portal přejdete do svého prostředku clusteru Azure Průzkumník dat. V záhlaví **Nastavení** vyberte **zabezpečení**. 

    ![Zapnout šifrování v klidovém umístění](media/manage-cluster-security/security-encryption-at-rest.png)

1. V okně **zabezpečení** vyberte pro nastavení zabezpečení **šifrování disku** možnost **zapnuto** . 

1. Vyberte **Uložit**.
 
> [!NOTE]
> Výběrem **vypnuto** zakážete šifrování po jeho povolení.

## <a name="next-steps"></a>Další postup

[Zkontroluje stav clusteru.](/azure/data-explorer/check-cluster-health)
