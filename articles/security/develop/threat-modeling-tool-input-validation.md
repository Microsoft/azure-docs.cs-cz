---
title: Ověřování vstupu – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Přečtěte si o ověřování vstupu v Threat Modeling Tool. Podívejte se na informace o zmírnění rizik a zobrazte příklady kódu.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-csharp
ms.openlocfilehash: 3bb944badfbdffd703672f9e78619c70a148aae2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89293349"
---
# <a name="security-frame-input-validation--mitigations"></a>Rámec zabezpečení: ověření vstupu | Hrozeb 
| Produkt/služba | Článek |
| --------------- | ------- |
| **Webová aplikace** | <ul><li>[Zakázat skriptování XSLT pro všechny transformace pomocí nedůvěryhodných šablon stylů](#disable-xslt)</li><li>[Zajistěte, aby každá stránka, která by mohla obsahovat uživatelsky ovladatelného obsahu, výslovný z automatického sledování MIME.](#out-sniffing)</li><li>[Posílení nebo zakázání rozlišení entit XML](#xml-resolution)</li><li>[Aplikace, které využívají http.sys provádění ověřování pomocí kanonického zpracování adresy URL](#app-verification)</li><li>[Zajistěte, aby byly při přijímání souborů od uživatelů k dismístě správné ovládací prvky.](#controls-users)</li><li>[Zajistěte, aby se pro přístup k datům používaly parametry bezpečné pro typ ve webové aplikaci.](#typesafe)</li><li>[Použití samostatných tříd vazeb modelů nebo seznamů filtru vazeb k zamezení ohrožení zabezpečení pro hromadné přiřazení MVC](#binding-mvc)</li><li>[Kódování nedůvěryhodného webového výstupu před vykreslením](#rendering)</li><li>[Provede ověřování vstupu a filtrování u všech vlastností modelu řetězcového typu.](#typemodel)</li><li>[Pro pole formuláře, která přijímají všechny znaky, například Editor formátovaného textu, by měla být použita upravená část.](#richtext)</li><li>[Nepřiřazujte elementy modelu DOM k jímky, které nemají sestavené kódování.](#inbuilt-encode)</li><li>[Ověření všech přesměrování v rámci aplikace je uzavřeno nebo provedeno bezpečně.](#redirect-safe)</li><li>[Implementovat ověřování vstupu pro všechny parametry řetězcového typu akceptované metodami řadiče](#string-method)</li><li>[Nastavte časový limit horní meze pro zpracování regulárního výrazu, aby se zabránilo DoS v důsledku chybných regulárních výrazů.](#dos-expression)</li><li>[Nepoužívejte HTML. Raw v zobrazeních Razor](#html-razor)</li></ul> | 
| **Databáze** | <ul><li>[Nepoužívejte dynamické dotazy v uložených procedurách](#stored-proc)</li></ul> |
| **Web API** | <ul><li>[Ujistěte se, že se ověřování modelu provádí na metodách webového rozhraní API.](#validation-api)</li><li>[Implementovat ověřování vstupu pro všechny parametry řetězcového typu akceptované metodami webového rozhraní API](#string-api)</li><li>[Zajistěte, aby se pro přístup k datům používaly parametry bezpečné pro typ ve webovém rozhraní API.](#typesafe-api)</li></ul> | 
| **Azure Document DB** | <ul><li>[Použití parametrizovaných dotazů SQL pro Azure Cosmos DB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[Ověřování vstupu WCF prostřednictvím vazby schématu](#schema-binding)</li><li>[WCF – ověřování vstupu prostřednictvím inspektorů parametrů](#parameters)</li></ul> |

## <a name="disable-xslt-scripting-for-all-transforms-using-untrusted-style-sheets"></a><a id="disable-xslt"></a>Zakázat skriptování XSLT pro všechny transformace pomocí nedůvěryhodných šablon stylů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Zabezpečení XSLT](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [vlastnost XsltSettings. EnableScript](https://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Kroky** | XSLT podporuje skriptování uvnitř šablon stylů pomocí `<msxml:script>` elementu. To umožňuje použití vlastních funkcí v transformaci XSLT. Skript je spuštěn v kontextu procesu, který provádí transformaci. Skript XSLT musí být zakázán, pokud je v nedůvěryhodném prostředí, aby nedocházelo k provádění nedůvěryhodného kódu. *Pokud používáte rozhraní .NET:* Ve výchozím nastavení je skriptování XSLT zakázané. je však nutné zajistit, aby nebyla explicitně povolena prostřednictvím `XsltSettings.EnableScript` Vlastnosti.|

### <a name="example"></a>Příklad 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Příklad
Pokud používáte MSXML 6,0, ve výchozím nastavení je skriptování XSLT zakázané. je však nutné zajistit, aby nebyl explicitně povolen prostřednictvím vlastnosti objektu XML DOM AllowXsltScript. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Příklad
Pokud používáte MSXML 5 nebo níže, je ve výchozím nastavení povoleno skriptování XSLT a je nutné je explicitně zakázat. Nastavte vlastnost objektu XML DOM AllowXsltScript na hodnotu false. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a name="ensure-that-each-page-that-could-contain-user-controllable-content-opts-out-of-automatic-mime-sniffing"></a><a id="out-sniffing"></a>Zajistěte, aby každá stránka, která by mohla obsahovat uživatelsky ovladatelného obsahu, výslovný z automatického sledování MIME.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [IE8 zabezpečení část V – komplexní ochrana](https://docs.microsoft.com/archive/blogs/ie/ie8-security-part-v-comprehensive-protection)  |
| **Kroky** | <p>Pro každou stránku, která by mohla obsahovat uživatelsky ovladatelné obsahy, je nutné použít hlavičku protokolu HTTP `X-Content-Type-Options:nosniff` . Aby bylo možné tento požadavek splnit, můžete buď nastavit požadovanou stránku záhlaví na stránce pouze na ty stránky, které mohou obsahovat uživatelsky přizpůsobený obsah, nebo je můžete nastavit globálně pro všechny stránky v aplikaci.</p><p>Každý typ souboru dodaný z webového serveru má přidružený [typ MIME](https://en.wikipedia.org/wiki/Mime_type) (označuje se také jako *typ obsahu*), který popisuje povahu obsahu (tj. obrázek, text, aplikace atd.).</p><p>Hlavička X-Content-Type-Options je hlavička protokolu HTTP, která vývojářům umožňuje určit, že jejich obsah by neměl být ve formátu MIME. Tato hlavička je navržená tak, aby zmírnila MIME-Sniffing útoky. Podpora pro tuto hlavičku se přidala v Internet Exploreru 8 (IE8).</p><p>Jenom uživatelé aplikace Internet Explorer 8 (IE8) budou využívat možnosti X-Content-Type-Type. Předchozí verze aplikace Internet Explorer aktuálně nerespektují hlavičku X-Content-Type-Options</p><p>Internet Explorer 8 (a novější) jsou jediné hlavní prohlížeče, které implementují funkci pro odhlášení pomocí kódování MIME. Pokud a pokud jiné hlavní prohlížeče (Firefox, Safari, Chrome) implementují podobné funkce, bude toto doporučení aktualizováno, aby obsahovalo i syntaxi pro tyto prohlížeče.</p>|

### <a name="example"></a>Příklad
Pokud chcete povinnou hlavičku globálně povolit pro všechny stránky v aplikaci, můžete provést jednu z následujících akcí: 

* Pokud je aplikace hostovaná pomocí Internetová informační služba (IIS) 7, přidejte do souboru web.config hlavičku. 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* Přidat hlavičku do globální aplikace \_ beginRequest 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* Implementace vlastního modulu HTTP 

``` 
public class XContentTypeOptionsModule : IHttpModule 
  {
    #region IHttpModule Members 
    public void Dispose() 
    { 

    } 
    public void Init(HttpApplication context)
    { 
      context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders); 
    } 
    #endregion 
    void context_PreSendRequestHeaders(object sender, EventArgs e) 
      { 
        HttpApplication application = sender as HttpApplication; 
        if (application == null) 
          return; 
        if (application.Response.Headers[""X-Content-Type-Options ""] != null) 
          return; 
        application.Response.Headers.Add(""X-Content-Type-Options "", ""nosniff""); 
      } 
  } 

``` 

* Požadovanou hlavičku můžete povolit jenom pro konkrétní stránky tak, že ji přidáte do jednotlivých odpovědí: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a name="harden-or-disable-xml-entity-resolution"></a><a id="xml-resolution"></a>Posílení nebo zakázání rozlišení entit XML

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Rozšíření entit XML](https://capec.mitre.org/data/definitions/197.html), [útoky DOS a obrany služby XML](https://msdn.microsoft.com/magazine/ee335713.aspx), [Přehled zabezpečení MSXML](https://msdn.microsoft.com/library/ms754611(v=VS.85).aspx), [osvědčené postupy pro zabezpečení kódu MSXML](https://msdn.microsoft.com/library/ms759188(VS.85).aspx), [reference protokolu NSXMLParserDelegate](https://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html)a [Překlad externích odkazů](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Kroky**| <p>I když se v současnosti nepoužívá, je funkce jazyka XML, která umožňuje analyzátoru jazyka XML rozbalovat entity maker s hodnotami definovanými buď v samotném dokumentu, nebo z externích zdrojů. Dokument může například definovat entitu "CompanyName" s hodnotou "Microsoft", takže pokaždé, když se text " &companyname; " zobrazí v dokumentu, bude automaticky nahrazen textem společnosti Microsoft. Nebo může dokument definovat entitu "MSFTStock", která odkazuje na externí webovou službu, aby se načetla aktuální hodnota akcie Microsoft.</p><p>&MSFTStock;V dokumentu se pak zobrazí všechny časy, automaticky se nahradí aktuální cenou akcií. Tato funkce se ale dá zneužít, aby se vytvořily podmínky DOS (Denial of Service). Útočník může vnořit více entit pro vytvoření exponenciálního rozšíření bomb XML, které spotřebovává veškerou dostupnou paměť v systému. </p><p>Alternativně může vytvořit externí odkaz, který streamuje zpět nekonečné množství dat nebo jednoduše zablokuje vlákno. V důsledku toho musí všechny týmy zakázat interní nebo externí rozlišení entity XML, pokud je aplikace nepoužívá, nebo ručně omezit množství paměti a času, který může aplikace spotřebovat pro řešení entit, pokud je tato funkce nezbytně nutná. Pokud vaše aplikace nevyžaduje překlad entit, pak ho zakažte. </p>|

### <a name="example"></a>Příklad
Pro .NET Framework kód můžete použít následující přístupy:

```csharp
XmlTextReader reader = new XmlTextReader(stream);
reader.ProhibitDtd = true;

XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);

// for .NET 4
XmlReaderSettings settings = new XmlReaderSettings();
settings.DtdProcessing = DtdProcessing.Prohibit;
XmlReader reader = XmlReader.Create(stream, settings);
```
Všimněte si, že výchozí hodnota `ProhibitDtd` v `XmlReaderSettings` je true, ale v `XmlTextReader` této hodnotě je false. Pokud používáte XmlReaderSettings, nemusíte explicitně nastavit ProhibitDtd na hodnotu true, ale doporučuje se z důvodu bezpečnosti. Všimněte si také, že Třída XmlDocument ve výchozím nastavení umožňuje překlad entit. 

### <a name="example"></a>Příklad
Chcete-li zakázat překlad entit pro XmlDocument, použijte `XmlDocument.Load(XmlReader)` přetížení metody Load a nastavte příslušné vlastnosti v argumentu XmlReader pro zakázání rozlišení, jak je znázorněno v následujícím kódu: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Příklad
Pokud není u vaší aplikace možné zakázat řešení entity, nastavte vlastnost XmlReaderSettings. MaxCharactersFromEntities na rozumnou hodnotu podle potřeb vaší aplikace. Tím se omezí dopad potenciálních útoků na exponenciální rozšíření DoS. Následující kód poskytuje příklad tohoto přístupu: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Příklad
Pokud potřebujete vyřešit vložené entity, ale nepotřebujete přeložit externí entity, nastavte vlastnost překladače XmlReaderSettings.Xmlna hodnotu null. Například: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Všimněte si, že ve MSXML6 je ProhibitDTD ve výchozím nastavení nastaveno na hodnotu true (zakázání zpracování DTD). Pro kód Apple OSX/iOS existují dva analyzátory XML, které můžete použít: NSXMLParser a libXML2. 

## <a name="applications-utilizing-httpsys-perform-url-canonicalization-verification"></a><a id="app-verification"></a>Aplikace, které využívají http.sys provádění ověřování pomocí kanonického zpracování adresy URL

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | Není k dispozici  |
| **Kroky** | <p>Každá aplikace, která používá http.sys, by měla postupovat podle těchto pokynů:</p><ul><li>Omezte délku adresy URL na maximálně 16 384 znaků (ASCII nebo Unicode). Toto je absolutní maximální délka adresy URL na základě výchozího nastavení Internetová informační služba (IIS) 6. Weby by se měly snažit s kratší délkou, pokud je to možné.</li><li>Použijte standardní .NET Framework třídy I/O souborů (například FileStream), protože budou využívat pravidla kanonikalizace v rozhraní .NET FX.</li><li>Explicitní sestavení seznamu známých názvů souborů jako povolených</li><li>Explicitně zamítnout známé typy souborů. nebudete zajišťovat nástroje UrlScan: exe, bat, cmd, com, HTW, IDA, IDQ, htr, IDC, SHTM [l], STM, tiskárna, ini, Pol, soubory dat dat.</li><li>Zachytit následující výjimky:<ul><li>System. ArgumentException (pro názvy zařízení)</li><li>System. NotSupportedException (pro datové proudy)</li><li>System. IO. FileNotFoundException (v případě neplatných řídicích názvů souborů)</li><li>System. IO. DirectoryNotFoundException (pro neplatnou řídicí adresářů)</li></ul></li><li>*Nevolejte na* vstupně-výstupní rozhraní API souborů Win32. Na neplatné adrese URL vrátí uživatel chybu 400 a zaprotokoluje skutečnou chybu.</li></ul>|

## <a name="ensure-appropriate-controls-are-in-place-when-accepting-files-from-users"></a><a id="controls-users"></a>Zajistěte, aby byly při přijímání souborů od uživatelů k dismístě správné ovládací prvky.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Neomezená nahrávání souborů](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload), [tabulka podpisů souborů](https://www.garykessler.net/library/file_sigs.html) |
| **Kroky** | <p>Nahrané soubory představují významné riziko pro aplikace.</p><p>Prvním krokem v mnoha útokech je, že systém získá nějaký kód k útokům. Pak útok potřebuje najít způsob, jak spustit kód. Použití nahrávání souborů pomáhá útočníkovi dosáhnout prvního kroku. Důsledky neomezeného nahrávání souboru se můžou lišit, včetně úplného převzetí systému, přetíženého systému souborů nebo databáze, předávání útoků back-endovém systémům a jednoduchého přítváření.</p><p>Záleží na tom, co aplikace dělá s nahraným souborem, a to hlavně tam, kde je uložená. Chybí ověření pro nahrávání souborů na straně serveru. Pro funkci nahrávání souborů by se měly implementovat následující ovládací prvky zabezpečení:</p><ul><li>Ověření přípony souboru (je třeba přijmout jenom platnou sadu povolených typů souborů)</li><li>Maximální omezení velikosti souboru</li><li>Soubor by neměl být nahrán do Webroot; umístění by mělo být adresářem na nesystémové jednotce.</li><li>Musí následovat konvence pojmenování, aby název nahraného souboru měl určitou náhodnost, aby se zabránilo přepsání souborů.</li><li>Před zápisem na disk musí být soubory zkontrolovány na antivirový program.</li><li>Zajistěte, aby byl název souboru a další metadata (například cesta k souboru) ověřeny pro škodlivé znaky.</li><li>Je nutné zkontrolovat podpis formátu souboru, aby uživatel mohl nahrávat maskovaný soubor (např. nahrání souboru exe změnou přípony na txt).</li></ul>| 

### <a name="example"></a>Příklad
Pro poslední bod týkající se ověření podpisu formátu souboru použijte níže uvedenou třídu pro podrobnosti: 

```csharp
        private static Dictionary<string, List<byte[]>> fileSignature = new Dictionary<string, List<byte[]>>
                    {
                    { ".DOC", new List<byte[]> { new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 } } },
                    { ".DOCX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".PDF", new List<byte[]> { new byte[] { 0x25, 0x50, 0x44, 0x46 } } },
                    { ".ZIP", new List<byte[]> 
                                            {
                                              new byte[] { 0x50, 0x4B, 0x03, 0x04 },
                                              new byte[] { 0x50, 0x4B, 0x4C, 0x49, 0x54, 0x55 },
                                              new byte[] { 0x50, 0x4B, 0x53, 0x70, 0x58 },
                                              new byte[] { 0x50, 0x4B, 0x05, 0x06 },
                                              new byte[] { 0x50, 0x4B, 0x07, 0x08 },
                                              new byte[] { 0x57, 0x69, 0x6E, 0x5A, 0x69, 0x70 }
                                                }
                                            },
                    { ".PNG", new List<byte[]> { new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A } } },
                    { ".JPG", new List<byte[]>
                                    {
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE1 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE8 }
                                    }
                                    },
                    { ".JPEG", new List<byte[]>
                                        { 
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 }
                                        }
                                        },
                    { ".XLS", new List<byte[]>
                                            {
                                              new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 },
                                              new byte[] { 0x09, 0x08, 0x10, 0x00, 0x00, 0x06, 0x05, 0x00 },
                                              new byte[] { 0xFD, 0xFF, 0xFF, 0xFF }
                                            }
                                            },
                    { ".XLSX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".GIF", new List<byte[]> { new byte[] { 0x47, 0x49, 0x46, 0x38 } } }
                };

        public static bool IsValidFileExtension(string fileName, byte[] fileData, byte[] allowedChars)
        {
            if (string.IsNullOrEmpty(fileName) || fileData == null || fileData.Length == 0)
            {
                return false;
            }

            bool flag = false;
            string ext = Path.GetExtension(fileName);
            if (string.IsNullOrEmpty(ext))
            {
                return false;
            }

            ext = ext.ToUpperInvariant();

            if (ext.Equals(".TXT") || ext.Equals(".CSV") || ext.Equals(".PRN"))
            {
                foreach (byte b in fileData)
                {
                    if (b > 0x7F)
                    {
                        if (allowedChars != null)
                        {
                            if (!allowedChars.Contains(b))
                            {
                                return false;
                            }
                        }
                        else
                        {
                            return false;
                        }
                    }
                }

                return true;
            }

            if (!fileSignature.ContainsKey(ext))
            {
                return true;
            }

            List<byte[]> sig = fileSignature[ext];
            foreach (byte[] b in sig)
            {
                var curFileSig = new byte[b.Length];
                Array.Copy(fileData, curFileSig, b.Length);
                if (curFileSig.SequenceEqual(b))
                {
                    flag = true;
                    break;
                }
            }

            return flag;
        }
```

## <a name="ensure-that-type-safe-parameters-are-used-in-web-application-for-data-access"></a><a id="typesafe"></a>Zajistěte, aby se pro přístup k datům používaly parametry bezpečné pro typ ve webové aplikaci.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | Není k dispozici  |
| **Kroky** | <p>Použijete-li kolekci Parameters, SQL považuje vstup za hodnotu literálu, a ne jako spustitelný kód. Kolekci Parameters lze použít k vynutit omezení typu a délky vstupních dat. Hodnoty mimo rozsah spouštějí výjimku. Pokud se nepoužívají parametry SQL bezpečného typu, útočník může být schopen spustit útoky prostřednictvím injektáže vložené do nefiltrovaného vstupu.</p><p>Při vytváření dotazů SQL použijte parametry typově bezpečné, aby nedocházelo k útokům prostřednictvím injektáže SQL, ke kterým může dojít s nefiltrovaným vstupem. Typ bezpečné parametry můžete použít s uloženými procedurami a s dynamickými příkazy SQL. Parametry jsou považovány za hodnoty literálu v databázi, nikoli jako spustitelný kód. U parametrů se také kontroluje typ a délka.</p>|

### <a name="example"></a>Příklad 
Následující kód ukazuje, jak použít parametry typu Safe s SqlParameterCollection při volání uložené procedury. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
V předchozím příkladu kódu nemůže být vstupní hodnota delší než 11 znaků. Pokud data neodpovídají typu nebo délce definované parametrem, třída SqlParameter vyvolá výjimku. 

## <a name="use-separate-model-binding-classes-or-binding-filter-lists-to-prevent-mvc-mass-assignment-vulnerability"></a><a id="binding-mvc"></a>Použití samostatných tříd vazeb modelů nebo seznamů filtru vazeb k zamezení ohrožení zabezpečení pro hromadné přiřazení MVC

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC5, MVC6 |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Atributy metadat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute), [zranitelnost zabezpečení veřejného klíče a zmírnění rizika](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation), [kompletní příručka k hromadnému přiřazení v ASP.NET MVC](https://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [Začínáme s EF pomocí MVC](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Kroky** | <ul><li>**Kdy je vhodné pohlížet na chyby při převzetí služeb při selhání?-** K ohrožení zabezpečení při selhání může dojít na jakémkoli místě, kde svážete třídy modelu od vstupu uživatele. Architektury, jako je MVC, mohou představovat uživatelská data ve vlastních třídách .NET, včetně jednoduchých starých objektů CLR (POCOs). MVC automaticky naplní tyto třídy modelu daty z požadavku a poskytne tak praktické vyjádření pro práci s uživatelským vstupem. Pokud tyto třídy obsahují vlastnosti, které by neměly být nastavené uživatelem, může být aplikace zranitelná vůči útokům přes více než jednou, což umožňuje uživateli řídit data, která aplikace nikdy nezamýšlela. Podobně jako vazby modelů MVC, technologie přístupu k databázi, jako jsou například objekty nebo relační mapovače, jako je například Entity Framework často podporují také použití objektů POCO k reprezentaci dat databáze. Tyto třídy datového modelu poskytují stejnou pohodlí při práci s databázovými daty jako MVC při práci s uživatelským vstupem. Vzhledem k tomu, že MVC i databáze podporují podobné modely, jako jsou objekty POCO, je pravděpodobné, že pro oba účely snadno znovu použijete stejné třídy. Tento postup nepovede k zachování oddělení potíží a jedná se o jednu běžnou oblast, ve které jsou nezamýšlené vlastnosti vystavené vazbě modelu a umožňují útoky přes navýšení.</li><li>**Proč nepoužívejte moje nefiltrované třídy databázového modelu jako parametry do akcí MVC?-** Vzhledem k tomu, že vazba modelu MVC bude v této třídě svázat cokoli. I když se data ve vašem zobrazení nezobrazí, může uživatel se zlými úmysly odeslat požadavek HTTP s těmito daty a MVC ho sváže, protože tato akce říká, že databázová třída je tvar dat, který by měl přijmout pro vstup uživatele.</li><li>**Proč se mám zajímat o tvar použitý pro vazbu modelu?-** Použití vazby modelu ASP.NET MVC s nadširokou škálou modelů zpřístupňuje aplikaci k útokům přes více než po publikování. Při převzetí služeb při selhání může útočníkům umožnit změnit data aplikací nad rámec zamýšlené vývojářů, jako je například přepsání ceny za položku nebo oprávnění zabezpečení pro účet. Aplikace by měly použít modely vazeb specifické pro akci (nebo konkrétní povolené seznamy filtru vlastností) k poskytnutí explicitního kontraktu pro to, který nedůvěryhodný vstup umožňuje prostřednictvím vazby modelu.</li><li>**Mají samostatné modely vazeb pouze duplikování kódu?-** Ne, záleží na tom, co se týká oddělení. Pokud znovu použijete databázové modely v metodách akcí, říkáte jakoukoli vlastnost (nebo dílčí vlastnost) v této třídě, kterou může uživatel nastavit v požadavku HTTP. Pokud to není to, co má MVC dělat, budete potřebovat seznam filtrů nebo samostatný tvar třídy, aby se zobrazila třída MVC, kterou data mohou pocházet ze vstupu uživatele.</li><li>**Pokud mám samostatné modely vazeb pro uživatelský vstup, musím Duplikovat všechny atributy mých poznámek k datům?-** Ne nutně. MetadataTypeAttribute můžete použít na třídě databázového modelu a propojit k metadatům třídy vazby modelu. Všimněte si, že typ odkazovaný rozhraním MetadataTypeAttribute musí být podmnožinou odkazujícího typu (může mít méně vlastností, ale ne více).</li><li>**Přesouvání dat mezi modely vstupu uživatele a databázovými modely je zdlouhavé. Můžu jenom kopírovat všechny vlastnosti pomocí reflexe? –** Ano. Jediné vlastnosti, které se zobrazí v modelech vazeb, jsou ty, které jste určili jako bezpečné pro vstup uživatele. Neexistuje žádný důvod zabezpečení, který znemožňuje použití reflexe ke kopírování všech vlastností, které existují mezi těmito dvěma modely společně.</li><li>**Co je [BIND (Exclude = "â &euro; ¦")]. Můžu místo toho používat samostatné modely vazeb? –** Tento přístup se nedoporučuje. Použití příkazu [BIND (Exclude = "â &euro; ¦")] znamená, že ve výchozím nastavení je vytvořena vazba jakékoli nové vlastnosti. Když se přidá nová vlastnost, je k dispozici další krok, abyste se zapamatovali o zabezpečení, ale nemuseli byste ji mít ve výchozím nastavení zabezpečené. V závislosti na tom, jak vývojář tuto vlastnost kontroluje, je riziková.</li><li>**Je [BIND (include = "â &euro; ¦")] užitečné pro operace úpravy?-** ne. [BIND (include = "â &euro; ¦")] je vhodná jenom pro operace typu INSERT (přidávání nových dat). U operací se stylem aktualizace (revize stávajících dat) použijte jiný přístup, jako je například použití samostatných modelů vazby nebo předání explicitního seznamu povolených vlastností UpdateModel nebo TryUpdateModel. Přidání atributu [BIND (include = "â &euro; ¦")] u operace Edit znamená, že MVC vytvoří instanci objektu a nastaví pouze uvedené vlastnosti, přičemž všechny ostatní na jejich výchozích hodnotách zůstanou. Když jsou data trvalá, nahradí ji zcela stávající entitou a obnoví jejich výchozí hodnoty z důvodu resetování hodnot pro všechny vynechané vlastnosti. Pokud je například správce v operaci Edit vynechán z atributu [BIND (include = "â &euro; ¦")], bude každý uživatel, jehož název byl upraven pomocí této akce, resetován na hodnotu admin = false (libovolný upravovaný uživatel ztratí stav správce). Pokud chcete zabránit aktualizacím některých vlastností, použijte jeden z dalších přístupů. Všimněte si, že některé verze nástrojů MVC generují třídy kontroleru s [BIND (include = "â &euro; ¦")] u akcí úprav a implikuje, že odebráním vlastnosti z tohoto seznamu zabráníte útokům přes opakované publikování. Jak je uvedeno výše, ale tento přístup nefunguje tak, jak je zamýšlený, a místo toho obnoví všechna data v vynechaných vlastnostech na jejich výchozí hodnoty.</li><li>**Pro operace vytvoření jsou k dispozici nějaká upozornění pomocí [BIND (include = "â &euro; ¦")] namísto samostatných modelů vazby?-** Yes. První tento přístup nefunguje pro scénáře úprav, což vyžaduje zachování dvou samostatných přístupů pro zmírnění všech zranitelných chyb zabezpečení. Za druhé, samostatné vazby modelů vynutily oddělení obav mezi tvarem použitým pro vstup uživatele a tvar použitý pro stálost, něco [BIND (include = "â &euro; ¦")] nedělá. Třetí, Všimněte si, že [BIND (include = "â &euro; ¦")] může zpracovávat pouze vlastnosti nejvyšší úrovně; v atributu nelze pouze použít části dílčích vlastností (například "Details.Name"). Nakonec, a možná nejdůležitější, pomocí [BIND (include = "â &euro; ¦")] přidá dodatečný krok, který musí být zapamatovatelné vždy, když se třída používá pro vazbu modelu. Pokud se nová metoda akce váže k třídě dat přímo a zamezí, aby obsahovala atribut [BIND (include = "â &euro; ¦")], může být zranitelná vůči útokům přes více než jednou, takže přístup [BIND (include = "â &euro; ¦")] je ve výchozím nastavení poněkud méně bezpečný. Použijete-li [BIND (include = "â &euro; ¦")], pokaždé, když se vaše datové třídy zobrazí jako parametry metody akce, pečlivě zapamatujte na jejich zadání.</li><li>**Informace o tom, jak vytvářet operace pro vytváření [BIND (include = "â &euro; ¦")] ve třídě modelu samotné? Nejedná se o tento přístup, aby nedocházelo k nutnosti zapamatovat si atribut pro každou metodu akce? –** Tento přístup funguje v některých případech. Použití [BIND (include = "â &euro; ¦")] u samotného typu modelu (místo u parametrů akce pomocí této třídy) se nemusíte zapamatovat, aby bylo možné zahrnout atribut [BIND (include = "â &euro; ¦")] u každé metody akce. Použití atributu přímo na třídě efektivně vytvoří samostatnou oblast povrchu této třídy pro účely vazby modelu. Tento přístup však povoluje pouze jeden obrazec vazby modelu na třídu modelu. Pokud je nutné, aby jedna metoda akce povolila vazbu modelu pole (například akce jenom správce, která aktualizuje role uživatelů) a další akce, které vyžadují, aby vazba modelu tohoto pole byla zabráněno, tento přístup nebude fungovat. Každá třída může mít pouze jeden obrazec vazby modelu; Pokud různé akce vyžadují různé obrazce vazeb modelu, musí tyto samostatné tvary reprezentovat pomocí samostatné třídy vazby modelu nebo oddělit atributy [BIND (include = "â &euro; ¦")] na metodách akcí.</li><li>**Co jsou vazby modelů? Mají stejné věci jako modely zobrazení? –** Jedná se o dvě související koncepce. Pojem vazby modelu odkazuje na třídu modelu použitou v seznamu parametrů akce (tvar předaný z vazby modelu MVC do metody Action). Pojem model zobrazení odkazuje na třídu modelu předanou metodou Action do zobrazení. Použití modelu specifického pro zobrazení je běžným přístupem k předávání dat z metody akce do zobrazení. Tento tvar je často vhodný také pro vazbu modelu a termínový model zobrazení lze použít k odkazování na stejný model, který je použit na obou místech. Aby byl tento postup přesný, hovoří se specificky o vazbách modelů a zaměřuje se na tvar předaný do akce, což je to, co je důležité pro účely hromadného přiřazení.</li></ul>| 

## <a name="encode-untrusted-web-output-prior-to-rendering"></a><a id="rendering"></a>Kódování nedůvěryhodného webového výstupu před vykreslením

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné, webové formuláře, MVC5, MVC6 |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Jak zabránit skriptování mezi weby v ASP.NET](https://msdn.microsoft.com/library/ms998274.aspx), [skriptování mezi weby](https://cwe.mitre.org/data/definitions/79.html), skriptování [XSS (skriptování mezi weby) prevence tahák listů](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **Kroky** | Skriptování mezi weby (obvykle se zkracuje jako XSS) je vektor útoku online služby nebo jakékoli aplikace nebo komponenty, která využívá vstup z webu. Ohrožení zabezpečení XSS může útočníkovi umožnit spouštění skriptu v počítači jiného uživatele prostřednictvím zranitelné webové aplikace. Škodlivé skripty lze použít ke krádeži souborů cookie a jiným způsobem manipulace s počítačem oběti prostřednictvím JavaScriptu. Skriptování XSS je znemožněno tím, že ověřuje vstup uživatele a zajišťuje správné formátování a kódování před jejich vykreslením na webové stránce. Ověřování vstupu a výstupní kódování lze provádět pomocí knihovny webové ochrany. Pro spravovaný kód (C \# , VB.NET atd.) použijte jednu nebo více odpovídajících metod kódování z knihovny webové ochrany (anti-XSS), v závislosti na kontextu, ve kterém se vstup uživatele stane manifestem:| 

### <a name="example"></a>Příklad

```csharp
* Encoder.HtmlEncode 
* Encoder.HtmlAttributeEncode 
* Encoder.JavaScriptEncode 
* Encoder.UrlEncode
* Encoder.VisualBasicScriptEncode 
* Encoder.XmlEncode 
* Encoder.XmlAttributeEncode 
* Encoder.CssEncode 
* Encoder.LdapEncode 
```

## <a name="perform-input-validation-and-filtering-on-all-string-type-model-properties"></a><a id="typemodel"></a>Provede ověřování vstupu a filtrování u všech vlastností modelu řetězcového typu.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Generic, MVC5, MVC6 |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Přidání ověřování](https://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [ověřování dat modelu v aplikaci MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [Principy identifikátorů GUID pro aplikace ASP.NET MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroky** | <p>Před použitím v aplikaci musí být všechny vstupní parametry ověřeny, aby se zajistilo, že je aplikace chráněná před škodlivými uživatelskými vstupy. Ověřte vstupní hodnoty pomocí validace regulárních výrazů na straně serveru s povolenou strategií ověřování seznamu. Neupravené uživatelské vstupy/parametry předané metodám můžou způsobit chyby zabezpečení injektáže kódu.</p><p>U webových aplikací můžou vstupní body zahrnovat také pole formuláře, řetězce dotazu, soubory cookie, hlavičky HTTP a parametry webové služby.</p><p>Po vytvoření vazby modelu je nutné provést následující kontroly ověřování:</p><ul><li>Vlastnosti modelu by měly být opatřeny poznámkou s poznámkou RegularExpression, aby bylo možné přijímat povolené znaky a maximální přípustnou délku.</li><li>Metody kontroleru by měly provádět ModelState platnosti</li></ul>|

## <a name="sanitization-should-be-applied-on-form-fields-that-accept-all-characters-eg-rich-text-editor"></a><a id="richtext"></a>Pro pole formuláře, která přijímají všechny znaky, například Editor formátovaného textu, by měla být použita upravená část.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Kódování nebezpečného vstupu](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3), [upraveného kódu HTML](https://github.com/mganss/HtmlSanitizer) |
| **Kroky** | <p>Identifikujte všechny značky statických značek, které chcete použít. Běžný postup je omezit formátování na bezpečné prvky HTML, například `<b>` (tučné) a `<i>` (kurzíva).</p><p>Před zápisem dat do HTML kódování. To způsobí, že by škodlivý skript mohl být zpracován jako text, nikoli jako spustitelný kód.</p><ol><li>Zakázat ověření žádosti ASP.NET přidáním atributu ValidateRequest = "false" do \@ direktivy stránky</li><li>Kódování řetězcového vstupu pomocí metody HtmlEncode</li><li>Použijte StringBuilder a zavolejte metodu Replace pro selektivní odebrání kódování u HTML elementů, které chcete povolit.</li></ol><p>Stránka – v odkazech zakáže ověřování žádostí ASP.NET nastavením `ValidateRequest="false"` . HTML – zakóduje vstup a selektivně povoluje `<b>` a `<i>` případně může být použita také knihovna .NET pro úpravu HTML.</p><p>HtmlSanitizer je knihovna .NET pro čištění fragmentů kódu HTML a dokumentů z konstrukcí, které mohou vést k útokům XSS. Používá AngleSharp k analýze, manipulaci a vykreslování HTML a CSS. HtmlSanitizer se dá nainstalovat jako balíček NuGet a vstup uživatele se dá na straně serveru předávat podle relevantních metod pro úpravu HTML nebo CSS. Mějte na paměti, že úpravu jako řízení zabezpečení byste měli vzít v úvahu jenom jako poslední možnost.</p><p>Ověřování vstupu a kódování výstupu se považují za lepší bezpečnostní prvky.</p> |

## <a name="do-not-assign-dom-elements-to-sinks-that-do-not-have-inbuilt-encoding"></a><a id="inbuilt-encode"></a>Nepřiřazujte elementy modelu DOM k jímky, které nemají sestavené kódování.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | Není k dispozici  |
| **Kroky** | Mnoho funkcí jazyka JavaScript ve výchozím nastavení nepoužívá kódování. Při přiřazování nedůvěryhodného vstupu k prvkům modelu DOM prostřednictvím takových funkcí může dojít k vykonání provádění skriptů skriptování mezi weby (XSS).| 

### <a name="example"></a>Příklad
Níže jsou uvedeny nezabezpečené příklady: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Nepoužívejte `innerHtml` místo toho použijte `innerText` . Podobně místo `$("#elm").html()` použijte `$("#elm").text()` 

## <a name="validate-all-redirects-within-the-application-are-closed-or-done-safely"></a><a id="redirect-safe"></a>Ověření všech přesměrování v rámci aplikace je uzavřeno nebo provedeno bezpečně.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Autorizační rozhraní OAuth 2,0 – otevřené přesměrování](https://tools.ietf.org/html/rfc6749#section-10.15) |
| **Kroky** | <p>Návrh aplikace vyžadující přesměrování do umístění zadaného uživatelem musí omezit možné cíle přesměrování na předdefinovaný seznam webů nebo domén. Všechna přesměrování v aplikaci musí být uzavřená/bezpečná.</p><p>Použijte následující postup:</p><ul><li>Identifikace všech přesměrování</li><li>Implementujte vhodné zmírnění pro každé přesměrování. Vhodná rizika zahrnují přesměrování seznamu povolených uživatelů nebo potvrzení uživatele. Pokud web nebo služba s otevřenou chybou zabezpečení přesměrování používá poskytovatele identity Facebook/OAuth/OpenID, může útočníka ukrást přihlašovací token uživatele a zosobnit tohoto uživatele. Jedná se o rizikové riziko při použití OAuth, které je popsané v dokumentu RFC 6749 "autorizační rozhraní OAuth 2,0", oddíl 10,15 "otevřít přesměrování" podobně, přihlašovací údaje uživatele můžou být ohrožené útoky typu phishing Spear pomocí otevřených přesměrování.</li></ul>|

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-controller-methods"></a><a id="string-method"></a>Implementovat ověřování vstupu pro všechny parametry řetězcového typu akceptované metodami řadiče

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Generic, MVC5, MVC6 |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Ověřování dat modelu v aplikaci MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [Principy identifikátorů guid pro aplikace ASP.NET MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroky** | Pro metody, které pouze akceptují primitivní datový typ a nikoli modely jako argument, by mělo být provedeno ověřování vstupu pomocí regulárního výrazu. Regulární výraz Regex. Match by měl být použit s platným vzorem regulárního výrazu. Pokud vstup neodpovídá zadanému regulárnímu výrazu, ovládací prvek by neměl pokračovat dále a zobrazí se odpovídající upozornění týkající se selhání ověřování.| 

## <a name="set-upper-limit-timeout-for-regular-expression-processing-to-prevent-dos-due-to-bad-regular-expressions"></a><a id="dos-expression"></a>Nastavte časový limit horní meze pro zpracování regulárního výrazu, aby se zabránilo DoS v důsledku chybných regulárních výrazů.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné, webové formuláře, MVC5, MVC6  |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Vlastnost DefaultRegexMatchTimeout](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Kroky** | Aby bylo zajištěno odmítnutí útoků proti chybně vytvořeným regulárním výrazům, které způsobují hodně zpětného navrácení, nastavte globální výchozí časový limit. Pokud doba zpracování trvá déle než definovaný horní limit, vyvolala výjimku Timeout. Pokud není nic nakonfigurované, časový limit by měl být nekonečný.| 

### <a name="example"></a>Příklad
Například následující konfigurace vyvolá RegexMatchTimeoutException, pokud zpracování trvá více než 5 sekund: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a name="avoid-using-htmlraw-in-razor-views"></a><a id="html-razor"></a>Nepoužívejte HTML. Raw v zobrazeních Razor

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC5, MVC6 |
| **Atributy**              | Není k dispozici  |
| **Reference**              | Není k dispozici  |
| Krok | Webové stránky ASP.NET (Razor) provádějí automatické kódování HTML. Všechny řetězce vytištěné pomocí vloženého kódu Nuggets (@ Blocks) jsou automaticky kódovány HTML. Nicméně, pokud `HtmlHelper.Raw` je vyvolána metoda, vrátí kód, který není kódovaný v jazyce HTML. Pokud `Html.Raw()` je použita pomocná metoda, obchází automatickou ochranu kódování, kterou Razor poskytuje.|

### <a name="example"></a>Příklad
Následuje příklad nezabezpečeného: 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Nepoužívejte, `Html.Raw()` Pokud není nutné zobrazit značky. Tato metoda neprovádí implicitní kódování výstupu. Použijte další ASP.NET pomocníky, např. `@Html.DisplayFor()` 

## <a name="do-not-use-dynamic-queries-in-stored-procedures"></a><a id="stored-proc"></a>Nepoužívejte dynamické dotazy v uložených procedurách

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | Není k dispozici  |
| **Kroky** | <p>Útok prostřednictvím injektáže SQL zneužije chyby zabezpečení ve vstupním ověřování a spustí v databázi libovolné příkazy. K tomu může dojít, pokud vaše aplikace používá vstup k vytváření dynamických příkazů SQL pro přístup k databázi. K tomu může dojít také v případě, že váš kód používá uložené procedury, které jsou předány řetězcem, který obsahuje nezpracovaný uživatelský vstup. Pomocí útoku na injektáže SQL může útočník spustit v databázi libovolné příkazy. Všechny příkazy SQL (včetně příkazů SQL v uložených procedurách) musí být parametrizované. Parametrizované příkazy SQL budou přijímat znaky, které mají zvláštní význam pro SQL (například jednoduchá uvozovky) bez problémů, protože jsou silného typu. |

### <a name="example"></a>Příklad
Následuje příklad nezabezpečené dynamické uložené procedury: 

```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteria]
(
  @productName nvarchar(200) = NULL,
  @startPrice float = NULL,
  @endPrice float = NULL
)
AS
 BEGIN
  DECLARE @sql nvarchar(max)
  SELECT @sql = ' SELECT ProductID, ProductName, Description, UnitPrice, ImagePath' +
       ' FROM dbo.Products WHERE 1 = 1 '
       PRINT @sql
  IF @productName IS NOT NULL
     SELECT @sql = @sql + ' AND ProductName LIKE ''%' + @productName + '%'''
  IF @startPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice > ''' + CONVERT(VARCHAR(10),@startPrice) + ''''
  IF @endPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice < ''' + CONVERT(VARCHAR(10),@endPrice) + ''''

  PRINT @sql
  EXEC(@sql)
 END
```

### <a name="example"></a>Příklad
Následuje postup bezpečné implementace stejné uložené procedury: 
```csharp
CREATE PROCEDURE [dbo].[uspGetProductsByCriteriaSecure]
(
             @productName nvarchar(200) = NULL,
             @startPrice float = NULL,
             @endPrice float = NULL
)
AS
       BEGIN
             SELECT ProductID, ProductName, Description, UnitPrice, ImagePath
             FROM dbo.Products where
             (@productName IS NULL or ProductName like '%'+ @productName +'%')
             AND
             (@startPrice IS NULL or UnitPrice > @startPrice)
             AND
             (@endPrice IS NULL or UnitPrice < @endPrice)         
       END
```

## <a name="ensure-that-model-validation-is-done-on-web-api-methods"></a><a id="validation-api"></a>Ujistěte se, že se ověřování modelu provádí na metodách webového rozhraní API.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC5, MVC6 |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Ověření modelu ve webovém rozhraní API ASP.NET](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Kroky** | Když klient odešle data do webového rozhraní API, je povinné ověřit data před jakýmkoli zpracováním. Pro ASP.NET webová rozhraní API, která přijímají modely jako vstup, použijte datové poznámky k modelům pro nastavení ověřovacích pravidel pro vlastnosti modelu.|

### <a name="example"></a>Příklad
Následující kód demonstruje stejné: 

```csharp
using System.ComponentModel.DataAnnotations;

namespace MyApi.Models
{
    public class Product
    {
        public int Id { get; set; }
        [Required]
        [RegularExpression(@"^[a-zA-Z0-9]*$", ErrorMessage="Only alphanumeric characters are allowed.")]
        public string Name { get; set; }
        public decimal Price { get; set; }
        [Range(0, 999)]
        public double Weight { get; set; }
    }
}
```

### <a name="example"></a>Příklad
V metodě Action řadičů rozhraní API musí být platnost modelu explicitně zkontrolována, jak je znázorněno níže: 

```csharp
namespace MyApi.Controllers
{
    public class ProductsController : ApiController
    {
        public HttpResponseMessage Post(Product product)
        {
            if (ModelState.IsValid)
            {
                // Do something with the product (not shown).

                return new HttpResponseMessage(HttpStatusCode.OK);
            }
            else
            {
                return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
            }
        }
    }
}
```

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-web-api-methods"></a><a id="string-api"></a>Implementovat ověřování vstupu pro všechny parametry řetězcového typu akceptované metodami webového rozhraní API

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné, MVC 5, MVC 6 |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Ověřování dat modelu v aplikaci MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [Principy identifikátorů guid pro aplikace ASP.NET MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroky** | Pro metody, které pouze akceptují primitivní datový typ a nikoli modely jako argument, by mělo být provedeno ověřování vstupu pomocí regulárního výrazu. Regulární výraz Regex. Match by měl být použit s platným vzorem regulárního výrazu. Pokud vstup neodpovídá zadanému regulárnímu výrazu, ovládací prvek by neměl pokračovat dále a zobrazí se odpovídající upozornění týkající se selhání ověřování.|

## <a name="ensure-that-type-safe-parameters-are-used-in-web-api-for-data-access"></a><a id="typesafe-api"></a>Zajistěte, aby se pro přístup k datům používaly parametry bezpečné pro typ ve webovém rozhraní API.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | Není k dispozici  |
| **Kroky** | <p>Použijete-li kolekci Parameters, SQL považuje vstup za hodnotu literálu, a ne jako spustitelný kód. Kolekci Parameters lze použít k vynutit omezení typu a délky vstupních dat. Hodnoty mimo rozsah spouštějí výjimku. Pokud se nepoužívají parametry SQL bezpečného typu, útočník může být schopen spustit útoky prostřednictvím injektáže vložené do nefiltrovaného vstupu.</p><p>Při vytváření dotazů SQL použijte parametry typově bezpečné, aby nedocházelo k útokům prostřednictvím injektáže SQL, ke kterým může dojít s nefiltrovaným vstupem. Typ bezpečné parametry můžete použít s uloženými procedurami a s dynamickými příkazy SQL. Parametry jsou považovány za hodnoty literálu v databázi, nikoli jako spustitelný kód. U parametrů se také kontroluje typ a délka.</p>|

### <a name="example"></a>Příklad
Následující kód ukazuje, jak použít parametry typu Safe s SqlParameterCollection při volání uložené procedury. 

```csharp
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter("LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
V předchozím příkladu kódu nemůže být vstupní hodnota delší než 11 znaků. Pokud data neodpovídají typu nebo délce definované parametrem, třída SqlParameter vyvolá výjimku. 

## <a name="use-parameterized-sql-queries-for-cosmos-db"></a><a id="sql-docdb"></a>Použití parametrizovaných dotazů SQL pro Cosmos DB

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Document DB | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Oznamujeme Parametrizace SQL v Azure Cosmos DB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Kroky** | I když Azure Cosmos DB podporuje jenom dotazy jen pro čtení, vkládání SQL je stále možné, pokud jsou dotazy vytvořené zřetězením s uživatelským vstupem. Může být možné, že uživatel získá přístup k datům, ke kterým by neměl přistupovat v rámci stejné kolekce, a to díky tomu, že se zlými úmysly dotazování SQL. Použijte parametrizované dotazy SQL, pokud jsou dotazy vytvořené na základě vstupu uživatele. |

## <a name="wcf-input-validation-through-schema-binding"></a><a id="schema-binding"></a>Ověřování vstupu WCF prostřednictvím vazby schématu

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné, NET Framework 3 |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Kroky** | <p>Nedostatek ověřování vede k různým útokům na vkládání typů.</p><p>Ověřování zpráv představuje jednu linii obrany v ochraně vaší aplikace WCF. Pomocí tohoto přístupu ověříte zprávy pomocí schémat k ochraně operací služby WCF proti útokům prostřednictvím škodlivého klienta. Ověří všechny zprávy, které klient přijal, aby chránil klienta před útoky přes škodlivou službu. Ověřování zpráv umožňuje ověřovat zprávy, když operace spotřebovávají kontrakty zpráv nebo kontrakty dat, které nelze provést pomocí ověření parametru. Ověřování zpráv umožňuje vytvořit logiku ověřování v rámci schémat a zajistit tak větší flexibilitu a zkrátit dobu vývoje. Schémata je možné v rámci organizace znovu použít v různých aplikacích a vytváření standardů pro reprezentace dat. Kromě toho ověřování zpráv umožňuje chránit operace při využívání složitějších datových typů, které zahrnují kontrakty představující obchodní logiku.</p><p>Chcete-li provést ověření zprávy, nejprve Sestavte schéma, které představuje operace vaší služby, a datové typy spotřebované těmito operacemi. Pak vytvoříte třídu .NET, která implementuje vlastní inspektor zprávy klienta a vlastní inspektor zprávy dispečera k ověření zpráv odeslaných/přijatých do/ze služby. Dále implementujete chování vlastního koncového bodu, které umožňuje ověřování zpráv na straně klienta i služby. Nakonec implementujete vlastní prvek konfigurace na třídu, která umožňuje vystavit rozšířené chování koncového bodu v konfiguračním souboru služby nebo klienta. "</p>|

## <a name="wcf--input-validation-through-parameter-inspectors"></a><a id="parameters"></a>WCF – ověřování vstupu prostřednictvím inspektorů parametrů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné, NET Framework 3 |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Kroky** | <p>Ověřování vstupu a dat představuje jednu významnou linii obrany v ochraně vaší aplikace WCF. Měli byste ověřit všechny parametry vystavené v operacích služby WCF, aby služba chránila před útoky ze škodlivého klienta. Naopak byste měli taky ověřit všechny vrácené hodnoty, které klient přijal, aby chránil klienta před útoky přes škodlivou službu.</p><p>Služba WCF poskytuje různé body rozšiřitelnosti, které umožňují přizpůsobit chování modulu runtime WCF vytvořením vlastních rozšíření. Kontrolory zpráv a kontroloři parametrů jsou dva mechanismy rozšíření, které umožňují získat větší kontrolu nad předáváním dat mezi klientem a službou. Měli byste použít inspektory parametrů pro ověřování vstupu a použít nástroje pro kontrolu zpráv pouze v případě, že potřebujete kontrolovat celou zprávu v toku a ze služby.</p><p>Chcete-li provést ověření vstupu, vytvoříte třídu .NET a implementujete vlastní inspektor parametrů, aby bylo možné ověřit parametry operací ve vaší službě. Pak implementujete chování vlastního koncového bodu, aby bylo možné ověřování povolit jak pro klienta, tak pro službu. Nakonec implementujete vlastní prvek konfigurace pro třídu, která umožňuje vystavit rozšířené chování vlastního koncového bodu v konfiguračním souboru služby nebo klienta nástroje.</p>|
