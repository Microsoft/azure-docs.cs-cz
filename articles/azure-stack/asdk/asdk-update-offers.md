---
title: "V tomto kurzu zjistíte, jak aktualizovat zásobník Azure nabízí a plány | Microsoft Docs"
description: "Tento článek popisuje, jak zobrazit a upravit stávající nabídky Azure zásobníku a plány."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 3b5d2ab5e924f578f5838d11b0d2058aacf67dec
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-update-offers-and-plans"></a>Kurz: aktualizace nabízí a plány
Jako operátor zásobník Azure vytvořte plány, které obsahují požadované služby a použít kvóty pro vaši uživatelé přihlásit k odběru. Tyto *základní plány* obsahovat základní služeb, které mají být nabízí uživatelům a může mít pouze jeden základního plánu na nabídku. Pokud budete muset upravit vaši nabídku, můžete použít *rozšíření plány* který vám umožní změnit plán rozšířit počítače, úložiště, nebo sítě kvóty nabízené s základního plánu. 

I když kombinování vše v jednom plánu může být v některých případech optimální, můžete mít základní plánu a nabízí další služby pomocí rozšíření plány. Může například rozhodnout, že nabízí IaaS služby v rámci základního plánu u všech služeb PaaS, které jsou považovány za rozšíření plány. Plány můžete také použít k řízení spotřeby prostředků ve vašem prostředí omezené ASDK. Například pokud chcete uživatelům mějte na paměti jejich využití prostředků, může mít poměrně malý základního plánu (v závislosti na požadované služby) a jak uživatelé získat přístup kapacitu, by dostanete upozornění, že již využívali přidělení prostředků na základě jejich přiřazené plánu. Odtud může uživatele vyberte plán rozšíření k dispozici pro další prostředky. 

> [!NOTE]
> Při přidání plán rozšíření k existujícímu předplatnému nabídka, další prostředky může trvat až jednu hodinu, než se objeví. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytváření rozšíření schématu 
> * Přihlášení k odběru do plánu rozšíření

## <a name="create-an-add-on-plan"></a>Vytváření rozšíření schématu
**Rozšíření schémat** vytvářejí úpravou existující nabídku.

1. Přihlaste se k [portál Azure zásobníku](https://adminportal.local.azurestack.external) jako správce cloudu.
2. Postupujte stejným způsobem použít dříve k [vytvořit základní plán](asdk-offer-services.md) k vytvoření nového plánu nabídky služeb, které nebyly nabízí dříve. V tomto příkladu služby Key Vault (Microsoft.KeyVault) zahrnuty v plánu.
3. Na portálu správce, klikněte na tlačítko **nabízí** a pak vyberte nabídku na Aktualizovat plán rozšíření.

   ![](media/asdk-update-offers/1.PNG)

4.  Přejděte do dolní části nabídky vlastnosti a vyberte **rozšíření plány**. Klikněte na tlačítko **Add** (Přidat).
   
    ![](media/asdk-update-offers/2.PNG)

5. Vyberte plán, který chcete přidat. V tomto příkladu se nazývá plánu **klíč trezoru plán**a potom klikněte na **vyberte** pro přidání do nabídky plánu. Měli byste obdržet oznámení, že plán byl úspěšně přidán do nabídky.
   
    ![](media/asdk-update-offers/3.PNG)

6. Projděte si seznam doplněk, který uvedené plány, které jsou součástí nabídku k ověření, že nové doplněk plánů.
   
    ![](media/asdk-update-offers/4.PNG)

## <a name="subscribe-to-the-add-on-plan"></a>Přihlášení k odběru do plánu rozšíření
Musíte se přihlásit k portálu user portal zásobník Azure rozšířit stávající předplatné Azure zásobníku se plán rozšíření.

Tyto kroky použijte ke zjištění další prostředky, které byly nabídnuty operátorem zásobník Azure a přidání plán rozšíření na nabídku, které jste dříve přihlášení k odběru.

1. Přihlaste se k [portálu user portal](https://portal.local.azurestack.external).
2. Chcete-li najít rozšíření s plánem rozšíření odběr, klikněte na tlačítko **další služby**, klikněte na tlačítko **odběry**, potom vyberte své předplatné.
   
    ![](media/asdk-update-offers/5.PNG)

3.  V přehledu předplatné, klikněte na tlačítko **přidat plán**.
   
    ![](media/asdk-update-offers/6.PNG)

4. V okně Přidat plán vyberte rozšíření chcete přidat do odběru. V tomto příkladu **klíč trezoru plán** je vybrána. Následně byste měli obdržet oznámení, že plán rozšíření byla úspěšná získali a budete muset aktualizovat na portálu přístup k předplatnému aktualizované.
   
    ![](media/asdk-update-offers/7.PNG)

5. Nakonec zkontrolujte plány rozšíření přidružené k předplatnému, ujistěte se, že rozšíření schématu bylo úspěšně přidáno.
   
    ![](media/asdk-update-offers/8.PNG)


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytváření rozšíření schématu 
> * Přihlášení k odběru do plánu rozšíření

> [!div class="nextstepaction"]
> [Vytvoření virtuálního počítače ze šablony](asdk-create-vm-template.md)

