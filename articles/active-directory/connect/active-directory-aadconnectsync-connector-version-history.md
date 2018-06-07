---
title: Konektor historie verzí | Microsoft Docs
description: Toto téma uvádí všechny verze konektorů pro Forefront Identity Manager (FIM) a Microsoft Identity Manager (MIM)
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
ms.author: davidste
ms.openlocfilehash: 11794739974a0efdd93a9c45cd18c6d959b1f7b2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592669"
---
# <a name="connector-version-release-history"></a>Historie vydaných verzí konektoru
Konektory pro Forefront Identity Manager (FIM) a Microsoft Identity Manager (MIM) jsou často aktualizuje.

> [!NOTE]
> Toto téma je k dispozici pouze v produktu FIM a MIM. Tyto konektory nejsou podporovány pro instalaci na Azure AD Connect. Vydaná konektory jsou předinstalované na službu AADConnect. při upgradu na zadaná sestavení.


Toto téma uvádí všechny verze konektory, které byly vydány.

Související odkazy:

* [Stáhněte si nejnovější konektory](http://go.microsoft.com/fwlink/?LinkId=717495)
* [Obecné konektor LDAP](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) referenční dokumentace
* [Obecné konektor SQL](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql) referenční dokumentace
* [Web Services Connector](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-ma-ws) referenční dokumentace
* [PowerShell Connector](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-powershell) referenční dokumentace
* [Konektoru Lotus Domino](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-domino) referenční dokumentace


## <a name="118300"></a>1.1.830.0

### <a name="fixed-issues"></a>Opravené problémy:
* Vyřešený System.Diagnostics.EventLogInternal.InternalWriteEvent(Message: A device attached to the system is not functioning) ConnectorsLog
* V této verzi konektorů budete muset aktualizovat přesměrování vazeb z 3.3.0.0-4.1.3.0 na 4.1.4.0 v miiserver.exe.config
* Obecné webové služby:
    * Vyřešený platný kód JSON odpovědi nebylo možné uložit v konfiguračním nástroji služby
* Obecné SQL:
    * Export vždy vygeneruje pouze aktualizace dotaz pro operaci odstranění. Přidat ke generování dotazu odstranění
    * Příkaz jazyka SQL, který získá objekty pro operaci rozdílový Import, pokud 'Rozdílů strategie' je sledování změn byla opravena. V této implementaci známá omezení: rozdílový Import s režimem sledování změn není sledovat změny v více hodnot atributů
    * Přidání možnosti ke generování dotazu delete pro případ, když je potřeba odstranit poslední hodnotu z vícehodnotového atributu a tento řádek neobsahuje žádná data kromě hodnotu, která je potřeba odstranit.
    * Při zpracování System.ArgumentException implementována výstupní parametry podle SP 
    * Nesprávný dotazu proveďte operaci exportu do pole, která má typ varbinary(max)
    * Problém s proměnnou parameterList byl inicializován dvakrát (v funkce ExportAttributes a GetQueryForMultiValue)


## <a name="116490-aadconnect-116490"></a>1.1.649.0 (AADConnect 1.1.649.0)

### <a name="fixed-issues"></a>Opravené problémy:

* Lotus Notes:
  * Filtrování vlastní udělení licence certifikátorům možnost
  * Importovat třídy ImportOperations pevné definice jaké operace můžete spouštět v režimu, zobrazení a který v režimu "Search".
* Obecné LDAP:
  * Adresář OpenLDAP používá jako ukotvení spíše než entryUUI rozlišující název. Nová možnost GLDAP konektor, který umožňuje změnit ukotvení
* Obecné SQL:
  * Opravené export do pole, která má typ varbinary(max).
  * Při přidávání binární data ze zdroje dat do objektu CSEntry, DataTypeConversion function se nezdařil na 0 bajtů. Fixed – funkce DataTypeConversion CSEntryOperationBase třídy.




### <a name="enhancements"></a>Vylepšení:

* Obecné SQL:
  * Umožňuje konfigurovat režim pro spuštění uložené procedury s pojmenované parametry, nebo není s názvem se přidá v okně Konfigurace agenta pro správu SQL obecné na stránce 'Globální parametry'. Na stránce 'globální parametry, že je zaškrtávací políčko s popiskem "použití pojmenované parametry ke spuštění uložené procedury, která zodpovídá za režim pro spouštění uložené procedury s pojmenované parametry nebo ne.
    * Možnost spuštění uložené procedury s pojmenované parametry v současné době funguje pouze pro databáze IBM DB2 a MSSQL. Pro databáze Oracle a MySQL tento postup nefunguje: 
      * Syntaxe SQL z databáze MySQL nepodporuje pojmenované parametry v uložené procedury.
      * Ovladač ODBC pro Oracle nepodporuje pojmenované parametry pro pojmenované parametry v uložené procedury)

