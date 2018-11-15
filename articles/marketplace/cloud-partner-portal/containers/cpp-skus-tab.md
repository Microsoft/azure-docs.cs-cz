---
title: SKU pro image kontejnerů Azure | Dokumentace Microsoftu
description: Skladové položky konfigurace pro Azure container.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 81f7e46e626bb061881be53e8cace36e1478e0e1
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51683974"
---
# <a name="container-skus-tab"></a>Karta skladové položky kontejneru

**SKU** karty **nová nabídka** stránka umožňuje vytvořit jeden nebo více skladové položky a přidružovat je k nové nabídky.  Různé skladové položky můžete použít k rozlišení řešení sady funkcí, modely fakturace nebo jiné vlastnosti.

## <a name="sku-settings"></a>Nastavení SKU

Když začnete vytvářet novou nabídku, nejsou k dispozici žádné skladové položky související s nabídkou. Pokud chcete vytvořit novou skladovou Položku, postupujte podle těchto kroků:

1. Na kartě skladové položky vyberte **novou skladovou Položku**

   ![Nové skladové položky řádku](./media/containers-sku-settings.png)

2. Zadejte požadované informace SKU a kontejner. Každý SKU odpovídá image kontejneru. Existují dvě části do skladové položky:

    -   Skladová položka metadat
    -   Metadata kontejneru

### <a name="sku-metadata"></a>Skladová položka metadat

Skladová položka metadat obsahuje prezentace zobrazovaných informací pro výpis kontejneru.

![Skladová položka metadat](./media/containers-sku-details.png)

### <a name="container-metadata"></a>Metadata kontejneru

Metadata kontejneru obsahují referenční informace o úložišti podrobnosti vaší image v Azure Container Registry (ACR). Azure Marketplace zkopíruje tuto image do registru specifické pro Marketplace, veřejná a potom zpřístupní bitovou kopii pro zákazníky po certifikaci. Všechny žádosti od uživatele Azure využívat webu Azure Marketplace image kontejneru se obsluhují z veřejného registru na webu Marketplace, ne služby ACR.

![Metadata kontejneru](./media/containers-image-repository.png)
    
**Podrobnosti úložiště Image** na předchozí obrazovce zachycený záznam obsahuje následující pole:

-   **ID předplatného** – ID předplatného Azure, kde je k dispozici služby ACR.
-   **Název skupiny prostředků** – název skupiny prostředků služby ACR.
-   **Název registru** – název The ACR.
-   **Název úložiště** – název úložiště. Jakmile je tento název, tato hodnota nedá změnit. Aby nedošlo ke konfliktu s jiné nabídky ve vašem účtu použijte jedinečný název.
-   **Uživatelské jméno** -username (uživatelské jméno správce) přidružené k imagi služby ACR.
-   **Heslo** – heslo přidružené k imagi služby ACR.

    >[!NOTE]
    >Uživatelské jméno a heslo jsou nezbytné k zajištění, že partneři mají přístup do služby ACR uvedených v procesu publikování.

### <a name="image-version"></a>Verze image

Při publikování image kontejneru, můžete zadat jednu nebo více značek image a hodnoty SHA Digest.

**Značka obrázku nebo ověřování algoritmem Digest**
 
- Musí obsahovat tento značek nebo přehledů `latest` značku a označení verze (například počínaje `xx.xx.xx-` kde xx je číslo). Měly by být [manifest značky](https://github.com/estesp/manifest-tool) pro více cílových platforem. Všechny značky odkazuje značku manifestu musí být rovněž přidána, takže jsme jejich nahrávání. 
- Můžete přidat několik verzí kontejnerů pomocí značek. Všechny značky manifest (s výjimkou `latest`) musí začínat buď `X.Y-` nebo `X.Y.Z-` kde X, Y, Z jsou celá čísla. <br/> Například pokud `latest` označení odkazuje na `1.0.1-linux-x64`, `1.0.1-linux-arm32`, a `1.0.1-windows-arm32`, tyto značky je potřeba přidat tady.

>[!NOTE]
>Nezapomeňte přidat **testovací značka** do bitové kopie, abyste mohli identifikovat bitovou kopii při testování.

## <a name="next-steps"></a>Další postup

Použití [Marketplace kartu](./cpp-marketplace-tab.md) vytvořit popis nabídky marketplace. 