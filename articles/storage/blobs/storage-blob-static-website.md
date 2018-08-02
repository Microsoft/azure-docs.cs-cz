---
title: Hostování statického webu ve službě Azure Storage (Preview) | Dokumentace Microsoftu
description: Azure Storage teď nabízí statický web, který je hostitelem (Preview), poskytuje nákladově efektivní, škálovatelné řešení pro hostování moderních webových aplikací.
services: storage
author: MichaelHauss
ms.service: storage
ms.topic: article
ms.date: 06/26/18
ms.author: mihauss
ms.component: blobs
ms.openlocfilehash: e53b573a27f0b1462ccf1170bbde2f8af01d0d3a
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397471"
---
# <a name="static-website-hosting-in-azure-storage-preview"></a>Hostování statického webu ve službě Azure Storage (Preview)
Azure Storage teď nabízí statického webu hostování (Preview), můžete nasadit nákladově efektivní a škálovatelné moderní webové aplikace v Azure. Webové stránky na statický web, obsahovat statický obsah a JavaScript nebo jiný kód na straně klienta. Naopak dynamické weby závisí na kódu na straně serveru a je možné hostovat pomocí [Azure Web Apps](/app-service/app-service-web-overview.md).

Nasazení shift směrem k pružné a nákladově efektivní modely, je důležité možnosti doručování webového obsahu bez nutnosti správy serverů. Po zavedení služby hostování statického webu ve službě Azure Storage to kvůli tomu je to možné, povolení možností bohaté back-endu s využitím architektur bez serveru [Azure Functions](/azure-functions/functions-overview.md) a další služby PaaS.

## <a name="how-does-it-work"></a>Jak to funguje?
Když povolíte statických webů na vašem účtu úložiště, vytvoří se nový koncový bod webové služby formuláře `<account-name>.<zone-name>.web.core.windows.net`.

Koncový bod webové služby vždy umožňuje anonymní přístup pro čtení, vrátí formátovaný stránky HTML v reakci na chyby služby a umožňuje pouze objekt operace čtení. Koncový bod webové služby vrátí dokumentu indexu v požadovaném adresáři, kořenovém adresáři a všech podadresářích. Když službu storage vrátí chybu 404, webový koncový bod dokumentu vlastní chybové vrátí, pokud jste je nakonfigurovali.

Obsah pro váš statický web je hostován ve speciální kontejner s názvem "$web". Jako součást procesu povolování se "$web" vytvoří za vás, pokud ještě neexistuje. V kořenovém adresáři účtu pomocí webový koncový bod je přístupný obsah "$web". Například `https://contoso.z4.web.core.windows.net/` vrátí dokumentu indexu, který jste nakonfigurovali pro web, pokud dokument s tímto názvem existuje v kořenovém adresáři $web.

Při nahrávání obsahu do vašeho webu, použijte koncový bod služby blob storage. K nahrání objektu blob s názvem "image.jpg", který je přístupný v kořenovém adresáři účtu použijte následující adresu URL `https://contoso.blob.core.windows.net/$web/image.jpg`. Nahraný obrázek může zobrazit ve webovém prohlížeči na odpovídající webový koncový bod `https://contoso.z4.web.core.windows.net/image.jpg`.


## <a name="custom-domain-names"></a>Vlastní názvy domén
Vlastní doménu můžete použít k hostování webového obsahu. Postupujte podle pokynů v [konfigurace vlastního názvu domény pro váš účet Azure Storage](storage-custom-domain-name.md). Přístup k vašemu webu hostovaných vlastního názvu domény pomocí protokolu HTTPS, naleznete v tématu [přístup k objektům BLOB s použitím vlastních domén přes protokol HTTPS pomocí Azure CDN](storage-https-custom-domain-cdn.md).

## <a name="pricing-and-billing"></a>Ceny a fakturace
Hostoval statický web je k dispozici bez dalších poplatků. Další podrobnosti o cenách pro službu Azure Blob Storage, podívejte se [Azure Blob Storage stránce s cenami](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="quickstart"></a>Rychlý start
### <a name="azure-portal"></a>portál Azure
Pokud chcete začít hostovat webové aplikace ve službě Azure Storage, můžete nakonfigurovat funkci na portálu Azure a klikněte na "Statický web (preview)" v části "Nastavení" v levém navigačním panelu. Klikněte na "Povoleno" a zadejte název dokumentu indexu a (volitelně) cesta dokumentu chyby vlastní.

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

Nahrajte webových prostředků do kontejneru "$web", který byl vytvořen jako součást povolení statického webu. Můžete to provést přímo na webu Azure Portal nebo můžete využít výhod [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) nahrát celý adresář struktury. Nezapomeňte uvést dokument index s názvem, který jste nakonfigurovali. V tomto příkladu je název dokumentu "index.html".

> [!NOTE]
> Název dokumentu je velká a malá písmena a proto musí přesně shodovat s názvem souboru v úložišti.

A konečně přejděte na koncový bod webové můžete otestovat váš web.

## <a name="faq"></a>Nejčastější dotazy
**Je k dispozici pro všechny typy účtů úložiště statických webů?**  
Ne, hostoval statický web je dostupná pouze v účtech GPv2 úložiště úrovně standard.

**Jsou virtuální sítě úložiště a pravidla brány firewall podporované na nový webový koncový bod?**  
Ano, nový webový koncový bod dodržuje pravidla virtuální sítě a brány firewall nakonfigurovaná pro účet úložiště.

## <a name="next-steps"></a>Další postup
* [Přístup k objektům BLOB s použitím vlastních domén přes protokol HTTPS pomocí Azure CDN](storage-https-custom-domain-cdn.md)
* [Konfigurace vlastního názvu domény pro koncový bod služby blob nebo web](storage-custom-domain-name.md)
* [Azure Functions](/azure-functions/functions-overview.md)
* [Azure Web Apps](/app-service/app-service-web-overview.md)
* [Vytvořte svoji první aplikaci bez serveru web](https://aka.ms/static-serverless-webapp)
