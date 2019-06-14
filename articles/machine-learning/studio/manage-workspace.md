---
title: Správa pracovního prostoru Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Správa přístupu k pracovním prostorům Azure Machine Learning Studio, nasazení a Správa webových služeb Machine Learning API
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: a947f9a94dd4ceed624e6b04a671b21b8926d25e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60322783"
---
# <a name="manage-an-azure-machine-learning-studio-workspace"></a>Správa pracovního prostoru Azure Machine Learning Studio

> [!NOTE]
> Informace o správě webových služeb Machine Learning Web Services portálu najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md).
> 
> 

Můžete spravovat pracovní prostory Machine Learning Studio na webu Azure Portal.



## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Chcete-li spravovat pracovní prostor Studio na webu Azure Portal:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) pomocí účtu správce předplatného Azure.
2. Do vyhledávacího pole v horní části stránky, zadejte "machine learning Studio pracovní prostory" a pak vyberte **pracovní prostory Machine Learning Studio**.
3. Klikněte na pracovním prostoru, který chcete spravovat.

Kromě standardních prostředků informacím a k dispozici možnosti můžete:

- Zobrazení **vlastnosti** – Tato stránka zobrazuje informace o pracovním prostoru a prostředků, a můžete změnit předplatné a skupinu prostředků, které tento pracovní prostor je propojená s.
- **Resynchronizovat klíče úložiště** -pracovního prostoru udržuje klíčů k účtu úložiště. Pokud účet úložiště se změní klíče, pak můžete kliknout na **resynchronizace klíčů** aby se klíče synchronizovaly s pracovním prostorem.

Správa webových služeb přidružené k tento pracovní prostor Studio, použití portálu webových služeb Machine Learning. Zobrazit [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md) podrobnější informace.

> [!NOTE]
> Nasazovat nebo spravovat s novými webovými službami musí mít přiřazenou roli přispěvatelem nebo správcem předplatného, které je nasazena webová služba. Pokud vás pozvat jiného uživatele do služby machine learning Studio pracovní prostor, je nutné jim přiřadit roli přispěvatelem nebo správcem předplatného mohli nasadit nebo spravovat webové služby. 
> 
>Další informace o nastavení přístupových oprávnění najdete v tématu [správě přístupu pomocí RBAC a webu Azure portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Další postup
* Další informace o [nasazení služby Machine Learning pomocí šablony Azure Resource Manageru](deploy-with-resource-manager-template.md). 
