---
title: Historie verzí konektoru | Dokumentace Microsoftu
description: Toto téma obsahuje seznam všech vydaných verzích konektorů pro Forefront Identity Manager (FIM) a Microsoft Identity Manageru (MIM)
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6a0c66ab-55df-4669-a0c7-1fe1a091a7f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/22/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: cbafe7fcdf2ccd515dfec5554c93bec9c7f3537a
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243159"
---
# <a name="connector-version-release-history"></a>Historie vydaných verzí konektoru
Konektory pro Forefront Identity Manager (FIM) a Microsoft Identity Manageru (MIM) se často aktualizují.

> [!NOTE]
> V tomto tématu je k dispozici pouze v produktu FIM a MIM. Tyto konektory nejsou podporovány pro instalaci na Azure AD Connect. Vydáno konektory jsou předinstalované na AADConnect upgrade na zadaná sestavení.


Toto téma uvádí všechny konektory, které byly vydané verze.

Související odkazy:

* [Stáhněte si nejnovější konektory](http://go.microsoft.com/fwlink/?LinkId=717495)
* [Obecný konektor LDAP](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) referenční dokumentaci
* [Obecný konektor SQL](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql) referenční dokumentaci
* [Webový konektor služby](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-ma-ws) referenční dokumentaci
* [PowerShell Connector](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-powershell) referenční dokumentaci
* [Konektor Lotus Domino](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-domino) referenční dokumentaci


## <a name="118300"></a>1.1.830.0

### <a name="fixed-issues"></a>Opravené problémy:
* Vyřešené System.Diagnostics.EventLogInternal.InternalWriteEvent(Message: A device attached to the system is not functioning) ConnectorsLog
* V této verzi konektorů, které budete muset aktualizovat přesměrování vazby z 3.3.0.0-4.1.3.0 na 4.1.4.0 v miiserver.exe.config
* Obecný webových služeb:
    * Nebylo možné uložit přeložit platný kód JSON odpovědi v nástroji pro konfiguraci
* Obecný konektor SQL:
    * Export vždy generovat jenom aktualizaci dotazu pro operaci odstranění. Přidat ke generování dotazu delete
    * Byl opraven dotaz SQL, který získá objekty pro operaci rozdílový Import, pokud je "Rozdílové strategie" Change Tracking. V této implementaci známá omezení: rozdílový Import s režimem sledování změn nesleduje změny ve více jednohodnotových atributů
    * Přidání možnosti Generovat dotaz delete pro případ, když je potřeba odstranit poslední hodnotu z vícehodnotového atributu a tento řádek neobsahuje žádná data s výjimkou hodnotu, která je potřeba odstranit.
    * Při zpracování System.ArgumentException implementované výstupní parametry podle SP 
    * Nesprávný dotazu provést operace exportu do pole, která má typ varbinary(max)
    * Problém s proměnnou Seznam_parametrů byl inicializován dvakrát (ve funkcích ExportAttributes a GetQueryForMultiValue)


## <a name="116490-aadconnect-116490"></a>1.1.649.0 (AADConnect 1.1.649.0)

### <a name="fixed-issues"></a>Opravené problémy:

* Lotus Notes:
  * Vlastní udělení licence certifikátorům možnosti filtrování
  * Importovat třídy ImportOperations oprava definici jaké operace lze spustit v režimu "Zobrazení" a který v režimu "Vyhledávání".
* Obecný konektor LDAP:
  * Adresář OpenLDAP používá jako ukotvení spíše než entryUUI rozlišující název. Nová možnost GLDAP konektor, který umožňuje upravovat anchor
* Obecný konektor SQL:
  * Oprava exportu do pole, která má typ varbinary(max).
  * Při přidávání binárních dat ze zdroje dat do objektu CSEntry, The DataTypeConversion function se nezdařil na nula bajtů. Fixed – funkce DataTypeConversion CSEntryOperationBase třídy.




### <a name="enhancements"></a>Vylepšení:

* Obecný konektor SQL:
  * Umožňuje konfigurovat režim pro spuštění uložené procedury s pojmenovanými parametry nebo není s názvem se přidá v okně Konfigurace agenta pro správu obecný konektor SQL na stránce "Globální parametry". Na stránce "Globální parametry" existuje zaškrtávací políčko s popiskem "Používání pojmenované parametry pro spuštění uložené procedury" který zodpovídá za režim pro spouštění uložené procedury s názvem parametry nebo ne.
    * V současné době umožňuje spustit uloženou proceduru s pojmenovanými parametry funguje jenom pro databáze IBM DB2 a MSSQL. Tento postup nefunguje pro databáze Oracle nebo MySQL: 
      * Syntaxe SQL, MySQL nepodporuje pojmenované parametry v uložených procedurách.
      * Ovladač ODBC pro Oracle nepodporuje pojmenované parametry pro pojmenované parametry v uložených procedurách)

