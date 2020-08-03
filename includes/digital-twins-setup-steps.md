---
author: baanders
description: zahrnutý soubor pro postup – přehled v nastavení digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b8751685cfd273171cb8a5b500e4bed41ce2f59e
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407439"
---
>[!NOTE]
>Tyto operace mají být dokončeny uživatelem s rolí *vlastníka* v předplatném Azure. I když některé části je možné dokončit bez těchto oprávnění zvýšené úrovně, bude se muset spolupracovat vlastník, aby se úplně nastavila použitelná instance. Další informace najdete v části [*požadavky: požadovaná oprávnění*](#prerequisites-permission-requirements) níže.

Úplná instalace nové instance digitálních vláken Azure se skládá ze tří částí:
1. **Vytvoření instance**
2. **Nastavení uživatelských oprávnění pro přístup k uživateli**: uživatelé Azure musí mít v instanci Azure Digital zdvojene *(Preview) přiřazenou roli Vlastník Azure Digital userss (Preview)* , aby ji bylo možné spravovat a její data. V tomto kroku se jako vlastník předplatného Azure přiřadí tato role osobě, která bude spravovat vaši instanci digitálních vláken Azure. Může to být sami nebo někdo jiný ve vaší organizaci.
3. **Nastavení oprávnění k přístupu pro klientské aplikace**: je běžné napsat klientskou aplikaci, která bude použita k interakci s vaší instancí. Aby klientská aplikace měla přístup k digitálním událostem Azure, musíte nastavit *registraci aplikace* v [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) , kterou klientská aplikace použije k ověření instance.

Abyste mohli pokračovat, budete potřebovat předplatné Azure. Můžete si ho nastavit zdarma [tady](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
