---
title: Klíč CEF (Common Event Format) a mapování polí CommonSecurityLog
description: Tento článek mapuje CEF klíče k odpovídajícím názvům polí v CommonSecurityLog ve službě Azure Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.date: 04/12/2021
ms.openlocfilehash: 1670d1bb291e30295018146f2a24c5282feac6e7
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311647"
---
# <a name="cef-and-commonsecuritylog-field-mapping"></a>Mapování polí CEF a CommonSecurityLog

Následující tabulky mapují názvy polí CEF (Common Event Format) na názvy používané v CommonSecurityLog Sentinel Azure a můžou být užitečné při práci s CEF zdrojem dat v Azure Sentinel.

Další informace najdete v tématu [připojení externího řešení pomocí běžných formátů událostí](connect-common-event-format.md).

## <a name="a---c"></a>A–C

|Název klíče CEF  |Název pole CommonSecurityLog  |Description  |
|---------|---------|---------|
| usnáší    |    <a name="deviceaction"></a> DeviceAction     |  Akce zmíněná v události       |
|   aplikace  |    ApplicationProtocol     |  Protokol použitý v aplikaci, například HTTP, HTTPS, SSHv2, Telnet, POP, IMPA, IMAPs atd.   |
| CNT    |    EventCount     |  Počet přidružený k události, který ukazuje, kolikrát byla stejná událost zjištěna.       |
| | | |

## <a name="d"></a>D

|Název klíče CEF  |Název CommonSecurityLog  |Description  |
|---------|---------|---------|
|Dodavatel zařízení     |  DeviceVendor       | Řetězec, který společně s definicemi produktů a verzí zařízení, jednoznačně identifikuje typ odesílajícího zařízení.       |
|Produkt zařízení     |   DeviceProduct      |   Řetězec, který společně s definicemi výrobce zařízení a verze, jednoznačně identifikuje typ odesílajícího zařízení.        |
|Verze zařízení     |   DeviceVersion      |      Řetězec, který společně s definicemi produktů a dodavatelů zařízení, jednoznačně identifikuje typ odesílajícího zařízení.     |
| destinationDnsDomain    | DestinationDnsDomain        |   Část plně kvalifikovaného názvu domény (FQDN) DNS.      |
| destinationServiceName | DestinationServiceName | Služba, která je cílem události. Například, `sshd`.|
| destinationTranslatedAddress | DestinationTranslatedAddress | Identifikuje přeložený cíl, na který odkazuje událost v síti IP, jako IP adresa IPv4. |
| destinationTranslatedPort | DestinationTranslatedPort | Port po překladu, jako je například brána firewall. <br>Platná čísla portů: `0` - `65535` |
| deviceDirection | <a name="communicationdirection"></a> CommunicationDirection | Jakékoli informace o směru pořízené komunikace. Platné hodnoty: <br>- `0` = Příchozí <br>- `1` = Odchozí |
| deviceDnsDomain | DeviceDnsDomain | Část domény DNS plně kvalifikovaného názvu domény (FQDN) |
|DeviceEventClassID     |   DeviceEventClassID     |   Řetězec nebo celé číslo, které slouží jako jedinečný identifikátor na typ události.      |
| deviceExternalID | DeviceExternalID | Název, který jedinečně identifikuje zařízení, které událost vygenerovalo. |
| deviceFacility | DeviceFacility | Zařízení, které událost vygenerovalo.|
| deviceInboundInterface | DeviceInboundInterface |Rozhraní, na kterém paket nebo data vstoupily do zařízení.  |
| deviceNtDomain | DeviceNtDomain | Doména systému Windows adresy zařízení |
| deviceOutboundInterface | DeviceOutboundInterface |Rozhraní, na kterém paket nebo data zůstaly v zařízení. |
| devicePayloadId |DevicePayloadId |Jedinečný identifikátor datové části přidružené k události |
| deviceProcessName | ProcessName | Název procesu přidružený k události <br><br>Například v systému UNIX proces, který generuje položku syslog. |
| deviceTranslatedAddress | DeviceTranslatedAddress | Identifikuje přeloženou adresu zařízení, na kterou událost odkazuje, v síti IP. <br><br>Formát je adresa IPv4. |
| dhost |DestinationHostName | Cíl, na který událost odkazuje v síti IP.  <br>Pokud je uzel k dispozici, měl by být tento formát plně kvalifikovaný název domény přidružený k cílovému uzlu. Příkladem je `host.domain.com` nebo `host`. |
| dmac | DestinationMacAddress | Cílová adresa MAC (FQDN) |
| dntdom | DestinationNTDomain | Název domény systému Windows cílové adresy.|
| dpid | DestinationProcessId |ID cílového procesu přidruženého k události|
| dpriv | DestinationUserPrivileges | Definuje oprávnění pro cílové použití. <br>Platné hodnoty: `Admninistrator` , `User` , `Guest` |
| dproc | DestinationProcessName | Název cílového procesu události, například `telnetd` nebo `sshd.` |
| dpt | DestinationPort | Cílový port. <br>Platné hodnoty: `*0` - `65535` |
| platnou | DestinationIP | Cílová adresa IpV4, na kterou událost odkazuje v síti IP. |
| dtz | DeviceTimeZon | Časové pásmo zařízení, které událost vygenerovalo |
| DUID |DestinationUserId | Identifikuje cílového uživatele podle ID. |
| duser | DestinationUserName |Identifikuje cílového uživatele podle názvu.|
| Souběžná | DeviceAddress | Adresa IPv4 zařízení, které událost vygenerovala. |
| dvchost | DeviceName | Plně kvalifikovaný název domény přidružený k uzlu zařízení, když je uzel k dispozici. Příkladem je `host.domain.com` nebo `host`.| 
| dvcmac | DeviceMacAddress | Adresa MAC zařízení, které událost vygenerovala. |
| dvcpid | ID procesu | Definuje ID procesu na zařízení, které událost vygenerovalo. |

