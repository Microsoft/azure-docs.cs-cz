---
title: Použití revizí k bezpečnému provedení nevýznamných změn ve službě Azure API Management | Microsoft Docs
description: Postupujte podle kroků v tomto kurzu a zjistěte, jak provádět nevýznamné změny s využitím revizí ve službě API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: c1c884e05d357db7e23574dbd31f206d6c3fe23c
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2018
ms.locfileid: "41920673"
---
# <a name="use-revisions-to-make-non-breaking-changes-safely"></a>Použití revizí k bezpečnému provedení nevýznamných změn
Jakmile bude vaše rozhraní API připravené a začnou ho využívat vývojáři, časem bude potřeba provádět změny rozhraní API, aniž by to mělo negativní vliv na volající vašeho rozhraní API. Také je užitečné informovat vývojáře o prováděných změnách. Ve službě Azure API Management k tomu můžeme použít **revize**. Další informace najdete v tématech [Verze a revize](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/) a [Správa verzí rozhraní API pomocí služby Azure API Management](https://blogs.msdn.microsoft.com/apimanagement/2017/09/13/api-versioning-with-azure-api-management/).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání nové revize
> * Provedení nevýznamných změn revize
> * Nastavení revize jako aktuální a přidání položky protokolu změn
> * Procházení portálu pro vývojáře a zobrazení změn a protokolu změn

![Protokol změn na portálu pro vývojáře](media/api-management-getstarted-revise-api/azure_portal.PNG)

## <a name="prerequisites"></a>Požadavky

+ Projděte si následující rychlý start: [Vytvoření instance Azure API Managementu](get-started-create-service-instance.md).
+ Projděte si také následující kurz: Navíc kurzu: [Import a publikování vašeho prvního rozhraní API](import-and-publish.md).

## <a name="add-a-new-revision"></a>Přidání nové revize

1. Vyberte stránku **Rozhraní API**.
2. V seznamu rozhraní API vyberte rozhraní **Demo Conference API** (nebo jiné rozhraní API, ke kterému chcete přidat revize).
3. V nabídce v horní části stránky klikněte na kartu **Revize**.
4. Vyberte **+ Přidat revizi**.

    > [!TIP]
    > Můžete také zvolit **Přidat revizi** v místní nabídce (**...**) rozhraní API.
    
    ![Nabídka Revize v horní části obrazovky](media/api-management-getstarted-revise-api/TopMenu.PNG)

5. Zadejte popis nové revize, který vám pomůže zapamatovat si, k čemu se bude používat.
6. Vyberte **Vytvořit**.
7. Teď se vytvoří vaše nová revize.

    > [!NOTE]
    > Původní rozhraní API zůstane v **Revizi 1**. Vaši uživatelé nadále volají tuto revizi, dokud se nerozhodnete nastavit jako aktuální jinou revizi.

## <a name="make-non-breaking-changes-to-your-revision"></a>Provedení nevýznamných změn revize

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
4. Zaškrtněte políčko **Odeslat do veřejného protokolu změn pro toto rozhraní API**, pokud chcete odesílat poznámky o této změně. Zadejte popis provedené změny, který se zobrazí vývojářům, například: **Testování revizí. Přidání nové operace test.**
5. **Revize 2** je teď nastavená jako aktuální.

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Procházení portálu pro vývojáře a zobrazení změn a protokolu změn

1. Na webu Azure Portal vyberte **Rozhraní API**.
2. V horní nabídce vyberte **Portál pro vývojáře**.
3. Vyberte **Rozhraní API** a pak rozhraní **Demo Conference API**.
4. Všimněte si, že je teď k dispozici vaše nová operace **test**.
5. Pod názvem rozhraní API vyberte **Historie změn rozhraní API**.
6. Všimněte si, že se v tomto seznamu zobrazí vaše položka protokolu změn.

    ![Portál pro vývojáře](media/api-management-getstarted-revise-api/developer_portal.PNG)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání nové revize
> * Provedení nevýznamných změn revize
> * Nastavení revize jako aktuální a přidání položky protokolu změn
> * Procházení portálu pro vývojáře a zobrazení změn a protokolu změn

Přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Publikování několika verzí vašeho rozhraní API](api-management-get-started-publish-versions.md)
