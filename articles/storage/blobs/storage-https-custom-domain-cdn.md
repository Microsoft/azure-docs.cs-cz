---
title: Použití Azure CDN pro přístup k objektům blob s vlastními doménami přes HTTPS
description: Naučte se integrovat Azure CDN do úložiště objektů BLOB pro přístup k objektům blob s vlastními doménami přes HTTPS.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 3ad599182191e41ea43d38260692a7ab46e1af6f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68844997"
---
# <a name="use-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Použití Azure CDN pro přístup k objektům blob s vlastními doménami přes HTTPS

Azure Content Delivery Network (Azure CDN) teď pro vlastní názvy domén podporuje protokol HTTPS. Pomocí Azure CDN můžete získat přístup k objektům blob pomocí vlastního názvu domény přes protokol HTTPS. Provedete to tak, že povolíte Azure CDN v objektu BLOB nebo webovém koncovém bodu a potom namapujete Azure CDN na vlastní název domény. Až to budete mít, Azure zjednoduší povolení HTTPS pro vaši vlastní doménu prostřednictvím přístupu a kompletní správy certifikátů. Nemusíte zvyšovat ceny za běžné Azure CDN.

Azure CDN pomáhá chránit ochranu osobních údajů a integritu dat webových aplikací během přenosu. Když použijete protokol SSL k obsluze provozu přes HTTPS, Azure CDN při posílání přes Internet uchovávat vaše data. Použití protokolu HTTPS s Azure CDN pomáhá chránit vaše webové aplikace před útoky.

> [!NOTE]  
> Kromě poskytování podpory SSL pro vlastní názvy domén vám Azure CDN může pomoci škálovat aplikaci tak, aby poskytovala obsah s vysokou šířkou pásma po celém světě. Další informace najdete v tématu [přehled Azure CDN](../../cdn/cdn-overview.md).

## <a name="quickstart"></a>Rychlý start

Pokud chcete povolit protokol HTTPS pro svůj vlastní koncový bod služby Blob Storage, udělejte toto:

1.  [Integrujte účet Azure Storage pomocí Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).  
    Tento článek vás provede vytvořením účtu úložiště ve Azure Portal, pokud jste to ještě neudělali.

    > [!NOTE]  
    > Pokud chcete přidat webový koncový bod úložiště během verze Preview podpory statických webů v Azure Storage, vyberte v rozevíracím seznamu **Typ původu** možnost **vlastní původ** . V Azure Portal musíte provést z profilu Azure CDN místo přímo v účtu úložiště.

2.  [Namapujte Azure CDN obsahu na vlastní doménu](../../cdn/cdn-map-content-to-custom-domain.md).

3.  [Povolte https u Azure CDN vlastní domény](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Sdílené přístupové podpisy

Ve výchozím nastavení zakazují koncovým bodům služby Blob Storage anonymní přístup pro čtení. Pokud je váš koncový bod služby Blob Storage nakonfigurovaný tak, aby zakázal anonymní přístup pro čtení, poskytněte v každé žádosti do vaší vlastní domény token [sdíleného přístupového podpisu](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) . Další informace najdete v tématu [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](storage-manage-access-to-resources.md).

Azure CDN nerespektují žádná omezení přidaná do tokenu sdíleného přístupového podpisu. Například platnost všech tokenů sdíleného přístupového podpisu vyprší. Přístup k obsahu s podpisem sdíleného přístupu s vypršenou platností můžete i nadále používat, dokud nebude obsah vymazán z Azure CDN hraničních uzlů. To, jak dlouho budou data uložena v mezipaměti Azure CDN, můžete řídit nastavením hlavičky odpovědi mezipaměti. Další informace najdete v tématu [Správa vypršení platnosti objektů blob Azure Storage v Azure CDN](../../cdn/cdn-manage-expiration-of-blob-content.md).

Pokud pro stejný koncový bod objektu BLOB vytvoříte dvě nebo víc adres URL sdíleného přístupového podpisu, doporučujeme pro Azure CDN zapnout ukládání řetězců dotazů do mezipaměti. Tato akce zajistí, že Azure považuje každou adresu URL za jedinečnou entitu. Další informace najdete v tématu [Řízení chování Azure CDN při ukládání řetězců dotazu do mezipaměti](../../cdn/cdn-query-string.md).

## <a name="http-to-https-redirection"></a>Přesměrování HTTP na HTTPS

Přenos HTTP můžete přesměrovat na HTTPS. To vyžaduje použití nabídky Azure CDN Premium z Verizon. Pomocí následujícího pravidla [přepište chování HTTP pomocí modulu Azure CDN Rules](../../cdn/cdn-rules-engine.md) :

![Pravidlo přesměrování HTTP na HTTPS](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

*CDN – koncový bod – název*, který vyberete v rozevíracím seznamu, odkazuje na název, který jste nakonfigurovali pro váš koncový bod Azure CDN. *Počátek – cesta* odkazuje na cestu v účtu úložiště, kde je uložený váš statický obsah. Pokud hostuje veškerý statický obsah v jednom kontejneru, nahraďte položku *Origin-Path* názvem tohoto kontejneru.

Hlubší podrobně na pravidla najdete v tématu [funkce modulu pravidla Azure CDN](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Ceny a fakturace

Při přístupu k objektům blob prostřednictvím Azure CDN platíte [ceny za úložiště objektů BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/) pro provoz mezi hraničními uzly a zdrojem (BLOB Storage). Platíte [Azure CDN ceny](https://azure.microsoft.com/pricing/details/cdn/) za data, která jsou k dispozici z hraničních uzlů.

Řekněme například, že máte účet úložiště v Západní USA, ke kterému přistupujete prostřednictvím Azure CDN. Když se někdo ve Spojeném království pokusí získat přístup k objektu BLOB v tomto účtu úložiště prostřednictvím Azure CDN, Azure nejdřív zkontroluje objekt blob na hraničním uzlu, který je nejblíže k velkému Británie. Pokud Azure najde objekt blob, přistupuje k kopii a používá Azure CDN cen, protože k ní přistupuje Azure CDN. Pokud Azure nenajde objekt blob, zkopíruje objekt blob do hraničního uzlu. Výsledkem této akce jsou náklady na výstup a transakce, které jsou uvedené v cenách služby Blob Storage. Azure potom přístup k souboru na hraničním uzlu, který vede Azure CDN fakturaci.

Na [stránce s cenami Azure CDN](https://azure.microsoft.com/pricing/details/cdn/)je podpora protokolu HTTPS pro vlastní názvy domén dostupná pro Azure CDN jenom z produktů Verizon Standard a Premium.

## <a name="next-steps"></a>Další postup

* [Konfigurace vlastního názvu domény pro koncový bod služby Blob Storage](storage-custom-domain-name.md)
* [Hostování statického webu ve službě Azure Storage](storage-blob-static-website.md)
