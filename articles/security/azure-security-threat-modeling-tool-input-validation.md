---
title: Vstupní ověření – nástroj Microsoft Threat modelování – Azure | Microsoft Docs
description: způsoby zmírnění hrozeb, které jsou zveřejněné v nástroji pro modelování hrozeb
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: d26d869748283718375e35ae4183eef0e51a96ed
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778575"
---
# <a name="security-frame-input-validation--mitigations"></a>Rámce zabezpečení: Ověřování vstupu | Způsoby zmírnění rizik 
| Produktům a službám | Článek |
| --------------- | ------- |
| **Webové aplikace** | <ul><li>[Zakázat XSLT skriptování všechny transformací pomocí nedůvěryhodné šablony stylů](#disable-xslt)</li><li>[Ujistěte se, že každé stránce, která může obsahovat uživatele ovladatelné obsah výslovný nesouhlas automatické sledování toku dat MIME](#out-sniffing)</li><li>[Posílení zabezpečení nebo zakázat řešení Entity XML](#xml-resolution)</li><li>[Aplikace využívá ovladač http.sys provést ověření kanonizace adresy URL](#app-verification)</li><li>[Zajistěte, aby byl příslušný ovládací prvky jsou zavedené při přijetí soubory od uživatelů](#controls-users)</li><li>[Ujistěte se, jestli je pro přístup k datům ve webové aplikaci používají bezpečnost typů parametrů](#typesafe)</li><li>[Používat samostatný model vazby třídy nebo seznamy vazby filtru, aby se zabránilo ohrožení zabezpečení velkokapacitního přiřazení MVC](#binding-mvc)</li><li>[Kódování nedůvěryhodné webové výstup před vykreslování](#rendering)</li><li>[Provedení ověření vstupu a filtrování u všech řetězec typu vlastnosti modelu](#typemodel)</li><li>[Čištění bude použito na pole formuláře, které přijímají všechny znaky, např, bohaté textového editoru](#richtext)</li><li>[Jímky, které nemají integrované kódování nepřiřazujte elementů modelu DOM](#inbuilt-encode)</li><li>[Ověřit, zda všechny jsou uzavřeny nebo bezpečně provést přesměrování v rámci aplikace](#redirect-safe)</li><li>[Implementace ověření vstupu na všechny parametry typu řetězec akceptovat metody Kontroleru](#string-method)</li><li>[Nastavit časový limit horní limit pro regulární výraz zpracování, aby se zabránilo DoS z důvodu chybné regulární výrazy](#dos-expression)</li><li>[Nepoužívejte Html.Raw v zobrazení syntaxe Razor](#html-razor)</li></ul> | 
| **Database** | <ul><li>[Nepoužívejte dynamické dotazy v uložené procedury](#stored-proc)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Zajistit, aby ověření modelu pro metody webového rozhraní API](#validation-api)</li><li>[Implementace ověření vstupu na všechny parametry typu řetězec přijata metodami webového rozhraní API](#string-api)</li><li>[Ujistěte se, že bezpečnost typů parametrů se používají v webového rozhraní API pro přístup k datům](#typesafe-api)</li></ul> | 
| **Azure Documentdb** | <ul><li>[Použít umožňující dotazy SQL pro Azure Cosmos DB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[Ověření vstupu WCF prostřednictvím vazbou schématu](#schema-binding)</li><li>[Ověření vstupu WCF prostřednictvím parametru kontroly](#parameters)</li></ul> |

## <a id="disable-xslt"></a>Zakázat XSLT skriptování všechny transformací pomocí nedůvěryhodné šablony stylů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Zabezpečení XSLT](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [XsltSettings.EnableScript vlastnost](http://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Kroky** | XSLT podporuje skriptování uvnitř pomocí šablony stylů `<msxml:script>` elementu. To umožňuje vlastní funkce pro použití v transformaci XSLT. Skript se spustí v kontextu procesu provádění transformace. Když v nedůvěryhodné prostředí, aby se zabránilo vykonání nedůvěryhodnými musí být zakázáno XSLT skriptu. *Pokud pomocí rozhraní .NET:* skriptování XSLT je ve výchozím nastavení zakázané; ale ujistěte se, že ho nebylo povoleno explicitně prostřednictvím `XsltSettings.EnableScript` vlastnost.|

### <a name="example"></a>Příklad: 

```csharp
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Příklad:
Pokud používáte pomocí MSXML 6.0, skriptování XSLT je zakázané ve výchozím nastavení; Nicméně je nutné zajistit, aby ho nebylo povoleno explicitně prostřednictvím vlastnosti objektu XML DOM AllowXsltScript. 

```csharp
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Příklad:
Pokud používáte MSXML 5 nebo níže, XSLT je povoleno skriptování ve výchozím nastavení je potřeba explicitně zakážete. Nastavte vlastnost objektu XML DOM AllowXsltScript na hodnotu false. 

```csharp
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a id="out-sniffing"></a>Ujistěte se, že každé stránce, která může obsahovat uživatele ovladatelné obsah výslovný nesouhlas automatické sledování toku dat MIME

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [IE8 V části zabezpečení - komplexní ochranu](http://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)  |
| **Kroky** | <p>Pro jednotlivé stránky, která může obsahovat uživatele ovladatelné obsahu, musíte použít záhlaví HTTP `X-Content-Type-Options:nosniff`. Abyste dosáhli souladu s tímto požadavkem, můžete buď nastavit požadovaná hlavička pro pouze stránky, které může obsahovat uživatele ovladatelné obsahu stránce stránky nebo můžete ho nastavit globálně pro všechny stránky v aplikaci.</p><p>Každý typ souboru doručit z webového serveru má přidruženou [typ MIME](http://en.wikipedia.org/wiki/Mime_type) (označované taky jako *typu obsahu*), který popisuje povaha obsah (to znamená, obrázek, text, aplikace, atd.)</p><p>Hlavička X-obsah-typ-Options je záhlaví HTTP, která umožňuje vývojářům určit, že obsah by neměl být MIME zachycení. Tuto hlavičku slouží ke zmírnění sledování toku dat MIME útoky. Přidala se podpora pro tuto hlavičku v aplikaci Internet Explorer 8 (IE8)</p><p>Jenom uživatelé aplikace Internet Explorer 8 (IE8) budou využívat X obsah typu možnosti. Předchozí verze aplikace Internet Explorer nerespektují aktuálně Hlavička X-obsah-typ-Options</p><p>Internet Explorer 8 (nebo novější) jsou pouze hlavní prohlížeče implementovat výslovný nesouhlas s funkci sledování toku dat MIME. Pokud další hlavní prohlížeče (Firefox, Safari, Chrome) implementovat podobné funkce, toto doporučení se aktualizují na zahrnují syntaxe také tyto prohlížeče</p>|

### <a name="example"></a>Příklad:
Pokud chcete povolit požadovaná hlavička globálně pro všechny stránky v aplikaci, můžete provést jednu z těchto možností: 

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

* Přidat hlavičku prostřednictvím aplikace globální\_BeginRequest 

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

* Požadovaná hlavička pouze pro konkrétní stránky můžete povolit jeho přidáním do jednotlivých odpovědí: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a id="xml-resolution"></a>Posílení zabezpečení nebo zakázat řešení Entity XML

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Rozšíření Entity XML](http://capec.mitre.org/data/definitions/197.html), [útoků a obrany XML útok DoS](http://msdn.microsoft.com/magazine/ee335713.aspx), [Přehled zabezpečení MSXML](http://msdn.microsoft.com/library/ms754611(v=VS.85).aspx), [osvědčené postupy pro zabezpečení MSXML kódu](http://msdn.microsoft.com/library/ms759188(VS.85).aspx), [ Referenční informace o protokolu NSXMLParserDelegate](http://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [řešení externí odkazy](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Kroky**| <p>I když není využívány často, je funkce XML, který umožňuje analyzátor XML rozbalte makro entity s hodnotami, které jsou definované v rámci samotného dokumentu nebo z externích zdrojů. Například dokument může definovat entity "NázevSpolečnosti" s hodnotou "Microsoft", který pokaždé, když text "&companyname;" se zobrazí v dokumentu, se automaticky nahradí textem Microsoft. Nebo, dokument může definovat entity "MSFTStock", který odkazuje na externí webovou službu načíst aktuální hodnota stock společnosti Microsoft.</p><p>Pak kdykoli "&MSFTStock;" se zobrazí v dokumentu, se automaticky nahradí aktuální uložených cena. Tuto funkci však lze zneužít k vytvoření útok na dostupnost služby (DoS) podmínek. Útočník může vnořit více entity k vytvoření bomb XML exponenciální rozšíření, které zabírá všechny dostupné paměti v systému. </p><p>Alternativně může udělat externího odkazu, který datové proudy zpět nekonečné množství dat nebo která jednoduše přestane reagovat vlákno. Všechny týmy v důsledku toho musíte zakázat interní nebo externí řešení entity XML zcela, pokud jejich aplikace nemá ho použít, nebo ručně omezit množství paměti a čas, který aplikace můžou využívat pro překlad entity, pokud je tato funkce je nezbytně nutné. Pokud řešení entity není vyžadován na základě vaší aplikace, potom jej vypněte. </p>|

### <a name="example"></a>Příklad:
Pro kód .NET Framework můžete postupovat takto:

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
Všimněte si, že na výchozí hodnotu `ProhibitDtd` v `XmlReaderSettings` má hodnotu true, ale v `XmlTextReader` je false. Pokud používáte XmlReaderSettings, není nutné nastavovat ProhibitDtd na hodnotu true, explicitně, ale se doporučuje pro zabezpečení saké, abyste provedli. Všimněte si také, že třída třídou XMLDocument nastavenou na umožňuje řešení entity ve výchozím nastavení. 

### <a name="example"></a>Příklad:
Zakázat entity řešení pro XmlDocuments, použijte `XmlDocument.Load(XmlReader)` přetížení metody zatížení a nastavte příslušné vlastnosti v argumentu XmlReader zakázat řešení, jak je znázorněno v následujícím kódu: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Příklad:
Pokud zakázání řešení entity není možné pro vaši aplikaci, nastavte vlastnost XmlReaderSettings.MaxCharactersFromEntities na odpovídající hodnotu podle potřeb vaší aplikace. To omezí dopad útoků DoS potenciální exponenciální rozšíření. Následující kód představuje příklad tohoto přístupu: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Příklad:
Pokud potřebujete vyřešit vložené entity, ale provést nebude potřebovat přeložit externí entity, nastavte vlastnost XmlReaderSettings.XmlResolver na hodnotu null. Příklad: 

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Všimněte si, že v MSXML6, ProhibitDTD nastavena na hodnotu true (zakázání DTD zpracování) ve výchozím nastavení. Pro kód Apple OSX/iOS jsou dvě analyzátory jazyka XML, můžete použít: NSXMLParser a libXML2. 

## <a id="app-verification"></a>Aplikace využívá ovladač http.sys provést ověření kanonizace adresy URL

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Všechny aplikace, která používá soubor http.sys postupujte podle těchto pokynů:</p><ul><li>Omezení délky adres URL na víc než 16 384 znaků (ASCII nebo Unicode). Toto je absolutní maximální délka adresy URL na základě výchozí nastavení Internetové informační služby (IIS) 6. Weby musí zajistit dobu kratší než to, pokud je to možné</li><li>Použití standardních vstupně-výstupní tříd rozhraní .NET Framework (například FileStream), jak to bude využívat výhod kanonizace pravidla v rozhraní .NET FX</li><li>Explicitně sestavit seznam povolených známé názvů souborů</li><li>Explicitně odmítnout známý typ souborů, neprovede UrlScan odmítne: exe bat, cmd, com, htw, ida, idq, htr, idc, shtm [l], stm, tiskárny, ini, pol, soubory dat</li><li>Catch následující výjimky:<ul><li>System.ArgumentException (pro názvy zařízení)</li><li>System.NotSupportedException (pro datové proudy)</li><li>System.IO.FileNotFoundException (pro neplatný uvozený názvy souborů)</li><li>System.IO.DirectoryNotFoundException (pro neplatný uvozený adresáře)</li></ul></li><li>*Nechcete* vyvolávající do souboru Win32 API vstupně-výstupní operace. Při neplatná adresa URL elegantně vrátí Chyba 400 na uživatele a protokolu skutečné chyby.</li></ul>|

## <a id="controls-users"></a>Zajistěte, aby byl příslušný ovládací prvky jsou zavedené při přijetí soubory od uživatelů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Neomezený nahrávání souborů](https://www.owasp.org/index.php/Unrestricted_File_Upload), [soubor podpisu tabulky](http://www.garykessler.net/library/file_sigs.html) |
| **Kroky** | <p>Odeslané soubory představuje významné riziko aplikace.</p><p>Prvním krokem při řada útoků se má získat kód, který se napadení systému. Útoku pak musí najít způsob, jak získat kód spustit. Použití nahrávání souborů pomáhá útočník provést v prvním kroku. Důsledky nahrávání neomezený souborů se může lišit, včetně převzetí celý systém, systém souborů přetížené nebo databáze, předávání útoků back-end systémy a jednoduchý poškození vzhledu.</p><p>To závisí na aplikace nemá s nahrávaný soubor a hlavně kde je uložen. Ověřování na straně serveru z nahrávání souborů nebyla nalezena. Následující kontrolní mechanismy zabezpečení, by měla být implementována pro nahrání souboru funkce:</p><ul><li>Soubor rozšíření kontrolu (jenom platnou sadu povolený typ měli accepted)</li><li>Maximální limit velikosti souboru</li><li>Soubor by neměl být odeslán do webroot; Toto umístění by měl být adresáře na nesystémové jednotky</li><li>Zásady vytváření názvů musí být sledována, tak, aby název nahrávaný soubor měly některé náhodnosti, aby se zabránilo souboru přepíše</li><li>Soubory by měl být kontrolována antivirový před zápisem na disk</li><li>Aby se zajistilo název souboru a všechny další metadata (například cesta k souboru) ověření škodlivý znaků</li><li>Podpis formátu souboru by měly být zkontrolovány, zabránit uživateli v nahrávání masqueraded souboru (například odeslat soubor exe změnou rozšíření na txt)</li></ul>| 

### <a name="example"></a>Příklad:
Pro vytvoření posledního bodu týkající se ověřování podpisu souboru formátu odkazovat na třídu následující podrobnosti: 

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

## <a id="typesafe"></a>Ujistěte se, jestli je pro přístup k datům ve webové aplikaci používají bezpečnost typů parametrů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Pokud používáte kolekci Parameters, vyhodnotí SQL je vstup jako hodnotu literálu místo pak jako spustitelný kód. Kolekce parametrů slouží k vynucení omezení typ a délku vstupní data. Hodnoty mimo rozsah aktivovat výjimku. Pokud se nepoužívají bezpečnost typů parametrů SQL, může být útočníci provést vkládání útoků, které jsou součástí nefiltrované vstup.</p><p>Používejte bezpečné parametry typu předejdete možných útoků vkládání SQL, které mohou nastat u nefiltrované vstup při vytváření dotazů SQL. Parametry typu bezpečné můžete použít s uložené procedury a dynamických příkazů SQL. Parametry jsou zpracovány jako literálových hodnot v databázi a ne jako spustitelný kód. Parametry jsou zaškrtnutá políčka, typ a délku.</p>|

### <a name="example"></a>Příklad: 
Následující kód ukazuje, jak pomocí bezpečné parametry typu SqlParameterCollection při volání uložené procedury. 

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
V předchozím příkladu kódu vstupní hodnota nemůže být delší než 11 znaků. Pokud data neodpovídá typu nebo délka definované parametrem, třída SqlParameter vyvolá výjimku. 

## <a id="binding-mvc"></a>Používat samostatný model vazby třídy nebo seznamy vazby filtru, aby se zabránilo ohrožení zabezpečení velkokapacitního přiřazení MVC

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | MVC5, MVC6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Atributy metadat](http://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute), [veřejný klíč ohrožení zabezpečení a snížení rizika zabezpečení](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation), [Kompletní návod k přiřazení velkokapacitních v architektuře ASP.NET MVC](http://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [Začínáme s EF pomocí MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Kroky** | <ul><li>**Pokud by měl vypadat pro typu overpost ohrožení zabezpečení? -** Typu overpost ohrožení zabezpečení může dojít, všechny místní vázat třídy modelu ze vstupu uživatele. Architektury, jako je MVC může představovat uživatelská data ve vlastní třídy rozhraní .NET, včetně prostý staré objekty CLR (POCOs). MVC automaticky naplní tyto třídy modelu s daty z požadavku, poskytuje pohodlné reprezentaci pro práci s vstup uživatele. Pokud tyto třídy obsahovat vlastnosti, které by neměl být nastavený uživatelem, může být aplikace bude zranitelný vůči typu overpost útoků, které umožňují uživatelský ovládací prvek dat, která je určená aplikace nikdy. Jako vazby modelu MVC, databáze přístup technologie jako objekt nebo relační mappers jako Entity Framework často také podpora používání objektů POCO představují data databáze. Tyto třídy modelu dat poskytují stejné pohodlí při plánování práce s data databáze stejně MVC v práci s vstup uživatele. Vzhledem k tomu MVC a databáze podporují podobné modely jako objektů POCO, nejspíš snadno opakovaně používat stejné třídy pro oba účely. Tento postup selže zachovat oddělené oblasti zájmu a je jeden běžné oblasti, kde jsou umístěny nezamýšleným vlastnosti do vazby modelu, povolení přečerpání příspěvků útoky.</li><li>**Proč by neměl pomocí třídy modelu Moje nefiltrované databáze jako parametry Moje akce MVC? -** Vazby modelu MVC protože nic vazby v dané třídě. I v případě, že data se nezobrazí v zobrazení, uživatel se zlými úmysly může odeslat požadavek HTTP s těmito daty zahrnuté a MVC bude Ochotně svázat ho, protože akci říká, že třída databáze je obrazec data, která se má přijmout na vstup uživatele.</li><li>**Proč by měla vědět o tvaru používanou pro vazbu modelu? -** Vazby modelu pomocí ASP.NET MVC s příliš široká modely zpřístupní aplikaci útoky typu overpost. Přečerpání příspěvků by mohl útočník měnit data aplikace nad rámec vývojáře, co určený, jako je například přepsání ceny pro položku nebo oprávnění zabezpečení pro účet. Aplikace by měly používat konkrétní akce vazby modelů (nebo seznamy filtrů určitou vlastnost povolené) k poskytování explicitního kontrakt pro jaké nedůvěryhodné vstup umožňující prostřednictvím vazby modelu.</li><li>**Má samostatnou vazbu modely právě duplikování kód? -** Ne, je otázka jsou oddělené oblasti zájmu. Použijete-li opakovaně modely databáze v metody akce, můžete si všechny vlastnosti (nebo dílčí vlastnosti) třída může být nastavena uživatelem v požadavku HTTP. Pokud se nechcete MVC udělat, musíte seznam filtrů nebo samostatné třídy tvaru zobrazíte MVC, jaká data mohou pocházet z místo vstupu uživatele.</li><li>**Pokud je nutné modely samostatnou vazbu na vstup uživatele, je nutné duplicitní všechny moje atributy poznámky dat? -** Nemusí. MetadataTypeAttribute na třídu modelu databáze slouží k propojení s metadaty na třídu vazby modelu. Jenom nezapomeňte, že typ odkazuje MetadataTypeAttribute musí být podmnožinu odkazující typ (může mít méně vlastností, ale ne více).</li><li>**Přesunutí dat a zpět mezi modely vstupu uživatele a modely databáze je zdlouhavé. Je možné pouze kopírovat přes všechny vlastnosti pomocí reflexe? -** Ano. Pouze vlastnosti, které se zobrazují v modelech vazby jsou ty, které jste určili bezpečné na vstup uživatele. Neexistuje žádný důvod zabezpečení, která zabraňuje pomocí reflexe zkopírovat přes všechny vlastnosti, které existují společné mezi tyto dva modely.</li><li>**Co se chystáte [Bind (vyloučit = "WinMgmt€ ¦")]. Můžete použít, místo nutnosti modely samostatnou vazbu? -** Tento přístup se nedoporučuje. Použití [Bind (vyloučit = "WinMgmt€ ¦")] znamená, že se všechny nové vlastnosti vazbu ve výchozím nastavení. Při přidání nové vlastnosti je krok navíc pamatovat k lepšímu zabezpečení věcí, místo nutnosti návrh bylo zabezpečené, ve výchozím nastavení. V závislosti na vývojáře pokaždé, když je vlastnost přidána kontrola tento seznam je rizikové.</li><li>**Je [Bind (zahrnout = "WinMgmt€ ¦")] užitečné pro operace upravit? -** Ne. [Bind (zahrnout = "WinMgmt€ ¦")] je vhodný pro operace INSERT stylu (přidání nových dat). Operace aktualizace stylu (Úprava existující data) použijte jiný přístup, jako má samostatnou vazbu modely nebo předání explicitní seznam povolených vlastností do UpdateModel nebo TryUpdateModel. Přidání [Bind (zahrnout = "¦ €")] atribut u operace úpravy znamená, že MVC se vytvořit instanci objektu a nastavit pouze uvedené vlastnosti, a všechny ostatní na jejich výchozí hodnoty. Když jsou data uložena, nahradí zcela existující entity, resetování hodnoty pro všechny vlastnosti není uveden na výchozí hodnoty. Například, pokud byl vynechaný IsAdmin [Bind (zahrnout = "¦ €")] atribut u operace úpravy, každý uživatel, jehož název byl upraven přes tato akce by se obnovit do IsAdmin = false (všechny upravená uživatel ztratí přihlášeni jako správce). Pokud chcete zabránit aktualizace do určité vlastnosti, použijte jeden z přístupů výše. Všimněte si, že některé verze nástrojů pro MVC generovat řadiče tříd pomocí [Bind (zahrnout = "¦ €")] upravit akce a neznamená, že odebrání vlastnosti z tohoto seznamu, bude zabránit útokům přečerpání účtování. Ale jak je popsáno výše, nefunguje tak, jak má tento přístup a místo toho se obnoví všechna data ve vlastnostech vynechání na jejich výchozí hodnoty.</li><li>**Pro operace vytvoření, existují jakékoli upozornění pomocí [Bind (zahrnout = "WinMgmt€ ¦")] místo modely samostatnou vazbu? -** Ano. Nejprve tento přístup nefunguje pro scénářům, úpravy, které vyžadují zachování dva samostatné přístupy pro minimalizaci všechna ohrožení zabezpečení přečerpání příspěvků. Druhou, samostatnou vazbu modely vynutit oddělené oblasti zájmu mezi tvaru používanou pro vstup uživatele a tvar používané pro trvalost, něco [Bind (zahrnout = "WinMgmt€ ¦")] neprovádí. Třetí, Všimněte si, že [Bind (zahrnout = "WinMgmt€ ¦")] lze zpracovat pouze nejvyšší úrovně vlastnosti; nelze povolit jenom části dílčí vlastnosti (například "Details.Name") v atributu. Nakonec a případně co je nejdůležitější – pomocí [Bind (zahrnout = "WinMgmt€ ¦")] Přidá další krok, který musí mít na paměti, kdykoli se třída se používá pro vazbu modelu. Pokud nové metody akce váže k třídě data přímo a zapomene zahrnout [Bind (zahrnout = "¦ €")] atributu, může být ohrožena útoky, typu overpost proto [Bind (zahrnout = "¦ €")] přístup je méně zabezpečené ve výchozím nastavení. Pokud používáte [Bind (zahrnout = "WinMgmt€ ¦")], postará se vždy k nezapomeňte zadat pokaždé, když vaše data třídy se zobrazí jako parametry metody akce.</li><li>**Pro operace vytvoření, co o uvedení [Bind (zahrnout = "WinMgmt€ ¦")] atributu na vlastní třídy modelu? Není tento přístup nemuseli pamatovat uvedení atribut na každou metodu akce? -** Tento postup funguje v některých případech. Pomocí [Bind (zahrnout = "WinMgmt€ ¦")] na vlastní typ modelu (ne na parametry akci pomocí této třídy) nemuseli nezapomeňte zahrnout [Bind (zahrnout = "WinMgmt€ ¦")] atribut na každou metodu akce. Pomocí atributu přímo na třídě efektivně vytvoří samostatné oblasti plochy této třídy pro účely vazby modelu. Však tento přístup umožňuje pouze pro jeden tvar vazby modelu za třídu modelu. Pokud jedna metoda akce musí povolit vazby modelu pole (například pouze správce akci, která aktualizuje rolí uživatele) a další akce potřebujete zabránit vazbě modelu tohoto pole, tento postup nebude fungovat. Každá třída může mít pouze jeden tvar vazby modelu; Pokud různé akce potřebovat jiný model vazby tvarů, potřebují k představují tyto samostatné obrazců pomocí třídy vazby buď samostatné modelu nebo oddělení [Bind (zahrnout = "WinMgmt€ ¦")] atributů na metody akce.</li><li>**Co jsou vazby modelů? Budou totéž jako Zobrazit modely? -** Toto jsou dva související koncepty. Vazba modelu termín odkazuje na třídu modelu, který je používán akci seznam parametrů (tvaru z vazby modelu MVC předaný metodě akce). Model zobrazení termín odkazuje na třídu modelu, který je předán zobrazení z metody akce. Použití modelu specifické pro zobrazení je běžný postup pro předávání dat z metody akce k zobrazení. Často tento tvar je také vhodná pro vazbu modelu a modelu zobrazení termín lze odkazovat stejný model použít v obou místech. Přesnosti lze dosáhnout, tento postup bude zmíněn konkrétně vazby modely, které jsou zaměřené na tvar předaný akce, která je to důležité pro účely velkokapacitního přiřazení.</li></ul>| 

## <a id="rendering"></a>Kódování nedůvěryhodné webové výstup před vykreslování

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné, webových formulářů, MVC5, MVC6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Jak zabránit skriptování v technologii ASP.NET](http://msdn.microsoft.com/library/ms998274.aspx), [skriptování mezi](http://cwe.mitre.org/data/definitions/79.html), [list cheaty prevence XSS (skriptování mezi weby)](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) |
| **Kroky** | Skriptování mezi (často se používá zkratka jako XSS) je způsob útoku pro online služby nebo jakékoli aplikace nebo součásti, která využívá vstup z webu. Skriptování XSS může útočníkovi umožnit spuštění skriptu na počítači jiného uživatele prostřednictvím ohrožené webové aplikace. Škodlivé skripty slouží k ukrást soubory cookie a jinak manipulovat s počítači napadeného počítače prostřednictvím jazyka JavaScript. XSS brání ověřování uživatelského vstupu, zajistíte, že je správně vytvořen a kódování před vykreslením na webové stránce. Pomocí webové ochrany knihovny lze provést ověření vstupu a výstupu kódování. Pro spravovaný kód (C\#, VB.net, atd.), použít jeden nebo více vhodné kódování metody z knihovny webové ochrany (Anti-XSS), v závislosti na kontextu, kde získá označované vstupu uživatele:| 

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

## <a id="typemodel"></a>Provedení ověření vstupu a filtrování u všech řetězec typu vlastnosti modelu

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné, MVC5, MVC6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Přidání ověřování](http://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [ověřování modelu dat v aplikaci MVC](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [hlavní zásady pro vaše aplikace ASP.NET MVC](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroky** | <p>Před použitím v aplikaci k zajištění, že aplikace zabezpečení proti uživatel se zlými úmysly vstupy musí být ověřeny všechny vstupní parametry. Ověření vstupní hodnoty pomocí regulárního výrazu ověření na straně serveru s strategie ověření seznamu povolených IP adres. Unsanitized uživatelské vstupy / parametry předané do metod může způsobit kódu chyby vkládání.</p><p>Pro webové aplikace vstupní body zahrnují také polí formuláře, QueryStrings, soubory cookie, hlaviček protokolu HTTP a parametry webové služby.</p><p>Následující kontroly ověření vstupu musí být prováděny po vazby modelu:</p><ul><li>Vlastnosti modelu měli s poznámkou regulární výraz pro přijetí povolený počet znaků a maximální přípustná délka poznámky.</li><li>Metody kontroleru proveďte ModelState platnosti</li></ul>|

## <a id="richtext"></a>Čištění bude použito na pole formuláře, které přijímají všechny znaky, např, bohaté textového editoru

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Kódování Unsafe vstup](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3), [HTML Sanitizér](https://github.com/mganss/HtmlSanitizer) |
| **Kroky** | <p>Identifikujte všechny značky statické značek, které chcete použít. Běžnou praxí je omezit formátování na bezpečné elementy HTML, jako například `<b>` (tučné) a `<i>` (kurzíva).</p><p>Před zápisem dat, kódování HTML ho. Díky tomu všech škodlivý skriptů bezpečné tak, že ho zpracovávat jako text, ne jako spustitelný kód.</p><ol><li>Zakázat ověřování žádostí ASP.NET tak, že přidáte ValidateRequest = "false" atribut @ Page – direktiva</li><li>Kódování vstupní řetězec pomocí metody HtmlEncode</li><li>Použití StringBuilder a volejte příslušnou metodu nahradit selektivně odebrat kódování na elementy HTML, které chcete povolit</li></ol><p>Na stránce in ověření žádosti odkazy zakáže ASP.NET nastavením `ValidateRequest="false"`. Ji umístí kódování HTML vstupu a umožňuje selektivně `<b>` a `<i>` Alternativně knihovny .NET pro čištění HTML mohou být využity také.</p><p>HtmlSanitizer je knihovna pro .NET pro čištění fragmentů kódu HTML a dokumenty z konstrukce, které můžou vést útoky XSS. Používá AngleSharp analyzovat, manipulaci a vykreslení HTML a CSS. HtmlSanitizer je možné nainstalovat jako balíčku NuGet a vstup uživatele může být předána relevantní HTML nebo šablon stylů CSS čištění metody, jako příslušné na straně serveru. Upozorňujeme, který čištění jako ovládací prvek zabezpečení by měl být považován za pouze jako poslední možnost.</p><p>Ověření vstupu a výstupu kódování jsou považovány za lepší kontrolních mechanismů pro zabezpečení.</p> |

## <a id="inbuilt-encode"></a>Jímky, které nemají integrované kódování nepřiřazujte elementů modelu DOM

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Mnoho funkcí javascript nedělají nic kódování ve výchozím nastavení. Při přiřazování nedůvěryhodné vstup na elementy DOM prostřednictvím takové funkce, může vést k křížové spouštění skriptů (XSS) lokality.| 

### <a name="example"></a>Příklad:
Následuje několik příkladů, nezabezpečené: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Nepoužívejte `innerHtml`; místo toho použijte `innerText`. Podobně, místo provedení `$("#elm").html()`, použijte `$("#elm").text()` 

## <a id="redirect-safe"></a>Ověřit, zda všechny jsou uzavřeny nebo bezpečně provést přesměrování v rámci aplikace

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Rozhraní autorizace OAuth 2.0 - otevřete přesměrovačů](http://tools.ietf.org/html/rfc6749#section-10.15) |
| **Kroky** | <p>Návrh aplikace, které vyžadují přesměrování na uživatelem zadané umístění musí omezit možné přesměrování cíle, které předem definovaného seznamu "bezpečnou" sítě nebo domény. Všechny přesměrování v aplikaci, musí být uzavřen nebo bezpečné.</p><p>Použijte následující postup:</p><ul><li>Identifikujte všechny přesměrování</li><li>Implementujte příslušné zmírnění dopadů pro každou funkci přesměrování. Odpovídající jejich zmírnění zahrnují přesměrování potvrzení seznamu povolených IP adres nebo uživatele. Pokud webové stránky nebo služby s chybou otevřete přesměrování umožňující používá zprostředkovatelů identity Facebook nebo OAuth/OpenID, může útočník ukrást token přihlášení uživatele a zosobnit uživatele. To je nese riziko při použití OAuth, které jsou uvedené v RFC 6749 "The OAuth 2.0 autorizace Framework", podobně jako část 10.15 "otevřete přesměruje", přihlašovací údaje uživatelů můžete ohroženy útoky spear phishing pomocí otevřete přesměrování</li></ul>|

## <a id="string-method"></a>Implementace ověření vstupu na všechny parametry typu řetězec akceptovat metody Kontroleru

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné, MVC5, MVC6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Ověřování dat modelu v aplikaci MVC](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [hlavní zásady pro vaše aplikace ASP.NET MVC](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroky** | Pro metody, které právě přijímají primitivní datový typ a ne modely jako argument by mělo být provedeno ověření vstupu pomocí regulárního výrazu. Regex.IsMatch – v tomto poli musí být použit s vzor platný regulární výraz. Pokud vstupní neodpovídá zadanému regulárnímu výrazu, řízení neměli pokračovat a má být zobrazena odpovídajícího upozornění týkající se chyby ověření.| 

## <a id="dos-expression"></a>Nastavit časový limit horní limit pro regulární výraz zpracování, aby se zabránilo DoS z důvodu chybné regulární výrazy

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné, webových formulářů, MVC5, MVC6  |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Vlastnost DefaultRegexMatchTimeout ](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Kroky** | Zajistit útoků DoS proti chybně vytvořený regulární výrazy, které způsobí mnoho zpětné navracení, nastavte globální výchozí časový limit. Pokud bude čas zpracování trvá déle, než definované horní limit, by vyvolat výjimku časového limitu. Pokud je nakonfigurovaná nic, bude časový limit nekonečné.| 

### <a name="example"></a>Příklad:
Například následující konfigurace vyvolá RegexMatchTimeoutException, pokud se zpracování trvá déle než 5 sekund: 

```csharp
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a id="html-razor"></a>Nepoužívejte Html.Raw v zobrazení syntaxe Razor

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | MVC5, MVC6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| Krok | Webových stránek ASP.Net (Razor) provést automatické kódování HTML. Všechny řetězce tištěné útržky vloženého kódu (@ bloky) jsou automaticky kódovaný jazykem HTML. Ale když `HtmlHelper.Raw` metoda je volána, vrátí kód, který není kódován jazykem HTML. Pokud `Html.Raw()` Pomocná metoda se používá, obchází automatickou ochranu kódování, která poskytuje Razor.|

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
Nepoužívejte `Html.Raw()` Pokud je třeba zobrazit značek. Tato metoda neprovádí kódování implicitně výstup. Například použijte další Pomocníci ASP.NET `@Html.DisplayFor()` 

## <a id="stored-proc"></a>Nepoužívejte dynamické dotazy v uložené procedury

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Útok prostřednictvím injektáže SQL zneužije chyby zabezpečení v ověření vstupu ke spuštění libovolného příkazů v databázi. Ho může dojít, když vaše aplikace používá vstup k sestavování dynamických příkazů SQL pro přístup k databázi. Může také dojít, pokud kód používá uložené procedury, které se předávají řetězce, které obsahují nezpracovaná uživatelský vstup. Pomocí útok prostřednictvím injektáže SQL, útočník může spustit libovolný příkazy v databázi. Všechny příkazy SQL (včetně příkazy SQL v uložené procedury) musí být parametry. Parametrizované příkazy SQL, bude přijímat znaků, které mají zvláštní význam jazyka SQL (např. jednoduchých uvozovkách) bez problémů, protože jsou silného typu. |

### <a name="example"></a>Příklad:
Následuje příklad nezabezpečené dynamické uloženou proceduru: 

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
Toto je stejné implementována bezpečně uložené procedury: 
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

## <a id="validation-api"></a>Zajistit, aby ověření modelu pro metody webového rozhraní API

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | MVC5, MVC6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Ověření modelu v rozhraní ASP.NET Web API ](http://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Kroky** | Když klient odešle data do webového rozhraní API, je nutné ověřit data před provedením jakékoli zpracovávání. Pro rozhraní ASP.NET Web API, kterou přijmout modely jako vstup, pomocí datových poznámek na modely nastavování pravidel ověřování pro vlastnosti modelu.|

### <a name="example"></a>Příklad:
Následující kód ukazuje stejné: 

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
V metodě akce řadičů rozhraní API platnosti modelu se musí explicitně zkontrolovat, jak je uvedeno níže: 

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

## <a id="string-api"></a>Implementace ověření vstupu na všechny parametry typu řetězec přijata metodami webového rozhraní API

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné, MVC 5, 6 MVC |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Ověřování dat modelu v aplikaci MVC](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [hlavní zásady pro vaše aplikace ASP.NET MVC](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Kroky** | Pro metody, které právě přijímají primitivní datový typ a ne modely jako argument by mělo být provedeno ověření vstupu pomocí regulárního výrazu. Regex.IsMatch – v tomto poli musí být použit s vzor platný regulární výraz. Pokud vstupní neodpovídá zadanému regulárnímu výrazu, řízení neměli pokračovat a má být zobrazena odpovídajícího upozornění týkající se chyby ověření.|

## <a id="typesafe-api"></a>Ujistěte se, že bezpečnost typů parametrů se používají v webového rozhraní API pro přístup k datům

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Pokud používáte kolekci Parameters, vyhodnotí SQL je vstup jako hodnotu literálu místo pak jako spustitelný kód. Kolekce parametrů slouží k vynucení omezení typ a délku vstupní data. Hodnoty mimo rozsah aktivovat výjimku. Pokud se nepoužívají bezpečnost typů parametrů SQL, může být útočníci provést vkládání útoků, které jsou součástí nefiltrované vstup.</p><p>Používejte bezpečné parametry typu předejdete možných útoků vkládání SQL, které mohou nastat u nefiltrované vstup při vytváření dotazů SQL. Parametry typu bezpečné můžete použít s uložené procedury a dynamických příkazů SQL. Parametry jsou zpracovány jako literálových hodnot v databázi a ne jako spustitelný kód. Parametry jsou zaškrtnutá políčka, typ a délku.</p>|

### <a name="example"></a>Příklad:
Následující kód ukazuje, jak pomocí bezpečné parametry typu SqlParameterCollection při volání uložené procedury. 

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
V předchozím příkladu kódu vstupní hodnota nemůže být delší než 11 znaků. Pokud data neodpovídá typu nebo délka definované parametrem, třída SqlParameter vyvolá výjimku. 

## <a id="sql-docdb"></a>Použít umožňující dotazy SQL pro Cosmos DB

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Documentdb | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Uvedení Parametrizace SQL v Azure Cosmos DB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Kroky** | I když Azure Cosmos DB podporuje pouze dotazy jen pro čtení, je stále možné v případě, že dotazy jsou vytvořený zřetězením s uživatelský vstup Injektáž SQL. Je možné, pro uživatele k získání přístupu k datům, které budou by neměla přístup v rámci stejné kolekce, tím, že vytvoří škodlivý dotazy SQL. Parametrizované dotazy SQL pomocí Pokud dotazy se vytvářejí na základě na vstup uživatele. |

## <a id="schema-binding"></a>Ověření vstupu WCF prostřednictvím vazbou schématu

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné, NET Framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Kroky** | <p>Nedostatečná ověření vede k prostřednictvím injektáže jiného typu.</p><p>Zpráva ověření představuje jeden řádek obrany v oblasti ochrany aplikace WCF. S tímto přístupem ověření zprávy použití schémata k ochraně před útoky ze strany škodlivého klienta operací služby WCF. Ověřte všechny zprávy přijaté službou klienta pro ochranu klienta před útokem škodlivý službou. Zpráva ověření umožňuje ověření zprávy při operations využívat kontrakty zpráv nebo datové kontrakty, které není možné pomocí ověření parametru. Zpráva ověření můžete vytvořit logiku ověření uvnitř schémat, a tím poskytuje větší flexibilitu a zkrácení doby vývoj. Schémata můžete opětovně použít napříč různými aplikacemi v organizaci, vytváření standardy pro znázornění dat. Kromě toho zpráva ověření umožňuje chránit operace, pokud budou využívat kontrakty představující obchodní logiku zahrnující komplexnější datové typy.</p><p>K provedení ověření zprávy, nejprve vytvoříte schéma, které představuje provozu služby a datové typy uplatníte tyto operace. Pak vytvoříte třídu rozhraní .NET, která implementuje inspector zpráva vlastního klienta a nástroj inspector vlastní dispečera zpráv k ověření zprávy odeslané/přijal od služby. V dalším kroku implementujete chování vlastní koncový bod pro povolení ověřování zpráv na klienta a služby. Nakonec implementovat vlastní konfigurace elementu na třídu, která umožňuje vystavit chování Rozšířené vlastní koncového bodu v konfiguračním souboru služby nebo klienta"</p>|

## <a id="parameters"></a>Ověření vstupu WCF prostřednictvím parametru kontroly

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné, NET Framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Kroky** | <p>Vstup a ověření dat představuje jeden důležité linii obrany v oblasti ochrany aplikace WCF. Měli byste ověřit, všechny parametry, které jsou zveřejněné v operací služby WCF k ochraně před útokem službu Klient se zlými úmysly. Naopak by mělo také ověřit všechny návratové hodnoty přijatých klientem pro ochranu klienta před útokem škodlivý službou</p><p>WCF poskytuje body rozšiřitelnosti jiný, které vám umožní přizpůsobit chování runtime WCF tak, že vytvoříte vlastní rozšíření. Inspektoři zpráv a parametr inspektoři dvěma způsoby rozšiřitelnost použít k získání větší kontrolu nad daty předávání mezi klientem a služby. By měl použít parametr inspektoři pro ověření vstupu a používejte inspektoři zpráv jenom v případě, že je potřeba zkontrolovat celý zpráv předávaných do/z služby.</p><p>K provedení ověření vstupu, bude sestavovat třídu rozhraní .NET a implementovat vlastní parametr inspector k ověřování parametry na operace ve službě. Potom budete implementovat vlastní koncový bod chování pro povolení ověřování klienta a služby. Nakonec budete implementovat vlastní konfigurace elementu na třídu, která umožňuje vystavit chování Rozšířené vlastní koncového bodu v konfiguračním souboru služby nebo klienta</p>|