## <a name="e---i"></a>E-I

|Název klíče CEF  |Název CommonSecurityLog  |Description  |
|---------|---------|---------|
|end     |  EndTime       | Čas, kdy aktivita související s událostí skončila.        |
|externalId    |   ExternalID      | ID, které používá zdrojové zařízení. Obvykle tyto hodnoty zvyšují hodnoty, které jsou přidruženy k události.        |
|fileCreateTime     |  FileCreateTime      | Čas, kdy byl soubor vytvořen.        |
|Hash – hodnota     |   FileHash      |   Hodnota hash souboru      |
|Identifikátor     |   Identifikátor      |ID přidružené k souboru, například inode.         |
| fileModificationTime | FileModificationTime |Čas poslední změny souboru |
| filePath | FilePath | Úplná cesta k souboru, včetně názvu souboru Například: `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` nebo `/usr/bin/zip` .|
| Oprávnění k |Oprávnění k |Oprávnění souboru. |
| fileType | FileType | Typ souboru, jako je například kanál, soket atd.|
|fname | Bitmap| Název souboru bez cesty. |
| fsize | Velikost souboru | Velikost souboru |
|Hostitel    |  Počítač       | Hostitel, od syslogu        |
|dovnitř     |  ReceivedBytes      |Počet bajtů přenesených na příchozí.         |
| | | |

## <a name="m---p"></a>M-P

|Název klíče CEF  |Název CommonSecurityLog  |Description  |
|---------|---------|---------|
|msg   |  Zpráva       | Zpráva, která poskytuje další podrobnosti o události.        |
|Name     |  Aktivita       |   Řetězec, který představuje uživatelsky čitelný a srozumitelný popis události.     |
|oldFileCreateTime     |  OldFileCreateTime       | Čas, kdy byl starý soubor vytvořen.        |
|oldFileHash     |   OldFileHash      |   Hodnota hash starého souboru      |
|oldFileId     |   OldFileId     |   A ID přidružené ke starému souboru, například inode.      |
| oldFileModificationTime | OldFileModificationTime |Čas poslední změny starého souboru |
| oldFileName |  OldFileName |Název starého souboru |
| oldFilePath | OldFilePath | Úplná cesta ke starému souboru, včetně názvu souboru. <br>Příkladem je `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` nebo `/usr/bin/zip`.|
| oldFilePermission | OldFilePermission |Oprávnění starého souboru. |
|oldFileSize | OldFileSize | Velikost starého souboru|
| oldFileType | OldFileType | Typ souboru starého souboru, například kanál, soket atd.|
| ven | SentBytes | Počet bajtů přenesených na odchozí. |
| Výsledek | Výsledek | Výsledek události, například `success` nebo `failure` .|
|proto    |  Protokol       | Transportní protokol, který identifikuje použitý protokol vrstvy 4. <br><br>Možné hodnoty zahrnují názvy protokolů, například `TCP` nebo `UDP` .        |
| | | |

