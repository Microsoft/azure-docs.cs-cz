---
title: Akustické navýšení projektu Azure Batch nastavení účtu
titlesuffix: Azure Cognitive Services
description: Tento postup popisuje, jak nastavit účet Azure Batch pro použití s Unrealmi a integrací stroje pro projekt.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 8f0f726d9d23f20698d3510ad674331ad74fb703
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855084"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Akustické navýšení projektu Azure Batch nastavení účtu
Tento postup popisuje, jak nastavit účet Azure Batch pro použití s Unrealmi a integrací stroje pro projekt.

## <a name="get-an-azure-subscription"></a>Získat předplatné Azure
Před nastavením účtů Batch a Storage se vyžaduje [předplatné Azure](https://azure.microsoft.com/free/) . Pokud si zaregistrujete poprvé, Azure poskytuje několik časově omezených prostředků, které jsou časově omezené a $200 kredity.

## <a name="create-azure-batch-and-storage-accounts"></a>Vytvoření účtů Azure Batch a úložiště
Dále postupujte podle [těchto pokynů](https://docs.microsoft.com/azure/batch/batch-account-create-portal) a nastavte Azure Batch a přidružené účty Azure Storage.

Vyberte výchozí možnosti pro účty Batch a Storage:
  
  ![Snímek obrazovky s možnostmi Azure Batch nové účty ukazující výchozí nastavení](media/new-batch-account-create.png)

  ![Snímek obrazovky s možnostmi Azure Storage nové účty ukazující výchozí nastavení](media/batch-storage-account-create.png)

Nasazení účtů v Azure trvá několik minut. V pravém horním rohu na portálu vyhledejte oznámení o dokončení.
  
  ![Snímek obrazovky s nasazenými oznámeními účtů Azure](media/batch-accounts-deploy-notification.png)

Vaše účty by se teď měly zobrazit na řídicím panelu.
  
  ![Snímek obrazovky řídicího panelu Azure Portal znázorňující účet Batch a úložiště](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Nastavení akustického uživatelského rozhraní zanesli s přihlašovacími údaji Azure
Klikněte na odkaz účet Batch na řídicím panelu a pak klikněte na odkaz **klíče** na stránce účtu Batch, abyste měli přístup k vašim přihlašovacím údajům.
  
  ![Snímek obrazovky se zvýrazněnou možností účet Azure Batch se stránkou odkazy na klíče](media/batch-access-keys.png)

  ![Snímek obrazovky se stránkou klíčů účtu Azure Batch s přístupovými klíči](media/batch-keys-info.png)

Kliknutím na odkaz **účet úložiště** na stránce získáte přístup k přihlašovacím údajům k účtu Azure Storage.
  
  ![Snímek obrazovky se stránkou klíčů účtu Azure Storage s přístupovými klíči](media/storage-keys-info.png)

Zadejte tyto přihlašovací údaje do modulu [Plug-in Unity zanesli](unity-baking.md) nebo [modulu plug-in Unreal zanesli](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Podpora typů uzlů a oblastí
Akustické projekty vyžadují Fsv2 a výpočetní prostředí Azure optimalizované pro řady H-Series, které nemusí být podporované ve všech oblastech Azure. Zkontrolujte prosím [tuto tabulku](https://azure.microsoft.com/global-infrastructure/services) , abyste měli jistotu, že vybíráte správné umístění účtu Batch.
![Snímek obrazovky s Virtual Machines Azure podle oblasti](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Upgrade kvóty
Účty Azure Batch se zřídí při vytváření účtu s limitem 20 výpočetních jader. Tento limit můžeme zvýšit pro rychlejší zanesli dobu, protože můžete paralelizovat úlohy akustického zatížení napříč mnoha uzly, a to až do počtu testovacích bodů ve scéně. Zvýšení kvóty můžete požádat kliknutím na odkaz na kvótu na stránce Azure Batchového portálu a následným kliknutím na **zvýšení kvóty žádostí**:

![Snímek obrazovky se stránkou kvót Azure](media/azure-quotas.png)

## <a name="next-steps"></a>Další postup
* Integrujte modul plug-in projektového akustického prostředí do vaší [Unity](unity-integration.md) nebo [Unreal](unreal-integration.md) projektu

