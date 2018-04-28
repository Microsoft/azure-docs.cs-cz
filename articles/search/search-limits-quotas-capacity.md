---
title: Omezení ve službě Azure Search služby | Microsoft Docs
description: Omezení služby používá pro plánování kapacity a maximální limit na požadavky a odpovědi pro službu Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: heidist
ms.openlocfilehash: 59b6efd8459f675bc44891d7b04251260fdd38e9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="service-limits-in-azure-search"></a>Omezení služby ve službě Azure Search
Maximální omezení na úložiště, úlohy a počty indexů, dokumentů, a další objekty závisí na tom, zda jste [zřízení Azure Search](search-create-service-portal.md) v **volné**, **základní**, nebo **Standardní** cenové úrovně.

+ **Volné** je víceklientské sdílené služby, která se dodává s předplatným Azure.

+ **Základní** poskytuje vyhrazený výpočetní prostředky pro úlohy v produkčním prostředí v menším měřítku.

+ **Standardní** běží na vyhrazené počítače s další kapacitou úložiště a zpracování na všech úrovních. Standard je rozdělena na čtyři úrovně: S1, S2, S3 a S3 HD.

  S3 (S3 HD) s vysokou hustotou je navržen s důrazem na konkrétní zatížení: [víceklientský](search-modeling-multitenant-saas-applications.md) a velké množství malých indexy (jeden milión dokumentů na index, tisíc tři indexy za služby). Tato úroveň neposkytuje [funkce indexeru](search-indexer-overview.md). Na S3 HD musí přijímání dat využít nabízené přístup, pomocí volání API vložit data ze zdroje na index. 