## <a name="116040-aadconnect-116140"></a>1.1.604.0 (AADConnect 1.1.614.0)


### <a name="fixed-issues"></a>Opravené problémy:

* Obecný webových služeb:
  * Opravili jsme problém brání vytváří, když existují dvě nebo víc koncových bodů protokolu SOAP projektu.
* Obecný konektor SQL:
  * Operace importu GSQL nebyl převod času správně, při ukládání do prostoru konektoru. Výchozí formát data a času pro prostoru konektoru GSQL se změnil z 'rrrr MM-dd: ssZ' k "rrrr MM-dd: ssZ".

## <a name="115510-aadconnect-115530"></a>1.1.551.0 (AADConnect 1.1.553.0)

### <a name="fixed-issues"></a>Opravené problémy:

* Obecný webových služeb:
  * Nástroj Wsconfig nepřevádět správně pole Json z "ukázkové žádosti" pro metodu služby REST. Problémy s serializace důvodem toto pole Json pro požadavek REST.
  * Webové služby konektoru konfigurační nástroj nepodporuje využití místa na symboly v názvech atributů JSON 
    * Vzoru pro nahrazení můžete ručně přidat do souboru WSConfigTool.exe.config, například ```<appSettings> <add key="JSONSpaceNamePattern" value="__" /> </appSettings>```
> [!NOTE]
> JSONSpaceNamePattern klíč je vyžadován při exportu se zobrazí následující chyba: zpráva: prázdný název není platný. 

* Lotus Notes:
  * Pokud možnost **povolit vlastní udělení licence certifikátorům pro organizace nebo organizační jednotky** je po exportu tok všechny atributy se vyexportují Domino, ale v době exportu zakázán konektoru selže při exportu (Update) Keynotfoundexception – dochází k synchronizaci. 
    * K tomu dojde, protože operace přejmenovat se nezdaří při pokusu o změnit rozlišující název (atribut uživatelského jména) tak, že změníte jeden z následujících atributů:  
      - LastName
      - FirstName
      - Indexována
      - AltFullName
      - AltFullNameLanguage
      - organizační jednotky
      - altcommonname

  * Když **povolit vlastní udělení licence certifikátorům pro organizace nebo organizační jednotky** možnost povolená, ale vyžaduje udělení licence certifikátorům jsou stále prázdné, dojde k keynotfoundexception –.

### <a name="enhancements"></a>Vylepšení:

* Obecný konektor SQL:
  * **Scénář: přepracovaná implementováno:** "*" funkce
  * **Popis řešení:** změnit pro přístup k [více Vážíme si toho odkaz atributy zpracování](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql).


### <a name="fixed-issues"></a>Opravené problémy:

* Obecný webových služeb:
  * Nelze importovat konfiguraci serveru, pokud je k dispozici webovou službu konektoru
  * Webová služba konektoru nefunguje s několika webových služeb

* Obecný konektor SQL:
  * Žádné typy objektů jsou uvedeny pro jednu hodnotu Odkazovaný atribut
  * Rozdílový import na řešení Change Tracking strategie odstraní objekt při hodnota se odebere z tabulky s více hodnotami
  * OverflowException – v konektoru GSQL s DB2 na AS / 400

Lotus:
  * Přidání možnosti enable\disable před otevřením GlobalParameters stránky vyhledávání organizační jednotky

## <a name="114430"></a>1.1.443.0

Vydáno. března 2017

### <a name="enhancements"></a>Vylepšení

