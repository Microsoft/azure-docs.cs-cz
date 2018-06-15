---
title: Postup živého streamování využívajícího službu Azure Media Services k vytvoření datových proudů s více přenosovými rychlostmi pomocí webu Azure Portal | Microsoft Docs
description: Tento kurz vás provede kroky k vytvoření kanálu, který přijímá datový proud s jednou přenosovou rychlostí a kóduje ho do datového proudu s více přenosovými rychlostmi pomocí webu Azure Portal.
services: media-services
documentationcenter: ''
author: anilmur
manager: cfowler
editor: ''
ms.assetid: 504f74c2-3103-42a0-897b-9ff52f279e23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: b9ac1696594ab9f7cb67bb0e340c6a80158e67e2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33940978"
---
# <a name="how-to-perform-live-streaming-using-azure-media-services-to-create-multi-bitrate-streams-with-the-azure-portal"></a>Postup živého streamování využívajícího službu Azure Media Services k vytvoření datových proudů s více přenosovými rychlostmi pomocí webu Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
> * [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
> * [REST API](https://docs.microsoft.com/rest/api/media/operations/channel)
> 
> 

Tento kurz vás provede kroky k vytvoření **kanálu**, který přijímá datový proud s jednou přenosovou rychlostí a kóduje ho do datového proudu s více přenosovými rychlostmi.

> [!NOTE]
> Další koncepční informace o kanálech s povoleným kódováním v reálném čase najdete v článku [Živé streamování využívající Azure Media Services k vytváření datových proudů s více přenosovými rychlostmi](media-services-manage-live-encoder-enabled-channels.md).
> 
> 

## <a name="common-live-streaming-scenario"></a>Běžný scénář živého streamování
Následující část představuje obecné kroky, které jsou součástí procesu vytváření běžných aplikací pro živé streamování.

> [!NOTE]
> V současné době doporučujeme maximální dobu trvání živé události v délce 8 hodin. Pokud potřebujete, aby kanál běžel delší dobu, kontaktujte nás na adrese amslived@microsoft.com.
> 
> 

1. Připojte k počítači videokameru. Spusťte a nakonfigurujte místní kodér pro kódování v reálném čase, který umí produkovat stream s jednou přenosovou rychlostí v jednom z následujících protokolů: RTMP nebo Smooth Streaming. Další informace najdete v článku [Podpora RTMP ve službě Azure Media Services a kodéry pro kódování v reálném čase](http://go.microsoft.com/fwlink/?LinkId=532824).
   
    Tento krok můžete provést i po vytvoření kanálu.
2. Vytvořte a spusťte kanál. 
3. Načtěte adresu URL ingestování kanálu. 
   
    Adresu URL ingestování používá kodér po kódování v reálném čase k odesílání datového proudu do kanálu.
4. Načtěte adresu URL náhledu kanálu. 
   
    Tuto adresu URL můžete použít, když chcete ověřit, jestli kanál správně přijímá proud živého vysílání.
5. Vytvořte událost nebo program (tím se vytvoří také asset). 
6. Publikujte událost (tím se vytvoří lokátor OnDemand pro přidružený asset).    
7. Jakmile budete připraveni začít streamovat a archivovat, spusťte událost.
8. Volitelně můžete dát kodéru pro kódování v reálném čase signál, aby spustil reklamu. Reklama bude vložena do výstupního proudu.
9. Kdykoli budete chtít zastavit streamování a archivaci události, zastavte událost.
10. Odstraňte událost (volitelně můžete odstranit i asset).   

## <a name="in-this-tutorial"></a>V tomto kurzu
V tomto kurzu budeme Azure Portal používat k provádění následujících úloh: 

1. Vytvoření kanálu, který má povolené kódování v reálném čase.
2. Získání ingestované adresy URL, která bude dodána kodéru pro kódování v reálném čase. Kodér pro kódování v reálném čase tuto adresu URL použije k ingestování datového proudu do kanálu.
3. Vytvoření události nebo programu (a assetu)
4. Publikování assetu a získání adresy URL pro streamování  
5. Přehrání obsahu
6. Čištění

## <a name="prerequisites"></a>Požadavky
K dokončení kurzu potřebujete následující:

* K dokončení tohoto kurzu potřebujete mít účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. 
  Podrobnosti najdete v tématu [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* Účet Media Services. Pokud chcete vytvořit účet Media Services, přečtěte si článek [Vytvoření účtu](media-services-portal-create-account.md).
* Webová kamera a kodér, který dokáže odesílat živý datový proud s jednou přenosovou rychlostí.

## <a name="create-a-channel"></a>Vytvoření kanálu
1. Na webu [Azure Portal](https://portal.azure.com/) vyberte Media Services a klikněte na název účtu Media Services.
2. Vyberte **Živé streamování**.
3. Vyberte **Vytvořit vlastní**. Tato možnost vám umožní vytvořit kanál, který má povolené kódování v reálném čase.
   
    ![Vytvoření kanálu](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel.png)
4. Klikněte na **Nastavení**.
   
   1. Jako typ kanálu zvolte **Live Encoding**. Tento typ určuje, že chcete vytvořit kanál, který má povolené kódování v reálném čase. To znamená, že příchozí datový proud s jednou přenosovou rychlostí se odesílá do kanálu a překóduje se na datový proud s více přenosovými rychlostmi pomocí určených nastavení kodéru pro kódování v reálném čase. Další informace najdete v článku [Živé streamování využívající službu Azure Media Services k vytvoření datových proudů s více přenosovými rychlostmi](media-services-manage-live-encoder-enabled-channels.md). Klikněte na tlačítko OK.
   2. Zadejte název kanálu.
   3. V dolní části obrazovky klikněte na OK.
5. Vyberte kartu **Ingestovat**.
   
   1. Na této stránce můžete vybrat protokol streamování. Pro kanál typu **Live Encoding** jsou platné tyto možnosti:
      
      * Fragmentovaný soubor MP4 s jednou přenosovou rychlostí (technologie Smooth Streaming)
      * RTMP s jednou přenosovou rychlostí
        
        Podrobné vysvětlení jednotlivých protokolů najdete v článku [Živé streamování využívající službu Azure Media Services k vytvoření datových proudů s více přenosovými rychlostmi](media-services-manage-live-encoder-enabled-channels.md).
        
        Možnost protokolu nelze změnit, pokud kanál nebo jeho přidružené události nebo programy právě běží. Pokud požadujete různé protokoly, vytvořte samostatné kanály pro každý protokol streamování.  
   2. Na ingestování můžete použít omezení IP adres. 
      
       Můžete definovat IP adresy, které mají v tomto kanálu povoleno ingestování videa. Povolené IP adresy můžete zadat buď jako jednu IP adresu (např. '10.0.0.1'), rozsah IP adres pomocí IP adresy a masky podsítě s technologií CIDR (např. '10.0.0.1/22') nebo jako rozsah IP adres pomocí IP adresy a masky podsítě zapsané jako čísla oddělená tečkami (např. '10.0.0.1(255.255.252.0)').
      
       Pokud žádné IP adresy nezadáte a nedefinujete žádné pravidlo, nebude povolená žádná IP adresa. Pokud chcete povolit libovolnou IP adresy, vytvořte pravidlo a nastavte 0.0.0.0/0.
6. Na kartě **Náhled** použijte na náhled omezení IP adres.
7. Na kartě **Kódování** zadejte předvolbu kódování. 
   
    Momentálně je dostupná jenom jedna možnost: **Výchozí 720 p**. Chcete-li zadat vlastní předvolbu, otevřete lístek podpory společnosti Microsoft. Poté zadejte název vytvořené předvolby. 

> [!NOTE]
> V současné době může spuštění kanálu trvat až 30 minut. Resetování kanálu může trvat až 5 minut.
> 
> 

Po vytvoření kanálu můžete kliknutím na kanál a výběrem **Nastavení** zobrazit konfigurace svých kanálů. 

Další informace najdete v článku [Živé streamování využívající službu Azure Media Services k vytvoření datových proudů s více přenosovými rychlostmi](media-services-manage-live-encoder-enabled-channels.md).

## <a name="get-ingest-urls"></a>Získání ingestovaných adres URL
Po vytvoření kanálu můžete získat ingestované adresy URL, které poskytnete kodéru pro kódování v reálném čase. Kodér tyto adresy URL používá ke vkládání živého proudu.

![ingesturls](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)

## <a name="create-and-manage-events"></a>Vytvoření a správa událostí
### <a name="overview"></a>Přehled
Kanál je přidružený k událostem a programům, které vám umožňují řídit publikování a ukládání segmentů v živém datovém proudu. Kanály spravují události nebo programy. Vztah kanálů a programů se velmi podobná tradičním médiím, kde kanál obsahuje nepřetržitý datový proud obsahu a program je vymezen na určité načasované události v tomto kanálu.

Nastavením délky **archivačního okna** můžete určit počet hodin, po který chcete uchovávat zaznamenaný obsah události. Tuto hodnotu můžete nastavit v rozmezí od 5 minut po 25 hodin. Délka archivačního okna také určuje maximální časový úsek, který můžou klienti prohledávat od aktuální živé pozice směrem zpět v čase. Programy můžou běžet po určenou dobu a obsah, který se do délky okna nevejde, bude vždy zahozen. Hodnota této vlastnosti také určuje, jak dlouho můžou růst manifesty klientů.

Každá událost je přidružena k assetu. Pokud chcete publikovat událost, musíte pro přidružený asset vytvořit lokátor OnDemand. Tento lokátor vám umožní sestavit adresu URL pro streamování, kterou potom poskytnete svým klientům.

Kanál podporuje až tři současně spuštěné programy, takže si můžete vytvořit několik archivů stejného příchozího datového proudu. To vám umožní podle potřeby publikovat a archivovat různé části události. Požadavek vaší firmy může být například takový, že chcete archivovat 6 hodin události, ale vysílat pouze posledních 10 minut. K tomu potřebujete vytvořit dvě současně spuštěné události. Jednu událost nastavíte, aby archivovala 6 hodin události, ale tento program nebudete publikován. Druhou událost nastavíte, aby archivovala 10 minut a tento program budete publikovat.

Existující programy nepoužívejte znovu pro nové události. Místo toho vytvořte a spusťte nový program pro každou jednotlivou událost.

Jakmile budete připraveni začít streamovat a archivovat, spusťte událost nebo program. Kdykoli budete chtít zastavit streamování a archivaci události, zastavte událost. 

Pokud chcete archivovaný obsah odstranit, zastavte a odstraňte událost a potom odstraňte přidružený asset. Asset nemůžete odstranit, pokud ho událost používá. Nejdříve je nutné odstranit událost. 

I po zastavení a odstranění události můžou uživatelé streamovat archivovaný obsah jako video na vyžádání, a to tak dlouho, dokud asset neodstraníte.

Pokud chcete archivovaný obsah zachovat, ale nechcete ho zpřístupňovat pro streamování, odstraňte lokátor streamování.

### <a name="createstartstop-events"></a>Vytvoření, spuštění a zastavení událostí
Jakmile datový proud plyne do kanálu, můžete událost streamování zahájit tím, že vytvoříte asset, program a lokátor streamování. Datový proud se tak archivuje a zpřístupní se divákům prostřednictvím koncového bodu streamování. 

>[!NOTE]
>Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno**. Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **Spuštěno**. 

Událost můžete spustit dvěma způsoby: 

1. Na stránce **Kanál** stisknutím **Živá událost** přidejte novou událost.
   
    Zadejte název události, název assetu, archivační okno a možnost šifrování.
   
    ![createprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)
   
    Pokud jste nechali zaškrtnuté políčko **Publikovat událost nyní**, budou vytvořeny ADRESY URL PRO PUBLIKOVÁNÍ události.
   
    Kdykoli budete připraveni událost streamovat, stiskněte **Spustit**.
   
    Po spuštění události můžete stisknout **Přehrát** a spustit přehrávání obsahu.
2. Případně můžete použít zástupce a na stránce **Kanál** stisknout tlačítko **Spustit streamování**. Tím vytvoříte výchozí asset, program a lokátor streamování.
   
    Událost získá název **default** a archivační okno bude nastaveno na 8 hodin.

Publikovanou událost můžete sledovat na stránce **Živá událost**. 

Pokud kliknete na tlačítko **Zrušit streamování**, zastaví se všechny živé události. 

## <a name="watch-the-event"></a>Sledování události
Pokud chcete sledovat událost, klikněte na tlačítko **Sledovat** na webu Azure Portal nebo zkopírujte adresu URL streamování a použijte přehrávač dle svého výběru. 

![Vytvořeno](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-play-event.png)

Při zastavení se živá událost automaticky převede na obsah na vyžádání.

## <a name="clean-up"></a>Vyčištění
Pokud jste dokončili streamování událostí a chcete dříve zřízené prostředky vyčistit, postupujte podle následujícího návodu.

* Zastavte odesílání datového proudu z kodéru.
* Zastavte kanál. Zastavený kanál zastaví narůstání poplatků. Když ho budete potřebovat znovu spustit, budete mít stejnou ingestovanou adresu URL, takže nebude nutné kodér znovu konfigurovat.
* Pokud nechcete pokračovat v poskytování archivu živé události ve formě datového proudu na vyžádání, můžete koncový bod streamování zastavit. Pokud je kanál v zastaveném stavu, nebudou vám narůstat poplatky.

## <a name="view-archived-content"></a>Zobrazení archivovaného obsahu
I po zastavení a odstranění události můžou uživatelé streamovat archivovaný obsah jako video na vyžádání, a to tak dlouho, dokud asset neodstraníte. Asset nemůžete odstranit, pokud ho událost používá. Nejdřív odstraňte událost. 

Chcete-li spravovat prostředky, vyberte **Nastavení** a klikněte na **Prostředky**.

![Prostředky](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-assets.png)

## <a name="considerations"></a>Požadavky
* V současné době doporučujeme maximální dobu trvání živé události v délce 8 hodin. Pokud potřebujete, aby kanál běžel delší dobu, kontaktujte nás na adrese amslived@microsoft.com.
* Zkontrolujte, že koncový bod streamování, ze kterého chcete streamovat obsah, je ve stavu **Spuštěno**.

## <a name="next-step"></a>Další krok
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

