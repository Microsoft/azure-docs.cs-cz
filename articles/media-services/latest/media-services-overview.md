---
title: Azure Media Services V3 přehled: Azure Media Services popis: podrobný přehled Azure Media Services V3 s odkazy na rychlé starty, kurzy a ukázky kódu.
služby: Media-Services documentationcenter: na Author: IngridAtMicrosoft Manager: femila Editor: ' ' Tags: ' ' klíčová slova: Azure Media Services, Stream, Broadcast, Live, offline

MS. Service: Media-Services MS. devlang: více MS. téma: Přehled ms.tgt_pltfrm: více MS. úlohy: Media MS. Date: 3/10/2021 MS. Author: inhenkel MS. Custom: MVC
#<a name="customer-intent-as-a-developer-or-a-content-provider-i-want-to-encode-stream-on-demand-or-live-analyze-my-media-content-so-that-my-customers-can-view-the-content-on-a-wide-variety-of-browsers-and-devices-gain-valuable-insights-from-recorded-content"></a>Záměr zákazníka: jako vývojář nebo poskytovatel obsahu chci kódovat, streamovat (na vyžádání nebo živě), analyzovat multimediální obsah tak, aby moji zákazníci mohli: zobrazit obsah v nejrůznějších prohlížečích a zařízeních, získat cenné poznatky z zaznamenaného obsahu.
---

# <a name="azure-media-services-v3-overview"></a>Přehled služby Azure Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services je cloudová platforma, která umožňuje vytvářet řešení zajišťující streamování videa ve vysílací kvalitě, vylepšovat dostupnost a distribuci, analyzovat obsah a nejenom to. Bez ohledu na to, jestli jste vývojář aplikace, centrum volání, státní úřad nebo zábava, Media Services vám pomůže vytvářet aplikace, které dodávají mediální prostředí vynikající kvality pro velké cílové skupiny na nejoblíbenějších mobilních zařízeních a prohlížečích.

