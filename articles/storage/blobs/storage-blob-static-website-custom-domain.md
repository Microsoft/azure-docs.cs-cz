---
title: 'Kurz: Povolení vlastní doménu s protokolem SSL na statický web využívající Azure CDN – Azure Storage'
description: Zjistěte, jak nakonfigurovat vlastní doménu hostoval statický Web.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: tamram
ms.custom: seodec18
ms.openlocfilehash: 6ccd33805fe4b62d3456121321edc4eec3bff2e5
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109445"
---
# <a name="tutorial-use-azure-cdn-to-enable-a-custom-domain-with-ssl-for-a-static-website"></a>Kurz: Použití Azure CDN umožňuje vlastní doménu s protokolem SSL pro statický Web

Tento kurz je druhá část série. V něm zjistíte, jak povolit koncový bod služby vlastní domény s protokolem SSL pro váš statický Web. 

Tento kurz ukazuje, jak používat [Azure CDN](../../cdn/cdn-overview.md) nakonfigurovat vlastní doménu koncového bodu pro váš statický Web. S Azure CDN můžete zřídit vlastní certifikáty SSL, používat vlastní doménu a nakonfigurovat pravidla pro přepis vlastního všechny najednou. Konfigurace Azure CDN má za následek další poplatky, ale poskytuje konsistentní nízkou latencí na váš web z libovolného místa na světě. Azure CDN taky poskytuje šifrování pomocí protokolu SSL s vlastním certifikátem. Informace o cenách Azure CDN najdete v tématu [ceny Azure CDN](https://azure.microsoft.com/pricing/details/cdn/).

Ve druhé části této série se naučíte:

> [!div class="checklist"]
> * Vytvoření koncového bodu CDN na koncový bod statického webu
> * Povolit vlastní domény a SSL

## <a name="prerequisites"></a>Požadavky

Než začnete tento kurz, proveďte první částí, [kurz: hostování statického webu ve službě Blob Storage](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [webu Azure portal](https://portal.azure.com/) začít.

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>Vytvoření koncového bodu CDN na koncový bod statického webu

1. Otevřít [webu Azure portal](https://portal.azure.com/) ve webovém prohlížeči. 
1. Vyhledejte svůj účet úložiště a zobrazí přehled účtu.
1. Vyberte **Azure CDN** pod **služby Blob Service** nabídky ke konfiguraci Azure CDN.
1. V **nový koncový bod** části, vyplňte pole k vytvoření nového koncového bodu CDN.
1. Zadejte název koncového bodu, jako například *mystaticwebsiteCDN*.
1. Zadejte doménu webu jako název hostitele pro koncový bod CDN.
1. Název počátečního hostitele, zadejte jste koncový bod statického webu. Pokud chcete najít váš koncový bod statického webu, přejděte na **statického webu** části vašeho účtu úložiště a kopírování koncového bodu. 
1. Testování tak, že přejdete na koncový bod CDN *mywebsitecdn.azureedge.net* v prohlížeči.

## <a name="enable-custom-domain-and-ssl"></a>Povolit vlastní domény a SSL

1. Vytvoření záznamu CNAME u svého poskytovatele název domény pro přesměrování vaši vlastní doménu do koncového bodu CDN. Záznam CNAME pro *www* subdoménu by měl vypadat přibližně takto:

    ![Zadejte záznam CNAME pro subdoménu www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. Na webu Azure Portal klikněte na nově vytvořený koncový bod ke konfiguraci vlastní domény a certifikátem SSL.
1. Vyberte **přidat vlastní doménu** a zadejte název domény a pak klikněte na **přidat**.
1. Vyberte mapování nově vytvořené vlastní domény ke zřízení certifikátu SSL.
1. Nastavte **HTTPS pro vlastní doménu** k **ON**. Vyberte **spravovat CDN** má Azure CDN spravovat svůj certifikát SSL. Klikněte na **Uložit**.
1. Otestujte svůj web tak přístup k adresu url svého webu.

## <a name="next-steps"></a>Další postup

Ve druhé části tohoto kurzu jste zjistili, jak nakonfigurovat vlastní domény s protokolem SSL v Azure CDN pro váš statický Web.

Přejděte na tento odkaz Další informace o hostování statického webu ve službě Azure Storage.

> [!div class="nextstepaction"]
> [Další informace o statických webů](storage-blob-static-website.md)
