---
title: SKU pro image kontejnerů Azure | Azure Marketplace
description: Nakonfigurujte skum pro kontejner Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: 9c5cf218632c720fd042cc5f5d4ed95d5096b5b5
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270192"
---
# <a name="container-skus-tab"></a>Karta SKU kontejneru

> [!IMPORTANT]
> dubna 2020 začneme přesouvat správu nabídek Azure Container do Centra partnerů. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Postupujte podle pokynů v [části Vytvoření nabídky kontejneru Azure](https://aka.ms/CreateContainerOffer) ke správě migrovaných nabídek.

Karta **SKU** na stránce **Nová nabídka** umožňuje vytvořit jednu nebo více slok a přidružit je k nové nabídce.  Pomocí různých skum můžete rozlišit řešení podle sad funkcí, fakturačních modelů nebo jiných charakteristik.

## <a name="sku-settings"></a>Nastavení skladové položky

Když začnete vytvářet novou nabídku, nejsou k nabídce přidruženy žádné souspolečnosti. Chcete-li vytvořit novou skladovou položku, postupujte takto:

1. Na kartě Skladové položky vyberte **Nová skladová položka.**

   ![Výzva k nové skladové položkě](./media/containers-sku-settings.png)

2. Zadejte požadované skladové položky a informace o kontejneru. Každá skladová položka odpovídá image kontejneru. Skladová položka má dvě části:

    -   Metadata skladové položky
    -   Metadata kontejneru


### <a name="sku-metadata"></a>Metadata skladové položky

Metadata skladové položky obsahují informace o zobrazení úložiště pro výpis kontejneru.

![Metadata skladové položky](./media/containers-sku-details.png)


### <a name="container-metadata"></a>Metadata kontejneru

Metadata kontejneru obsahuje referenční informace o podrobnostech úložiště bitových obrázků v registru kontejnerů Azure (ACR). Azure Marketplace zkopíruje tuto bitovou kopii do veřejného registru specifického pro Marketplace a po certifikaci ji zpřístupní zákazníkům. Všechny požadavky od uživatele Azure využívat image kontejneru Azure Marketplace se zotírají z veřejného registru Marketplace, nikoli ACR.

![Metadata kontejneru](./media/containers-image-repository.png)
    
**Podrobnosti úložiště obrázků** v předchozím zachycení obrazovky obsahují následující pole.  Povinná pole jsou obžalována hvězdičkou (*).

-   **ID\* předplatného** – ID předplatného Azure, kde je k dispozici ACR.
-   **Název\* skupiny prostředků** - Název skupiny prostředků ACR.
-   **Název\* registru** - Název ACR.
-   **Název\* úložiště** - Název úložiště. Po nastavení tohoto názvu nelze tuto hodnotu změnit. Použijte jedinečný název, abyste zabránili konfliktu s jinými nabídkami ve vašem účtu.
-   **Uživatelské\* jméno** - Uživatelské jméno (uživatelské jméno správce) přidružené k obrázku ACR.
-   **Heslo\* ** - Heslo přidružené k obrázku ACR.

    >[!NOTE]
    >Uživatelské jméno a heslo jsou nutné k zajištění, že partneři mají přístup k ACR uvedené v procesu publikování.


### <a name="image-version"></a>Verze image

Při publikování image kontejneru můžete zadat jednu nebo více značek bitové kopie a výtahy SHA.

**Značka\* obrázku nebo Digest**
 
- Tato značka nebo digest `latest` musí obsahovat značku a značku `xx.xx.xx-` verze (například počínaje místem, kde xx je číslo). Měly by být [značky manifestu,](https://github.com/estesp/manifest-tool) které cílí na více platforem. Všechny značky odkazované značkou manifestu musí být také přidány, abychom je mohli nahrát. 
- Pomocí značek můžete přidat několik verzí kontejneru. Všechny značky `latest`manifestu (kromě) `X.Y.Z-` musí začínat buď buď `X.Y-` s celými písmeny X, Y, Z. <br/> Pokud například `latest` značka odkazuje `1.0.1-linux-x64` `1.0.1-linux-arm32`na `1.0.1-windows-arm32`, a , je třeba tyto značky přidat sem.

>[!NOTE]
>Nezapomeňte do obrázku přidat **testovací značku,** abyste mohli obrázek během testování identifikovat.


## <a name="next-steps"></a>Další kroky

Na [kartě Marketplace](./cpp-marketplace-tab.md) můžete vytvořit popis marketplace pro vaši nabídku. 