> [!NOTE]
> Služba se zřídí v konkrétní úroveň. Přechod k získání kapacity vrstvy zahrnuje zřizování (neexistuje žádné místní upgrade) nové služby. Další informace najdete v tématu [zvolte SKU nebo vrstvě](search-sku-tier.md). Další informace o úpravě kapacity v rámci služby jste už zřízené, najdete v části [škálovat prostředek úrovně pro dotaz a indexování úlohy](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Limity předplatného
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Limity úložiště
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Omezení indexu

| Prostředek | Free | Základní&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD |
| -------- | ---- | ------------------- | --- | --- | --- | --- |
| Maximální počet indexů |3 |5 nebo 15 |50 |200 |200 |1 000 na oddíl nebo 3 000 na službu |
| Maximální počet polí na index |1000 |100 |1000 |1000 |1000 |1000 |
| Maximální vyhodnocování profily pro jednotlivé indexu |100 |100 |100 |100 |100 |100 |
| Maximální funkce jeden profil |8 |8 |8 |8 |8 |8 |

<sup>1</sup> základní služby vytvořené po pozdní 2017 mít vyšší limit 15 indexy, indexery a zdroje dat. Služba vytvořený mají 5. Úroveň Basic je určená jenom SKU s nižší limit 100 polí na index.

## <a name="document-limits"></a>Omezení dokumentů vztahuje 

Ve většině oblastí Azure Search cenové úrovně (Basic, S1, S2, S3, S3 HD) počty neomezená dokumentů pro všechny služby vytvořili po listopadu/prosinci 2017. Tato část identifikuje oblastí, kde limity platí a jak určit, zda je vliv na vaši službu. 

Chcete-li určit, zda má vaše služba limity, zkontrolujte dlaždice využití na stránce Přehled služby. Zdokumentujte počty jsou neomezená nebo předmět k omezení podle úrovně.

  ![Dlaždice využití](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-and-services-having-document-limits"></a>Oblasti a služby s limity

Služby s limity byly vytvořeny před pozdní 2017, nebo jsou spuštěné v datových centrech pomocí clusterů nižší kapacitu pro hostování služby vyhledávání systému Azure. Ovlivněné datových centrech jsou v následujících oblastech:

+ Austrálie – východ
+ Východní Asie
+ Střed Indie
+ Japonsko – západ
+ Západní střed USA

Pro služby vztahují omezení dokumentů vztahuje platí následující maximální limit:

|  Free | Basic | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10,000 |1 milion |15 milionů na oddíl nebo 180 milionů na službu |60 milionů na oddíl nebo 720 milionů na službu |120 milionů na oddíl nebo 1,4 miliard na službu |1 milion na index nebo 200 milionů na oddíl |

> [!Note] 
> Pro služby S3 s vysokou hustotou vytvořené po pozdní 2017 200 milionů dokumentů na oddíl odebral ale 1 milion dokumentů na index limit zůstanou.


### <a name="document-size-limits-per-api-call"></a>Omezení velikosti dokumentu na volání rozhraní API

Dokument maximální velikost při volání rozhraní API Index je přibližně 16 MB.

Velikost dokument je ve skutečnosti omezení velikosti obsahu žádosti Index rozhraní API. Vzhledem k tomu, že můžete předat dávky více dokumentů do indexu API najednou, omezení velikosti reálně závisí na tom, kolik dokumenty jsou v dávce. Dokument maximální velikost dávky s jedním dokumentem, je 16 MB JSON.

Snížení velikosti dokumentu, nezapomeňte vyloučit-dotazovatelný data z požadavku. Obrázky a další binární data nejsou přímo dotazovatelný a by neměly být uloženy v indexu. K integraci dat bez dotazovatelné výsledky hledání, definujte není prohledávatelné pole, které obsahuje adresu URL odkaz na prostředek.

## <a name="indexer-limits"></a>Indexer omezení

Základní služby vytvořené po pozdní 2017 mít vyšší limit 15 indexy, indexery a zdroje dat.

| Prostředek | Volné&nbsp;<sup>1</sup> | Základní&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|
| -------- | ----------------- | ----------------- | --- | --- | --- | --- |
| Maximální počet indexerů |3 |5 nebo 15|50 |200 |200 |neuvedeno |
| Maximální počet zdrojů dat |3 |5 nebo 15 |50 |200 |200 |neuvedeno |
| Maximální indexování zatížení na vyvolání |10 000 dokumentů |Omezeno pouze maximální dokumenty |Omezeno pouze maximální dokumenty |Omezeno pouze maximální dokumenty |Omezeno pouze maximální dokumenty |neuvedeno |
| Maximální dobu běhu | 1 – 3 minut |24 hodin |24 hodin |24 hodin |24 hodin |neuvedeno  |
| Indexer objektů blob: velikost maximální objektu blob, MB |16 |16 |128 |256 |256 |neuvedeno  |
| Indexer objektů blob: maximální počet znaků z objektu blob extrahovat obsahu |32,000 |64,000 |4 miliony |4 miliony |4 miliony |neuvedeno |

<sup>1</sup> bezplatné služby mít maximální dobu spuštění indexeru 3 minut zdroje blob a pro všechny ostatní zdroje dat 1 minuta.

<sup>2</sup> základní služby vytvořené po pozdní 2017 mít vyšší limit 15 indexy, indexery a zdroje dat. Služba vytvořený mají 5.

<sup>3</sup> služby S3 HD Nezahrnovat podpora indexeru.

## <a name="queries-per-second-qps"></a>Dotazy na za sekundu (QPS)

Odhadne QPS musí být vyvinutá nezávisle každých zákazníkem. Index velikost a složitost, dotazu velikost a složitost a objem provozu, jsou primární faktory QPS. Neexistuje žádný způsob, jak nabízet smysluplný odhady při tyto faktory neznámé.

Odhadne jsou předvídatelnější při počítaného na služby spuštěné na vyhrazených prostředcích (úrovně Basic a Standard). QPS další můžete podrobně odhadnout, protože budete mít kontrolu nad více parametrů. Pokyny o tom, jak odhad přístupu najdete v tématu [výkonu Azure Search a optimalizace](search-performance-optimization.md).

## <a name="api-request-limits"></a>Omezení žádostí o rozhraní API
* Maximálně 16 MB každý požadavek <sup>1</sup>
* Maximální délka adresy URL 8 KB
* Maximální 1000 dokumentů na jednu dávku indexu nahrávání, sloučí nebo odstraní
* Maximální 32 polí v klauzuli $orderby
* Maximální hledání termín velikost je 32 766 bajtů (32 KB minus 2 bajtů) textu ve formátu UTF-8

<sup>1</sup> ve službě Azure Search je text požadavku podléhá horní limit 16 MB, nastavení praktické omezení na obsah jednotlivých polí nebo kolekce, které nejsou v opačném případě omezené teoretické omezení (viz [podporované dat typy](https://msdn.microsoft.com/library/azure/dn798938.aspx) Další informace o omezení a pole složení).

## <a name="api-response-limits"></a>Omezení odpovědi rozhraní API
* Maximální 1000 dokumenty vrácené na stránku výsledků hledání
* Maximální 100 návrhy vrátí na žádost navrhnout rozhraní API

## <a name="api-key-limits"></a>Omezení klíč rozhraní API
Klíče API Key se používají pro ověřování služby. Existují dva typy. Klíče správce se zadaným v hlavičce žádosti a udělit přístup úplné pro čtení a zápis ke službě. Klíče dotazu jsou jen pro čtení, zadaná na adresu URL a obvykle se distribuují klientským aplikacím.

* Maximálně 2 klíče správce pro službu
* Maximální počet klíčů 50 dotaz na službu
