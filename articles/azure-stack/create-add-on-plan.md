---
title: V tomto článku, zjistíte, jak aktualizovat zásobník Azure nabízí a plány | Microsoft Docs
description: Tento článek popisuje, jak zobrazit a upravit stávající nabídky Azure zásobníku a plány.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.custom: mvc
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a84148a3ac31d51ff30cebffab00e5fec8fdaa87
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238363"
---
# <a name="azure-stack-add-on-plans"></a>Plány rozšíření Azure zásobníku
Jako operátor zásobník Azure vytvořte plány, které obsahují požadované služby a použít kvóty pro vaši uživatelé přihlásit k odběru. Tyto [ *základní plány* ](azure-stack-create-plan.md) obsahovat základní služeb, které mají být nabízí uživatelům a může mít pouze jeden základního plánu na nabídku. Pokud budete muset upravit vaši nabídku, můžete použít *rozšíření plány* který vám umožní změnit plán rozšířit počítače, úložiště, nebo sítě kvóty nabízené s základního plánu. 

I když kombinování vše v jednom plánu může být v některých případech optimální, můžete mít základní plánu a nabízí další služby pomocí rozšíření plány. Může například rozhodnout, že nabízí IaaS služby v rámci základního plánu u všech služeb PaaS, které jsou považovány za rozšíření plány. Plány můžete také použít k řízení spotřeby prostředků ve vašem prostředí Azure zásobníku. Například pokud chcete uživatelům mějte na paměti jejich využití prostředků, může mít poměrně malý základního plánu (v závislosti na požadované služby) a jak uživatelé získat přístup kapacitu, by dostanete upozornění, že již využívali přidělení prostředků na základě jejich přiřazené plánu. Odtud může uživatele vyberte plán rozšíření k dispozici pro další prostředky. 

> [!NOTE]
> Při přidání plán rozšíření k existujícímu předplatnému nabídka, další prostředky může trvat až jednu hodinu, než se objeví. 

## <a name="create-an-add-on-plan"></a>Vytváření rozšíření schématu
Rozšíření plány jsou vytvořeny úpravou existující nabídku:

1. Přihlaste se k portálu správce Azure zásobníku jako správce cloudu.
2. Použít stejný postup lze [vytvořit nový plán základní](azure-stack-create-plan.md) k vytvoření nového plánu nabídky služeb, které nebyly nabízí dříve. V tomto příkladu služby Key Vault (Microsoft.KeyVault) bude obsahovat nový plán.
3. Na portálu správce, klikněte na tlačítko **nabízí** a pak vyberte nabídku na Aktualizovat plán rozšíření.

   ![](media/create-add-on-plan/1.PNG)

4.  Přejděte do dolní části nabídky vlastnosti a vyberte **rozšíření plány**. Klikněte na tlačítko **Add** (Přidat).
   
    ![](media/create-add-on-plan/2.PNG)

5. Vyberte plán, který chcete přidat. V tomto příkladu se nazývá plánu **klíč trezoru plán**a potom klikněte na **vyberte** pro přidání do nabídky plánu. Měli byste obdržet oznámení, že plán byl úspěšně přidán do nabídky.
   
    ![](media/create-add-on-plan/3.PNG)

6. Projděte si seznam doplněk, který uvedené plány, které jsou součástí nabídku k ověření, že nové doplněk plánů.
   
    ![](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>Další postup
[Vytvoření nabídky](azure-stack-create-offer.md)