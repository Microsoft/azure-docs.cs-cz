---
title: Správa pracovních prostorů
titleSuffix: ML Studio (classic) - Azure
description: Správa přístupu k pracovním prostorům Azure Machine Learning Studio (klasické) a nasazení a správa webových služeb rozhraní API pro strojové učení
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: 68c9ca4c7b5f30d6f62d333014f4098fe7826c99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217949"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>Správa pracovního prostoru Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> [!NOTE]
> Informace o správě webových služeb na portálu Machine Learning Web Services najdete v [tématu Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md).
> 
> 

Pracovní prostory Machine Learning Studio (klasické) můžete spravovat na portálu Azure.



## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Správa pracovního prostoru Studia (klasického) na webu Azure Portal:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) pomocí účtu správce předplatného Azure.
2. Do vyhledávacího pole v horní části stránky zadejte "pracovní prostory strojového učení Studio (klasické) a pak vyberte **pracovní prostory Machine Learning Studio (klasické).**
3. Klikněte na pracovní prostor, který chcete spravovat.

Kromě standardních informací a dostupných možností správy zdrojů můžete:

- **Vlastnosti zobrazení** – tato stránka zobrazuje informace o pracovním prostoru a zdrojích a můžete změnit skupinu odběrů a prostředků, ke kterým je tento pracovní prostor spojen.
- **Znovu synchronizovat klíče úložiště** – pracovní prostor udržuje klíče k účtu úložiště. Pokud účet úložiště změní klíče, můžete klepnutím na tlačítko **Resync klíče** synchronizovat klíče s pracovním prostorem.

Chcete-li spravovat webové služby přidružené k tomuto pracovnímu prostoru studia (klasické), použijte portál Machine Learning Web Services. Úplné informace najdete [v tématu Správa webové služby pomocí portálu Azure Machine Learning Web Services.](manage-new-webservice.md)

> [!NOTE]
> Chcete-li nasadit nebo spravovat nové webové služby, musíte být přiřazena role přispěvatele nebo správce v předplatném, do kterého je webová služba nasazena. Pokud pozvete jiného uživatele do pracovního prostoru studia strojového učení (klasické), musíte ho přiřadit k roli přispěvatele nebo správce v předplatném, než bude moci nasadit nebo spravovat webové služby. 
> 
>Další informace o nastavení přístupových oprávnění najdete v [tématu Správa přístupu pomocí RBAC a portálu Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [nasazení Machine Learningu pomocí šablon Azure Resource Manager .](deploy-with-resource-manager-template.md) 
