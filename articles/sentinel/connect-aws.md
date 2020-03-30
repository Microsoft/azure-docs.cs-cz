---
title: Připojení AWS CloudTrail k Azure Sentinelu | Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit data AWS CloudTrail k Azure Sentinelu.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 5cbef1f31ea7088d4fab4888f5630af1b765a910
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588650"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Připojení Azure Sentinelu k AWS CloudTrail

Pomocí konektoru AWS můžete streamovat všechny události AWS CloudTrail do Azure Sentinelu. Tento proces připojení deleguje přístup pro Azure Sentinel do protokolů prostředků AWS a vytváří vztah důvěryhodnosti mezi AWS CloudTrail a Azure Sentinel. Toho se provádí na AWS vytvořením role, která uděluje oprávnění k Azure Sentinel pro přístup k protokolům AWS.

## <a name="prerequisites"></a>Požadavky

Musíte mít oprávnění k zápisu do pracovního prostoru Azure Sentinel.

> [!NOTE]
> Azure Sentinel shromažďuje události CloudTrail ze všech oblastí. Doporučujeme nestreamovat události z jedné oblasti do druhé.

## <a name="connect-aws"></a>Připojení AWS 


1. V Azure Sentinelu vyberte **datové konektory** a pak v tabulce vyberte řádek **Amazon Web Services** a v podokně AWS vpravo klikněte na Otevřít **stránku konektoru**.

1. Postupujte podle pokynů v části **Konfigurace** pomocí následujících kroků.
 
1.  V konzole Amazon Web Services v části **Zabezpečení, identita & compliance**vyberte **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Zvolte **Role** a vyberte **Vytvořit roli**.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Zvolte **jiný účet AWS.** Do pole **ID účtu** zadejte **ID účtu Microsoft** (**123412341234**), které najdete na stránce konektoru AWS na portálu Azure Sentinel.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Ujistěte **se, že je vybráno vyžadovat externí ID** a pak zadejte externí ID (ID pracovního prostoru), které se nachází na stránce konektoru AWS na portálu Azure Sentinel.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  V části **Připojit zásady oprávnění** vyberte **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Zadejte značku (volitelné).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Potom zadejte **název role** a vyberte tlačítko Vytvořit **roli.**

    ![AWS7](./media/connect-aws/aws-7.png)

1.  V seznamu Role zvolte roli, kterou jste vytvořili.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Zkopírujte **roli ARN**. Na portálu Azure Sentinel na obrazovce konektoru Amazon Web Services vložte do pole **Role pro přidání** pole a klikněte na **Přidat**.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Chcete-li použít příslušné schéma v Log Analytics pro události AWS, vyhledejte **AWSCloudTrail**.



## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit AWS CloudTrail k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).
- Ke sledování dat [použijte sešity.](tutorial-monitor-your-data.md)

