---
title: 'Azure AD Connect: Historie verzí verze | Dokumenty společnosti Microsoft'
description: Tento článek obsahuje seznam všech verzí Azure AD Connect a Azure AD Sync
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 10/7/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: afb295ca561bfa69805362182dc60ce908e1f206
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331141"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: Historie vydaných verzí
Tým Azure Active Directory (Azure AD) pravidelně aktualizuje Azure AD Connect s novými funkcemi a funkcemi. Ne všechny dodatky se vztahují na všechny cílové skupiny.


Tento článek je navržen tak, aby vám pomohl sledovat verze, které byly vydány, a pochopit, jaké změny jsou v nejnovější verzi.

Tato tabulka obsahuje seznam souvisejících témat:

Téma |  Podrobnosti
--------- | --------- |
Postup upgradu z Azure AD Connect | Různé metody [upgradu z předchozí verze na nejnovější](how-to-upgrade-previous-version.md) verzi Azure AD Connect.
Požadovaná oprávnění | Oprávnění potřebná k instalaci aktualizace naleznete v tématu [Účty a oprávnění](reference-connect-accounts-permissions.md#upgrade).
Stáhnout| [Stáhněte si službu Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>Uvolnění nové verze Azure AD Connect je proces, který vyžaduje několik kroků kontroly kvality k zajištění funkčnosti provozu služby a při procházení tohoto procesu číslo verze nové verze, stejně jako stav vydání bude aktualizován tak, aby odrážely nejnovější stav.
Zatímco procházíme tímto procesem, číslo verze vydání se zobrazí s "X" v pozici menšího čísla vydání, jako v "1.3.X.0" - to znamená, že poznámky k verzi v tomto dokumentu jsou platné pro všechny verze začínající na "1.3". Jakmile dokončíme proces vydání, číslo verze verze bude aktualizováno na naposledy vydanou verzi a stav vydání bude aktualizován na "Vydáno ke stažení a automatickému upgradu".
Ne všechny verze Azure AD Connect budou k dispozici pro automatický upgrade. Stav vydání bude uvádět, zda je verze k dispozici pro automatický upgrade nebo pouze ke stažení. Pokud byl na serveru Azure AD Connect povolen automatický upgrade, bude tento server automaticky upgradovat na nejnovější verzi Služby Azure AD Connect, která je vydána pro automatický upgrade. Všimněte si, že ne všechny konfigurace Azure AD Connect jsou způsobilé pro automatický upgrade. Prosím, následujte tento odkaz se přečtěte více o [automatickém upgradu](how-to-connect-install-automatic-upgrade.md)

>[!IMPORTANT]
> listopadu 1st, 2020 začneme implementovat proces vyřazení, kdy verze Azure AD Connect, které byly vydány před více než 18 měsíci, budou zastaralé. V té době začneme tento proces zastaralou všechny verze Azure AD Connect s verzí 1.3.20.0 (který byl vydán na 4/24/2019) a starší a budeme pokračovat vyhodnotit vyřazení staršíverze Azure AD Connect pokaždé, když nové verze vydané.
>
> Musíte se ujistit, že používáte nejnovější verzi Služby Azure AD Connect, abyste získali optimální prostředí podpory. 
>
>Pokud spustíte zastaralou verzi služby Azure AD Connect, nemusíte mít nejnovější opravy zabezpečení, vylepšení výkonu, nástroje pro řešení potíží a diagnostická zařízení a vylepšení služeb a pokud potřebujete podporu, nemusíchom vám být schopni poskytnout úroveň služby, které vaše organizace potřebuje.
>
>Pokud jste povolili Azure AD Connect pro synchronizaci, budete brzy automaticky začne přijímat oznámení o stavu, které vás upozorní na nadcházející vyřazení při spuštění jedné ze starších verzí.
>
>Další informace o upgradu služby Azure AD Connect na nejnovější verzi najdete v [tomto článku.](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>Stav verze
12/9/2019: Vydání ke stažení. Není k dispozici prostřednictvím automatického upgradu.
### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
- Aktualizovali jsme synchronizaci hodnot hash hesla pro službu Azure AD Domain Services, aby správně účet pro odsazení v hodnotě hash protokolu Kerberos.  To bude poskytovat zlepšení výkonu během synchronizace hesel z AAD na Služby domény Azure AD.
- Přidali jsme podporu pro spolehlivé relace mezi agentem ověřování a sběrnicí.
- Tato verze vynucuje TLS 1.2 pro komunikaci mezi agentem ověřování a cloudovými službami.
- Přidali jsme mezipaměť DNS pro připojení websocketmezi agentem ověřování a cloudovými službami.
- Přidali jsme možnost cílit na konkrétního agenta z cloudu na test připojení agenta.

### <a name="fixed-issues"></a>Oprava potíží
- Vydání 1.4.18.0 měl chybu, kde rutina prostředí PowerShell pro DSSO používalpřihlašovací windows pověření namísto pověření správce za předpokladu, že při spuštění ps. V důsledku čehož nebylo možné povolit DSSO ve více doménových struktuře prostřednictvím uživatelského rozhraní AADConnect. 
- Byla provedena oprava umožňující současně službu DSSO ve všech doménových struktuře prostřednictvím uživatelského rozhraní AADConnect

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Stav verze
11/08/2019: Vydáno ke stažení. Není k dispozici prostřednictvím automatického upgradu.

>[!IMPORTANT]
>Vzhledem ke změně interního schématu v této verzi služby Azure AD Connect, pokud spravujete nastavení konfigurace vztahu důvěryhodnosti služby ADFS pomocí prostředí MSOnline PowerShell, je nutné aktualizovat modul MSOnline PowerShell na verzi 1.1.183.57 nebo vyšší
### <a name="fixed-issues"></a>Oprava potíží

Tato verze opravuje problém s existujícími zařízeními spojenými s hybridním Azure AD. Tato verze obsahuje nové pravidlo synchronizace zařízení, které tento problém opravuje.
Všimněte si, že tato změna pravidla může způsobit odstranění zastaralých zařízení z Azure AD. To není důvodkem k obavám, protože tyto objekty zařízení nejsou používány službou Azure AD během autorizace podmíněného přístupu. U některých zákazníků může počet zařízení, která budou odstraněna prostřednictvím této změny pravidla, překročit prahovou hodnotu pro odstranění. Pokud se zobrazí odstranění objektů zařízení ve službě Azure AD přesahující prahovou hodnotu odstranění exportu, doporučuje se povolit odstranění projít. [Jak povolit odstranění toku, když překročí prahovou hodnotu odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Stav verze
28. 9.2019: Vydáno pro automatický upgrade pro vybrané klienty. Není k dispozici ke stažení.

Tato verze opravuje chybu, kdy některé servery, které byly automaticky upgradovány z předchozí verze na 1.4.18.0 a došlo k problémům s samoobslužným resetováním hesla (SSPR) a zpětným zápisem hesla.

### <a name="fixed-issues"></a>Oprava potíží

Za určitých okolností servery, které byly automaticky upgradovány na verzi 1.4.18.0, po dokončení upgradu znovu nepovolily samoobslužné resetování hesla a zpětný zápis hesla. Tato verze automatického upgradu opravuje tento problém a znovu povolí samoobslužné resetování hesla a zpětný zápis hesla.

Opravili jsme chybu v nástroji pro kompresi chyb synchronizace, který nezpracovával náhradní znaky správně.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>Vyšetřujeme incident, kdy někteří zákazníci zažívají problém s existujícími zařízeními pro připojení hybridního Azure AD po upgradu na tuto verzi Služby Azure AD Connect. Doporučujeme zákazníkům, kteří nasadili hybridní Azure AD připojit odložit upgrade na tuto verzi, dokud hlavní příčina těchto problémů jsou plně pochopeny a zmírnit. Další informace budou poskytnuty co nejdříve.

>[!IMPORTANT]
>V této verzi Azure AD Connect někteří zákazníci mohou vidět některé nebo všechny jejich zařízení s Windows zmizí z Azure AD. To není důvodkem k obavám, protože tyto identity zařízení nejsou používány službou Azure AD během autorizace podmíněného přístupu. Další informace najdete [v tématu Principy Azure AD Connect 1.4.xx.x zařízení disappearnce](reference-connect-device-disappearance.md)


### <a name="release-status"></a>Stav verze
25. 9/2019: Vydáno pouze pro automatický upgrade.

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
- Nové nástroje pro odstraňování problémů pomáhají řešit scénáře "uživatel není synchronizován", "skupina není synchronizována" nebo "člen skupiny nesynchronizuje".
- Přidání podpory pro národní cloudy ve skriptu pro řešení potíží služby AAD Connect 
- Zákazníci by měli být informováni, že zastaralé koncové body wmi pro MIIS_Service byly nyní odebrány. Všechny operace služby WMI by nyní měly být prováděny pomocí rutin PS.
- Zlepšení zabezpečení obnovením omezenédelegace na objekt AZUREADSSOACC
- Při přidávání nebo úpravě pravidla synchronizace, pokud existují nějaké atributy používané v pravidle, které jsou ve schématu konektoru, ale nejsou přidány do konektoru, atributy automaticky přidány do konektoru. Totéž platí pro typ objektu, který pravidlo ovlivňuje. Pokud je něco přidáno do konektoru, konektor bude označen pro úplný import v dalším cyklu synchronizace.
- Použití správce rozlehlé sítě nebo domény jako účtu konektoru již není v nových nasazeních Připojení AAD podporováno. Aktuální nasazení služby AAD Connect pomocí správce rozlehlé sítě nebo domény jako účtu konektoru nebudou touto verzí ovlivněna.
- Ve Správci synchronizace se spustí úplná synchronizace při vytváření/úpravě/odstranění pravidla. Vyskakovací okno se zobrazí na jakékoli změně pravidla upozorňující uživatele, pokud bude spuštěn úplný import nebo úplná synchronizace.
- Přidány kroky zmírnění chyb hesel na stránku "konektory > vlastnosti > připojení"
- Na stránce vlastností konektoru bylo přidáno upozornění na vyřazení pro správce synchronizačních služeb. Toto upozornění upozorní uživatele, že změny by měly být provedeny prostřednictvím průvodce AADC.
- Byla přidána nová chyba u problémů se zásadami hesla uživatele.
- Zabránit chybné konfiguraci filtrování skupin podle doména mise a filtrů ou. Filtrování skupin zobrazí chybu, když je doména nebo ou doména zadané skupiny již odfiltrována, a zabrání uživateli v pohybu vpřed, dokud nebude problém vyřešen.
- Uživatelé již nemohou vytvářet konektor pro službu Active Directory Domain Services nebo službu Windows Azure Active Directory v uživatelském rozhraní Správce synchronizačních služeb.
- Opravena přístupnost vlastních ovládacích prvků ui ve Správci synchronizačních služeb.
- Povoleno šest úloh správy federace pro všechny metody přihlášení v Azure AD Connect.  (Dříve byla pro všechna přihlášení k dispozici pouze úloha Aktualizovat certifikát TLS/SSL služby AD FS.)
- Přidáno upozornění při změně metody přihlášení z federace na PHS nebo PTA, že všechny domény Azure AD a uživatelé budou převedeny na spravované ověřování.
- Odebrané certifikáty pro podepisování tokenů z úlohy Obnovení důvěryhodnosti Azure AD a AD FS a přidání samostatné dílčí úlohy k aktualizaci těchto certifikátů.
- Byl přidán nový úkol správy federace s názvem Správa certifikátů, který má dílčí úkoly pro aktualizaci certifikátů TLS nebo podpisových certifikátů tokenů pro farmu služby AD FS.
- Byl přidán nový dílčí úkol správy federace s názvem "Zadat primární server", který správcům umožňuje určit nový primární server pro farmu služby AD FS.
- Byl přidán nový úkol správy federace s názvem Správa serverů, který má dílčí úkoly pro nasazení serveru služby AD FS, nasazení serveru proxy webových aplikací a určení primárního serveru.
- Byl přidán nový úkol správy federace s názvem "Zobrazit konfiguraci federace", která zobrazuje aktuální nastavení služby AD FS.  (Z důvodu tohoto přidání byla nastavení služby AD FS odebrána ze stránky Kontrola řešení.)

### <a name="fixed-issues"></a>Oprava potíží
- Vyřešen problém s chybou synchronizace pro scénář, kdy objekt uživatele, který přebírá odpovídající objekt kontaktu, má vlastní odkaz (např. uživatel je jejich vlastní správce).
- Vyskakovací okno nápovědy se nyní zobrazuje při zaostření na klávesnici.
- Pro automatický upgrade, pokud nějaká konfliktní aplikace běží od 6 hodin, utečte ji a pokračujte v upgradu.
- Omezte počet atributů, které může zákazník vybrat, na 100 na objekt při výběru rozšíření adresáře. Tím zabráníte chybě dochází během exportu jako Azure má maximálně 100 atributy rozšíření na objekt.
- Opravena chyba, aby byl skript služby AD Connectivity robustnější
- Opravena chyba, která znepřesňovala instalaci adconnectu v počítači pomocí existující služby WCF pojmenovaných kanálů.
- Vylepšená diagnostika a řešení potíží týkajících se zásad skupiny, které neumožňují spuštění služby ADSync při počáteční instalaci.
- Opravena chyba, kdy byl zobrazovaný název počítače se systémem Windows napsán nesprávně.
- Opravte chybu, kdy byl typ operačního systému pro počítač se systémem Windows napsán nesprávně.
- Opravena chyba, kdy se neočekávaně synchronizovaly počítače, které nejsou systémy Windows 10. Všimněte si, že účinek této změny je, že počítače, které nebyly systémem Windows-10 dříve synchronizovány, budou nyní odstraněny. To nemá vliv na žádné funkce, protože synchronizace počítačů se systémem Windows se používá pouze pro hybridní připojení domény Azure AD, které funguje pouze pro zařízení s Windows-10.
- Do modulu ADSync PowerShell bylo přidáno několik nových (interních) rutin.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Existuje známý problém s upgradem Azure AD Connect ze starší verze na 1.3.21.0, kde portál O365 neodráží aktualizovanou verzi, i když Azure AD Connect úspěšně upgradována.
>
> Chcete-li tento problém vyřešit, je třeba`Set-ADSyncDirSyncConfiguration` importovat modul **AdSync** a potom spustit rutinu powershellu na serveru Azure AD Connect.  Můžete použít následující kroky:
>
>1. Otevření powershellu v režimu administator
>2. Spusťte `Import-Module "ADSync"`.
>3. Spusťte `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`.
 
### <a name="release-status"></a>Stav verze 

05/14/2019: Vydáno ke stažení

### <a name="fixed-issues"></a>Oprava potíží 

- Opravena chyba zabezpečení ve zvýšení úrovně oprávnění, která existuje v aplikaci Microsoft Azure Active Connect build 1.3.20.0.  Tato chyba zabezpečení může za určitých podmínek útočníkovi umožnit spuštění dvou rutin prostředí PowerShell v kontextu privilegovaného účtu a provádění privilegovaných akcí.  Tato aktualizace zabezpečení řeší problém zakázáním těchto rutin. Další informace naleznete v [tématu aktualizace zabezpečení](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000).

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>Stav verze 

04/24/2019: Vydáno ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení 

- Přidání podpory pro aktualizaci domény 
- Exchange Mail veřejné složky funkce jde GA 
- Zlepšit zpracování chyb průvodce pro selhání služby 
- Byl přidán odkaz upozornění na ui správce synchronizačníslužby na stránce vlastností konektoru. 
- Funkce zpětného zápisu sjednocených skupin je nyní ga 
- Vylepšená chybová zpráva sspr, když řadič domény chybí ovládací prvek LDAP 
- Přidána diagnostika chyb registru DCOM během instalace  
- Vylepšené sledování chyb PHS RPC 
- Povolit creds EA z podřízené domény 
- Povolit zadání názvu databáze během instalace (výchozí název ADSync)
- Upgrade na ADAL 3.19.8 pro vyzvednutí opravy WS-Trust pro ping a přidání podpory pro nové instance Azure 
- Upravit pravidla synchronizace skupiny tak, aby tok samAccountName, DomainNetbios a DomainFQDN do cloudu - potřebné pro deklarace identity 
- Upravené výchozí zpracování pravidel synchronizace – více se [přečtěte zde](how-to-connect-fix-default-rules.md).
- Byl přidán nový agent spuštěný jako služba systému Windows. Tento agent s názvem "Agent pro správu" umožňuje hlubší vzdálenou diagnostiku serveru Azure AD Connect, který pomáhá technikům společnosti Microsoft řešit potíže při otevření případu podpory. Tento agent není ve výchozím nastavení nainstalován a povolen.  Další informace o tom, jak nainstalovat a povolit agenta, najdete [v tématu Co je agent správy připojení Azure AD?](whatis-aadc-admin-agent.md). 
- Byla aktualizována licenční smlouva s koncovým uživatelem (EULA) 
- Přidána podpora automatického upgradu pro nasazení, která používají službu AD FS jako typ přihlášení.  Tím se také odstranil požadavek na aktualizaci ad FS Azure AD Předávající strany trust jako součást procesu upgradu. 
- Přidán úkol správy důvěryhodnosti Azure AD, který poskytuje dvě možnosti: analyzovat nebo aktualizovat vztah důvěryhodnosti a obnovit vztah důvěryhodnosti. 
- Změněno chování důvěryhodnosti důvěryhodné strany Azure AD Azure AD tak, aby vždy používá přepínač -SupportMultipleDomain (zahrnuje aktualizace důvěryhodnosti a domény Azure AD). 
- Změnilo se chování nové farmy služby Install FS tak, aby vyžadovalo certifikát .pfx odebráním možnosti použití předinstalovaného certifikátu.
- Byl aktualizován pracovní postup instalace nové farmy služby AD FS tak, aby umožňoval nasazení pouze 1 serveru Služby AD FS a 1 serveru WAP.  Všechny další servery budou provedeny po počáteční instalaci. 

### <a name="fixed-issues"></a>Oprava potíží 

- Oprava logiky opětovného připojení SQL pro službu ADSync 
- Oprava povolit čistou instalaci pomocí prázdné sql AOA DB 
- Oprava skriptu oprávnění ps pro upřesnění oprávnění GWB 
- Oprava chyb VSS pomocí LocalDB  
- Oprava zavádějící chybové zprávy, pokud typ objektu není v oboru 
- Opraven problém, kdy instalace prostředí Azure AD PowerShell na serveru může potenciálně způsobit konflikt sestavení s Azure AD Connect. 
- Opravena chyba PHS na pracovním serveru při aktualizaci pověření konektoru v uzlech správce synchronizačních služeb. 
- Opraveny některé nevracení paměti 
- Různé opravy automatického upgradu 
- Různé opravy exportu a nepotvrzeného zpracování importu 
- Opravena chyba při zpracování zpětného lomítka v doméně a filtrování OU 
- Byl opraven problém, kdy zastavení služby ADSync trvá déle než 2 minuty a způsobuje problém v době upgradu. 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>Stav verze

12/18/2018: Vydáno ke stažení

### <a name="fixed-issues"></a>Oprava potíží

Toto sestavení aktualizuje nestandardní konektory (například obecný konektor LDAP a obecný konektor SQL) dodávané s Azure AD Connect. Další informace o příslušných konektorech naleznete ve verzi 1.1.911.0 v [historii verzí konektoru](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history).


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>Stav verze
12/11/2018: Vydáno ke stažení

### <a name="fixed-issues"></a>Oprava potíží
Toto sestavení opravy hotfix umožňuje uživateli vybrat cílovou doménu v rámci zadané doménové struktury pro kontejner RegisteredDevices při povolení zpětného zápisu zařízení.  V předchozích verzích, které obsahují nové možnosti zařízení funkce (1.1.819.0 – 1.2.68.0), registereddevices umístění kontejneru byla omezena na kořenové doménové struktury a neumožňovala podřízené domény.  Toto omezení se projevilo pouze u nových nasazení – upgrady na místě nebyly ovlivněny.  

Pokud bylo na nový server nasazeno jakékoli sestavení obsahující aktualizovanou funkci Možnosti zařízení a bylo povoleno zpětné zápisování zařízení, budete muset ručně zadat umístění kontejneru, pokud jej nechcete v kořenové struktuře doménové struktury.  Chcete-li to provést, je třeba zakázat zpětný zápis zařízení a znovu povolit, což vám umožní zadat umístění kontejneru na stránce "Doménové struktury zpětného zápisu".



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>Stav verze 

11/30/2018: Vydáno ke stažení

### <a name="fixed-issues"></a>Oprava potíží

Toto sestavení opravy hotfix opravuje konflikt, kdy může dojít k chybě ověřování z důvodu nezávislé přítomnosti modulu MSOnline PowerShell Gallery na synchronizačním serveru.



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>Stav verze 

11/19/2018: Vydáno ke stažení

### <a name="fixed-issues"></a>Oprava potíží

Toto sestavení opravy hotfix opravuje regresi v předchozím sestavení, kde se při použití řadiče domény ADDS v systému Windows Server 2008/R2 nezdaří zpětné snížení hesla.

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>Stav verze 

10/25/2018: vydáno ke stažení

 
### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení 


- Byla změněna funkce zpětného zápisu atributu, aby bylo zajištěno, že hostovaná hlasová pošta funguje podle očekávání.  V určitých scénářích Azure AD přepsal atribut msExchUcVoicemailSettings během zpětného zápisu s nulovou hodnotou.  Azure AD už teď nebude vymazat místní hodnotu tohoto atributu, pokud není nastavena hodnota cloudu.
- Přidána diagnostika v průvodci Azure AD Connect k prozkoumání a identifikaci problémů s připojením k Azure AD. Tyto stejné diagnostiky lze také spustit přímo prostřednictvím prostředí Powershell pomocí rutiny Test- AdSyncAzureServiceConnectivity. 
- Přidána diagnostika v průvodci Azure AD Connect k prozkoumání a identifikaci problémů s připojením ke službě AD. Stejnou diagnostiku lze také spustit přímo prostřednictvím prostředí Powershell pomocí funkce Start-ConnectivityValidation v modulu ADConnectivityTools Powershell.  Další informace naleznete [v tématu Co je modul ADConnectivityTool PowerShell?](how-to-connect-adconnectivitytools.md)
- Byla přidána verze schématu služby AD, která je předběžně kontrolována pro hybridní připojení služby Azure Active Directory a zpětný zápis zařízení 
- Bylo změněno hledání atributů stránky rozšíření adresáře, aby se nerozlišují malá a velká písmena.
-   Přidána plná podpora pro TLS 1.2. Tato verze podporuje všechny ostatní protokoly, které jsou zakázány a pouze TLS 1.2 je povolena v počítači, kde je nainstalován aD Connect.  Další informace najdete v tématu [Vynucení TLS 1.2 pro Azure AD Connect](reference-connect-tls-enforcement.md)

 

### <a name="fixed-issues"></a>Oprava potíží   

- Opravena chyba, při které by se upgrade služby Azure AD Connect nezdaří, pokud by se používalo vždy zapnuté SQL. 
- Opravena chyba, která správně analyzuje názvy ouobjektů, které obsahují lomítko. 
- Byl opraven problém, kdy bylo předávací ověřování zakázáno pro čistou instalaci v pracovním režimu. 
- Opravena chyba, která bránila načtení modulu PowerShellu při spuštění nástrojů pro řešení potíží 
- Opravena chyba, která bránila zákazníkům používat číselné hodnoty v prvním znaku názvu hostitele. 
- Opravena chyba, kdy Azure AD Connect povolila neplatné oddíly a výběr kontejnerů 
- Opravena chybová zpráva "Neplatné heslo", když je povoleno přichystání služby Připevnění zabezpečení plochy. 
- Různé opravy chyb pro správu důvěryhodnosti ad FS  
- Při konfiguraci zpětného zápisu zařízení – opravena kontrola schématu, aby se hledala třída objektů msD-DeviceContainer (zavedená na WS2012 R2)

 
## <a name="118820"></a>1.1.882.0  

9/ 7 / 2018: vydáno ke stažení, nebude uvolněna pro automatický upgrade 

### <a name="fixed-issues"></a>Oprava potíží  

Upgrade připojení Azure AD se nezdaří, pokud je pro databázi ADSync nakonfigurovaná dostupnost SQL Always On Availability. Tato oprava hotfix řeší tento problém a umožňuje úspěšné upgradu. 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>Stav verze

21.8.2018: Vydáno ke stažení a automatickému upgradu. 

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

- Integrace Federate ping v Azure AD Connect je teď k dispozici pro obecnou dostupnost. [Další informace o federované azure ad s Ping Federate](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#federation-with-pingfederate)
- Azure AD Connect teď vytvoří zálohu důvěryhodnosti Azure AD ve službě AD FS při každém provedení aktualizace a uloží ji do samostatného souboru pro snadné obnovení v případě potřeby. [Další informace o nových funkcích a správě důvěryhodnosti Azure AD ve službě Azure AD Connect](https://aka.ms/fedtrustinaadconnect).
- Nové nástroje pro odstraňování potíží pomáhají řešit potíže se změnou primární e-mailové adresy a skrytím účtu z globálního seznamu adres
- Azure AD Connect byla aktualizována tak, aby zahrnovala nejnovější nativní klientsql serveru 2012
- Když přepnete přihlášení uživatele na synchronizaci hodnot hash hesel nebo předávací ověřování v úloze Změnit přihlášení uživatele, je ve výchozím nastavení povoleno bezešvé jednotné přihlašování.
- Přidána podpora pro Windows Server Essentials 2019
- Agent Azure AD Connect Health byl aktualizován na nejnovější verzi 3.1.7.0
- Pokud instalační program během upgradu zjistí změny výchozích pravidel synchronizace, zobrazí se před přepsáním změněných pravidel s upozorněním na správce. To umožní uživateli provést nápravná opatření a pokračovat později. Staré chování: Pokud došlo k nějaké upravené pravidlo out-of-box pak ruční upgrade byl přepsání těchto pravidel bez udání upozornění pro uživatele a synchronizace plánovač byl zakázán bez informování uživatele. Nové chování: Uživatel i dotázat se upozornění před přepsáním upravených pravidel synchronizace out-of-box. Uživatel bude mít možnost zastavit proces upgradu a pokračovat později po přijetí nápravných opatření.
- Poskytněte lepší zpracování problému s dodržováním předpisů FIPS, poskytuje chybovou zprávu pro generování hash MD5 v prostředí kompatibilní mfips a odkaz na dokumentaci, která poskytuje řešení tohoto problému.
- Aktualizace rozhraní pro zlepšení federačních úkolů v průvodci, které jsou nyní pod samostatnou podskupinou pro federaci. 
- Všechny další úkoly federace jsou nyní seskupeny do jedné podnabídky pro snadné použití.
- Nový přepracovaný modul ADSyncConfig Posh (AdSyncConfig.psm1) s novými funkcemi oprávnění služby AD přesunutý ze starého rozhraní ADSyncPrep.psm1 (který může být v brzké době zastaralá)

### <a name="fixed-issues"></a>Oprava potíží 

- Opravena chyba, kdy server AAD Connect po upgradu na rozhraní .NET 4.7.2 vykazoval vysoké využití procesoru
- Opravena chyba, která občas vyvoláchybu pro automaticky vyřešený problém s zablokováním SQL
- Opraveno několik problémů s usnadněním přístupu pro Editor pravidel synchronizace a Správce synchronizačních služeb  
- Opravena chyba, kdy Azure AD Connect nemohl získat informace o nastavení registru
- Opravena chyba, která způsobila problémy, když uživatel přešel v průvodci dopředu/zpět
- Opravena chyba, která zabránila chybě způsobené nesprávným předáním více vláken v průvodci
- Když stránka filtrování synchronizace skupin narazí na chybu LDAP při řešení skupin zabezpečení, Azure AD Connect teď vrátí výjimku s plnou věrností.  Hlavní příčina výjimky odkazu je stále neznámá a bude řešena jinou chybou.
-  Opravena chyba, kdy nebyla správně nastavena oprávnění pro klíče STK a NGC (atribut ms-DS-KeyCredentialLink na objektech User/Device pro WHfB).     
- Opravena chyba, kdy nebyla správně volána funkce Set-ADSyncRestrictedPermissions
-  Přidání podpory pro udělení oprávnění k zpětnému zápisu skupiny v průvodci instalací služby AADConnect
- Při změně metody přihlášení z synchronizace hash hesla na službu AD FS nebyla synchronizace hash hesla zakázána.
- Přidáno ověření adres IPv6 v konfiguraci služby AD FS
- Byla aktualizována zpráva s oznámením, že existuje existující konfigurace.
- Zpětný zápis zařízení se nezdaří rozpoznat kontejner v nedůvěryhodné doménové struktury. Tato zpráva byla aktualizována, aby poskytovala lepší chybovou zprávu a odkaz na příslušnou dokumentaci
- Zrušení výběru ou položky a následné synchronizace/zpětného zápisu odpovídající této akci poskytuje obecnou chybu synchronizace. To bylo změněno vytvořit srozumitelnější chybovou zprávu.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Stav verze

14.5.5.2018: Vydáno pro automatický upgrade a stahování.

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

Nové funkce a vylepšení

- Tato verze zahrnuje public preview integrace PingFederate v Azure AD Connect. Díky této verzi můžou zákazníci snadno a spolehlivě nakonfigurovat své prostředí Azure Active Directory tak, aby využívalo PingFederate jako svého poskytovatele federace. Další informace o používání této nové funkce naleznete v naší [online dokumentaci](plan-connect-user-signin.md#federation-with-pingfederate). 
- Byl aktualizován nástroj poradce při potížích průvodce azure apřipojením, kde nyní analyzuje další scénáře chyb, jako jsou propojené poštovní schránky a dynamické skupiny služby AD. Další informace o nástroji pro odstraňování problémů naleznete [zde](tshoot-connect-objectsync.md).
- Konfigurace zpětného zápisu zařízení se teď spravuje výhradně v rámci Průvodce připojením Azure AD.
- Je přidán nový modul prostředí PowerShell s názvem ADSyncTools.psm1, který lze použít k řešení problémů s připojením SQL a různých dalších nástrojů pro řešení potíží. Přečtěte si více o modulu ADSyncTools [zde](tshoot-connect-tshoot-sql-connectivity.md). 
- Byla přidána nová další úloha "Konfigurovat možnosti zařízení". Tuto úlohu můžete použít ke konfiguraci následujících dvou operací: 
  - **Hybridní připojení Azure AD**: Pokud vaše prostředí má místní stopu služby AD a chcete také těžit z možností poskytovaných službou Azure Active Directory, můžete implementovat hybridní zařízení připojená k Azure AD. Jedná se o zařízení připojená k místní službě Active Directory i k Azure Active Directory.
  - **Zpětný zápis zařízení**: Zpětný zápis zařízení se používá k povolení podmíněného přístupu na základě zařízení chráněných zařízení služby AD FS (2012 R2 nebo vyšší).

    >[!NOTE] 
    > - Možnost povolit zpětný zápis zařízení z možností vlastní synchronizace bude šedě. 
    > -  Modul prostředí PowerShell pro adprep je s touto verzí již zastaral.



### <a name="fixed-issues"></a>Oprava potíží 

- Tato verze aktualizuje instalaci sql serveru Express na SQL Server 2012 SP4, která mimo jiné poskytuje opravy několika chyb zabezpečení.  Další informace o sql serveru 2012 SP4 naleznete [zde.](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information)
- Zpracování pravidla synchronizace: Odchozí pravidla synchronizace spojení bez podmínky spojení by měla být de-li de-použít, pokud nadřazené pravidlo synchronizace již není použitelné
- V u rozhraní Synchronizace správce služeb a v Editoru pravidel synchronizace bylo použito několik oprav usnadnění.
- Průvodce připojením Azure AD: Chyba při vytváření účtu konektoru Služby AD, když je Azure AD Connect v pracovní skupině
- Průvodce připojením Azure AD: Na přihlašovací stránce Služby Azure AD se zobrazí zaškrtávací políčko ověření vždy, když dojde k neshodě v doménách služby AD a doménách Azure AD Verified
- Automatická inovace powershellové opravy pro správné nastavení stavu automatického upgradu v určitých případech po pokusu o automatický upgrade.
- Průvodce připojením Azure AD: Aktualizovaná telemetrická data pro zachycení dříve chybějících informací
- Průvodce připojením azure aslužby: Při použití **úlohy změnit přihlášení uživatele** k přepnutí ze služby AD FS na předávací ověřování byly provedeny následující změny:
    - Předávací agent ověřování je nainstalovaný na serveru Azure AD Connect a předací ověřování je povolena před převodem domén z federované na spravované.
    - Uživatelé již nejsou převedeny z federované na spravované. Jsou převedeny pouze domény.
- Průvodce připojením azure ad: AD FS Multi Domain Regex není správný, když má hlavní název uživatele pro podporu speciálních znaků aktualizaci Regex speciální znak
- Průvodce připojením azure ad: odebrání falešné zprávy "Konfigurovat atribut zdrojové kotvy", když se žádná změna 
- Průvodce připojením Azure AD: Podpora služby AD FS pro scénář duální federace
- Průvodce připojením azure aslužby: Deklarace identity služby AD FS se neaktualizují pro přidanou doménu při převodu spravované domény na federované
- Průvodce připojením Azure AD: Během zjišťování nainstalovaných balíčků najdeme zastaralé produkty související se službou Dirsync/Azure AD Sync/Azure AD Connect. Nyní se pokusíme odinstalovat zastaralé produkty.
- Průvodce připojením Azure AD: Oprava mapování chybových zpráv při selhání instalace agenta ověřování průchodu
- Průvodce připojením azure aslužby: Odebrán kontejner Konfigurace ze stránky filtrování ou domény
- Instalace synchronizačního modulu: odebrání zbytečné starší logiky, která občas selhala z instalace modulu Sync Engine msi
- Průvodce připojením azure ad: Oprava textu nápovědy k vyskakovacímu okně na stránce Volitelné funkce pro synchronizaci hash hesel
- Modul runtime synchronizačního modulu: Opravte scénář, kdy má objekt CS importovaný odstranit a pravidla synchronizace se pokoušejí objekt znovu zřídit.
- Modul runtime synchronizačního modulu: Přidání odkazu nápovědy pro průvodce odstraňováním potíží s připojením online do protokolu událostí pro chybu importu
- Modul runtime synchronizačního modulu: Snížené využití paměti plánovače synchronizace při výčtu konektorů
- Průvodce připojením Azure AD: Oprava problému při řešení vlastního účtu služby Synchronizace, který nemá žádná oprávnění ke čtení ve službě AD
- Průvodce připojením azure aslužby: Zlepšení protokolování výběrů filtrování domény a ou.
- Průvodce připojením azure aslužby: Služba AD FS Přidání výchozích deklarací vztahů důvěryhodnosti federace vytvořených pro scénář vícefaktorové informace
- Průvodce připojením Azure AD: Nasazení WAP služby AD FS: Přidání serveru se nepodaří použít nový certifikát
- Průvodce připojením azure ad: Výjimka dsso, když onPremCredentials nejsou inicializovány pro doménu 
- Přednostně toku AD rozlišovací Name atribut z aktivního uživatele objektu.
- Opravena kosmetická chyba byla priorita prvního pravidla synchronizace OOB byla nastavena na 99 místo 100



## <a name="117510"></a>1.1.751.0
Stav 4/12/2018: Vydáno pouze ke stažení

>[!NOTE]
>Tato verze je oprava hotfix pro Azure AD Connect

### <a name="azure-ad-connect-sync"></a>Synchronizace služby Azure AD Connect
#### <a name="fixed-issues"></a>Oprava potíží
Opraven problém byly automatické zjišťování instance Azure pro klienty Číny byl občas nedaří.  

### <a name="ad-fs-management"></a>Správa ad FS
#### <a name="fixed-issues"></a>Oprava potíží

V logice opakování konfigurace došlo k potížím, který by vedl k argumentu ArgumentException, který uvádí, že položka se stejným klíčem již byla přidána.  To by způsobilo selhání všech operací opakování.

## <a name="117500"></a>1.1.750.0
Stav 3/22/2018: Vydáno pro automatický upgrade a stažení.
>[!NOTE]
>Po dokončení upgradu na tuto novou verzi se automaticky spustí úplná synchronizace a úplný import pro konektor Azure AD a úplnou synchronizaci pro konektor AD. Vzhledem k tomu, že to může nějakou dobu trvat, v závislosti na velikosti prostředí Azure AD Connect, ujistěte se, že jste provedli nezbytné kroky k podpoře tohoto nebo pozdržet upgrade, dokud jste našli vhodný okamžik, aby tak učinily.

>[!NOTE]
>"Funkce automatického upgradu byla nesprávně zakázána pro některé klienty, kteří nasadili sestavení později než 1.1.524.0. Chcete-li zajistit, aby vaše instance Azure AD Connect byla stále způsobilá pro automatický upgrade, spusťte následující rutinu prostředí PowerShell: "Set-ADSyncAutoUpgrade -AutoupGradeState Enabled"


### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Oprava potíží

* Rutina Set-ADSyncAutoUpgrade by dříve zablokovala automatický upgrade, pokud je stav automatického upgradu nastaven na Pozastaveno. Tato funkce se nyní změnila, takže neblokuje automatický upgrade budoucích sestavení.
* Byla změněna možnost stránky **přihlášení uživatele** "Synchronizace hesel" na "Synchronizace hash hesel".  Azure AD Connect synchronizuje hashe hesla, nikoli hesla, takže to zarovná s co skutečně dochází.  Další informace najdete [v tématu Implementace synchronizace hash hesel se synchronizací Azure AD Connect](how-to-connect-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
Stav: Vydáno pro vybrané zákazníky

>[!NOTE]
>Po dokončení upgradu na tuto novou verzi se automaticky spustí úplná synchronizace a úplný import pro konektor Azure AD a úplnou synchronizaci pro konektor AD. Vzhledem k tomu, že to může nějakou dobu trvat, v závislosti na velikosti prostředí Azure AD Connect, ujistěte se, že jste provedli nezbytné kroky k podpoře tohoto nebo pozdržet upgrade, dokud jste našli vhodný okamžik, aby tak učinily.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Oprava potíží
* Oprava časového okna na úlohách na pozadí pro stránku Filtrování oddílů při přepnutí na další stránku

* Opravena chyba, která způsobila narušení přístupu během vlastní akce ConfigDB.

* Opravena chyba, která se má zotavit z časového limitu připojení SQL.

* Opravena chyba, kdy certifikáty se zástupnými znaky sítě SAN selhaly při kontrole předpokladů.

* Opravena chyba, která způsobila selhání programu miiserver.exe během exportu konektoru Azure AD.

* Opravena chyba, která chybná chyba pokus o heslo přihlášen a dc při spuštění průvodce Azure AD Connect změnit konfiguraci.


#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Přidání nastavení ochrany osobních údajů pro obecné nařízení o ochraně osobních údajů (GDPR).  Další informace naleznete v článku [zde](reference-connect-user-privacy.md).

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* telemetrie aplikace - admin může tuto třídu dat zapnout/vypnout podle vůle

* Data o stavu Azure AD – správce musí navštívit portál stavu a řídit jejich nastavení stavu.
   Po změně zásad služby agenti přečte a vynucují ji.

* Přidány akce konfigurace zpětného zápisu zařízení a indikátor průběhu pro inicializaci stránky

* Vylepšená obecná diagnostika se zprávou HTML a úplným sběrem dat v sestavě ZIP-Text / HTML

* Vylepšena spolehlivost automatického upgradu a přidána další telemetrie, aby bylo zajištěno, že bude možné určit stav serveru

* Omezení oprávnění dostupných na privilegované účty v účtu služby AD Connector

  * U nových instalací průvodce omezí oprávnění, která mají privilegované účty k účtu MSOL po vytvoření účtu MSOL.

Změny se postarají o následující:
1. Expresní instalace
2. Vlastní instalace s účtem automatického vytváření
3. Instalační program byl změněn tak, aby při čisté instalaci služby Azure AD Connect nevyžadoval oprávnění SA

* Byl přidán nový nástroj pro řešení problémů se synchronizací určitého objektu. Je k dispozici v části Poradce při potížích s synchronizací objektů v Průvodci připojením Azure AD Poradce při potížích s další úlohou. V současné době nástroj kontroluje následující:

  * Neshoda userPrincipalName mezi synchronizovaným objektem uživatele a uživatelským účtem v tenantu Azure AD.
  * Pokud je objekt filtrován ze synchronizace z důvodu filtrování domény
  * Pokud je objekt filtrován ze synchronizace z důvodu filtrování organizační jednotky (OU)

* Byl přidán nový nástroj pro synchronizaci aktuální hodnoty hash hesla uložené v místní službě Active Directory pro konkrétní uživatelský účet.

Nástroj nevyžaduje změnu hesla. Je k dispozici v části Poradce při potížích s synchronizací hash hesla v Průvodci připojením Azure AD Poradce při potížích s další úlohou.






## <a name="116540"></a>1.1.654.0
Stav: 12.

>[!NOTE]
>Tato verze je oprava hotfix související se zabezpečením pro Azure AD Connect

### <a name="azure-ad-connect"></a>Azure AD Connect
Do služby Azure AD Connect verze 1.1.654.0 (a po) bylo přidáno vylepšení, aby bylo zajištěno, že doporučené změny oprávnění popsané v části [Uzamčení přístupu k účtu služby AD DS](#lock) se automaticky použijí, když Azure AD Connect vytvoří účet služby AD DS. 

- Při nastavování Služby Azure AD Connect může instalační správce buď poskytnout existující účet služby AD DS, nebo nechat Azure AD Connect účet automaticky vytvořit. Změny oprávnění se automaticky použijí na účet služby AD DS, který je vytvořen službou Azure AD Connect během instalace. Nejsou použity na existující účet služby AD DS poskytovaný instalačním správcem.
- Pro zákazníky, kteří upgradovali ze starší verze služby Azure AD Connect na 1.1.654.0 (nebo po), nebudou změny oprávnění zpětně použity na existující účty služby AD DS vytvořené před upgradem. Budou použity pouze pro nové účty ad DS vytvořené po upgradu. K tomu dochází, když přidáváte nové doménové struktury služby AD, které mají být synchronizovány do služby Azure AD.

>[!NOTE]
>Tato verze pouze odstraňuje chybu zabezpečení pro nové instalace Služby Azure AD Connect, kde je účet služby vytvořen procesem instalace. U stávajících instalací nebo v případech, kdy účet zadáte sami, byste měli zajistit, aby tato chyba zabezpečení neexistovala.

#### <a name="lock-down-access-to-the-ad-ds-account"></a><a name="lock"></a>Uzamknutí přístupu k účtu ad DS
Uzamkněte přístup k účtu ad DS implementací následujících změn oprávnění v místním službě AD:  

*   Zakázat dědičnost zadaného objektu
*   Odeberte všechny ace na konkrétní objekt, s výjimkou ACE specifické pro SELF. Chceme zachovat výchozí oprávnění neporušená, pokud jde o SELF.
*   Přiřaďte tato konkrétní oprávnění:

Typ     | Name (Název)                          | Access               | Platí pro
---------|-------------------------------|----------------------|--------------|
Povolit    | SYSTEM                        | Úplné řízení         | Tento objekt  |
Povolit    | Enterprise Admins             | Úplné řízení         | Tento objekt  |
Povolit    | Domain Admins                 | Úplné řízení         | Tento objekt  |
Povolit    | Správci                | Úplné řízení         | Tento objekt  |
Povolit    | Podnikové řadiče domény | Obsah seznamu        | Tento objekt  |
Povolit    | Podnikové řadiče domény | Číst všechny vlastnosti  | Tento objekt  |
Povolit    | Podnikové řadiče domény | Oprávnění ke čtení     | Tento objekt  |
Povolit    | Authenticated Users           | Obsah seznamu        | Tento objekt  |
Povolit    | Authenticated Users           | Číst všechny vlastnosti  | Tento objekt  |
Povolit    | Authenticated Users           | Oprávnění ke čtení     | Tento objekt  |

Chcete-li zpřísnit nastavení účtu služby AD DS, můžete spustit [tento skript prostředí PowerShell](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978). Skript prostředí PowerShell přiřadí výše uvedená oprávnění účtu služby AD DS.

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>Skript prostředí PowerShell pro zpřísnění již existujícího účtu služby

Chcete-li použít skript Prostředí PowerShell, chcete-li použít tato nastavení, na již existující účet služby AD DS (éter poskytovaný vaší organizací nebo vytvořený předchozí instalací služby Azure AD Connect, stáhněte si skript z výše uvedeného odkazu.

##### <a name="usage"></a>Použití:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Kde 

**$ObjectDN** = Účet služby Active Directory, jehož oprávnění je třeba zpřísnit.

**$Credential** = pověření správce, který má potřebná oprávnění k omezení oprávnění k účtu $ObjectDN. Tato oprávnění jsou obvykle v držení správce rozlehlé sítě nebo domény. Chcete-li se vyhnout selhání vyhledávání účtu, použijte plně kvalifikovaný název domény účtu správce. Příklad: contoso.com\admin.

>[!NOTE] 
>$credential. UserName by měl být ve formátu FQDN\uživatelské jméno. Příklad: contoso.com\admin 

##### <a name="example"></a>Příklad:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Byla tato chyba zabezpečení použita k získání neoprávněného přístupu?

Chcete-li zjistit, zda tato chyba zabezpečení byla použita k ohrožení konfigurace služby Azure AD Connect, měli byste ověřit datum posledního obnovení hesla účtu služby.  Pokud časové razítko v neočekávané, další šetření, prostřednictvím protokolu událostí, pro tuto událost resetování hesla, by měla být provedena.

Další informace naleznete [v informačním zpravodaji zabezpečení společnosti Microsoft 4056318](https://technet.microsoft.com/library/security/4056318)

## <a name="116490"></a>1.1.649.0
Stav: 27.

>[!NOTE]
>Toto sestavení není k dispozici zákazníkům prostřednictvím funkce automatického upgradu služby Azure AD Connect.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Oprava potíží 
* Opraven problém s kompatibilitou verzí mezi Azure AD Connect a Agentem stavu Azure AD Connect (pro synchronizaci). Tento problém se týká zákazníků, kteří provádějí upgrade služby Azure AD Connect na místo na verzi 1.1.647.0, ale aktuálně má agenta stavu verze 3.0.127.0. Po upgradu agenta stavu už nemůže odesílat data o synchronizační službě Azure AD Connect do služby Azure AD Health. S touto opravou se agent a agent a stav verze 3.0.129.0 nainstaluje během upgradu Azure AD Connect na místě. Agent stavu verze 3.0.129.0 nemá problém s kompatibilitou s Azure AD Connect verze 1.1.649.0.


## <a name="116470"></a>1.1.647.0
Stav: 19.

> [!IMPORTANT]
> Existuje známý problém s kompatibilitou mezi Azure AD Connect verze 1.1.647.0 a Agent aD Connect Health Agent (pro synchronizaci) verze 3.0.127.0. Tento problém brání agenta stavu odesílání dat o službě synchronizace připojení Azure AD (včetně chyb synchronizace objektů a dat historie) do služby Azure AD Health. Než začnete ručně upgradovat nasazení služby Azure AD Connect na verzi 1.1.647.0, ověřte aktuální verzi agenta Azure AD Connect Health agenta nainstalovaného na serveru Azure AD Connect. Můžete tak učinit tak, že přejdete do *Ovládacích panelů → Přidat odebrat programy* a vyhledejte aplikaci *Microsoft Azure AD Connect Health Agent for Sync*. Pokud je jeho verze 3.0.127.0, doporučujeme počkat na další verzi Azure AD Connect, která bude k dispozici před upgradem. Pokud verze agenta stavu není 3.0.127.0, je v pořádku pokračovat v ručním upgradu na místě. Všimněte si, že tento problém nemá vliv na upgrade swing nebo zákazníky, kteří provádějí novou instalaci Služby Azure AD Connect.
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Oprava potíží
* Byl opraven problém s úlohou změnit přihlášení *uživatele* v průvodci Azure AD Connect:

  * K problému dochází, pokud máte existující nasazení Služby Azure AD Connect s **synchronizací**hesel povoleno a pokoušíte se nastavit metodu přihlášení uživatele jako *předávací ověřování*. Před použitím změny průvodce nesprávně zobrazí výzvu*Zakázat synchronizaci hesel.* Synchronizace hesel však zůstane povolena i po použití změny. Pomocí této opravy průvodce již nezobrazuje výzvu.

  * Podle návrhu průvodce nezakáže synchronizaci hesel při aktualizaci metody přihlášení uživatele pomocí *úlohy změnit přihlášení uživatele.* To to je, aby se zabránilo narušení pro zákazníky, kteří chtějí zachovat synchronizaci hesel, i když jsou povolení předávací ověřování nebo federace jako jejich primární metoda přihlášení uživatele.
  
  * Chcete-li po aktualizaci metody přihlášení uživatele zakázat synchronizaci hesel, je nutné v průvodci provést úlohu *Přizpůsobit konfiguraci synchronizace.* Když přejdete na stránku *Volitelné funkce,* zaškrtněte *políčko Synchronizace hesel.*
  
  * Všimněte si, že stejný problém také dochází, pokud se pokusíte povolit nebo zakázat bezešvé jednotné přihlašování. Konkrétně máte existující nasazení Azure AD Connect s povolenou synchronizací hesel a metoda přihlášení uživatele je již nakonfigurovaná jako *předávací ověřování*. Pomocí *úlohy změnit přihlášení uživatele* se pokusíte zaškrtnutí/zrušení zaškrtnutí *možnosti Povolit bezproblémové jednotné přihlašování,* zatímco metoda přihlášení uživatele zůstane nakonfigurovaná jako "Předávací ověřování". Před použitím změny průvodce nesprávně zobrazí výzvu*Zakázat synchronizaci hesel.* Synchronizace hesel však zůstane povolena i po použití změny. Pomocí této opravy průvodce již nezobrazuje výzvu.

* Byl opraven problém s úlohou změnit přihlášení *uživatele* v průvodci Azure AD Connect:

  * K problému dochází, pokud máte existující nasazení Služby Azure AD Connect s synchronizací hesel **zakázáno**a pokoušíte se nastavit metodu přihlášení uživatele jako *předávací ověřování*. Po použití změny průvodce povolí předávací ověřování i synchronizaci hesel. Pomocí této opravy průvodce již nepovoluje synchronizaci hesel.

  * Dříve byla synchronizace hesel předpokladem pro povolení předávacího ověřování. Pokud nastavíte metodu přihlášení uživatele jako *předávací ověřování*, průvodce povolí předávací ověřování i synchronizaci hesel. Nedávno byla synchronizace hesel odebrána jako předpoklad. Jako součást Azure AD Connect verze 1.1.557.0 byla provedena změna služby Azure AD Connect, aby se nepovolila synchronizace hesel při nastavení metody přihlášení uživatele jako *předávací ověřování*. Změna však byla použita pouze pro instalaci Azure AD Connect. S touto opravou se stejná změna použije také pro úlohu *přihlášení uživatele změnit.*
  
  * Všimněte si, že stejný problém také dochází, pokud se pokusíte povolit nebo zakázat bezešvé jednotné přihlašování. Konkrétně máte existující nasazení Azure AD Connect s synchronizací hesel zakázáno a metoda přihlášení uživatele je již nakonfigurovaná jako *předávací ověřování*. Pomocí *úlohy změnit přihlášení uživatele* se pokusíte zaškrtnutí/zrušení zaškrtnutí *možnosti Povolit bezproblémové jednotné přihlašování,* zatímco metoda přihlášení uživatele zůstane nakonfigurovaná jako "Předávací ověřování". Po použití změny průvodce povolí synchronizaci hesel. Pomocí této opravy průvodce již nepovoluje synchronizaci hesel. 

* Byl opraven problém, který způsoboval selhání upgradu služby Azure AD Connect s chybou *"Nelze upgradovat synchronizační službu*". Dále synchronizační služba již nelze spustit s chybou události "*Služba nebyla schopna spustit, protože verze databáze je novější než verze nainstalovaných binárních souborů*". K problému dochází, když správce provádějící upgrade nemá oprávnění sysadmin k serveru SQL, který je používán Azure AD Connect. S touto opravou Azure AD Connect vyžaduje pouze správce mít db_owner oprávnění k databázi ADSync během upgradu.

* Byl opraven problém s upgradem služby Azure AD Connect, který ovlivnil zákazníky, kteří povolili [bezproblémové jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). Po upgradu služby Azure AD Connect se bezešvé jednotné přihlašování nesprávně zobrazí jako zakázané v průvodci Azure AD Connect, i když tato funkce zůstane povolená a plně funkční. S touto opravou se funkce nyní zobrazí správně jako povolená v průvodci.

* Byl opraven problém, který způsoboval, že průvodce Azure AD Connect vždy zobrazoval výzvu Konfigurace*zdrojové kotvy*na stránce *Připraveno ke konfiguraci,* a to i v případě, že nebyly provedeny žádné změny související se zdrojovou kotvou.

* Při provádění ručního upgradu na místě služby Azure AD Connect je zákazník povinen poskytnout přihlašovací údaje globálního správce odpovídajícího klienta Azure AD. Dříve upgrade může pokračovat i v případě, že přihlašovací údaje globálního správce patřily do jiného klienta Azure AD. Při dokončení upgradu se zdá být úspěšně dokončena, některé konfigurace nejsou správně trvalé s upgradem. S touto změnou průvodce zabrání pokračování upgradu, pokud poskytnuté přihlašovací údaje neodpovídají tenantovi Azure AD.

* Odebrala redundantní logiku, která zbytečně restartovala službu Azure AD Connect Health na začátku ručníinovace.


#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Přidána logika pro zjednodušení kroků potřebných k nastavení Azure AD Connect s Microsoft Germany Cloud. Dříve jste museli aktualizovat konkrétní klíče registru na serveru Azure AD Connect, aby správně fungoval s Microsoft Germany Cloud, jak je popsáno v tomto článku. Azure AD Connect teď může automaticky zjistit, jestli je váš tenant v Microsoft Germany Cloud na základě přihlašovacích údajů globálního správce poskytnutých během instalace.

### <a name="azure-ad-connect-sync"></a>Synchronizace služby Azure AD Connect
> [!NOTE]
> Poznámka: Služba synchronizace má rozhraní služby WMI, které umožňuje vyvíjet vlastní plánovač. Toto rozhraní je nyní zastaralé a bude odebráno z budoucích verzí Služby Azure AD Connect dodaných po 30. Zákazníci, kteří chtějí přizpůsobit plán synchronizace, by měli používat [předdefinovaný plánovač](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler).

#### <a name="fixed-issues"></a>Oprava potíží
* Když průvodce připojením Azure AD vytvoří účet konektoru Služby AD, který je nutný k synchronizaci změn z místní služby Active Directory, nepřiřadí mu správně oprávnění potřebné ke čtení objektů PublicFolder. Tento problém se týká instalace Express i vlastní instalace. Tato změna řeší problém.

* Byl opraven problém, který způsoboval, že se stránka Průvodce odstraňováním potíží s Průvodce azure ad připojením nezoakliovala správně pro správce spuštěné z Windows Serveru 2016.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Při řešení potíží s synchronizací hesel pomocí stránky poradce při potížích s průvodcem Azure AD Connect stránka řešení potíží, stránka řešení potíží nyní vrátí stav specifický pro doménu.

* Dříve, pokud jste se pokusili povolit synchronizaci hash hesel, Azure AD Connect neověřuje, zda účet Konektor služby AD má požadovaná oprávnění k synchronizaci hodnot hash hesel z místní služby AD. Průvodce připojením Azure AD teď ověří a upozorní vás, pokud účet konektoru služby AD nemá dostatečná oprávnění.

### <a name="ad-fs-management"></a>Správa ad FS
#### <a name="fixed-issue"></a>Oprava potíží 
* Byl opraven problém související s používáním funkce [ms-DS-ConsistencyGuid jako source anchor.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) Tento problém se týká zákazníků, kteří nakonfigurovali *Federaci se spoje ad FS* jako metodou přihlášení uživatele. Při spuštění *konfigurovat úlohu ukotvení zdroje* v průvodci, Azure AD Connect přepne na použití *ms-DS-KonzistenceGuid jako zdrojový atribut pro immutableId. V rámci této změny Azure AD Connect pokusí aktualizovat pravidla deklarace pro ImmutableId ve službě AD FS. Tento krok se však nezdařil, protože Azure AD Connect neměl pověření správce potřebné ke konfiguraci služby AD FS. S touto opravou Azure AD Connect nyní vyzve k zadání přihlašovacích údajů správce pro službu AD FS při spuštění úlohy *Konfigurovat zdrojovou kotvu.*



## <a name="116140"></a>1.1.614.0
Stav: září 05 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Známé problémy
* Existuje známý problém, který způsobuje selhání upgradu služby Azure AD Connect s chybou *"Nelze inovovat službu synchronizace*". Dále synchronizační služba již nelze spustit s chybou události "*Služba nebyla schopna spustit, protože verze databáze je novější než verze nainstalovaných binárních souborů*". K problému dochází, když správce provádějící upgrade nemá oprávnění sysadmin k serveru SQL, který je používán Azure AD Connect. Oprávnění Dbo nejsou dostatečná.

* Existuje známý problém s upgradem Služby Azure AD Connect, který ovlivňuje zákazníky, kteří povolili [bezproblémové jednotné přihlašování](how-to-connect-sso.md). Po upgradu služby Azure AD Connect se tato funkce v průvodci zobrazí jako zakázaná, i když tato funkce zůstane povolená. Oprava tohoto problému bude poskytnuta v budoucí verzi. Zákazníci, kteří mají obavy z tohoto problému se zobrazením, jej mohou ručně opravit povolením bezproblémového jednotného přihlašování v průvodci.

#### <a name="fixed-issues"></a>Oprava potíží
* Byl opraven problém, který bránil službě Azure AD Connect v aktualizaci pravidel deklarací identity v místním systému Služby AD FS a zároveň povoloval funkci [ms-DS-ConsistencyGuid jako zdrojové kotvy.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) K problému dochází, pokud se pokusíte povolit funkci pro existující nasazení Azure AD Connect, který má AD FS nakonfigurované jako metoda přihlášení. K problému dochází, protože průvodce nezobrazí výzvu k zadání pověření služby ADFS před pokusem o aktualizaci pravidel deklarací identity ve službě AD FS.
* Opraven problém, který způsoboval selhání instalace služby Azure AD Connect, pokud je v místní doménové struktuře služby AD zakázáno. Problém je kvůli Průvodci Azure AD Connect neposkytuje plně kvalifikovaná pověření při vytváření kontextů zabezpečení požadovaných pro ověřování protokolem Kerberos. To způsobí, že ověřování protokolem Kerberos se nezdaří a průvodce Azure AD Connect se vrátí k používání ntlm.

### <a name="azure-ad-connect-sync"></a>Synchronizace služby Azure AD Connect
#### <a name="fixed-issues"></a>Oprava potíží
* Byl opraven problém, kdy nelze vytvořit nové pravidlo synchronizace, pokud není vyplněn atribut Tag.
* Byl opraven problém, který způsoboval, že se Azure AD Connect připojilk místní službě AD pro synchronizaci hesel pomocí ntlm, i když je protokol Kerberos k dispozici. K tomuto problému dochází, pokud místní topologie služby AD má jeden nebo více řadičů domény, které byly obnoveny ze zálohy.
* Byl opraven problém, který způsoboval, že po upgradu došlo zbytečně k úplným krokům synchronizace. Obecně platí, že spuštění úplné kroky synchronizace je nutné po upgradu, pokud dojde ke změnám pravidel synchronizace out-of-box. Problém byl způsoben chybou v logice zjišťování změn, která nesprávně zjistila změnu při výskytu výrazu pravidla synchronizace se znaky nového řádku. Znaky nového řádku jsou vloženy do výrazu pravidla synchronizace pro zlepšení čitelnosti.
* Byl opraven problém, který může způsobit, že server Azure AD Connect nebude po automatickém upgradu fungovat správně. Tento problém se týká serverů Azure AD Connect s verzí 1.1.443.0 (nebo starší). Podrobnosti o problému naleznete v článku [Azure AD Connect nefunguje správně po automatickém upgradu](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Byl opraven problém, který může způsobit opakování automatického upgradu každých 5 minut, když dojde k chybám. S opravou, automatické upgradu opakování s exponenciální back-off, když dojde k chybám.
* Byl opraven problém, kdy byla událost synchronizace hesla 611 nesprávně zobrazena v protokolech událostí aplikace systému Windows jako **informační** místo **chyby**. Událost 611 je generována vždy, když synchronizace hesel narazí na problém. 
* Opraven problém v průvodci Azure AD Connect, který umožňuje povolit funkci zpětného zápisu skupiny bez výběru ou položky požadované pro zpětné zápisdo skupiny.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* V části Další úkoly byl přidán úkol poradce při potížích s azure ad připojením. Zákazníci mohou tuto úlohu využít k řešení problémů souvisejících se synchronizací hesel a shromažďovat obecnou diagnostiku. V budoucnu bude úloha poradce při potížích rozšířena tak, aby zahrnovala další problémy související se synchronizací adresářů.
* Azure AD Connect teď podporuje nový režim instalace s názvem **Použít existující databázi**. Tento režim instalace umožňuje zákazníkům nainstalovat Azure AD Connect, který určuje existující databázi ADSync. Další informace o této funkci naleznete v článku [Použití existující databáze](how-to-connect-install-existing-database.md).
* Pro lepší zabezpečení Azure AD Connect teď výchozí použití TLS1.2 pro připojení k Synchronizaci adresářů Azure AD. Dříve byl výchozí hodnota TLS1.0.
* Při spuštění agenta synchronizace hesel Azure AD Connect se pokusí připojit k dobře známému koncovému bodu Azure AD pro synchronizaci hesel. Po úspěšném připojení je přesměrován na koncový bod specifický pro oblast. Dříve agent synchronizace hesel ukládá koncový bod specifický pro oblast do mezipaměti, dokud nebude restartován. Nyní agent vymaže mezipaměti a opakuje s dobře známý koncový bod, pokud narazí na problém připojení s koncovým bodem specifické pro oblast. Tato změna zajišťuje, že synchronizace hesel může převzetí služeb při selhání do jiné hodu specifické ho v oblasti, když koncový bod specifický pro oblast uložených v mezipaměti již není k dispozici.
* K synchronizaci změn z místní doménové struktury AD se vyžaduje účet služby AD DS. Můžete buď (i) vytvořit účet služby AD DS sami a poskytnout jeho přihlašovací údaje pro Azure AD Connect, nebo (ii) poskytnout přihlašovací údaje podnikového správce a nechat Azure AD Connect vytvořit účet služby AD DS za vás. Dříve (i) je výchozí možnost v průvodci Azure AD Connect. Nyní (ii) je výchozí možnost.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Přidána podpora pro Microsoft Azure Government Cloud a Microsoft Cloud Germany.

### <a name="ad-fs-management"></a>Správa ad FS
#### <a name="fixed-issues"></a>Oprava potíží
* Rutina Initialize-ADSyncNGCKeysWriteBack v modulu powershellu AD prep nesprávně používala alokace ACL s kontejnerem registrace zařízení a proto by zdědila pouze existující oprávnění.  Tato aktualizace byla aktualizována tak, aby účet synchronizační služby měl správná oprávnění.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Úloha AAD Connect Verify ADFS Login byla aktualizována tak, aby ověřovala přihlášení proti službě Microsoft Online a nikoli pouze načítání tokenů ze služby ADFS.
* Při nastavování nové farmy ADFS pomocí služby AAD Connect byla stránka s žádostí o pověření služby ADFS přesunuta tak, aby k ní nyní došlo dříve, než je uživatel požádán o poskytnutí serverů ADFS a WAP.  To umožňuje službě AAD Connect zkontrolovat, zda má zadaný účet správná oprávnění.
* Během upgradu služby AAD Connect již neselžeme v případě, že se neaktualizuje důvěryhodný vztah ADFS AAD.  Pokud k tomu dojde, uživateli se zobrazí příslušná varovná zpráva a měl by pokračovat v obnovení důvěryhodnosti prostřednictvím dalšího úkolu AAD Connect.

### <a name="seamless-single-sign-on"></a>Bezproblémové jednotné přihlašování
#### <a name="fixed-issues"></a>Oprava potíží
* Byl opraven problém, který způsoboval, že průvodce azure ad připojení vrátit chybu, pokud se pokusíte povolit [bezproblémové jednotné přihlašování](how-to-connect-sso.md). Chybová zpráva se zobrazí *"Konfigurace agenta ověřování služby Microsoft Azure AD Connect se nezdařila."* Tento problém se týká stávajících zákazníků, kteří ručně upgradovali verzi preview agenty ověřování pro [předávací ověřování](how-to-connect-sso.md) na základě kroků popsaných v tomto [článku](how-to-connect-pta-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
Stav: červenec 23 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Oprava potíží 

* Byl opraven problém, který způsoboval odebrání pravidla pro synchronizaci out-of-box "Out to AD – User ImmutableId":

  * K problému dochází při upgradu Azure AD Connect nebo při možnosti aktualizace *synchronizace v průvodci* Azure AD Connect se používá k aktualizaci konfigurace synchronizace Azure AD Connect.
  
  * Toto pravidlo synchronizace platí pro zákazníky, kteří povolili [funkci ms-DS-ConsistencyGuid jako zdrojová kotva](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). Tato funkce byla zavedena ve verzi 1.1.524.0 a po. Při odebrání pravidla synchronizace Azure AD Connect již nelze naplnit místní atribut AD ms-DS-KonzistenceGuid s hodnotou atributu ObjectGuid. Nebrání novým uživatelům zřídit do Azure AD.
  
  * Oprava zajišťuje, že pravidlo synchronizace již nebude odebráno během upgradu nebo během změny konfigurace, pokud je tato funkce povolena. Pro stávající zákazníky, kteří byli ovlivněni tímto problémem, oprava také zajišťuje, že pravidlo synchronizace je přidán zpět po upgradu na tuto verzi Azure AD Connect.

* Byl opraven problém, který způsobuje, že pravidla synchronizace za vybalení mají hodnotu priority menší než 100:

  * Obecně platí, že hodnoty priority 0 - 99 jsou vyhrazeny pro vlastní pravidla synchronizace. Během upgradu jsou aktualizovány hodnoty priority pro pravidla synchronizace předváděcí rámečku tak, aby vyhovovaly změnám pravidel synchronizace. Z důvodu tohoto problému out-of-box synchronizace pravidla mohou být přiřazeny prioritu hodnotu, která je menší než 100.
  
  * Oprava zabraňuje problému dochází během upgradu. Však neobnoví hodnoty priority pro stávající zákazníky, kteří byli ovlivněni problémem. V budoucnu bude poskytnuta samostatná oprava, která pomůže s obnovou.

* Opraven problém, kdy [obrazovka Filtrování domény a ou v](how-to-connect-install-custom.md#domain-and-ou-filtering) průvodci Připojení azure ad připojení zobrazuje *synchronizovat všechny domény a vou* možnost jako vybranou, i když je povoleno filtrování podle ou.

*   Byl opraven problém, který způsoboval, že [obrazovka Konfigurovat oddíly adresářů](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) ve Správci synchronizačních služeb vrátila chybu, pokud klepnete na tlačítko *Aktualizovat.* Chybová zpráva se zobrazí *:Při aktualizaci domén došlo k chybě: Nelze přetypovat objekt typu System.Collections.ArrayList, který zadejte soubor Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject.* K chybě dochází, když byla do existující doménové struktury služby AD přidána nová doménová struktura služby AD a pokoušíte se aktualizovat službu Azure AD Connect pomocí tlačítka Aktualizovat.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* [Funkce automatického upgradu](how-to-connect-install-automatic-upgrade.md) byla rozšířena tak, aby podporovala zákazníky s následujícími konfiguracemi:
  * Povolili jste funkci zpětného zápisu zařízení.
  * Povolili jste funkci zpětného zápisu skupiny.
  * Instalace není expresní nastavení nebo upgrade DirSync.
  * Máte více než 100 000 objektů v metaverse.
  * Připojujete se k více než jedné doménové struktuře. Expresní nastavení se připojuje pouze k jedné doménové struktuře.
  * Účet konektoru ad již není výchozím účtem MSOL_.
  * Server je nastaven na pracovní režim.
  * Povolili jste funkci zpětného zápisu uživatele.
  
  >[!NOTE]
  >Rozšíření oboru funkce automatického upgradu ovlivňuje zákazníky s Azure AD Connect sestavení 1.1.105.0 a po. Pokud nechcete, aby se server Azure AD Connect automaticky upgradoval, musíte na serveru Azure `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`AD Connect spustit následující rutinu: . Další informace o povolení nebo zakázání automatického upgradu naleznete v článku [Azure AD Connect: Automatický upgrade](how-to-connect-install-automatic-upgrade.md).

## <a name="115580"></a>1.1.558.0
Stav: Nebude uvolněna. Změny v tomto sestavení jsou zahrnuty ve verzi 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Oprava potíží 

* Byl opraven problém, který způsoboval odebrání pravidla synchronizace out-of-box "Out to AD – User ImmutableId" při aktualizaci konfigurace filtrování na základě ou. Toto pravidlo synchronizace je vyžadováno pro [funkci ms-DS-ConsistencyGuid as Source Anchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

* Opraven problém, kdy [obrazovka Filtrování domény a ou v](how-to-connect-install-custom.md#domain-and-ou-filtering) průvodci Připojení azure ad připojení zobrazuje *synchronizovat všechny domény a vou* možnost jako vybranou, i když je povoleno filtrování podle ou.

*   Byl opraven problém, který způsoboval, že [obrazovka Konfigurovat oddíly adresářů](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) ve Správci synchronizačních služeb vrátila chybu, pokud klepnete na tlačítko *Aktualizovat.* Chybová zpráva se zobrazí *:Při aktualizaci domén došlo k chybě: Nelze přetypovat objekt typu System.Collections.ArrayList, který zadejte soubor Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheetBase.MaPropertyPages.PartitionObject.* K chybě dochází, když byla do existující doménové struktury služby AD přidána nová doménová struktura služby AD a pokoušíte se aktualizovat službu Azure AD Connect pomocí tlačítka Aktualizovat.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* [Funkce automatického upgradu](how-to-connect-install-automatic-upgrade.md) byla rozšířena tak, aby podporovala zákazníky s následujícími konfiguracemi:
  * Povolili jste funkci zpětného zápisu zařízení.
  * Povolili jste funkci zpětného zápisu skupiny.
  * Instalace není expresní nastavení nebo upgrade DirSync.
  * Máte více než 100 000 objektů v metaverse.
  * Připojujete se k více než jedné doménové struktuře. Expresní nastavení se připojuje pouze k jedné doménové struktuře.
  * Účet konektoru ad již není výchozím účtem MSOL_.
  * Server je nastaven na pracovní režim.
  * Povolili jste funkci zpětného zápisu uživatele.
  
  >[!NOTE]
  >Rozšíření oboru funkce automatického upgradu ovlivňuje zákazníky s Azure AD Connect sestavení 1.1.105.0 a po. Pokud nechcete, aby se server Azure AD Connect automaticky upgradoval, musíte na serveru Azure `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`AD Connect spustit následující rutinu: . Další informace o povolení nebo zakázání automatického upgradu naleznete v článku [Azure AD Connect: Automatický upgrade](how-to-connect-install-automatic-upgrade.md).

## <a name="115570"></a>1.1.557.0
Stav: červenec 2017

>[!NOTE]
>Toto sestavení není k dispozici zákazníkům prostřednictvím funkce automatického upgradu služby Azure AD Connect.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Oprava potíží 
* Byl opraven problém s rutinou Initialize-ADSyncDomainJoinedComputerSync, který způsoboval změnu ověřené domény nakonfigurované na existujícím objektu spojovacího bodu služby, i když se stále jedná o platnou doménu. K tomuto problému dochází, když váš klient Azure AD má více než jednu ověřenou doménu, které lze použít ke konfiguraci bodu připojení služby.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Zpětný zápis hesla je teď k dispozici pro předběžnou verzi s Cloud Microsoft Azure Government a Microsoft Cloud Germany. Další informace o podpoře Azure AD Connect pro různé instance služby naleznete v článku [Azure AD Connect: Zvláštní aspekty pro instance](reference-connect-instances.md).

* Rutina Initialize-ADSyncDomainJoinedComputerSync má nyní nový volitelný parametr s názvem AzureADDomain. Tento parametr umožňuje určit, která ověřená doména se má použít pro konfiguraci spojovacího bodu služby.

### <a name="pass-through-authentication"></a>Předávací ověřování

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Název agenta požadovaného pro předávací ověřování byl změněn z *konektoru proxy aplikací Microsoft Azure AD* na *Agent ověřování microsoft azure ad připojení*.

* Povolení předávacího ověřování již ve výchozím nastavení neumožňuje synchronizaci hodnot hash hesel.


## <a name="115530"></a>1.1.553.0
Stav: červen 2017

> [!IMPORTANT]
> V tomto sestavení jsou zavedeny změny schématu a pravidel synchronizace. Služba synchronizace azure a připojení azure spustí úplné importu a úplné synchronizace kroky po upgradu. Podrobnosti o změnách jsou popsány níže. Chcete-li dočasně odložit úplné importu a úplné synchronizace kroky po upgradu, naleznete v článku [Jak odložit úplnou synchronizaci po upgradu](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>

### <a name="azure-ad-connect-sync"></a>Synchronizace služby Azure AD Connect

#### <a name="known-issue"></a>Známý problém
* Je problém, který se týká zákazníků, kteří používají [filtrování na základě ou s](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) azure ad connect synchronizace. Když přejdete na [stránku Filtrování domény a ou v](how-to-connect-install-custom.md#domain-and-ou-filtering) průvodci Azure AD Connect, očekává se následující chování:
  * Pokud je povoleno filtrování založené na ou, je **vybrána možnost Synchronizovat vybrané domény a ouslužby.**
  * V opačném případě je vybrána možnost **Synchronizovat všechny domény a mimo jiné.**

Problém, který vzniká, je, že **synchronizovat všechny domény a ruj možnost** je vždy vybrána při spuštění průvodce.  K tomu dochází i v případě, že filtrování založené na ou bylo dříve nakonfigurováno. Před uložením všech změn konfigurace služby AAD Connect zkontrolujte, zda **je vybrána možnost Synchronizovat vybrané domény a vou,** a zkontrolujte, zda jsou všechny vous, které je třeba synchronizovat, znovu povoleny. V opačném případě bude filtrování založené na ou zakázáno.

#### <a name="fixed-issues"></a>Oprava potíží

* Byl opraven problém s zpětným zápisem hesla, který umožňuje správci služby Azure AD resetovat heslo místního privilegovaného uživatelského účtu služby AD. K problému dochází, když Azure AD Connect je uděleno oprávnění obnovit heslo přes privilegovaný účet. Problém je vyřešen v této verzi Služby Azure AD Connect tím, že správce Azure AD neumožňuje resetovat heslo libovolného místního účtu privilegovaného uživatelského účtu služby AD, pokud správce není vlastníkem tohoto účtu. Další informace naleznete v [informačním zpravodaji zabezpečení 4033453](https://technet.microsoft.com/library/security/4033453).

* Opraven problém související s funkcí [ms-DS-ConsistencyGuid as Source Anchor,](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) kde Azure AD Connect neodepíše na místní atribut AD ms-DS-ConsistencyGuid. K problému dochází, když existuje více místních doménových struktur služby AD přidané do Služby Azure AD Connect a *identity uživatele existují ve více adresářích možnost* je vybrána. Při použití takové konfigurace, výsledná pravidla synchronizace nenaplňují sourceAnchorBinary atribut v Metaverse. Atribut sourceAnchorBinary se používá jako zdrojový atribut pro atribut ms-DS-ConsistencyGuid. V důsledku toho nedochází k zpětnému zápisu do atributu ms-DSConsistencyGuid. Chcete-li tento problém vyřešit, následující pravidla synchronizace byly aktualizovány, aby bylo zajištěno, že sourceAnchorBinary atribut v Metaverse je vždy naplněna:
  * V ze služby AD - Účet InetOrgPersonEnabled.xml
  * V od ad - InetOrgPerson Common.xml
  * V ad - user accountenabled.xml
  * V ad - Uživatel Common.xml
  * V ze ad - uživatel připojit SOAInAAD.xml

* Dříve i v případě, [že ms-DS-KonzistenceGuid jako zdroj kotva](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) funkce není povolena, "Out to AD – Uživatel ImmutableId" pravidlo synchronizace je stále přidán do Azure AD Connect. Efekt je neškodný a nezpůsobí zpětné hodu atributu ms-DS-ConsistencyGuid. Aby nedošlo k nejasnostem, byla přidána logika, která zajišťuje, že pravidlo synchronizace je přidáno pouze v případě, že je funkce povolena.

* Byl opraven problém, který způsoboval selhání synchronizace hodnot hash hesel s chybovou událostí 611. K tomuto problému dochází po jeden nebo více řadičů domény byly odebrány z místní služby AD. Na konci každého cyklu synchronizace hesel obsahuje synchronizační soubor cookie vydaný místním službou AD ID vyvolání odebraných řadičů domény s hodnotou USN (Update Sequence Number) 0. Správce synchronizace hesel nemůže zachovat synchronizační soubor cookie obsahující hodnotu USN 0 a selže s chybovou událostí 611. Během dalšího cyklu synchronizace Správce synchronizace hesel znovu použije poslední trvalý synchronizační soubor cookie, který neobsahuje hodnotu USN 0. To způsobí, že stejné změny hesla, které mají být znovu synchronizovány. Při této opravě správce synchronizace hesel zachová synchronizační soubor cookie správně.

* Dříve, i když byl automatický upgrade zakázán pomocí rutiny Set-ADSyncAutoUpgrade, proces automatického upgradu pokračuje v pravidelné kontrole upgradu a spoléhá na stažený instalační program, aby vyhověl zakázání. Pomocí této opravy proces automatického upgradu již pravidelně nekontroluje upgrade. Oprava se automaticky použije při upgradu instalátor pro tuto verzi Azure AD Connect se spustí jednou.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Dříve [ms-DS-ConsistencyGuid jako zdroj kotva](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) funkce byla k dispozici pouze pro nová nasazení. Nyní je k dispozici pro stávající nasazení. A konkrétně:
  * Chcete-li získat přístup k funkci, spusťte průvodce Azure AD Connect a zvolte možnost *Aktualizovat zdrojovou kotvu.*
  * Tato možnost je viditelná pouze pro existující nasazení, které používají atribut objectGuid jako sourceAnchor.
  * Při konfiguraci této možnosti průvodce ověří stav atributu ms-DS-ConsistencyGuid v místní službě Active Directory. Pokud atribut není nakonfigurován na žádný objekt uživatele v adresáři, průvodce používá ms-DS-ConsistencyGuid jako sourceAnchor atribut. Pokud je atribut nakonfigurován na jeden nebo více uživatelských objektů v adresáři, průvodce dospěje k závěru, že atribut je používán jinými aplikacemi a není vhodný jako atribut sourceAnchor a neumožňuje pokračování změny zdrojové kotvy. Pokud jste si jisti, že atribut není používán existujícími aplikacemi, musíte kontaktovat podporu a získat informace o tom, jak potlačit chybu.

* Specifický pro atribut **userCertificate** na objektech zařízení azure ad připojení teď hledá hodnoty certifikátů požadované pro [připojení zařízení spojených s doménou k prostředí Azure AD pro Windows 10](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) a filtruje zbytek před synchronizací do Azure AD. Chcete-li povolit toto chování, out-of-box pravidlo synchronizace "Out to AAD - Device Join SOAInAD" byla aktualizována.

* Azure AD Connect teď podporuje zpětný zápis atributu **CloudPublicDelegates** Exchange Online na místní atribut **AD publicDelegates.** To umožňuje scénář, kde poštovní schránky Exchange Online může být udělena SendOnBehalfTo práva pro uživatele s místní poštovní schránky Exchange. Pro podporu této funkce bylo přidáno nové pravidlo synchronizace out-of-box "Out to AD – User Exchange Hybrid PublicDelegates writeback". Toto pravidlo synchronizace se do Azure AD Connect přidá jenom v případě, že je povolena funkce Exchange Hybrid.

* Azure AD Connect teď podporuje synchronizaci **altRecipient** atribut z Azure AD. Pro podporu této změny byly aktualizovány následující pravidla synchronizace out-of-box tak, aby zahrnovaly požadovaný tok atributů:
  * V ad – výměna uživatelů
  * Ven na AAD - Uživatel ExchangeOnline
  
* Atribut **cloudSOAExchMailbox** v Metaverse označuje, zda daný uživatel má poštovní schránku Exchange Online nebo ne. Jeho definice byla aktualizována tak, aby zahrnovala další Exchange Online RecipientDisplayTypes jako takové zařízení a konferenční místnosti poštovní schránky. Chcete-li povolit tuto změnu, definice cloudSOAExchMailbox atribut, který se nachází v rámci pravidla synchronizace out-of-box "In z AAD – User Exchange Hybrid", byla aktualizována z:

```
CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
```

... na následující:

```
CBool(
  IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
    IIF([cloudMSExchRecipientDisplayType]=0,True,(
      IIF([cloudMSExchRecipientDisplayType]=2,True,(
        IIF([cloudMSExchRecipientDisplayType]=7,True,(
          IIF([cloudMSExchRecipientDisplayType]=8,True,(
            IIF([cloudMSExchRecipientDisplayType]=10,True,(
              IIF([cloudMSExchRecipientDisplayType]=16,True,(
                IIF([cloudMSExchRecipientDisplayType]=17,True,(
                  IIF([cloudMSExchRecipientDisplayType]=18,True,(
                    IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                       IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))

```

* Byla přidána následující sada funkcí kompatibilních s x509Certificate2 pro vytváření výrazů pravidel synchronizace pro zpracování hodnot certifikátů v atributu userCertificate:

    ||||
    | --- | --- | --- |
    |CertSubject|CertIssuer|Algoritmus CertKey|
    |CertSubjectNameDN|CertIssuerOid|Informace o názvu certifikátu|
    |CertSubjectNameOid|CertIssuerDN|IsCert|
    |Název certifikátu|CertThumbprint|CertExtensionOidy|
    |Formát certifikátu|CertNotAfter|CertPublicKeyOid|
    |Číslo CertSerial|CertNotPřed|CertPublicKeyParametersOid|
    |Verze certifikátu|CertifikátSignatureAlgorithmOid|Vyberte|
    |CertKeyAlgorithmParams|CertHashString|Kde|
    |||With|

* Následující změny schématu byly zavedeny umožnit zákazníkům vytvořit vlastní pravidla synchronizace toku sAMAccountName, domainNetBios a domainFQDN pro objekty skupiny, stejně jako rozlišovací název pro objekty uživatele:

  * Do schématu MV byly přidány následující atributy:
    * Skupina: AccountName
    * Skupina: domainNetBios
    * Skupina: domainFQDN
    * Osoba: distinguishedName

  * Do schématu programu Azure AD Connector byly přidány následující atributy:
    * Skupina: Název_účtu OnPremisesSamName
    * Skupina: NetBiosName
    * Skupina: DnsDomainName
    * Uživatel: OnPremisesDistinguishedName

* Skript rutiny ADSyncDomainJoinedComputerSync má nyní nový volitelný parametr s názvem AzureEnvironment. Parametr se používá k určení oblasti, ve které je hostovaný odpovídající klient služby Azure Active Directory. Mezi platné hodnoty patří:
  * AzureCloud (výchozí)
  * AzureChinaCloud
  * AzureGermanyCloud
  * UsGovernment
 
* Editor pravidel synchronizace, aby se používalo spojení (místo provision) jako výchozí hodnota typu propojení během vytváření pravidel synchronizace.

### <a name="ad-fs-management"></a>Správa ad FS

#### <a name="issues-fixed"></a>Opraveny problémy

* Následující adresy URL jsou nové koncové body WS-Federation zavedené službou Azure AD za účelem zlepšení odolnosti proti výpadku ověřování a budou přidány do místní konfigurace vztahu důvěryhodnosti strany, která odpovídá na službu AD FS:
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* Byl opraven problém, který způsoboval, že služba AD FS generovala nesprávnou hodnotu deklarace pro IssuerID. K problému dochází, pokud existuje více ověřených domén v tenantovi Azure AD a přípona domény userPrincipalName atribut slouží ke generování Deklarace IssuerID je alespoň 3 úrovně hluboké (například johndoe@us.contoso.com). Problém je vyřešen aktualizací regulárního výrazu používaného pravidly deklarace.

#### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení
* Dříve funkci Správy certifikátů ADFS poskytovanou službou Azure AD Connect lze použít jenom se s farmy ADFS spravovanými prostřednictvím služby Azure AD Connect. Teď můžete tuto funkci používat s farmy ADFS, které nejsou spravované pomocí služby Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Vydáno: květen 2017

> [!IMPORTANT]
> V tomto sestavení jsou zavedeny změny schématu a pravidel synchronizace. Služba synchronizace azure apřipojení spustí úplné importaa úplné synchronizace kroky po upgradu. Podrobnosti o změnách jsou popsány níže.
>
>

**Opraveny problémy:**

Synchronizace služby Azure AD Connect

* Byl opraven problém, který způsobuje, že na serveru Azure AD Connect dochází k automatickému upgradu, i když zákazník tuto funkci zakázal pomocí rutiny Set-ADSyncAutoUpgrade. S touto opravou proces automatického upgradu na serveru stále kontroluje upgrade pravidelně, ale stažený instalační program respektuje konfiguraci automatického upgradu.
* Během upgradu DirSync na místě Azure AD Connect vytvoří účet služby Azure AD, který bude používat konektor Azure AD pro synchronizaci s Azure AD. Po vytvoření účtu Azure AD Connect ověřuje pomocí Azure AD pomocí účtu. V některých případech se ověření nezdaří z důvodu přechodných problémů, což zase způsobí selhání upgradu dirsync na místě s chybou *"Došlo k chybě při provádění úlohy Konfigurace synchronizace AAD: AADSTS50034: Chcete-li se přihlásit do této aplikace, musí být účet přidán do xxx.onmicrosoft.com adresáře."* Chcete-li zlepšit odolnost proti chybám upgradu DirSync, Azure AD Connect nyní opakuje krok ověřování.
* Došlo k problému s sestavením 443, který způsobuje, že upgrade DirSync na místě bude úspěšný, ale nejsou vytvořeny profily spuštění požadované pro synchronizaci adresářů. Logika hojení je součástí tohoto sestavení Azure AD Connect. Když zákazník upgraduje na toto sestavení, Azure AD Connect zjistí chybějící profily spuštění a vytvoří je.
* Byl opraven problém, který způsobil, že proces synchronizace hesel nezačal s ID události 6900 a chyba *"Položka se stejným klíčem již byla přidána"*. K tomuto problému dochází, pokud aktualizujete konfiguraci filtrování ou zahrnout oddíl konfigurace služby AD. Chcete-li tento problém vyřešit, proces synchronizace hesel nyní synchronizuje změny hesla pouze z oddílů domény služby AD. Jsou přeskočeny oddíly, které nejsou doménové, jako je například konfigurační oddíl.
* Během expresní instalace Azure AD Connect vytvoří místní účet služby AD DS, který bude konektor služby AD používat ke komunikaci s místní službou AD. Dříve byl účet vytvořen pomocí příznaku PASSWD_NOTREQD nastaveného na atributu user-Account-Control a v účtu je nastaveno náhodné heslo. Azure AD Connect teď explicitně odebere příznak PASSWD_NOTREQD po nastavení hesla v účtu.
* Opraven problém, který způsobil selhání upgradu DirSync s *chybou "došlo k zablokování serveru SQL, který se pokoušel získat zámek aplikace",* když byl atribut mailNickname nalezen v místním schématu služby AD, ale není ohraničen třídou objektů AD User.
* Byl opraven problém, který způsobuje, že se funkce zpětného zápisu zařízení automaticky deaktivovala, když správce aktualizuje konfiguraci synchronizace služby Azure AD Connect pomocí průvodce Azure AD Connect. Tento problém je způsoben tím, že průvodce provedl předběžnou kontrolu existující konfigurace zpětného zápisu zařízení v místním službě AD a kontrola se nezdaří. Oprava je přeskočit kontrolu, pokud je již dříve povolen zpětný zápis zařízení.
* Chcete-li nakonfigurovat filtrování ou, můžete použít průvodce Azure AD Connect nebo Správce synchronizačních služeb. Dříve pokud používáte Průvodce připojením Azure AD ke konfiguraci filtrování ou, nové hlavní sady vytvořené později jsou zahrnuty pro synchronizaci adresářů. Pokud nechcete, aby byly zahrnuty nové provozní únosy, je nutné nakonfigurovat filtrování ou pomocí Správce služeb synchronizace. Teď můžete dosáhnout stejného chování pomocí Průvodce Azure AD Connect.
* Byl opraven problém, který způsobuje, že uložené procedury vyžadované službou Azure AD Connect se vytvořily pod schématem správce instalace, nikoli pod schématem dbo.
* Byl opraven problém, který způsobuje, že atribut TrackingId vrácený službou Azure AD byl v protokolech událostí serveru AAD Connect vynechán. K problému dochází, pokud Azure AD Connect obdrží zprávu o přesměrování z Azure AD a Azure AD Connect se nemůže připojit ke koncovému bodu k dispozici. TrackingId používá pracovníci technické podpory korelovat s protokoly na straně služby při řešení potíží.
* Když Azure AD Connect obdrží chybu LargeObject z Azure AD, Azure AD Connect generuje událost s EventID 6941 a zprávu *"Zřízený objekt je příliš velký. Ořízněte počet hodnot atributů u tohoto objektu."* Současně Azure AD Connect také generuje zavádějící událost s EventID 6900 a zpráva *"Microsoft.Online.Coexistence.ProvisionRetryException: Nelze komunikovat se službou Windows Azure Active Directory."* Chcete-li minimalizovat nejasnosti, Azure AD Connect již generuje druhou událost při přijetí chyby LargeObject.
* Byl opraven problém, který způsobovat, že správce synchronizačních služeb přestal reagovat při pokusu o aktualizaci konfigurace pro obecný konektor LDAP.

**Nové funkce/vylepšení:**

Synchronizace služby Azure AD Connect
* Změny pravidel synchronizace – byly implementovány následující změny pravidel synchronizace:
  * Byla aktualizována výchozí pravidlo synchronizace nastavená na neexportovat atributy **userCertificate** a **userSMIMECertificate,** pokud mají atributy více než 15 hodnot.
  * Atributy služby AD **employeeID** a **msExchBypassModeonLink** jsou nyní zahrnuty ve výchozí sadě pravidel synchronizace.
  * **Fotografie** atributu služby AD byla odebrána z výchozí sady pravidel synchronizace.
  * Přidáno **preferredDataLocation** do schématu Metaverse a aAD konektor schématu. Zákazníci, kteří chtějí aktualizovat buď atributy ve službě Azure AD, můžou implementovat vlastní pravidla synchronizace. 
  * Přidáno **userType** do schématu Metaverse a AAD connector schéma. Zákazníci, kteří chtějí aktualizovat buď atributy ve službě Azure AD, můžou implementovat vlastní pravidla synchronizace.

* Azure AD Connect teď automaticky umožňuje použití ConsistencyGuid atribut jako atribut Source Anchor pro místní objekty Služby AD. Dále Azure AD Connect naplní ConsistencyGuid atribut s hodnotou atributu objectGuid, pokud je prázdný. Tato funkce se vztahuje pouze na nové nasazení. Další informace o této funkci naleznete v části článku [Azure AD Connect: Koncepty návrhu – použití ms-DS-ConsistencyGuid jako sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).
* Byla přidána nová rutina pro řešení potíží Invoke-ADSyncDiagnostics, která pomáhá diagnostikovat problémy související se synchronizací hash hesel. Informace o použití rutiny naleznete v článku [Poradce při potížích se synchronizací hash hesel pomocí synchronizace Azure AD Connect](tshoot-connect-password-hash-synchronization.md).
* Azure AD Connect teď podporuje synchronizaci objektů veřejné složky s podporou pošty z místního služby AD do služby Azure AD. Tuto funkci můžete povolit pomocí průvodce Azure AD Connect v části Volitelné funkce. Další informace o této funkci najdete v článku [Podpora blokování okrajů na základě adresáře Office 365 pro místní veřejné složky s povoleným poštou](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders).
* Azure AD Connect vyžaduje účet služby AD DS k synchronizaci z místní služby AD. Dříve, pokud jste nainstalovali Azure AD Connect pomocí režimu Express, můžete zadat přihlašovací údaje účtu Enterprise Admin a Azure AD Connect by vytvořit účet Služby AD DS požadované. Pro vlastní instalaci a přidání doménových struktur do existujícího nasazení jste však museli místo toho zadat účet služby AD DS. Teď máte také možnost zadat přihlašovací údaje účtu enterprise admin během vlastní instalace a nechat Azure AD Connect vytvořit účet služby AD DS požadované.
* Azure AD Connect teď podporuje SQL AOA. Před instalací služby Azure AD Connect je nutné povolit SQL AOA. Během instalace Azure AD Connect zjistí, zda je poskytnutá instance SQL povolená pro SQL AOA nebo ne. Pokud je povolena SQL AOA, Azure AD Connect dále zjistí, pokud SQL AOA je nakonfigurovaný pro použití synchronní replikace nebo asynchronní replikace. Při nastavování naslouchací proces skupiny dostupnosti se doporučuje nastavit vlastnost RegisterAllProvidersIP na 0. Toto doporučení je, protože Azure AD Connect aktuálně používá nativní ho klienta SQL pro připojení k NAtivnímu klientovi SQL a SQL Nepodporuje použití vlastnosti MultiSubNetFailover.
* Pokud používáte LocalDB jako databázi pro server Azure AD Connect a dosáhla jeho 10 GB omezení velikosti, synchronizační služba se již nespustí. Dříve je nutné provést operaci ShrinkDatabase na LocalDB uvolnit dostatek místa DB pro spuštění služby synchronizace. Poté můžete pomocí Správce synchronizačních služeb odstranit historii spuštění a uvolnit tak více místa v db. Nyní můžete pomocí rutiny Start-ADSyncPurgeRunHistory vyčistit data historie z LocalDB a uvolnit prostor DB. Dále tato rutina podporuje režim offline (zadáním parametru -offline), který lze použít, když není spuštěna služba synchronizace. Poznámka: Režim offline lze použít pouze v případě, že synchronizační služba není spuštěna a použitá databáze je LocalDB.
* Chcete-li snížit množství úložného prostoru potřebného, Azure AD Connect nyní komprimuje podrobnosti o chybě synchronizace před jejich uložením v databázích LocalDB/SQL. Při upgradu ze starší verze Azure AD Connect na tuto verzi Azure AD Connect provádí jednorázovou kompresi na podrobnosti o existující chybě synchronizace.
* Dříve po aktualizaci konfigurace filtrování ou je nutné ručně spustit úplný import, abyste zajistili, že existující objekty budou správně zahrnuty nebo vyloučeny ze synchronizace adresářů. Azure AD Connect teď automaticky aktivuje úplný import během dalšího cyklu synchronizace. Úplný import se dále použije pouze na konektory služby AD, kterých se aktualizace týká. Poznámka: Toto vylepšení se vztahuje na aktualizace filtrování ou provedených pouze pomocí průvodce Azure AD Connect. Nevztahuje se na aktualizaci filtrování ou provedených pomocí Správce synchronizačních služeb.
* Dříve filtrování založené na skupině podporovalo pouze objekty Uživatelé, Skupiny a Kontakt. Filtrování založené na skupině nyní také podporuje objekty Počítače.
* Dříve můžete odstranit data prostoru konektoru bez zakázání plánovače synchronizace Azure AD Connect. Nyní Správce synchronizačních služeb blokuje odstranění dat prostoru konektoru, pokud zjistí, že plánovač je povolen. Dále je vráceno upozornění, které informuje zákazníky o potenciální ztrátě dat, pokud jsou odstraněna data prostoru konektoru.
* Dříve je nutné zakázat přepis prostředí PowerShell pro Azure AD Connect průvodce správně spustit. Tento problém je částečně vyřešen. Přepis prostředí PowerShell můžete povolit, pokud ke správě konfigurace synchronizace používáte průvodce Azure AD Connect. Pokud ke správě konfigurace služby ADFS používáte průvodce Azure AD Connect, musíte zakázat přepis prostředí PowerShell.



## <a name="114860"></a>1.1.486.0
Vydáno: duben 2017

**Opraveny problémy:**
* Byl opraven problém, kdy se služba Azure AD Connect nenainstalovala úspěšně v lokalizované verzi Windows Serveru.

## <a name="114840"></a>1.1.484.0
Vydáno: duben 2017

**Známé problémy:**

* Tato verze Služby Azure AD Connect se nenainstaluje úspěšně, pokud jsou splněny všechny následující podmínky:
   1. Provádíte buď DirSync inovován na místě nebo čerstvé instalace Azure AD Connect.
   2. Používáte lokalizovanou verzi systému Windows Server, kde název předdefinované skupiny Administrator na serveru není "Administrators".
   3. Používáte výchozí SQL Server 2012 Express LocalDB nainstalovaný s Azure AD Connect namísto poskytování vlastní úplné SQL.

**Opraveny problémy:**

Synchronizace služby Azure AD Connect
* Byl opraven problém, kdy plánovač synchronizace přeskočí celý krok synchronizace, pokud jeden nebo více konektorů chybí profil spuštění pro tento krok synchronizace. Například ručně jste přidali konektor pomocí Správce synchronizačních služeb bez vytvoření profilu spuštění rozdílového importu. Tato oprava zajišťuje, že plánovač synchronizace nadále spouštět delta import pro ostatní konektory.
* Opraven problém, kdy služba synchronizace okamžitě zastavila zpracování profilu spuštění, když došlo k problému s jedním z kroků spuštění. Tato oprava zajišťuje, že služba synchronizace přeskočí tento krok spuštění a pokračuje ve zpracování zbytku. Například máte profil spuštění rozdílového importu pro konektor služby AD s více kroky spuštění (jeden pro každou místní doménu služby AD). Služba synchronizace spustí rozdílový import s ostatními doménami služby AD, i když má jedna z nich problémy s připojením k síti.
* Byl opraven problém, který způsobil, že aktualizace konektoru Azure AD byla během automatického upgradu přeskočena.
* Opraven problém, který způsobuje, že Azure AD Connect nesprávně určuje, zda je server řadičem domény během instalace, což zase způsobí selhání upgradu DirSync.
* Byl opraven problém, který způsobuje, že upgrade DirSync na místě nevytváří žádný profil spuštění pro konektor Azure AD.
* Byl opraven problém, kdy uživatelské rozhraní Správce synchronizačních služeb přestalo reagovat při pokusu o konfiguraci obecného konektoru LDAP.

Správa ad FS
* Opraven problém, kdy se průvodce připojením Azure AD nezdaří, pokud byl primární uzel služby AD FS přesunut na jiný server.

Přiosít k ploše
* Opraven problém v průvodci Azure AD Connect, kde přihlašovací obrazovka neumožňuje povolit funkci přihlašování k ploše, pokud jste během nové instalace zvolili synchronizaci hesel jako možnost přihlášení.

**Nové funkce/vylepšení:**

Synchronizace služby Azure AD Connect
* Azure AD Connect Sync teď podporuje použití účtu virtuální služby, účtu spravované služby a účtu spravované služby skupiny jako svého účtu služby. To platí pouze pro novou instalaci azure ad connect. Při instalaci Služby Azure AD Connect:
    * Ve výchozím nastavení průvodce Azure AD Connect vytvoří účet virtuální služby a použije ho jako svůj účet služby.
    * Pokud instalujete na řadič domény, Azure AD Connect přejde zpět k předchozímu chování, kde vytvoří uživatelský účet domény a místo toho ho použije jako účet služby.
    * Výchozí chování můžete přepsat zadáním jedné z následujících možností:
      * Účet služby spravované služby skupiny
      * Účet spravované služby
      * Uživatelský účet domény
      * Místní uživatelský účet
* Dříve pokud upgradujete na nové sestavení Azure AD Connect obsahující konektory aktualizace nebo změny pravidel synchronizace, Azure AD Connect spustí úplný cyklus synchronizace. Azure AD Connect teď selektivně aktivuje úplný krok importu jenom pro konektory s aktualizací a krok úplné synchronizace jenom pro konektory se změnami pravidel synchronizace.
* Dříve prahová hodnota odstranění exportu se vztahuje pouze na exporty, které jsou spuštěny prostřednictvím plánovače synchronizace. Nyní je funkce rozšířena tak, aby zahrnovala exporty ručně aktivované zákazníkem pomocí Správce synchronizačních služeb.
* V tenantovi Azure AD je konfigurace služby, která označuje, zda je pro vašeho klienta povolena funkce synchronizace hesel nebo ne. Dříve je snadné pro konfiguraci služby nesprávně nakonfigurovat Azure AD Connect, když máte aktivní a pracovní server. Azure AD Connect se teď pokusí zachovat konfiguraci služby v souladu s vaším aktivním serverem Azure AD Connect.
* Průvodce azure ad připojení teď zjistí a vrátí upozornění, pokud místní služba AD nemá povolený koš služby AD.
* Dříve exportovat do Služby Azure AD časový limit a selže, pokud kombinovaná velikost objektů v dávce překročí určitou prahovou hodnotu. Nyní synchronizační služba se pokusí znovu odeslat objekty v samostatných, menších dávkách, pokud dojde k problému.
* Aplikace Správa klíčů služby Synchronizace byla odebrána z nabídky Start systému Windows. Správa šifrovacího klíče bude i nadále podporována prostřednictvím rozhraní příkazového řádku pomocí nástroje miiskmu.exe. Informace o správě šifrovacího klíče najdete v článku [Opuštění šifrovacího klíče Azure AD Connect Sync](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-adsync-service-account-encryption-key).
* Dříve pokud změníte heslo účtu synchronizační služby Azure AD Connect, synchronizační služba nebude možné spustit správně, dokud jste opustili šifrovací klíč a znovu inicializovat heslo účtu synchronizační služby Azure AD Connect. Nyní tento proces již není nutný.

Přiosít k ploše

* Průvodce azure ad připojení již vyžaduje port 9090 otevřít v síti při konfiguraci předávací ověřování a desktop ové přiřazování. Je vyžadován pouze port 443. 

## <a name="114430"></a>1.1.443.0
Vydáno: březen 2017

**Opraveny problémy:**

Synchronizace služby Azure AD Connect
* Opraven problém, který způsobil selhání průvodce azure ad připojením, pokud zobrazovaný název konektoru Azure AD neobsahuje počáteční onmicrosoft.com domény přiřazené k tenantovi Azure AD.
* Opraven problém, který způsoboval selhání průvodce Azure AD Connect při navazování připojení k databázi SQL, když heslo účtu služby Synchronizace obsahuje speciální znaky, jako je apostrof, dvojtečka a prostor.
* Opraven problém, který způsoboval, že chyba "Bitová kopie má ukotvení, které se liší od image", které se vyskytují na serveru Azure AD Connect v pracovním režimu poté, co jste dočasně vyloučili místní objekt Služby AD ze synchronizace a pak jej znovu zahrnuli pro synchronizaci.
* Opraven problém, který způsoboval, že se chyba "Objekt umístěný podle DN je fiktivní" na serveru Azure AD Connect v pracovním režimu poté, co jste dočasně vyloučili místní objekt Služby AD ze synchronizace a pak jej znovu zahrnuli pro synchronizaci.

Správa ad FS
* Opraven problém, kdy průvodce Azure AD Connect neaktualizoval konfiguraci služby AD FS a po konfiguraci alternativního ID přihlášení nastavil správné deklarace identity na důvěryhodný certifikát předávající strany.
* Byl opraven problém, kdy průvodce azure ad connect není schopen správně zpracovat servery služby AD FS, jejichž účty služeb jsou konfigurovány pomocí formátu userPrincipalName namísto formátu sAMAccountName.

Předávací ověřování
* Opraven problém, který způsoboval selhání průvodce Azure AD Connect, pokud je vybraná možnost Předat ověřování, ale registrace jeho konektoru se nezdaří.
* Opraven problém, který způsoboval, že průvodce Azure AD Connect obcoval kontroly ověření při metodě přihlášení vybrané při povolení funkce přihlašování k ploše.

Resetování hesla
* Byl opraven problém, který může způsobit, že se server Azure AAD Connect nepokouší o opětovné připojení, pokud bylo připojení spuštěno bránou firewall nebo proxy serverem.

**Nové funkce/vylepšení:**

Synchronizace služby Azure AD Connect
* Rutina Get-ADSyncScheduler nyní vrací novou logickou vlastnost s názvem SyncCycleInProgress. Pokud vrácená hodnota je true, znamená to, že probíhá naplánovaný cyklus synchronizace.
* Cílová složka pro ukládání protokolů instalace a instalace služby Azure AD Connect byla přesunuta z %localappdata%\AADConnect na %programdata%\AADConnect za účelem zlepšení přístupu k souborům protokolu.

Správa ad FS
* Přidána podpora pro aktualizaci certifikátu TLS/SSL farmy služby AD FS.
* Přidána podpora pro správu služby AD FS 2016.
* Nyní můžete zadat existující gMSA (účet spravované služby skupiny) během instalace služby AD FS.
* Nyní můžete nakonfigurovat SHA-256 jako algoritmus hash podpisu pro azure ad předávající strany důvěryhodnosti.

Resetování hesla
* Byla zavedena vylepšení, která umožňují, aby produkt fungoval v prostředích s přísnějšími pravidly brány firewall.
* Vylepšená spolehlivost připojení k Azure Service Bus.

## <a name="113800"></a>1.1.380.0
Vydáno: prosinec 2016

**Opraven problém:**

* Opraven problém, kdy v tomto sestavení chybělo pravidlo deklarace problémpro službu AD FS (Issuerid.

>[!NOTE]
>Toto sestavení není k dispozici zákazníkům prostřednictvím funkce automatického upgradu služby Azure AD Connect.

## <a name="113710"></a>1.1.371.0
Vydáno: prosinec 2016

**Známý problém:**

* V tomto sestavení chybí pravidlo deklarace vystavující deklarace pro ad FS. Pravidlo deklarace problému je vyžadováno, pokud federating více domén s Azure Active Directory (Azure AD). Pokud používáte Azure AD Connect ke správě místního nasazení služby AD FS, upgrade na toto sestavení odebere stávající pravidlo deklarace výdeje z konfigurace služby AD FS. Problém můžete vyřešit přidáním pravidla deklarace problému po instalaci nebo upgradu. Podrobnosti o přidání pravidla deklarace problémové položky naleznete v tomto článku o [podpoře více domén pro federace s Azure AD](how-to-connect-install-multiple-domains.md).

**Opraven problém:**

* Pokud port 9090 není otevřen pro odchozí připojení, instalace nebo upgrade Azure AD Connect se nezdaří.

>[!NOTE]
>Toto sestavení není k dispozici zákazníkům prostřednictvím funkce automatického upgradu služby Azure AD Connect.

## <a name="113700"></a>1.1.370.0
Vydáno: prosinec 2016

**Známé problémy:**

* V tomto sestavení chybí pravidlo deklarace vystavující deklarace pro ad FS. Pravidlo deklarace problému je vyžadováno, pokud federating více domén s Azure AD. Pokud používáte Azure AD Connect ke správě místního nasazení služby AD FS, upgrade na toto sestavení odebere stávající pravidlo deklarace výdeje z konfigurace služby AD FS. Problém můžete vyřešit přidáním pravidla deklarace problému po instalaci nebo upgradu. Podrobnosti o přidání pravidla deklarace problémové položky naleznete v tomto článku [o podpoře více domén pro federace s Azure AD](how-to-connect-install-multiple-domains.md).
* Port 9090 musí být otevřen odchozí k dokončení instalace.

**Nové funkce:**

* Předávací ověřování (náhled).

>[!NOTE]
>Toto sestavení není k dispozici zákazníkům prostřednictvím funkce automatického upgradu služby Azure AD Connect.

## <a name="113430"></a>1.1.343.0
Vydáno: listopad 2016

**Známý problém:**

* V tomto sestavení chybí pravidlo deklarace vystavující deklarace pro ad FS. Pravidlo deklarace problému je vyžadováno, pokud federating více domén s Azure AD. Pokud používáte Azure AD Connect ke správě místního nasazení služby AD FS, upgrade na toto sestavení odebere stávající pravidlo deklarace výdeje z konfigurace služby AD FS. Problém můžete vyřešit přidáním pravidla deklarace problému po instalaci nebo upgradu. Podrobnosti o přidání pravidla deklarace problémové položky naleznete v tomto článku [o podpoře více domén pro federace s Azure AD](how-to-connect-install-multiple-domains.md).

**Opraveny problémy:**

* V některých případě se instalace služby Azure AD Connect nezdaří, protože nemůže vytvořit účet místní služby, jehož heslo splňuje úroveň složitosti určenou zásadami hesla organizace.
* Opraven problém, kdy pravidla spojení nejsou znovu vyhodnocena, když se objekt v prostoru spojnice současně stane neoborovým pro jedno pravidlo spojení a stane se neoborovým pro jiné. K tomu může dojít, pokud máte dvě nebo více pravidel spojení, jejichž podmínky spojení se vzájemně vylučují.
* Opraven problém, kdy pravidla příchozí synchronizace (z Azure AD), které neobsahují pravidla spojení, nejsou zpracovány, pokud mají nižší hodnoty priority než ty, které obsahují pravidla spojení.

**Vylepšení:**

* Přidána podpora pro instalaci Azure AD Connect na Windows Server 2016 Standard nebo vyšší.
* Přidána podpora pro použití SQL Serveru 2016 jako vzdálené databáze pro Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Vydáno: srpen 2016

**Opraveny problémy:**

* Změny intervalu synchronizace se neuskuteční až po dokončení dalšího cyklu synchronizace.
* Průvodce Azure AD Connect nepřijímá účet Azure AD, jehož uživatelské jméno začíná podtržítkem (\_).
* Průvodce azure ad připojení se nezdaří ověření účtu Azure AD, pokud heslo účtu obsahuje příliš mnoho speciálních znaků. Chybová zpráva "Nelze ověřit pověření. Došlo k neočekávané chybě." je vrácena.
* Odinstalace pracovního serveru zakáže synchronizaci hesel v tenantovi Azure AD a způsobí selhání synchronizace hesel s aktivním serverem.
* Synchronizace hesel se nezdaří v neobvyklých případech, kdy není uložena žádná hash hesla v uživateli.
* Pokud je server Azure AD Connect povolen pro pracovní režim, zpětný zápis hesla není dočasně zakázán.
* Průvodce azure ad připojení nezobrazuje skutečnou synchronizaci hesel a konfiguraci zpětného zápisu hesla, když je server v pracovním režimu. Vždycky je to ukazuje jako zakázané.
* Změny konfigurace synchronizace hesel a zpětný zápis hesla nejsou trvalé průvodcem Azure AD Connect, když je server v pracovním režimu.

**Vylepšení:**

* Byla aktualizována rutina Start-ADSyncSyncCycle, která označuje, zda je schopna úspěšně spustit nový cyklus synchronizace či nikoli.
* Přidána rutina Stop-ADSyncSyncCycle pro ukončení cyklu synchronizace a operace, které právě probíhají.
* Byla aktualizována rutina Stop-ADSyncScheduler, aby byl ukončen cyklus synchronizace a operace, které právě probíhají.
* Při konfiguraci [rozšíření adresáře](how-to-connect-sync-feature-directory-extensions.md) v průvodci Azure AD Connect, Atribut Azure AD typu "Řetězec Teletex" lze nyní vybrat.

## <a name="111890"></a>1.1.189.0
Vydáno: červen 2016

**Opraveny problémy a vylepšení:**

* Azure AD Connect teď můžou být nainstalované na serveru kompatibilním s FIPS.
  * Synchronizace hesel naleznete [v tématu Synchronizace hash hesel a FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips).
* Byl opraven problém, kdy nebylo možné přeložit název rozhraní NetBIOS na název v yKD v konektoru služby Active Directory.

## <a name="111800"></a>1.1.180.0
Vydáno: květen 2016

**Nové funkce:**

* Varuje a pomůže vám ověřit domény, pokud jste to neudělali před spuštěním Azure AD Connect.
* Přidána podpora pro [Microsoft Cloud Germany](reference-connect-instances.md#microsoft-cloud-germany).
* Přidána podpora pro nejnovější [cloudovou](reference-connect-instances.md#microsoft-azure-government) infrastrukturu Microsoft Azure Government s novými požadavky na adresy URL.

**Opraveny problémy a vylepšení:**

* Do Editoru pravidel synchronizace bylo přidáno filtrování, aby bylo snadné najít pravidla synchronizace.
* Zlepšený výkon při mazání prostoru konektoru.
* Byl opraven problém, kdy byl stejný objekt odstraněn a přidán ve stejném spuštění (nazývaném odstranění/přidání).
* Zakázané pravidlo synchronizace již znovu nepovolí zahrnuté objekty a atributy při upgradu nebo aktualizaci schématu adresáře.

## <a name="111300"></a>1.1.130.0
Vydáno: duben 2016

**Nové funkce:**

* Byla přidána podpora vícehodnotových atributů [rozšíření adresáře](how-to-connect-sync-feature-directory-extensions.md).
* Přidána podpora pro další varianty konfigurace pro [automatický upgrade,](how-to-connect-install-automatic-upgrade.md) které mají být považovány za způsobilé pro upgrade.
* Přidány některé rutiny pro [vlastní plánovač](how-to-connect-sync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Vydáno: březen 2016

**Opraveny problémy:**

* Ujistěte se, že expresní instalaci nelze použít v systému Windows Server 2008 (pre-R2), protože synchronizace hesel není v tomto operačním systému podporována.
* Upgrade z DirSync s vlastní konfigurací filtru nefungoval podle očekávání.
* Při upgradu na novější verzi a neexistují žádné změny v konfiguraci, úplný import nebo synchronizace by neměla být naplánována.

## <a name="111100"></a>1.1.110.0
Vydáno: únor 2016

**Opraveny problémy:**

* Upgrade z dřívějších verzí nefunguje, pokud instalace není ve výchozí složce C:\Program Files.
* Pokud nainstalujete a **zrušte zaškrtnutí políčka Spustit proces synchronizace** na konci průvodce instalací, spuštění průvodce instalací podruhé plánovače nepovolí.
* Plánovač nefunguje podle očekávání na serverech, kde se nepoužívá formát data a času us-en. Bude také `Get-ADSyncScheduler` blokovat vrátit správné časy.
* Pokud jste nainstalovali dřívější verzi Azure AD Connect s AD FS jako možnost přihlášení a upgrade, nelze spustit průvodce instalací znovu.

## <a name="111050"></a>1.1.105.0
Vydáno: únor 2016

**Nové funkce:**

* [Funkce automatického upgradu](how-to-connect-install-automatic-upgrade.md) pro zákazníky nastavení Express.
* Podpora globálního správce pomocí azure multi-factor authentication a privileged identity management v průvodci instalací.
  * Je třeba povolit proxy také https://secure.aadcdn.microsoftonline-p.com povolit provoz, pokud používáte vícefaktorové ověřování.
  * Chcete-li, https://secure.aadcdn.microsoftonline-p.com aby vícefaktorové ověřování fungovalo správně, je třeba přidat do seznamu důvěryhodných serverů.
* Povolit změnu metody přihlášení uživatele po počáteční instalaci.
* Povolit [filtrování domény a ou v](how-to-connect-install-custom.md#domain-and-ou-filtering) průvodci instalací. To také umožňuje připojení k doménovým strukturám, kde nejsou k dispozici všechny domény.
* [Plánovač](how-to-connect-sync-feature-scheduler.md) je integrován do synchronizačního modulu.

**Funkce povýšen z náhledu na GA:**

* [Zpětný zápis zařízení](how-to-connect-device-writeback.md).
* [Rozšíření adresářů](how-to-connect-sync-feature-directory-extensions.md).

**Nové funkce náhledu:**

* Nový výchozí interval cyklu synchronizace je 30 minut. Bývaly to tři hodiny pro všechny předchozí verze. Přidá podporu pro změnu chování [plánovače.](how-to-connect-sync-feature-scheduler.md)

**Opraveny problémy:**

* Stránka Ověřit domény DNS ne vždy rozpoznala domény.
* Při konfiguraci služby AD FS zobrazí výzvy k zadání pověření správce domény.
* Místní účty služby AD nejsou průvodcem instalací rozpoznány, pokud se nacházejí v doméně s jiným stromem DNS než kořenová doména.

## <a name="1091310"></a>1.0.9131.0
Vydáno: prosinec 2015

**Opraveny problémy:**

* Při změně hesla ve službě AD DS nemusí synchronizace hesel fungovat, ale funguje při nastavení hesla.
* Pokud máte proxy server, ověřování na Azure AD může selhat během instalace nebo pokud upgrade je zrušena na stránce konfigurace.
* Aktualizace z předchozí verze Azure AD Connect s úplnou instancí SQL Serveru se nezdaří, pokud nejste správce systému SQL Server (SA).
* Aktualizace z předchozí verze Azure AD Connect se vzdáleným SQL Serverem zobrazuje chybu "Nelze získat přístup k databázi ADSync SQL".

## <a name="1091250"></a>1.0.9125.0
Vydáno: listopad 2015

**Nové funkce:**

* Můžete překonfigurovat službu AD FS na vztah důvěryhodnosti Azure AD.
* Může aktualizovat schéma služby Active Directory a znovu vygenerovat pravidla synchronizace.
* Může zakázat pravidlo synchronizace.
* Můžete definovat "AutoritativníNull" jako nový literál v pravidle synchronizace.

**Nové funkce náhledu:**

* [Azure AD Connect Health pro synchronizaci](how-to-connect-health-sync.md).
* Podpora synchronizace hesel [služby Azure AD Domain Services.](../user-help/active-directory-passwords-update-your-own-password.md)

**Nový podporovaný scénář:**

* Podporuje více místních organizací Exchange. Další informace naleznete [v tématu Hybridní nasazení s více doménovými strukturami služby Active Directory](https://docs.microsoft.com/previous-versions/exchange-server/exchange-150/jj873754(v=exchg.150)).

**Opraveny problémy:**

* Problémy se synchronizací hesel:
  * Objekt přesunutý z out-of-scope do in-scope nebude mít jeho heslo synchronizováno. To zahrnuje filtrování ou a atributů.
  * Výběr nové hlavní výužky, která má být zahrnuta do synchronizace, nevyžaduje úplnou synchronizaci hesel.
  * Pokud je zakázáno uživatelem heslo nesynchronizuje.
  * Fronta opakování hesla je nekonečná a předchozí limit 5 000 objektů, které mají být vyřazeny, byl odebrán.
* Nelze se připojit ke službě Active Directory pomocí úrovně funkčnosti doménové struktury systému Windows Server 2016.
* Nelze změnit skupinu, která se používá pro filtrování skupin po počáteční instalaci.
* Už nevytvoří nový profil uživatele na serveru Azure AD Connect pro každého uživatele, který provádí změnu hesla s povoleným zpětným zápisem hesla.
* Nelze použít dlouhé integer hodnoty v oborech pravidel synchronizace.
* Zaškrtávací políčko "Zpětný zápis zařízení" zůstává zakázáno, pokud existují nedostupné řadiče domény.

## <a name="1086670"></a>1.0.8667.0
Vydáno: srpen 2015

**Nové funkce:**

* Průvodce instalací služby Azure AD Connect je teď lokalizovaný do všech jazyků Windows Serveru.
* Přidána podpora pro odemknutí účtu při použití správy hesel Azure AD.

**Opraveny problémy:**

* Průvodce instalací služby Azure AD Connect se zhroutí, pokud jiný uživatel pokračuje v instalaci, nikoli osoba, která instalaci spustila jako první.
* Pokud předchozí odinstalace Azure AD Connect se nezdaří odinstalovat Azure AD Connect synchronizace čistě, není možné přeinstalovat.
* Azure AD Connect nelze nainstalovat pomocí expresní instalace, pokud uživatel není v kořenové doméně doménové struktury nebo pokud se používá neanglická verze služby Active Directory.
* Pokud nelze přeložit vícenežvin y uživatelského účtu služby Active Directory, zobrazí se zavádějící chybová zpráva "Nepodařilo se potvrdit schéma".
* Pokud se účet použitý v konektoru služby Active Directory mimo průvodce změní, průvodce při následných spuštěních selže.
* Azure AD Connect se někdy nepodaří nainstalovat na řadič domény.
* Nelze povolit a zakázat "Pracovní režim", pokud byly přidány atributy rozšíření.
* Zpětný zápis hesla se v některých konfiguracích nezdaří z důvodu chybného hesla v konektoru služby Active Directory.
* DirSync nelze inovovat, pokud se při filtrování atributů používá rozlišující název (DN).
* Nadměrné využití procesoru při použití resetování hesla.

**Odstraněné funkce náhledu:**

* Funkce [náhledu Zpětný zápis uživatele](how-to-connect-preview.md#user-writeback) byl dočasně odebrán na základě zpětné vazby od našich zákazníků ve verzi Preview. Bude přidána později poté, co jsme se zabývali poskytnutou zpětnou vazbu.

## <a name="1086410"></a>1.0.8641.0
Vydáno: červen 2015

**Počáteční verze Služby Azure AD Connect.**

Změněný název z Azure AD Sync na Azure AD Connect.

**Nové funkce:**

* [Expresní instalace nastavení](how-to-connect-install-express.md)
* Lze [nakonfigurovat AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* Lze [upgradovat z DirSync](how-to-dirsync-upgrade-get-started.md)
* [Prevence náhodného odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* Zavedenpracovní [režim](how-to-connect-sync-staging-server.md)

**Nové funkce náhledu:**

* [Zpětný zápis uživatele](how-to-connect-preview.md#user-writeback)
* [Zpětný zápis skupin](how-to-connect-preview.md#group-writeback)
* [Zpětný zápis zařízení](how-to-connect-device-writeback.md)
* [Rozšíření adresáře](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
Vydáno: květen 2015

**Nový požadavek:**

* Azure AD Sync teď vyžaduje instalaci rozhraní .NET Framework verze 4.5.1.

**Opraveny problémy:**

* Zpětný zápis hesla z Azure AD se lhaní s chybou připojení Azure Service Bus.

## <a name="104910413"></a>1.0.491.0413
Vydáno: duben 2015

**Opraveny problémy a vylepšení:**

* Konektor služby Active Directory nezpracovává odstranění správně, pokud je koš povolen a v doménové struktuře je více domén.
* Výkon operací importu byl vylepšen pro konektor Azure Active Directory Connector.
* Pokud skupina překročila limit členství (ve výchozím nastavení je limit nastaven na 50 000 objektů), skupina byla odstraněna ve službě Azure Active Directory. S novým chováním není skupina odstraněna, je vyvolána chyba a nové změny členství nejsou exportovány.
* Nový objekt nelze zřídit, pokud fázované odstranění se stejným DN je již k dispozici v prostoru spojnice.
* Některé objekty jsou označeny pro synchronizaci během synchronizace delta i v případě, že neexistuje žádná změna připravené na objektu.
* Vynucení synchronizace hesla také odebere upřednostňovaný seznam řadičů domény.
* CSExportAnalyzer má problémy s některými objekty stavy.

**Nové funkce:**

* Spojení se nyní může připojit k typu objektu "ANY" v MV.

## <a name="104850222"></a>1.0.485.0222
Vydáno: únor 2015

**Vylepšení:**

* Zlepšený výkon importu.

**Opraveny problémy:**

* Synchronizace hesel respektuje atribut cloudFiltered, který se používá při filtrování atributů. Filtrované objekty již nejsou v oboru pro synchronizaci hesel.
* Ve výjimečných situacích, kdy topologie měla mnoho řadičů domény, synchronizace hesel nefunguje.
* "Zastaveno server" při importu z konektoru Azure AD po povolení správy zařízení v Azure AD/Intune.
* Připojení k objektům FSP (Foreign Security Principals) z více domén ve stejné doménové struktuře způsobí chybu nejednoznačného spojení.

## <a name="104751202"></a>1.0.475.1202
Vydáno: prosinec 2014

**Nové funkce:**

* Synchronizace hesel s filtrováním založeným na atributech je nyní podporována. Další informace naleznete v [tématu Synchronizace hesel s filtrováním](how-to-connect-sync-configure-filtering.md).
* Atribut ms-DS-ExternalDirectoryObjectID je zapsán zpět do služby Active Directory. Tato funkce přidává podporu aplikací Office 365. Používá OAuth2 pro přístup k online a místní poštovní schránky v nasazení hybridní exchange.

**Opraveny problémy s upgradem:**

* Na serveru je k dispozici novější verze pomocníka pro přihlášení.
* K instalaci Azure AD Sync se používala vlastní instalační cesta.
* Neplatné kritérium vlastního spojení blokuje upgrade.

**Další opravy:**

* Byly opraveny šablony pro Office Pro Plus.
* Opraveny problémy s instalací způsobené uživatelskými jmény, které začínají pomlčkou.
* Opravena ztráta nastavení sourceAnchor při druhém spuštění průvodce instalací.
* Opraveno trasování ETW pro synchronizaci hesel.

## <a name="104701023"></a>1.0.470.1023
Vydáno: říjen 2014

**Nové funkce:**

* Synchronizace hesel z více místních služby Active Directory do služby Azure AD.
* Lokalizované instalační uzemňovací centrum do všech jazyků systému Windows Server.

**Upgrade z AADSync 1.0 GA**

Pokud už máte nainstalovanou službu Azure AD Sync, je jeden další krok, který musíte provést v případě, že jste změnili některá z pravidel synchronizace předváděcí služby. Po upgradu na verzi 1.0.470.1023 jsou změněna pravidla synchronizace, která jste upravili. U každého upraveného pravidla synchronizace postupujte takto:

1. Vyhledejte změněné pravidlo synchronizace a poznamenejte si změny.
1. Odstraňte pravidlo synchronizace.
1. Vyhledejte nové pravidlo synchronizace, které je vytvořené službou Azure AD Sync, a znovu použijte změny.

**Oprávnění pro účet služby Active Directory**

Aby bylo možné číst hashe hesla ze služby Active Directory, musí být účtu služby Active Directory udělena další oprávnění. Oprávnění k udělení se nazývají "Replikace změn adresáře" a "Replikace změny adresáře všechny." K čtení hesel hashe s heslem je nutné provést obě oprávnění.

## <a name="104190911"></a>1.0.419.0911
Vydáno: září 2014

**Počáteční verze Azure AD Sync.**

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
