---
title: Nastavení účtu Azure Batch Akustika projektu
titlesuffix: Azure Cognitive Services
description: Tento návod popisuje nastavení účtu Azure Batch pro použití s integrací projektu Akustika Unity a Unreal engine.
services: cognitive-services
author: ashtat
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: db4f96ff7c355f3582966e4daa945f54a6e5b847
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616537"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Nastavení účtu Azure Batch Akustika projektu
Tento návod popisuje nastavení účtu Azure Batch pro použití s integrací projektu Akustika Unity a Unreal engine.

## <a name="get-an-azure-subscription"></a>Získání předplatného Azure
[Předplatné Azure](https://azure.microsoft.com/free/) se vyžaduje před nastavením účtů Batch a Storage. Pokud se zaregistrujete poprvé, Azure poskytuje několik časově omezené bezplatné materiály a kreditem 200 USD.

## <a name="create-azure-batch-and-storage-accounts"></a>Vytvoření účtů služby Azure Batch a storage
Potom postupujte podle [tyto pokyny](https://docs.microsoft.com/azure/batch/batch-account-create-portal) k nastavení služby Azure Batch a související účty Azure Storage.

Vyberte výchozí možnosti pro účty Batch a Storage:
  
  ![Snímek obrazovky služby Azure Batch nové účty zobrazuje výchozí nastavení možnosti](media/new-batch-account-create.png)

  ![Snímek obrazovky z účtů služby Azure Storage nové možnosti, které zobrazuje výchozí nastavení](media/batch-storage-account-create.png)

Trvá několik minut, než Azure k nasazení účty. Vyhledejte oznámení o dokončení v pravém horním rohu portálu.
  
  ![Snímek obrazovky Azure účty nasazené oznámení](media/batch-accounts-deploy-notification.png)

Vaše účty by teď mělo zobrazit na řídicím panelu.
  
  ![Snímek obrazovky Azure řídicí panel portal ukazující účet Batch a Storage](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Nastavit Akustika která má označení vytvoření uživatelského rozhraní pomocí přihlašovacích údajů k Azure
Klikněte na odkaz daného účtu Batch na řídicím panelu a potom klikněte na **klíče** odkazu na stránce účtu služby Batch pro přístup k svoje přihlašovací údaje.
  
  ![Účet Batch Azure – snímek obrazovky s odkazem na stránku klíče se zvýrazněnou](media/batch-access-keys.png)

  ![Snímek obrazovky s Azure Batch stránku klíče účtu pomocí přístupových klíčů](media/batch-keys-info.png)

Klikněte na **účtu úložiště** odkaz na stránku pro přístup k přihlašovací údaje účtu úložiště Azure.
  
  ![Stránka klíče účtu snímek obrazovky s Azure Storage pomocí přístupových klíčů](media/storage-keys-info.png)

Zadejte tyto přihlašovací údaje v [Unity která má označení vytvoření modulu plug-in](unity-baking.md) nebo [Unreal která má označení vytvoření modulu plug-in](unreal-baking.md).

## <a name="node-types-and-region-support"></a>Typy uzlů a oblasti podpory
Projekt Akustika vyžaduje že fsv2 a H-series – compute optimalizované uzly virtuálních počítačů Azure, které nemusí být podporované ve všech oblastech Azure. Zkontrolujte prosím [Tato tabulka](https://azure.microsoft.com/global-infrastructure/services) zajistit vybíráte správné umístění pro váš účet Batch.
![Snímek obrazovky s Azure Virtual Machines podle oblastí](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Upgrade vaší kvóty
Účty služby Azure Batch jsou zřízené na účet vytváření s maximálně 20 výpočetních jader. Může chcete zvýšit tento limit, pro která má označení vytvoření rychlejší, protože můžete paralelní zpracování vašich úloh Akustika mezi velký počet uzlů, až počet bodů testu ve scéně. Můžete požádat o zvýšení kvóty po kliknutí na **kvóty** odkaz na stránku portálu služby Azure Batch a kliknete na **žádost o zvýšení kvóty**:

![Kvóta Azure – snímek obrazovky stránky](media/azure-quotas.png)

## <a name="next-steps"></a>Další postup
* Integrujte modul plug-in Akustika projektu do vaší [Unity](unity-integration.md) nebo [Unreal](unreal-integration.md) projektu

