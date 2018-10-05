---
title: Vytvoření nabídky modul IoT Edge | Dokumentace Microsoftu
description: Jak publikovat nový modul IoT Edge pro web Marketplace.
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
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: d3cc1a09963c5f7fee613af24c63fd15b1cfffee
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809201"
---
# <a name="how-to-publish-a-new-iot-edge-module-in-the-cloud-partner-portal"></a>Jak publikovat nový modul IoT Edge v portál Cloud Partner

Tento článek popisuje kroky pro publikování nové nabídky modul IoT Edge.

## <a name="prerequisites"></a>Požadavky

Následující požadavky platí pro publikování na webu Azure Marketplace modul IoT Edge.

-   Přístup k [cloudu portál pro partnery (CPP)](https://cloudpartner.azure.com/#alloffers). Další informace najdete v tématu [Průvodce publikováním](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

-   Máte modul IoT Edge hostované ve službě Azure Container Registry.

-   Máte připravený (včetně – úplný seznam) metadata modulu IoT Edge:

    -   Titul

    -   Popis (základní formát HTML)

    -   Logo ve formátu png obrázku a v následující formáty: 40 pixelů x 40 pixelů, 90 pixelů x 90 pixelů, 115 pixelů x 115 pixelů, 255 pixelů x 115 pixelů.

    -   Podmínky použití a ochrana osobních údajů zásad

<a name="prepare-your-iot-edge-module-listing-in-cpp"></a>Připravte svůj výpis modul IoT Edge v CPP
-------------------------------------------

### <a name="create-a-new-offer-of-the-type-iot-edge-module"></a>Vytvořit novou nabídku typu modul IoT Edge 

Postupujte podle následujících kroků připravíte svůj výpis modul IoT Edge:

-   Přihlášení k vaší [CPP účet](https://cloudpartner.azure.com/).

>[!Note]
>Obecné informace o na portál Cloud Partner, můžete zkontrolovat [další dokumentace](https://cloudpartner.azure.com/#learn)

-   Vyberte **nová nabídka**a pak vyberte **modul IoT Edge**.

>[!NOTE]
>Modul IoT Edge je kontejner, který je speciálně k ke spuštění na hraničních zařízeních IoT. Scénáře řešený modul IoT Edge musí dávat smysl v kontextu IoT Edge. Zahrnuje také výchozí nastavení konfigurace pro nasazení do IoT Edge zařízení přímočaré. Kontejner může také zahrnovat SDK modul IoT Edge k umožnění komunikace s edgeHub a centrem IoT.

### <a name="define-your-offer-settings"></a>Definujte nastavení nabídky

Na kartě nabízejí nastavení zadejte informace pro vaši nabídku.

![Nabídka Identity](./media/cloud-partner-portal-create-iot-edge-module-offer/offer-identity.png)


-   **ID nabídky** jednoznačně identifikuje vaši nabídku v CPP a může být použití v adresách URL určených pro zákazníky.

-   **Název** je viditelná jenom vy pro odkazování na tuto nabídku v CPP.

### <a name="create-one-or-more-skus"></a>Vytvořte jeden nebo více skladové položky

Každý SKU odpovídá image kontejneru. Musí mít alespoň jedna skladová položka a můžete přidat více než jeden. Existují dvě části do skladové položky:

-   Skladová položka metadat

-   Metadata kontejneru

**Chcete-li vytvořit SKU:**

Vyberte **SKU** kartu a potom vyberte **novou skladovou Položku**.

![Nové SKU](./media/cloud-partner-portal-create-iot-edge-module-offer/SKUs.png)

Skladová položka metadat obsahuje následující pole, které jsou požadovány:
- ID SKU – jedinečný identifikátor.
- Titul – Název SKU, maximálně 50 znaků.
- Shrnutí – stručný popis, maximálně 100 znaků.
- Popis – dlouhý popis.
- Skrýt toto SKU – výchozí hodnota je **ne**.
   
![Podrobné údaje SKU](./media/cloud-partner-portal-create-iot-edge-module-offer/SKU-settings.png)

#### <a name="iot-edge-module-metadata-and-the-container-registry"></a>Metadata modulu IoT Edge a container registry

Modul IoT Edge metadata obsahují odkaz na informace o bitové kopii, která je uložená ve službě Azure container registry (ACR). Na webu Azure Marketplace zkopíruje image do registru veřejného marketplace a je dostupný pro zákazníky po certifikaci. Všechny žádosti uživatelů používat image modulu IoT Edge se obsluhují z registru kontejneru webu Marketplace.

![Image kontejneru](./media/cloud-partner-portal-create-iot-edge-module-offer/container-images.png)

**Image kontejneru**

Podrobnosti o Imagi úložiště má následující požadované pole:

-   **ID předplatného** id předplatného Azure, kde je k dispozici registru ACR.

-   **Název skupiny prostředků** – název skupiny prostředků z registru ACR.

-   **Název registru** – název registru The ACR.

-   **Název úložiště** – název úložiště. Po nastavení, tato hodnota není možné později změnit. Název by měl být jedinečný, tak, aby žádné jiné nabídky v rámci vašeho účtu má stejný název.

-   **Uživatelské jméno** – uživatelské jméno přidružené k ACR (uživatelské jméno správce).

-   **Heslo** – heslo přidružené k služby ACR.

    >[!Note]
    >Uživatelské jméno a heslo jsou nezbytné k zajištění, že partneři mají přístup do služby ACR je popsáno v procesu publikování.

Publikujete image s modulem IoT Edge, můžete zadat jednu nebo více značek bitové kopie. Nezapomeňte přidat značku 'nejnovější' (výchozí) do modulu modulu mohli snadno identifikovat během testování.

Můžete také zadat následující specifika modul IoT Edge:

-   **CreateOptions field** – výchozí CreateOptions field předat, takže tento modul IoT Edge může být spuštěna ihned.

-   **dvojče:** -twin výchozí k předání, který tento modul IoT Edge můžete okamžité spuštění, při použití sady SDK modulu IoT.

-   **postupy:** – výchozí trasy k předání tak, aby tento modul IoT Edge lze spustit ihned, když pomocí sady SDK modulu IoT.

### <a name="describe-your-iot-edge-module-for-your-customers"></a>Popište modul IoT Edge pro vaše zákazníky

Na kartě Marketplace přidáte vlastní obsah specifické pro marketing. Tyto informace jsou, co bude veřejně viditelný a jsou uvedeny na webu Azure Marketplace.

![Přehled modulu IoT Edge](./media/cloud-partner-portal-create-iot-edge-module-offer/overview.png)

-   **Název** – název modulu IoT Edge, který je veřejně přístupný.

-   **Souhrn** – souhrn modul IoT Edge, který je veřejně přístupný ve většině stránky, jako je procházení stránek.

-   **Dlouhé shrnutí** – souhrn modul IoT Edge, který je veřejně přístupných při modulu je funkční. 

-   **Popis** – popis modul IoT Edge, který je veřejně přístupných na stránce podrobností produktu. (Základní značky HTML můžete použít k popisu formátu.)

-   **Identifikátor Marketing** – jedinečný identifikátor, který se používá k vytvoření adresu URL svého produktu. Tato adresa URL je v následujícím formátu: *azuremarketplace.microsoft.com/enus/marketplace/apps/yourpublisherid.youriotedgemodulemarketingidentifier*.

-   **Ve verzi Preview ID předplatného** – uživatelé, kteří mají přístup k těmto předplatným bude moci zobrazit modul IoT Edge po certifikace a před platí za provozu.

-   **Užitečné odkazy** – můžete přidat až 10 odkazy, které se zobrazí na vaší stránce s podrobnostmi o produktu.

-   **Navrhované kategorie** -vybrat až o pěti kategorií. Že se zobrazí na vaší stránce s podrobnostmi o produktu. V současné době všechny moduly IoT Edge se zobrazí pod *Internet of Things \> modul IoT Edge* kategorií v procházení stránek.

-   **Loga** – nahrajte logo Image modulu IoT Edge ve formátu PNG. Použít následující formáty: a přesně v následující formáty: 40 pixelů x 40 pixelů, 90 pixelů x 90 pixelů, 115 pixelů x 115 pixelů, 255 pixelů x 115 pixelů.

-   **Snímky obrazovky** – snímky obrazovky se zobrazí na vaší stránce s podrobnostmi o produktu. Jsou vhodné k vizuálnímu zobrazení činnosti modulu IoT Edge a jak to funguje. Můžete zobrazit diagramy architektury nebo použít případu obrázky pro instanci.

-   **Videa** -videa se zobrazí na vaší stránce s podrobnostmi o produktu. Jsou vhodné k vizuálnímu zobrazení činnosti modulu IoT Edge a jak to funguje.

-   **Správa zájemců** – můžete použít systém shromažďovat všechny potenciální zákazníky, které ukazují zájem o produktu.

-   **Ochrana osobních údajů** -musí mít adresu URL odkazující na vaše zásady ochrany osobních údajů.

-   **Podmínky použití** -musí mít podmínky použití. Značky HTML můžete použít formátování na této stránce nebo přejděte na některou z jiných stránek.

### <a name="enter-your-support-contact-information"></a>Zadejte své kontaktní informace podpory

Na kartě podporu poskytují **technickým kontaktem** a **zákaznickou podporu** informace.

![Kontaktní údaje podpory](./media/cloud-partner-portal-create-iot-edge-module-offer/support-contact-info.png)


## <a name="certify-your-iot-edge-module"></a>Certifikovat modul IoT Edge

Po zadání všechny informace, které je nutné vybrat **publikovat** k odeslání k certifikaci modul IoT Edge. Uvidíte, že časovou osu, která ukazují uživatele kroků v procesu certifikace.

**Ověřovací modul**

Modul je ověřováno náš tým certifikace. Po modulu má certifikaci získáte soukromého odkazu pro modul testování. Pokud potřebujete provést změny po testování, úprava metadat vaší nabídky a znovu odeslat modulu je tým certifikace. 

## <a name="publish-your-iot-edge-module"></a>Publikování modul IoT Edge

Po dokončení testování a jste připraveni publikovat, vyberte **aktivace** publikovat modul IoT Edge.

>[!Important]
>Pokud chcete mít modul IoT Edge oznámené na konferenci Ignite událost, musí být modul veřejné 09/23/2018.
