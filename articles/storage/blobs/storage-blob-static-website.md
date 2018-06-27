---
title: Hostování statického webu ve službě Azure Storage (Preview) | Microsoft Docs
description: Azure Storage teď nabízí statická webová stránka hostování (Preview), poskytuje nákladově efektivní a škálovatelné řešení pro hostování moderních webových aplikací.
services: storage
author: MichaelHauss
manager: vamshik
ms.service: storage
ms.topic: article
ms.date: 06/26/18
ms.author: mihauss
ms.openlocfilehash: 7021a0499547818d702d14aecb9d8e451a820181
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025674"
---
# <a name="static-website-hosting-in-azure-storage-preview"></a>Hostování statického webu ve službě Azure Storage (Preview)
Azure Storage teď nabízí statická webová stránka hostování (Preview), umožňuje nasadit nákladově efektivní a škálovatelné moderních webových aplikací v Azure. Webové stránky na statická webová stránka obsahovat statický obsah a JavaScript nebo jiných kódu na straně klienta. Naopak dynamické weby s závisí na kódu na straně serveru a může být hostovaný pomocí [Azure Web Apps](/app-service/app-service-web-overview.md).

Jak nasazení posunutí směrem k elastické a nákladově efektivní modely, je velmi důležité možnosti vytvořit webového obsahu bez nutnosti pro správu serveru. Zavedení hostování statického webu ve službě Azure Storage to umožňuje, povolení možností bohaté back-end s využitím bez serveru architektury [Azure Functions](/azure-functions/functions-overview.md) a dalším službám PaaS.

## <a name="how-does-it-work"></a>Jak to funguje?
Když povolíte statických webů na vašem účtu úložiště, vytvoří se nový koncový bod webové služby formuláře `<account-name>.<zone-name>.web.core.windows.net`.

Koncový bod webové služby vždy umožňuje anonymní přístup pro čtení, vrátí formátovaný stránky HTML v odpovědi na chyby služby a umožňuje pouze objekt operace čtení. Koncový bod webové služby vrátí dokumentu indexu v požadovaném adresáři pro kořenové i všechny podadresáře. Při službu úložiště vrátí chybu 404, vrátí koncový bod webové dokumentu vlastní chyby, pokud jste ji nakonfigurovali.

Obsah pro statického webu je hostován ve speciální kontejner s názvem "$web". Jako součást procesu povolování se vytvoří "$web" pro vás, pokud ještě neexistuje. Obsah v "$web" je přístupná v kořenovém adresáři účtu pomocí koncový bod webové. Například `https://contoso.z4.web.core.windows.net/` vrátí index dokument, jste nakonfigurovali pro svůj web, pokud dokument s tímto názvem v kořenovém adresáři $web existuje.

Při nahrávání obsahu na váš web, použijte koncový bod úložiště objektů blob. Nahrát objekt blob s názvem 'image.jpg', která je přístupná v kořenovém adresáři účet použijte následující adresu URL `https://contoso.blob.core.windows.net/$web/image.jpg`. Nahraný obrázek lze zobrazit v prohlížeči na odpovídající koncový bod webové `https://contoso.z4.web.core.windows.net/image.jpg`.


## <a name="custom-domain-names"></a>Vlastní názvy domén
Vlastní doménu můžete použít k hostování webovému obsahu. Postupujte podle pokynů v [konfigurace vlastního názvu domény pro váš účet úložiště Azure](storage-custom-domain-name.md). Pro přístup k webu přes protokol HTTPS hostovanou na vlastní název domény, najdete v části [používání Azure CDN pro přístup k objektům BLOB pomocí vlastních domén prostřednictvím protokolu HTTPS](storage-https-custom-domain-cdn.md).

## <a name="pricing-and-billing"></a>Ceny a fakturace
Hostování statická webová stránka je k dispozici bez dalších poplatků. Další informace o cenách pro Azure Blob Storage, podívejte se [Azure Blob Storage stránce s cenami](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="quickstart"></a>Rychlý start
### <a name="azure-portal"></a>Azure Portal
Pokud chcete spustit, který je hostitelem webové aplikace v Azure Storage, můžete nakonfigurovat funkci pomocí portálu Azure a klikněte na "Statická webová stránka (preview)" v části "Nastavení" v levém navigačním panelu. Klikněte na tlačítko "Povoleno" a zadejte název dokumentu indexu a (volitelně) cesta k dokumentu vlastní chyby.

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

Nahrajte vaše webové prostředky ke kontejneru "$web", který byl vytvořen jako součást povolení statická webová stránka. Můžete to provést přímo na portálu Azure, nebo můžete využít výhod [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) nahrát struktury celý adresář. Nezapomeňte zahrnout dokument index s názvem, který jste nakonfigurovali. V tomto příkladu je název dokumentu "index.html".

Nakonec přejděte na koncový bod webové otestovat váš web.

## <a name="faq"></a>Nejčastější dotazy
**Je k dispozici pro všechny typy účtů úložiště statických webů?**  
Ne, hostování statická webová stránka je k dispozici v účtech úložiště standard storage GPv2 pouze.

**Jsou virtuální sítě úložiště a pravidla brány firewall na nový koncový bod webové podporována?**  
Ano, nový koncový bod webové dodržuje pravidla virtuální sítě a brány firewall nakonfigurované pro účet úložiště.

## <a name="next-steps"></a>Další postup
* [Přístup k objektům BLOB pomocí vlastní domény přes HTTPS pomocí Azure CDN](storage-https-custom-domain-cdn.md)
* [Konfigurace vlastního názvu doménu pro koncový bod služby objektů blob nebo webu](storage-custom-domain-name.md)
* [Azure Functions](/azure-functions/functions-overview.md)
* [Azure Web Apps](/app-service/app-service-web-overview.md)
* [Vytvoření první bez serveru webové aplikace](https://aka.ms/static-serverless-webapp)
