---
title: "V tomto kurzu vytvoříte zásobník Azure nabízí | Microsoft Docs"
description: "Naučte se vytvořit nabídku zásobník Azure včetně plány a kvót."
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
ms.openlocfilehash: 083b5e20b89f22cb8e523926858fe9ffb1441319
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-offer-azure-stack-iaas-services"></a>Kurz: nabízet služby Azure IaaS zásobníku
Jako správce cloudu Azure zásobníku můžete vytvořit nabídky, které vaši uživatelé (někdy označované jako klienty) můžete přihlásit k odběru. Pomocí svého předplatného, mohou uživatelé pak využívání služeb Azure zásobníku.

V tomto kurzu se dozvíte, jak vytvořit nabídky k uživatelům umožňují vytvářet virtuální počítače na základě systému Windows Server 2016 Datacenter zásobník Azure marketplace, bitové kopie, který jste nahráli v [předchozí kurzu](asdk-marketplace-item.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření nabídky
> * Vytvoření plánu
> * Nastavení kvót
> * Veřejná sada nabídka

V zásobníku Azure služby se doručují na uživatele, kteří používají odběry, nabídky a plány. Uživatelé se mohou přihlásit k více nabídky. Nabízí může mít jeden nebo více plánů a plány může mít jednu nebo více služeb, jak je znázorněno v následujícím diagramu:

![Odběry, nabídky a plány](media/asdk-offer-services/sop.png)

Jako operátor zásobník Azure nabídky služeb budete nejprve vyzváni k vytvoření nabídky, pak plánu a nakonec kvóty. Po vytvoření nabídku, uživatelé zásobník Azure můžete pak přihlásit k odběru nabízí prostřednictvím portálu user portal.

## <a name="create-an-offer"></a>Vytvoření nabídky
**Nabízí** jsou skupiny jeden nebo více plánů, které operátory zásobník Azure k dispozici pro uživatele se koupit nebo přihlášení k odběru.

1. Přihlaste se k [portál Azure zásobníku](https://adminportal.local.azurestack.external) jako správce cloudu.

2. Klikněte na tlačítko **nové** > **nabízí + plány** > **nabízejí**.

   ![Nová nabídka](media/asdk-offer-services/new-offer.png)

2. V **nové nabízejí** část, vyplňte **zobrazovaný název** a **název prostředku**a poté vyberte nový nebo existující **skupiny prostředků**. Zobrazovaný název není na nabídku veřejného popisný název, který uživatelé uvidí. Operátor cloudu můžete vidět název prostředku, který je používán admins pro práci s nabídkou jako prostředek Azure Resource Manager.

   ![Zobrazované jméno](media/asdk-offer-services/offer-display-name.png)


## <a name="create-a-plan"></a>Vytvoření plánu
Po zadání informací o základní nabídce, je nutné přidat alespoň jeden základního plánu na nabídku. 

**Plány** povolit operátorům Azure zásobník skupiny služeb a jejich přidružené kvóty, nabízena uživatelům.

1. Klikněte na tlačítko **základní plány**a v **plán** klikněte na tlačítko **přidat** přidat nový plán do nabídky.

   ![Přidat plán](media/asdk-offer-services/new-plan.png)

2. V **nový plán** část, vyplňte **zobrazovaný název** a **název prostředku**. Zobrazovaný název je popisný název plánu, který uživatelé uvidí. Operátor cloudu můžete vidět název prostředku používaný operátorům cloudu pro práci s plánem jako prostředek Azure Resource Manager.

   ![Plánování zobrazovaný název](media/asdk-offer-services/plan-display-name.png)

3. V dalším kroku vyberte služby, které mají být zahrnuty v plánu. Pokud chcete nabízet služby IaaS, klikněte na tlačítko **služby**, vyberte **Microsoft.Compute**, **Microsoft.Network**, a **Microsoft.Storage**a potom Klikněte na tlačítko **vyberte**.

   ![Plánování služby](media/asdk-offer-services/select-services.png)


## <a name="set-quotas"></a>Nastavení kvót
Nyní, když nabídku plán, který zahrnuje služby, je nutné nastavit kvóty pro každou z nich. 

**Kvóty** určit množství prostředků, které může uživatel využít u každé služby zahrnuty v plánu nabízený.

1. Klikněte na tlačítko **kvóty**a potom vyberte službu, pro který chcete vytvořit kvótu. 

   Chcete-li začít, nejdřív Vytvořte kvótu pro výpočetní služba. Vyberte v seznamu názvů **Microsoft.Compute** obor názvů a pak klikněte na **vytvořit nové kvóty**.
   
   ![Vytvořit novou kvótu](media/asdk-offer-services/create-quota.png)

2. Na **vytvořit kvótu** části, zadejte název pro kvótu, nastavit kvóty pro požadované parametry a klikněte na tlačítko **OK**.

   ![Název kvóty](media/asdk-offer-services/quota-properties.png)

3. Vybrat kvótu, kterou jste vytvořili pro **Microsoft.Compute** služby.

   ![Vyberte kvóty](media/asdk-offer-services/set-quota.png)

4. Opakujte kroky 1 – 3 nastavit kvóty pro sítě a úložiště služby a klikněte na tlačítko OK v oddílu kvóty. Klikněte na tlačítko **OK** na **nový plán** části a dokončete nastavení plánu. 

   ![Nastavit všechny kvóty](media/asdk-offer-services/all-quotas-set.png)

5. Vytvoření nabídky kliknutím **vytvořit** v oddílu plán. Oznámení se zobrazí, když nabídka je úspěšně vytvořen a nebude uvedené jako dostupné nabídku.

   ![Vytvoření nabídky](media/asdk-offer-services/offer-complete.png)

## <a name="set-offer-to-public"></a>Veřejná sada nabídka
Nabízí musí být provedeny veřejné uživatelům zobrazit, když vyberete nabídku pro přihlášení k odběru. 

Nabízí může být:
- **Veřejné**: viditelné pro všechny uživatele.
- **Privátní**: viditelné na správce cloudu. Užitečné při navrhování plán nebo nabídky, nebo pokud chce správce cloudu vytvořit každé předplatné pro uživatele.
- **Vyřazeno z provozu**: Uzavřené pro nové odběratele. Můžete použít Správce cloudu vyřazení zabránit budoucím odběry, ale ponechat nezměněný aktuální odběratele.

> [!TIP]
> Změny nabídku nejsou okamžitě viditelné pro uživatele. Chcete-li zobrazit změny, uživatelé možná muset odhlásit a znovu se přihlaste k [portálu user portal](https://portal.local.azurestack.external) zobrazíte nové nabídky.

Nastavení nové nabídky veřejný: 

1. V nabídce řídícího panelu klikněte na tlačítko **nabízí** a pak klikněte na nabídku jste vytvořili.

2. Klikněte na **Změnit stav** a potom na **Veřejné**.

   ![Veřejné stavu](media/asdk-offer-services/set-public.png)

3. Nabídka bude nyní k dispozici na portálu Azure zásobník uživatele.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření nabídky
> * Vytvoření plánu
> * Nastavení kvót
> * Veřejná sada nabídka

Přechodu na v dalším kurzu se dozvíte, jak k odběru nabídku, kterou jste právě vytvořili jako uživatel Azure zásobníku.

> [!div class="nextstepaction"]
> [Přihlášení k odběru nabídky](asdk-subscribe-services.md)