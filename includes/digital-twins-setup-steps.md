---
author: baanders
description: zahrnutý soubor pro postup – přehled v nastavení digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 5ab6812d144122c61018ad740892db869a7ba43d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205475"
---
>[!NOTE]
>Tyto operace mají být dokončeny uživatelem s výkonem pro správu prostředků i přístupu uživatelů k předplatnému Azure. I když je možné provést některé kroky s nižšími oprávněními, bude nutné, aby se spolupráce někoho s těmito oprávněními kompletně nastavila v použitelné instanci. Další informace najdete v části [*požadavky: požadovaná oprávnění*](#prerequisites-permission-requirements) níže.

Úplná instalace nové instance digitálních vláken Azure se skládá ze dvou částí:
1. **Vytvoření instance**
2. **Nastavení uživatelských oprávnění pro přístup k uživateli**: uživatelé Azure musí mít v instanci Azure Digital zdvojene *(Preview) přiřazenou roli Vlastník Azure Digital userss (Preview)* , aby ji bylo možné spravovat a její data. V tomto kroku se jako vlastník nebo správce předplatného Azure přiřadí tato role osobě, která bude spravovat vaši instanci digitálních vláken Azure. Může to být sami nebo někdo jiný ve vaší organizaci.