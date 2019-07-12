---
title: Připojení Symantec AWS dat Sentinelu ve verzi Preview Azure | Dokumentace Microsoftu
description: Zjistěte, jak připojit data Symantec AWS do Azure Sentinelu.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 214269bc5c854aa4d3bfd508b0adb5a53ec096df
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673970"
---
# <a name="connect-azure-sentinel-to-aws"></a>Připojení Azure Sentinel AWS

Streamování událostí CloudTrail v AWS do Azure Sentinelu pomocí konektoru AWS. Tento proces připojení pro Azure Sentinelu deleguje přístup k protokolům prostředků AWS, vytvoření vztahu důvěryhodnosti mezi CloudTrail v AWS a Azure Sentinelu. To se provádí v AWS tak, že vytvoříte roli, která poskytuje oprávnění k Azure Sentinelu pro přístup k protokolům AWS.

## <a name="prerequisites"></a>Požadavky

V pracovním prostoru Azure Sentinelu musí mít oprávnění k zápisu.

## <a name="connect-aws"></a>Připojení AWS 
 
1.  Na webu Amazon služby konzole v části **Security, Identity & Compliance**, klikněte na **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

2.  Zvolte **role** a klikněte na tlačítko **vytvořit roli**.

    ![AWS2](./media/connect-aws/aws-2.png)

3.  Zvolte **AWS jiný účet.** V **ID účtu** zadejte **ID účtu Microsoft** (**123412341234**), který najdete na stránce konektoru AWS na portálu Azure Sentinelu.

    ![AWS3](./media/connect-aws/aws-3.png)

4.  Ujistěte se, že **vyžadují externí ID** je vybrána a potom a zadat externí ID (ID pracovního prostoru) nalezeným na stránce konektoru AWS na portálu Azure Sentinelu.

    ![AWS4](./media/connect-aws/aws-4.png)

5.  V části **připojit zásady oprávnění** vyberte **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

6.  Zadejte značky (volitelné).

    ![AWS6](./media/connect-aws/aws-6.png)

7.  Potom zadejte **název Role** a klikněte na tlačítko **vytvořit roli** tlačítko.

    ![AWS7](./media/connect-aws/aws-7.png)

8.  V seznamu rolí vyberte roli, kterou jste vytvořili.

    ![AWS8](./media/connect-aws/aws-8.png)

9.  Kopírovat **Role ARN** a vložte ho do **Role pro přidání** pole Sentinelu portálu Azure.

    ![AWS9](./media/connect-aws/aws-9.png)

10. Chcete-li použít příslušné schéma v Log Analytics pro události AWS, vyhledejte **AWSCloudTrail**.



## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit k Azure Sentinelu CloudTrail v AWS. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).