## <a name="r---t"></a>R-T

|Název klíče CEF  |Název CommonSecurityLog  |Description  |
|---------|---------|---------|
|Důvod     |  Důvod      |Důvod, proč byla vygenerována událost auditu. <br><br>Příkladem je `Bad password` nebo `Unknown user`.         |
|Žádost     |   RequestURL      | Adresa URL, na kterou se přistupoval požadavek HTTP, včetně protokolu. Například `http://www/secure.com`.        |
|requestClientApplication     |   RequestClientApplication      |   Uživatelský agent přidružený k žádosti      |
| Třída requestContext | Třída requestContext | Popisuje obsah, ze kterého pochází požadavek, jako je například odkazující HTTP. |
| requestCookies | RequestCookies |Soubory cookie přidružené k žádosti |
| requestMethod | RequestMethod | Metoda použitá pro přístup k adrese URL <br><br>Platné hodnoty zahrnují metody, jako například `POST` , `GET` a tak dále. |
| RT | ReceiptTime | Čas, kdy byla přijata událost související s aktivitou. |
|Závažnost     |  <a name="logseverity"></a> LogSeverity       |  Řetězec nebo celé číslo, které popisuje důležitost události.<br><br> Platné řetězcové hodnoty: `Unknown` , `Low` , `Medium` , `High` , `Very-High` <br><br>Platné celočíselné hodnoty jsou:<br> - `0`-`3` = Nízká <br>- `4`-`6` = Střední<br>- `7`-`8` = Vysoká<br>- `9`-`10` = Very-High |
| shost    | SourceHostName        |Určuje zdroj, na který událost odkazuje v síti IP. Pokud je uzel dostupný, musí být ve formátu plně kvalifikovaný název domény (DQDN) přidružený ke zdrojovému uzlu. Příkladem je `host` nebo `host.domain.com`. |
| smac | SourceMacAddress | Adresa MAC zdroje. |
| sntdom | SourceNTDomain | Název domény systému Windows pro zdrojovou adresu. |
| sourceDnsDomain | SourceDnsDomain | Část domény DNS úplného plně kvalifikovaného názvu domény. |
| sourceServiceName | SourceServiceName | Služba odpovědná za generování události. |
| sourceTranslatedAddress | SourceTranslatedAddress | Identifikuje přeložený zdroj, na který událost odkazuje v síti IP. |
| sourceTranslatedPort | SourceTranslatedPort | Zdrojový port po překladu, jako je například brána firewall. <br>Platná čísla portů jsou `0`  -  `65535` . |
| SPID | SourceProcessId | ID zdrojového procesu přidruženého k události|
| spriv | SourceUserPrivileges | Oprávnění zdrojového uživatele. <br><br>Platné hodnoty jsou: `Administrator` , `User` , `Guest` |
| sproc | SourceProcessName | Název procesu zdroje události.|
| spt | SourcePort | Číslo zdrojového portu <br>Platná čísla portů jsou `0`  -  `65535` . |
| src | SourceIP |Zdroj, na který událost odkazuje v síti IP, jako adresu IPv4. |
| start | StartTime | Čas, kdy aktivita, na kterou událost odkazuje, je zahájena. |
| suid | SourceUserID | Identifikuje zdrojového uživatele podle ID. |
| suser | SourceUserName | Identifikuje zdrojového uživatele podle názvu. |
| typ | Typ události | Typ události Hodnoty hodnot zahrnují: <br>- `0`: základní událost <br>- `1`: agregované <br>- `2`: korelační událost <br>- `3`: událost akce <br><br>**Poznámka**: Tato událost může být pro základní události vynechána. |
| | | |

## <a name="custom-fields"></a>Vlastní pole

V následujících tabulkách jsou namapovány názvy klíčů CEF a polí CommonSecurityLog, která jsou zákazníkům k dispozici pro použití pro data, která se nevztahují na žádná z vestavěných polí.

