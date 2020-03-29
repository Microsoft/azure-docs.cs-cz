---
title: Nastavení dávkového účtu Azure v oblasti akustiky projektu
titlesuffix: Azure Cognitive Services
description: Tento návod popisuje nastavení účtu Azure Batch pro použití s project acoustics unity a unreal integrací modulu.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68855084"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Nastavení dávkového účtu Azure v oblasti akustiky projektu
Tento návod popisuje nastavení účtu Azure Batch pro použití s project acoustics unity a unreal integrací modulu.

## <a name="get-an-azure-subscription"></a>Získání předplatného Azure
Před nastavením účtů Batch a Storage je vyžadováno [předplatné Azure.](https://azure.microsoft.com/free/) Pokud se přihlašujete poprvé, Azure poskytuje několik časově omezených bezplatných prostředků a kreditu $200.

## <a name="create-azure-batch-and-storage-accounts"></a>Vytvoření účtů Azure Batch a storage
Dále podle [těchto pokynů](https://docs.microsoft.com/azure/batch/batch-account-create-portal) nastavte azure batch a přidružené účty Azure Storage.

Vyberte výchozí možnosti pro účty Batch i Storage:
  
  ![Snímek obrazovky s novými možnostmi účtů Azure Batch zobrazujícívýchozí nastavení](media/new-batch-account-create.png)

  ![Snímek obrazovky s novými možnostmi účtů Azure Storage zobrazujícívýchozí nastavení](media/batch-storage-account-create.png)

Nasazení účtů azure trvá několik minut. Vyhledejte oznámení o dokončení v pravém horním rohu na portálu.
  
  ![Snímek obrazovky s oznámením o nasazených účtech Azure](media/batch-accounts-deploy-notification.png)

Vaše účty by teď měly být viditelné na řídicím panelu.
  
  ![Snímek obrazovky řídicího panelu portálu Azure zobrazujícího účet dávky a úložiště](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Nastavení akustiky pečit ui s přihlašovacími údaji Azure
Klikněte na odkaz Dávkový účet na řídicím panelu a potom klikněte na odkaz **Klíče** na stránce Dávkový účet, abyste měli přístup ke svým přihlašovacím údajům.
  
  ![Snímek obrazovky se zvýrazněným odkazem na klíče](media/batch-access-keys.png)

  ![Snímek obrazovky stránky klíčů účtů Azure Batch s přístupovými klíči](media/batch-keys-info.png)

Kliknutím na odkaz **Účet úložiště** na stránce přenesete přihlašovací údaje k účtu Azure Storage.
  
  ![Snímek obrazovky stránky klíčů účtu Úložiště Azure s přístupovými klíči](media/storage-keys-info.png)

Zadejte tyto přihlašovací údaje v [Unity péct plugin](unity-baking.md) nebo [Unreal péct plugin](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Typy uzlů a podpora oblastí
Akustika projektu vyžaduje výpočetní uzly optimalizované pro výpočetní výkony řady Fsv2 a H, které nemusí být podporované ve všech oblastech Azure. V [této tabulce](https://azure.microsoft.com/global-infrastructure/services) zkontrolujte, zda si pro svůj účet Batch vybíráte správné umístění.
![Snímek obrazovky s virtuálními počítači Azure podle oblasti](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Upgrade kvóty
Účty Azure Batch se zřaží při vytváření účtu s limitem 20 výpočetních jader. Možná budeme chtít zvýšit tento limit pro rychlejší časy pečení, protože můžete paralelizovat vaše akustika zatížení v mnoha uzlech, až do počtu bodů sondy ve vaší scéně. O zvýšení kvóty můžete požádat kliknutím na odkaz **Kvóta** na stránce portálu Azure Batch a kliknutím na **Možnost Zvýšení kvóty požadavku**:

![Snímek obrazovky se stránkou Kvóta Azure](media/azure-quotas.png)

## <a name="next-steps"></a>Další kroky
* Integrujte plugin Project Acoustics do svého projektu [Unity](unity-integration.md) nebo [Unreal](unreal-integration.md)