## <a name="116040-aadconnect-116140"></a>1.1.604.0 (AADConnect 1.1.614.0)


### <a name="fixed-issues"></a>Opravené problémy:

* Obecné webové služby:
  * Byl opraven problém brání vytváří při nebyly k dispozici dva nebo víc koncových bodů protokolu SOAP projektu.
* Obecné SQL:
  * Operace importu GSQL nebyl konvertování času správně, když se uloží do prostoru konektoru. Výchozí formát data a času pro konektor místa GSQL byl změněn z "rrrr MM-dd: ssZ" na "rrrr MM-dd: ssZ.

## <a name="115510-aadconnect-115530"></a>1.1.551.0 (AADConnect 1.1.553.0)

### <a name="fixed-issues"></a>Opravené problémy:

* Obecné webové služby:
  * Nástroj Wsconfig nepřevádět správně pole Json z "Ukázková žádost" pro metodu služby REST. Příčinou problémů s serializace toto pole Json pro požadavek REST.
  * Webové služby konektoru konfigurační nástroj nepodporuje názvy atributů JSON využití místa symbolů 
    * Vzor nahrazení lze přidat ručně soubor WSConfigTool.exe.config, například ```<appSettings> <add key=”JSONSpaceNamePattern” value="__" /> </appSettings>```
> [!NOTE]
> JSONSpaceNamePattern klíč je požadován, jak exportovat zobrazí následující chybová zpráva: zpráva: prázdný název není platný. 

* Lotus Notes:
  * Pokud možnost **povolit vlastní udělení licence certifikátorům pro organizace nebo organizační jednotky** je po exportu toku Domino, ale v době exportu, exportují se všechny atributy zakázán konektoru selže při exportu (Update) KeyNotFoundException dochází k synchronizaci. 
    * K tomu dojde, protože operace přejmenování se nezdařila při pokusu změnit rozlišující název (uživatelské jméno atribut) změnou jeden z atributů níže:  
      - Příjmení
      - FirstName
      - Indexována
      - AltFullName
      - AltFullNameLanguage
      - organizační jednotky
      - altcommonname

  * Když **povolit vlastní udělení licence certifikátorům pro organizace nebo organizační jednotky** možnost povolena, ale vyžaduje udělení licence certifikátorům jsou stále prázdné, dojde k KeyNotFoundException.

### <a name="enhancements"></a>Vylepšení:

* Obecné SQL:
  * **Scénář: přepracovali Neimplementováno:** "*" funkce
  * **Popis řešení:** změnit metodu pro [odkaz na více hodnot atributů zpracování](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql).


### <a name="fixed-issues"></a>Opravené problémy:

* Obecné webové služby:
  * Nelze importovat konfiguraci serveru, pokud je k dispozici webovou službu konektoru
  * Webová služba konektoru nepracuje s více webových služeb

* Obecné SQL:
  * Žádné typy objektů jsou uvedené pro jednu hodnotu Odkazovaný atribut
  * Rozdílový import na sledování změn strategie odstraní objekt při hodnota se odebere z tabulky s více hodnotami
  * OverflowException v konektoru GSQL s DB2 na AS / 400

Lotus:
  * Přidání možnosti enable\disable hledání organizační jednotky před otevřením GlobalParameters stránky

## <a name="114430"></a>1.1.443.0

Vydáno: 2017 března

### <a name="enhancements"></a>Vylepšení

