---
title: Kurz – použití revizí v API Management k bezpečnému provedení neprůlomových změn rozhraní API
titleSuffix: Azure API Management
description: Postupujte podle kroků v tomto kurzu a zjistěte, jak provádět nevýznamné změny s využitím revizí ve službě API Management.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: 3804bfb2a269c431b1a00947f5c7613566a78f49
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377483"
---
# <a name="tutorial-use-revisions-to-make-non-breaking-api-changes-safely"></a>Kurz: použití revizí k bezpečnému provedení neprůlomových změn rozhraní API
Jakmile bude vaše rozhraní API připravené a začnou ho využívat vývojáři, časem bude potřeba provádět změny rozhraní API, aniž by to mělo negativní vliv na volající vašeho rozhraní API. Také je užitečné informovat vývojáře o prováděných změnách. 

V Azure API Management použijte *Revize* k provedení neprůlomových změn rozhraní API, abyste mohli bezpečně modelovat a testovat změny. Až budete připraveni, můžete provést revizi aktuální a nahradit aktuální rozhraní API. 

Základní informace najdete v tématu [verze & revize](https://azure.microsoft.com/blog/versions-revisions/) a [verze API s využitím Azure API Management](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání nové revize
> * Provedení nevýznamných změn revize
> * Nastavení revize jako aktuální a přidání položky protokolu změn
> * Procházení portálu pro vývojáře a zobrazení změn a protokolu změn

:::image type="content" source="media/api-management-getstarted-revise-api/azure-portal.png" alt-text="Revize rozhraní API v Azure Portal":::

## <a name="prerequisites"></a>Předpoklady

+ Seznamte se s [terminologií služby Azure API Management](api-management-terminology.md).
+ Dokončete následující rychlý Start: [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).
+ Projděte si také následující kurz: Navíc kurzu: [Import a publikování vašeho prvního rozhraní API](import-and-publish.md).

## <a name="add-a-new-revision"></a>Přidání nové revize

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a přejděte k instanci API Management.
1. Vyberte **Rozhraní API**.
2. V seznamu rozhraní API vyberte **ukázková konferenční konference API** (nebo jiné rozhraní API, ke kterému chcete přidat revize).
3. Vyberte kartu **Revize** .
4. Vyberte **+ Přidat revizi**.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-01-add-new-revision.png" alt-text="Přidání revize rozhraní API":::

    > [!TIP]
    > Můžete také vybrat **Přidat revizi** v místní nabídce ( **...** ) rozhraní API.

5. Zadejte popis nové revize, který vám pomůže zapamatovat si, k čemu se bude používat.
6. Vyberte **vytvořit** ,
7. Teď se vytvoří vaše nová revize.

    > [!NOTE]
    > Původní rozhraní API zůstane v **Revizi 1**. Vaši uživatelé nadále volají tuto revizi, dokud se nerozhodnete nastavit jako aktuální jinou revizi.

## <a name="make-non-breaking-changes-to-your-revision"></a>Provedení nevýznamných změn revize

1. V seznamu rozhraní API vyberte rozhraní **Demo Conference API**.
1. V horní části obrazovky vyberte kartu **Návrh**.
1. Všimněte si, že jako aktuálně vybraná revize v **selektoru revize** (přímo nad kartou Návrh) se zobrazí **Revize 2**.

    > [!TIP]
    > Pomocí selektoru revize můžete přepínat mezi revizemi, na kterých chcete pracovat.
1. Vyberte **+ Přidat operaci**.
1. Nastavte novou operaci na **POST** a Název, Zobrazovaný název a adresu URL operace na **test**.
1. **Uložte** novou operaci.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-02-make-changes.png" alt-text="Úprava revize":::
1. Nyní jste provedli změnu **Revize 2**. Pomocí **selektoru revizí** v horní části stránky přepněte zpět na **revizi 1**.
1. Všimněte si, že vaše nová operace se v **Revizi 1** nezobrazí. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Nastavení revize jako aktuální a přidání položky protokolu změn

1. V nabídce v horní části stránky vyberte kartu **Revize**.
1. Otevřete místní nabídku ( **...** ) pro **Revizi 2**.
1. Vyberte **nastavit jako aktuální**.
1. Zaškrtněte políčko **publikovat do veřejného protokolu změn pro toto rozhraní API** , pokud chcete publikovat poznámky k této změně. Zadejte popis změny, kterou vývojáři uvidí, například: **testování revizí. Byla přidána nová operace "test".**
1. **Revize 2** je teď nastavená jako aktuální.

    :::image type="content" source="media/api-management-getstarted-revise-api/revisions-menu.png" alt-text="Nabídka revize v okně Revize":::


## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Procházení portálu pro vývojáře a zobrazení změn a protokolu změn

Pokud jste si vyzkoušeli [portál pro vývojáře](api-management-howto-developer-portal-customize.md), můžete si prohlédnout změny v rozhraní API a protokol změn tam.

1. V Azure Portal vyberte **rozhraní API**.
1. V horní nabídce vyberte **portál pro vývojáře** .
1. Na portálu pro vývojáře vyberte **rozhraní API** a pak vyberte **ukázková konferenční rozhraní API**.
1. Všimněte si, že je teď k dispozici vaše nová operace **test**.
1. V blízkosti názvu rozhraní API vyberte protokol **změn** .
1. Všimněte si, že se v tomto seznamu zobrazí vaše položka protokolu změn.

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
