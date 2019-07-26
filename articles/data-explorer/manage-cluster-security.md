---
title: Zabezpečení clusteru v Azure Průzkumník dat
description: Tento článek popisuje, jak zabezpečit cluster v Azure Průzkumník dat v rámci Azure Portal.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: fbc5b18de093b2c91b17fa310c08a5b02b113a22
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406519"
---
# <a name="secure-your-cluster-in-azure-data-explorer"></a>Zabezpečení clusteru v Azure Průzkumník dat

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) pomáhá chránit a chránit vaše data, aby splňovala závazky zabezpečení vaší organizace a dodržování předpisů. Poskytuje šifrování svazku pro operační systém a datové disky virtuálních počítačů clusteru. Integruje se také s [Azure Key Vault](/azure/key-vault/) , které vám pomohou řídit a spravovat klíče a tajné kódy disku, a zajistit, aby všechna data na discích virtuálních počítačů byla během Azure Storage zašifrovaná v klidovém stavu. Nastavení zabezpečení clusteru umožňuje povolit šifrování disku v clusteru.
  
## <a name="enable-encryption-at-rest"></a>Povolit šifrování v klidovém umístění
  
Povolení [šifrování](/azure/security/azure-security-encryption-atrest) v klidovém clusteru zajišťuje ochranu dat pro uložená data (v klidovém prostředí). 

1. V Azure Portal přejdete do svého prostředku clusteru Azure Průzkumník dat. V záhlaví **Nastavení** vyberte **zabezpečení**. 

    ![Zapnout šifrování v klidovém umístění](media/manage-cluster-security/security-encryption-at-rest.png)

1. V okně **zabezpečení** vyberte pro nastavení zabezpečení **šifrování disku** možnost **zapnuto** . 

1. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

[Zkontroluje stav clusteru.](/azure/data-explorer/check-cluster-health)
