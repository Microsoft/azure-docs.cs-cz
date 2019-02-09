---
title: Živé streamování kodérů doporučená službou Media Services – Azure | Dokumentace Microsoftu
description: Další informace o streamování s místními kodéry doporučená službou Media Services
services: media-services
keywords: kódování; kodérů; média
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 01/17/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: a165fac2de89d5510b21b9185d4bc61e730b09ff
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55960118"
---
# <a name="recommended-live-streaming-encoders"></a>Doporučené datové proudy kodéry

Ve službě Azure Media Services [živá událost](https://docs.microsoft.com/rest/api/media/liveevents) (kanál) představuje kanál pro zpracování obsahu živého streamování. Živá událost přijímat živé vstupní datové proudy v jednom ze dvou způsobů.

* Místní odešle kodér služby live Encoding pro živá událost, která není povoleno provádět živé kódování pomocí Media Services datového proudu s více přenosovými rychlostmi RTMP nebo technologie Smooth Streaming (fragmentovaný MP4). Ingestované datové proudy prochází živé události bez dalšího zpracování. Tato metoda je volána **předávací**. Live encoder můžete odeslat datový proud s jednou přenosovou rychlostí průchozí kanál. Tuto konfiguraci nedoporučujeme, protože ji nepovoluje pro adaptivní přenosové rychlosti streamování do klienta.

  > [!NOTE]
  > Použití průchozí metody je nejekonomičtější způsob, jak živě Streamovat.

* Místní kodér služby live Encoding odešle datový proud s jednou přenosovou rychlostí živá událost, který má povolené provádět živé kódování pomocí Media Services v jednom z následujících formátů: RTMP nebo technologie Smooth Streaming (fragmentovaný MP4). Živá událost potom provede kódování v reálném čase příchozího datového proudu s jednou přenosovou rychlostí na datový proud s více přenosovými rychlostmi (adaptivní) videa.

Podrobné informace o kódování v reálném čase pomocí služby Media Services najdete v tématu [živé streamování pomocí služby Media Services v3](live-streaming-overview.md).

## <a name="live-encoders-that-output-rtmp"></a>Kodéry výstupu RTMP

Služba Media Services se doporučuje používat jednu z následující kodéry, které mají RTMP jako výstup. Jsou podporované schémata URL `rtmp://` nebo `rtmps://`.

> [!NOTE]
 > Při streamování přes RTMP, zkontrolujte nastavení brány firewall nebo proxy serveru pro potvrzení, že jsou otevřené odchozí porty TCP 1935 a 1936.<br/>
 Při streamování přes RTMPS, zkontrolujte nastavení brány firewall nebo proxy serveru pro potvrzení, že jsou otevřené odchozí porty TCP 2935 a 2936.

- Adobe Flash Media Live Encoder 3.2
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Přepínání Studio (iOS)
- Telestream Wirecast 8.1 +
- Telestream Wirecast S
- Řez Teradek 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream

## <a name="live-encoders-that-output-fragmented-mp4"></a>Kodéry pro kódování, jejichž výstupem fragmentovaného MP4

Služba Media Services se doporučuje používat jednu z následující kodéry, které mají s více přenosovými rychlostmi technologie Smooth Streaming (fragmentovaný MP4) jako výstup. Jsou podporované schémata URL `http://` nebo `https://`.

- Ateme TITAN za provozu
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 obecné III
- Imagine Communications Selenio MCP3
- Média pro Excel Hero za provozu a Hero 4K (UHD/HEVC)

## <a name="configuring-on-premises-live-encoder-settings"></a>Konfigurace místní kodér služby live Encoding nastavení

Informace o tom, jaká nastavení jsou platné pro váš typ živou událost najdete v tématu [živá událost typy porovnání](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Požadavky pro přehrávání

K přehrávání obsahu, musí být k dispozici jak datovému proudu zvuku a videa. Přehrání datového proudu jen pro videa se nepodporuje.

### <a name="configuration-tips"></a>Tipy týkající se konfigurace

- Kdykoli je to možné, použijte standardní kabelové internetové připojení.
- Pokud při určování požadavků na šířku pásma, double streamování přenosových rychlostí. I když není povinné, toto jednoduché pravidlo pomáhá zmírnit dopad zahlcení sítě.
- Při použití softwarových kodérů uzavřete všechny nepotřebné programy.
- Po zahájení jeho doručením (push), když změníte konfiguraci kodér má negativní vliv na události. Změny konfigurace může způsobit nestabilitu události. 
- Ujistěte se, abyste udělili sami dostatek času na vytvoření události. Pro zajištění vysoce škálovatelné události doporučujeme spuštěním instalačního programu za hodinu před události.

## <a name="becoming-an-on-premises-encoder-partner"></a>Stát partnerem místní kodér

Jako partner Azure Media Services místní kodér Media Services podporuje váš produkt doporučením váš kodér pro podnikové zákazníky. Pokud chcete stát partnerem místní kodér, musíte ověřit kompatibilitu vaší místní kodér pomocí služby Media Services. Uděláte to tak, dokončete následující ověření.

### <a name="pass-through-live-event-verification"></a>Předávací ověřování živá událost

1. Ve vašem účtu Media Services, ujistěte se, že **koncový bod streamování** běží. 
2. Vytvoření a spuštění **předávací** živá událost. <br/> Další informace najdete v tématu [živá událost stavy a fakturace](live-event-states-billing.md).
3. Získání adres URL ingestování a konfiguraci vaší místní kodér odesílat živý datový proud s více přenosovými rychlostmi do Media Services pomocí adresy URL.
4. Získat adresu URL ve verzi preview a použít ho k ověření, že je ve skutečnosti přijímají vstup z kodéru.
5. Vytvořte nový **Asset** objektu.
6. Vytvoření **Live výstup** a používat název assetu, kterou jste vytvořili.
7. Vytvoření **Lokátor streamování** pomocí integrované **streamování zásad** typy.
8. V seznamu cest **Lokátor streamování** získat zpět adresy URL používat.
9. Získat název hostitele **koncový bod streamování** , kterou chcete z datového proudu.
10. Adresa URL v kroku 8 kombinovat s názvem hostitele v kroku 9 získat úplnou adresu URL.
11. Spusťte váš kodér služby live Encoding přibližně 10 minut.
12. Zastavte živou událost. 
13. Například použijte přehrávač [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) sledovat archivovaný asset, který k zajištění přehrávání nemá žádné viditelné potížím s vykreslováním na všech úrovních kvality. Nebo, sledovat a ověřit prostřednictvím adresy URL náhledu během živé relace.
14. ID assetu, publikované streamovací adresa URL pro živý archív a nastavení a verze se používá z váš kodér služby live Encoding záznamu.
15. Obnovit stav živá událost po vytvoření každý vzorek.
16. Opakujte kroky 5 až 15 u všech konfigurací s nepodporuje váš kodér (s opakováním a bez signalizace ad, titulky nebo různými rychlostmi kódování).

### <a name="live-encoding-live-event-verification"></a>Živé kódování živá událost ověření

1. Ve vašem účtu Media Services, ujistěte se, že **koncový bod streamování** běží. 
2. Vytvoření a spuštění **živého kódování** živá událost. <br/> Další informace najdete v tématu [živá událost stavy a fakturace](live-event-states-billing.md).
3. Získání adres URL ingestování a nakonfigurovat váš kodér tak, aby nabízel živý datový proud s jednou přenosovou rychlostí služby Media Services.
4. Získat adresu URL ve verzi preview a použít ho k ověření, že je ve skutečnosti přijímají vstup z kodéru.
5. Vytvořte nový **Asset** objektu.
6. Vytvoření **Live výstup** a používat název assetu, kterou jste vytvořili.
7. Vytvoření **Lokátor streamování** pomocí integrované **streamování zásad** typy.
8. V seznamu cest **Lokátor streamování** získat zpět adresy URL používat.
9. Získat název hostitele **koncový bod streamování** , kterou chcete z datového proudu.
10. Adresa URL v kroku 8 kombinovat s názvem hostitele v kroku 9 získat úplnou adresu URL.
11. Spusťte váš kodér služby live Encoding přibližně 10 minut.
12. Zastavte živou událost.
13. Například použijte přehrávač [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) sledovat archivovaný asset, který k zajištění přehrávání histogramu úplně bez viditelné pro všechny úrovně kvality. Nebo, sledovat a ověřit prostřednictvím adresy URL náhledu během živé relace.
14. ID assetu, publikované streamovací adresa URL pro živý archív a nastavení a verze se používá z váš kodér služby live Encoding záznamu.
15. Obnovit stav živá událost po vytvoření každý vzorek.
16. Opakujte kroky 5 až 15 u všech konfigurací s nepodporuje váš kodér (s opakováním a bez signalizace ad, titulky nebo různými rychlostmi kódování).

### <a name="longevity-verification"></a>Životnost ověření

Postupujte stejným způsobem jako v [živá událost předávací ověřování](#pass-through-live-event-verification) s výjimkou kroku 11. <br/>Místo 10 minut spusťte váš kodér služby live Encoding pro jeden týden nebo i delší dobu. Například použijte přehrávač [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) sledovat živé streamování z času na čas (nebo archivovaný asset) k zajištění přehrávání nemá žádné viditelné potížím s vykreslováním.

### <a name="email-your-recorded-settings"></a>Zaznamenané nastavení e-mailu

Nakonec zaznamenané nastavení e-mailu a live parametry archivu služby Azure Media Services na amsstreaming@microsoft.com jako oznámení, že se prošly všechny kontroly připravenosti ověření. Také vaše kontaktní informace pro všechny zpracování. Obraťte se na tým Azure Media Services s jakékoliv otázky týkající se tohoto procesu.

## <a name="next-steps"></a>Další postup

[Živé streamování pomocí služby Media Services v3](live-streaming-overview.md)
