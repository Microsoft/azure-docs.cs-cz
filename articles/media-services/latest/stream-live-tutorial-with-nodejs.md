---
title: Živé streamování s Media Services V3 Node.js
titleSuffix: Azure Media Services
description: Naučte se streamovat živě pomocí Node.js.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc, devx-track-nodejs
ms.date: 04/15/2021
ms.author: inhenkel
ms.openlocfilehash: 749d2fc845f036a2802c80c161b3fc8c171c2555
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730528"
---
# <a name="tutorial-stream-live-with-media-services-using-nodejs-and-typescript"></a>Kurz: živé streamování pomocí Media Services s využitím Node.js a TypeScriptu

> [!NOTE]
> I když kurz používá Node.js příklady, jsou obecné kroky stejné pro [REST API](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event)nebo jiné podporované sady [SDK](media-services-apis-overview.md#sdks). 

V Azure Media Services za zpracování obsahu živého streamování odpovídají [živé události](/rest/api/media/liveevents) . Živá událost poskytuje vstupní koncový bod (adresa URL pro příjem), který pak poskytnete pro živý kodér. Živá událost přijímá živé vstupní datové proudy z kodéru Live a zpřístupňuje je pro streamování prostřednictvím jednoho nebo více [koncových bodů streamování](/rest/api/media/streamingendpoints). Živé události také poskytují koncový bod verze Preview (adresa URL náhledu), který používáte k zobrazení náhledu a ověření datového proudu před dalším zpracováním a doručením. V tomto kurzu se dozvíte, jak použít Node.js k vytvoření **průchozího** typu živé události a vysílání živého datového proudu pomocí [OBS studia](https://obsproject.com/download).

V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Stáhněte si vzorový kód popsaný v tématu.
> * Projděte si kód, který nakonfiguruje a provede živé streamování.
> * Sledujte událost s [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) v [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net) .
> * Vyčistěte prostředky.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení kurzu potřebujete následující položky:

- Nainstalovat [Node.js](https://nodejs.org/en/download/)
- Nainstalovat [TypeScript](https://www.typescriptlang.org/)
- [Vytvořte účet Media Services](./create-account-howto.md).<br/>Nezapomeňte si pamatovat hodnoty, které jste použili pro název skupiny prostředků a název účtu Media Services.
- Postupujte podle kroků v [části přístup k rozhraní API Azure Media Services pomocí Azure CLI](./access-api-howto.md) a přihlašovací údaje uložte. Budete je muset použít k přístupu k rozhraní API a ke konfiguraci souboru proměnných prostředí.
- Projděte si článek [Konfigurace a připojení pomocí Node.js](./configure-connect-nodejs-howto.md) , jak nejprve porozumět tomu, jak používat Node.js klientské sady SDK.
- Nainstalujte Visual Studio Code nebo Visual Studio.
- [Nastavte prostředí Visual Studio Code](https://code.visualstudio.com/Docs/languages/typescript) pro podporu jazyka TypeScript.

## <a name="additional-settings-for-live-streaming-software"></a>Další nastavení pro živý software streamování

- Fotoaparát nebo zařízení (jako laptop), které se používá k vysílání události.
- Místní softwarový kodér, který kóduje datový proud kamery a odesílá ho do Media Services služby živého streamování pomocí protokolu RTMP, najdete v tématu [Doporučené místní živé kodéry](encode-recommended-on-premises-live-encoders.md). Datový proud musí být ve formátu **RTMP** nebo **Smooth Streaming**.  
- V této ukázce se doporučuje začít se softwarovým kodérem, jako je bezplatný [software pro vysílání Open OBS Studio](https://obsproject.com/download) , aby bylo snadné začít.

V této ukázce se předpokládá, že budete pomocí OBS studia vysílat RTMP do koncového bodu ingestování. Nejdřív nainstalujte OBS Studio.
Použijte následující nastavení kódování v OBS studiu:

- Kodér: NVIDIA NVENC (Pokud je k dispozici) nebo x264
- Řízení sazeb: CBR
- Přenosová rychlost: 2500 kb/s (nebo něco přijatelného pro váš přenosný počítač)
- Interval klíčového snímku: 2 s nebo 1 s pro nízkou latenci  
- Přednastavení: kvalita nebo výkon s nízkou latencí (NVENC) nebo "veryfast" pomocí x264
- Profil: vysoká
- GPU: 0 (auto)
- Max B – snímky: 2

> [!TIP]
> Než budete pokračovat, přečtěte si téma [Živé streamování s Media Services v3](stream-live-streaming-concept.md).

## <a name="download-and-configure-the-sample"></a>Stažení a konfigurace ukázky

Naklonujte následující úložiště centra Git, které obsahuje ukázku Node.js živého streamování pro váš počítač pomocí následujícího příkazu:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Ukázku živého streamování najdete ve složce [Live](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live) (Živé).

Ve složce [AMSv3Samples](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples) zkopírujte soubor s názvem Sample. env do nového souboru s názvem. ENV a uložte nastavení proměnné prostředí, které jste shromáždili v článku [přístup k rozhraní API Azure Media Services pomocí Azure CLI](./access-api-howto.md).
Ujistěte se, že soubor obsahuje "tečka" (.) před. env ", aby to fungovalo správně s ukázkou kódu.

[Soubor. env](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/sample.env) obsahuje klíč aplikace AAD a tajný kód spolu s informacemi o názvu účtu a předplatném, které jsou potřebné k ověření přístupu sady SDK k vašemu účtu Media Services. Soubor. gitignore je již nakonfigurován tak, aby zabránil publikování tohoto souboru do rozvětvené úložiště. Nepovolujte vrácení těchto přihlašovacích údajů, protože jsou důležitými tajnými kódy pro váš účet.

> [!IMPORTANT]
> Tato ukázka používá pro každý prostředek jedinečnou příponu. Pokud ladění zrušíte nebo ukončíte bez spuštění aplikace přes, skončíte s několika živými událostmi ve vašem účtu. <br/>Nezapomeňte zastavit běžící živé události. V opačném případě se vám bude **účtovat**! Spusťte program celým způsobem až do dokončení automatického čištění prostředků. Pokud dojde k chybě programu nebo nechtěně zastavíte ladicí program a dojde k přerušení provádění programu, měli byste na portálu dvakrát zkontrolovat, zda jste neopustili žádné živé události v běžícím nebo úsporném stavu, které by způsobily nepotřebné fakturační poplatky.

## <a name="examine-the-typescript-code-for-live-streaming"></a>Projděte si kód TypeScriptu pro živé streamování

V této části jsou zkontrolovány funkce definované v souboru [index. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/Live/index.ts) v rámci *aktivního* projektu.

Ukázka vytvoří jedinečnou příponu pro každý prostředek, aby nedošlo ke kolizi názvů, pokud spustíte ukázku několikrát bez vyčištění.

### <a name="start-using-media-services-sdk-for-nodejs-with-typescript"></a>Začínáme používat sadu Media Services SDK pro Node.js s TypeScript

Pokud chcete začít používat Media Services rozhraní API s Node.js, musíte nejdřív přidat [@azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices) modul SDK pomocí Správce balíčků npm.

```bash
npm install @azure/arm-mediaservices
```

V package.jszapnuto, je to pro vás již nakonfigurováno, takže stačí spustit *instalaci npm* a načíst moduly a závislosti.

1. Otevřete **příkazový řádek** a přejděte do adresáře ukázky.
1. Změňte adresář na složku AMSv3Samples.

    ```bash
    cd AMSv3Samples
    ```

1. Nainstaluje balíčky používané v *packages.js* souboru.

    ```bash
    npm install 
    ```

1. Spusťte Visual Studio Code ze složky *AMSv3Samples* . (To se vyžaduje pro spuštění ze složky, kde se nachází složka *. VSCode* a *tsconfig.jsv* souborech)

    ```bash
    cd ..
    code .
    ```

Otevřete složku pro *Live* a otevřete soubor *index. ts* v editoru Visual Studio Code.
V souboru *index. TS* stisknutím klávesy F5 spusťte ladicí program.

### <a name="create-the-media-services-client"></a>Vytvoření klienta Media Services

Následující fragment kódu ukazuje, jak vytvořit klienta Media Services v Node.js.
Všimněte si, že v tomto kódu nejdřív nastavujeme vlastnost **LongRunningOperationRetryTimeout** AzureMediaServicesOptions na 2 sekundy, aby se snížil čas potřebný k dotazování na stav dlouhotrvající operace na koncovém bodu správy prostředků Azure.  Vzhledem k tomu, že většina operací s dynamickými událostmi bude asynchronní a její dokončení může nějakou dobu trvat, měli byste tento interval dotazování v sadě SDK snížit z výchozí hodnoty 30 sekund, abyste urychlili čas potřebný k dokončení hlavních operací, jako je vytváření živých událostí, spouštění a zastavování, což jsou všechna asynchronní volání. Dvě sekundy jsou doporučenou hodnotou pro většinu scénářů použití.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Vytvoření živé události

V této části se dozvíte, jak vytvořit **průchozí** typ živé události (LiveEventEncodingType set to None). Další informace o dalších dostupných typech živých událostí najdete v tématu [typy událostí typu Live](live-event-outputs-concept.md#live-event-types). Kromě předávacího typu můžete pro cloudové kódování 720P nebo 1080P s adaptivní přenosovou rychlostí použít živý překódování živé události.
 
Některé věci, které byste mohli chtít zadat při vytváření živé události:

* Protokol ingestování pro živou událost (aktuálně jsou podporovány protokoly RTMP a Smooth Streaming).<br/>Možnost protokolu se nedá změnit, když je spuštěná živá událost nebo její přidružený živý výstup. Pokud požadujete různé protokoly, vytvořte samostatnou živou událost pro každý protokol streamování.  
* Omezení IP adres u ingestování a náhledu. Můžete definovat IP adresy, které můžou ingestovat video do této živé události. Povolené IP adresy se dají zadat jako jedna IP adresa (třeba 10.0.0.1), rozsah IP adres pomocí IP adresy a masky podsítě CIDR (třeba 10.0.0.1/22) nebo rozsah IP adres a maska podsítě v desítkovém zápisu s tečkou (třeba 10.0.0.1(255.255.252.0)).<br/>Pokud nejsou zadané žádné IP adresy a není k dispozici žádná definice pravidla, nebude povolena žádná IP adresa. Pokud chcete povolit libovolnou IP adresy, vytvořte pravidlo a nastavte 0.0.0.0/0.<br/>IP adresy musí být v jednom z následujících formátů: IpV4 adresa se čtyřmi čísly nebo rozsahem adres CIDR.
* Při vytváření události můžete zadat automatické spuštění. <br/>Pokud je vlastnost autostart nastavena na hodnotu true, spustí se po vytvoření živá událost. To znamená, že se fakturace začne ihned po spuštění živé události. Chcete-li zastavit další fakturaci, je nutné explicitně volat stop u prostředku živé události. Další informace najdete v tématu [stavy událostí Live a fakturace](live-event-states-billing-concept.md).
K dispozici jsou také pohotovostní režimy, ve kterých je možné spustit živou událost ve stavu přiděleno nižší náklady, což urychluje přesun do spuštěného stavu. To je užitečné v situacích, jako jsou horké fondy, které potřebují rychle vysílat kanály pro streamování.
* Aby adresa URL pro ingestování mohla být prediktivní a snazší pro údržbu v rámci živého kodéru založeného na hardwaru, nastavte vlastnost "useStaticHostname" na hodnotu true a použijte vlastní jedinečný identifikátor GUID v "accessToken". Podrobné informace najdete v tématu [adresy URL pro příjem živých událostí](live-event-outputs-concept.md#live-event-ingest-urls).

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveEvent)]

### <a name="create-an-asset-to-record-and-archive-the-live-event"></a>Vytvoření Assetu pro záznam a archivaci živé události

V tomto bloku kódu vytvoříte prázdný Asset, který bude použit jako "páska" pro záznam živého archivu událostí do.
Při učení těchto konceptů je nejlepší si představit, že se jedná o objekt Assetu jako páska, kterou byste vložili do záznamu videa na videokazety během starých dnů. Živý výstup je počítač pro záznam pásky. Živá událost je pouze signál videa, který přichází do zadní části počítače.

Mějte na paměti, že Asset nebo páska se dá kdykoli vytvořit. Je to jen prázdný prostředek "Asset", který budete mít k dispozici pro živý výstupní objekt, záznam pásky v tomto analogovém formátu.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateAsset)]

### <a name="create-the-live-output"></a>Vytvořit živý výstup

V této části vytvoříme živý výstup, který jako vstup používá název Assetu, abychom zjistili, kam se má zaznamenat živá událost. Kromě toho nastavíme okno časového posunutí (DVR), které se použije v záznamu.
Vzorový kód ukazuje, jak nastavit časový interval posunutí 1 hodiny. To umožní klientům přejít zpátky za poslední hodinu události.  Kromě toho v archivu zůstane jenom poslední 1 hodina živé události. V případě potřeby ho můžete prodloužit o až 25 hodin.  Všimněte si také, že je možné řídit vytváření názvů výstupních manifestů použitých HLS a SPOJOVNÍKy v cestách URL při publikování.

Živý výstup nebo "záznam pásky" v našem analogii lze vytvořit také kdykoli. To znamená, že můžete před zahájením toku signálu vytvořit živý výstup. Pokud potřebujete zrychlit věci, je často užitečné ji vytvořit před zahájením toku signálu.

Živé výstupy začínají při vytváření a při odstranění se zastaví.  Když odstraníte živý výstup, neodstraníte základní Asset nebo obsah v assetu. Můžete si ho představit jako vysunutí pásky. Asset se záznamem bude poslední, jak budete chtít a kdy se má vysunout (tzn. když se živý výstup odstraní), bude k dispozici okamžitě pro zobrazení na vyžádání.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveOutput)]


### <a name="get-ingest-urls"></a>Získání ingestovaných adres URL

Jakmile se vytvoří živá událost, můžete získat adresy URL pro ingestování, které poskytnete kodéru Live. Kodér používá tyto adresy URL ke vstupu do živého datového proudu pomocí protokolu RTMP.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetIngestURL)]