* Obecné SQL:</br>
  **Scénář příznaky:** je dobře známé omezení s konektorem SQL, které jsme pouze povolit odkaz na typ jeden objekt a vyžadují křížových odkazů se členy. </br>
  **Popis řešení:** v kroku zpracování odkazy byly "*" zvolíte možnost, všechny kombinace typů objektu se vrátí zpět synchronizační modul.

>[!Important]
- Tím se vytvoří mnoho zástupné symboly
- Je vyžadována zajistěte, aby byl pojmenování jedinečný pro různé typy objektů.


* Obecné LDAP:</br>
 **Scénář:** na konkrétní oddíl je vybrána pouze několik kontejnerů, pak vyhledávání stále bude provedeno v celý oddíl. Konkrétní bude filtrováno podle synchronizační služba, ale ne podle MA, což by mohlo způsobit snížení výkonu. </br>

 **Popis řešení:** kód změnit GLDAP konektor bylo možné přejít přes všechny kontejnery a hledání objektů v každé z nich, namísto hledání v celé oddílu.


* Lotus Domino:

  **Scénář:** podporu odstranění Domino pošty pro odebrání osoba během exportu. </br>
  **Řešení:** podporu odstranění konfigurovat e-mailu pro odebrání osoba během exportu.

### <a name="fixed-issues"></a>Opravené problémy:
* Obecné webové služby:
 * Při změně ve výchozí adresa URL služby SAP wsconfig projekty prostřednictvím webové služby konfigurační nástroj a dochází k následující chybě: Nelze najít součást cesty

      ``'C:\Users\cstpopovaz\AppData\Local\Temp\2\e2c9d9b0-0d8a-4409-b059-dceeb900a2b3\b9bedcc0-88ac-454c-8c69-7d6ea1c41d17\cfg.config\cloneconfig.xml'. ``

* Obecné LDAP:
 * Konektor GLDAP nejsou vidět všechny atributy ve službě AD LDS
 * Průvodce zalomení při zjištění žádné atributy UPN ze schématu adresáře LDAP
 * Rozdílová importy selhání s chybami zjišťování nejsou k dispozici během úplný import, pokud není vybraná atribut "objectclass"
 * Na stránce konfigurace "Konfigurace oddílů a hierarchií", nezobrazí všechny objekty typů, které se rovná oddílu pro nové servery v obecné  
LDAP MA. Ukázalo se pouze objekty z oddílu RootDSE.


* Obecné SQL:
 * Opravte pro obecné SQL vodoznak chyb vícehodnotový atribut není importován rozdílový Import
 * Při exportu deleted\added hodnoty atributu s více hodnotami, nejsou deleted\added ve zdroji dat.  


* Lotus Notes:
 * Konkrétní pole "Úplný název" se zobrazí správně v úložišti metaverse ale při exportu do poznámky hodnotu pro atribut má hodnotu Null nebo prázdný.
 * Opravit duplicitní Certifier došlo k chybě
 * Když je vybraný objekt bez všechna data na konektoru Lotus Domino s jinými objekty potom jsme zobrazí zjišťování Chyba při provádění úplné importu.
 * Pokud se rozdílový Import se systémem konektoru Lotus Domino na konci tohoto spustit Microsoft.IdentityManagement.MA.LotusDomino.Service.exe služby někdy vrátí chyby aplikace.
 * Skupiny členství celkové funguje bez problémů a zůstane zachované, s výjimkou při spuštění exportu do pokuste se odebrat uživatele z členství se zobrazí jako úspěšně dokončený s aktualizací, ale není ve skutečnosti získat odstranění uživatele z členství v Lotus Notes.
 * Možnost zvolit režim exportu "Připojení položka v dolní části" se přidala v konfiguraci grafického uživatelského rozhraní systému Lotus MA pro připojení nové položky v dolní části během exportu pro více hodnot atributů.
 * Konektor přidá logice potřebné k odstranění tohoto souboru ze složky e-mailu a ID trezoru.
 * Odstraňte členství nefunguje mezi NAB člen.
 * Hodnoty by měla být úspěšně odstraněna z vícehodnotového atributu

## <a name="111170"></a>1.1.117.0
Vydáno: března 2016

**Nový konektor**  
Počáteční verze [obecné konektor SQL](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericsql).

**Nové funkce:**

* Generický konektor LDAP:
  * Přidaná podpora pro Rozdílový import s Isode.
