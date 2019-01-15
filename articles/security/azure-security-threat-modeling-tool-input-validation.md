---
title: Zadejte ověřovací – nástroj pro modelování hrozeb Microsoftu – Azure | Dokumentace Microsoftu
description: zmírnění rizik pro hrozby v nástroj pro modelování hrozeb
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: dd2914c675d3bca32ca8951ffca1b04e23786400
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54266898"
---
# <a name="security-frame-input-validation--mitigations"></a>Zabezpečení rámce: Ověřování vstupu | Zmírnění rizik 
| Produkt nebo službu | Článek |
| --------------- | ------- |
| **Webové aplikace** | <ul><li>[Zakázat skriptování všechny transformace pomocí nedůvěryhodného šablony stylů XSLT](#disable-xslt)</li><li>[Ujistěte se, že každé stránce, které by mohly obsahovat obsah může ovládat uživatel požádá o mimo automatické sledování toku dat MIME](#out-sniffing)</li><li>[Posílení nebo zakázat rozlišení Entity XML](#xml-resolution)</li><li>[Aplikace využívající http.sys provedení převodu do kanonického tvaru ověření adresy URL](#app-verification)</li><li>[Ujistěte se, že odpovídající ovládací prvky jsou na místě při přijetí soubory od uživatelů](#controls-users)</li><li>[Ujistěte se, že se používají parametry zajišťující bezpečnost typů ve webové aplikaci pro přístup k datům](#typesafe)</li><li>[Použít samostatný model vazby třídy nebo aby se zabránilo ohrožení zabezpečení hromadné přiřazení MVC seznamy vazby filtru](#binding-mvc)</li><li>[Kódování výstupu nedůvěryhodném webovém před vykreslování](#rendering)</li><li>[Provedení ověření vstupu a filtrování na všechny řetězce typu vlastnosti modelu](#typemodel)</li><li>[Sanitizace bude použito na pole formuláře, které přijímají všechny znaky, např., editor formátovaného textu](#richtext)</li><li>[Nepřiřazujte elementů modelu DOM do jímky, které nemají integrované kódování](#inbuilt-encode)</li><li>[Ověřit, zda všechny jsou uzavřeny nebo bezpečně provést přesměrování v rámci aplikace](#redirect-safe)</li><li>[Implementace ověření vstupu na všechny parametry typu řetězec přijal metody Kontroleru](#string-method)</li><li>[Nastavit časový limit horní mez pro zpracování, aby se zabránilo DoS z důvodu chybné regulárních výrazů regulární výraz](#dos-expression)</li><li>[Vyhněte se použití Html.Raw v zobrazení Razor](#html-razor)</li></ul> | 
| **Database** | <ul><li>[Nepoužívejte dynamické dotazy v uložených procedurách](#stored-proc)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Ujistěte se, že ověření modelu se provádí na metody webového rozhraní API](#validation-api)</li><li>[Implementace ověření vstupu na všechny parametry typu řetězec přijal metody webového rozhraní API](#string-api)</li><li>[Ujistěte se, že jsou typově bezpečné parametry použity ve webové rozhraní API pro přístup k datům](#typesafe-api)</li></ul> | 
| **Azure Documentdb** | <ul><li>[Použití parametrizovaných dotazů SQL pro službu Azure Cosmos DB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[Ověření vstupu WCF prostřednictvím vazbu schémat](#schema-binding)</li><li>[Zadání WCF ověřování prostřednictvím parametru kontroly](#parameters)</li></ul> |

## <a id="disable-xslt"></a>Zakázat skriptování všechny transformace pomocí nedůvěryhodného šablony stylů XSLT

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [XSLT Security](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [XsltSettings.EnableScript Property](https://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Kroky** | XSLT podporuje skriptování uvnitř šablony stylů pomocí `<msxml:script>` elementu. To umožňuje vlastní funkce, který se má použít v transformaci XSLT. Skript se spustí v kontextu procesu provádění transformací, která se. Musí se zakázat skriptu XSLT v nedůvěryhodných prostředí tak, aby spuštění nedůvěryhodného kódu. *Pokud používáte .NET:* Ve výchozím nastavení; je zakázáno skriptování XSLT ale musíte zajistit, že jej nepovolil explicitně prostřednictvím `XsltSettings.EnableScript` vlastnost.|

### <a name="example"></a>Příklad: 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Příklad:
Pokud používáte jazyk MSXML 6.0, je ve výchozím nastavení; zakázáno skriptování XSLT ale musíte zajistit, že jej nepovolil explicitně prostřednictvím vlastnosti objektu modelu DOM jazyka XML AllowXsltScript. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Příklad:
Pokud používáte MSXML 5 nebo níže, XSLT je povoleno skriptování ve výchozím nastavení, a vy musíte explicitně ji zakážete. Nastavte vlastnosti objektu modelu DOM jazyka XML AllowXsltScript na hodnotu false. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a id="out-sniffing"></a>Ujistěte se, že každé stránce, které by mohly obsahovat obsah může ovládat uživatel požádá o mimo automatické sledování toku dat MIME

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Aplikaci Internet Explorer 8 V části zabezpečení - komplexní ochranu](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)  |
| **Kroky** | <p>Pro každou stránku, který může obsahovat uživatele řídit obsah, je nutné použít hlavičku protokolu HTTP `X-Content-Type-Options:nosniff`. Pro dosažení souladu s tímto požadavkem, můžete buď nastavit požadované záhlaví stránku po stránce pro pouze stránky, které mohou obsahovat může ovládat uživatel obsahu nebo ho můžete nastavit globálně pro všechny stránky v aplikaci.</p><p>Všechny typy souborů doručovaným z webového serveru má přiřazený [typ MIME](http://en.wikipedia.org/wiki/Mime_type) (také nazývané *content-type*), který popisuje povahu obsah (to znamená, obrázku, textu, aplikací, atd.)</p><p>Hlavička X-Content-typ-Options se hlavičky protokolu HTTP, který umožňuje vývojářům určit, že jejich obsahu by neměly být MIME zachycení. Tato hlavička slouží ke zmírnění útoků MIME pro analýzu sítě. Přidala se podpora pro toto záhlaví v aplikaci Internet Explorer 8 (aplikaci Internet Explorer 8)</p><p>Jenom uživatelé z aplikace Internet Explorer 8 (aplikaci Internet Explorer 8) budou těžit z X obsah typu možnosti. Předchozí verze aplikace Internet Explorer, nerespektují aktuálně Hlavička X-Content-typ-Options</p><p>Aplikace Internet Explorer 8 (nebo novější) jsou pouze hlavní prohlížeče k implementaci funkce pro analýzu sítě MIME odhlásit. Pokud další hlavní prohlížeče (Firefox a Safari, Chrome) implementovat podobné funkce, toto doporučení se aktualizují na syntaxe pro tyto prohlížeče a include</p>|

### <a name="example"></a>Příklad:
Pokud chcete povolit požadované záhlaví globálně pro všechny stránky v aplikaci, proveďte jednu z následujících: 

* Přidat hlavičku v souboru web.config, pokud je aplikace hostovaná pomocí Internetové informační služby (IIS) 7 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* Přidat hlavičku prostřednictvím globální aplikace\_BeginRequest 

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

* Požadované záhlaví jen pro konkrétní stránky můžete povolit přidáním do jednotlivých odpovědi: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a id="xml-resolution"></a>Posílení nebo zakázat rozlišení Entity XML

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Rozšíření Entity XML](http://capec.mitre.org/data/definitions/197.html), [útoků a ochrany XML útok DoS](https://msdn.microsoft.com/magazine/ee335713.aspx), [Přehled zabezpečení služby MSXML](https://msdn.microsoft.com/library/ms754611(v=VS.85).aspx), [osvědčené postupy pro zabezpečení kódu MSXML](https://msdn.microsoft.com/library/ms759188(VS.85).aspx), [ Referenční informace o protokolu NSXMLParserDelegate](http://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [překladu externích odkazů](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Kroky**| <p>I když nejsou využívány často, je funkce XML, který umožňuje analyzátoru XML rozšíření makra entity s hodnotami, které jsou definovány v rámci samotného dokumentu nebo z externích zdrojů. Například dokument může definovat entitu "companyname" s hodnotou "Microsoft", tak pokaždé, když, který text "&companyname;" se zobrazí v dokumentu, je automaticky nahrazena textem Microsoft. Nebo dokumentu může definovat "MSFTStock" odkazující na externí webové služby se načíst aktuální hodnotu populace Microsoft entity.</p><p>Pak kdykoli "&MSFTStock;" se zobrazí v dokumentu, je automaticky nahrazena minimální cenu akcie aktuální. Tuto funkci však lze zneužít k vytvoření podmínky služby (DoS) s cílem odepření. Útočník můžete vnořit více entit, chcete-li vytvořit bomb XML exponenciální rozšíření, která spotřebovává všechnu dostupnou paměť v systému. </p><p>Alternativně si můžete vytvořit externí odkaz, která jsou streamována back nekonečné množství dat nebo, který jednoduše zablokuje vlákno. Všechny týmy v důsledku toho musíte zakázat interní nebo externí řešení entity XML, úplně, pokud jejich aplikace neobsahuje ho použít, nebo ručně omezit množství paměti a času, který aplikace můžou využívat pro rozlišení entity, pokud je tato funkce nezbytně nutné. Pokud řešení entity není požadovaná vaší aplikací, potom jej vypněte. </p>|

### <a name="example"></a>Příklad:
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
Všimněte si, že výchozí hodnota `ProhibitDtd` v `XmlReaderSettings` má hodnotu true, ale v `XmlTextReader` má hodnotu false. Pokud používáte XmlReaderSettings, není nutné nastavovat ProhibitDtd na hodnotu true, explicitně, ale se doporučuje pro bezpečný přístup z více saké, který používáte. Všimněte si také, že třída XmlDocument umožňuje rozlišení entity ve výchozím nastavení. 

### <a name="example"></a>Příklad:
Chcete-li zakázat rozlišení entity pro XmlDocuments, použijte `XmlDocument.Load(XmlReader)` přetížení metody zatížení a nastavte vlastnosti na odpovídající argument objekt XmlReader zakázat rozlišení, jak je znázorněno v následujícím kódu: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Příklad:
Je-li zakázat rozlišení entity není možné pro vaši aplikaci, nastavte vlastnost XmlReaderSettings.MaxCharactersFromEntities na přiměřenou hodnotu podle potřeb vaší aplikace. To omezí dopadu na potenciální útoky DoS exponenciální rozšíření. Následující kód představuje příklad tohoto přístupu: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Příklad:
Pokud je potřeba vyřešit vložené entity na rozdíl od přeložit externí entity, není nutné nastavit vlastnost XmlReaderSettings.XmlResolver na hodnotu null. Příklad: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Všimněte si, že v MSXML6, ProhibitDTD nastavena na hodnotu true (zakázání zpracování deklarace DTD) ve výchozím nastavení. Pro Apple OSX/iOS kód existují dvě analyzátorů XML, které můžete použít: NSXMLParser a libXML2. 

## <a id="app-verification"></a>Aplikace využívající http.sys provedení převodu do kanonického tvaru ověření adresy URL

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Všechny aplikace používající http.sys by měl postupovat podle následujících pokynů:</p><ul><li>Omezte délka adresy URL na více než 16 384 znaků (ASCII nebo Unicode). Toto je absolutní maximální délka adresy URL na základě výchozího nastavení Internetové informační služby (IIS) 6. Websites přiklonit po dobu kratší než pokud je to možné</li><li>Použít standardní vstupně-výstupní třídy rozhraní .NET Framework (například FileStream), jak tyto výhody pravidla převodu do kanonického tvaru v .NET FX</li><li>Explicitní vytváření seznamu povolených známých názvů souborů</li><li>Explicitně odmítnout známých typů souborů neposkytuje UrlScan odmítne: exe, bat cmd, com, htw, Idea, idq, htr, idc, shtm [l], stm, tiskárny, ini, pol, soubory dat</li><li>Zachycujte následující výjimky:<ul><li>System.ArgumentException (pro názvy zařízení)</li><li>System.NotSupportedException (pro datové proudy)</li><li>System.IO.FileNotFoundException (pro neplatný řídicí názvy souborů)</li><li>System.IO.DirectoryNotFoundException (pro neplatný uvozený uvozovacím znakem adresáře)</li></ul></li><li>*Ne* vyvolávající do souboru Win32 API vstupně-výstupních operací. Neplatná adresa URL elegantně vrátí Chyba 400 uživateli a protokolovat Skutečná chyba.</li></ul>|

## <a id="controls-users"></a>Ujistěte se, že odpovídající ovládací prvky jsou na místě při přijetí soubory od uživatelů

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Neomezený nahrání souboru](https://www.owasp.org/index.php/Unrestricted_File_Upload), [soubor podpisu tabulky](http://www.garykessler.net/library/file_sigs.html) |
| **Kroky** | <p>Nahrané soubory představovat významné riziko pro aplikace.</p><p>Prvním krokem v mnoha útokům je získání kódu pro systém a být napaden. Útok pak musí najít způsob, jak získat prováděný kód. Pomocí možnosti nahrávání souborů pomáhá útočník provádět v prvním kroku. Důsledky odesílání neomezeného souborů se může lišit, včetně převzetí celý systém, systém souborů přetížené nebo databáze, předávání útoky na back endových systémů a jednoduché pouze poškození vzhledu.</p><p>To závisí na co aplikace dělá s nahraný soubor a hlavně kde jsou uložená. Ověřování na straně serveru z nahrávání souborů se nenašel. Následující kontrolní mechanismy zabezpečení by měla být implementována pro nahrávání souborů funkce:</p><ul><li>Kontrola souboru rozšíření (pouze platná sada povolený typ by měl být přijat)</li><li>Maximální limit velikosti souboru</li><li>Soubor by neměl být nahrán do webroot; umístění by měl být adresáře na nesystémové jednotky</li><li>Zásady vytváření názvů byste měli dodržet, tak, aby název nahraného souboru mají některé náhodnost, aby se zabránilo souboru přepíše</li><li>Soubory by se měl zkontrolovat pro antivirový program před zápisem na disk</li><li>Ujistěte se, že škodlivý znaků se ověří název souboru a všechny další metadata (například cesta k souboru)</li><li>Podpis formát souboru by měly být porovnány, chcete-li zabránit uživateli v odesílání masqueraded souboru (například nahrávání souboru exe tak, že změníte příponu txt)</li></ul>| 

### <a name="example"></a>Příklad:
Poslední bod týkající se ověření formátu podpisu souboru najdete v třídě níže podrobnosti: 

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

## <a id="typesafe"></a>Ujistěte se, že se používají parametry zajišťující bezpečnost typů ve webové aplikaci pro přístup k datům

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Pokud používáte kolekci parametrů, zpracuje SQL vstup je jako s hodnotou literálu místo toho, jak spustitelného kódu. Kolekce parametrů je možné vynutit omezení typu a délky na vstupní data. Hodnoty mimo rozsah aktivuje výjimku. Pokud nejsou použity parametry SQL typově bezpečný, může být útočníci moci být prováděny útoky prostřednictvím injektáže, které jsou vložené v nefiltrované vstup.</p><p>Při vytváření dotazů SQL pomocí bezpečné parametry typu aby se zabránilo možné útoky prostřednictvím injektáže SQL, které mohou nastat u nefiltrované vstup. Můžete bezpečně parametry typu s uloženými procedurami a pomocí dynamických příkazů SQL. Parametry jsou zpracovány jako hodnoty literálu v databázi a ne jako spustitelný kód. Parametry jsou zkontrolovány také pro typ a délku.</p>|

### <a name="example"></a>Příklad: 
Následující kód ukazuje, jak používat bezpečné parametry typu s SqlParameterCollection při volání uložené procedury. 

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
V předchozím příkladu kódu vstupní hodnota nemůže být delší než 11 znaků. Pokud data není v souladu s typem nebo délkou určené parametrem, třída SqlParameter vyvolá výjimku. 

## <a id="binding-mvc"></a>Použít samostatný model vazby třídy nebo aby se zabránilo ohrožení zabezpečení hromadné přiřazení MVC seznamy vazby filtru

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | MVC5, MVC6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Atributy metadat na](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute), [veřejný klíč zabezpečení ohrožení zabezpečení a omezení rizik](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation), [kompletní pokyny k přiřazení velkokapacitních v architektuře ASP.NET MVC](http://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [Začínáme s EF pomocí MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Kroky** | <ul><li>**Pokud by měl vypadat pro nadbytečně účtování ohrožení zabezpečení? -** Over-pass-the účtování ohrožení zabezpečení může dojít, kdekoli vázat tříd modelu ze vstupu uživatele. Architektury, jako je MVC může představovat dat uživatele ve vlastní třídy .NET, včetně prostý starší objekty CLR (POCOs). MVC automaticky naplní tyto třídy modelu se data z požadavku, poskytuje pohodlný reprezentaci pro práci s uživatelským vstupem. Při těchto tříd patří vlastnosti, které by se neměla nastavovat tímto uživatelem, může být zranitelný vůči over-pass-the účtování útoků, které umožňují uživatelský ovládací prvek dat, která nikdy aplikace určena aplikace. Jako vazby modelu MVC, databázi přístup technologie, jako je objektově/relační mapovačů jako Entity Framework často také podporu pomocí objektů POCO pro reprezentaci dat z databáze. Tyto třídy modelu dat poskytují stejné pohodlí při práci s databázovými daty MVC stejně jako v práci s uživatelským vstupem. MVC i databáze podporuje podobné modelů, jako jsou objekty POCO, a to vypadá snadno znovu použít stejné třídy pro oba účely. Tento postup selže zachovat oddělené oblasti zájmu a je jedné běžné oblasti, kde neúmyslnému vlastnosti jsou vystaveny vazby modelu, povolení útoky typu over-pass-the účtování.</li><li>**Proč by neměl používám Moje nefiltrované databáze třídy modelu jako parametry pro mé akce MVC? -** Vazby modelu MVC protože nic vytvoří vazbu v dané třídě. I v případě, že data se nezobrazí v zobrazení, uživatel se zlými úmysly může odeslat požadavek HTTP s těmito daty zahrnuté a MVC se Ochotně svázat ho, protože vaše akce říká, že třída databáze je tvar dat, která by měla přijímat na vstup uživatele.</li><li>**Proč je by měl tvaru používanou pro vazbu modelu záleží? -** Vazby modelu ASP.NET MVC pomocí nadměrně širokému modely zpřístupňuje aplikaci over-pass-the útoky typu overpost. Útoky over-pass-the účtování může umožnit útočníkům získat změnit data aplikací rámec toho, co vývojář určený, jako je například přepsání cena za položky nebo oprávnění zabezpečení pro účet. Aplikace by měly používat konkrétní akce vazby modelů (nebo konkrétní vlastnost povolené seznamy filtrů) poskytovat explicitní smlouvy pro jaké nedůvěryhodný vstup povolit prostřednictvím vazby modelu.</li><li>**Dochází k danému modely samostatnou vazbu jenom duplikování kódu? -** Ne, je na vás oddělení oblastí zájmu. Pokud znovu použijete modelů databáze v metody akce, můžete se říká libovolné vlastnosti (nebo dílčí vlastnosti), v tom, že třídy můžete nastavit uživatele v požadavku HTTP. Pokud nechcete MVC udělat, musíte mít seznam filtrů nebo obrazec samostatné třídy k zobrazení MVC, jaká data můžou pocházet z místo toho vstup uživatele.</li><li>**Pokud mám samostatnou vazbu modely pro uživatelský vstup, je nutné duplikovat všechny moje atributy anotace dat? -** Ne nutně. MetadataTypeAttribute databáze třídy modelu můžete vytvořit odkaz metadat na třídu vazby modelu. Právě Všimněte si, že typ odkazuje MetadataTypeAttribute musí být podmnožinou odkazující typ (může mít méně vlastností, ale ne více).</li><li>**Přesun dat vpřed a zpět mezi modely vstupu uživatele a modelů databáze je únavné. Můžete mi právě zkopírovat všechny vlastnosti pomocí reflexe? -** Ano. Pouze vlastnosti, které se zobrazují v modelech vazby jsou ty, které jste určili jako bezpečné pro uživatelský vstup. Neexistuje žádný důvod zabezpečení, která brání použití reflexe pro všechny vlastnosti, které existují v běžných mezi těmito dvěma modely zkopírovat.</li><li>**A co [Bind (vyloučit = "některá€ ¦")]. Můžete použít, namísto toho, aby modely samostatnou vazbu? -** Tento přístup nedoporučuje. Použití [Bind (vyloučit = "některá€ ¦")] znamená, že se všechny nové vlastnosti umožňující vazbu ve výchozím nastavení. Když se přidá nová vlastnost je nadbytečný krok zapamatovat, aby vše zůstalo zabezpečené, spíše než s návrhem se ve výchozím nastavení zabezpečený. V závislosti na vývojáře pokaždé, když vlastnost kontrola tento seznam je nebezpečné.</li><li>**Je [Bind (zahrnout = "některá€ ¦")] užitečné pro operace úpravy? -** Ne. [Vytvořit vazbu (zahrnout = "některá€ ¦")] je vhodný pro operace INSERT-style (přidání nových dat). Pro operace UPDATE-style (revizi stávající data) použít jiný přístup, jako je mít samostatnou vazbu modely nebo předávání explicitní seznam povolených vlastností UpdateModel nebo TryUpdateModel. Přidávání [vytvořit vazbu (zahrnout = "¦ €")] atribut na operaci úprav znamená, že MVC se vytvořit instanci objektu a nastavit pouze uvedené vlastnosti ponechat všechny ostatní výchozí hodnoty. Když jsou data uložena, nahradí zcela existující entity, když hodnoty pro všechny vlastnosti není uveden jejich výchozí nastavení výrobce. Například, pokud chybí IsAdmin [vytvořit vazbu (zahrnout = "¦ €")] atribut na operaci úprav, každý uživatel, jehož název se upraví přes tato akce by obnovíte IsAdmin = false (žádný upravených uživatel ztratí stav správce). Pokud chcete zabránit aktualizace k určité vlastnosti, použijte jeden z přístupů výše. Všimněte si, že některé verze nástrojů MVC vygeneruje třídy kontroleru s [vytvořit vazbu (zahrnout = "¦ €")] na Upravit akce a znamenají, že odebrání vlastnosti z tohoto seznamu se útokům typu over-pass-the účtování. Ale jak bylo popsáno výše, tento přístup nefunguje očekávaným způsobem a místo toho se resetuje všechna data ve vlastnostech vynechaný na výchozí hodnoty.</li><li>**Pro operace vytvoření, existují jakékoli upozornění pomocí [Bind (zahrnout = "některá€ ¦")] místo samostatnou vazbu modely? -** Ano. Nejprve tento přístup nefunguje pro scénáře, úpravy, vyžaduje údržbu dvě samostatné metody pro snížení rizik souvisejících s všechna ohrožení zabezpečení typu over-pass-the účtování. Modely druhou, samostatnou vazbu, vynucení oddělení oblastí zájmu mezi tvaru používanou pro vstup uživatele a tvar používané pro trvalost, něco [Bind (zahrnout = "některá€ ¦")] není nutné. Třetí, Všimněte si, že [Bind (zahrnout = "některá€ ¦")] může zpracovat pouze vlastnosti nejvyšší úrovně; v atributu nelze povolit pouze části dílčí vlastnosti (například "Details.Name"). A konečně a případně je nejdůležitější, pomocí [Bind (zahrnout = "některá€ ¦")] Přidá další krok, který musí uživatel zadat pokaždé, když třída se používá pro vazbu modelu. Pokud nové metody akce váže k datové třídy přímo a zapomene zahrnout [vytvořit vazbu (zahrnout = "¦ €")] atributu, může být ohrožená útoky over-pass-the účtování proto [vytvořit vazbu (zahrnout = "¦ €")] je ve výchozím nastavení se o něco méně zabezpečený přístup. Pokud používáte [Bind (zahrnout = "některá€ ¦")], pečlivě vždy nezapomeňte zadat pokaždé, když se vaše data třídy se zobrazí jako parametry metod akce.</li><li>**Pro operace vytvoření, a co uvedení [Bind (zahrnout = "některá€ ¦")] atributu na vlastní třídy modelu? Není tento přístup nemuseli pamatovat uvedení atribut na každou metodu akce? -** Tento přístup funguje v některých případech. Pomocí [Bind (zahrnout = "některá€ ¦")] na samotného typu modelu (a nikoli na parametry akce pomocí této třídy), vyhněte se nutnosti nezapomeňte [vytvořit vazbu (zahrnout = "některá€ ¦")] atribut na každou metodu akce. Pomocí atributu přímo ve třídě účinně vytvoří samostatné plochy této třídy pro účely vazby modelu. Ale tento přístup umožňuje pouze jeden prvek vazby modelu za třídy modelu. Pokud jedna metoda akce je potřeba povolit vazby modelu pole (například pouze správce akci, která aktualizuje role uživatelů) a další akce potřebujete zabránit vazbě modelu tohoto pole, tento postup nebude fungovat. Každá třída může mít pouze jeden prvek vazby modelu; Pokud různé akce potřebovat jiný model vazba tvary, musí představovat tyto samostatné tvary pomocí obou tříd vazby samostatného modelu nebo oddělení [Bind (zahrnout = "některá€ ¦")] atributy u metody akce.</li><li>**Co jsou vazby modely? Jde o stejnou věc jako Zobrazit modely? -** Toto jsou dvě související koncepty. Vazby modelu výraz odkazuje na třídu modelu, který je použit v seznamu parametrů akce (tvaru předat metodě akce z vazby modelu MVC). Model zobrazení výraz odkazuje na třídu modelu, který je předán zobrazení z metody akce. Pomocí konkrétní zobrazení modelu je běžným přístupem k předávání dat z metody akce k zobrazení. Často je také vhodný pro vazbu modelu tohoto obrazce a model zobrazení termín můžete použít jako reference na obou místech používá stejný model. Abychom byli přesní, tento postup bude zmíněn konkrétně vazby modelů, zaměřuje se na obrazec předán akce, která je to, co je pro účely hromadné přiřazení.</li></ul>| 

## <a id="rendering"></a>Kódování výstupu nedůvěryhodném webovém před vykreslování

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné, webových formulářů, MVC5, MVC6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Jak zabránit skriptování napříč weby v technologii ASP.NET](https://msdn.microsoft.com/library/ms998274.aspx), [skriptování napříč weby](http://cwe.mitre.org/data/definitions/79.html), [XSS (skriptování mezi) ochrany před únikem informací Ošidit list](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) |
| **Kroky** | Skriptování napříč weby (často se zkracuje jako XSS) je vektor útoku jakékoli/součásti aplikace, která využívá vstup z webu nebo služeb online services. XSS ohrožení zabezpečení může útočníkovi umožnit spuštění skriptu do jiného uživatele počítače ohrožené webové aplikace. Škodlivé skripty slouží k krádež souborů cookie a jinak úmyslně poškodit napadený počítač prostřednictvím JavaScriptu. XSS brání ověřování uživatelského vstupu, zajistit, že je správně vytvořený a kódování před vykreslením na webové stránce. Ověření vstupu a výstupu kódování můžete provést pomocí knihovny ochrany Web. Pro spravovaný kód (C\#, VB.net, atd.), použijte jednu nebo více odpovídající metody kódování z knihovny ochrana před webovými (Anti-XSS), v závislosti na kontextu, ve kterém získá označované vstup uživatele:| 

### <a name="example"></a>Příklad:

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

## <a id="typemodel"></a>Provedení ověření vstupu a filtrování na všechny řetězce typu vlastnosti modelu

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Generic, MVC5, MVC6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Přidání ověřování](http://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [ověřování modelu dat v aplikaci MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [zásady pro vaše aplikace ASP.NET MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroky** | <p>Vstupní parametry musí ověřit dříve, než se používají v aplikaci k zajištění, že aplikace je chráněna proti uživatel se zlými úmysly vstupy. Ověřte vstupní hodnoty, které pomocí regulárního výrazu ověření na straně serveru se strategií ověření seznamu povolených IP adres. Unsanitized uživatelské vstupy / parametry předané do metod může způsobit kódu vkládání chyb zabezpečení.</p><p>Pro webové aplikace vstupních bodů použít také pole formuláře, řetězci dotazu, soubory cookie, hlavičky protokolu HTTP a parametry webové služby.</p><p>Po vazbě modelu se musí provádět následující kontroly ověření vstupu:</p><ul><li>Vlastnosti projektu by měl být komentována atributem regulární výraz poznámky pro příjem povolených znaků a maximální povolenou délku</li><li>Metody kontroleru by měl provádět ModelState platnosti</li></ul>|

## <a id="richtext"></a>Sanitizace bude použito na pole formuláře, které přijímají všechny znaky, např., editor formátovaného textu

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Zakódujte nebezpečné vstup](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3), [HTML Sanitizer](https://github.com/mganss/HtmlSanitizer) |
| **Kroky** | <p>Identifikujte všechny statické značky, které chcete použít. Běžnou praxí je omezení formátování pro bezpečné prvky jazyka HTML, jako například `<b>` (tučné) a `<i>` (kurzívu).</p><p>Před zápisem data s kódováním HTML ho. Díky tomu všech škodlivých skriptů bezpečné podle by ji zpracovat jako text, ne jako spustitelný kód.</p><ol><li>Zakázání ověření žádosti ASP.NET tak, že přidáte ValidateRequest = "false" atributu \@ direktivě stránky</li><li>Vstupní řetězec kódování pomocí HtmlEncode – metoda</li><li>Použít StringBuilder a volání metody nahradit chcete provést selektivní vymazání kódování na prvcích HTML, které chcete povolit</li></ol><p>Na stránce se změnami ověření žádosti ASP.NET zakáže odkazy tak, že nastavíte `ValidateRequest="false"`. To umístí kódování HTML vstupu a selektivně umožňuje `<b>` a `<i>` Alternativně knihovna .NET pro sanitizace HTML mohou být využity také.</p><p>HtmlSanitizer je knihovna .NET pro čištění fragmentů kódu HTML a dokumenty z konstrukce, které můžou vést útoky XSS. Používá AngleSharp pro analýzu, zpracování a vykreslení kódu HTML a CSS. HtmlSanitizer je možné nainstalovat jako balíček NuGet a uživatelský vstup, mohou být předány prostřednictvím příslušné HTML a CSS sanitizace metody, případně na straně serveru. Mějte prosím na paměti, která Sanitizace jako ovládací prvek zabezpečení by měly být považovány za pouze jako poslední možnost.</p><p>Ověření vstupu a kódování výstupu se považují za lepší kontrolních mechanismů pro zabezpečení.</p> |

## <a id="inbuilt-encode"></a>Nepřiřazujte elementů modelu DOM do jímky, které nemají integrované kódování

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Mnoho funkcí jazyka javascript. neudělají to kódování ve výchozím nastavení. Při přiřazování nedůvěryhodný vstup k prvkům modelu DOM pomocí takových funkcí, nemusí se pro různé lokality spuštění skriptu (XSS).| 

### <a name="example"></a>Příklad:
Následují příklady nezabezpečeného: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Nepoužívejte `innerHtml`; místo toho použijte `innerText`. Podobně, nikoli z `$("#elm").html()`, použijte `$("#elm").text()` 

## <a id="redirect-safe"></a>Ověřit, zda všechny jsou uzavřeny nebo bezpečně provést přesměrování v rámci aplikace

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Rozhraní autorizace OAuth 2.0 - otevřít přesměrovačů](http://tools.ietf.org/html/rfc6749#section-10.15) |
| **Kroky** | <p>Návrh aplikace, které vyžadují přesměrování do uživatelem zadané umístění musí omezit možné přesměrování cíle do předem definovaného seznamu "bezpečné" ze sítě nebo domény. Všechny přesměrování v aplikaci musí být uzavřen a bezpečné.</p><p>Použijte následující postup:</p><ul><li>Identifikujte všechny přesměrování</li><li>Implementujte odpovídající omezení rizik pro každý přesměrování. Vhodné způsoby zmírnění rizik obsahovat přesměrování potvrzení seznamu povolených IP adres nebo uživatelů. Pokud webové stránky nebo služby s ohrožení zabezpečení otevřeném přesměrování používá zprostředkovatelé identity Facebook/OAuth/OpenID, útočník může ukrást přihlašovací token uživatele a vydávat za tohoto uživatele. To přináší riziko je při použití OAuth, které jsou uvedené v dokumentu RFC 6749 "OAuth 2.0 autorizace rámci", podobně části 10.15 "otevřete přesměruje", může být ohrožena přihlašovacích údajů uživatelů o útoky typu phishing spear pomocí otevřené přesměrování</li></ul>|

## <a id="string-method"></a>Implementace ověření vstupu na všechny parametry typu řetězec přijal metody Kontroleru

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Generic, MVC5, MVC6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Ověřování modelu dat v aplikaci MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [zásady pro vaše aplikace ASP.NET MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroky** | Pro metody, které stačí přijmout primitivní datový typ a ne modely jako argument musí být provedeno ověření vstupu pomocí regulárního výrazu. Regex.IsMatch – je třeba použít tady se vzorem platný regulární výraz. Pokud vstup neodpovídá zadanému regulárnímu výrazu, ovládací prvek nesmí pokračovat a má být zobrazena odpovídající upozornění týkající se selhání ověření.| 

## <a id="dos-expression"></a>Nastavit časový limit horní mez pro zpracování, aby se zabránilo DoS z důvodu chybné regulárních výrazů regulární výraz

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné, webových formulářů, MVC5, MVC6  |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Vlastnost DefaultRegexMatchTimeout ](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Kroky** | Zajistit odmítnutí útoků služby proti chybně vytvořený regulární výrazy, které způsobují velké množství používání mechanismu zpětného navracení, nastavte globální výchozí časový limit. Pokud doba zpracování trvá déle než definované horní mez, vede výjimka časového limitu. Pokud není nic nakonfigurované, by neomezený časový limit.| 

### <a name="example"></a>Příklad:
Například následující konfigurace vyvolá RegexMatchTimeoutException, pokud zpracování trvá déle než 5 sekund: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a id="html-razor"></a>Vyhněte se použití Html.Raw v zobrazení Razor

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | MVC5, MVC6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| Krok | Webových stránek ASP.Net (Razor) provádět automatické kódování HTML. Všechny řetězce zprávy vytištěné funkcí útržky vloženého kódu (bloky) @ jsou automaticky kódovaný jazykem HTML. Ale když `HtmlHelper.Raw` vyvolání metody, vrátí kód, který není kódován jazykem HTML. Pokud `Html.Raw()` se používá pomocnou metodu, obchází automatické ochrany kódování, která poskytuje syntaxi Razor.|

### <a name="example"></a>Příklad:
Tady je příklad nezabezpečené: 

```csharp
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Nepoužívejte `Html.Raw()` Pokud potřebujete zobrazit značky. Tato metoda neprovede výstup kódování implicitně. Například použití jiných pomocných rutin technologie ASP.NET `@Html.DisplayFor()` 

## <a id="stored-proc"></a>Nepoužívejte dynamické dotazy v uložených procedurách

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Útok prostřednictvím injektáže SQL zneužije chyby zabezpečení v ověření vstupu pro spuštění libovolných příkazů v databázi. To může dojít, když vaše aplikace používá vstup k sestavování dynamických příkazů SQL pro přístup k databázi. Je také může dojít, pokud váš kód používá uložené procedury, které jsou předány řetězce, které obsahují nezpracované uživatelský vstup. Pomocí útoku prostřednictvím injektáže SQL, útočník může prováděly libovolné příkazy v databázi. Všechny příkazy SQL (včetně příkazů SQL v uložených procedurách) musí být parametrizovány. Parametrizované příkazy SQL bude přijímat znaků, které mají speciální význam, aby SQL (jako je jednoduchá uvozovka) bez problémů, protože jsou silného typu. |

### <a name="example"></a>Příklad:
Tady je příklad nezabezpečené dynamické uložená procedura: 

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

### <a name="example"></a>Příklad:
Toto je stejný implementovat bezpečně uložené procedury: 
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

## <a id="validation-api"></a>Ujistěte se, že ověření modelu se provádí na metody webového rozhraní API

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | MVC5, MVC6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Ověření modelu v rozhraní ASP.NET Web API ](http://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Kroky** | Když klient odešle data do webového rozhraní API, je nutné ověřit data před provedením jakékoli zpracování. Pro webová rozhraní API technologie ASP.NET, které přijímají modely jako vstup, používají anotacemi dat na modely nastavování pravidel ověřování pro vlastnosti modelu.|

### <a name="example"></a>Příklad:
Následující kód ukazuje stejný: 

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

### <a name="example"></a>Příklad:
V metodě akce kontrolery rozhraní API má platnost modelu chcete explicitně kontrolovat, jak je znázorněno níže: 

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

## <a id="string-api"></a>Implementace ověření vstupu na všechny parametry typu řetězec přijal metody webového rozhraní API

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné, MVC 5, MVC 6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Ověřování modelu dat v aplikaci MVC](https://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [zásady pro vaše aplikace ASP.NET MVC](https://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroky** | Pro metody, které stačí přijmout primitivní datový typ a ne modely jako argument musí být provedeno ověření vstupu pomocí regulárního výrazu. Regex.IsMatch – je třeba použít tady se vzorem platný regulární výraz. Pokud vstup neodpovídá zadanému regulárnímu výrazu, ovládací prvek nesmí pokračovat a má být zobrazena odpovídající upozornění týkající se selhání ověření.|

## <a id="typesafe-api"></a>Ujistěte se, že jsou typově bezpečné parametry použity ve webové rozhraní API pro přístup k datům

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Pokud používáte kolekci parametrů, zpracuje SQL vstup je jako s hodnotou literálu místo toho, jak spustitelného kódu. Kolekce parametrů je možné vynutit omezení typu a délky na vstupní data. Hodnoty mimo rozsah aktivuje výjimku. Pokud nejsou použity parametry SQL typově bezpečný, může být útočníci moci být prováděny útoky prostřednictvím injektáže, které jsou vložené v nefiltrované vstup.</p><p>Při vytváření dotazů SQL pomocí bezpečné parametry typu aby se zabránilo možné útoky prostřednictvím injektáže SQL, které mohou nastat u nefiltrované vstup. Můžete bezpečně parametry typu s uloženými procedurami a pomocí dynamických příkazů SQL. Parametry jsou zpracovány jako hodnoty literálu v databázi a ne jako spustitelný kód. Parametry jsou zkontrolovány také pro typ a délku.</p>|

### <a name="example"></a>Příklad:
Následující kód ukazuje, jak používat bezpečné parametry typu s SqlParameterCollection při volání uložené procedury. 

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
V předchozím příkladu kódu vstupní hodnota nemůže být delší než 11 znaků. Pokud data není v souladu s typem nebo délkou určené parametrem, třída SqlParameter vyvolá výjimku. 

## <a id="sql-docdb"></a>Použití parametrizovaných dotazů SQL pro službu Cosmos DB

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Documentdb | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Oznamujeme vydání Parametrizace SQL ve službě Azure Cosmos DB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Kroky** | I když službu Azure Cosmos DB podporuje pouze dotazy jen pro čtení, je stále možné, pokud jsou dotazy konstruovány zřetězením s uživatelským vstupem útok prostřednictvím injektáže SQL. Může být uživatel k získání přístupu k datům, které se nesmí získávat přístup k v rámci stejné kolekce tím, že vytvoří škodlivých příkazů jazyka SQL. Použití parametrizovaných dotazů SQL Pokud jsou dotazy konstruovány na základě uživatelského zadání. |

## <a id="schema-binding"></a>Ověření vstupu WCF prostřednictvím vazbu schémat

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecná rozhraní NET Framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Kroky** | <p>Nedostatek ověření, který vede k útokům prostřednictvím injektáže jiného typu.</p><p>Ověřovací zpráva představuje jeden řádek z ochrany v oblasti ochrany aplikace WCF. S tímto přístupem ověření zprávy použití schémata k ochraně před útoky ze strany škodlivého klienta operací služby WCF. Ověřte všechny zprávy přijaté službou klienta pro ochranu klienta před útoky škodlivý službou. Ověření zprávy umožňuje ověření zprávy, když operace využívají kontraktů zpráv nebo kontraktů dat, které nelze provést pomocí ověření parametru. Ověření zprávy umožňuje vytvořit logiku ověřování uvnitř schémata, a poskytuje tak vyšší flexibilitu a snižuje dobu vývoje. Schémata můžete použít opakovaně napříč různými aplikacemi v organizaci, vytváření standardy pro reprezentaci dat. Kromě toho zpráva ověření umožňuje ochranu operace při spotřebují komplexnější datové typy kontraktů představující obchodní logiku zahrnující.</p><p>K provedení ověření zprávy, nejprve vytvořit schéma, které představuje provoz služby a datové typy používané tyto operace. Pak vytvoříte třída rozhraní .NET, která implementuje inspektoru zpráva vlastního klienta a vlastní dispečera zpráv inspektoru k ověření zpráv odeslaných/přijatých ze služby. V dalším kroku implementujete chování vlastní koncový bod má povolit ověřování zpráv na klienta a služby. A konečně implementujete vlastní element konfigurace na třídu, která umožňuje vystavit Rozšířené vlastní koncový bod chování v konfiguračním souboru služby nebo klienta"</p>|

## <a id="parameters"></a>Zadání WCF ověřování prostřednictvím parametru kontroly

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecná rozhraní NET Framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Kroky** | <p>Ověření vstupu dat představuje jeden důležitý linie obrany v oblasti ochrany aplikace WCF. Měli byste ověřit všechny parametry, které jsou vystavené škodlivého klienta v operací služby WCF služby ochrany před útoky. Naopak by mělo také ověřit všechny návratové hodnoty přijetí klientem pro ochranu klienta před útoky škodlivý službou</p><p>WCF poskytuje body jiné rozšíření, které umožňují přizpůsobit chování modulu runtime WCF vytvořením vlastních rozšíření. Inspektoři zpráv a parametr kontroly jsou dva mechanismy rozšíření umožňují získat větší kontrolu nad daty předávání mezi klientem a službou. By měl použít parametr kontroly pro ověření vstupu a použijte messageinspectors pouze v případě, že je potřeba zkontrolovat celá zpráva odesílaných do služby.</p><p>K provedení ověření vstupu, bude sestavení třída rozhraní .NET a implementovat vlastní parametr inspektoru Chcete-li ověřit parametry pro operace ve službě. Potom budete implementovat chování vlastní koncový bod má povolit ověřování na klienta a služby. Nakonec budete implementovat vlastní element konfigurace na třídu, která umožňuje vystavit Rozšířené vlastní koncový bod chování v konfiguračním souboru služby nebo klienta</p>|