### <a name="get-the-preview-url"></a>Získání adresy URL náhledu

Použijte previewEndpoint a vytvořte náhled a ověřte, že se skutečně přijímá vstup z kodéru.

> [!IMPORTANT]
> Než budete pokračovat, ujistěte se, že video přetéká do adresy URL náhledu.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetPreviewURL)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Vytváření a Správa živých událostí a živých výstupů

Po spuštění streamování do živé události můžete událost streamování zahájit publikováním lokátoru streamování pro vaše klientské přehrávače, které chcete používat. Díky tomu budou čtenáři k dispozici uživatelům přes koncový bod streamování.

Nejdřív vytvoříte signál vytvořením "živá událost".  Signál nepokračuje, dokud nespustíte tuto živou událost a připojíte kodér ke vstupu.

Pokud chcete zastavit záznam pásky, můžete na LiveOutput zavolat odstranit. Tím se ve skutečnosti neodstraní **obsah** vašeho archivu na pásce "Asset", odstraní se jenom "záznam o pásce" a zastaví se archivace. Asset se vždycky udržuje s archivovaným obsahem videa, dokud na samotném prostředku nebudete explicitně volat metodu DELETE. Jakmile liveOutput odstraníte, bude zaznamenaný obsah prostředku stále k dispozici pro přehrání všech již publikovaných adres URL lokátoru streamování. Pokud chcete, aby zákazník mohl přehrajte archivovaný obsah, musíte nejdřív odebrat všechny Lokátory z assetu a také vyprázdnit mezipaměť CDN na cestě URL, pokud používáte CDN pro doručování. V opačném případě bude obsah živý v mezipaměti CDN pro standardní nastavení Time to Live pro CDN (což může být až 72 hodin.)

