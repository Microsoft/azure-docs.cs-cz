---
title: 'Kurz: povolení protokolu SSL vlastní domény & pro statický web v Azure'
description: Zjistěte, jak nakonfigurovat vlastní doménu hostoval statický Web.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 893ac53dc9f0b6b162c5ec22e478cd15706e50fb
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327501"
---
# <a name="tutorial-enable-custom-domain--ssl-for-a-static-website-in-azure"></a>Kurz: povolení protokolu SSL vlastní domény & pro statický web v Azure

Tento kurz je druhá část série. V něm zjistíte, jak povolit koncový bod služby vlastní domény s protokolem SSL pro váš statický Web. 

V tomto kurzu se dozvíte, jak pomocí [Azure CDN](../../cdn/cdn-overview.md) nakonfigurovat vlastní koncový bod domény pro váš statický Web. S Azure CDN můžete zřídit vlastní certifikáty SSL, používat vlastní doménu a nakonfigurovat pravidla pro přepis vlastního všechny najednou. Konfigurace Azure CDN má za následek další poplatky, ale poskytuje konsistentní nízkou latencí na váš web z libovolného místa na světě. Azure CDN taky poskytuje šifrování pomocí protokolu SSL s vlastním certifikátem. Informace o cenách Azure CDN najdete v tématu [Azure CDN ceny](https://azure.microsoft.com/pricing/details/cdn/).

Ve druhé části této série se naučíte:

> [!div class="checklist"]
> * Vytvoření koncového bodu CDN na koncový bod statického webu
> * Povolit vlastní domény a SSL

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem, ukončete první část [kurzu: hostování statického webu na BLOB Storage](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Začněte tím, že se přihlásíte k [Azure Portal](https://portal.azure.com/) .

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>Vytvoření koncového bodu CDN na koncový bod statického webu

1. Vyhledejte účet úložiště v Azure Portal a zobrazte si přehled o účtu.
1. Pro konfiguraci Azure CDN vyberte v nabídce **Služba BLOB Service** možnost **Azure CDN** .
1. V části **profil CDN** zadejte nový nebo existující profil CDN. Další informace najdete v tématu [Rychlý start: Vytvoření profilu a koncového bodu Azure CDN](../../cdn/cdn-create-new-endpoint.md).
1. Zadejte cenovou úroveň pro koncový bod CDN. V tomto kurzu se používá standardní cenová úroveň **Akamai** , protože se rychle šíří, obvykle během několika minut. Další cenové úrovně mohou trvat delší dobu, ale mohou nabízet i další výhody. Další informace najdete v tématu [porovnání Azure CDNch funkcí produktu](../../cdn/cdn-features.md).
1. Do pole **název koncového bodu CDN** zadejte název koncového bodu CDN. Koncový bod CDN musí být v rámci Azure jedinečný.
1. Určete, že jste koncový bod statického webu v poli **počáteční název hostitele** . Pokud chcete najít koncový bod statického webu, přejděte k nastavení **statického webu** pro váš účet úložiště. Zkopírujte primární koncový bod a vložte ho do konfigurace CDN odebráním identifikátoru protokolu (*např.* https).

    Následující obrázek ukazuje příklad konfigurace koncového bodu:

    ![Snímek obrazovky s ukázkou konfigurace koncového bodu CDN](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Vytvořte koncový bod CDN a počkejte, než se rozšíří.
1. Pokud chcete ověřit, že je koncový bod CDN správně nakonfigurovaný, přejděte kliknutím na koncový bod na jeho nastavení. V přehledu CDN pro váš účet úložiště najděte název hostitele koncového bodu a přejděte ke koncovému bodu, jak je znázorněno na následujícím obrázku. Formát koncového bodu CDN bude podobný `https://staticwebsitesamples.azureedge.net`.

    ![Snímek obrazovky znázorňující přehled koncového bodu CDN](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    Po dokončení šíření koncového bodu CDN zobrazuje koncový bod CDN obsah souboru index. html, který jste předtím nahráli na váš statický Web.

1. Pokud chcete zkontrolovat nastavení zdroje pro koncový bod CDN, přejděte na **počátek** v části **Nastavení** pro koncový bod CDN. Uvidíte, že pole **Typ původu** je nastavené na *vlastní zdroj* a že se v poli **původní název hostitele** zobrazuje váš koncový bod statického webu.

    ![Snímek obrazovky znázorňující nastavení počátku pro koncový bod CDN](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Povolit vlastní domény a SSL

1. Vytvoření záznamu CNAME u svého poskytovatele název domény pro přesměrování vaši vlastní doménu do koncového bodu CDN. Záznam CNAME pro subdoménu *www* by měl vypadat přibližně takto:

    ![Zadejte záznam CNAME pro subdoménu www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. V Azure Portal zobrazte nastavení pro koncový bod CDN. V části **Nastavení** přejděte na **vlastní** domény a nakonfigurujte vlastní doménu a certifikát SSL.
1. Vyberte **Přidat vlastní doménu** a zadejte název domény a pak klikněte na **Přidat**.
1. Vyberte nové mapování vlastní domény a zřiďte certifikát SSL.
1. Nastavte možnost **https pro vlastní doménu** na **zapnuto**a pak klikněte na **Uložit**. Konfigurace vlastní domény může trvat několik hodin. Na portálu se zobrazí postup, jak je znázorněno na následujícím obrázku.

    ![Snímek obrazovky znázorňující průběh vlastní konfigurace domény](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Otestujte mapování svého statického webu do vlastní domény přístupem k adrese URL pro vaši vlastní doménu.

Další informace o povolení protokolu HTTPS pro vlastní domény najdete v tématu [kurz: konfigurace https u Azure CDN vlastní domény](../../cdn/cdn-custom-ssl.md).

## <a name="next-steps"></a>Další kroky

Ve druhé části tohoto kurzu jste zjistili, jak nakonfigurovat vlastní domény s protokolem SSL v Azure CDN pro váš statický Web.

Další informace o konfiguraci a použití Azure CDN najdete v tématu [co je Azure CDN?](../../cdn/cdn-overview.md).