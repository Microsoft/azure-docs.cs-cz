---
title: Ověření vstupu – Nástroj pro modelování hrozeb společnosti Microsoft – Azure | Dokumenty společnosti Microsoft
description: zmírnění hrozeb vystavených v nástroji pro modelování hrozeb
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
ms.openlocfilehash: f443bf3111d2ab97874bdc62ec1370d17e2fc406
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728060"
---
# <a name="security-frame-input-validation--mitigations"></a>Bezpečnostní rámec: Ověření vstupu | Skutečnosti snižující závažnost rizika 
| Produkt/služba | Článek |
| --------------- | ------- |
| **Webová aplikace** | <ul><li>[Zakázání skriptování XSLT pro všechny transformace pomocí nedůvěryhodných šablon stylů](#disable-xslt)</li><li>[Ujistěte se, že každá stránka, která by mohla obsahovat uživatelsky kontrolovatelný obsah, se odhlásí z automatického čichání MIME](#out-sniffing)</li><li>[Zpevnění nebo zakázání rozlišení entit XML](#xml-resolution)</li><li>[Aplikace využívající http.sys provádějí ověření kanonizace url](#app-verification)</li><li>[Ujistěte se, že jsou při přijímání souborů od uživatelů zavedeny příslušné ovládací prvky.](#controls-users)</li><li>[Ujistěte se, že jsou ve webové aplikaci používány parametry zajišťující bezpečnost typů pro přístup k datům.](#typesafe)</li><li>[Použití samostatných tříd vazeb modelu nebo seznamů filtrů vazeb, abyste zabránili chybě zabezpečení přiřazení hmoty MVC](#binding-mvc)</li><li>[Kódování nedůvěryhodného webového výstupu před vykreslováním](#rendering)</li><li>[Provedení ověření vstupu a filtrování všech vlastností modelu typu řetězec](#typemodel)</li><li>[Sanitace by měla být použita na pole formuláře, která přijímají všechny znaky, např.](#richtext)</li><li>[Nepřiřazovat prvky DOM k propadům, které nemají vestavěné kódování](#inbuilt-encode)</li><li>[Ověřit, že všechna přesměrování v rámci aplikace jsou uzavřena nebo provedena bezpečně](#redirect-safe)</li><li>[Implementovat ověření vstupu u všech parametrů typu řetězce přijatých metodami kontroleru](#string-method)</li><li>[Nastavení časového limitu horního limitu pro zpracování regulárních výrazů, aby se zabránilo dos z důvodu chybné regulární výrazy](#dos-expression)</li><li>[Vyhněte se použití Html.Raw v zobrazení Razor](#html-razor)</li></ul> | 
| **Databáze** | <ul><li>[Nepoužívejte dynamické dotazy v uložených procedurách](#stored-proc)</li></ul> |
| **Web API** | <ul><li>[Ujistěte se, že ověření modelu se provádí na metody webového rozhraní API](#validation-api)</li><li>[Implementace ověření vstupu u všech parametrů typu řetězce přijatých metodami webového rozhraní API](#string-api)</li><li>[Ujistěte se, že jsou ve webovém rozhraní API používány parametry zajišťující bezpečnost typů pro přístup k datům.](#typesafe-api)</li></ul> | 
| **Databáze dokumentů Azure** | <ul><li>[Použití parametrizovaných dotazů SQL pro Azure Cosmos DB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[Ověřování vstupu WCF prostřednictvím vazby schématu](#schema-binding)</li><li>[WCF - Ověření vstupu prostřednictvím inspektorů parametrů](#parameters)</li></ul> |

## <a name="disable-xslt-scripting-for-all-transforms-using-untrusted-style-sheets"></a><a id="disable-xslt"></a>Zakázání skriptování XSLT pro všechny transformace pomocí nedůvěryhodných šablon stylů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Zabezpečení XSLT](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [vlastnost XsltSettings.EnableScript](https://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Kroky** | XSLT podporuje skriptování uvnitř `<msxml:script>` šablon stylů pomocí prvku. To umožňuje vlastní funkce, které mají být použity v transformaci XSLT. Skript je spuštěn v kontextu procesu provádění transformace. Skript XSLT musí být zakázán v nedůvěryhodném prostředí, aby se zabránilo spuštění nedůvěryhodného kódu. *Pokud používáte rozhraní .NET:* Skriptování XSLT je ve výchozím nastavení zakázáno. však je nutné zajistit, že nebylexplicitně povolena prostřednictvím vlastnosti. `XsltSettings.EnableScript`|

### <a name="example"></a>Příklad 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Příklad
Pokud používáte jazyk MSXML 6.0, je skriptování XSLT ve výchozím nastavení zakázáno. je však nutné zajistit, aby nebyl explicitně povolen prostřednictvím vlastnosti objektu XML DOM AllowXsltScript. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Příklad
Pokud používáte msxml 5 nebo nižší, je skriptování XSLT ve výchozím nastavení povoleno a je nutné jej explicitně zakázat. Nastavte vlastnost objektu XML DOM AllowXsltScript na hodnotu false. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a name="ensure-that-each-page-that-could-contain-user-controllable-content-opts-out-of-automatic-mime-sniffing"></a><a id="out-sniffing"></a>Ujistěte se, že každá stránka, která by mohla obsahovat uživatelsky kontrolovatelný obsah, se odhlásí z automatického čichání MIME

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [IE8 Bezpečnostní část V - Komplexní ochrana](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)  |
| **Kroky** | <p>Pro každou stránku, která by mohla obsahovat uživatelsky kontrolovatelný obsah, je nutné použít hlavičku `X-Content-Type-Options:nosniff`HTTP . Chcete-li tento požadavek splnit, můžete buď nastavit požadovanou stránku záhlaví po stránce pouze pro stránky, které mohou obsahovat obsah, který lze ovládat uživatelem, nebo jej můžete nastavit globálně pro všechny stránky v aplikaci.</p><p>Každý typ souboru dodaného z webového serveru má přidružený [typ MIME](https://en.wikipedia.org/wiki/Mime_type) (nazývaný také *typ obsahu),* který popisuje povahu obsahu (tj. obrázek, text, aplikaci atd.)</p><p>Hlavička X-Content-Type-Options je hlavička HTTP, která vývojářům umožňuje určit, že jejich obsah by neměl být mime-sniffed. Tato hlavička je určena ke zmírnění útoků MIME-sniffing. Podpora pro toto záhlaví byla přidána v aplikaci Internet Explorer 8 (IE8)</p><p>Z možností X-Content-Type-Options budou mít prospěch pouze uživatelé aplikace Internet Explorer 8 (IE8). Předchozí verze aplikace Internet Explorer v současné době nerespektují záhlaví X-Content-Type-Options</p><p>Internet Explorer 8 (a novější) jsou pouze hlavní prohlížeče implementovat MIME-sniffing opt-out funkce. Pokud a kdy ostatní hlavní prohlížeče (Firefox, Safari, Chrome) implementují podobné funkce, bude toto doporučení aktualizováno tak, aby zahrnovalo syntaxi pro tyto prohlížeče.</p>|

### <a name="example"></a>Příklad
Chcete-li povolit požadované záhlaví globálně pro všechny stránky v aplikaci, můžete provést jednu z následujících akcí: 

* Přidání záhlaví do souboru web.config, pokud je aplikace hostována Internetovou informační službou (IIS) 7 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* Přidání záhlaví prostřednictvím\_globálního požadavku BeginRequest aplikace 

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

* Požadované záhlaví můžete povolit pouze pro určité stránky přidáním jednotlivých odpovědí: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a name="harden-or-disable-xml-entity-resolution"></a><a id="xml-resolution"></a>Zpevnění nebo zakázání rozlišení entit XML

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Rozšíření entit XML](https://capec.mitre.org/data/definitions/197.html), [Útoky a obrana odmítnutí služby XML](https://msdn.microsoft.com/magazine/ee335713.aspx), Přehled zabezpečení [msxml](https://msdn.microsoft.com/library/ms754611(v=VS.85).aspx), [doporučené postupy pro zabezpečení kódu MSXML](https://msdn.microsoft.com/library/ms759188(VS.85).aspx), Odkaz na protokol [NSXMLParserDelegate ,](https://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html)Řešení [externích odkazů](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Kroky**| <p>Ačkoli není široce používán, existuje funkce XML, která umožňuje analyzátoru XML rozbalit entity maker s hodnotami definovanými buď v samotném dokumentu, nebo z externích zdrojů. Dokument může například definovat entitu "název společnosti" s hodnotou Microsoft,&companyname;takže pokaždé, když se v dokumentu zobrazí text " ", bude automaticky nahrazen textem Microsoft. Nebo dokument může definovat entitu "MSFTStock", která odkazuje na externí webovou službu pro načtení aktuální hodnoty akcií společnosti Microsoft.</p><p>Poté se&MSFTStock;v dokumentu objeví " " , je automaticky nahrazen aktuální cenou akcií. Tato funkce však může být zneužita k vytvoření podmínek odmítnutí služby (DoS). Útočník může vnořit více entit a vytvořit exponenciální rozšiřující bombu XML, která spotřebovává veškerou dostupnou paměť v systému. </p><p>Alternativně může vytvořit externí odkaz, který streamuje zpět nekonečné množství dat nebo který jednoduše zablokuje vlákno. V důsledku toho musí všechny týmy zcela zakázat interní nebo externí rozlišení entit XML, pokud je jejich aplikace nepoužívá, nebo ručně omezit množství paměti a čas, který aplikace může spotřebovat pro rozlišení entity, pokud je tato funkce naprosto nezbytná. Pokud aplikace nevyžaduje rozlišení entity, zakažte ji. </p>|

### <a name="example"></a>Příklad
Pro kód rozhraní .NET Framework můžete použít následující přístupy:

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
Všimněte si, `ProhibitDtd` že `XmlReaderSettings` výchozí hodnota `XmlTextReader` in je true, ale v něm je false. Pokud používáte XmlReaderSettings, není nutné explicitně nastavit ProhibitDtd na true, ale z bezpečnostních důvodů, které provádíte. Všimněte si také, že třída XmlDocument umožňuje ve výchozím nastavení rozlišení entit. 

### <a name="example"></a>Příklad
Chcete-li zakázat rozlišení entit pro XmlDocuments, použijte `XmlDocument.Load(XmlReader)` přetížení metody Load a nastavte příslušné vlastnosti v argumentu XmlReader, abyste zakázali rozlišení, jak je znázorněno v následujícím kódu: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Příklad
Pokud zakázání rozlišení entity není možné pro vaši aplikaci, nastavte XmlReaderSettings.MaxCharactersFromEntities vlastnost přiměřenou hodnotu podle potřeb vaší aplikace. Tím se omezí dopad potenciálních exponenciálních expanzních útoků DoS. Následující kód poskytuje příklad tohoto přístupu: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Příklad
Pokud potřebujete vyřešit včleněné entity, ale nepotřebujete přeložit externí entity, nastavte vlastnost XmlReaderSettings.XmlResolver na hodnotu null. Například: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Všimněte si, že v MSXML6, ProhibitDTD je nastavena na hodnotu true (zakázání zpracování DTD) ve výchozím nastavení. Pro kód Apple OSX/iOS můžete použít dva analyzátory XML: NSXMLParser a libXML2. 

## <a name="applications-utilizing-httpsys-perform-url-canonicalization-verification"></a><a id="app-verification"></a>Aplikace využívající http.sys provádějí ověření kanonizace url

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>Každá aplikace, která používá http.sys by měly dodržovat tyto pokyny:</p><ul><li>Omezte délku adresy URL na maximálně 16 384 znaků (ASCII nebo Unicode). Toto je absolutní maximální délka adresy URL založená na výchozím nastavení Internetové informační služby (IIS) 6. Webové stránky by měly usilovat o kratší délku, než je to, pokud je to možné</li><li>Použijte standardní vstupně-to-v. třídy souboru rozhraní .NET Framework (například FileStream), protože budou využívat pravidla kanonizace v rozhraní .NET FX</li><li>Explicitně sestavit seznam známých názvů souborů</li><li>Explicitně odmítnout známé typy souborů nebudete sloužit UrlScan odmítá: exe, bat, cmd, com, htw, ida, idq, htr, idc, shtm[l], stm, tiskárna, ini, pol, dat files</li><li>Zachyťte následující výjimky:<ul><li>System.ArgumentException (pro názvy zařízení)</li><li>System.NotSupportedException (pro datové proudy)</li><li>System.IO.FileNotFoundException (pro neplatné názvy uvozevačů)</li><li>System.IO.DirectoryNotFoundException (pro neplatné uvozené dirs)</li></ul></li><li>*Nevolat* na win32 soubor vstupně-v., vstupně-v. API. Na neplatnou adresu URL řádně vrátit chybu 400 uživateli a protokolovat skutečnou chybu.</li></ul>|

## <a name="ensure-appropriate-controls-are-in-place-when-accepting-files-from-users"></a><a id="controls-users"></a>Ujistěte se, že jsou při přijímání souborů od uživatelů zavedeny příslušné ovládací prvky.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Neomezené nahrávání souborů](https://www.owasp.org/index.php/Unrestricted_File_Upload), [tabulka podpisů souborů](https://www.garykessler.net/library/file_sigs.html) |
| **Kroky** | <p>Nahrané soubory představují významné riziko pro aplikace.</p><p>Prvním krokem v mnoha útocích je získat nějaký kód do systému, který má být napaden. Pak útok potřebuje pouze najít způsob, jak získat kód spuštěn. Použití nahrání souboru pomáhá útočníkovi provést první krok. Důsledky neomezeného nahrávání souborů se mohou lišit, včetně úplného převzetí systému, přetíženého systému souborů nebo databáze, předávání útoků do back-endových systémů a jednoduchého znehodnocení.</p><p>Záleží na tom, co aplikace dělá s nahraným souborem a zejména tam, kde je uložen. Chybí ověření na straně serveru pro nahrávání souborů. Pro funkci nahrávání souborů by měly být implementovány následující ovládací prvky zabezpečení:</p><ul><li>Kontrola přípona souboru (měla by být přijata pouze platná sada povoleného typu souboru)</li><li>Maximální limit velikosti souboru</li><li>Soubor by neměl být nahrán do webroot; umístění by mělo být adresář na non-systémové jednotky</li><li>Je třeba dodržovat konvence pojmenování tak, aby nahraný název souboru měl určitou náhodnost, aby se zabránilo přepsání souborů.</li><li>Soubory by měly být před zápisem na disk zkontrolovány na ochranu proti virům.</li><li>Ujistěte se, že název souboru a všechna další metadata (např. cesta k souboru) jsou ověřena pro škodlivé znaky</li><li>Je třeba zkontrolovat podpis ve formátu souboru, aby uživatel nemohl nahrát maskovaný soubor (např. nahrání souboru exe změnou přípony na txt)</li></ul>| 

### <a name="example"></a>Příklad
Poslední bod týkající se ověření podpisu ve formátu souboru naleznete v následující třídě podrobnosti: 

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

## <a name="ensure-that-type-safe-parameters-are-used-in-web-application-for-data-access"></a><a id="typesafe"></a>Ujistěte se, že jsou ve webové aplikaci používány parametry zajišťující bezpečnost typů pro přístup k datům.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>Pokud použijete parametry kolekce, SQL zachází vstup je jako literál hodnotu spíše než jako spustitelný kód. Kolekce Parametry lze vynutit omezení typu a délky na vstupní data. Hodnoty mimo rozsah aktivují výjimku. Pokud nejsou použity parametry SQL bezpečné pro daný typ, útočníci mohou být schopni provést injektážní útoky, které jsou vloženy do nefiltrovaného vstupu.</p><p>Při vytváření dotazů SQL používejte parametry typu safe, abyste se vyhnuli možným útokům injektáže SQL, ke kterým může dojít s nefiltrovaným vstupem. Můžete použít typ bezpečné parametry s uloženými procedurami a s dynamickými příkazy SQL. Parametry jsou považovány za literálové hodnoty databáze a nikoli jako spustitelný kód. Parametry jsou také kontrolovány pro typ a délku.</p>|

### <a name="example"></a>Příklad 
Následující kód ukazuje, jak používat typ bezpečné parametry s SqlParameterCollection při volání uložené procedury. 

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
V předchozím příkladu kódu nesmí být vstupní hodnota delší než 11 znaků. Pokud data neodpovídá typu nebo délce definované parametrem, třída SqlParameter vyvolá výjimku. 

## <a name="use-separate-model-binding-classes-or-binding-filter-lists-to-prevent-mvc-mass-assignment-vulnerability"></a><a id="binding-mvc"></a>Použití samostatných tříd vazeb modelu nebo seznamů filtrů vazeb, abyste zabránili chybě zabezpečení přiřazení hmoty MVC

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC5, MVC6 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Atributy metadat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute), [Chyba zabezpečení veřejného klíče a zmírnění rizik](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation), Kompletní průvodce [hromadným přiřazením v ASP.NET MVC](https://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [Začínáme s EF pomocí MVC](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Kroky** | <ul><li>**Kdy bych měl hledat chyby zabezpečení nadměrného vysílání?** Chyby zabezpečení při nadměrném účtování mohou nastat na libovolném místě, které svážete třídy modelu ze vstupu uživatele. Architektury jako MVC mohou představovat uživatelská data ve vlastních třídách .NET, včetně prostých starých objektů CLR (POCO). MVC automaticky vyplní tyto třídy modelu s daty z požadavku, poskytuje pohodlné reprezentace pro práci s vstupem uživatele. Pokud tyto třídy obsahují vlastnosti, které by neměly být nastaveny uživatelem, aplikace může být ohrožena útoky nadměrného účtování, které umožňují uživatelské řízení dat, která aplikace nikdy nezamýšlela. Stejně jako vazba modelu MVC, technologie přístupu k databázi, jako jsou objekt/relační mapovače, jako je Entity Framework, často také podporují použití objektů POCO k reprezentaci databázových dat. Tyto třídy datového modelu poskytují stejné pohodlí při práci s daty databáze jako MVC při práci s vstupem uživatele. Vzhledem k tomu, že MVC a databáze podporují podobné modely, jako jsou objekty POCO, zdá se snadné znovu použít stejné třídy pro oba účely. Tento postup nezachová oddělení obavy a je jedna společná oblast, kde jsou nezamýšlené vlastnosti vystaveny vazby modelu, povolení over-posting útoky.</li><li>**Proč bych neměl používat své nefiltrované třídy modelu databáze jako parametry pro mé akce MVC?** Protože vazba modelu MVC bude vázat cokoliv v této třídě. I v případě, že data se nezobrazí ve vašem zobrazení, uživatel se zlými úmysly můžete odeslat požadavek HTTP s tato data zahrnuty a MVC bude rádi svázat, protože vaše akce říká, že třída databáze je tvar dat, které by měl přijmout pro vstup uživatele.</li><li>**Proč bych se měl starat o tvar používaný pro modelovou vazbu?** Použití ASP.NET vazbě modelu MVC s příliš širokými modely zpřístupňuje aplikaci k útokům nadměrného účtování. Nadměrné zaúčtování by mohlo útočníkům umožnit změnit data aplikací nad rámec toho, co vývojář zamýšlel, například přepsání ceny za položku nebo oprávnění zabezpečení pro účet. Aplikace by měly používat modely vazeb specifických pro akci (nebo seznamy filtrů určitých povolených vlastností) k poskytnutí explicitní smlouvy pro nedůvěryhodný vstup, který má být povolen prostřednictvím vazby modelu.</li><li>**Je mít samostatné vazby modely jen duplikaci kódu? -** Ne, je to otázka oddělení obav. Pokud znovu použijete databázové modely v metodách akce, říkáte, že všechny vlastnosti (nebo dílčí vlastnosti) v této třídě mohou být nastaveny uživatelem v požadavku HTTP. Pokud to není to, co chcete, aby MVC dělat, budete potřebovat seznam filtrů nebo samostatné třídy obrazec ukázat MVC, jaká data mohou pocházet z uživatelského vstupu místo.</li><li>**Pokud mám samostatné modely vazeb pro vstup uživatele, musím duplikovat všechny atributy anotace dat? -** Ne nutně. Atribut MetadataTypeAttribute ve třídě modelu databáze můžete použít k propojení s metadaty ve třídě vazby modelu. Jen si všimněte, že typ odkazovaný MetadataTypeAttribute musí být podmnožinou typu odkazování (může mít méně vlastností, ale ne více).</li><li>**Přesouvání dat tam a zpět mezi modely vstupu uživatele a databázové modely je únavné. Mohu jen zkopírovat všechny vlastnosti pomocí reflexe? -** Ano, je to tak. Jediné vlastnosti, které se zobrazí v modelech vazby jsou ty, které jste zjistili, že jsou bezpečné pro vstup uživatele. Neexistuje žádný důvod zabezpečení, který brání použití reflexe ke kopírování přes všechny vlastnosti, které existují společné mezi těmito dvěma modely.</li><li>**A co [Bind(Vyloučit ="â € |")]. Mohu použít, že místo toho, aby samostatné vazby modely? -** Tento přístup se nedoporučuje. Použití [Bind(Exclude ="â € |")] znamená, že všechny nové vlastnosti je bindable ve výchozím nastavení. Když je přidána nová vlastnost, je další krok k zapamatování, aby věci zabezpečené, spíše než mít návrh být bezpečné ve výchozím nastavení. V závislosti na vývojáře kontroly tohoto seznamu pokaždé, když je přidána vlastnost je riskantní.</li><li>**Je [Bind(Include ="â € |")] užitečné pro operace úprav? -** Ne. [Bind(Include ="â € |")] je vhodný pouze pro operace ve stylu INSERT (přidávání nových dat). Pro operace ve stylu UPDATE (revisuje existující data) použijte jiný přístup, jako je mít samostatné modely vazby nebo předávání explicitní seznam povolených vlastností UpdateModel nebo TryUpdateModel. Přidání atributu [Bind(Include ="â €¦")] v operaci Úpravy znamená, že MVC vytvoří instanci objektu a nastaví pouze uvedené vlastnosti, přičemž všechny ostatní zůstanou na výchozích hodnotách. Pokud jsou data trvalá, zcela nahradí existující entitu a resetuje hodnoty všech vynechaných vlastností na výchozí hodnoty. Například pokud IsAdmin byl vynechán z [Bind(Include ="â € |")] ] atribut na operaci upravit, každý uživatel, jehož jméno bylo upraveno prostřednictvím této akce by se obnovit na IsAdmin = false (každý upravený uživatel ztratí stav správce). Pokud chcete zabránit aktualizacím určitých vlastností, použijte jeden z výše uvedených přístupů. Všimněte si, že některé verze nástrojů MVC generovat třídy řadiče s [Bind(Include ="â € |")] na upravit akce a znamenat, že odebrání vlastnosti z tohoto seznamu zabrání over-posting útoky. Nicméně, jak je popsáno výše, tento přístup nefunguje tak, jak bylo zamýšleno a místo toho obnoví všechna data v vynechaných vlastnostech na jejich výchozí hodnoty.</li><li>**Pro vytvoření operací, existují nějaké upozornění pomocí [Bind(Include ="â € |")] spíše než samostatné vazby modely? -** Ano. Za prvé tento přístup nefunguje pro scénáře úprav, které vyžadují zachování dvou samostatných přístupů pro zmírnění všech chyb zabezpečení over-posting. Za druhé, samostatné modely vazby vynucují oddělení obav mezi obrazec použitý pro vstup uživatele a obrazec použitý pro trvalost, něco [Bind(Include ="â € |")] neprovede. Za třetí, všimněte si, že [Bind(Include ="â € |")] může zpracovávat pouze vlastnosti nejvyšší úrovně; v atributu nelze povolit pouze části dílčích vlastností (například "Details.Name"). Nakonec a možná nejdůležitější, pomocí [Bind(Include ="â € |")] přidá další krok, který musí být zapamatován pokaždé, když se třída používá pro vazbu modelu. Pokud se nová metoda akce váže přímo na třídu dat a zapomene zahrnout atribut [Bind(Include ="â €¦")], může být zranitelná vůči útokům nadměrného účtování, takže přístup [Bind(Include ="â €¦")] je ve výchozím nastavení poněkud méně bezpečný. Pokud používáte [Bind(Include ="â € |")], dbejte vždy na to, abyste je zadali pokaždé, když se vaše třídy dat zobrazí jako parametry metody akce.</li><li>**Pro operace vytvořit, co uvedení [Bind(Include ="â € |")] atribut na samotné třídě modelu? Nevyhýbá se tento přístup nutnosti pamatovat si, že atribut je uveden na každé metodě akce? -** Tento přístup funguje v některých případech. Použití [Bind(Include ="â € |")] na samotném typu modelu (spíše než na parametrech akce pomocí této třídy), se vyhnete nutnosti zahrnout atribut [Bind(Include ="â €¦")] na každou metodu akce. Pomocí atributu přímo na třídu efektivně vytvoří samostatnou plochu této třídy pro účely vazby modelu. Tento přístup však umožňuje pouze pro jeden tvar vazby modelu na třídu modelu. Pokud jedna metoda akce potřebuje povolit vazbu modelu pole (například akce pouze pro správce, která aktualizuje role uživatelů) a další akce musí zabránit vazbě modelu tohoto pole, tento přístup nebude fungovat. Každá třída může mít pouze jeden tvar vazby modelu; Pokud různé akce potřebují různé tvary vazby modelu, musí reprezentovat tyto samostatné tvary pomocí samostatných tříd vazby modelu nebo samostatných atributů [Bind(Include ="â €¦")] na metody akce.</li><li>**Co jsou vázací modely? Jsou totéž jako zobrazit modely? -** To jsou dva související pojmy. Termín vazba model odkazuje na třídu modelu použitou v seznamu parametrů akce (tvar předaný z vazby modelu MVC na metodu akce). Model zobrazení termínodkazuje na třídu modelu předanou z metody akce do pohledu. Použití modelu specifického pro zobrazení je běžný přístup pro předávání dat z metody akce do zobrazení. Tento tvar je často vhodný i pro vazbu modelu a termín zobrazit model lze použít k odkazování na stejný model, který se používá na obou místech. Abych byl přesný, tento postup hovoří konkrétně o závazných modelech se zaměřením na tvar předaných akci, což je to, co je důležité pro účely hromadného postoupení.</li></ul>| 

## <a name="encode-untrusted-web-output-prior-to-rendering"></a><a id="rendering"></a>Kódování nedůvěryhodného webového výstupu před vykreslováním

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné, webové formuláře, MVC5, MVC6 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Jak zabránit cross-site skriptování v ASP.NET](https://msdn.microsoft.com/library/ms998274.aspx), [Cross-site Scripting](https://cwe.mitre.org/data/definitions/79.html), [XSS (Cross Site Scripting) Prevence Cheat Sheet](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) |
| **Kroky** | Skriptování mezi weby (obvykle zkráceně XSS) je vektor útoku pro online služby nebo jakoukoli aplikaci/komponentu, která spotřebovává vstup z webu. Chyby zabezpečení xss mohou útočníkovi umožnit spouštět skript v počítači jiného uživatele prostřednictvím zranitelné webové aplikace. Škodlivé skripty mohou být použity ke krádeži cookies a jinak manipulovat s počítačem oběti prostřednictvím JavaScriptu. XSS je zabráněno ověřením vstupu uživatele, zajištění, že je dobře tvarované a kódování před tím, než je vykreslen na webové stránce. Vstupní ověření a kódování výstupu lze provést pomocí knihovny webové ochrany. Pro spravovaný kód (C,\#VB.NET atd.) použijte jednu nebo více vhodných metod kódování z knihovny Webové ochrany (Anti-XSS) v závislosti na kontextu, ve kterém se projeví vstup uživatele:| 

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

## <a name="perform-input-validation-and-filtering-on-all-string-type-model-properties"></a><a id="typemodel"></a>Provedení ověření vstupu a filtrování všech vlastností modelu typu řetězec

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecný, MVC5, MVC6 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Přidání validace](https://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [ověřování dat modelu v aplikaci MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), hlavní [zásady pro vaše ASP.NET aplikace MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroky** | <p>Všechny vstupní parametry musí být ověřeny před jejich použitím v aplikaci, aby bylo zajištěno, že aplikace je chráněna proti vstupům uživatelů se zlými úmysly. Ověřte vstupní hodnoty pomocí ověření regulárních výrazů na straně serveru pomocí strategie ověření seznamu povolených serverů. Nedezinfikované uživatelské vstupy / parametry předané metodám mohou způsobit chyby zabezpečení vkládání kódu.</p><p>Pro webové aplikace mohou vstupní body zahrnovat také pole formuláře, řetězce querystringů, soubory cookie, hlavičky HTTP a parametry webové služby.</p><p>Následující kontroly ověření vstupu musí být provedeny na vazbě modelu:</p><ul><li>Vlastnosti modelu by měly být anotovány s poznámkou RegularExpression pro přijetí povolených znaků a maximální přípustné délky.</li><li>Metody řadiče by měly provádět platnost ModeluState</li></ul>|

## <a name="sanitization-should-be-applied-on-form-fields-that-accept-all-characters-eg-rich-text-editor"></a><a id="richtext"></a>Sanitace by měla být použita na pole formuláře, která přijímají všechny znaky, např.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Zakódovat nebezpečný vstup](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3), [HTML sanitizer](https://github.com/mganss/HtmlSanitizer) |
| **Kroky** | <p>Identifikujte všechny statické značky, které chcete použít. Běžnou praxí je omezit formátování na bezpečné prvky HTML, například `<b>` (tučné) a `<i>` (kurzíva).</p><p>Před zápisem dat je kódujte ve formátu HTML. To umožňuje jakýkoli škodlivý skript bezpečné tím, že způsobí, že je zpracován jako text, nikoli jako spustitelný kód.</p><ol><li>Zakázat ověření žádosti o ASP.NET přidáním atributu \@ ValidateRequest="false" do direktivy Page</li><li>Zakódovat vstup řetězce metodou HtmlEncode</li><li>Pomocí StringBuilder a volání jeho Nahradit metoda selektivně odebrat kódování na prvky HTML, které chcete povolit</li></ol><p>Stránka-v odkazy zakáže ověření ASP.NET `ValidateRequest="false"`požadavku nastavením . To HTML-kóduje vstup a selektivně umožňuje `<b>` a `<i>` alternativně, .NET knihovna pro html sanitace může být také použit.</p><p>HtmlSanitizer je knihovna .NET pro čištění fragmentů HTML a dokumentů z konstrukcí, které mohou vést k útokům XSS. Používá AngleSharp analyzovat, manipulovat a vykreslovat HTML a CSS. HtmlSanitizer lze nainstalovat jako balíček NuGet a vstup uživatele může být předán prostřednictvím příslušných metod sanitace HTML nebo CSS, podle potřeby na straně serveru. Vezměte prosím na vědomí, že sanitizace jako bezpečnostní kontrola by měla být považována pouze za poslední možnost.</p><p>Vstupní ověření a kódování výstupu jsou považovány za lepší ovládací prvky zabezpečení.</p> |

## <a name="do-not-assign-dom-elements-to-sinks-that-do-not-have-inbuilt-encoding"></a><a id="inbuilt-encode"></a>Nepřiřazovat prvky DOM k propadům, které nemají vestavěné kódování

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Mnoho funkcí javascriptu ve výchozím nastavení nekóduje. Při přiřazování nedůvěryhodný vstup do dom prvky prostřednictvím těchto funkcí, může mít za následek spuštění skriptu mezi lokalitami (XSS).| 

### <a name="example"></a>Příklad
Následují nezabezpečené příklady: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Nepoužívejte `innerHtml`; místo `innerText`toho použijte . Podobně místo `$("#elm").html()`, použijte`$("#elm").text()` 

## <a name="validate-all-redirects-within-the-application-are-closed-or-done-safely"></a><a id="redirect-safe"></a>Ověřit, že všechna přesměrování v rámci aplikace jsou uzavřena nebo provedena bezpečně

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Autorizační rámec OAuth 2.0 - Otevřené přeředitelů](https://tools.ietf.org/html/rfc6749#section-10.15) |
| **Kroky** | <p>Návrh aplikace vyžadující přesměrování do umístění zadaného uživatelem musí omezit možné cíle přesměrování na předdefinovaný "bezpečný" seznam sítí nebo domén. Všechna přesměrování v aplikaci musí být uzavřena/bezpečná.</p><p>Použijte následující postup:</p><ul><li>Identifikovat všechna přesměrování</li><li>Implementujte vhodné zmírnění pro každé přesměrování. Mezi příslušné skutečnosti snižující závažnost rizika patří seznam povolených přesměrování nebo potvrzení uživatele. Pokud web nebo služba s otevřenou chybou zabezpečení přesměrování využívá zprostředkovatele identity Facebook/OAuth/OpenID, může útočník ukrást přihlašovací token uživatele a zosobnit tohoto uživatele. To je inherentní riziko při použití OAuth, který je dokumentován v RFC 6749 "OAuth 2.0 Authorization Framework", Oddíl 10.15 "Open Redirects" Podobně mohou být přihlašovací údaje uživatelů ohroženy phishingovými útoky s kopím pomocí otevřených přesměrování</li></ul>|

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-controller-methods"></a><a id="string-method"></a>Implementovat ověření vstupu u všech parametrů typu řetězce přijatých metodami kontroleru

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecný, MVC5, MVC6 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Ověření modelových dat v aplikaci MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [hlavní zásady pro vaše ASP.NET aplikace MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroky** | Pro metody, které pouze přijmout primitivní datový typ a ne modely jako argument, vstupní ověření pomocí regulárního výrazu by mělo být provedeno. Zde Regex.IsMatch by měl být použit s platným vzorem regulární výraz. Pokud vstup neodpovídá zadanému regulárnímu výrazu, ovládací prvek by neměl pokračovat dále a mělo by se zobrazit odpovídající upozornění týkající se selhání ověření.| 

## <a name="set-upper-limit-timeout-for-regular-expression-processing-to-prevent-dos-due-to-bad-regular-expressions"></a><a id="dos-expression"></a>Nastavení časového limitu horního limitu pro zpracování regulárních výrazů, aby se zabránilo dos z důvodu chybné regulární výrazy

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné, webové formuláře, MVC5, MVC6  |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Vlastnost DefaultRegexMatchTimeout](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Kroky** | Chcete-li zajistit útoky odmítnutí služby proti špatně vytvořeným regulárním výrazům, které způsobují velké množství zpětných navracení, nastavte globální výchozí časový limit. Pokud doba zpracování trvá déle, než je definovaný horní limit, vyvolá výjimku časového limitu. Pokud není nic nakonfigurováno, časový čas by byl nekonečný.| 

### <a name="example"></a>Příklad
Například následující konfigurace vyvolá RegexMatchTimeoutException, pokud zpracování trvá déle než 5 sekund: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a name="avoid-using-htmlraw-in-razor-views"></a><a id="html-razor"></a>Vyhněte se použití Html.Raw v zobrazení Razor

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC5, MVC6 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| Krok | ASP.NET webových stránek (Razor) provádí automatické kódování HTML. Všechny řetězce vytištěné vloženými kódovými kostkami (@ blocks) jsou automaticky kódovány ve formátu HTML. Však `HtmlHelper.Raw` při method je vyvolána, vrátí značky, které není kódován HTML. Pokud `Html.Raw()` je použita pomocná metoda, obchází ochranu automatického kódování, kterou poskytuje Razor.|

### <a name="example"></a>Příklad
Následuje nezabezpečený příklad: 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Nepoužívejte, `Html.Raw()` pokud nepotřebujete zobrazit značky. Tato metoda neprovádí implicitní kódování výstupu. Používejte jiné ASP.NET pomocníky např.`@Html.DisplayFor()` 

## <a name="do-not-use-dynamic-queries-in-stored-procedures"></a><a id="stored-proc"></a>Nepoužívejte dynamické dotazy v uložených procedurách

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>Útok injektáží SQL využívá chyby zabezpečení při ověřování vstupů ke spuštění libovolných příkazů v databázi. Může dojít, když vaše aplikace používá vstup pro vytvoření dynamické příkazy SQL pro přístup k databázi. Může také dojít, pokud váš kód používá uložené procedury, které jsou předány řetězce, které obsahují nezpracovaná vstup uživatele. Pomocí útoku injektáží SQL může útočník provádět libovolné příkazy v databázi. Všechny příkazy SQL (včetně příkazů SQL v uložených procedurách) musí být parametrizovány. Parametrizované příkazy SQL budou přijímat znaky, které mají zvláštní význam pro SQL (jako je jedna uvozovka) bez problémů, protože jsou silně zadány. |

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
Následuje stejná uložená procedura implementovaná bezpečně: 
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

## <a name="ensure-that-model-validation-is-done-on-web-api-methods"></a><a id="validation-api"></a>Ujistěte se, že ověření modelu se provádí na metody webového rozhraní API

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC5, MVC6 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Ověření modelu v ASP.NET webovérozhraní API](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Kroky** | Když klient odesílá data do webového rozhraní API, je povinné ověřit data před provedením jakékoli zpracování. Pro ASP.NET webová rozhraní API, která přijímají modely jako vstup, použijte datové poznámky na modelech k nastavení ověřovacích pravidel pro vlastnosti modelu.|

### <a name="example"></a>Příklad
Následující kód ukazuje totéž: 

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
V akční metodě řadičů rozhraní API musí být platnost modelu explicitně zkontrolována, jak je znázorněno níže: 

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

## <a name="implement-input-validation-on-all-string-type-parameters-accepted-by-web-api-methods"></a><a id="string-api"></a>Implementace ověření vstupu u všech parametrů typu řetězce přijatých metodami webového rozhraní API

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecný, MVC 5, MVC 6 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Ověření modelových dat v aplikaci MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [hlavní zásady pro vaše ASP.NET aplikace MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroky** | Pro metody, které pouze přijmout primitivní datový typ a ne modely jako argument, vstupní ověření pomocí regulárního výrazu by mělo být provedeno. Zde Regex.IsMatch by měl být použit s platným vzorem regulární výraz. Pokud vstup neodpovídá zadanému regulárnímu výrazu, ovládací prvek by neměl pokračovat dále a mělo by se zobrazit odpovídající upozornění týkající se selhání ověření.|

## <a name="ensure-that-type-safe-parameters-are-used-in-web-api-for-data-access"></a><a id="typesafe-api"></a>Ujistěte se, že jsou ve webovém rozhraní API používány parametry zajišťující bezpečnost typů pro přístup k datům.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>Pokud použijete parametry kolekce, SQL zachází vstup je jako literál hodnotu spíše než jako spustitelný kód. Kolekce Parametry lze vynutit omezení typu a délky na vstupní data. Hodnoty mimo rozsah aktivují výjimku. Pokud nejsou použity parametry SQL bezpečné pro daný typ, útočníci mohou být schopni provést injektážní útoky, které jsou vloženy do nefiltrovaného vstupu.</p><p>Při vytváření dotazů SQL používejte parametry typu safe, abyste se vyhnuli možným útokům injektáže SQL, ke kterým může dojít s nefiltrovaným vstupem. Můžete použít typ bezpečné parametry s uloženými procedurami a s dynamickými příkazy SQL. Parametry jsou považovány za literálové hodnoty databáze a nikoli jako spustitelný kód. Parametry jsou také kontrolovány pro typ a délku.</p>|

### <a name="example"></a>Příklad
Následující kód ukazuje, jak používat typ bezpečné parametry s SqlParameterCollection při volání uložené procedury. 

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
V předchozím příkladu kódu nesmí být vstupní hodnota delší než 11 znaků. Pokud data neodpovídá typu nebo délce definované parametrem, třída SqlParameter vyvolá výjimku. 

## <a name="use-parameterized-sql-queries-for-cosmos-db"></a><a id="sql-docdb"></a>Použití parametrizovaných dotazů SQL pro Cosmos DB

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze dokumentů Azure | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Oznámení parametrizace SQL v Db Azure Cosmos](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Kroky** | Přestože Azure Cosmos DB podporuje pouze dotazy jen pro čtení, vkládání SQL je stále možné, pokud dotazy jsou vytvořeny zřetězení s vstupem uživatele. Může být možné pro uživatele získat přístup k datům, které by neměly být přístup v rámci stejné kolekce crafting škodlivé dotazy SQL. Parametrizované dotazy SQL použijte, pokud jsou dotazy vytvořeny na základě vstupu uživatele. |

## <a name="wcf-input-validation-through-schema-binding"></a><a id="schema-binding"></a>Ověřování vstupu WCF prostřednictvím vazby schématu

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecný, NET Framework 3 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Kroky** | <p>Nedostatečná validace vede k útokům injekce jiného typu.</p><p>Ověření zprávy představuje jednu linii obrany v ochraně aplikace WCF. Pomocí tohoto přístupu ověřte zprávy pomocí schémat k ochraně operací služby WCF před útokem klienta se zlými úmysly. Ověřte všechny zprávy přijaté klientem, abyste klienta ochránili před útokem škodlivé služby. Ověření zprávy umožňuje ověřit zprávy, když operace spotřebovávají smlouvy zpráv nebo kontrakty dat, což nelze provést pomocí ověření parametru. Ověření zprávy umožňuje vytvořit logiku ověření uvnitř schémat, a tím poskytuje větší flexibilitu a zkrátí dobu vývoje. Schémata lze znovu použít v různých aplikacích v rámci organizace a vytvářet standardy pro reprezentaci dat. Ověření zpráv navíc umožňuje chránit operace, když spotřebovávají složitější datové typy zahrnující smlouvy představující obchodní logiku.</p><p>Chcete-li provést ověření zprávy, nejprve vytvořte schéma, které představuje operace vaší služby a datové typy spotřebované těmito operacemi. Potom vytvoříte třídu .NET, která implementuje vlastní inspektor zpráv klienta a vlastního inspektora zpráv dispečera k ověření zpráv odeslaných/přijatých do/ze služby. Dále implementovat vlastní chování koncového bodu povolit ověření zprávy na straně klienta i služby. Nakonec implementovat vlastní konfigurační prvek na třídu, která umožňuje vystavit rozšířené chování vlastního koncového bodu v konfiguračním souboru služby nebo klienta"</p>|

## <a name="wcf--input-validation-through-parameter-inspectors"></a><a id="parameters"></a>WCF - Ověření vstupu prostřednictvím inspektorů parametrů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecný, NET Framework 3 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Kroky** | <p>Ověření vstupu a dat představuje jednu důležitou obrannou linii v ochraně aplikace WCF. Měli byste ověřit všechny parametry vystavené v operacích služby WCF k ochraně služby před útokem klienta se zlými úmysly. Naopak byste měli také ověřit všechny vrácené hodnoty přijaté klientem k ochraně klienta před útokem škodlivé služby</p><p>WCF poskytuje různé body rozšiřitelnosti, které umožňují přizpůsobit chování wcf runtime vytvořením vlastní rozšíření. Inspektoři zpráv a inspektoři parametrů jsou dva mechanismy rozšiřitelnosti, které se používají k získání větší kontroly nad daty předávaných mezi klientem a službou. Inspektory parametrů byste měli použít pro ověření vstupu a inspektoři zpráv pouze v případě, že potřebujete zkontrolovat celou zprávu, která proudí do a ze služby.</p><p>Chcete-li provést ověření vstupu, vytvoříte třídu .NET a implementujete vlastní inspektor parametrů za účelem ověření parametrů operací ve vaší službě. Potom implementujete vlastní chování koncového bodu, abyste povolili ověření na straně klienta i služby. Nakonec implementujete vlastní konfigurační prvek ve třídě, který umožňuje vystavit rozšířené chování vlastního koncového bodu v konfiguračním souboru služby nebo klienta</p>|
