---
title: Kodéry živého streamování doporučují Media Services – Azure | Microsoft Docs
description: Přečtěte si o službě Live streamování pro místní kodéry Doporučené Media Services
services: media-services
keywords: kódování; kodéry; média
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 08/08/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6b9cb325f2bb7419e32efd5bde4705786c5dbeb5
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934933"
---
# <a name="recommended-live-streaming-encoders"></a>Doporučené kodéry živého streamování

V Azure Media Services [živá událost](https://docs.microsoft.com/rest/api/media/liveevents) (kanál) představuje kanál pro zpracování obsahu živého streamování. Živá událost přijímá živé vstupní proudy jedním ze dvou způsobů.

* On-premises Live Encoder odesílá datový proud s více přenosovými rychlostmi nebo Smooth Streaming (fragmentovaný MP4) do živé události, která není povolena k provádění živého kódování pomocí Media Services. Ingestované datové proudy procházejí živými událostmi bez dalšího zpracování. Tato metoda se nazývá **předávací**. Živý kodér může poslat datový proud s jednou přenosovou rychlostí do předávacího kanálu. Tuto konfiguraci nedoporučujeme, protože neumožňuje streamování s adaptivní přenosovou rychlostí na klienta.

  > [!NOTE]
  > Použití předávací metody je nejúčinnější způsob, jak provádět živé streamování.
 
* On-premises Live Encoder pošle datový proud s jednou přenosovou rychlostí do živé události, která má povoleno provádět kódování v reálném čase pomocí Media Services v jednom z následujících formátů: RTMP nebo Smooth Streaming (fragmentovaný MP4). Živá událost pak provede živé kódování příchozího datového proudu s jednou přenosovou rychlostí do datového proudu s více přenosovými rychlostmi (adaptivní).

Podrobné informace o živém kódování pomocí Media Services najdete v tématu [živé streamování s Media Services V3](live-streaming-overview.md).

## <a name="live-encoders-that-output-rtmp"></a>Živé kodéry, které mají výstup RTMP

Služba Media Services doporučuje používat jeden z následujících kodérů pro kódování v reálném čase, které mají RTMP jako výstup. Podporovaná schémata URL jsou `rtmp://` nebo `rtmps://`.

> [!NOTE]
> Při streamování přes RTMP zkontrolujte nastavení brány firewall nebo proxy serveru, aby se zajistilo, že jsou otevřené odchozí porty TCP 1935 a 1936.

- Adobe Flash Media Live Encoder 3.2
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream

## <a name="live-encoders-that-output-fragmented-mp4"></a>Živé kodéry, jejichž výstupem je fragment MP4

Media Services doporučuje použít jeden z následujících živých kodérů, které mají s více přenosovými rychlostmi Smooth Streaming (fragmentované MP4) jako výstup. Podporovaná schémata URL jsou `http://` nebo `https://`.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Představte si Communications Selenio MCP3
- Media Excel Hero Live a Hero 4K (UHD/HEVC)

> [!TIP]
>  Pokud vytváříte streamování živých událostí v několika jazycích (například jedna anglická zvuková stopa a jedna Španělská zvuková stopa), můžete toho dosáhnout pomocí Media Encoder Live kodéru nakonfigurovaného tak, aby odesílal živý kanál do předávací živé události.

## <a name="configuring-on-premises-live-encoder-settings"></a>Konfigurace nastavení místního kodéru pro Live Encoder

Informace o tom, jaká nastavení jsou platná pro váš typ živé události, najdete v tématu [porovnání typů událostí typu Live](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Požadavky na přehrávání

Aby bylo možné přehrávat obsah, musí být k dispozici zvukový a obrazový Stream. Přehrávání datového proudu, který je jen pro video, se nepodporuje.

### <a name="configuration-tips"></a>Tipy pro konfiguraci

- Kdykoli je to možné, použijte standardní kabelové internetové připojení.
- Při určování požadavků na šířku pásma Zdvojnásobte přenosové rychlosti streamování. I když není povinné, toto jednoduché pravidlo pomáhá zmírnit dopad zahlcení sítě.
- Při použití softwarových kodérů uzavřete všechny nepotřebné programy.
- Změna konfigurace kodéru po zahájení práce má na událost negativní vliv. Změny konfigurace můžou způsobit, že dojde k nestabilitě události. 
- Ujistěte se, že máte k nastavení události dostatek času. Pro vysoce škálovatelné události doporučujeme spustit nastavení hodinu před událostí.

## <a name="becoming-an-on-premises-encoder-partner"></a>Stane se místní partner kodéru.

Jako Azure Media Services on-premises Encoder partner Media Services propaguje váš produkt tím, že doporučí kodér zákazníkům pro podniky. Pokud se chcete stát místním partnerem kodéru, musíte ověřit kompatibilitu místního kodéru s Media Services. Provedete to tak, že provedete následující ověření.

### <a name="pass-through-live-event-verification"></a>Předávací ověření události za provozu

1. Ujistěte se, že je v účtu Media Services spuštěný **koncový bod streamování** . 
2. Vytvořte a spusťte **předávací** živou událost. <br/> Další informace najdete v tématu [stavy událostí Live a fakturace](live-event-states-billing.md).
3. Získejte adresy URL pro příjem a nakonfigurujte místní kodér tak, aby používal adresu URL k odeslání živého datového proudu s více přenosovými rychlostmi do Media Services.
4. Získejte adresu URL náhledu a použijte ji k ověření, že vstup z kodéru je skutečně přijatý.
5. Vytvoří nový objekt **assetu** .
6. Vytvořte **živý výstup** a použijte název assetu, který jste vytvořili.
7. Vytvořte **Lokátor streamování** s integrovanými typy **zásad streamování** .
8. Vypište cesty na **lokátoru streamování** a vraťte tak adresy URL, které se mají použít.
9. Získejte název hostitele **koncového bodu streamování** , ze kterého chcete streamovat datový proud.
10. Kombinací adresy URL z kroku 8 s názvem hostitele v kroku 9 získáte úplnou adresu URL.
11. Spusťte živý kodér po dobu přibližně 10 minut.
12. Zastaví živou událost. 
13. Pomocí přehrávače, jako je například [Azure Media Player](https://aka.ms/azuremediaplayer) , Sledujte archivovaný Asset a ujistěte se, že přehrávání nemá žádné viditelné histogramu na všech úrovních kvality. Nebo sledujte a ověřte pomocí adresy URL náhledu během živé relace.
14. Poznamenejte si ID assetu, publikovanou adresu URL streamování pro živý archiv a nastavení a verzi používanou z kodéru Live Encoder.
15. Po vytvoření každého vzorku resetujte stav živé události.
16. Opakujte kroky 5 až 15 pro všechny konfigurace, které kodér podporuje (s signalizací a bez něj, popisky nebo jiné rychlosti kódování).

### <a name="live-encoding-live-event-verification"></a>Live Encoding – ověření události Live

1. Ujistěte se, že je v účtu Media Services spuštěný **koncový bod streamování** . 
2. Vytvořte a spusťte živou událost **živého kódování** . <br/> Další informace najdete v tématu [stavy událostí Live a fakturace](live-event-states-billing.md).
3. Získejte adresy URL pro příjem a nakonfigurujte kodér tak, aby do Media Services načetl živý datový proud s jednou přenosovou rychlostí.
4. Získejte adresu URL náhledu a použijte ji k ověření, že vstup z kodéru je skutečně přijatý.
5. Vytvoří nový objekt **assetu** .
6. Vytvořte **živý výstup** a použijte název assetu, který jste vytvořili.
7. Vytvořte **Lokátor streamování** s integrovanými typy **zásad streamování** .
8. Vypište cesty na **lokátoru streamování** a vraťte tak adresy URL, které se mají použít.
9. Získejte název hostitele **koncového bodu streamování** , ze kterého chcete streamovat datový proud.
10. Kombinací adresy URL z kroku 8 s názvem hostitele v kroku 9 získáte úplnou adresu URL.
11. Spusťte živý kodér po dobu přibližně 10 minut.
12. Zastaví živou událost.
13. Pomocí přehrávače, jako je například [Azure Media Player](https://aka.ms/azuremediaplayer) , Sledujte archivovaný Asset a ujistěte se, že přehrávání nemá žádné viditelné histogramu pro všechny úrovně kvality. Nebo sledujte a ověřte pomocí adresy URL náhledu během živé relace.
14. Poznamenejte si ID assetu, publikovanou adresu URL streamování pro živý archiv a nastavení a verzi používanou z kodéru Live Encoder.
15. Po vytvoření každého vzorku resetujte stav živé události.
16. Opakujte kroky 5 až 15 pro všechny konfigurace, které kodér podporuje (s signalizací a bez něj, popisky nebo jiné rychlosti kódování).

### <a name="longevity-verification"></a>Longevity ověřování

Použijte stejný postup jako v rámci [předávacího ověřování pro živé události](#pass-through-live-event-verification) s výjimkou kroku 11. <br/>Místo 10 minut spusťte živý kodér po dobu jednoho týdne nebo déle. Pomocí přehrávače, jako je například [Azure Media Player](https://aka.ms/azuremediaplayer) , Sledujte živé streamování v čase (nebo archivovaný Asset), abyste zajistili, že přehrávání nemá žádné viditelné histogramu.

### <a name="email-your-recorded-settings"></a>Poslat zaznamenaná nastavení e-mailem

Nakonec odešlete e-mailem zaznamenaná nastavení a parametry živého archivu do Azure Media Services amshelp@microsoft.com jako oznámení, že prošly všechny kontroly pro vlastní ověření. Také zahrňte kontaktní informace pro jakékoliv následné zprávy. Můžete se obrátit na tým Azure Media Services s případnými dotazy týkajícími se tohoto procesu.

## <a name="next-steps"></a>Další postup

[Živé streamování s Media Services V3](live-streaming-overview.md)