#### <a name="create-a-streaming-locator-to-publish-hls-and-dash-manifests"></a>Vytvoření lokátoru streamování pro publikování HLSch a SPOJOVNÍKových manifestů

> [!NOTE]
> Po vytvoření účtu Media Services se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno** . Pokud chcete spustit streamování vašeho obsahu a využít výhod [dynamického balení](encode-dynamic-packaging-concept.md) a dynamického šifrování, musí koncový bod streamování, ze kterého chcete streamovat obsah, být ve stavu **spuštěno** .

Když publikujete prostředek pomocí lokátoru streamování, bude se dál zobrazovat živá událost (až do délky okna DVR), dokud nevyprší platnost nebo odstranění lokátoru streamování, podle toho, co nastane dřív. Tímto způsobem zpřístupníte virtuální "pásku", která je k dispozici pro vaši cílovou skupinu pro zobrazení v reálném čase a na vyžádání. Stejná adresa URL se dá použít ke sledování živé události, okna DVR nebo assetu na vyžádání po dokončení nahrávání (když se odstraní živý výstup).

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateStreamingLocator)]

#### <a name="build-the-paths-to-the-hls-and-dash-manifests"></a>Sestavení cest k manifestům HLS a SPOJOVNÍKům

Metoda BuildManifestPaths v ukázce ukazuje, jak deterministickém způsobem vytvořit cesty streamování pro použití PŘERUŠOVANého nebo HLSho doručení do různých klientů a rozhraní přehrávače.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#BuildManifestPaths)]