* Konektor webových služeb:
  * Aktualizovat csEntryChangeResult aktivity a aktivity setImportErrorCode umožňující objekt úrovně chyby vrátit zpět do synchronizační modul.
  * Aktualizovat SAP6 a SAP6User šablony využívat nové funkce úrovně Chyba objektu.
* Konektoru Lotus Domino:
  * Pro export budete potřebovat jeden certifier na adresář. Nyní můžete stejné heslo pro všechny udělení licence certifikátorům v zájmu snazší správy.

**Opravené problémy:**

* Generický konektor LDAP:
  * Pro IBM Tivoli DS nebyla zjištěna správně některé atributy typu odkaz.
  * Otevřete protokol během Rozdílový import bylo zkráceno mezery na začátku a konci řetězce.
  * Novell a NetIQ přejmenovat, přesunout objekt mezi organizační jednotky nebo kontejnery a současně export objektu se nezdařilo.
* Konektor webových služeb:
  * Pokud webová služba měli víc koncových bodů pro stejnou vazbu, pak konektor není zjistit správně těchto koncových bodů.
* Konektoru Lotus Domino:
  * Exportu atribut fullName k e-mailu v databázi nebyla úspěšná.
  * Exportu, což jak přidat nebo odebrat člena ze skupiny exportovat pouze přidaných členů.
  * Pokud dokument poznámky je neplatný (isValid atributu nastavena na hodnotu false), pak konektor selže.

## <a name="older-releases"></a>Starší verze
Před. března 2016 byly vydané konektory jako témata týkající se podpory.

**Obecné LDAP**

* [KB3078617](https://support.microsoft.com/kb/3078617) -1.0.0597, září 2015
* [KB3044896](https://support.microsoft.com/kb/3044896) -1.0.0549, března 2015
* [KB3031009](https://support.microsoft.com/kb/3031009) -1.0.0534, leden 2015
* [KB3008177](https://support.microsoft.com/kb/3008177) -1.0.0419, září 2014
* [KB2936070](https://support.microsoft.com/kb/2936070) -4.3.1082, března 2014

**WebServices**

* [KB3008178](https://support.microsoft.com/kb/3008178) -1.0.0419, září 2014

**PowerShell**

* [KB3008179](https://support.microsoft.com/kb/3008179) -1.0.0419, září 2014

**Lotus Domino**

* [KB3096533](https://support.microsoft.com/kb/3096533) -1.0.0597, září 2015
* [KB3044895](https://support.microsoft.com/kb/3044895) -1.0.0549, března 2015
* [KB2977286](https://support.microsoft.com/kb/2977286) -5.3.0712, srpen 2014
* [KB2932635](https://support.microsoft.com/kb/2932635) -5.3.1003 února 2014  
* [KB2899874](https://support.microsoft.com/kb/2899874) -5.3.0721, říjen 2013
* [KB2875551](https://support.microsoft.com/kb/2875551) -5.3.0534, srpen 2013

## <a name="troubleshooting"></a>Řešení potíží 

> [!NOTE]
> Při aktualizaci Microsoft Identity Manager nebo služby AADConnect s použitím každého ECMA2 konektory. 

Je nutné aktualizovat definici konektor při upgradu tak, aby odpovídaly nebo zobrazí následující chybová zpráva v protokolu událostí spouštění aplikací tak, aby odesílaly upozornění ID 6947: "verze sestavení v konfiguraci konektoru AAD ("X.X.XXX. X"), je dřívější než ve skutečnosti verze ("X.X.XXX. X") z"C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll".

K aktualizaci definice:
* Otevřete vlastnosti pro instanci konektoru
* Klikněte na připojení / připojit na kartě
  * Zadejte heslo pro účet konektoru
* Klikněte na každou z karty vlastností naopak
  * Pokud má tento typ konektoru na kartě oddíly tlačítko Aktualizovat, klikněte na tlačítko Aktualizovat na této kartě
* Po přistupujete všechny karty vlastností klikněte na tlačítko OK a uložte změny.


## <a name="next-steps"></a>Další postup
Další informace o [synchronizace Azure AD Connect](active-directory-aadconnectsync-whatis.md) konfigurace.

Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md).
