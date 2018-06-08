---
title: Spravovat pracovní prostor Machine Learning | Microsoft Docs
description: Správa přístupu k Azure Machine Learning pracovní prostory a nasadit a spravovat rozhraní API pro ML webové služby
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: daf3d413-7a77-4beb-9a7a-6b4bdf717719
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 8c8e65d5fd7efd1f9212b76e1a72b91a0ea07272
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835062"
---
# <a name="manage-an-azure-machine-learning-workspace"></a>Správa pracovního prostoru Azure Machine Learning

> [!NOTE]
> Informace týkající se správy webové služby v portálu webové služby Machine Learning najdete v tématu [spravovat webové služby pomocí portálu webové služby Azure Machine Learning](manage-new-webservice.md).
> 
> 

Můžete spravovat pracovní prostory Machine Learning na portálu Azure.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Správa prostoru na portálu Azure:

1. Přihlaste se k [portál Azure](https://portal.azure.com/) pomocí účtu správce předplatného Azure.
2. Do vyhledávacího pole v horní části stránky, zadejte "počítač pracovní prostory learning" a potom vyberte **Machine Learning pracovních prostorů**.
3. Klikněte na pracovní prostor, který chcete spravovat.

Kromě informací o správu standardní prostředku a možnosti, které jsou k dispozici můžete:

- Zobrazení **vlastnosti** – Tato stránka zobrazuje informace o pracovním prostoru a prostředků, a můžete změnit předplatném nebo skupině prostředků, tento pracovní prostor je propojená s.
- **Nové synchronizace klíčů k úložišti** -pracovním prostoru udržuje klíče k účtu úložiště. Pokud účet úložiště změny klíčů, pak můžete kliknout na **nové synchronizace klíče** synchronizovat klíče s pracovním prostoru.

Ke správě webových služeb přidružený tento pracovní prostor, použití portálu webové služby Machine Learning. V tématu [spravovat webové služby pomocí portálu webové služby Azure Machine Learning](manage-new-webservice.md) úplné informace.

> [!NOTE]
> Nasadit nebo spravovat nové webové služby musí mít přiřazenou roli Přispěvatel nebo správce na předplatné, která je nasazena webová služba. Pokud můžete pozvat jiného uživatele na pracovní prostor machine learning, musíte je přiřadit k roli Přispěvatel nebo správce na předplatné, než můžete nasadit nebo spravovat webové služby. 
> 
>Další informace o nastavení oprávnění přístupu najdete v tématu [zobrazení přiřazení přístupu pro uživatele a skupiny na portálu Azure](../../role-based-access-control/role-assignments-users.md).

## <a name="next-steps"></a>Další postup
* Další informace o [nasazení Machine Learning s šablon Azure Resource Manageru](deploy-with-resource-manager-template.md). 