---
title: 'Kurz: Povolit vlastní doménu s protokolem SSL na statický web využívající Azure CDN – Azure Storage'
description: Zjistěte, jak nakonfigurovat vlastní doménu hostoval statický Web.
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: normesta
ms.reviewer: seguler
ms.custom: seodec18
ms.openlocfilehash: 2b0bb94be2ba8ea983cda8fd015d05fcd532f2bc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226118"
---
# <a name="tutorial-use-azure-cdn-to-enable-a-custom-domain-with-ssl-for-a-static-website"></a>Kurz: Použití Azure CDN umožňuje vlastní doménu s protokolem SSL pro statický Web

Tento kurz je druhá část série. V něm zjistíte, jak povolit koncový bod služby vlastní domény s protokolem SSL pro váš statický Web. 

Tento kurz ukazuje, jak používat [Azure CDN](../../cdn/cdn-overview.md) nakonfigurovat vlastní doménu koncového bodu pro váš statický Web. S Azure CDN můžete zřídit vlastní certifikáty SSL, používat vlastní doménu a nakonfigurovat pravidla pro přepis vlastního všechny najednou. Konfigurace Azure CDN má za následek další poplatky, ale poskytuje konsistentní nízkou latencí na váš web z libovolného místa na světě. Azure CDN taky poskytuje šifrování pomocí protokolu SSL s vlastním certifikátem. Informace o cenách Azure CDN najdete v tématu [ceny Azure CDN](https://azure.microsoft.com/pricing/details/cdn/).

Ve druhé části této série se naučíte:

> [!div class="checklist"]
> * Vytvoření koncového bodu CDN na koncový bod statického webu
> * Povolit vlastní domény a SSL

## <a name="prerequisites"></a>Požadavky

Než začnete tento kurz, proveďte první částí, [kurzu: Hostování statického webu ve službě Blob Storage](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [webu Azure portal](https://portal.azure.com/) začít.

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>Vytvoření koncového bodu CDN na koncový bod statického webu

1. Vyhledejte svůj účet úložiště na webu Azure Portal a zobrazte přehled účtu.
1. Vyberte **Azure CDN** pod **služby Blob Service** nabídky ke konfiguraci Azure CDN.
1. V **profil CDN** části, zadejte nové nebo existující profil CDN. Další informace najdete v tématu [rychlý start: Vytvoření koncového bodu a profilu Azure CDN](../../cdn/cdn-create-new-endpoint.md).
1. Určete cenovou úroveň pro koncový bod CDN. Tento kurz používá **Standard Akamai** cenovou úroveň, protože ji postoupí rychle, obvykle během několika minut. Jiné cenové úrovně může trvat delší dobu rozšířit, ale může navíc nabízí další výhody. Další informace najdete v tématu [funkce produktu Azure CDN porovnání](../../cdn/cdn-features.md).
1. V **název koncového bodu CDN** pole, zadejte název koncového bodu CDN. Koncový bod CDN musí být jedinečný v Azure.
1. Zadejte koncový bod statického webu v jste **název počátečního hostitele** pole. Pokud chcete najít váš koncový bod statického webu, přejděte na **statického webu** nastavení vašeho účtu úložiště. Primární koncový bod zkopírujte a vložte ho do konfigurace CDN, odebrání identifikátor protokolu (*třeba*, HTTPS).

    Následující obrázek ukazuje příklad konfigurace koncového bodu:

    ![Snímek obrazovky zobrazující ukázkový CDN konfigurace koncového bodu](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Vytvoření koncového bodu CDN a počkejte na dokončení propagace.
1. Pokud chcete ověřit, jestli je správně nakonfigurovaný koncový bod CDN, klikněte na koncový bod pro navigaci na jeho nastavení. Přehled CDN pro váš účet úložiště vyhledejte název hostitele koncového bodu a přejděte ke koncovému bodu, jak je znázorněno na následujícím obrázku. Formát vašeho koncového bodu CDN bude vypadat podobně jako `https://staticwebsitesamples.azureedge.net`.

    ![Snímek obrazovky zobrazující přehled koncového bodu CDN](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    Po dokončení rozšíření koncového bodu CDN přejdete na koncový bod CDN zobrazí obsah, který byl dříve odeslán do statického webu souboru index.html.

1. Zkontrolujte původní nastavení pro koncový bod CDN, přejděte na **původu** pod **nastavení** oddíl pro koncový bod CDN. Uvidíte, že **typ počátku** je nastaveno na *vlastní zdroj* a že **název počátečního hostitele** pole se zobrazí váš koncový bod statického webu.

    ![Snímek obrazovky zobrazující nastavení počátku pro koncový bod CDN](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Povolit vlastní domény a SSL

1. Vytvoření záznamu CNAME u svého poskytovatele název domény pro přesměrování vaši vlastní doménu do koncového bodu CDN. Záznam CNAME pro *www* subdoménu by měl vypadat přibližně takto:

    ![Zadejte záznam CNAME pro subdoménu www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. Na webu Azure Portal zobrazte nastavení pro koncový bod CDN. Přejděte do **vlastní domény** pod **nastavení** konfigurace vlastní domény a certifikátem SSL.
1. Vyberte **přidat vlastní doménu** a zadejte název domény a pak klikněte na **přidat**.
1. Vyberte nové mapování vlastní domény ke zřízení certifikátu SSL.
1. Nastavte **HTTPS vlastní domény** k **ON**, pak klikněte na tlačítko **Uložit**. Může trvat několik hodin konfigurace vlastní domény. Na portálu se zobrazí průběh, jak je znázorněno na následujícím obrázku.

    ![Snímek obrazovky zobrazující průběh konfigurace vlastní domény](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Přístup k adrese URL pro vlastní doménu otestujte mapování váš statický web k vaší vlastní doméně.

Další informace o povolení HTTPS pro vlastní domény, najdete v části [kurzu: Konfigurace HTTPS pro vlastní doménu Azure CDN](../../cdn/cdn-custom-ssl.md).

## <a name="next-steps"></a>Další postup

Ve druhé části tohoto kurzu jste zjistili, jak nakonfigurovat vlastní domény s protokolem SSL v Azure CDN pro váš statický Web.

Další informace o konfiguraci a používání Azure CDN najdete v tématu [co je Azure CDN?](../../cdn/cdn-overview.md).