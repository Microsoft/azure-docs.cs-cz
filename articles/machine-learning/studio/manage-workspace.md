---
title: Správa pracovního prostoru Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Správa přístupu k pracovních prostorů Azure Machine Learning a nasazovat a spravovat služby webového rozhraní API ML
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: f1904552b6c43cadd611659ed544b9f4bafcb465
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55487267"
---
# <a name="manage-an-azure-machine-learning-studio-workspace"></a>Správa pracovního prostoru Azure Machine Learning Studio

> [!NOTE]
> Informace o správě webových služeb Machine Learning Web Services portálu najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md).
> 
> 

Můžete spravovat pracovní prostory Machine Learning na webu Azure Portal.



## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Správa pracovního prostoru na webu Azure Portal:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) pomocí účtu správce předplatného Azure.
2. Do vyhledávacího pole v horní části stránky, zadejte "pracovní prostory machine learning" a pak vyberte **pracovní prostory Machine Learning**.
3. Klikněte na pracovním prostoru, který chcete spravovat.

Kromě standardních prostředků informacím a k dispozici možnosti můžete:

- Zobrazení **vlastnosti** – Tato stránka zobrazuje informace o pracovním prostoru a prostředků, a můžete změnit předplatné a skupinu prostředků, které tento pracovní prostor je propojená s.
- **Resynchronizovat klíče úložiště** -pracovního prostoru udržuje klíčů k účtu úložiště. Pokud účet úložiště se změní klíče, pak můžete kliknout na **resynchronizace klíčů** aby se klíče synchronizovaly s pracovním prostorem.

Správa webových služeb přidružený k tomuto pracovnímu prostoru, použití portálu webových služeb Machine Learning. Zobrazit [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md) podrobnější informace.

> [!NOTE]
> Nasazovat nebo spravovat s novými webovými službami musí mít přiřazenou roli přispěvatelem nebo správcem předplatného, které je nasazena webová služba. Pokud vás pozvat jiného uživatele do pracovního prostoru machine learning, je nutné jim přiřadit roli přispěvatelem nebo správcem předplatného mohli nasadit nebo spravovat webové služby. 
> 
>Další informace o nastavení přístupových oprávnění najdete v tématu [správě přístupu pomocí RBAC a webu Azure portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Další postup
* Další informace o [nasazení služby Machine Learning pomocí šablony Azure Resource Manageru](deploy-with-resource-manager-template.md). 