* Obecný konektor SQL:</br>
  **Scénář příznaky:**   je dobře známé omezení týkajícího se konektor SQL, kde jsme pouze povolit odkaz na jeden typ objektu a vyžadují křížový odkaz se členy. </br>
  **Popis řešení:** v kroku zpracování pro odkazy byly "*" je zvolena možnost, všechny kombinace typů objektů se vrátí zpět do modulu synchronizace.

>[!Important]
- Tím se vytvoří počtu zástupných symbolů
- Je vyžadován, abyste měli jistotu, že pojmenování je jedinečný pro různé typy objektů.


* Obecný konektor LDAP:</br>
 **Scénář:** při pouze několik kontejnerů jsou vybrány v konkrétní oddíl, potom hledání stále bude provedeno v celý oddíl. Konkrétní vyfiltrují podle synchronizační služba, ale nikoli MA, což by mohlo způsobit snížení výkonu. </br>

 **Popis řešení:** kód změnit GLDAP konektor bylo možné přejít přes všechny kontejnery a objekty vyhledávání v každém z nich, namísto vyhledávání v celé oddílu.


* Lotus Domino:

  **Scénář:** Domino e-mailu odstranění podpory pro odebrání osoba během export. </br>
  **Řešení:** konfigurovat e-mailu odstranění podpory pro odebrání osoba během export.

### <a name="fixed-issues"></a>Opravené problémy:
* Obecný webových služeb:
 * Při změně ve výchozí adresu URL služby SAP wsconfig projekty prostřednictvím webové služby konfigurační nástroj a dochází k následující chybě: Nelze najít část cesty

      ``'C:\Users\cstpopovaz\AppData\Local\Temp\2\e2c9d9b0-0d8a-4409-b059-dceeb900a2b3\b9bedcc0-88ac-454c-8c69-7d6ea1c41d17\cfg.config\cloneconfig.xml'. ``

* Obecný konektor LDAP:
 * Konektor GLDAP nezobrazují všechny atributy ve službě AD LDS
 * Průvodce konce při zjištění žádné atributy. hlavní název uživatele ze schématu adresáře LDAP
 * Rozdílová importy selháním kvůli zjištění chyb během úplný import, pokud není vybrána atribut "objectclass" není k dispozici
 * Stránka konfigurace "Konfigurace oddílů a hierarchií", nezobrazí všechny objekty typů, které se rovná oddíl pro nové servery v obecné  
LDAP MA. Jejich jsme si ukázali pouze objekty z oddílu RootDSE.


* Obecný konektor SQL:
 * Oprava pro obecný konektor SQL vodoznak chyby vícehodnotového atributu se neimportovaly rozdílový Import
 * Při exportu deleted\added hodnoty s více hodnotami atributu nejsou deleted\added ve zdroji dat.  


* Lotus Notes:
 * Konkrétní pole "Jméno" se zobrazí správně v úložišti metaverse však při exportu do poznámky hodnotu pro atribut má hodnotu Null nebo prázdný.
 * Opravit duplicitní Certifier chyby
 * Když je vybraný objekt bez všechna data na konektor Lotus Domino s jinými objekty pak státem Chyba zjišťování při provádění úplné importu.
 * Pokud se rozdílový Import právě běží na konektor Lotus Domino na konci, ve kterých běží Microsoft.IdentityManagement.MA.LotusDomino.Service.exe služby někdy vrátí chyby aplikace.
 * Skupiny členství celkové funguje správně a je udržován, s výjimkou při exportu pro pokus o odebrání uživatele z členství spuštění zobrazí jako úspěšně dokončený s aktualizací, ale není ve skutečnosti získat odstranění uživatele z členství ve Lotus Notes.
 * Možnost zvolit režim exportu jako "Položka připojit v dolní části" se přidala v konfiguraci grafického uživatelského rozhraní aplikace Lotus MA pro přidání nové položky v dolní části během exportu pro více jednohodnotových atributů.
 * Konektor přidá potřebné logiky odstranit soubor ze složky e-mailu a ID trezoru.
 * Odstraňte členství nepracuje pro různé NAB člena.
 * Hodnoty musí úspěšně odstraněna z vícehodnotového atributu

## <a name="111170"></a>1.1.117.0
Vydáno: březen 2016

**Nový konektor**  
Počáteční verze [pro obecný konektor SQL](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql).

**Nové funkce:**

