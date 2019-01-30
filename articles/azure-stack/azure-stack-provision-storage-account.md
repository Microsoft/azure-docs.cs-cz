---
title: Účty úložiště ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit účet úložiště Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 1/18/2019
ms.author: mabrigg
ms.lastreviewed: 1/18/2019
ms.openlocfilehash: 4123d4cec25bab116c642f1b89cd8eff4779af42
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55252145"
---
# <a name="storage-accounts-in-azure-stack"></a>Účty úložiště v Azure Stack
Účty úložiště zahrnují služby Blob service a Table service a unikátní obor názvů pro datové objekty úložiště. Ve výchozím nastavení jsou data ve vašem účtu dostupná pouze pro vás, vlastníka účtu úložiště.

1. Na počítači Azure Stack POC, přihlaste se k `https://adminportal.local.azurestack.external` jako [správce](azure-stack-connect-azure-stack.md)a potom klikněte na tlačítko **+ vytvořit prostředek** > **Data + úložiště**  >  **Účtu úložiště**.

   ![](media/azure-stack-provision-storage-account/image01.png)
2. V **vytvořit účet úložiště** okno, zadejte název účtu úložiště. Vytvořte nový **skupiny prostředků**, nebo vyberte existující skupinu a potom klikněte na tlačítko **vytvořit** k vytvoření účtu úložiště.

   ![](media/azure-stack-provision-storage-account/image02.png)
3. Váš nový účet úložiště, klikněte na **všechny prostředky**, vyhledejte účet úložiště a klikněte na jeho název.

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Další postup
[Použití šablon Azure Resource Manageru](user/azure-stack-arm-templates.md)

[Další informace o účtech Azure storage](../storage/common/storage-create-storage-account.md)

[Stáhnout příručku o ověřování úložišť konzistentních s Azure Azure Stack](https://aka.ms/azurestacktp1doc)