### <a name="custom-ipv6-address-fields"></a>Vlastní pole IPv6 adresy

Následující tabulka mapuje CEF klíče a CommonSecurityLog názvy pro pole *IPv6* adres, která jsou k dispozici pro vlastní data.

|Název klíče CEF  |Název CommonSecurityLog  |
|---------|---------|
|     c6a1    |     DeviceCustomIPv6Address1       |
|     c6a1Label    |     DeviceCustomIPv6Address1Label    |
|     c6a2    |     DeviceCustomIPv6Address2    |
|     c6a2Label    |     DeviceCustomIPv6Address2Label    |
|     c6a3    |     DeviceCustomIPv6Address3    |
|     c6a3Label    |     DeviceCustomIPv6Address3Label    |
|     c6a4    |     DeviceCustomIPv6Address4    |
|     c6a4Label    |     DeviceCustomIPv6Address4Label    |
|     cfp1    |     DeviceCustomFloatingPoint1    |
|     cfp1Label    |     deviceCustomFloatingPoint1Label    |
|     cfp2    |     DeviceCustomFloatingPoint2    |
|     cfp2Label    |     deviceCustomFloatingPoint2Label    |
|     cfp3    |     DeviceCustomFloatingPoint3    |
|     cfp3Label    |     deviceCustomFloatingPoint3Label    |
|     cfp4    |     DeviceCustomFloatingPoint4    |
|     cfp4Label    |     deviceCustomFloatingPoint4Label    |
| | |

### <a name="custom-number-fields"></a>Vlastní číselná pole

Následující tabulka mapuje CEF klíče a CommonSecurityLog názvy *pro pole, která jsou* k dispozici pro vlastní data.

|Název klíče CEF  |Název CommonSecurityLog  |
|---------|---------|
|     CN1    |     DeviceCustomNumber1       |
|     cn1Label    |     DeviceCustomNumber1Label       |
|     CN2    |     DeviceCustomNumber2       |
|     cn2Label    |     DeviceCustomNumber2Label       |
|     cn3    |     DeviceCustomNumber3       |
|     cn3Label    |     DeviceCustomNumber3Label       |
| | |

### <a name="custom-string-fields"></a>Pole vlastních řetězců

Následující tabulka mapuje CEF klíče a CommonSecurityLog názvy pro pole *řetězců* , která jsou k dispozici pro vlastní data.

