---
title: 'ML Studio (Classic): Správa pracovních prostorů – Azure'
description: Správa přístupu k pracovním prostorům Azure Machine Learning Studio (Classic) a nasazení a správa webových služeb rozhraní API Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: 1996f4c6c73040fa16a1c5a0468ebcced3ab1641
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516477"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>Správa pracovního prostoru Azure Machine Learning Studio (Classic)

**platí pro:** ![ Platí pro. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ neplatí pro.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


> [!NOTE]
> Informace o tom, jak spravovat webové služby na portálu Machine Learning Web Services, najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md).
> 
> 

V Azure Portal můžete spravovat pracovní prostory Machine Learning Studio (Classic).



## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Správa pracovního prostoru studia (Classic) v Azure Portal:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu správce předplatného Azure.
2. Do vyhledávacího pole v horní části stránky zadejte "pracovní prostory Machine Learning Studio (Classic) a pak vyberte **Machine Learning Studio (Classic) pracovní prostory**.
3. Klikněte na pracovní prostor, který chcete spravovat.

Kromě standardních informací a možností správy prostředků, které jsou k dispozici, můžete:

- Zobrazit **vlastnosti** – na této stránce se zobrazují informace o pracovním prostoru a prostředcích a můžete změnit předplatné a skupinu prostředků, se kterými je tento pracovní prostor připojený.
- Opětovné **synchronizace klíčů úložiště** – pracovní prostor uchovává klíče pro účet úložiště. Pokud účet úložiště změní klíče, můžete kliknout na znovu **synchronizovat klíče** a synchronizovat klíče s pracovním prostorem.

Pro správu webových služeb přidružených k tomuto pracovnímu prostoru studia (Classic) použijte portál Machine Learning Web Services. Úplné informace najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md) .

> [!NOTE]
> Chcete-li nasadit nebo spravovat nové webové služby, musíte mít přiřazenou roli přispěvatel nebo správce v předplatném, ve kterém je webová služba nasazená. Pokud budete pozvat jiného uživatele k pracovnímu prostoru Machine Learning Studio (Classic), musíte je přiřadit k roli přispěvatel nebo správce v předplatném, aby mohli nasadit nebo spravovat webové služby. 
> 
>Další informace o nastavení přístupových oprávnění najdete v tématu [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure Portal](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [nasazení Machine Learning s využitím šablon Azure Resource Manager](deploy-with-resource-manager-template.md).