## <a name="watch-the-event"></a>Sledování události

Pokud chcete sledovat událost, zkopírujte adresu URL streamování, kterou jste dostali, když jste spustili kód popsaný v tématu Vytvoření lokátoru streamování. Můžete použít přehrávač médií podle vašeho výběru. [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) je k dispozici pro otestování streamu na https://ampdemo.azureedge.net .

Živá událost automaticky převádí události na obsah na vyžádání při zastavení. I po zastavení a odstranění události můžou uživatelé streamovat archivovaný obsah jako video na vyžádání, pokud prostředek neodstraníte. Prostředek se nedá odstranit, pokud ho používá událost. nejdříve je nutné odstranit událost.

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Vyčištění zdrojů v účtu služby Media Services

Pokud aplikaci spouštíte po celém začátku, automaticky se vyčistí všechny prostředky, které se používají ve funkci nazvané "cleanUpResources". Ujistěte se, že aplikace nebo ladicí program spouští celý způsob, jak docílit, nebo pokud nemůžete neměnit prostředky a ukončit spouštění živých událostí ve vašem účtu. Dvojitým vrácením Azure Portal potvrďte, že se všechny prostředky ve vašem účtu Media Services vyčistily.  

V ukázkovém kódu naleznete podrobnosti v metodě **cleanUpResources** .

> [!IMPORTANT]
> Ponechávání živých událostí při provozu se účtují náklady na fakturaci. Mějte na paměti, že pokud dojde k chybě projektu nebo programu nebo z jakéhokoli důvodu dojde k jeho zavření, může dojít k tomu, že živá událost běží ve stavu fakturace.

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Další dokumentaci pro vývojáře k Node.js v Azure

- [Azure pro JavaScript & Node.js vývojáři](/azure/developer/javascript/)
- [Media Services zdrojový kód v @azure/azure-sdk-for-js úložišti centra Git](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Dokumentace k balíčku Azure pro vývojáře Node.js](/javascript/api/overview/azure/)


## <a name="next-steps"></a>Další kroky

[Streamování souborů](stream-files-tutorial-with-api.md)