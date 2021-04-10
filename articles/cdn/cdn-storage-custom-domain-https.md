---
title: 'Kurz: Přístup k objektům blob úložiště pomocí vlastní domény Azure CDN přes HTTPS'
description: Naučte se, jak přidat vlastní doménu Azure CDN a povolit HTTPS v této doméně pro svůj vlastní koncový bod služby Blob Storage.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 296a51edf024551a03330f4843690e97c6abd47d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564879"
---
# <a name="tutorial-access-storage-blobs-using-an-azure-cdn-custom-domain-over-https"></a>Kurz: Přístup k objektům blob úložiště pomocí vlastní domény Azure CDN přes HTTPS

Po integraci účtu úložiště Azure se službou Azure CDN (Content Delivery Network) můžete přidat vlastní doménu a povolit u ní protokol HTTPS pro vlastní koncový bod úložiště objektů blob. 

## <a name="prerequisites"></a>Požadavky

Než budete moct dokončit postup uvedený v tomto kurzu, musíte nejprve integrovat účet úložiště Azure se službou Azure CDN. Další informace najdete v článku [Rychlý start: Integrace účtu úložiště Azure s Azure CDN](cdn-create-a-storage-account-with-cdn.md).

## <a name="add-a-custom-domain"></a>Přidání vlastní domény
Ve výchozím nastavení se při vytváření koncového bodu CDN ve vašem profilu vloží název koncového bodu, což je subdoména domény azureedge.net, do adresy URL pro doručování obsahu CDN. Máte také možnost přidružit ke koncovému bodu CDN vlastní doménu. Díky této možnosti můžete doručovat obsah na adrese URL s vlastní doménou místo názvu koncového bodu. Pokud chcete do svého koncového bodu přidat vlastní doménu, postupujte podle pokynů v kurzu týkajícím se [přidání vlastní domény do koncového bodu Azure CDN](cdn-map-content-to-custom-domain.md).

## <a name="configure-https"></a>Konfigurace HTTPS
Když u vlastní domény použijete protokol HTTPS, zajistíte tak, že se vaše data budou na internetu doručovat zabezpečeně prostřednictvím šifrování TLS/SSL. Když se webový prohlížeč připojí k webu přes HTTPS, ověří certifikát zabezpečení webu a to, že je vydán legitimní certifikační autoritou. Pokud chcete nakonfigurovat protokol HTTPS pro vlastní doménu, postupujte podle pokynů uvedených v kurzu: [Konfigurace HTTPS pro vlastní doménu Azure CDN](cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Sdílené přístupové podpisy
Pokud je koncový bod úložiště objektů blob nakonfigurovaný tak, aby zakazoval anonymní přístup pro čtení, měli byste poskytnout token [sdíleného přístupového podpisu (SAS)](cdn-sas-storage-support.md) v každé žádosti, kterou ve vlastní doméně provedete. Koncové body úložiště objektů blob ve výchozím nastavení anonymní přístup pro čtení zakazují. Další informace o sdíleném přístupovém podpisu najdete v tématu o [správě anonymního přístupu pro čtení ke kontejnerům a objektům blob](../storage/blobs/anonymous-read-access-configure.md).

Azure CDN ignoruje jakákoli omezení přidaná do tokenu SAS. Všechny tokeny SAS například mají čas konce platnosti, což znamená, že k obsahu lze dále přistupovat i s SAS, jehož platnost vypršela, dokud nebude daný obsah ze serverů POP (point-of-presence) služby CDN odstraněn. To, jak dlouho budou data uložena v mezipaměti Azure CDN, můžete řídit nastavením hlavičky odpovědi mezipaměti. Další informace najdete v článku o [správě vypršení platnosti objektů blob úložiště Azure v Azure CDN](cdn-manage-expiration-of-blob-content.md).

Pokud vytvoříte více adres URL SAS pro stejný koncový bod objektu blob, zvažte povolení ukládání řetězce dotazu do mezipaměti. Zajistíte tím, že se každá adresa URL bude považovat za jedinečnou entitu. Další informace najdete v článku o [řízení chování Azure CDN při ukládání řetězců dotazu do mezipaměti](cdn-query-string.md).

## <a name="http-to-https-redirection"></a>Přesměrování HTTP na HTTPS
Můžete se rozhodnout přesměrovat provoz HTTP na HTTPS tím, že vytvoříte pravidlo přesměrování adresy URL s modulem [Standard Rules](cdn-standard-rules-engine.md) nebo s [modulem pravidel Verizon Premium](cdn-verizon-premium-rules-engine.md). Modul Standard Rules je dostupný jenom pro Azure CDN z profilů Microsoftu, zatímco modul pravidel Premium Verizon je k dispozici jenom z Azure CDN Premium z profilů Verizon.

![Pravidlo přesměrování Microsoftu](./media/cdn-storage-custom-domain-https/cdn-standard-redirect-rule.png)

Ve výše uvedeném pravidle ponechte název hostitele, cestu, řetězec dotazu a fragment hodnoty v přesměrování na použité příchozí hodnoty. 

![Pravidlo přesměrování Verizon](./media/cdn-storage-custom-domain-https/cdn-url-redirect-rule.png)

Ve výše uvedeném pravidle *CDN-Endpoint-Name* odkazuje na název, který jste nakonfigurovali pro koncový bod CDN, který můžete vybrat z rozevíracího seznamu. Hodnota pro *origin-path* odkazuje na cestu v rámci původního účtu úložiště, ve kterém je uložen statický obsah. Pokud veškerý statický obsah hostujete v jednom kontejneru, nahraďte hodnotu *origin-path* názvem tohoto kontejneru.

## <a name="pricing-and-billing"></a>Ceny a fakturace
Při přístupu k objektům blob prostřednictvím služby Azure CDN platíte [ceny za úložiště objektů blob](https://azure.microsoft.com/pricing/details/storage/blobs/) pro přenos mezi servery POP a původním umístěním (úložiště objektů blob) a [ceny za Azure CDN](https://azure.microsoft.com/pricing/details/cdn/) pro data, ke kterým ze serverů POP přistupujete.

Pokud třeba máte v USA účet úložiště, ke kterému přistupujete pomocí služby Azure CDN, a uživatel v Evropě se k jednomu z objektů blob v tomto účtu úložiště pokusí přistoupit prostřednictvím služby Azure CDN, služba Azure CDN pro tento objekt blob nejprve ověří server POP, který je nejblíže Evropě. Pokud ho najde, Azure CDN přistoupí k této kopii objektu blob a použije ceny CDN, protože se k němu přistupuje ve službě Azure CDN. Pokud ho nenajde, Azure CDN zkopíruje objekt blob na server POP. V důsledku toho dojde k výchozímu přenosu a transakčním poplatkům, které jsou specifikované cenami za úložiště objektů blob. Potom se k souboru přistoupí na serveru POP, což je zpoplatněno cenami za Azure CDN.

## <a name="next-steps"></a>Další kroky
[Kurz: Nastavení pravidel ukládání do mezipaměti Azure CDN](cdn-caching-rules-tutorial.md)