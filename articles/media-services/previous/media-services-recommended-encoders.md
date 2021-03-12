---
title: Další informace o kodérech doporučených Azure Media Services | Microsoft Docs
description: Tento článek obsahuje seznam místních kodérů doporučených pro Azure Media Services.
services: media-services
keywords: kódování; kodéry; média
author: IngridAtMicrosoft
manager: johndeu
ms.author: johndeu
ms.date: 03/10/2021
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 98d78a105bf06a2e49dee0b8c2be710b220a0023
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009432"
---
# <a name="recommended-on-premises-encoders"></a>Doporučené místní kodéry

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Když živé streamování s Azure Media Services, můžete určit, jak chcete, aby kanál dostal vstupní datový proud. Pokud se rozhodnete použít místní kodér s kanálem pro kódování v reálném čase, váš kodér by měl jako výstup nabízet vysoce kvalitní datový proud s jednou přenosovou rychlostí. Pokud se rozhodnete použít místní kodér s průchozím kanálem, měl by váš kodér nabízet datový proud s více přenosovými rychlostmi jako výstup se všemi požadovanými výstupními kvalitou. Další informace najdete v tématu [živé streamování s místními kodéry](media-services-live-streaming-with-onprem-encoders.md).

## <a name="encoder-requirements"></a>Požadavky kodéru

Kodéry musí podporovat protokol TLS 1,2 při použití protokolů HTTPS nebo RTMP.

## <a name="live-encoders-that-output-rtmp"></a>Živé kodéry, které mají výstup RTMP 

Azure Media Services doporučuje použít jeden z následujících živých kodérů, které mají RTMP jako výstup:

- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Wirecast (verze 13.0.2 nebo vyšší) z důvodu požadavku TLS 1,2)

  Kodéry musí podporovat protokol TLS 1,2 při použití protokolů RTMP.
- Teradek Slice 756
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Živé kodéry, jejichž výstupem je fragment MP4 

Azure Media Services doporučuje použít jeden z následujících živých kodérů, které mají s více přenosovými rychlostmi fragmenty-MP4 (Smooth Streaming) jako výstup:

- Media Excel Hero Live a Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Element-Live (verze 2.14.15 a vyšší z důvodu požadavku TLS 1,2)

  Kodéry musí podporovat protokol TLS 1,2 při použití protokolů HTTPS.
- Envivio 4Caster C4 Gen III
- Představte si Communications Selenio MCP3

> [!NOTE]
> Live Encoder může poslat datový proud s jednou přenosovou rychlostí do předávacího kanálu, ale tato konfigurace se nedoporučuje, protože nepovoluje streamování s adaptivní přenosovou rychlostí pro klienta.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Jak se stát místním partnerem kodéru

Jako Azure Media Services v místním partnerovi kodéru Media Services propaguje svůj produkt tím, že doporučí kodér zákazníkům pro podniky. Pokud se chcete stát místním partnerem kodéru, musíte ověřit kompatibilitu místního kodéru s Media Services. Provedete to tak, že provedete následující ověření:

Předávací ověřování kanálu
1. Vytvořit nebo navštívit účet Azure Media Services
2. Vytvoření a spuštění **předávacího** kanálu
3. Nakonfigurujte kodér tak, aby načetl živý datový proud s více přenosovými rychlostmi.
4. Vytvořit publikovanou živou událost
5. Spusťte živý kodér po dobu přibližně 10 minut.
6. Zastavit živou událost
7. Vytvořte, spusťte koncový bod streamování, pomocí přehrávače, jako je například [Azure Media Player](https://aka.ms/azuremediaplayer) , Sledujte archivovaný Asset, abyste zajistili, že přehrávání nemá žádné viditelné histogramu pro všechny úrovně kvality (nebo můžete sledovat a ověřovat přes adresu URL náhledu během živé relace před krokem 6).
8. Poznamenejte si ID Assetu, publikovanou adresu URL streamování pro živý archiv a nastavení a verzi, která se používá z kodéru Live Encoder.
9. Po vytvoření každého vzorku resetovat stav kanálu
10. Opakujte kroky 3 až 9 pro všechny konfigurace, které kodér podporuje (s a bez něj signalizace/titulky a různé rychlosti kódování).

Ověřování kanálu v reálném kódování
1. Vytvořit nebo navštívit účet Azure Media Services
2. Vytvoření a spuštění kanálu pro **kódování v reálném čase**
3. Nakonfigurujte kodér tak, aby načetl živý datový proud s jednou přenosovou rychlostí.
4. Vytvořit publikovanou živou událost
5. Spusťte živý kodér po dobu přibližně 10 minut.
6. Zastavit živou událost
7. Vytvořte, spusťte koncový bod streamování, pomocí přehrávače, jako je například [Azure Media Player](https://aka.ms/azuremediaplayer) , Sledujte archivovaný Asset, abyste zajistili, že přehrávání nemá žádné viditelné histogramu pro všechny úrovně kvality (nebo můžete sledovat a ověřovat přes adresu URL náhledu během živé relace před krokem 6).
8. Poznamenejte si ID Assetu, publikovanou adresu URL streamování pro živý archiv a nastavení a verzi, která se používá z kodéru Live Encoder.
9. Po vytvoření každého vzorku resetovat stav kanálu
10. Opakujte kroky 3 až 9 pro všechny konfigurace podporované vaším kodérem (s a bez inzerce/titulků a s různými rychlostmi kódování).

Longevity ověřování
1. Vytvořit nebo navštívit účet Azure Media Services
2. Vytvoření a spuštění **předávacího** kanálu
3. Nakonfigurujte kodér tak, aby načetl živý datový proud s více přenosovými rychlostmi.
4. Vytvořit publikovanou živou událost
5. Spusťte živý kodér po dobu jednoho týdne nebo delšího.
6. Pomocí přehrávače, jako je například [Azure Media Player](https://aka.ms/azuremediaplayer) , Sledujte živé streamování v čase (nebo archivovaný Asset), abyste zajistili, že přehrávání nemá žádné viditelné histogramu
7. Zastavit živou událost
8. Poznamenejte si ID Assetu, publikovanou adresu URL streamování pro živý archiv a nastavení a verzi, která se používá z kodéru Live Encoder.

Nakonec odešlete zaznamenaná nastavení a parametry pro živý archiv do Media Services e-mailem amsstreaming@microsoft.com . Po přijetí Media Services provádí ověřovací testy na ukázkách z kodéru Live Encoder. Media Services můžete kontaktovat s případnými dotazy týkajícími se tohoto procesu.
