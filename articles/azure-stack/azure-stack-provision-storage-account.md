---
title: Účty úložiště ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit účet úložiště Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: e1152110-b756-4c1a-9fa2-73fe3ab0ad8e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2018
ms.author: mabrigg
ms.openlocfilehash: ae6539900e201f0559d998ad2d9be24c39d42e3b
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713489"
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

[Stáhnout příručku o ověřování úložišť konzistentních s Azure Azure Stack](http://aka.ms/azurestacktp1doc)
