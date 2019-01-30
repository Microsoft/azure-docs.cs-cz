---
title: Přístup k objektům BLOB s použitím vlastních domén přes protokol HTTPS pomocí Azure CDN
description: Zjistěte, jak integrovat Azure CDN pomocí úložiště objektů Blob a přístup k objektům BLOB s použitím vlastních domén přes protokol HTTPS
services: storage
author: michaelhauss
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: mihauss
ms.subservice: blobs
ms.openlocfilehash: 1166f98dcd39362ef5e28be12ff9e2f561ab5b1f
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238207"
---
# <a name="use-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Přístup k objektům BLOB s použitím vlastních domén přes protokol HTTPS pomocí Azure CDN

Azure Content Delivery Network (CDN v Azure) teď podporuje HTTPS pro vlastní názvy domén. S Azure CDN můžete přístup k objektům BLOB s použitím vlastního názvu domény pomocí protokolu HTTPS. Povolení Azure CDN na váš koncový bod objektu blob nebo web a potom mapování Azure CDN na vlastní název domény. Až budete hotovi, Azure usnadňuje povolení HTTPS pro vlastní doménu prostřednictvím přístupu jedním kliknutím a kompletní správě certifikátů. Neexistuje žádný nárůst běžné ceny Azure CDN.

Azure CDN k ochraně soukromí a integrity dat. data webové aplikace během přenosu. Azure CDN pomocí protokolu SSL pro obsluhu provozu přes protokol HTTPS zajišťuje dat při posílání přes internet. Pomocí protokolu HTTPS s Azure CDN pomáhá chránit vaše webové aplikace před útokem.

> [!NOTE]  
> Kromě toho, že podpora protokolu SSL pro vlastní názvy domén, Azure CDN můžete škálovat aplikaci k doručování širokopásmového obsahu po celém světě. Další informace najdete v tématu [přehled Azure CDN](../../cdn/cdn-overview.md).

## <a name="quickstart"></a>Rychlý start

Pokud chcete povolit HTTPS pro váš vlastní koncový bod služby Blob storage, postupujte takto:

1.  [Integrace účtu služby Azure storage s Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).  
    Tento článek vás provede vytvořením účtu úložiště na webu Azure Portal, pokud jste tak již neučinili.

    > [!NOTE]  
    > Chcete-li přidat koncový bod webové služby storage ve verzi preview podporu statických webů ve službě Azure Storage, **vlastní původ** v **typ počátku** rozevíracího seznamu. Na webu Azure Portal budete muset udělat z profilu Azure CDN namísto přímo ve vašem účtu úložiště.

2.  [Mapování obsahu Azure CDN na vlastní doménu](../../cdn/cdn-map-content-to-custom-domain.md).

3.  [Povolení HTTPS pro vlastní doménu Azure CDN](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Sdílené přístupové podpisy

Ve výchozím nastavení zakázat koncových bodů úložiště objektů Blob anonymní přístup pro čtení. Pokud chcete zakázat anonymní přístup pro čtení je nakonfigurovaný koncový bod služby Blob storage, zadejte [sdílený přístupový podpis](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) tokenu v každém požadavku na vaši vlastní doménu. Další informace najdete v tématu [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](storage-manage-access-to-resources.md).

Azure CDN nerespektuje omezení, které jsou přidány do token sdíleného přístupového podpisu. Například všechny sdílený přístupový podpis vyprší platnost těchto tokenů. Můžete dál přístup k obsahu s vypršenou platností sdílený přístupový podpis dokud tento obsah je odstraněna z hraničních uzlů Azure CDN. To, jak dlouho budou data uložena v mezipaměti Azure CDN, můžete řídit nastavením hlavičky odpovědi mezipaměti. Další informace o postupu [spravovat vypršení platnosti objektů BLOB Azure Storage v Azure CDN](../../cdn/cdn-manage-expiration-of-blob-content.md).

Pokud vytvoříte dva nebo více sdílený přístupový podpis adresy URL pro stejný koncový bod služby blob, doporučujeme zapnout v dotazu řetězec ukládání do mezipaměti pro Azure CDN. Tím zajistíte, že Azure považuje za každou adresu URL jedinečné entity. Další informace najdete v tématu [Řízení chování Azure CDN při ukládání řetězců dotazu do mezipaměti](../../cdn/cdn-query-string.md).

## <a name="http-to-https-redirection"></a>Protokol HTTP do HTTPS přesměrování

Můžete přesměrovat provoz protokolu HTTP na HTTPS. To vyžaduje použití Azure CDN premium od Verizonu nabídky. [Potlačení chování protokolu HTTP s Azure CDN stroj pravidel](../../cdn/cdn-rules-engine.md) s použitím následujících pravidel:

![Protokol HTTP do HTTPS pravidlo přesměrování](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

*Název koncového bodu CDN*, které vyberete v rozevíracím seznamu, odkazuje na název, který jste nakonfigurovali pro koncový bod Azure CDN. *Cesta k počátku* odkazuje na cestu v rámci účtu úložiště původ, kde je uložený statický obsah. Pokud máte všechny statický obsah do jednoho kontejneru, nahraďte *cesta k počátku* s názvem tohoto kontejneru.

Prozkoumejte podrobněji pravidla, najdete v článku [funkce stroje pravidel Azure CDN](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Ceny a fakturace

Při přístupu k objektům BLOB pomocí Azure CDN, platíte [ceny za úložiště objektů Blob](https://azure.microsoft.com/pricing/details/storage/blobs/) pro přenos dat mezi hraniční uzly a původu (úložiště objektů Blob). Platíte [ceny Azure CDN](https://azure.microsoft.com/pricing/details/cdn/) pro data, která je přístupná z hraničních uzlů.

Řekněme například, že máte účet úložiště v oblasti západní USA, který přistupujete přes Azure CDN. Když se někdo ve Spojeném království pokusí o přístup k objektu blob v tomto účtu úložiště pomocí Azure CDN, Azure nejprve zkontroluje, zda objekt blob na hraničním uzlu, který je nejblíž k Spojeném království. Pokud Azure najde objekt blob, přistupuje k nim kopii a používá ceny Azure CDN, protože Azure CDN je přístup. Pokud Azure nedokáže najít objekt blob, zkopíruje objekt blob k hraničnímu uzlu. Tato akce způsobí odchozí transakce a transakce poplatky, jak je uvedeno v ceny za objekty Blob storage. Azure pak přistupuje k souboru na hraničním uzlu, což vede k fakturace Azure CDN.

Na [stránce s cenami Azure CDN](https://azure.microsoft.com/pricing/details/cdn/), podporu protokolu HTTPS pro vlastní názvy domén je k dispozici pro Azure CDN pouze z produktů Verizon úrovně Standard a Premium.

## <a name="next-steps"></a>Další postup

* [Konfigurace vlastního názvu domény pro koncový bod služby Blob storage](storage-custom-domain-name.md)
* [Hostování statického webu ve službě Azure Storage (preview)](storage-blob-static-website.md)
