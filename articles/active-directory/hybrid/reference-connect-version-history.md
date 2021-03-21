---
title: 'Azure AD Connect: Historie vydání verze | Microsoft Docs'
description: V tomto článku jsou uvedené všechny verze Azure AD Connect a Azure AD Sync.
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a19babffa63667b0d2deb954d432421a2b7868b8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722136"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Historie vydaných verzí
Tým Azure Active Directory (Azure AD) pravidelně aktualizuje Azure AD Connect s novými funkcemi a funkcemi. Ne všechny dodatky platí pro všechny cílové skupiny.

Tento článek je navržený tak, aby vám pomohly sledovat verze, které byly vydané, a zjistit, jaké jsou změny v nejnovější verzi.



V této tabulce najdete seznam souvisejících témat:

Téma |  Podrobnosti
--------- | --------- |
Postup upgradu z Azure AD Connect | Různé metody [upgradu z předchozí verze na nejnovější](how-to-upgrade-previous-version.md) verzi Azure AD Connect.
Požadovaná oprávnění | Oprávnění potřebná k použití aktualizace najdete v tématu [účty a oprávnění](reference-connect-accounts-permissions.md#upgrade).
Stáhnout| [Stáhněte si Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>Vydání nové verze Azure AD Connect je proces, který vyžaduje několik kroků řízení kvality, aby se zajistila funkčnost této služby. při tomto procesu se ale aktualizuje číslo verze nové verze a stav verze se aktualizuje tak, aby odrážela nejnovější stav.
I když procházíme tímto procesem, číslo verze vydaných verzí se zobrazí s číslem "X" v umístění vedlejší verze, jako v "1.3. X. 0" – to znamená, že poznámky k verzi v tomto dokumentu jsou platné pro všechny verze začínající znakem "1,3". Po dokončení procesu vydávání verzí bude číslo vydané verze aktualizováno na nejnovější vydanou verzi a stav vydání bude aktualizován na hodnotu Vydáno ke stažení a automatický upgrade.
Pro automatický upgrade nebudou zpřístupněny všechny verze Azure AD Connect. Stav vydání označuje, zda je vydaná verze dostupná pro automatický upgrade nebo pouze pro stažení. Pokud byl na Azure AD Connect serveru povolen automatický upgrade, server se automaticky upgraduje na nejnovější verzi Azure AD Connect vydanou pro automatický upgrade. Všimněte si, že ne všechny konfigurace Azure AD Connect mají nárok na automatický upgrade. 

Chcete-li objasnit použití automatického upgradu, je třeba, abyste na vás zaznamenali všechny důležité aktualizace a důležité opravy. To nemusí nutně být nejnovější verze, protože ne všechny verze budou vyžadovat nebo zahrnují opravu kritického bezpečnostního problému (jenom jeden příklad mnoha). Takový problém by byl vyřešen novou verzí poskytnutou prostřednictvím automatického upgradu. Pokud neexistují žádné takové problémy, aktualizace se neodesílají pomocí automatického upgradu a obecně platí, že pokud používáte nejnovější verzi automatického upgradu, měli byste být dobrá.
Pokud byste ale chtěli mít všechny nejnovější funkce a aktualizace, nejlepším způsobem, jak zjistit, jestli je tato stránka k dispozici, je to, abyste si tuto stránku zkontrolovali a nainstalovali podle potřeby. 

Další informace o [automatickém upgradu](how-to-connect-install-automatic-upgrade.md) získáte pomocí tohoto odkazu.

>[!IMPORTANT]
> Od 1. dubna 2024 vyřadíme verze Azure AD Connect vydané před 1. května 2018 verze 1.1.751.0 a starší. 
>
> Abyste získali optimální prostředí podpory, musíte mít jistotu, že používáte nejnovější verzi Azure AD Connect. 
>
>Pokud spustíte vystaralou verzi Azure AD Connect nebudete mít k dispozici nejnovější opravy zabezpečení, vylepšení výkonu, řešení potíží a diagnostické nástroje a vylepšení služeb. Pokud požadujete podporu, můžeme vám poskytnout úroveň služby, kterou vaše organizace potřebuje.
>

>
>Další informace o tom, jak upgradovat Azure AD Connect na nejnovější verzi najdete v [tomto článku](./how-to-upgrade-previous-version.md) .
>
>Informace o historii verzí v vyřazených verzích najdete v článku [archiv Historie vydání verze Azure AD Connect](reference-connect-version-history-archive.md) .


## <a name="1623"></a>1.6.2.3

>[!NOTE]
> - Tato verze bude dostupná jenom pro stažení.
> - Upgrade na tuto verzi bude vyžadovat úplnou synchronizaci z důvodu změn pravidel synchronizace.
> - Tato verze vyhodnotí server AADConnect do nového koncového bodu v2. Všimněte si, že tento koncový bod není podporovaný v německém národním cloudu, v rámci čínského národního cloudu a v cloudu pro státní správu USA, a pokud potřebujete nasadit tuto verzi v těchto cloudech, musíte podle [těchto pokynů](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-endpoint-api-v2#rollback) přejít zpátky na koncový bod v1. V důsledku tohoto selhání dojde k chybám při synchronizaci.

### <a name="release-status"></a>Stav verze
3/17/2021: vydáno ke stažení

### <a name="functional-changes"></a>Funkční změny

 - Aktualizovaná výchozí pravidla synchronizace pro omezení členství v zapsaných skupinách zpět na 50 tis členové.
   - Přidala se nová výchozí pravidla synchronizace pro omezení počtu členství ve skupině zpětný zápis skupin (na limit počtu členů se zpětným zápisem skupiny) a synchronizace skupin na Azure Active Directory (z limitu počtu členů služby AAD-skupina writeup).
   - Přidání atributu členu do pravidla "out do AD-Group SOAInAAD-Exchange" pro omezení členů v zapsaných skupinách zpět na 50 tis
 - Aktualizovaná pravidla synchronizace pro podporu zpětného zápisu skupin v2 – Pokud je pravidlo in z AAD-Group SOAInAAD naklonované a upgraduje se AADConnect.
     – Aktualizované pravidlo bude ve výchozím nastavení zakázané a targetWritebackType bude mít hodnotu null.
     - AADConnect zruší všechny cloudové skupiny (včetně skupin zabezpečení Azure Active Directory povolených pro zpětný zápis) jako distribučních skupin.
   – Pokud je pravidlo "out to AD-Group SOAInAAD" naklonované a aktualizuje se AADConnect.
     - Aktualizované pravidlo bude ve výchozím nastavení zakázáno. Bude se ale aktivovat nové pravidlo synchronizace "out do AD-Group SOAInAAD-Exchange", které se přidá.
     - V závislosti na prioritě klonovaného vlastního pravidla synchronizace bude AADConnect přesměrovat atributy pro poštu a Exchange.
     - Pokud naklonované vlastní pravidlo synchronizace neflowe některé atributy pošty a Exchange, nové pravidlo synchronizace Exchange tyto atributy přidá.
 - Přidání podpory pro [synchronizaci hodnot hash selektivního hesla](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-selective-password-hash-synchronization)
 - Přidala se nová [rutina synchronizace s jedním objektem](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-single-object-sync). Pomocí této rutiny můžete řešit potíže s konfigurací Azure AD Connect synchronizace. 
 - Agent AADConnectHealth se aktualizoval na 3.1.83.0.
 - Nová verze [modulu ADSyncTools PowerShellu](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adsynctools), který obsahuje několik nových nebo vylepšených rutin. 
 
   - Clear-ADSyncToolsMsDsConsistencyGuid
   - ConvertFrom-ADSyncToolsAadDistinguishedName
   - ConvertFrom-ADSyncToolsImmutableID
   - ConvertTo-ADSyncToolsAadDistinguishedName
   - ConvertTo-ADSyncToolsCloudAnchor
   - ConvertTo-ADSyncToolsImmutableID
   - Export-ADSyncToolsAadDisconnectors
   - Export-ADSyncToolsObjects
   - Export-ADSyncToolsRunHistory
   - Get-ADSyncToolsAadObject
   - Get-ADSyncToolsMsDsConsistencyGuid
   - Import-ADSyncToolsObjects
   - Import-ADSyncToolsRunHistory
   - Remove-ADSyncToolsAadObject
   - Search-ADSyncToolsADobject
   - Set-ADSyncToolsMsDsConsistencyGuid
   - Trace-ADSyncToolsADImport
   - Trace-ADSyncToolsLdapQuery

 - Bylo aktualizováno protokolování chyb pro chyby získání tokenu.
 - Aktualizované odkazy "Další informace" na stránce konfigurace poskytují více podrobností o propojených informacích.
 - Odebrání explicitního sloupce ze stránky vyhledávání CS ve starém uživatelském rozhraní synchronizace
 - Do toku zpětného zápisu skupiny bylo přidáno další uživatelské rozhraní, které uživateli vyzve k zadání přihlašovacích údajů nebo ke konfiguraci vlastních oprávnění pomocí modulu ADSyncConfig, pokud přihlašovací údaje ještě nejsou v dřívějším kroku k dispozici.
 - Automaticky vytvoří MSA pro účet služby ADSync na řadiči domény. 
 -  Přidala se možnost nastavit a získat Azure Active Directory se zpětným zápisem skupiny funkcí DirSync v2 ve stávajících rutinách:
    - Set-ADSyncAADCompanyFeature
    - Get-ADSyncAADCompanyFeature
 - Přidání dvou rutin ke čtení verze rozhraní AWS API
    - Get-ADSyncAADConnectorImportApiVersion – získání verze rozhraní API pro import AWS
    - Get-ADSyncAADConnectorExportApiVersion – získání verze rozhraní API pro export AWS

 - Změny pravidel synchronizace jsou nyní sledovány, což pomáhá při odstraňování potíží se změnami ve službě. Rutina Get-ADSyncRuleAudit načte sledované změny.
 - Aktualizovali jsme rutinu Add-ADSyncADDSConnectorAccount v [modulu PowerShellu ADSyncConfig](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account#using-the-adsyncconfig-powershell-module) , aby uživatel ve skupině ADSyncAdmin mohl změnit účet konektoru služba AD DS. 

### <a name="bug-fixes"></a>Opravy chyb
 - Aktualizace zakázané barvy popředí pro splnění požadavků na světlost na bílém pozadí. Přidání dalších podmínek pro navigační strom pro nastavení barvy textu v popředí na bílou, je-li vybrána zakázaná stránka pro splnění požadavků na světlost.
 - Zvyšte členitost rutiny Set-ADSyncPasswordHashSyncPermissions – aktualizovaný skript KOSMETICE Permissions (set-ADSyncPasswordHashSyncPermissions), aby zahrnoval volitelný parametr "ADobjectDN". 
 - Oprava chyb usnadnění. Čtečka obrazovky by nyní popsala prvek UX, který obsahuje seznam doménových struktur jako "**seznam doménových** struktur" místo "**seznamu seznamu domén**".
 - Aktualizovaný výstup čtečky obrazovky pro některé položky v průvodci Azure AD Connect. Aktualizace barvy najetí myší na tlačítko pro splnění požadavků na kontrast Aktualizovala se barva názvu Synchronization Service Manager, aby splňovala požadavky na kontrast.
 - Opravili jsme problém s instalací AADConnect z exportované konfigurace s vlastními atributy rozšíření – přidejte podmínku, která při použití pravidla synchronizace přeskočí kontrolu atributů rozšíření v cílovém schématu.
 - Pokud je povolená funkce zpětného zápisu skupiny, přidají se do instalace příslušná oprávnění.
 - Opravit duplicitní výchozí prioritu pravidla synchronizace při importu
 - Opravili jsme problém, který způsobil chybu přípravy během rozdílového importu API v2 pro konfliktní objekt, který byl opraven prostřednictvím portálu Health.
 - Opravili jsme problém v synchronizačním modulu, který způsobil, že objekty CS mají nekonzistentní stav propojení.
 - Do výstupního Get-ADSyncConnectorStatistics se přidaly čítače importu.
 - V některých rohových případech během průvodce pass2 byla opravena nedosažitelná doména a zrušení výběru.
 - Změna importu a exportu zásad do selhání, pokud má vlastní pravidlo duplicitní prioritu 
 - Opravili jsme chybu v logice výběru domény.
 - Řeší problém se sestavou 1.5.18.0, pokud jako zdrojový kotvu použijete mS-DS-ConsistencyGuid a naklonujte ho v části z pravidla spojování AD-Group.
 - Pokud je k dispozici nová instalace AADConnect, použije se prahová hodnota pro odstranění exportu uložená v cloudu, pokud je k dispozici a nedošlo k žádné jiné předané.
 - Opraven problém, kdy AADConnect nepřečte změny ve službě AD DisplayName u zařízení připojených k hybridnímu připojení

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>Stav verze
07/29/2020: vydáno ke stažení

### <a name="functional-changes"></a>Funkční změny
Toto je verze opravy chyb. V této verzi nejsou žádné funkční změny.

### <a name="fixed-issues"></a>Opravené problémy

- Opravili jsme problém, kdy správce nemůže povolit bezproblémové jednotné přihlašování, pokud se účet počítače AZUREADSSOACC už v Active Directory nachází.
- Opravili jsme problém, který způsobil chybu přípravy během rozdílového importu API v2 pro konfliktní objekt, který byl opraven prostřednictvím portálu Health.
- Opravili jsme problém v konfiguraci importu/exportu, kde bylo zakázané vlastní pravidlo, které se naimportovalo jako povolené.

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>Stav verze
07/10/2020: vydáno ke stažení

### <a name="functional-changes"></a>Funkční změny
Tato verze zahrnuje veřejnou verzi Preview funkce pro export konfigurace existujícího serveru Azure AD Connect do. Soubor JSON, který se pak dá použít při instalaci nového serveru Azure AD Connect pro vytvoření kopie původního serveru.

Podrobný popis této nové funkce najdete v [tomto článku](./how-to-connect-import-export-config.md) .

### <a name="fixed-issues"></a>Opravené problémy
- Opravili jsme chybu, kdy by došlo k nepravdivému upozornění na velikost místní databáze v lokalizovaných sestaveních během upgradu.
- Opravili jsme chybu, kdy došlo k nepravdivé chybě v událostech aplikace pro prohození názvu účtu nebo názvu domény.
- Opravili jsme chybu, kdy se Azure AD Connect nepovedlo nainstalovat na řadič domény, což způsobí chybu "člen nebyl nalezen".


## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>Stav verze
05/07/2020: vydáno ke stažení

### <a name="fixed-issues"></a>Opravené problémy
Toto sestavení opravy hotfix řeší problém, ve kterém nevybrané domény byly nesprávně vybrány z uživatelského rozhraní průvodce, pokud byly vybrány pouze podoblasti.


>[!NOTE]
>Tato verze zahrnuje nové rozhraní API koncového bodu Azure AD Connect Sync v2.  Tento nový koncový bod v2 je momentálně ve verzi Public Preview.  Tato verze nebo novější se vyžaduje k použití nového rozhraní API koncového bodu v2.  Pouhá instalace této verze ale nepovolí koncový bod v2. Pokud nepovolíte koncový bod v2, budete moct dál používat koncový bod v1.  Pokud chcete povolit a přihlásit se k veřejné verzi Preview, musíte postupovat podle kroků v části [Azure AD Connect Sync v2 Endpoint API (Public Preview)](how-to-connect-sync-endpoint-api-v2.md) .  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>Stav verze
04/23/2020: vydáno ke stažení

### <a name="fixed-issues"></a>Opravené problémy
Toto sestavení opravy hotfix opravuje problém představený v sestavení 1.5.20.0, kde správce klienta s MFA nemohl povolit DSSO.

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>Stav verze
04/20/2020: vydáno ke stažení

### <a name="fixed-issues"></a>Opravené problémy
Toto sestavení opravy hotfix řeší problém v Build 1.5.20.0, pokud jste naklonoval **v nástroji z pravidla spojení AD-Group** a neklonoval ho **ze společného pravidla AD-Group** .

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>Stav verze
04/09/2020: vydáno ke stažení

### <a name="fixed-issues"></a>Opravené problémy
- Toto sestavení opravy hotfix řeší problém se 1.5.18.0 Build, pokud máte povolenou funkci filtrování skupin a jako zdrojovou kotvu použijete mS-DS-ConsistencyGuid.
- Opravili jsme problém v modulu ADSyncConfig PowerShellu, kde vyvolání příkazu DSACLS, který se používá ve všech rutinách oprávnění set-ADSync *, by způsobilo jednu z následujících chyb:
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> Pokud jste naklonováni v nástroji z pravidla synchronizace **spojení se službou AD-Group** a Neklonujte ho z pravidla synchronizace **AD-Group Common** Sync a plánu, který chcete upgradovat, proveďte v rámci upgradu následující kroky:
> 1. Během upgradu zrušte zaškrtnuté políčko **po dokončení konfigurace spustit proces synchronizace**.
> 2. Upravte pravidlo synchronizace klonovaného připojení a přidejte následující dvě transformace:
>     - Nastavte přímý tok `objectGUID` na `sourceAnchorBinary` .
>     - Nastavte tok výrazů `ConvertToBase64([objectGUID])` na `sourceAnchor` .     
> 3. Povolit Plánovač pomocí `Set-ADSyncScheduler -SyncCycleEnabled $true` .



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>Stav verze
04/02/2020: vydáno ke stažení

### <a name="functional-changes-adsyncautoupgrade"></a>Funkční změny ADSyncAutoUpgrade 

- Byla přidána podpora funkce mS-DS-ConsistencyGuid pro skupinové objekty. To vám umožní přesouvat skupiny mezi doménovými strukturami nebo znovu připojit skupiny ve službě AD do Azure AD, kde se změnil identifikátor objectID skupiny AD, třeba když se server služby AD znovu vytvoří po Calamity. Další informace najdete v tématu [Přesun skupin mezi doménovými strukturami](how-to-connect-migrate-groups.md).
- Atribut mS-DS-ConsistencyGuid se automaticky nastaví u všech synchronizovaných skupin a k povolení této funkce není nutné provádět žádné akce. 
- Get-ADSyncRunProfile odebrat, protože se už nepoužívá. 
- Změnili jsme upozornění, které se zobrazí při pokusu o použití účtu správce podnikové sítě nebo správce domény pro účet služby služba AD DS Connector k poskytnutí dalšího kontextu. 
- Přidala se nová rutina pro odebrání objektů z místa konektoru, který je odebraný ze starého CSDelete.exe nástroje, a nahradí se novou rutinou Remove-ADSyncCSObject. Rutina Remove-ADSyncCSObject jako vstup převezme CsObject. Tento objekt lze načíst pomocí rutiny Get-ADSyncCSObject.

>[!NOTE]
>Starý nástroj CSDelete.exe se odebral a nahradil novou rutinou Remove-ADSyncCSObject 

### <a name="fixed-issues"></a>Opravené problémy

- Opravili jsme chybu v selektoru v doménové struktuře zpětného zápisu skupiny a v selektor organizační jednotky při spuštění Průvodce Azure AD Connect po zakázání funkce. 
- Zavedli jsme novou chybovou stránku, která se zobrazí, pokud chybí požadované hodnoty registru DCOM s novým odkazem na aplikaci help. Do souborů protokolu jsou také zapisovány informace. 
- Opravili jsme problém s vytvořením účtu synchronizace Azure Active Directory, kde povolení rozšíření adresáře nebo KOSMETICE může selhat, protože účet se před pokusem o použití nerozšířil napříč všemi replikami služby. 
- Opravili jsme chybu v nástroji pro kompresi chyb synchronizace, který nezpracovává správně náhradní znaky. 
- Opravili jsme chybu v rámci automatického upgradu, která opustila Server ve službě Scheduler Suspended State. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>Stav verze
12/9/2019: vydaná verze ke stažení Není k dispozici prostřednictvím automatického upgradu.
### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
- Aktualizovali jsme synchronizaci hodnot hash hesel, aby Azure AD Domain Services správně zohlednila odsazení v hodnotách hash protokolu Kerberos.  Tím zajistíte zlepšení výkonu při synchronizaci hesel ze služby Azure AD až po Azure AD Domain Services.
- Přidali jsme podporu spolehlivých relací mezi ověřovacím agentem a Service Bus.
- Tato vydaná verze vynutila TLS 1,2 pro komunikaci mezi ověřovacím agentem a cloudovou službou.
- Přidali jsme mezipaměť DNS pro připojení protokolu WebSocket mezi ověřovacím agentem a cloudovou službou.
- Přidali jsme možnost zaměřit se na konkrétního agenta z cloudu na testování připojení agenta.

### <a name="fixed-issues"></a>Opravené problémy
- Verze 1.4.18.0 využívala chybu, při které rutina PowerShellu pro DSSO používala přihlašovací údaje přihlašovacího uživatele systému Windows místo přihlašovacích údajů správce, které jste zadali při spuštění PS. V důsledku toho není možné povolit DSSO ve více doménových strukturách prostřednictvím uživatelského rozhraní Azure AD Connect. 
- Byla provedena oprava umožňující DSSO souběžně ve všech doménových strukturách prostřednictvím uživatelského rozhraní Azure AD Connect.

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Stav verze
11/08/2019: vydáno ke stažení. Není k dispozici prostřednictvím automatického upgradu.

>[!IMPORTANT]
>Z důvodu interní změny schématu v této verzi Azure AD Connect, pokud spravujete AD FS nastavení konfigurace vztahů důvěryhodnosti pomocí prostředí MSOnline PowerShell, musíte aktualizovat modul MSOnline PowerShellu na verzi 1.1.183.57 nebo vyšší.

### <a name="fixed-issues"></a>Opravené problémy

Tato verze opravuje problém se stávajícími zařízeními připojenými k hybridní službě Azure AD. Tato verze obsahuje nové pravidlo synchronizace zařízení, které tento problém vyřeší.
Všimněte si, že tato změna pravidla může způsobit odstranění zastaralých zařízení z Azure AD. Nejedná se o příčinu obav, protože tyto objekty zařízení služba Azure AD během autorizace podmíněného přístupu nepoužívá. Počet zařízení, která se budou pomocí této změny pravidla odstraňovat u některých zákazníků, může překročit prahovou hodnotu pro odstranění. Pokud se vám v Azure AD zobrazuje odstranění objektů zařízení, než je prahová hodnota pro odstranění exportu, doporučuje se, aby se odstranění procházela. [Postup při překročení prahové hodnoty odstranění do toku při odstraňování](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Stav verze
9/28/2019: vydaná pro automatický upgrade pro výběr klientů. Není k dispozici ke stažení.

Tato verze opravuje chybu, při které některé servery, které byly automaticky upgradovány z předchozí verze, 1.4.18.0 a byly zjištěny problémy s samoobslužným resetováním hesla (SSPR) a zpětným zápisem hesla.

### <a name="fixed-issues"></a>Opravené problémy

Za určitých okolností servery, které byly automaticky upgradovány na verzi 1.4.18.0, po dokončení upgradu znovu nepovolily Samoobslužné resetování hesla a zpětný zápis hesla. Tato verze automatického upgradu opravuje tento problém a znovu umožňuje Samoobslužné resetování hesla a zpětný zápis hesla.

Opravili jsme chybu v nástroji pro kompresi chyb synchronizace, který nezpracovává správně náhradní znaky.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>Zkoumáme incident, ve kterém někteří zákazníci nastávají problém se stávajícími zařízeními připojenými k hybridní službě Azure AD po upgradu na tuto verzi Azure AD Connect. Zákazníkům, kteří si nasadili hybridní službu Azure AD JOIN, doporučujeme, aby odložili upgrade na tuto verzi, dokud se hlavní příčina těchto problémů plně nerozumí a nesnižuje. Další informace budou k dispozici, jakmile to bude možné.

>[!IMPORTANT]
>V této verzi Azure AD Connect můžou někteří zákazníci vidět, že některá nebo všechna zařízení s Windows zmizí z Azure AD. Nejedná se o příčinu obav, protože tyto identity zařízení služba Azure AD během autorizace podmíněného přístupu nepoužívá. Další informace najdete v tématu [principy Azure AD Connect 1.4. xx. x disappearnce zařízení](reference-connect-device-disappearance.md) .


### <a name="release-status"></a>Stav verze
9/25/2019: vydáno pouze pro automatický upgrade.

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
- Nové nástroje pro řešení potíží pomáhají při řešení potíží s scénáři "uživatel neprobíhá synchronizace", "seskupení není synchronizovaný" nebo "nesynchronizované členství ve skupině".
- Přidání podpory pro národní cloudy do skriptu pro řešení potíží s Azure AD Connect.
- Zákazníci by měli být informováni o tom, že vyřazení koncových bodů služby WMI pro MIIS_Service byla nyní odebrána. Jakékoli operace WMI by se teď měly provádět pomocí rutin PS.
- Vylepšení zabezpečení pomocí resetování omezeného delegování objektu AZUREADSSOACC
- Pokud přidáváte nebo upravujete pravidlo synchronizace, jsou-li v pravidle použity atributy, které jsou ve schématu konektoru, ale nejsou přidány do tohoto konektoru, přidají se do konektoru automaticky atributy. Totéž platí pro typ objektu, který pravidlo ovlivňuje. Pokud se do konektoru přidá cokoli, konektor se označí pro úplný import na další cyklus synchronizace.
- Použití organizace nebo správce domény jako účtu konektoru už se v nových nasazeních Azure AD Connect nepodporuje. Tato verze nebude mít vliv na aktuální Azure AD Connect nasazení pomocí podnikového nebo správce domény jako účet konektoru.
- V nástroji Správce synchronizace se při vytváření/úpravách a odstraňování pravidel spustí Úplná synchronizace. Automaticky otevírané okno se zobrazí u libovolné změny pravidla oznamující uživateli, pokud bude spuštěn úplný import nebo Úplná synchronizace.
- Přidání kroků zmírnění pro chyby hesla do ' konektory > vlastnosti > připojení '.
- Přidání upozornění na zastaralost pro správce synchronizační služby na stránce vlastností konektoru. Toto upozornění upozorňuje uživatele, že by se měly provádět změny prostřednictvím Průvodce Azure AD Connect.
- Přidala se nová chyba pro problémy se zásadami hesel uživatele.
- Zabraňuje nekonfiguraci filtrování skupin podle domén a organizačních jednotek. Filtrování skupin zobrazí chybu, pokud je už doména nebo organizační jednotka zadané skupiny odfiltrovaná, a uživatel zůstane v pohybu dál, dokud se problém nevyřeší.
- Uživatelé už nemůžou vytvářet konektor pro Active Directory Domain Services nebo Azure Active Directory Windows v uživatelském rozhraní Synchronization Service Manager.
- Pevné usnadnění vlastních ovládacích prvků uživatelského rozhraní v Synchronization Service Manager.
- Povoluje se šest úloh správy federace pro všechny metody přihlašování v Azure AD Connect.  (Dříve byl pro všechna přihlášení k dispozici pouze úkol "aktualizace AD FS TLS/SSL".)
- Přidání upozornění při změně metody přihlašování z federace na KOSMETICE nebo PTA, že všechny domény a uživatelé služby Azure AD budou převedeny na spravované ověřování.
- Odstranili jste podpisové certifikáty tokenu z úlohy resetovat vztah Azure AD a AD FS trustu a přidali samostatnou dílčí úlohu k aktualizaci těchto certifikátů.
- Přidali jsme novou úlohu správy federace nazvanou "spravovat certifikáty", která má dílčí úkoly pro aktualizaci TLS nebo podpisových certifikátů tokenů pro AD FS farmu.
- Byl přidán nový dílčí úkol správy federace s názvem "zadejte primární server", který umožňuje správcům zadat pro AD FS farmu nový primární server.
- Přidala se nová úloha správy federace s názvem "spravovat servery", která obsahuje podúlohy pro nasazení AD FS serveru, nasazení serveru proxy webových aplikací a určení primárního serveru.
- Přidala se nová úloha správy federace s názvem "zobrazení konfigurace federace", která zobrazuje aktuální nastavení AD FS.  (Kvůli tomuto přidání se AD FS nastavení odebrala ze stránky zkontrolovat vaše řešení.)

### <a name="fixed-issues"></a>Opravené problémy
- Vyřešen problém s chybou synchronizace pro situaci, kdy objekt uživatele přebírající odpovídající objekt kontaktu má odkaz na sebe sama (například uživatel je vlastní správce).
- Automaticky otevíraná okna s nápovědou se teď zobrazují na fokusu klávesnice.
- Pokud se při automatickém upgradu spustí nějaká konfliktní aplikace z 6 hodin, ukončí se a pokračuje v upgradu.
- Omezte počet atributů, které může zákazník při výběru rozšíření adresáře vybrat na jeden objekt 100. Tím zabráníte výskytu chyby během exportu, protože Azure má maximálně 100 atributů rozšíření na jeden objekt.
- Opravili jsme chybu, aby byl skript připojení služby AD robustnější.
- Opravili jsme chybu, aby se Azure AD Connect nainstalovala na počítač s použitím existující pojmenovaného kanálu služby WCF robustnější.
- Vylepšená Diagnostika a odstraňování potíží se zásadami skupiny, které neumožňují spuštění služby ADSync při počáteční instalaci.
- Opravili jsme chybu, kdy se zobrazovaný název počítače se systémem Windows nesprávně napsal.
- Oprava chyby, kdy byl typ operačního systému pro počítač se systémem Windows nesprávně napsán.
- Opravili jsme chybu, kdy počítače s Windows 10 nebyly neočekávaně synchronizovány. Všimněte si, že vlivem této změny je, že se teď odstraní počítače, které nejsou dřív synchronizované s Windows 10. To nemá vliv na žádné funkce, protože synchronizace počítačů s Windows se používá jenom pro připojení k doméně Azure AD, které funguje jenom pro zařízení s Windows 10.
- Do modulu PowerShellu ADSync bylo přidáno několik nových (interních) rutin.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Došlo k známému problému s upgradem Azure AD Connect ze starší verze na 1.3.21.0, kde Microsoft 365 portál neodráží aktualizovanou verzi, i když Azure AD Connect upgradovat úspěšně.
>
> Chcete-li tento problém vyřešit, je třeba importovat modul **AdSync** a pak spustit `Set-ADSyncDirSyncConfiguration` rutinu prostředí PowerShell na serveru Azure AD Connect.  Můžete použít následující postup:
>
>1. Otevřete PowerShell v režimu správce.
>2. Spusťte `Import-Module "ADSync"`.
>3. Spusťte `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`.
 
### <a name="release-status"></a>Stav verze 

05/14/2019: vydáno ke stažení

### <a name="fixed-issues"></a>Opravené problémy 

- Opravili jsme zvýšení oprávnění k ohrožení zabezpečení, které existuje v Microsoft Azure Active Directory Connect Build 1.3.20.0.  Tato zranitelnost za určitých podmínek může útočníkovi umožnit spustit dvě rutiny prostředí PowerShell v kontextu privilegovaného účtu a provádět privilegované akce.  Tato aktualizace zabezpečení řeší problém tím, že tyto rutiny zakáže. Další informace najdete v tématu [aktualizace zabezpečení](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000).


## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
