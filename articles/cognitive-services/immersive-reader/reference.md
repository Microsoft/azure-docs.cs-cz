---
title: Referenční dokumentace sady pro moderní čtečku
titleSuffix: Azure Cognitive Services
description: Sada moderní čtečka SDK obsahuje knihovnu JavaScriptu, která umožňuje integrovat moderní čtečku do vaší aplikace.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 73322cdee151969e6e765690284bbffc1c871f4e
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90090189"
---
# <a name="immersive-reader-javascript-sdk-reference-v11"></a>Referenční dokumentace sady pro moderní čtečku JavaScript SDK (v 1.1)

Sada moderní čtečka SDK obsahuje knihovnu JavaScriptu, která umožňuje integrovat moderní čtečku do vaší aplikace.

## <a name="functions"></a>Functions

Sada SDK zpřístupňuje funkce:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

<br>

## <a name="launchasync"></a>launchAsync

Spustí moderní čtečku v rámci `iframe` ve vaší webové aplikaci. Všimněte si, že velikost vašeho obsahu je omezená na maximálně 50 MB.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

#### <a name="launchasync-parameters"></a>Parametry launchAsync

| Název | Typ | Description |
| ---- | ---- |------------ |
| `token` | řetězec | Ověřovací token Azure AD. Další podrobnosti najdete v tématu [Postup vytvoření prostředku pro moderní čtečku](./how-to-create-immersive-reader.md) . |
| `subdomain` | řetězec | Vlastní subdoména prostředku pro moderní čtečku v Azure. Další podrobnosti najdete v tématu [Postup vytvoření prostředku pro moderní čtečku](./how-to-create-immersive-reader.md) . |
| `content` | [Obsah](#content) | Objekt obsahující obsah, který se má zobrazit v moderní čtečce. |
| `options` | [Možnosti](#options) | Možnosti pro konfiguraci určitého chování moderního čtecího zařízení. Nepovinný parametr. |

#### <a name="returns"></a>Návraty

Vrátí `Promise<LaunchResponse>` , který se vyřeší, když se nahraje moderní čtečka. `Promise`Překládá na [`LaunchResponse`](#launchresponse) objekt.

#### <a name="exceptions"></a>Výjimky

Vrácený `Promise` objekt se odmítne s [`Error`](#error) objektem, pokud se nepovede načíst moderní čtecí zařízení. Další informace najdete v tématu [kódy chyb](#error-codes).

<br>

## <a name="close"></a>close

Zavře moderní čtečku.

Příkladem případu použití této funkce je, že je tlačítko Ukončit skryto nastavením ```hideExitButton: true``` v [Možnosti](#options). Pak jiné tlačítko (například šipky zpět pro mobilní hlavičku) může zavolat tuto ```close``` funkci, když na ni kliknete.

```typescript
close(): void;
```

<br>

## <a name="immersive-reader-launch-button"></a>Tlačítko pro spuštění moderního čtecího zařízení

Sada SDK poskytuje výchozí styl pro tlačítko pro spuštění moderního čtecího zařízení. `immersive-reader-button`Pro povolení tohoto stylu použijte atribut class. Další podrobnosti najdete v tématu [Postup přizpůsobení tlačítka pro moderní čtečku](./how-to-customize-launch-button.md) .

```html
<div class='immersive-reader-button'></div>
```

#### <a name="optional-attributes"></a>Volitelné atributy

Pomocí následujících atributů můžete nakonfigurovat vzhled a chování tlačítka.

| Atribut | Popis |
| --------- | ----------- |
| `data-button-style` | Nastaví styl tlačítka. Může být `icon` , `text` , nebo `iconAndText` . Výchozí hodnota je `icon` . |
| `data-locale` | Nastaví národní prostředí. Příkladem je `en-US` nebo `fr-FR`. Výchozí hodnota je angličtina `en` . |
| `data-icon-px-size` | Nastaví velikost ikony v pixelech. Výchozí hodnota je 20px. |

<br>

## <a name="renderbuttons"></a>renderButtons

Tato ```renderButtons``` funkce není nutná, pokud používáte postup přizpůsobení doprovodných pokynů [pro tlačítko pro moderní čtečku](./how-to-customize-launch-button.md) .

Tato funkce styly a aktualizuje prvky tlačítka pro moderní čtečku dokumentu. Pokud ```options.elements``` je k dispozici, pak se tlačítka vykreslí v rámci každého prvku, který je součástí ```options.elements``` . Použití ```options.elements``` parametru je užitečné v případě, že máte v dokumentu více oddílů, na kterých se má spustit moderní čtečka, a chcete, aby bylo možné vykreslit více tlačítek se stejným stylem, nebo chcete vykreslit tlačítka pomocí jednoduchého a konzistentního vzoru návrhu. Chcete-li použít tuto funkci s parametrem [renderButtons Options](#renderbuttons-options) , zavolejte ```ImmersiveReader.renderButtons(options: RenderButtonsOptions);``` na načtení stránky, jak je znázorněno v následujícím fragmentu kódu. V opačném případě se tlačítka vykreslí v rámci prvků dokumentu, které mají třídu ```immersive-reader-button``` , jak je znázorněno v [postupu přizpůsobení tlačítka pro moderní čtečku](./how-to-customize-launch-button.md) .

```typescript
// This snippet assumes there are two empty div elements in
// the page HTML, button1 and button2.
const btn1: HTMLDivElement = document.getElementById('button1');
const btn2: HTMLDivElement = document.getElementById('button2');
const btns: HTMLDivElement[] = [btn1, btn2];
ImmersiveReader.renderButtons({elements: btns});
```

Další možnosti vykreslování najdete na výše uvedených [volitelných atributech](#optional-attributes) . Chcete-li použít tyto možnosti, přidejte všechny atributy možností na každou ```HTMLDivElement``` stránku ve formátu HTML stránky.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

#### <a name="renderbuttons-parameters"></a>Parametry renderButtons

| Název | Typ | Popis |
| ---- | ---- |------------ |
| `options` | [renderButtons možnosti](#renderbuttons-options) | Možnosti pro konfiguraci určitého chování funkce renderButtons Nepovinný parametr. |

### <a name="renderbuttons-options"></a>renderButtons možnosti

Možnosti pro vykreslování tlačítek pro moderní čtečku

```typescript
{
    elements: HTMLDivElement[];
}
```

#### <a name="renderbuttons-options-parameters"></a>Parametry možností renderButtons

| Nastavení | Typ | Popis |
| ------- | ---- | ----------- |
| elementy | HTMLDivElement[] | Prvky pro vykreslení tlačítek pro moderní čtečku v. |

##### `-elements`
```Parameters
Type: HTMLDivElement[]
Required: false
```

<br>

## <a name="launchresponse"></a>LaunchResponse

Obsahuje odpověď od volání `ImmersiveReader.launchAsync` . Všimněte si, že odkaz na `iframe` , který obsahuje moderní čtečku, je k dispozici prostřednictvím `container.firstChild` .

```typescript
{
    container: HTMLDivElement;
    sessionId: string;
}
```

#### <a name="launchresponse-parameters"></a>Parametry LaunchResponse

| Nastavení | Typ | Popis |
| ------- | ---- | ----------- |
| kontejner | HTMLDivElement | Prvek HTML, který obsahuje prvek pro moderní čtečku. |
| sessionId | Řetězec | Globálně jedinečný identifikátor pro tuto relaci, který se používá pro ladění. |
 
## <a name="error"></a>Chyba

Obsahuje informace o chybě.

```typescript
{
    code: string;
    message: string;
}
```

#### <a name="error-parameters"></a>Parametry chyby

| Nastavení | Typ | Popis |
| ------- | ---- | ----------- |
| kód | Řetězec | Jeden ze sady chybových kódů. Viz [kódy chyb](#error-codes). |
| zpráva | Řetězec | Reprezentace chyby v čitelném člověku |

#### <a name="error-codes"></a>Kódy chyb

| Kód | Popis |
| ---- | ----------- |
| BadArgument | Zadaný argument je neplatný, viz `message` parametr [chyby](#error). |
| Časový limit | V rámci zadaného časového limitu se nepovedlo načíst moderní čtečku. |
| TokenExpired | Platnost zadaného tokenu vypršela. |
| Omezené | Překročilo se omezení četnosti volání. |

<br>

## <a name="types"></a>Typy

### <a name="content"></a>Content

Obsahuje obsah, který se zobrazí v moderní čtečce.

```typescript
{
    title?: string;
    chunks: Chunk[];
}
```

#### <a name="content-parameters"></a>Parametry obsahu

| Název | Typ | Popis |
| ---- | ---- |------------ |
| title | Řetězec | Text nadpisu zobrazený v horní části moderního čtecího zařízení (volitelné) |
| bloky dat | [Blok dat []](#chunk) | Pole bloků |

##### `-title`
```Parameters
Type: String
Required: false
Default value: "Immersive Reader" 
```

##### `-chunks`
```Parameters
Type: Chunk[]
Required: true
Default value: null 
```

<br>

### <a name="chunk"></a>Blok dat

Jeden blok dat, který se předává do obsahu moderního čtecího zařízení.

```typescript
{
    content: string;
    lang?: string;
    mimeType?: string;
}
```

#### <a name="chunk-parameters"></a>Parametry bloku dat

| Název | Typ | Popis |
| ---- | ---- |------------ |
| obsah | Řetězec | Řetězec obsahující obsah odeslaný do moderního čtecího zařízení. |
| jazyk | Řetězec | Jazyk textu, hodnota je ve formátu značek IETF BCP 47, například en, ES-ES. Jazyk bude detekován automaticky, pokud není zadán. Viz [Podporované jazyky](#supported-languages). |
| mimeType | řetězec | Jsou podporovány formáty prostého textu, MathML, HTML & formáty DOCX Microsoft Wordu. Další podrobnosti najdete v tématu [podporované typy MIME](#supported-mime-types) . |

##### `-content`
```Parameters
Type: String
Required: true
Default value: null 
```

##### `-lang`
```Parameters
Type: String
Required: false
Default value: Automatically detected 
```

##### `-mimeType`
```Parameters
Type: String
Required: false
Default value: "text/plain"
```

#### <a name="supported-mime-types"></a>Podporované typy MIME

| Typ MIME | Popis |
| --------- | ----------- |
| Text/prostý | Prostý text. |
| text/html | Obsah HTML. [Další informace](#html-support)|
| Application/MathML + XML | Jazyk MathML (Matematická Markup Language). [Přečtěte si další informace](./how-to/display-math.md).
| aplikace/vnd.openxmlformats-officedocument.wordprocessingml.document | Dokument formátu Microsoft Word. docx.


<br>

## <a name="options"></a>Možnosti

Obsahuje vlastnosti, které konfigurují určité chování moderního čtecího zařízení.

```typescript
{
    uiLang?: string;
    timeout?: number;
    uiZIndex?: number;
    useWebview?: boolean;
    onExit?: () => any;
    allowFullscreen?: boolean;
    hideExitButton?: boolean;
    cookiePolicy?: CookiePolicy;
    disableFirstRun?: boolean;
    readAloudOptions?: ReadAloudOptions;
    translationOptions?: TranslationOptions;
    displayOptions?: DisplayOptions;
    preferences?: string;
    onPreferencesChanged?: (value: string) => any;
    customDomain?: string;
}
```

#### <a name="options-parameters"></a>Parametry možností

| Název | Typ | Popis |
| ---- | ---- |------------ |
| uiLang | Řetězec | Jazyk uživatelského rozhraní, hodnota je ve formátu značek IETF BCP 47, například en, ES-ES. Pokud není zadaný, použije se výchozí jazyk prohlížeče. |
| timeout | Číslo | Doba (v milisekundách), po jejímž uplynutí [launchAsync](#launchasync) dojde k chybě s časovým limitem (výchozí hodnota je 15000 MS). Tento časový limit se vztahuje pouze na počáteční spuštění stránky čtenář, kde po otevření stránky čtenář a spuštění číselníku dojde k úspěchu. Nastavení časového limitu by nemělo být nutné. |
| uiZIndex | Číslo | Z-index prvku IFRAME, který bude vytvořen (výchozí hodnota je 1000). |
| useWebview | Logická hodnota| Pro kompatibilitu s aplikacemi pro Chrome (výchozí hodnota je false) použijte značku WebView namísto prvku IFRAME. |
| Probíhá ukončení | Funkce | Provede se při ukončení moderního čtečky. |
| allowFullscreen | Logická hodnota | Možnost přepnout na celou obrazovku (výchozí hodnota je true). |
| hideExitButton | Logická hodnota | Určuje, zda se má skrýt šipka tlačítka pro tlačítko ukončení pro moderní čtečku (výchozí hodnota je false). To by mělo být pravdivé pouze v případě, že je k dispozici alternativní mechanismus pro opuštění moderního čtecího zařízení (například šipky zpětného volání na mobilním panelu nástrojů). |
| cookiePolicy | [CookiePolicy](#cookiepolicy-options) | Nastavení pro použití souborů cookie pro moderní čtečku (výchozí nastavení je *CookiePolicy. disable*). Je zodpovědný za to, že hostitelská aplikace získá potřebný souhlas uživatele v souladu se zásadami dodržování předpisů v EU cookie. Viz [Možnosti zásad souborů cookie](#cookiepolicy-options). |
| disableFirstRun | Logická hodnota | Zakažte první prostředí pro spuštění. |
| readAloudOptions | [ReadAloudOptions](#readaloudoptions) | Možnosti pro konfiguraci čtení po nahlasu. |
| translationOptions | [TranslationOptions](#translationoptions) | Možnosti konfigurace překladu |
| displayOptions | [DisplayOptions](#displayoptions) | Možnosti konfigurace velikosti textu, písma atd. |
| hodnot | Řetězec | Řetězec vrácený z onPreferencesChanged představující předvolby uživatele v moderní čtečce. Další informace najdete v tématu [Nastavení parametrů](#settings-parameters) a [postup ukládání uživatelských předvoleb](./how-to-store-user-preferences.md) . |
| onPreferencesChanged | Funkce | Provede se, když se změní předvolby uživatele. Další informace najdete v tématu [jak ukládat předvolby uživatele](./how-to-store-user-preferences.md) . |
| customDomain | Řetězec | Vyhrazeno pro interní použití. Vlastní doména, ve které je WebApp moderního čtecího zařízení (výchozí hodnota je null). |

##### `-uiLang`
```Parameters
Type: String
Required: false
Default value: User's browser language 
```

##### `-timeout`
```Parameters
Type: Number
Required: false
Default value: 15000
```

##### `-uiZIndex`
```Parameters
Type: Number
Required: false
Default value: 1000
```

##### `-onExit`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `-preferences`

> [!CAUTION]
> **Důležité** informace Nepokoušejte se programově změnit hodnoty `-preferences` řetězce odesílaného do a z aplikace moderního čtecího zařízení, protože to může způsobit neočekávané chování, které by vašemu zákazníkům vedlo ke zhoršení uživatelského prostředí.

```Parameters
Type: String
Required: false
Default value: null
```

##### `-onPreferencesChanged`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `-customDomain`
```Parameters
Type: String
Required: false
Default value: null
```

<br>

## <a name="readaloudoptions"></a>ReadAloudOptions

```typescript
type ReadAloudOptions = {
    voice?: string;
    speed?: number;
    autoplay?: boolean;
};
```

#### <a name="readaloudoptions-parameters"></a>Parametry ReadAloudOptions

| Název | Typ | Popis |
| ---- | ---- |------------ |
| telefonní | Řetězec | Voice, buďto "žena" nebo "muž". Všimněte si, že ne všechny jazyky podporují pohlaví. |
| rychlost | Číslo | Rychlost přehrávání musí být v rozmezí od 0,5 do 2,5 (včetně). |
| autoPlay | Logická hodnota | Při načtení moderního čtecího zařízení se automaticky spustí čtení. |

##### `-voice`
```Parameters
Type: String
Required: false
Default value: "Female" or "Male" (determined by language) 
Values available: "Female", "Male"
```

##### `-speed`
```Parameters
Type: Number
Required: false
Default value: 1
Values available: 0.5, 0.75, 1, 1.25, 1.5, 1.75, 2, 2.25, 2.5
```

> [!NOTE]
> V prohlížeči Safari není funkce automatického přehrávání podporována v důsledku omezení prohlížeče.

<br>

## <a name="translationoptions"></a>TranslationOptions

```typescript
type TranslationOptions = {
    language: string;
    autoEnableDocumentTranslation?: boolean;
    autoEnableWordTranslation?: boolean;
};
```

#### <a name="translationoptions-parameters"></a>Parametry TranslationOptions

| Název | Typ | Popis |
| ---- | ---- |------------ |
| language | Řetězec | Nastaví jazyk překladu, hodnota je ve formátu značky jazyka IETF BCP 47, například fr-FR, ES-MX, zh-Hans-CN. Vyžaduje se pro automatické povolení převodu Wordu nebo dokumentu. |
| autoEnableDocumentTranslation | Logická hodnota | Automaticky převede celý dokument. |
| autoEnableWordTranslation | Logická hodnota | Automaticky povolit převod slov |

##### `-language`
```Parameters
Type: String
Required: true
Default value: null 
Values available: See the Supported Languages section
```

<br>

## <a name="displayoptions"></a>DisplayOptions

```typescript
type DisplayOptions = {
    textSize?: number;
    increaseSpacing?: boolean;
    fontFamily?: string;
};
```

#### <a name="displayoptions-parameters"></a>Parametry DisplayOptions

| Název | Typ | Popis |
| ---- | ---- |------------ |
| textSize | Číslo | Nastaví zvolenou velikost textu. |
| increaseSpacing | Logická hodnota | Nastaví, zda se má zapnout nebo vypnout řádkování textu. |
| fontFamily | Řetězec | Nastaví zvolené písmo ("Calibri", "ComicSans" nebo "Sitka"). |

##### `-textSize`
```Parameters
Type: Number
Required: false
Default value: 20, 36 or 42 (Determined by screen size)
Values available: 14, 20, 28, 36, 42, 48, 56, 64, 72, 84, 96
```

##### `-fontFamily`
```Parameters
Type: String
Required: false
Default value: "Calibri"
Values available: "Calibri", "Sitka", "ComicSans"
```

<br>

## <a name="cookiepolicy-options"></a>CookiePolicy možnosti

```typescript
enum CookiePolicy { Disable, Enable }
```

**Níže uvedená nastavení slouží pouze k informativním účelům**. Moderní čtečka ukládá nastavení nebo předvolby uživatele v souborech cookie. Tato *cookiePolicy* možnost cookiePolicy **zakáže** použití souborů cookie ve výchozím nastavení, aby bylo možné dodržovat zákony dodržování předpisů v souborech cookie EU. Chcete-li znovu povolit soubory cookie a obnovit výchozí funkce pro uživatelské předvolby pro moderní čtečku, bude nutné zajistit, aby váš web nebo aplikace získala správný souhlas od uživatele a povolil soubory cookie. Chcete-li znovu povolit soubory cookie v moderní čtečce, je nutné explicitně nastavit možnost *cookiePolicy* na *cookiePolicy. Enable* při spouštění moderního čtecího zařízení. Následující tabulka popisuje, jaká nastavení jsou v souboru cookie pro moderní čtečku v případě, že je povolená možnost *cookiePolicy* .

#### <a name="settings-parameters"></a>Parametry nastavení

| Nastavení | Typ | Popis |
| ------- | ---- | ----------- |
| textSize | Číslo | Nastaví zvolenou velikost textu. |
| fontFamily | Řetězec | Nastaví zvolené písmo ("Calibri", "ComicSans" nebo "Sitka"). |
| textSpacing | Číslo | Nastaví, zda se má zapnout nebo vypnout řádkování textu. |
| formattingEnabled | Logická hodnota | Nastaví, zda je formátování jazyka HTML přepnuto nebo vypnuto. |
| použit | Řetězec | Nastaví zvolený motiv (např. "Light", "tmavý"...). |
| syllabificationEnabled | Logická hodnota | Nastaví, zda byl přepínač syllabification zapnut nebo vypnut. |
| nounHighlightingEnabled | Logická hodnota | Tím se nastaví, jestli se má zapnout nebo vypnout zvýrazňování podstatných zvýraznění. |
| nounHighlightingColor | Řetězec | Nastaví zvolenou barvu zvýraznění podstatných jmen. |
| verbHighlightingEnabled | Logická hodnota | Nastaví, zda se má zapnout nebo vypnout zvýrazňování operací. |
| verbHighlightingColor | Řetězec | Nastaví zvolenou barvu zvýraznění příkazu. |
| adjectiveHighlightingEnabled | Logická hodnota | Nastaví, jestli se má zapnout nebo vypnout zvýraznění přídavného jména. |
| adjectiveHighlightingColor | Řetězec | Nastaví zvolenou barvu zvýraznění přídavného jména. |
| adverbHighlightingEnabled | Logická hodnota | Nastaví, zda se má zapnout nebo vypnout zvýrazňování příslovení. |
| adverbHighlightingColor | Řetězec | Nastaví vybranou barvu zvýraznění příslovu. |
| pictureDictionaryEnabled | Logická hodnota | Nastaví, jestli se má zapnout nebo vypnout slovník obrázků. |
| posLabelsEnabled | Logická hodnota | Nastaví, zda je textový popisek horního indexu každé zvýrazněné části rozpoznávání řeči přepnut nebo vypnut.  |

<br>

## <a name="supported-languages"></a>Podporované jazyky

Funkce překladu moderního čtecího zařízení podporuje mnoho jazyků. Další podrobnosti najdete v [tomto článku](https://www.onenote.com/learningtools/languagesupport) .

<br>

## <a name="html-support"></a>Podpora HTML

Když je povolené formátování, v moderním čtečce se v moderní čtečce vykreslí následující obsah jako HTML.

| HTML | Podporovaný obsah |
| --------- | ----------- |
| Styly písma | Tučné, kurzíva, podtržení, kód, přeškrtnutí, horní index, dolní index |
| Neuspořádané seznamy | Disk, kruh, čtverec |
| Seřazené seznamy | Decimal, Upper-Alpha, nižší-alfa, horní – Roman, nižší – Roman |

Nepodporované značky budou vykresleny srovnatelně. Obrázky a tabulky se aktuálně nepodporují.

<br>

## <a name="browser-support"></a>Podpora prohlížečů

K dosažení nejlepšího prostředí pro moderní čtečku použijte nejnovější verze následujících prohlížečů.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

<br>

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [sadu moderních čtenářů na GitHubu](https://github.com/microsoft/immersive-reader-sdk)
* [Rychlý Start: Vytvoření webové aplikace, která spustí moderní čtečku (C#)](./quickstarts/client-libraries.md?pivots=programming-language-csharp)
