---
title: Správa pracovního prostoru Machine Learning | Dokumentace Microsoftu
description: Správa přístupu k pracovních prostorů Azure Machine Learning a nasazovat a spravovat služby webového rozhraní API ML
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
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
ms.openlocfilehash: 80a9c92c80d82555c5bf8f75a615095647eb1dc9
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52264943"
---
# <a name="manage-an-azure-machine-learning-workspace"></a>Správa pracovního prostoru Azure Machine Learning

> [!NOTE]
> Informace o správě webových služeb Machine Learning Web Services portálu najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md).
> 
> 

Můžete spravovat pracovní prostory Machine Learning na webu Azure Portal.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

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