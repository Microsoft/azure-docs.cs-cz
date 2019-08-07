---
title: Zabezpečení clusteru v Azure Průzkumník dat
description: Tento článek popisuje, jak zabezpečit cluster v Azure Průzkumník dat v rámci Azure Portal.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: 86350f21f5c530a00560c92cc0ae2fd58c9a2c57
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780046"
---
# <a name="secure-your-cluster-in-azure-data-explorer"></a>Zabezpečení clusteru v Azure Průzkumník dat

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) pomáhá chránit a chránit vaše data, aby splňovala závazky zabezpečení vaší organizace a dodržování předpisů. Poskytuje šifrování svazku pro operační systém a datové disky virtuálních počítačů clusteru. Integruje se také s [Azure Key Vault](/azure/key-vault/) , které vám pomohou řídit a spravovat klíče a tajné kódy disku, a zajistit, aby všechna data na discích virtuálních počítačů byla během Azure Storage zašifrovaná v klidovém stavu. Nastavení zabezpečení clusteru umožňuje povolit šifrování disku v clusteru.
  
## <a name="enable-encryption-at-rest"></a>Povolit šifrování v klidovém umístění
  
Povolení [šifrování](/azure/security/fundamentals/encryption-atrest) v klidovém clusteru zajišťuje ochranu dat pro uložená data (v klidovém prostředí). 

1. V Azure Portal přejdete do svého prostředku clusteru Azure Průzkumník dat. V záhlaví **Nastavení** vyberte **zabezpečení**. 

    ![Zapnout šifrování v klidovém umístění](media/manage-cluster-security/security-encryption-at-rest.png)

1. V okně **zabezpečení** vyberte pro nastavení zabezpečení **šifrování disku** možnost **zapnuto** . 

1. Vyberte **Uložit**.

## <a name="next-steps"></a>Další postup

[Zkontroluje stav clusteru.](/azure/data-explorer/check-cluster-health)