* Obecný konektor LDAP:
  * Přidání podpory pro Rozdílový import s Isode.
* Konektor webových služeb:
  * Aktualizovat csEntryChangeResult aktivitu a aktivitu setImportErrorCode umožňující chyby na úrovni objektu má být vrácena zpět do modulu synchronizace.
  * Aktualizované šablony SAP6 a SAP6User využívat nové funkce chyba na úrovni objektu.
* Konektor Lotus Domino:
  * Pro export budete potřebovat jeden certifier na adresář. Nyní můžete stejného hesla pro všechny udělení licence certifikátorům usnadnit správu.

**Opravené problémy:**

* Obecný konektor LDAP:
  * Pro IBM Tivoli DS nebyly správně rozpoznány některé atributy typu odkaz.
  * Otevřít protokol během Rozdílový import byly mezery na začátku a konce řetězce zkráceny.
  * Novell a NetIQ přejmenovat, export, přesunout objekt mezi organizačními jednotkami/kontejnery a současně objektu se nezdařilo.
* Konektor webových služeb:
  * Pokud webovou službu více koncových bodů pro stejnou vazbu, pak konektor není zjistit správně tyto koncové body.
* Konektor Lotus Domino:
  * Export úplný název atributu k e-mailu v databázi nebyla úspěšná.
  * Export, které jak přidat nebo odebrat člena ze skupiny exportovat pouze přidaní členové.
  * Pokud dokument poznámky je neplatný (isValid atributu nastavena na hodnotu false), pak konektoru selže.

## <a name="older-releases"></a>Starší verze
Před březnem 2016 konektory vydané jako témata podpory.

**Obecný konektor LDAP**

* [KB3078617](https://support.microsoft.com/kb/3078617) -1.0.0597, září 2015
* [KB3044896](https://support.microsoft.com/kb/3044896) -1.0.0549, březen 2015
* [KB3031009](https://support.microsoft.com/kb/3031009) -1.0.0534, leden 2015
* [KB3008177](https://support.microsoft.com/kb/3008177) -1.0.0419, září 2014
* [KB2936070](https://support.microsoft.com/kb/2936070) -4.3.1082, březen 2014

**WebServices**

* [KB3008178](https://support.microsoft.com/kb/3008178) -1.0.0419, září 2014

**PowerShell**

* [KB3008179](https://support.microsoft.com/kb/3008179) -1.0.0419, září 2014

**Lotus Domino**

* [KB3096533](https://support.microsoft.com/kb/3096533) -1.0.0597, září 2015
* [KB3044895](https://support.microsoft.com/kb/3044895) -1.0.0549, březen 2015
* [KB2977286](https://support.microsoft.com/kb/2977286) -5.3.0712, srpen 2014
* [KB2932635](https://support.microsoft.com/kb/2932635) -5.3.1003, únor 2014  
* [KB2899874](https://support.microsoft.com/kb/2899874) -5.3.0721, říjen 2013
* [KB2875551](https://support.microsoft.com/kb/2875551) -5.3.0534 srpna 2013

## <a name="troubleshooting"></a>Řešení potíží 

> [!NOTE]
> Při aktualizaci Microsoft Identity Manageru nebo AADConnect některý z konektorů ECMA2 užívání. 

Je nutné aktualizovat definice konektoru po upgradu tak, aby odpovídaly nebo v protokolu událostí aplikace po spuštění hlásit upozornění ID 6947 se zobrazí následující chyba: "verze sestavení v konfiguraci konektoru AAD ("X.X.XXX. X") je dřívější než aktuální verze ("X.X.XXX. X") z"C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll".

Chcete-li aktualizovat definici:
* Otevřete vlastnosti pro instanci konektoru
* Klepněte na připojení / připojit ke kartě
  * Zadejte heslo pro účet konektoru
* Klikněte na jednotlivé karty vlastností pak
  * Pokud má tento typ konektoru na kartě oddíly s tlačítko pro aktualizaci, klikněte na tlačítko Aktualizovat na této kartě
* Poté, co byly zobrazeny všechny karty vlastnosti, klikněte na tlačítko OK uložte změny.


## <a name="next-steps"></a>Další postup
Další informace o [synchronizace Azure AD Connect](how-to-connect-sync-whatis.md) konfigurace.

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