Sady SDK Media Services V3 jsou založené na [Media Services V3 openapi Specification (Swagger)](https://aka.ms/ams-v3-rest-sdk).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Dodržování předpisů, ochrana osobních údajů a zabezpečení

Důležitou připomínkou je, že je nutné dodržovat všechny použitelné zákony v používání Azure Media Services a nesmíte používat Media Services ani žádnou službu Azure způsobem, který porušuje práva ostatních nebo kteří můžou být pro ostatní škodlivé.

Před nahráním videa nebo obrázku do Media Services musíte mít všechna správná práva k používání videa nebo obrázku, včetně, pokud to vyžaduje zákon, všech nezbytných souhlasů od jednotlivců (pokud existují) na videu nebo obrázku, pro použití, zpracování a ukládání svých dat v Media Services a Azure. Některé jurisdikce můžou u této kolekce stanovit zvláštní zákonné požadavky, online zpracování a ukládání určitých kategorií dat, jako je biometriková data. Než začnete používat Media Services a Azure pro zpracování a ukládání jakýchkoli dat, která se vztahují na zvláštní zákonné požadavky, musíte zajistit dodržování předpisů u všech právních požadavků, které se na vás můžou vztahovat.

Informace o dodržování předpisů, ochraně osobních údajů a zabezpečení v Media Services najdete na webu Microsoft [Trust Center](https://www.microsoft.com/trust-center/?rtc=1). Pro závazky společnosti Microsoft, postupy pro zpracování a uchovávání dat, včetně toho, jak odstranit vaše data, přečtěte si [prohlášení o zásadách ochrany osobních údajů](https://privacy.microsoft.com/PrivacyStatement)od Microsoftu, [podmínky používání služeb Online Services](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") a [doplněk pro zpracování dat](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) (DPA). Pomocí Media Services souhlasíte s tím, že DPA a prohlášením o zásadách ochrany osobních údajů.
 
## <a name="what-can-i-do-with-media-services"></a>Co všechno jde dělat v Media Services?

Media Services umožňuje vytvářet různé pracovní postupy pro média v cloudu. Mezi příklady toho, co můžete s Media Services provádět, patří:

* Vytvářet videa v různých formátech pro přehrávání v široké škále prohlížečů a zařízení. V případě doručování na vyžádání a živého streamování do různých klientů (mobilní zařízení, televize, počítač atd.) je potřeba správně kódovat a zabalit obsah videa a zvuku. Jak takový obsah dodávat a streamovat, se dozvíte v článku [Rychlý start: kódování a streamování souborů](stream-files-dotnet-quickstart.md).
* Streamujte živé sportovní události na velkou online cílovou skupinu, jako je fotbal, baseball, školní a vysoký školní Sport a další.
* Vysílat veřejné schůzky a události, jako je městská sály, schůze města a právní orgány.
* Analyzovat zaznamenaný videoobsah a zvukový obsah. Pro větší komfort při sledování může například organizace u svého obsahu převést řeč na text nebo vytvořit index vyhledávání a řídicí panel. Může také analyzovat nejčastější témata stížností, jejich zdroje a další související data.
* Vytvořit službu odběru videa a streamovat obsah chráněný technologií DRM, pokud zákazník (například filmové studio) potřebuje omezit přístup a pracuje s díly chráněnými autorským zákonem.
* Poskytovat offline obsah pro přehrávání v letadlech, vlacích a automobilech. Zákazník si může obsah stáhnout do svého telefonu nebo tabletu pro pozdější přehrání, pokud očekává nedostupnost sítě.
* Implementujte výukovou platformu pro vzdělávací e-Learning s využitím Azure Media Services a [Azure rozhraní API služeb Cognitive Services](../../index.yml?pivot=products&panel=ai) pro titulkování na text, překladu na více jazyků a tak dále.
* Pomocí Azure Media Services společně se službou [Azure rozhraní API služeb Cognitive Services](../../index.yml?pivot=products&panel=ai) můžete přidat titulky a titulky do videí do služby stravování pro širší cílovou skupinu (například lidem s postižením sluchu nebo lidem, kteří chtějí načítat v jiném jazyce).
* Umožněte Azure CDN dosáhnout velkého měřítka, aby bylo možné lépe zvládnout okamžité vysoké zátěže (například začátek události spuštění produktu).

## <a name="how-can-i-get-started-with-v3"></a>Jak začít s v3?

Naučte se kódovat a zabalit obsah, streamovat videa na vyžádání, živě vysílat a analyzovat vaše videa pomocí Media Services V3. Kurzy, reference k rozhraní API a další dokumentace ukazují, jak bezpečně doručovat živé video a video na vyžádání nebo zvukový stream, který se škáluje milionům uživatelů.

> [!TIP]
> Než začnete s vývojem, Projděte si téma: [základní koncepty](concepts-overview.md) , které zahrnují důležité koncepty, jako je balení, kódování a ochrana, a [vývoj s rozhraními API Media Services V3](media-services-apis-overview.md) , která obsahují informace o přístupu k rozhraním API, konvencím pojmenování atd.

### <a name="sdks"></a>Sady SDK

Začněte vyvíjet pomocí [sad SDK klienta Azure Media Services V3](media-services-apis-overview.md#sdks).

### <a name="quickstarts"></a>Rychlé starty  

V rychlých startech se při rychlém vyzkoušení Media Services v nových zákaznících zobrazují základní pokyny od dne 1.

* [Streamování videosouborů – .NET](stream-files-dotnet-quickstart.md)
* [Streamování souborů videa – CLI](stream-files-cli-quickstart.md)
* [Streamování videosouborů – Node.js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>Kurzy

Kurzy ukazují postupy založené na scénářích pro některé z hlavních Media Services úloh.

* [Kódování vzdáleného souboru a streamování videa – REST](stream-files-tutorial-with-rest.md)
* [Kódování načteného souboru a streamování videa – .NET](stream-files-tutorial-with-api.md)
* [Živé streamování – .NET](stream-live-tutorial-with-api.md)
* [Analýza videa – .NET](analyze-videos-tutorial.md)
* [Dynamické šifrování AES-128 – .NET](drm-playready-license-template-concept.md)

### <a name="samples"></a>ukázky

Pomocí [tohoto prohlížeče ukázek](/samples/browse/?products=azure-media-services) můžete procházet Azure Media Services ukázky kódu.

### <a name="how-to-guides"></a>Návody

Návody obsahují ukázky kódu, které demonstrují, jak dokončit úlohu. V této části najdete spoustu příkladů. Tady je několik z nich:

* [Vytvoření účtu – rozhraní příkazového řádku](./account-create-how-to.md)
* [Přístup k rozhraním API – rozhraní příkazového řádku](./access-api-howto.md)
* [Kódování pomocí protokolu HTTPS jako vstup úlohy – .NET](job-input-from-http-how-to.md)  
* [Monitorování událostí – portál](monitoring/monitor-events-portal-how-to.md)
* [Dynamické šifrování pomocí více DRM – technologie .NET](drm-protect-with-drm-tutorial.md) 
* [Postup kódování pomocí vlastního transformačního rozhraní příkazového řádku](encode-custom-preset-cli-how-to.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Položte otázky, sdělte nám svůj názor, Získejte aktualizace.

Podívejte se na článek o [komunitě Azure Media Services](media-services-community.md) a podívejte se na různé způsoby, jak můžete klást otázky, sdělit svůj názor a získávat aktualizace Media Services.

## <a name="next-steps"></a>Další kroky

[Seznamte se se základními koncepty](concepts-overview.md)
