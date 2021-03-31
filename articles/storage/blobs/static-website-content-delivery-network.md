---
title: Integrace statického webu s Azure CDN-Azure Storage
description: Přečtěte si, jak ukládat do mezipaměti obsah statického webu z Azure Storage účtu pomocí Azure Content Delivery Network (CDN).
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.author: normesta
ms.date: 04/07/2020
ms.openlocfilehash: e458f98d82c910ec845ebf951b7f6470b6aba10d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95527322"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Integrace statického webu s Azure CDN

Můžete povolit [službě azure Content Delivery Network (CDN)](../../cdn/cdn-overview.md) ukládat obsah do mezipaměti ze [statického webu](storage-blob-static-website.md) , který je hostovaný v účtu úložiště Azure. Pomocí Azure CDN můžete nakonfigurovat vlastní koncový bod domény pro váš statický web, zřídit vlastní certifikáty TLS/SSL a nakonfigurovat vlastní pravidla přepsání. Při konfiguraci Azure CDN se účtují další poplatky, ale v celém světě budou mít k webu konzistentní nízké latence. Azure CDN taky poskytuje šifrování TLS s vlastním certifikátem. 

Informace o cenách Azure CDN najdete v tématu [Azure CDN ceny](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="enable-azure-cdn-for-your-static-website"></a>Povolení Azure CDN pro váš statický Web

Azure CDN pro váš statický Web můžete povolit přímo z účtu úložiště. Pokud chcete pro koncový bod CDN specifikovat pokročilá nastavení konfigurace, jako je například [optimalizaci stahování velkých souborů](../../cdn/cdn-optimization-overview.md#large-file-download), můžete místo toho použít [rozšíření Azure CDN](../../cdn/cdn-create-new-endpoint.md) a vytvořit profil CDN a koncový bod.

1. Vyhledejte účet úložiště v Azure Portal a zobrazte si přehled o účtu.

1. V nabídce **Služba objektů BLOB** vyberte **Azure CDN** a otevřete stránku **Azure CDN** :

    ![Vytvoření koncového bodu CDN](media/storage-blob-static-website-custom-domain/cdn-storage-new.png)

1. V části **profil CDN** určete, jestli se má vytvořit nový profil CDN, nebo použijte existující. Profil CDN je kolekce koncových bodů CDN, které sdílejí cenovou úroveň a poskytovatele. Pak zadejte název pro CDN, který je jedinečný v rámci vašeho předplatného.

1. Zadejte cenovou úroveň pro koncový bod CDN. Další informace o cenách najdete v tématu [Content Delivery Network ceny](https://azure.microsoft.com/pricing/details/cdn/). Další informace o funkcích, které jsou k dispozici na jednotlivých úrovních, najdete v tématu [porovnání Azure CDNch funkcí produktu](../../cdn/cdn-features.md).

1. Do pole **název koncového bodu CDN** zadejte název koncového bodu CDN. Koncový bod CDN musí být v rámci Azure jedinečný a poskytuje první část adresy URL koncového bodu. Formulář ověří, jestli je název koncového bodu jedinečný.

1. V poli **počáteční název hostitele** zadejte svůj statický koncový bod webu. 

   Pokud chcete najít koncový bod statického webu, přejděte k nastavení **statického webu** pro váš účet úložiště.  Zkopírujte primární koncový bod a vložte ho do konfigurace CDN.

   > [!IMPORTANT]
   > Nezapomeňte odebrat identifikátor *protokolu (například* https) a koncové lomítko v adrese URL. Pokud je například koncový bod statického webu `https://mystorageaccount.z5.web.core.windows.net/` , zadejte `mystorageaccount.z5.web.core.windows.net` v poli **počáteční název hostitele** .

   Následující obrázek ukazuje příklad konfigurace koncového bodu:

   ![Snímek obrazovky s ukázkou konfigurace koncového bodu CDN](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Vyberte **vytvořit** a potom počkejte na zřízení CDN. Koncový bod se po vytvoření zobrazí v seznamu koncových bodů. (Pokud máte ve formuláři nějaké chyby, zobrazí se vedle tohoto pole Znak vykřičníku.)

1. Pokud chcete ověřit, že je koncový bod CDN správně nakonfigurovaný, přejděte kliknutím na koncový bod na jeho nastavení. V přehledu CDN pro váš účet úložiště najděte název hostitele koncového bodu a přejděte ke koncovému bodu, jak je znázorněno na následujícím obrázku. Formát koncového bodu CDN bude podobný `https://staticwebsitesamples.azureedge.net` .

    ![Snímek obrazovky znázorňující přehled koncového bodu CDN](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

1. Po zřízení koncového bodu CDN se navigace do koncového bodu CDN zobrazuje s obsahem index.html, který jste dříve nahráli na váš statický Web.

1. Pokud chcete zkontrolovat nastavení zdroje pro koncový bod CDN, přejděte na **počátek** v části **Nastavení** pro koncový bod CDN. Uvidíte, že pole **Typ původu** je nastavené na *vlastní zdroj* a že se v poli **původní název hostitele** zobrazuje váš koncový bod statického webu.

    ![Snímek obrazovky znázorňující nastavení počátku pro koncový bod CDN](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Odebrání obsahu z Azure CDN

Pokud už nechcete objekt v Azure CDN ukládat do mezipaměti, můžete použít některý z následujících kroků:

* Nastavte kontejner jako privátní, nikoli veřejný. Další informace najdete v tématu [Správa anonymního přístupu pro čtení do kontejnerů a objektů BLOB](./anonymous-read-access-configure.md).
* Zakažte nebo odstraňte koncový bod CDN pomocí webu Azure Portal.
* Upravte hostovanou službu tak, aby už nereagovala na žádosti tohoto objektu.

Objekt, který už je v mezipaměti v Azure CDN uložený, tam zůstane uložený, dokud neuplyne období TTL (Time-To-Live) objektu nebo dokud se koncový bod [nevyprázdní](../../cdn/cdn-purge-endpoint.md). Když uplyne období TTL, Azure CDN určí, jestli je koncový bod stále platný a jestli je objekt stále anonymně přístupný. Pokud tomu tak není, objekt se už v mezipaměti ukládat nebude.

## <a name="next-steps"></a>Další kroky

Volitelné Přidejte do svého koncového bodu Azure CDN vlastní doménu. Viz [kurz: Přidání vlastní domény do koncového bodu Azure CDN](../../cdn/cdn-map-content-to-custom-domain.md).