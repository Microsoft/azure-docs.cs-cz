---
title: Přístup k objektům BLOB s použitím vlastních domén přes protokol HTTPS pomocí Azure CDN
description: Zjistěte, jak integrovat Azure CDN pomocí úložiště objektů blob a přístup k objektům BLOB s použitím vlastních domén přes protokol HTTPS
services: storage
author: michaelhauss
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: mihauss
ms.component: blobs
ms.openlocfilehash: 7c4acc7d0832442b94735619ea3a01cb319da993
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398251"
---
# <a name="using-the-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Přístup k objektům BLOB s použitím vlastních domén přes protokol HTTPS pomocí Azure CDN
Azure Content Delivery Network (CDN) teď podporuje HTTPS pro vlastní názvy domén. Můžete využít tuto funkci přístup k objektům BLOB storage pomocí vaší vlastní doméně přes protokol HTTPS. Uděláte to tak, budete nejdřív muset povolení Azure CDN na váš koncový bod objektu blob nebo web a mapování CDN na vlastní název domény. Jakmile tyto kroky povolení HTTPS pro vlastní domény je zjednodušený povolování jedním kliknutím, kompletní správu certifikátů a všechny běžné ceny CDN bez dalších poplatků.

Tato funkce je důležitá, protože umožňuje ochrany osobních údajů a integrity dat vašich citlivých dat z webu aplikace v průběhu přenosu. Pomocí protokolu SSL pro obsluhu provozu přes protokol HTTPS se zajistí, že se data šifrují při posílání přes internet. HTTPS zajišťuje vztah důvěryhodnosti a ověřování a chrání vaše webové aplikace před útoky.

> [!NOTE]  
> Kromě toho, že podpora protokolu SSL pro vlastní názvy domén, Azure CDN můžete škálovat aplikaci k doručování širokopásmového obsahu po celém světě. Další informace, podívejte se na [přehled Azure CDN](../../cdn/cdn-overview.md).

## <a name="quick-start"></a>Rychlý start
Následují kroky nutné k povolení HTTPS pro koncový bod služby blob vlastního úložiště:

1.  [Integrace účtu služby Azure storage s Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md).
    Tento článek vás provede vytvořením účtu úložiště na webu Azure Portal, pokud jste tak již neučinili.

    > [!NOTE]  
    > Během období preview podpory statických webů ve službě Azure Storage vyberte "vlastní původ" z "typ počátku" rozevírací nabídka přidat koncový bod webové služby storage. Na webu Azure Portal je potřeba udělat z profilu CDN místo přímo ve vašem účtu úložiště.

2.  [Mapování obsahu Azure CDN na vlastní doménu](../../cdn/cdn-map-content-to-custom-domain.md).
3.  [Povolení HTTPS pro vlastní doménu Azure CDN](../../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Sdílené přístupové podpisy
Pokud chcete zakázat anonymní přístup pro čtení je nakonfigurovaný koncový bod služby blob storage, budete muset zadat [sdíleného přístupového podpisu (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) v každé žádosti o token provedete k vaší vlastní doméně. Ve výchozím nastavení zakázat koncových bodů úložiště objektů blob anonymní přístup pro čtení. Zobrazit [Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob](storage-manage-access-to-resources.md) Další informace o sdílených přístupových podpisů.

Azure CDN nerespektuje nějaká omezení, přidá do tokenu SAS. Například všechny tokeny SAS mít nastavený čas vypršení platnosti. To znamená, že obsah můžete stále přístupné s vypršenou platností SAS, dokud tento obsah je odstraněna z hraničních uzlů CDN. Můžete řídit, jak dlouho data je uložená v mezipaměti v CDN nastavením mezipaměti hlaviček odpovědi. Zobrazit [Správa vypršení platnosti objektů BLOB Azure Storage v Azure CDN](../../cdn/cdn-manage-expiration-of-blob-content.md) pokyny.

Pokud vytvoříte více adres URL SAS pro stejný koncový bod služby blob, doporučujeme zapnout v řetězci dotazu ukládání do mezipaměti pro Azure CDN. Tím je zajištěno, že každá adresa URL je považován za jedinečný entity. Zobrazit [řízení Azure CDN pomocí řetězců dotazu chování ukládání do mezipaměti](../../cdn/cdn-query-string.md) Další informace.

## <a name="http-to-https-redirection"></a>Protokol HTTP do HTTPS přesměrování
Můžete se rozhodnout, jestli pro přesměrování přenosu dat protokolu HTTP na HTTPS. To vyžaduje použití Azure CDN Premium od Verizonu nabídky. Je potřeba [chování přepsat HTTP pomocí Azure CDN stroj pravidel](../../cdn/cdn-rules-engine.md) následujícímu pravidlu:

![](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

"Název Cdn endpoint" odkazuje na název, který jste nakonfigurovali pro koncový bod CDN. Vyberte tuto hodnotu z rozevíracího seznamu. "Cesta k počátku" odkazuje na cestu v rámci účtu úložiště původ, ve které se nachází váš statický obsah. Pokud hostujete všechny statický obsah do jednoho kontejneru, nahraďte "cesta k počátku" název tohoto kontejneru.

Prozkoumejte podrobněji pravidla, najdete v tématu [funkce stroje pravidel Azure CDN](../../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Ceny a fakturace
Při přístupu k objektům BLOB pomocí Azure CDN, platíte [ceny za úložiště objektů Blob](https://azure.microsoft.com/pricing/details/storage/blobs/) pro přenos dat mezi hraniční uzly a původu (úložiště objektů Blob), a [ceny CDN](https://azure.microsoft.com/pricing/details/cdn/) pro data z hraničních uzlů.

Řekněme například, že máte účet úložiště v oblasti západní USA, který je přistupováno pomocí Azure CDN. Pokud někdo ve Spojeném království pokusí o přístup, jeden z objektů BLOB v účtu úložiště přes CDN, Azure nejprve zkontroluje nejblíže UK pro tento objekt blob na hraničním uzlu. Pokud najde, jeho přistupuje k nim tuto kopii objektu blob a použije ceny CDN, protože je přistupováno v síti CDN. Pokud není nalezen, Azure se kopírovat objekt blob k hraničnímu uzlu, který za následek poplatky za odchozí transakce a transakce podle ceny za objekty Blob storage a pak přístup k souboru na hraničním uzlu, který způsobí účtování CDN.

Při prohlížení [CDN stránce s cenami](https://azure.microsoft.com/pricing/details/cdn/), Všimněte si, že podpora HTTPS pro vlastní názvy domén je dostupný jenom pro Azure CDN od Verizonu produktů (Standard a Premium).

## <a name="next-steps"></a>Další postup
* [Konfigurace vlastního názvu domény pro koncový bod služby Blob storage](storage-custom-domain-name.md)
* [Hostování statického webu ve službě Azure Storage (Preview)](storage-blob-static-website.md)
