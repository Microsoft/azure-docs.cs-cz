---
title: SKU pro Image kontejnerů Azure | Azure Marketplace
description: Konfigurace SKU pro kontejner Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 5b69eea8ad7fd4c62925b50434b653118890e280
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823150"
---
# <a name="container-skus-tab"></a>Karta SKU kontejneru

Karta **SKU** na nové stránce **nabídky** umožňuje vytvořit jednu nebo více SKU a přidružit je k nové nabídce.  Pomocí různých SKU můžete odlišit řešení pomocí sad funkcí, modelů fakturace nebo jiné charakteristiky.

## <a name="sku-settings"></a>Nastavení SKU

Když začnete vytvářet novou nabídku, nejsou k této nabídce přidružené žádné skladové položky. Chcete-li vytvořit novou SKLADOVOU položku, postupujte takto:

1. Na kartě SKU vyberte **Nová SKU** .

   ![Výzva k zadání nové SKU](./media/containers-sku-settings.png)

2. Zadejte požadované informace o SKU a kontejneru. Každá SKU odpovídá imagi kontejneru. Existují dvě části SKU:

    -   Metadata SKU
    -   Metadata kontejneru


### <a name="sku-metadata"></a>Metadata SKU

Metadata SKU obsahují informace o zobrazení prezentace pro výpis kontejneru.

![Metadata SKU](./media/containers-sku-details.png)


### <a name="container-metadata"></a>Metadata kontejneru

Metadata kontejneru obsahují referenční informace o vašich podrobnostech úložiště imagí v rámci Azure Container Registry (ACR). Azure Marketplace zkopíruje tento obrázek do registru, který je specifický pro Marketplace, a následně zpřístupní image zákazníkům po certifikaci. Všechny požadavky od uživatele Azure, které využívají Azure Marketplace image kontejneru, se zpracovávají z veřejného registru na webu Marketplace, nikoli ACR.

![Metadata kontejneru](./media/containers-image-repository.png)
    
**Podrobnosti o úložišti** imagí v předchozím snímku obrazovky obsahují následující pole.  Požadovaná pole jsou indicted hvězdičkou (*).

-   **ID Předplatného\*** – ID předplatného Azure, kde se nachází ACR.
-   **Název skupiny prostředků\*** – název skupiny prostředků ACR.
-   **Název registru\*** – název ACR.
-   **Název úložiště\*** – název úložiště. Po nastavení tohoto názvu se tato hodnota nedá změnit. Použijte jedinečný název, abyste se vyhnuli konfliktu s jinými nabídkami ve vašem účtu.
-   **Username\*** – uživatelské jméno (uživatelské jméno správce) přidružené k imagi ACR.
-   **Heslo\*** – heslo přidružené k imagi ACR.

    >[!NOTE]
    >Uživatelské jméno a heslo jsou potřebné k tomu, aby měli partneři přístup k ACR, který se zmiňuje v procesu publikování.


### <a name="image-version"></a>Verze image

Při publikování image kontejneru můžete zadat jednu nebo více značek obrázku a SHA výtahy.

**\* značek obrázku nebo hodnoty Digest**
 
- Tato značka nebo algoritmus Digest musí zahrnovat značku `latest` a značku verze (například počínaje `xx.xx.xx-`, kde XX je číslo). Měly by být v [manifestu značky](https://github.com/estesp/manifest-tool) , aby bylo možné cílit na více platforem. Všechny značky, na které se odkazuje značka manifestu, musí být také přidané, aby je bylo možné nahrát. 
- Pomocí značek můžete přidat několik verzí kontejneru. Všechny značky manifestu (s výjimkou `latest`) musí začínat buď `X.Y-`, nebo `X.Y.Z-`, kde X, Y, Z jsou celá čísla. <br/> Pokud například značka `latest` odkazuje na `1.0.1-linux-x64`, `1.0.1-linux-arm32`a `1.0.1-windows-arm32`, je třeba přidat tyto značky sem.

>[!NOTE]
>Nezapomeňte přidat **značku testu** do obrázku, abyste mohli identifikovat obrázek během testování.


## <a name="next-steps"></a>Další kroky

Na [kartě Marketplace](./cpp-marketplace-tab.md) můžete vytvořit popis Marketplace pro vaši nabídku. 
