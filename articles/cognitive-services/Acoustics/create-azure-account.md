---
title: Nastavení Azure účtů pro Akustika projektu
titlesuffix: Azure Cognitive Services
description: Postupujte podle tohoto průvodce pro nastavení účtů služby Azure Batch a úložiště potřebné pro práci s Akustika.
services: cognitive-services
author: ashtat
manager: cgronlun
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 638ea3e707352a95cb1fb407add365a83d11faa7
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55148638"
---
# <a name="create-an-azure-batch-account"></a>Vytvoření účtu služby Azure Batch
Postupujte podle tohoto průvodce pro nastavení účtů služby Azure Batch a úložiště potřebné pro práci s Akustika. Informace o modulu Unity vyvinutý jako součást projektu Akustika najdete v tématu [novinky Akustika](what-is-acoustics.md). Informace o tom, jak začlenit do vašich projektů Unity Akustika najdete v tématu [Začínáme](getting-started.md).  

## <a name="get-an-azure-subscription"></a>Získání předplatného Azure
[Předplatné Azure](https://azure.microsoft.com/free/) se vyžaduje před nastavením účtů Batch a Storage. Pokud se zaregistrujete poprvé, Azure poskytuje několik časově omezené bezplatné materiály a kreditem 200 USD.

## <a name="create-azure-batch-and-storage-accounts"></a>Vytvoření účtů služby Azure Batch a storage
Potom postupujte podle [tyto pokyny](https://docs.microsoft.com/azure/batch/batch-account-create-portal) k nastavení služby Azure Batch a související účty Azure Storage.

Vyberte výchozí možnosti pro účty Batch a Storage:
  
  ![Nový účet Batch](media/NewBatchAccountCreate.png)

  ![Nový účet úložiště](media/BatchStorageAccountCreate.png)

Trvá několik minut, než Azure k nasazení účty. Vyhledejte oznámení o dokončení v pravém horním rohu portálu.
  
  ![Účty nasazení](media/BatchAccountsDeployNotification.png)

Vaše účty by teď mělo zobrazit na řídicím panelu.
  
  ![Řídicí panel portálu](media/AzurePortalDashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Nastavit Akustika která má označení vytvoření uživatelského rozhraní pomocí přihlašovacích údajů k Azure
Klikněte na odkaz daného účtu Batch na řídicím panelu a potom klikněte na **klíče** odkazu na stránce účtu služby Batch pro přístup k svoje přihlašovací údaje.
  
  ![Odkaz klíčů služby batch](media/BatchAccessKeys.png)

  ![Přihlašovací údaje k účtu batch](media/BatchKeysInfo.png)

Klikněte na **účtu úložiště** odkaz na stránku pro přístup k přihlašovací údaje účtu úložiště Azure.
  
  ![Přihlašovací údaje účtu úložiště](media/StorageKeysInfo.png)

Zadejte tyto přihlašovací údaje v kartě která má označení vytvoření, jak je popsáno v [vytvoření uživatelského rozhraní návod](bake-ui-walkthrough.md).

## <a name="node-types-and-region-support"></a>Typy uzlů a oblasti podpory
Projekt Akustika vyžaduje že f a H-series – compute optimalizované uzly virtuálních počítačů Azure, které nemusí být podporované ve všech oblastech Azure. Zkontrolujte prosím [Tato tabulka](https://azure.microsoft.com/global-infrastructure/services) zajistit vybíráte správné umístění pro váš účet Batch. V tuto chvíli H series jsou virtuální počítače podporované v oblastech východní USA, střed USA – sever, střed USA – Jih, USA – Západ, USA – západ 2, Severní Evropa, západní Evropa a Japonsko – západ.

## <a name="upgrading-your-quota"></a>Upgrade vaší kvóty
Účty služby Azure Batch jsou zřízené na účet vytváření s maximálně 20 výpočetních jader. Můžete chtít zvýšit tento limit, pro která má označení vytvoření rychlejší, protože můžete paralelní zpracování vašich úloh Akustika mezi velký počet uzlů, až počet bodů testu ve scéně. Můžete požádat o zvýšení kvóty po kliknutí na **kvóty** odkaz na stránku portálu služby Azure Batch a kliknete na **žádost o zvýšení kvóty**:

![Zvýšení kvóty Azure](media/azurequotas.png)

## <a name="next-steps"></a>Další postup
* Začínáme [integrace Akustika do svým projektem Unity](getting-started.md)
* Prozkoumejte [ukázková Scéna](sample-walkthrough.md)

