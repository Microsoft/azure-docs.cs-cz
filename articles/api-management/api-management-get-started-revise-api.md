---
title: Použití revizí k bezpečnému provedení nevýznamných změn v API Management
titleSuffix: Azure API Management
description: Postupujte podle kroků v tomto kurzu a zjistěte, jak provádět nevýznamné změny s využitím revizí ve službě API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 7a4655b20fabcc72e02037de05dd0ef7c4671e52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86254907"
---
# <a name="use-revisions-to-make-non-breaking-changes-safely"></a>Použití revizí k bezpečnému provedení nevýznamných změn
Jakmile bude vaše rozhraní API připravené a začnou ho využívat vývojáři, časem bude potřeba provádět změny rozhraní API, aniž by to mělo negativní vliv na volající vašeho rozhraní API. Také je užitečné informovat vývojáře o prováděných změnách. Ve službě Azure API Management k tomu můžeme použít **revize**. Další informace najdete v tématech [Verze a revize](https://azure.microsoft.com/blog/versions-revisions/) a [Správa verzí rozhraní API pomocí služby Azure API Management](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání nové revize
> * Provedení nevýznamných změn revize
> * Nastavení revize jako aktuální a přidání položky protokolu změn
> * Procházení portálu pro vývojáře a zobrazení změn a protokolu změn

![Protokol změn na portálu pro vývojáře](media/api-management-getstarted-revise-api/azure_portal.PNG)

## <a name="prerequisites"></a>Požadavky

+ Seznamte se s [terminologií služby Azure API Management](api-management-terminology.md).
+ Dokončete následující rychlý Start: [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).
+ Projděte si také následující kurz: Navíc kurzu: [Import a publikování vašeho prvního rozhraní API](import-and-publish.md).

## <a name="add-a-new-revision"></a>Přidání nové revize

![Přidání revize rozhraní API](media/api-management-getstarted-revise-api/07-AddRevisions-01-AddNewRevision.png)

1. Vyberte stránku **Rozhraní API**.
2. V seznamu rozhraní API vyberte rozhraní **Demo Conference API** (nebo jiné rozhraní API, ke kterému chcete přidat revize).
3. V nabídce v horní části stránky klikněte na kartu **Revize**.
4. Vyberte **+ Přidat revizi**.

    > [!TIP]
    > Můžete také zvolit **Přidat revizi** v místní nabídce (**...**) rozhraní API.

5. Zadejte popis nové revize, který vám pomůže zapamatovat si, k čemu se bude používat.
6. Vyberte **Vytvořit**.
7. Teď se vytvoří vaše nová revize.

    > [!NOTE]
    > Původní rozhraní API zůstane v **Revizi 1**. Vaši uživatelé nadále volají tuto revizi, dokud se nerozhodnete nastavit jako aktuální jinou revizi.

## <a name="make-non-breaking-changes-to-your-revision"></a>Provedení nevýznamných změn revize

![Úprava revize](media/api-management-getstarted-revise-api/07-AddRevisions-02-MakeChanges.png)

1. V seznamu rozhraní API vyberte rozhraní **Demo Conference API**.
2. V horní části obrazovky vyberte kartu **Návrh**.
3. Všimněte si, že jako aktuálně vybraná revize v **selektoru revize** (přímo nad kartou Návrh) se zobrazí **Revize 2**.

    > [!TIP]
    > Pomocí selektoru revize můžete přepínat mezi revizemi, na kterých chcete pracovat.

4. Vyberte **+ Přidat operaci**.
5. Nastavte novou operaci na **POST** a Název, Zobrazovaný název a adresu URL operace na **test**.
6. **Uložte** novou operaci.
7. Právě jsme provedli změnu **Revize 2**. Pomocí **selektoru revize** v horní části stránky přepněte zpět na **Revizi 1**.
8. Všimněte si, že vaše nová operace se v **Revizi 1** nezobrazí. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Nastavení revize jako aktuální a přidání položky protokolu změn

1. V nabídce v horní části stránky vyberte kartu **Revize**.

    ![Nabídka Revize na obrazovce Revize](media/api-management-getstarted-revise-api/RevisionsMenu.PNG)

2. Otevřete místní nabídku (**...**) pro **Revizi 2**.
3. Vyberte **Nastavit jako aktuální**.
4. Zaškrtněte políčko **Odeslat do veřejného protokolu změn pro toto rozhraní API**, pokud chcete odesílat poznámky o této změně. Zadejte popis změny, kterou vývojáři uvidí, například: **testování revizí. Byla přidána nová operace "test".**
5. **Revize 2** je teď nastavená jako aktuální.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Procházení portálu pro vývojáře a zobrazení změn a protokolu změn

1. V Azure Portal vyberte **rozhraní API**.
2. V horní nabídce vyberte **Portál pro vývojáře**.
3. Vyberte **Rozhraní API** a pak rozhraní **Demo Conference API**.
4. Všimněte si, že je teď k dispozici vaše nová operace **test**.
5. V blízkosti názvu rozhraní API klikněte na protokol **změn** .
6. Všimněte si, že se v tomto seznamu zobrazí vaše položka protokolu změn.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání nové revize
> * Provedení nevýznamných změn revize
> * Nastavení revize jako aktuální a přidání položky protokolu změn
> * Procházení portálu pro vývojáře a zobrazení změn a protokolu změn

Přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Publikování několika verzí rozhraní API](api-management-get-started-publish-versions.md)
