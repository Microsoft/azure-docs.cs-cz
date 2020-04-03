---
title: Zásady obsahu klíče v mediálních službách - Azure | Dokumenty společnosti Microsoft
description: Tento článek poskytuje vysvětlení, jaké zásady klíče obsahu jsou a jak je používají Mediální služby Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 85a9cad80156dc6ac40e78610c91805d485ff3df
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585993"
---
# <a name="content-key-policies"></a>Zásady symetrických klíčů

Pomocí služby Media Services můžete poskytovat obsah živého i na vyžádání šifrovaný dynamicky pomocí advanced encryption standardu (AES-128) nebo některého ze tří hlavních systémů správy digitálních práv (DRM): Microsoft PlayReady, Google Widevine a Apple FairPlay. Mediální služby také poskytují službu pro doručování klíčů AES a licencí DRM (PlayReady, Widevine a FairPlay) oprávněným klientům. 

Chcete-li v datovém proudu určit možnosti šifrování, musíte vytvořit [zásady streamování](streaming-policy-concept.md) a přidružit je k [lokátoru streamování](streaming-locators-concept.md). Vytvoříte [zásady klíče obsahu](https://docs.microsoft.com/rest/api/media/contentkeypolicies) a nakonfigurujete, jak je klíč obsahu (který poskytuje zabezpečený přístup k vašim [prostředkům)](assets-concept.md)doručován koncovým klientům. Je třeba nastavit požadavky (omezení) na zásady klíče obsahu, které musí být splněny, aby klíče se zadanou konfigurací, které mají být dodány klientům. Zásady klíče obsahu nejsou potřebné pro jasné streamování nebo stahování. 

Zásady klíče obsahu obvykle přidružíte k [lokátoru streamování](streaming-locators-concept.md). Případně můžete zadat zásady klíče obsahu v rámci [zásad streamování](streaming-policy-concept.md) (při vytváření vlastní zásady streamování pro pokročilé scénáře). 

## <a name="best-practices-and-considerations"></a>Osvědčené postupy a důležité informace

> [!IMPORTANT]
> Přečtěte si následující doporučení.

* Měli byste navrhnout omezenou sadu zásad pro váš účet Media Service a znovu je použít pro lokátory streamování, kdykoli jsou potřeba stejné možnosti. Další informace naleznete v [tématu Kvóty a omezení](limits-quotas-constraints.md).
* Klíčové zásady obsahu jsou aktualizovatelné. Aktualizace a vyzvednutí aktualizovaných zásad může trvat až 15 minut. 

   Aktualizací zásad přepíšete stávající mezipaměť CDN, což může způsobit problém y s přehráváním pro zákazníky, kteří používají obsah uložený v mezipaměti.  
* Doporučujeme nevytvářet nové zásady klíče obsahu pro každý datový zdroj. Hlavní výhody sdílení stejné zásady obsahu klíče mezi prostředky, které potřebují stejné možnosti zásad jsou:
   
   * Je snazší spravovat malý počet zásad.
   * Pokud potřebujete provést aktualizace zásad klíče obsahu, změny vstoupí v platnost u všech nových žádostí o licenci téměř ihned.
* Pokud potřebujete vytvořit novou zásadu, musíte vytvořit nový lokátor streamování pro datový zdroj.
* Doporučujeme, aby media services automaticky generovat klíč obsahu. 

   Obvykle byste použili klíč s dlouhou životností a zkontrolovali existenci zásad klíče obsahu pomocí [funkce Získat](https://docs.microsoft.com/rest/api/media/contentkeypolicies/get). Chcete-li získat klíč, musíte volat samostatnou metodu akce získat tajné klíče nebo pověření, viz příklad, který následuje.

## <a name="example"></a>Příklad

Chcete-li se dostat `GetPolicyPropertiesWithSecretsAsync`ke klíči, použijte , jak je znázorněno v [například Získat podpisový klíč z existujícího příkladu zásad.](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets)

## <a name="filtering-ordering-paging"></a>Filtrování, řazení, stránkování

Viz [Filtrování, řazení, stránkování entit Mediálních služeb](entities-overview.md).

## <a name="additional-notes"></a>Další poznámky

* Vlastnosti zásad klíče obsahu, `Datetime` které jsou typu jsou vždy ve formátu UTC.
* Widevine je služba poskytovaná společností Google Inc. a podléhá podmínkám služeb a zásadám ochrany osobních údajů společnosti Google, Inc.

## <a name="next-steps"></a>Další kroky

* [Používejte dynamické šifrování AES-128 a službu doručování klíčů](protect-with-aes128.md)
* [Použití ochrany DRM s dynamickým šifrováním a služby doručování licencí](protect-with-drm.md)
* [ZakódovatHTTPAndPublishAESŠifrované](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
