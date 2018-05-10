---
title: Řešení pro správu Office 365 v Azure | Microsoft Docs
description: Tento článek poskytuje podrobné informace o konfiguraci a použití řešení Office 365 v Azure.  Obsahuje podrobný popis záznamů Office 365 vytvořené v analýzy protokolů.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: bwren
ms.openlocfilehash: 8797e08ad942687b7d2defd765f4fe3f9765812f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="office-365-management-solution-in-azure-preview"></a>Řešení pro správu Office 365 v Azure (Preview)

![Logo Office 365](media/oms-solution-office-365/icon.png)

Řešení pro správu Office 365 umožňuje monitorovat prostředí Office 365 v analýzy protokolů.

- Monitorování aktivit uživatelů na účty služeb Office 365 a analyzovat vzorce používání a také identifikovat trendy chování. Například můžete rozbalit konkrétní využití scénáře, jako jsou například soubory, které jsou sdíleny mimo vaši organizaci nebo nejoblíbenější webů služby SharePoint.
- Monitorování aktivit správce sledovat změny v konfiguraci nebo operations vysoká oprávnění.
- Zjištění a prozkoumat chování nežádoucí uživatele, který lze přizpůsobit potřebám vaší organizace.
- Předvedení auditu a dodržování předpisů. Například můžete sledovat přístup operací se soubory na důvěrné soubory, které vám můžou pomoct se proces auditu a dodržování předpisů.
- Provádět provozní řešení problémů pomocí [protokolu hledání](../log-analytics/log-analytics-log-search.md) nad data Office 365 aktivit vaší organizace.

## <a name="prerequisites"></a>Požadavky
Toto je požadované před toto řešení je nainstalován a nakonfigurován.

