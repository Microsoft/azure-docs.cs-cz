---
title: Referenční informace k typům entit Azure Sentinel | Microsoft Docs
description: Tento článek zobrazuje typy entit Azure Sentinel a jejich požadované identifikátory.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 17a4df3037f9922d92fca924de0d246458cfa08e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102456251"
---
# <a name="azure-sentinel-entity-types-reference"></a>Reference k typům entit Azure Sentinel

## <a name="entity-types-and-identifiers"></a>Typy a identifikátory entit

V následující tabulce jsou uvedeny **typy entit** , které jsou aktuálně k dispozici pro mapování v rámci Azure Sentinel, a **atributy** dostupné jako **identifikátory** pro každý typ entity, který se zobrazí v rozevíracím seznamu **identifikátory** v části [mapování entit](map-data-fields-to-entities.md) v [Průvodci analytickým pravidlem](tutorial-detect-threats-custom.md).

Každý z identifikátorů ve sloupci **požadované identifikátory** je minimálně nezbytný k identifikaci své entity. Požadovaný identifikátor ale nemusí sám o sobě stačit k poskytnutí *jedinečné* identifikace. Více používaných identifikátorů je větší pravděpodobnost jedinečné identifikace. Pro jedno mapování entit můžete použít až tři identifikátory.

Nejlepší výsledky – pro zaručenou jedinečnou identifikaci – Pokud je to možné, měli byste použít identifikátory ze sloupce **nejsilnějších identifikátorů** . Použití více silných identifikátorů umožňuje korelaci mezi silnými identifikátory z různých zdrojů dat a schémat. Tím umožníte, aby Azure Sentinel poskytoval pro danou entitu komplexnější přehledy.

