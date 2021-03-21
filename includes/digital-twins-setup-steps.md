---
author: baanders
description: zahrnutý soubor pro postup – přehled v nastavení digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: c1cfab8c9e68d9e6b0c3b84e8848645a50c24710
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100560787"
---
Úplná instalace nové instance digitálních vláken Azure se skládá ze dvou částí:
1. **Vytvoření instance**
2. **Nastavení oprávnění pro přístup uživatelů**: uživatelé Azure musí mít v instanci digitálních vláken Azure, která je schopná spravovat data a její data, roli *vlastníka dat Azure s digitálními zdvojenými daty* . V tomto kroku se jako vlastník nebo správce předplatného Azure přiřadí tato role osobě, která bude spravovat vaši instanci digitálních vláken Azure. Může to být sami nebo někdo jiný ve vaší organizaci.
 
>[!NOTE]
>Tyto operace mají být dokončeny uživatelem s výkonem pro správu prostředků i přístupu uživatelů k předplatnému Azure. I když je možné provést některé kroky s nižšími oprávněními, bude nutné, aby se spolupráce někoho s těmito oprávněními kompletně nastavila v použitelné instanci. Další informace najdete v části [*požadavky: požadovaná oprávnění*](#prerequisites-permission-requirements) níže.