---
title: Účty úložiště v Azure zásobníku | Microsoft Docs
description: Zjistěte, jak vytvořit účet úložiště Azure zásobníku.
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
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: a599d809ba3da8487a6c5d115bf04922a546e6ad
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
ms.locfileid: "29733719"
---
# <a name="storage-accounts-in-azure-stack"></a>Účty úložiště v Azure Stack
Účty úložiště zahrnují služby Blob a Table a unikátní obor názvů pro datové objekty úložiště. Ve výchozím nastavení jsou data ve vašem účtu dostupná pouze pro vás, vlastníka účtu úložiště.

1. V počítači, POC zásobník Azure, přihlaste se k `https://adminportal.local.azurestack.external` jako [správce](azure-stack-connect-azure-stack.md)a potom klikněte na **nový** > **Data + úložiště**  >  **Účet úložiště**.

   ![](media/azure-stack-provision-storage-account/image01.png)
2. V **vytvořit účet úložiště** okno, zadejte název účtu úložiště. Vytvořte novou **skupiny prostředků**, nebo vyberte existující šablonu a potom klikněte na tlačítko **vytvořit** k vytvoření účtu úložiště.

   ![](media/azure-stack-provision-storage-account/image02.png)
3. Chcete-li zobrazit váš nový účet úložiště, klikněte na tlačítko **všechny prostředky**, vyhledejte účet úložiště a klikněte na jeho název.

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Další postup
[Použití šablon Azure Resource Manageru](user/azure-stack-arm-templates.md)

[Další informace o účtech Azure storage](../storage/common/storage-create-storage-account.md)

[Stáhnout příručku ověření Azure konzistentní úložiště Azure zásobníku](http://aka.ms/azurestacktp1doc)