|Název klíče CEF  |Název CommonSecurityLog  |
|---------|---------|
|     CS1    |     DeviceCustomString1 <sup> [1](#use-sparingly)</sup>    |
|     cs1Label    |     DeviceCustomString1Label <sup> [1](#use-sparingly)</sup>    |
|     CS2    |     DeviceCustomString2 <sup> [1](#use-sparingly)</sup>   |
|     cs2Label    |     DeviceCustomString2Label <sup> [1](#use-sparingly)</sup> |
|     3    |     DeviceCustomString3 <sup> [1](#use-sparingly)</sup>  |
|     cs3Label    |     DeviceCustomString3Label <sup> [1](#use-sparingly)</sup> |
|     CS4    |     DeviceCustomString4 <sup> [1](#use-sparingly)</sup> |
|     cs4Label    |     DeviceCustomString4Label <sup> [1](#use-sparingly)</sup>  |
|     cs5    |     DeviceCustomString5 <sup> [1](#use-sparingly)</sup>   |
|     cs5Label    |     DeviceCustomString5Label <sup> [1](#use-sparingly)</sup>    |
|     cs6    |     DeviceCustomString6 <sup> [1](#use-sparingly)</sup> |
|     cs6Label    |     DeviceCustomString6Label <sup> [1](#use-sparingly)</sup> |
|     flexString1    |     FlexString1    |
|     flexString1Label    |     FlexString1Label    |
|     flexString2    |     FlexString2    |
|     flexString2Label    |     FlexString2Label    |
| | |

> [!TIP]
> <a name="use-sparingly"></a><sup>1</sup> Pokud je to možné, doporučujeme používat pole **DeviceCustomStringně** a použít konkrétnější a Vestavěná pole.
> 

### <a name="custom-timestamp-fields"></a>Vlastní pole časových razítek

Následující tabulka mapuje CEF klíče a CommonSecurityLog názvy pro pole *časového razítka* , která jsou k dispozici pro vlastní data.

|Název klíče CEF  |Název CommonSecurityLog  |
|---------|---------|
|     deviceCustomDate1    |     DeviceCustomDate1    |
|     deviceCustomDate1Label    |     DeviceCustomDate1Label    |
|     deviceCustomDate2       |     DeviceCustomDate2    |
|     deviceCustomDate2Label    |     DeviceCustomDate2Label    |
|     flexDate1    |     FlexDate1    |
|     flexDate1Label    |     FlexDate1Label    |
| | |

### <a name="custom-integer-data-fields"></a>Vlastní celočíselná datová pole

Následující tabulka mapuje CEF klíče a CommonSecurityLog názvy pro pole s *celými čísly* , která jsou k dispozici pro vlastní data.

|Název klíče CEF  |Název CommonSecurityLog  |
|---------|---------|
|     flexNumber1    |     FlexNumber1    |
|     flexNumber1Label    |     FlexNumber1Label    |
|     flexNumber2    |     FlexNumber2    |
|     flexNumber2Label    |     FlexNumber2Label    |
| | |

## <a name="enrichment-fields"></a>Pole obohacení

Následující pole **CommonSecurityLog** jsou přidána pomocí funkce Azure Sentinel pro rozšíření původních událostí přijatých ze zdrojových zařízení a nemají mapování v CEF klíčích:

### <a name="threat-intelligence-fields"></a>Pole analýzy hrozeb

|Název pole CommonSecurityLog  |Description  |
|---------|---------|
|   **IndicatorThreatType**  |  Typ hrozby [MaliciousIP](#MaliciousIP) podle kanálu analýzy hrozeb.       |
| <a name="MaliciousIP"></a>**MaliciousIP** | Zobrazí seznam všech IP adres ve zprávě, která se koreluje s aktuálním kanálem pro analýzu hrozeb. |
|  **MaliciousIPCountry**   | [MaliciousIP](#MaliciousIP) země podle geografických údajů v době ingestování záznamů.        |
| **MaliciousIPLatitude**    |   [MaliciousIP](#MaliciousIP) Zeměpisná délka podle geografických údajů v době ingestování záznamů.      |
| **MaliciousIPLongitude**    |  [MaliciousIP](#MaliciousIP) Zeměpisná délka podle geografických údajů v době ingestování záznamů.       |
| **ReportReferenceLink**    |    Odkaz na sestavu analýzy hrozeb     |
|  **ThreatConfidence**   |   Spolehlivost hrozby [MaliciousIP](#MaliciousIP) podle kanálu analýzy hrozeb.      |
| **ThreatDescription**    |   Popis hrozby [MaliciousIP](#MaliciousIP) podle informačního kanálu analýzy hrozeb.      |
| **ThreatSeverity** | Závažnost hrozby pro [MaliciousIP](#MaliciousIP)podle kanálu analýzy hrozeb v době ingestování záznamů. |
|     |         |

### <a name="additional-enrichment-fields"></a>Další pole pro obohacení

|Název pole CommonSecurityLog  |Description  |
|---------|---------|
|**OriginalLogSeverity**     |  Vždy prázdné, podporováno pro integraci s CiscoASA. <br>Podrobnosti o hodnotách závažnosti protokolu najdete v poli [LogSeverity](#logseverity) .       |
|**RemoteIP**     |     Vzdálená IP adresa. <br>Tato hodnota je založená na poli [CommunicationDirection](#communicationdirection) , pokud je to možné.     |
|**RemotePort**     |   Vzdálený port. <br>Tato hodnota je založená na poli [CommunicationDirection](#communicationdirection) , pokud je to možné.      |
|**SimplifiedDeviceAction**     |   Zjednodušuje hodnotu [DeviceAction](#deviceaction) na statickou sadu hodnot a přitom udržuje původní hodnotu v poli [DeviceAction](#deviceaction) . <br>Například: `Denied`  >  `Deny` .      |
|**SourceSystem**     | Vždy definováno jako **OpsManager**.        |
|     |         |

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [připojení externího řešení pomocí běžných formátů událostí](connect-common-event-format.md).
