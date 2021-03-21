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
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: acb121bb00df481c926ebed9594bf0fe1b9b17ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100546631"
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
    > Můžete také vybrat **Přidat revizi** v místní nabídce (**...**) rozhraní API.

5. Zadejte popis nové revize, který vám pomůže zapamatovat si, k čemu se bude používat.
6. Vyberte **vytvořit**,
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

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V nabídce v horní části stránky vyberte kartu **Revize**.
1. Otevřete místní nabídku (**...**) pro **Revizi 2**.
1. Vyberte **nastavit jako aktuální**.
1. Zaškrtněte políčko **publikovat do veřejného protokolu změn pro toto rozhraní API** , pokud chcete publikovat poznámky k této změně. Zadejte popis změny, kterou vývojáři uvidí, například: **testování revizí. Byla přidána nová operace "test".**
1. **Revize 2** je teď nastavená jako aktuální.

    :::image type="content" source="media/api-management-getstarted-revise-api/revisions-menu.png" alt-text="Nabídka revize v okně Revize":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li začít používat rozhraní příkazového řádku Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Pomocí tohoto postupu můžete vytvořit a aktualizovat verzi.

1. Spuštěním příkazu [AZ APIM API list](/cli/azure/apim/api#az_apim_api_list) zobrazíte ID rozhraní API:

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

   ID rozhraní API, které se má použít v dalším příkazu, je `Name` hodnota. Revize rozhraní API je ve `ApiRevision` sloupci.

1. Pokud chcete vytvořit vydání, pomocí poznámky k verzi spusťte příkaz [AZ APIM API Release Create](/cli/azure/apim/api/release#az_apim_api_release_create) :

   ```azurecli
   az apim api release create --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --api-revision 2 --service-name apim-hello-world \
       --notes 'Testing revisions. Added new "test" operation.'
   ```

   Verze, kterou vydáte, se stala aktuální revizí.

1. Chcete-li zobrazit vaše verze, použijte příkaz [AZ APIM API Release list](/cli/azure/apim/api/release#az_apim_api_release_list) :

   ```azurecli
   az apim api release list --resource-group apim-hello-word-resource-group \
       --api-id echo-api --service-name apim-hello-world --output table
   ```

   Poznámky, které zadáte, se zobrazí v protokolu změn. Můžete je zobrazit ve výstupu předchozího příkazu.

1. Když vytvoříte vydání, `--notes` parametr je nepovinný. Poznámky můžete přidat nebo změnit později pomocí příkazu [AZ APIM API Release Update](/cli/azure/apim/api/release#az_apim_api_release_update) :

   ```azurecli
   az apim api release update --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --release-id 00000000000000000000000000000000 \
       --service-name apim-hello-world --notes "Revised notes."
   ```

   `Name`Pro ID vydané verze použijte hodnotu ve sloupci.

Všechny vydané verze můžete odebrat spuštěním příkazu [AZ APIM API Release Delete ](/cli/azure/apim/api/release#az_apim_api_release_delete) :

```azurecli
az apim api release delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --release-id 00000000000000000000000000000000 
    --service-name apim-hello-world
```

---

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
> [Publikování několika verzí vašeho rozhraní API](api-management-get-started-publish-versions.md)