| Typ entity | Identifikátory | Požadované identifikátory | Nejsilnější identifikátory |
| - | - | - | - |
| [**Uživatelský účet**](#user-account)<br>*Zohledňují* | Name<br>FullName<br>NTDomain<br>DnsDomain<br>UPNSuffix<br>ID<br>AadTenantId<br>AadUserId<br>PUID<br>IsDomainJoined<br>DisplayName<br>ObjectGuid | FullName<br>ID<br>Name<br>AadUserId<br>PUID<br>ObjectGuid | Název + NTDomain<br>Název + UPNSuffix<br>AADUserId<br>ID |
| [**Hostitel**](#host) | DnsDomain<br>NTDomain<br>Název hostitele<br>FullName<br>NetBiosName<br>AzureID<br>OMSAgentID<br>OSFamily<br>OSVersion<br>IsDomainJoined | FullName<br>Název hostitele<br>NetBiosName<br>AzureID<br>OMSAgentID | Název hostitele + NTDomain<br>Název hostitele + DnsDomain<br>Název NetBios + NTDomain<br>Název NetBios + DnsDomain<br>AzureID<br>OMSAgentID |
| [**IP adresa**](#ip-address)<br>*IP* | Adresa | Adresa | |
| [**Malware**](#malware) | Name<br>Kategorie | Name | |
| [**Soubor**](#file) | Adresář<br>Název | Název | |
| [**Proces**](#process) | ID<br>CommandLine<br>ElevationToken<br>CreationTimeUtc | CommandLine<br>ID | |
| [**Cloudová aplikace**](#cloud-application)<br>*(CloudApplication)* | AppId<br>Name<br>InstanceName | AppId<br>Name | |
| [**Název domény**](#domain-name)<br>*NÁZV* | DomainName | DomainName | |
| [**Prostředek Azure**](#azure-resource) | ResourceId | ResourceId | |
| [**Hodnota hash souboru**](#file-hash)<br>*(Hash)* | Algoritmus<br>Hodnota | Algoritmus + hodnota | |
| [**Klíč registru**](#registry-key) | Hive<br>Klíč | Hive<br>Klíč | Podregistr + – klíč |
| [**Hodnota registru**](#registry-value) | Name<br>Hodnota<br>ValueType | Name | |
| [**Skupina zabezpečení**](#security-group) | DistinguishedName<br>SID<br>ObjectGuid | DistinguishedName<br>SID<br>ObjectGuid | |
| [**URL**](#url) | URL | URL | |
| [**Zařízení IoT**](#iot-device) | IoTHub<br>DeviceId<br>DeviceName<br>IoTSecurityAgentId<br>DeviceType<br>Zdroj<br>SourceRef<br>Manufacturer<br>Modelování<br>OperatingSystem<br>Adresa<br>MacAddress<br>Protokoly<br>SerialNumber | IoTHub<br>DeviceId | IoTHub + DeviceId |
| [**Vůči**](#mailbox) | MailboxPrimaryAddress<br>DisplayName<br>Názvu<br>ExternalDirectoryObjectId<br>RiskLevel | MailboxPrimaryAddress | |
| [**Poštovní cluster**](#mail-cluster) | NetworkMessageIds<br>CountByDeliveryStatus<br>CountByThreatType<br>CountByProtectionStatus<br>Hrozby<br>Dotaz<br>QueryTime<br>MailCount<br>IsVolumeAnomaly<br>Zdroj<br>ClusterSourceIdentifier<br>ClusterSourceType<br>ClusterQueryStartTime<br>ClusterQueryEndTime<br>ClusterGroup | Dotaz<br>Zdroj | Dotaz a zdroj |
| [**Poštovní zpráva**](#mail-message) | Příjemce<br>Adrese<br>Hrozby<br>Odesílatel<br>P1Sender<br>P1SenderDisplayName<br>P1SenderDomain<br>SenderIP<br>P2Sender<br>P2SenderDisplayName<br>P2SenderDomain<br>Přijetí<br>NetworkMessageId<br>InternetMessageId<br>Předmět<br>BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5<br>AntispamDirection<br>DeliveryAction<br>DeliveryLocation<br>Jazyk<br>ThreatDetectionMethods | NetworkMessageId<br>Příjemce | NetworkMessageId a příjemce |
| [**Odeslaná pošta**](#submission-mail) | SubmissionId<br>SubmissionDate<br>Odesílatel<br>NetworkMessageId<br>Timestamp<br>Příjemce<br>Odesílatel<br>SenderIp<br>Předmět<br>ReportType | SubmissionId<br>NetworkMessageId<br>Příjemce<br>Odesílatel |  |
|

## <a name="entity-type-schemas"></a>Schémata typů entit

Níže najdete podrobné informace o všech schématech jednotlivých typů entit. Všimnete si, že mnohé z těchto schémat obsahují odkazy na jiné typy entit – například schéma uživatelského účtu obsahuje odkaz na typ entity hostitele, protože jeden atribut uživatelského účtu je hostitel, na kterém je definován. Tyto externě propojené entity se nedají použít jako identifikátory pro mapování entit, ale jsou velmi užitečné při poskytování kompletního přehledu entit na stránkách entit a v grafu šetření.

> [!NOTE]
> Otazník, který následuje za hodnotou ve sloupci **typ** , označuje, že pole může mít hodnotu null.

## <a name="user-account"></a>Uživatelský účet

*Název entity: účet*

| Pole | Typ | Popis |
| ----- | ---- | ----------- |
| Typ | Řetězec | zohledňují |
| Name | Řetězec | Název účtu. V tomto poli by se měl ukládat jenom název bez přidané domény. |
| *Úplný název* | *NENÍ K DISPOZICI* | *Nejedná se o součást schématu, která je součástí zpětné kompatibility se starou verzí mapování entit.*
| NTDomain | Řetězec | Název domény pro rozhraní NETBIOS, jak se zobrazuje ve formátu výstrahy – doména \ uživatelské_jméno Příklady: finance, NT AUTHORITY |
| DnsDomain | Řetězec | Plně kvalifikovaný název DNS domény. Příklady: finance.contoso.com |
| UPNSuffix | Řetězec | Přípona hlavního názvu uživatele pro účet. V některých případech je to také název domény. Příklady: contoso.com |
| Hostitel | Entita | Hostitel, který obsahuje účet, pokud se jedná o místní účet. |
| ID | Řetězec | Identifikátor zabezpečení účtu, jako je například S-1-5-18. |
| AadTenantId | Hlavních? | ID tenanta Azure AD, pokud je známé. |
| AadUserId | Hlavních? | ID objektu účtu Azure AD, pokud je známé. |
| PUID | Hlavních? | ID uživatele služby Azure AD Passport, pokud je známé. |
| IsDomainJoined | Logick? | Určuje, zda se jedná o doménový účet. |
| DisplayName | Řetězec | Zobrazovaný název účtu. |
| ObjectGuid | Hlavních? | Atribut objectGUID je atribut s jednou hodnotou, který je jedinečným identifikátorem objektu přiřazeným službou Active Directory. |
|

Silné identifikátory entity účtu:

- Název + UPNSuffix
- AadUserId
- SID + hostitel (vyžaduje se pro identifikátory SID předdefinovaných účtů)
- SID (s výjimkou identifikátorů SID předdefinovaných účtů)
- Name + NTDomain (Pokud NTDomain je předdefinovaná doména, například pracovní skupina)
- Název + hostitel (Pokud je NTDomain předdefinovaná doména, například pracovní skupina)
- Název + DnsDomain
- PUID
- ObjectGuid

Slabé identifikátory entity účtu:

- Name

## <a name="host"></a>Hostitel

| Pole | Typ | Popis |
| ----- | ---- | ----------- |
| Typ | Řetězec | provoz |
| DnsDomain | Řetězec | Doména DNS, do které tento hostitel patří. By měl obsahovat úplnou příponu DNS pro doménu, pokud je to známo. |
| NTDomain | Řetězec | Doména NT, do které tento hostitel patří. |
| Název hostitele | Řetězec | Název hostitele bez přípony domény |
| *Úplný název* | *NENÍ K DISPOZICI* | *Nejedná se o součást schématu, která je součástí zpětné kompatibility se starou verzí mapování entit.*
| NetBiosName | Řetězec | Název hostitele (Pre-Windows 2000). |
| IoTDevice | Entita | Entita zařízení IoT (Pokud tento hostitel představuje zařízení IoT) |
| AzureID | Řetězec | ID prostředku Azure virtuálního počítače, pokud je známo |
| OMSAgentID | Řetězec | ID agenta OMS, pokud má hostitel nainstalovaného agenta OMS |
| OSFamily | Vytváření? | Jedna z následujících hodnot: <li>Linux<li>Windows<li>Android<li>iOS |
| OSVersion | Řetězec | Reprezentace operačního systému, která je bezplatná.<br>Toto pole má obsahovat konkrétní verze, které jsou podrobněji větší než OSFamily nebo budoucí hodnoty nejsou podporovány výčtem OSFamily. |
| IsDomainJoined | Logická hodnota | Určuje, zda tento hostitel patří do domény. |
|

Silné identifikátory entity hostitele:
- Název hostitele + NTDomain
- Název hostitele + DnsDomain
- Název NetBios + NTDomain
- Název NetBios + DnsDomain
- AzureID
- OMSAgentID
- IoTDevice (není podporováno pro mapování entit)

Slabé identifikátory entity hostitele:
- Název hostitele
- NetBiosName

## <a name="ip-address"></a>IP adresa

*Název entity: IP*

| Pole | Typ | Popis |
| ----- | ---- | ----------- |
| Typ | Řetězec | IP |
| Adresa | Řetězec | IP adresa jako řetězec, např. 127.0.0.1 (buď v protokolu IPv4 nebo IPv6). |
| Umístění | Zeměpisná poloha | Kontext geografického umístění připojený k entitě IP |
|

Silné identifikátory entity IP:
- Adresa

## <a name="malware"></a>Malware

| Pole | Typ | Popis |
| ----- | ---- | ----------- |
| Typ | Řetězec | jiný |
| Name | Řetězec | Název malwaru dodavatelem, například `Win32/Toga!rfn` . |
| Kategorie | Řetězec | Kategorie malwaru dodavatelem, třeba trojský kůň. |
| Soubory | Seznam\<Entity> | Seznam entit propojených souborů, na kterých byl malware nalezen Může obsahovat soubor entity inline nebo as reference.<br>Další podrobnosti o struktuře najdete v entitě souboru. |
| Procesy | Seznam\<Entity> | Seznam entit propojených procesů, na kterých byl malware nalezen Tato možnost se často používá, když se aktivuje výstraha u aktivity s neaktivními soubory.<br>Další podrobnosti o struktuře najdete v entitě [procesu](#process) . |
|

Silné identifikátory entity malwaru:

- Název + kategorie

## <a name="file"></a>Soubor

| Pole | Typ | Popis |
| ----- | ---- | ----------- |
| Typ | Řetězec | souborů |
| Adresář | Řetězec | Úplná cesta k souboru. |
| Name | Řetězec | Název souboru bez cesty (některé výstrahy nemusí obsahovat cestu). |
| Hostitel | Entita | Hostitel, na kterém byl soubor uložen. |
| Hodnoty hash | Seznam &lt; entit&gt; | Hodnoty hash souborů přidružené k tomuto souboru. |
|

Silné identifikátory entity souboru:
- Název + adresář
- Název + hodnota hash
- Název + adresář + hodnota hash

## <a name="process"></a>Proces

| Pole | Typ | Popis |
| ----- | ---- | ----------- |
| Typ | Řetězec | přihlášení |
| ID | Řetězec | ID procesu. |
| CommandLine | Řetězec | Příkazový řádek použitý k vytvoření procesu. |
| ElevationToken | Vytváření? | Token zvýšení oprávnění přidružený k procesu.<br>Možné hodnoty:<li>TokenElevationTypeDefault<li>TokenElevationTypeFull<li>TokenElevationTypeLimited |
| CreationTimeUtc | Hodnotu? | Čas spuštění procesu. |
| ImageFile | Entita (soubor) | Může obsahovat vložený soubor entity nebo jako referenci.<br>Další podrobnosti o struktuře najdete v entitě souboru. |
| Účet | Entita | Účet, ve kterém jsou spuštěny procesy.<br>Může obsahovat vložené entity [účtu](#user-account) nebo odkazy.<br>Další podrobnosti o struktuře najdete v entitě [účtu](#user-account) . |
| ParentProcess | Entita (proces) | Entita nadřazeného procesu. <br>Může obsahovat částečná data, tj. pouze PID. |
| Hostitel | Entita | Hostitel, na kterém byl proces spuštěn. |
| LogonSession | Entita (HostLogonSession) | Relace, ve které byl proces spuštěn. |
|

Silné identifikátory entity procesu:

- Hostitel + ProcessId + CreationTimeUtc
- Hostitel + ParentProcessId + CreationTimeUtc a příkazový řádek
- Hostitel + ProcessId + CreationTimeUtc + ImageFile
- Host + ProcessId + CreationTimeUtc + ImageFile. hash

Slabé identifikátory entity procesu:

- ProcessId + CreationTimeUtc + CommandLine (bez hostitele)
- ProcessId + CreationTimeUtc + ImageFile (a žádný hostitel)

## <a name="cloud-application"></a>Cloudová aplikace

*Název entity: CloudApplication*

| Pole | Typ | Popis |
| ----- | ---- | ----------- |
| Typ | Řetězec | Cloud-aplikace |
| AppId | Int | Technický identifikátor aplikace Mělo by se jednat o jednu z hodnot definovaných v seznamu [identifikátorů cloudových aplikací](#cloud-application-identifiers). Hodnota pole AppId je nepovinná. |
| Name | Řetězec | Název související cloudové aplikace. Hodnota názvu aplikace je volitelná. |
| InstanceName | Řetězec | Uživatelsky definovaný název instance cloudové aplikace. Často se používá k rozlišení mezi několika aplikacemi stejného typu, které má zákazník. |
|

Silné identifikátory entity cloudové aplikace:
 - AppId (bez InstanceName)
 - Název (bez názvu instance)
 - AppId + InstanceName
 - Název + název instance

## <a name="domain-name"></a>Název domény

*Název entity: DNS*

| Pole | Typ | Popis |
| ----- | ---- | ----------- |
| Typ | Řetězec | názv |
| DomainName | Řetězec | Název záznamu DNS přidruženého k výstraze. |
| Adresa | Vypsat &lt; entitu (IP)&gt; | Entity, které odpovídají vyřešeným IP adresám. |
| DnsServerIp | Entita (IP) | Entita, která představuje server DNS, který překládá požadavek. |
| HostIpAddress | Entita (IP) | Entita, která představuje klienta žádosti DNS. |
|

Silné identifikátory entity DNS:
- Název_domény + DnsServerIp + HostIpAddress

Slabé identifikátory entity DNS:
- Název_domény + HostIpAddress

## <a name="azure-resource"></a>Prostředek Azure

| Pole | Typ | Popis |
| ----- | ---- | ----------- |
| Typ | Řetězec | "Azure-Resource" |
| ResourceId | Řetězec | ID prostředku Azure prostředku. |
| SubscriptionId | Řetězec | ID předplatného prostředku |
| TryGetResourceGroup | Logická hodnota | Hodnota skupiny prostředků, pokud existuje. |
| TryGetProvider | Logická hodnota | Hodnota poskytovatele, pokud existuje. |
| TryGetName | Logická hodnota | Hodnota name, pokud existuje. |
|

Silné identifikátory entity prostředku Azure:
- ResourceId

## <a name="file-hash"></a>Hodnota hash souboru

*Název entity: hash souborů*

| Pole | Typ | Popis |
| ----- | ---- | ----------- |
| Typ | Řetězec | ' hash ' |
| Algoritmus | Výčet | Typ algoritmu hash. Možné hodnoty:<li>Neznámý<li>MD5<li>SHA1<li>SHA256<li>SHA256AC |
| Hodnota | Řetězec | Hodnota hash |
|

Silné identifikátory entity hash souborů:
- Algoritmus + hodnota

## <a name="registry-key"></a>Klíč registru

*Název entity: RegistryKey*

| Pole | Typ | Popis |
| ----- | ---- | ----------- |
| Typ | Řetězec | ' registry-key ' |
| Hive | Vytváření? | Jedna z následujících hodnot:<li>HKEY_LOCAL_MACHINE<li>HKEY_CLASSES_ROOT<li>HKEY_CURRENT_CONFIG<li>HKEY_USERS<li>HKEY_CURRENT_USER_LOCAL_SETTINGS<li>HKEY_PERFORMANCE_DATA<li>HKEY_PERFORMANCE_NLSTEXT<li>HKEY_PERFORMANCE_TEXT<li>HKEY_A<li>HKEY_CURRENT_USER |
| Klíč | Řetězec | Cesta ke klíči registru. |
|

Silné identifikátory entity klíče registru:
- Podregistr + – klíč

## <a name="registry-value"></a>Hodnota registru

*Název entity: RegistryValue*

| Pole | Typ | Popis |
| ----- | ---- | ----------- |
| Typ | Řetězec | ' Registry-value ' |
| Klíč | Entita (RegistryKey) | Entita klíče registru |
| Name | Řetězec | Název hodnoty registru. |
| Hodnota | Řetězec | Reprezentace dat hodnot ve formátu řetězce |
| ValueType | Vytváření? | Jedna z následujících hodnot:<li>Řetězec<li>Binární<li>Hodnoty<li>Hodnota<li>Víceřetězcová řetězcová<li>ExpandString<li>Žádné<li>Neznámý<br>Hodnoty by měly odpovídat výčtu Microsoft. Win32. hodnota RegistryValueKind. |
|

Silné identifikátory entity hodnota registru:
- Klíč + název

Slabé identifikátory entity hodnoty registru:
- Název (bez klíče)

## <a name="security-group"></a>Skupina zabezpečení

*Název entity: zabezpečení*

| Pole | Typ | Popis |
| ----- | ---- | ----------- |
| Typ | Řetězec | Skupina Security-Group |
| DistinguishedName | Řetězec | Rozlišující název skupiny |
| SID | Řetězec | Atribut SID je atribut s jednou hodnotou, který určuje identifikátor zabezpečení (SID) skupiny. |
| ObjectGuid | Hlavních? | Atribut objectGUID je atribut s jednou hodnotou, který je jedinečným identifikátorem objektu přiřazeným službou Active Directory. |
|

Silné identifikátory entity skupiny zabezpečení:
 - DistinguishedName
 - SID
 - ObjectGuid

## <a name="url"></a>URL

| Pole | Typ | Popis |
| ----- | ---- | ----------- |
| Typ | Řetězec | Adresa URL |
| URL | Identifikátor URI | Úplná adresa URL, na kterou odkazuje entita |
|

Silné identifikátory entity adresy URL:
- Adresa URL (při absolutní adrese URL)

Slabé identifikátory entity adresy URL:
- Adresa URL (při relativní adrese URL)

## <a name="iot-device"></a>Zařízení IoT

*Název entity: IoTDevice*

| Pole | Typ | Popis |
| ----- | ---- | ----------- |
| Typ | Řetězec | 'iotdevice' |
| IoTHub | Entita (AzureResource) | Entita AzureResource představující IoT Hub, do které zařízení patří |
| DeviceId | Řetězec | ID zařízení v kontextu IoT Hub. |
| DeviceName | Řetězec | Popisný název zařízení. |
| IoTSecurityAgentId | Hlavních? | ID agenta v programu *Defender pro IoT* spuštěné v zařízení. |
| DeviceType | Řetězec | Typ zařízení ("senzor teploty", "mrazicíer", "Větrná turbína" atd.) |
| Zdroj | Řetězec | Zdroj (Microsoft/dodavatel) entity zařízení. |
| SourceRef | Entita (URL) | Adresa URL odkazu na zdrojovou položku, kde je zařízení spravováno. |
| Manufacturer | Řetězec | Výrobce zařízení |
| Modelování | Řetězec | Model zařízení |
| OperatingSystem | Řetězec | Operační systém, na kterém je zařízení spuštěno. |
| Adresa | Entita (IP) | Aktuální IP adresa zařízení. |
| MacAddress | Řetězec | Adresa MAC zařízení. |
| Protokoly | Seznam &lt; řetězců&gt; | Seznam protokolů, které zařízení podporuje. |
| SerialNumber | Řetězec | Sériové číslo zařízení. |
|

Silné identifikátory entity zařízení IoT:
- IoTHub + DeviceId

Slabé identifikátory entity zařízení IoT:
- DeviceId (bez IoTHub)

## <a name="mailbox"></a>Mailbox

| Pole | Typ | Popis |
| ----- | ---- | ----------- |
| Typ | Řetězec | vůči |
| MailboxPrimaryAddress | Řetězec | Primární adresa poštovní schránky. |
| DisplayName | Řetězec | Zobrazovaný název poštovní schránky |
| Názvu | Řetězec | Hlavní název uživatele poštovní schránky. |
| RiskLevel | Vytváření? | Úroveň rizika této poštovní schránky. Možné hodnoty:<li>Žádné<li>Nízká<li>Střední<li>Vysoká |
| ExternalDirectoryObjectId | Hlavních? | Identifikátor AzureAD poštovní schránky V entitě účtu se podobá AadUserId, ale tato vlastnost je specifická pro objekt poštovní schránky na straně Office. |
|

Silné identifikátory entity poštovní schránky:
- MailboxPrimaryAddress

## <a name="mail-cluster"></a>Poštovní cluster

*Název entity: MailCluster*

> [!NOTE]
> **MDO**  =  **Microsoft Defender pro Office 365**, dříve označovaný jako Office 365 Advanced Threat Protection (O365 ATP).

| Pole | Typ | Popis |
| ----- | ---- | ----------- |
| Typ | Řetězec | "mail-cluster" |
| NetworkMessageIds | &lt;Řetězec IList&gt; | ID e-mailových zpráv, které jsou součástí clusteru pošty. |
| CountByDeliveryStatus | &lt;Řetězec IDictionary, int&gt; | Počet e-mailových zpráv podle reprezentace řetězce DeliveryStatus |
| CountByThreatType | &lt;Řetězec IDictionary, int&gt; | Počet e-mailových zpráv podle reprezentace řetězce ThreatType |
| CountByProtectionStatus | &lt;Řetězec IDictionary, Long&gt; | Počet e-mailových zpráv podle stavu ochrany před hrozbami |
| Hrozby | &lt;Řetězec IList&gt; | Hrozby e-mailových zpráv, které jsou součástí clusteru pošty. |
| Dotaz | Řetězec | Dotaz, který se použil k identifikaci zpráv v e-mailovém clusteru |
| QueryTime | Hodnotu? | Čas dotazu. |
| MailCount | Hmot? | Počet e-mailových zpráv, které jsou součástí clusteru pošty. |
| IsVolumeAnomaly | Logick? | Určuje, zda se jedná o poštovní cluster anomálií svazku. |
| Zdroj | Řetězec | Zdroj poštovního clusteru (výchozí je O365 ATP). |
| ClusterSourceIdentifier | Řetězec | ID síťové zprávy e-mailu, která je zdrojem tohoto poštovního clusteru. |
| ClusterSourceType | Řetězec | Typ zdroje poštovního clusteru Tím se namapuje na nastavení MailClusterSourceType z MDO (viz poznámku výše). |
| ClusterQueryStartTime | Hodnotu? | Čas spuštění clusteru – používá se jako počáteční čas pro dotaz na počty clusterů. Obvykle se jedná o data do času ukončení minus nastavení DaysToLookBack z MDO (viz poznámku výše). |
| ClusterQueryEndTime | Hodnotu? | Čas ukončení clusteru – používá se jako koncový čas pro dotaz na počty clusterů. Obvykle je čas přijetí e-mailových dat. |
| ClusterGroup | Řetězec | Odpovídá klíči dotazu Kusto použitému na MDO (viz poznámku výše). |
|

Silné identifikátory entity clusteru pošty:
- Dotaz a zdroj

## <a name="mail-message"></a>Poštovní zpráva

*Název entity: MailMessage*

| Pole | Typ | Popis |
| ----- | ---- | ----------- |
| Typ | Řetězec | "Mail-Message" |
| Soubory | &lt;Soubor IList&gt; | Souborové entity těchto příloh e-mailové zprávy. |
| Příjemce | Řetězec | Příjemce této e-mailové zprávy V případě více příjemců se e-mailová zpráva zkopíruje a každá kopie má jednoho příjemce. |
| Adrese | &lt;Řetězec IList&gt; | Adresy URL obsažené v této e-mailové zprávě |
| Hrozby | &lt;Řetězec IList&gt; | Hrozby obsažené v této e-mailové zprávě |
| Odesílatel | Řetězec | E-mailová adresa odesílatele |
| P1Sender | Řetězec | ID e-mailu (delegovaný) uživatel, který odeslal tento e-mail jménem uživatele P2 (primární) Pokud se e-maily neodesílají pomocí delegáta, tato hodnota se rovná P2Sender. |
| P1SenderDisplayName | Řetězec | Zobrazované jméno uživatele (delegovaného), který odeslal tento e-mail jménem P2 (primární) uživatele. Reprezentované v záhlaví e-mailu vlastností "OnbehalfofSenderDisplayName". |
| P1SenderDomain | Řetězec | E-mailová doména (delegovaný) uživatel, který odeslal tento e-mail jménem P2 (primární) uživatele Pokud se e-maily neodesílají pomocí delegáta, tato hodnota se rovná P2SenderDomain. |
| P2Sender | Řetězec | E-mail (primární) uživatel jménem odesílatele tohoto e-mailu. |
| P2SenderDisplayName | Řetězec | Zobrazovaný název (primární) uživatel jménem odesílatele tohoto e-mailu Pokud e-maily nejsou odesílány delegátem, představuje zobrazované jméno odesílatele. |
| P2SenderDomain | Řetězec | E-mailová doména (primární) uživatel jménem odesílatele tohoto e-mailu. Pokud e-maily nejsou odesílány delegátem, představuje to doména odesilatele. |
| SenderIP | Řetězec | IP adresa odesilatele. |
| Přijetí | DateTime | Datum přijetí této zprávy |
| NetworkMessageId | Hlavních? | ID síťové zprávy této e-mailové zprávy |
| InternetMessageId | Řetězec | ID internetové zprávy této e-mailové zprávy |
| Předmět | Řetězec | Předmět této e-mailové zprávy |
| BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5 | UInt? | Používá se v programu Microsoft Defender pro Office 365 k vyhledání shodných nebo podobných e-mailových zpráv. |
| AntispamDirection | Vytváření? | Směr této e-mailové zprávy. Možné hodnoty:<li>Neznámý<li>Příchozí<li>Odchozí<li>Intraorg (interní) |
| DeliveryAction | Vytváření? | Akce doručení této e-mailové zprávy Možné hodnoty:<li>Neznámý<li>DeliveredAsSpam<li>Doručeno<li>Blokované<li>Znění |
| DeliveryLocation | Vytváření? | Umístění pro doručování této e-mailové zprávy. Možné hodnoty:<li>Neznámý<li>Doručená pošta<li>JunkFolder<li>DeletedFolder<li>Karanténa<li>Externí<li>Neúspěšný<li>Odpojení<li>Přesměrovaná |
| Jazyk | Řetězec | Jazyk, ve kterém se zapisuje obsah e-mailu |
| ThreatDetectionMethods | &lt;Řetězec IList&gt; | Seznam metod detekce hrozeb použitých v této poště |
|

Silné identifikátory entity e-mailové zprávy:
- NetworkMessageId a příjemce

## <a name="submission-mail"></a>Odeslaná pošta

*Název entity: SubmissionMail*

| Pole | Typ | Popis |
| ----- | ---- | ----------- |
| Typ | Řetězec | 'SubmissionMail' |
| SubmissionId | Hlavních? | ID odeslání. |
| SubmissionDate | Hodnotu? | Nahlášený datum a čas pro toto odeslání. |
| Odesílatel | Řetězec | E-mailová adresa odesílatele |
| NetworkMessageId | Hlavních? | ID síťové zprávy pro e-mail, na který patří odeslání. |
| Timestamp | Hodnotu? | Časové razítko přijetí zprávy (mail). |
| Příjemce | Řetězec | Příjemce e-mailu. |
| Odesílatel | Řetězec | Odesílatel e-mailu |
| SenderIp | Řetězec | IP adresa odesilatele. |
| Předmět | Řetězec | Předmět odesílání pošty. |
| ReportType | Řetězec | Typ odeslání pro danou instanci. To se mapuje na nevyžádaný, phishing, malware nebo NotJunk. |
|

Silné identifikátory entity SubmissionMail:
- SubmissionId, odesílatel, NetworkMessageId, příjemce

## <a name="cloud-application-identifiers"></a>Identifikátory cloudových aplikací

Následující seznam definuje identifikátory pro známé cloudové aplikace. Hodnota ID aplikace se používá jako identifikátor entity [cloudové aplikace](#cloud-application) .

|ID aplikace|Name|
|------|----|
|10026|DocuSign|
|10395|Anaplan|
|10489|Box|
|10549|Cisco Webex|
|10618|Atlassian|
|10915|Cornerstone OnDemand|
|10921|Zendesk|
|10980|Okta|
|11042|Jive software|
|11114|Salesforce|
|11161|Office 365|
|11162|Microsoft OneNote Online|
|11394|Microsoft Online Services|
|11522|Yammer|
|11599|Amazon Web Services|
|11627|Dropbox|
|11713|Expensify|
|11770|G Suite|
|12005|SuccessFactors|
|12260|Microsoft Azure|
|12275|Workday|
|13843|LivePerson|
|13979|Concur|
|14509|ServiceNow|
|15570|Tableau|
|15600|Microsoft OneDrive pro firmy|
|15782|Citrix ShareFile|
|17152|Amazon|
|17865|Společnost Ariba Inc|
|18432|Zscaler|
|19688|Xactly|
|20595|Microsoft Cloud App Security|
|20892|Microsoft SharePoint Online|
|20893|Microsoft Exchange Online|
|20940|Active Directory|
|20941|Adallom CPanel|
|22110|Google Cloud Platform|
|22930|Gmail|
|23004|Životní cyklus Autodesk Fusion|
|23043|Slack|
|23233|systém Microsoft Office Online|
|25275|Microsoft Skype for Business|
|25988|Dokumenty Google|
|26055|Centrum pro správu systém Microsoft Office 365|
|26060|OPSWAT ozubené kolečky|
|26061|Microsoft Word Online|
|26062|Microsoft PowerPoint Online|
|26063|Microsoft Excel Online|
|26069|Disk Google|
|26206|Workiva|
|26311|Microsoft Dynamics|
|26318|Microsoft Azure AD|
|26320|systém Microsoft Office Sway|
|26321|Microsoft Delvu|
|26324|Microsoft Power BI|
|27548|Microsoft Forms|
|27592|Microsoft Flow|
|27593|Microsoft PowerApps|
|28353|Workplace od Facebooku|
|28373|Emulátor serveru proxy CAS|
|28375|Microsoft Teams|
|32780|Microsoft Dynamics 365|
|33626|Google|
|34127|Microsoft AppSource|
|34667|HighQ|
|35395|Microsoft Dynamics talentů|
|

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli o struktuře, identifikátorech a schématu entit v Azure Sentinel.

Přečtěte si další informace o [entitách](entities-in-azure-sentinel.md) a [mapování entit](map-data-fields-to-entities.md). 
