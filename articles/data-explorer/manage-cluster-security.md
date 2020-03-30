---
title: Zabezpečení clusteru v Azure Data Exploreru
description: Tento článek popisuje, jak zabezpečit cluster v Azure Data Explorer u na webu Azure Portal.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 0f935999b68a7283c032d43c42d688b273d5c450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75720340"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>Zabezpečení clusteru v Azure Data Exploreru – portál Azure

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) pomáhá chránit a chránit vaše data, aby splňovala vaše závazky v oblasti zabezpečení a dodržování předpisů v organizaci. Poskytuje šifrování svazku pro operační systém a datové disky virtuálních počítačů clusteru. Integruje se také s [Azure Key Vault](/azure/key-vault/), který nám umožňuje řídit a spravovat šifrovací klíče a tajné klíče disku a zajistit, aby všechna data na discích virtuálních počítačů byla šifrovaná. 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>Povolení šifrování v klidovém stavu na webu Azure Portal
  
Nastavení zabezpečení clusteru umožňuje povolit šifrování disku v clusteru. Povolení [šifrování v klidovém stavu](/azure/security/fundamentals/encryption-atrest) v clusteru poskytuje ochranu dat pro uložená data (v klidovém stavu). 

1. Na webu Azure Portal přejděte na prostředek clusteru Azure Data Explorer. V záhlaví **Nastavení** vyberte **Možnost Zabezpečení**. 

    ![Zapnutí šifrování v klidovém stavu](media/manage-cluster-security/security-encryption-at-rest.png)

1. V okně **Zabezpečení** vyberte **možnost Zapnuto** pro nastavení zabezpečení **šifrování disku.** 

1. Vyberte **Uložit**.
 
> [!NOTE]
> Chcete-li šifrování povolit, vyberte **možnost Vypnuto.**

## <a name="next-steps"></a>Další kroky

[Kontrola stavu clusteru](/azure/data-explorer/check-cluster-health)