- Organizace předplatné služeb Office 365.
- Přihlašovací údaje pro uživatelský účet, který je globálním správcem.
- Chcete-li přijímat data auditu, je potřeba [konfigurace auditování](https://support.office.com/en-us/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) v rámci vašeho předplatného Office 365.  Všimněte si, že [auditování poštovní schránky](https://technet.microsoft.com/library/dn879651.aspx) je samostatně nakonfigurovaný.  Přesto můžete nainstalovat řešení a shromažďovat další data, pokud není nakonfigurováno auditování.
 


## <a name="management-packs"></a>Sady Management Pack
Toto řešení nenainstaluje žádné sady management Pack v připojených skupin pro správu.
  

## <a name="configuration"></a>Konfigurace
Jednou budete [do svého předplatného přidáte řešení Office 365](../log-analytics/log-analytics-add-solutions.md), budete muset připojit k předplatné služeb Office 365.

1. Přidat do řešení pro správu výstrah do pracovního prostoru analýzy protokolů pomocí procesu popsaného v tématu [přidat řešení](../log-analytics/log-analytics-add-solutions.md).
2. Přejděte na **nastavení** na portálu OMS.
3. V části **připojené zdroje**, vyberte **Office 365**.
4. Klikněte na **připojení Office 365**.<br>![Spojení jednotlivých Office 365](media/oms-solution-office-365/configure.png)
5. Přihlaste se k Office 365 pomocí účtu, který je globálním správcem pro vaše předplatné. 
6. Objeví se předplatné s zátěží, které řešení bude monitorovat.<br>![Spojení jednotlivých Office 365](media/oms-solution-office-365/connected.png) 


## <a name="data-collection"></a>Shromažďování dat
### <a name="supported-agents"></a>Podporovaní agenti
Řešení Office 365 nepodporuje načtení dat ze všech [OMS agenti](../log-analytics/log-analytics-data-sources.md).  Načte data přímo z Office 365.

### <a name="collection-frequency"></a>Četnost shromažďování dat
Odešle Office 365 [webhooku oznámení](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) s podrobná data k analýze protokolů pokaždé, když se vytvoří záznam.

## <a name="using-the-solution"></a>Použití řešení
Když přidáte do pracovního prostoru analýzy protokolů řešení Office 365 **Office 365** dlaždice budou přidány do řídicího panelu. Na této dlaždici se zobrazuje počet a grafické vyjádření počtu počítačů ve vašem prostředí a jejich kompatibilita s aktualizacemi.<br><br>
![Dlaždice souhrnu Office 365](media/oms-solution-office-365/tile.png)  

Klikněte na **Office 365** dlaždici otevřete **Office 365** řídicího panelu.

![Řídicí panel Office 365](media/oms-solution-office-365/dashboard.png)  

Řídicí panel obsahuje sloupce v následující tabulce. Každý sloupec uvádí top deseti výstrah podle počtu odpovídající kritériím tento sloupec pro zadaný obor a časový rozsah. Můžete spustit hledání protokolů, který poskytuje celý seznam kliknutím najdete všechny v dolní části sloupec nebo kliknutím na záhlaví sloupce.

| Sloupec | Popis |
|:--|:--|
| Operace | Poskytuje informace o aktivní uživatelé z vašich všechny monitorované předplatných Office 365. Také bude moci zobrazit počet aktivit, které dojít v průběhu času.
| Výměna | Ukazuje rozpis aktivity systému Exchange Server, například Add-Mailbox oprávnění nebo Set-poštovní schránky. |
| SharePoint | Ukazuje nejvyšší aktivity, že uživatelé provádět na dokumenty služby SharePoint. Pokud přecházíte z této dlaždice stránky hledání zobrazuje podrobnosti o tyto aktivity, jako je například cílovém dokumentu a umístění této aktivity. Například pro událost přistupovat k souboru, bude možné zobrazit dokumentu, do kterého se přistupuje, jeho přidružený účet názvu a IP adresy. |
| Azure Active Directory | Obsahuje hlavní uživatelské aktivity, například Resetovat heslo uživatele a pokusů o přihlášení. Když přejdete k podrobnostem, nebudete moci zobrazit podrobnosti o tyto aktivity, jako je výsledný stav. Toto je nejčastěji užitečné, pokud chcete monitorovat podezřelých aktivit v Azure Active Directory. |




## <a name="log-analytics-records"></a>Záznamy služby Log Analytics

Mají všechny záznamy v pracovním prostoru analýzy protokolů vytvořené řešení Office 365 **typ** z **OfficeActivity**.  **OfficeWorkload** vlastnost určuje služby Office 365, která odkazuje záznam - Exchange azureactivedirectory selhala, SharePoint nebo OneDrive.  **RecordType** vlastnost určuje typ operace.  Vlastnosti pro každý typ operace se bude lišit a jsou uvedeny v následujících tabulkách.

### <a name="common-properties"></a>Běžné vlastnosti
Následující vlastnosti jsou společné pro všechny záznamy Office 365.

| Vlastnost | Popis |
|:--- |:--- |
| Typ | *OfficeActivity* |
| Když | IP adresa zařízení, která byla použita při protokolu byla zaznamenána aktivita. IP adresa se zobrazí ve formátu adresa IPv4 nebo IPv6. |
| OfficeWorkload | Služby Office 365, která odkazuje na záznam.<br><br>Azureactivedirectory selhala<br>Výměna<br>SharePoint|
| Operace | Název aktivity uživatele nebo správce.  |
| Kódu organizace | Identifikátor GUID pro klienta Office 365 vaší organizace. Tato hodnota bude vždy stejná pro vaši organizaci, bez ohledu na to, ve kterém byly provedeny služby Office 365. |
| recordType | Typ operace provádí. |
| ResultStatus | Určuje, zda akce (zadaná ve vlastnosti operace) byla úspěšná, nebo ne. Možné hodnoty jsou úspěšné, PartiallySucceded nebo se nezdařilo. Aktivita správce systému Exchange, má hodnotu buď True nebo False. |
| ID uživatele | Hlavní název uživatele (hlavní název uživatele) uživatele, který provedl akci, která byla vygenerována v záznamu protokolována; například my_name@my_domain_name. Všimněte si, že zaznamenává aktivity prováděné účty systému (například SHAREPOINT\system nebo NTAUTHORITY\SYSTEM) jsou také zahrnuty. | 
| UserKey | Alternativní ID pro uživatele identifikovaného ve vlastnosti ID uživatele.  Například tato vlastnost obsahuje jedinečné ID passport (PUID) pro události se provádí uživatelů na SharePoint, Onedrivu pro firmy a Exchange. Tato vlastnost může také zadejte stejnou hodnotu jako vlastnost ID uživatele pro události, ke kterým dochází v jiných službách a událostech provádí účty systému|
| UserType | Typ uživatele, který provádí operaci.<br><br>Správa<br>Aplikace<br>DcAdmin<br>Regulární<br>Rezervováno<br>ServicePrincipal<br>Systémový |


### <a name="azure-active-directory-base"></a>Základní Azure Active Directory
Následující vlastnosti jsou společné pro všechny záznamy Azure Active Directory.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Azureactivedirectory selhala |
| recordType     | Azureactivedirectory selhala |
| AzureActiveDirectory_EventType | Typ události, Azure AD. |
| ExtendedProperties | Rozšířené vlastnosti události, Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Azure přihlašovací účet služby Active Directory
Tyto záznamy jsou vytvořeny při uživatele služby Active Directory se pokusí přihlásit.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Azureactivedirectory selhala |
| recordType     | AzureActiveDirectoryAccountLogon |
| Aplikace | Aplikace, která spustí událost přihlášení účtu, jako je například Office 15. |
| Klient | Podrobnosti o klientovi zařízení, operačního systému zařízení a prohlížeč zařízení, která byla použita pro události přihlášení účtu. |
| loginStatus | Tato vlastnost je přímo z OrgIdLogon.LoginStatus. Mapování různé zajímavé nezdařených pokusů o přihlášení může provést výstrahy algoritmy. |
| UserDomain | Informace identita klienta (TÍ). | 


### <a name="azure-active-directory"></a>Azure Active Directory
Tyto záznamy jsou vytvořen při změna nebo přidání jsou objekty služby Azure Active Directory.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Azureactivedirectory selhala |
| recordType     | Azureactivedirectory selhala |
| AADTarget | Uživatel, který na byla provedena akce (identifikovaný vlastnost operace). |
| Objekt actor | Uživatel nebo objektu služby, která akci provedla. |
| ActorContextId | Organizace, který je součástí objektu actor identifikátor GUID. |
| ActorIpAddress | Objektu actor IP adresu ve formátu adresy IPV4 nebo IPV6. |
| InterSystemsId | Identifikátor GUID, které sledují akce mezi komponentami v rámci služby Office 365. |
| IntraSystemId |   Identifikátor GUID, které se generují ve službě Azure Active Directory ke sledování akce. |
| SupportTicketId | Pro akci v situacích, "act-on-behalf-of" podporovat ID lístku. |
| TargetContextId | Organizace, který cílový uživatel patří do identifikátor GUID. |


### <a name="data-center-security"></a>Datové Centrum zabezpečení
Tyto záznamy jsou vytvořené z dat auditu zabezpečení dat Center.  

| Vlastnost | Popis |
|:--- |:--- |
| EffectiveOrganization | Název klienta, která byla cílem zvýšení oprávnění nebo rutiny v. |
| ElevationApprovedTime | Časové razítko pro když byla schválena zvýšení oprávnění. |
| ElevationApprover | Název správce společnosti Microsoft. |
| ElevationDuration | Doba trvání, pro který byl aktivní zvýšení oprávnění. |
| ElevationRequestId |  Jedinečný identifikátor pro žádost o zvýšení oprávnění. |
| ElevationRole | Bylo vyžádáno roli zvýšení oprávnění. |
| ElevationTime | Čas zahájení zvýšení oprávnění. |
| Start_Time | Počáteční čas provádění rutiny. |


### <a name="exchange-admin"></a>Správce systému Exchange
Tyto záznamy jsou vytvořeny při provedení změn konfigurace serveru Exchange.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Výměna |
| recordType     | ExchangeAdmin |
| ExternalAccess |  Určuje, zda byl rutinu spustit uživatele v organizaci, pracovníky datového centra společnosti Microsoft nebo účtu služby datacenter nebo delegovaný správce. Hodnota False určuje, že rutina byla spouštět uživatel ve vaší organizaci. Hodnotu True. Určuje, že byl rutinu spouštět pracovníky datacenter, účet služby datacenter nebo delegovaný správce. |
| ModifiedObjectResolvedName |  Toto je popisný název objektu, který rutina, byla změněna. To se zaznamená pouze v případě, že rutina upraví objekt. |
| Název organizace | Název klienta. |
| OriginatingServer | Název serveru, ze kterého byla spuštěna rutina. |
| Parametry | Název a hodnotu pro všechny parametry, které se používaly pomocí rutiny identifikovanou ve vlastnosti operace. |


### <a name="exchange-mailbox"></a>Poštovní schránky serveru Exchange
Tyto záznamy jsou vytvořen při změny nebo přidání jsou poštovní schránky serveru Exchange.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Výměna |
| recordType     | ExchangeItem |
| ClientInfoString | Informace o e-mailovém klientovi, který byl použit k provedení operace, jako je například verze prohlížeče, verze aplikace Outlook a informace o mobilních zařízeních. |
| Client_IPAddress | IP adresa zařízení, která byla použita při operaci protokolu byla zaznamenána. IP adresa se zobrazí ve formátu adresa IPv4 nebo IPv6. |
| ClientMachineName | Název počítače, který je hostitelem klientovi aplikace Outlook. |
| ClientProcessName | Klient e-mailu, který byl použit pro přístup k poštovní schránku. |
| ClientVersion | Verze e-mailového klienta. |
| InternalLogonType | Vyhrazeno pro interní použití. |
| Logon_Type | Určuje typ uživatele, který získat přístup k poštovní schránku a provést operaci, která byla zapsána do protokolu. |
| LogonUserDisplayName |    Uživatelsky přívětivý název uživatele, který provádí operaci. |
| LogonUserSid | SID uživatele, který provedl operaci. |
| MailboxGuid | Identifikátor GUID Exchange poštovní schránky, který byl přístupný. |
| MailboxOwnerMasterAccountSid | Účet vlastníka poštovní schránky hlavní SID účtu. |
| MailboxOwnerSid | Identifikátor SID vlastníka poštovní schránky. |
| MailboxOwnerUPN | E-mailovou adresu osoby, která vlastní poštovní schránky, který byl přístupný. |


### <a name="exchange-mailbox-audit"></a>Auditování poštovní schránky systému Exchange
Tyto záznamy jsou vytvořeny při vytvoření položky auditu poštovní schránky.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Výměna |
| recordType     | ExchangeItem |
| Položka | Představuje položku, na kterém byla provedena operace | 
| SendAsUserMailboxGuid | Identifikátor GUID Exchange poštovní schránky, které se přistupovalo k odesílání e-mailu. |
| SendAsUserSmtp | Adresa SMTP uživatele, který jde o zosobňovaného. |
| SendonBehalfOfUserMailboxGuid | Identifikátor GUID Exchange poštovní schránky, které se přistupovalo k odesílání pošty jménem. |
| SendOnBehalfOfUserSmtp | Adresa SMTP uživatele, jehož jménem je odesláno e-mailu. |


### <a name="exchange-mailbox-audit-group"></a>Skupiny auditování poštovní schránky serveru Exchange
Tyto záznamy jsou vytvořen při změny nebo přidání jsou skupiny Exchange.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | Výměna |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informace o jednotlivých položkách ve skupině. |
| CrossMailboxOperations | Označuje, pokud operace zapojená víc než jednu poštovní schránku. |
| DestMailboxId | Nastavte jenom v případě, že parametr CrossMailboxOperations má hodnotu True. Určuje poštovní schránku cílový identifikátor GUID. |
| DestMailboxOwnerMasterAccountSid | Nastavte jenom v případě, že parametr CrossMailboxOperations má hodnotu True. Určuje identifikátor SID pro hlavní účet SID vlastníka poštovní schránky cíl. |
| DestMailboxOwnerSid | Nastavte jenom v případě, že parametr CrossMailboxOperations má hodnotu True. Určuje identifikátor SID poštovní schránky, cíl. |
| DestMailboxOwnerUPN | Nastavte jenom v případě, že parametr CrossMailboxOperations má hodnotu True. Určuje název UPN vlastníka poštovní schránky, cíl. |
| DestFolder | Cílová složka pro operace, jako je například přesunout. |
| Složka | Složky, kde se nachází skupinu položek. |
| Složky |     Informace o zahrnutých v operaci; zdrojové složky Pokud například složky jsou vybrané a pak je odstranit. |


### <a name="sharepoint-base"></a>Základní služby SharePoint
Tyto vlastnosti jsou společné pro všechny záznamy služby SharePoint.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Určuje, že k události došlo ve službě SharePoint. Možné hodnoty jsou SharePoint nebo ObjectModel. |
| Typ položky | Typ objektu, který byl přístup nebo upravil. Najdete v tabulce ItemType podrobnosti pro typy objektů. |
| MachineDomainInfo | Informace o zařízení synchronizační operace. Tyto informace je nahlášeno pouze v případě, že je k dispozici v požadavku. |
| MachineId |   Informace o zařízení synchronizační operace. Tyto informace je nahlášeno pouze v případě, že je k dispozici v požadavku. |
| Site_ | Identifikátor GUID lokality, kde se nachází soubor nebo složku přístup uživatele. |
| Source_Name | Typ entity, která aktivuje operace auditování. Možné hodnoty jsou SharePoint nebo ObjectModel. |
| UserAgent | Informace o klienta nebo prohlížeče uživatele. Tato informace jsou poskytovány klienta nebo prohlížeče. |


### <a name="sharepoint-schema"></a>Schéma služby SharePoint
Tyto záznamy jsou vytvořeny při provedení změn konfigurace služby SharePoint.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Volitelný řetězec pro vlastní události. |
| Event_Data |  Volitelné datové části pro vlastní události. |
| ModifiedProperties | Vlastnost je zahrnuté pro správce události, jako je například přidávání uživatele jako člen webu nebo skupinu pro správu kolekce webu. Vlastnost obsahuje název vlastnosti, které bylo změněno (například správce webu skupiny), nová hodnota změněné vlastnosti (takový uživatel, který byl přidán jako správce webu) a předchozí hodnotu upravený objekt. |


### <a name="sharepoint-file-operations"></a>Operace se soubory služby SharePoint
Tyto záznamy jsou vytvořeny v reakci na operací se soubory ve službě SharePoint.

| Vlastnost | Popis |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | Příponu souboru, který je zkopírovat nebo přesunout. Tato vlastnost se zobrazí pouze pro FileCopied a FileMoved události. |
| DestinationFileName | Název souboru, který je zkopírovat nebo přesunout. Tato vlastnost se zobrazí pouze pro FileCopied a FileMoved události. |
| DestinationRelativeUrl | Adresa URL cílovou složku, kde je soubor zkopírovat nebo přesunout. Kombinace hodnot pro parametry SiteURL, DestinationRelativeURL a DestinationFileName je stejná jako hodnota pro vlastnost ObjectID, což je úplná cesta název souboru, který jste zkopírovali. Tato vlastnost se zobrazí pouze pro FileCopied a FileMoved události. |
| SharingType | Typ oprávnění, které byly přiřazeny uživateli, který prostředek se sdílet s sdílení. Tento uživatel je určen parametrem UserSharedWith. |
| Site_Url | Adresa URL webu, kde je uložený soubor nebo složku přístup uživatele. |
| SourceFileExtension | Příponu souboru, který byl přístupný uživatelem. Tato vlastnost je prázdná, pokud je objekt, který byl přístupný složka. |
| SourceFileName |  Název souboru nebo složky přístup uživatele. |
| SourceRelativeUrl | Adresa URL složky, která obsahuje soubor dostupný uživatelem. Kombinace hodnot pro parametry SiteURL SourceRelativeURL a SourceFileName je stejná jako hodnota pro vlastnost ObjectID, což je název úplná cesta k souboru přístup uživatele. |
| UserSharedWith |  Uživatel, který prostředek se sdílet s. |




## <a name="sample-log-searches"></a>Ukázky hledání v protokolech
V následující tabulce jsou uvedeny ukázky hledání v protokolech pro záznamy aktualizace shromážděné tímto řešením.

| Dotaz | Popis |
| --- | --- |
|Počet všech operací na předplatné služeb Office 365 |Typ = OfficeActivity &#124; měření count() operací |
|Použití webů služby SharePoint|Typ = OfficeActivity OfficeWorkload = sharepoint &#124; měření count() jako počet podle SiteUrl &#124; řazení počet asc|
|Operace se soubory přístup podle typu uživatele|Typ = OfficeActivity OfficeWorkload = sharepoint operaci = FileAccessed &#124; měření count() podle UserType|
|Hledání s konkrétní – klíčové slovo|Typ = OfficeActivity OfficeWorkload = azureactivedirectory selhala "Test"|
|Externí akce monitorování na Exchange|Typ = OfficeActivity OfficeWorkload = exchange ExternalAccess = true|



## <a name="troubleshooting"></a>Řešení potíží

Pokud vaše řešení Office 365 není shromažďování dat podle očekávání, zkontrolujte stav na portálu OMS na **nastavení** -> **připojené zdroje** -> **Office 365**. Následující tabulka popisuje každý stav.

| Status | Popis |
|:--|:--|
| Aktivní | Předplatné služeb Office 365 je aktivní a zatížení se úspěšně připojil k pracovní prostor analýzy protokolů. |
| Čekající na vyřízení | Je aktivní předplatné služeb Office 365, ale zatížení není dosud připojen do pracovního prostoru analýzy protokolů úspěšně. Při prvním připojení předplatné služeb Office 365, všechny úlohy bude tento stav dokud úspěšně připojeno. Počkejte prosím 24 hodin pro všechny úlohy přepnout na aktivní. |
| Neaktivní | Předplatné služeb Office 365 je v neaktivním stavu. Zkontrolujte stránku Správce služeb Office 365 podrobnosti. Po aktivaci předplatné služeb Office 365, odpojit z pracovního prostoru analýzy protokolů a propojit jej znovu a začít přijímat data. |



## <a name="next-steps"></a>Další postup
* K zobrazení podrobných údajů o aktualizaci použijte Hledání v protokolu služby [Log Analytics](../log-analytics/log-analytics-log-searches.md).
* [Vytvořit vlastní řídicí panely](../log-analytics/log-analytics-dashboards.md) zobrazíte váš oblíbený vyhledávací dotazy Office 365.
* [Vytvářet výstrahy](../log-analytics/log-analytics-alerts.md) proaktivně oznámení důležité aktivit Office 365.  
