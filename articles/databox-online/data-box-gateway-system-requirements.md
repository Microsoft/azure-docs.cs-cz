---
title: Požadavky na systém Microsoft Azure Data Box Gateway | Dokumentace Microsoftu
description: Další informace o softwaru a požadavky na síť pro bránu Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/17/2018
ms.author: alkohli
ms.openlocfilehash: 6978360769ba350d85a06116bfee6db9f80b0607
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50157356"
---
# <a name="azure-data-box-gateway-system-requirements-preview"></a>Azure Data Box brány požadavky na systém (Preview)

Tento článek popisuje důležité systémové požadavky pro vaše řešení Microsoft Azure Data Box brány a klienti připojení k Azure Data Box brány. Doporučujeme, abyste si informace o pečlivě před nasazení brány dat pole a pak zpátky na ni odkazovat podle potřeby během nasazení a následná operace.

Požadavky na systém pro virtuální zařízení Data Box brány patří:

- **Požadavky na software pro hostitele** -popisuje podporované platformy, prohlížeče pro místní konfiguraci, uživatelského rozhraní, klienti SMB a veškeré další požadavky pro hostitele, které se připojují k zařízení.
- **Požadavky na síť pro zařízení** – poskytuje informace o všech požadavků na síť pro operaci virtuálního zařízení.

> [!IMPORTANT]
> Data Box Gateway je ve verzi Preview. Před nasazením tohoto řešení si prosím přečtěte [podmínky použití verze Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## <a name="supported-os-for-clients-connected-to-device"></a>Podporovaný operační systém pro klienty připojené k zařízení

Tady je seznam podporovaných operačních systémů pro klienty nebo hostitele, které jsou připojeny k bráně Data Box.

| **Operačního systému a platformy** | **Verze** |
| --- | --- |
| Windows Server |2012 R2 <br> 2016 |
| Windows |8, 10 |
| SUSE Linux |Podnikový Server 12 (x86_64)|
| Ubuntu |16.04.3 LTS|
| CentOS | 7.0 |

## <a name="supported-protocols-for-clients-accessing-device"></a>Podporované protokoly klientům přístup k zařízení

|**Protokol** |**Verze**   |**Poznámky**  |
|---------|---------|---------|
|SMB    | 2.X 3.X      | Nepodporuje se SMB 1.|
|Systém souborů NFS     | V3 a V4        |         |

## <a name="supported-virtualization-platforms-for-device"></a>Podporovaný virtualizační platformy pro zařízení

| **Operačního systému a platformy**  |**Verze**   |**Poznámky**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016  |         |
|VMware ESXi     | 6.0 <br> 6.5        |Nástroje VMware nejsou podporovány.         |


## <a name="supported-storage-accounts"></a>Účty úložiště podporuje

Tady je seznam účtů úložiště podporuje pro bránu dat pole.

| **Účet úložiště** | **Poznámky** |
| --- | --- |
| Classic | Standard |
| Obecné použití  |Standardní; verze V1 i V2 podporují. Vrstvy hot a cool nejsou podporovány. |


## <a name="supported-storage-types"></a>Typy podporovaných úložišť

Tady je seznam typů úložiště podporuje pro bránu dat pole.

| **Formát souboru** | **Poznámky** |
| --- | --- |
| Objekt blob bloku Azure | |
| Objekt blob stránky Azure  | |
| Soubory Azure | |

## <a name="supported-browsers-for-local-web-ui"></a>Podporované prohlížeče pro místního webového uživatelského rozhraní

Tady je seznam prohlížečů podporovaných pro místního webového uživatelského rozhraní pro virtuální zařízení.

|Prohlížeč  |Verze  |Další požadavky a poznámky  |
|---------|---------|---------|
|Google Chrome   |Nejnovější verze         |         |
|Microsoft Edge    | Nejnovější verze        |         |
|Internet Explorer     | Nejnovější verze        |         |
|FireFox    |Nejnovější verze         |         |


## <a name="networking-requirements"></a>Požadavky na síť

Následující tabulka uvádí porty, které je potřeba otevřít v bráně firewall povolit pro protokol SMB, cloud nebo přenos pro správu. V této tabulce *v* nebo *příchozí* odkazuje na směru, které mají přístup příchozí požadavky klienta na vaše zařízení. *Navýšení kapacity* nebo *odchozí* odkazuje na směru, ve kterém zařízení Data Box brány odesílá data externě, nad rámec nasazení: pro příklad, odchozí k Internetu.

| Port č.| Snížení nebo navýšení kapacity | Rozsah portů| Požaduje se|   Poznámky                                                             |                                                                                     |
|--------|---------|----------|--------------|----------------------|---------------|
| TCP 80 (HTTP)|Výstup|WAN |Ne|Odchozí port se používá pro přístup k Internetu pro načtení aktualizací. <br>Odchozí webový proxy server je konfigurovatelná uživatelem. |                          
| TCP 443 (HTTPS)|Výstup|WAN|Ano|Odchozí port se používá pro přístup k datům v cloudu.<br>Odchozí webový proxy server je konfigurovatelná uživatelem.|   
| UDP 53 (DNS)|Výstup|WAN|V některých případech<br>Viz poznámky|Tento port je povinný, jenom v případě, že používáte server služby Internetová DNS.<br>Doporučujeme používat místní server DNS. |
| UDP 123 (NTP)|Výstup|WAN|V některých případech<br>Viz poznámky|Tento port je povinný, jenom v případě, že používáte server služby Internetová NTP.  |
| UDP 67 (DHCP)|Výstup|WAN|V některých případech<br>Viz poznámky|Tento port je požadován, pouze pokud používáte DHCP server.  |
| TCP 80 (HTTP)|V|LAN|Ano|Toto je příchozí port pro místní uživatelské rozhraní na zařízení pro místní správu. <br>Přístup k místním uživatelského rozhraní pomocí protokolu HTTP bude automaticky přesměrovat na protokol HTTPS.  | 
| TCP 443 (HTTPS)|V|LAN|Ano|Toto je příchozí port pro místní uživatelské rozhraní na zařízení pro místní správu. | 

## <a name="url-patterns-for-firewall-rules"></a>Vzory adres URL pro pravidla brány firewall

Správci sítě můžou často konfigurace pravidla brány firewall na Upřesnit na základě vzory adres URL pro filtrování příchozího a odchozího provozu. Vaše zařízení Data Box brány a služba brány dat pole závisí na jiné aplikace od Microsoftu, jako je Azure Service Bus, Azure Active Directory Access Control, účty úložiště a serverům Microsoft Update. Vzory adres URL, které jsou přidružené k těmto aplikacím je možné nakonfigurovat pravidla brány firewall. Je důležité pochopit, že můžete změnit vzory adres URL, které jsou přidružené k těmto aplikacím. To zase vyžaduje správce sítě, monitorovat a aktualizovat pravidla brány firewall pro vaši bránu Data pole jako a v případě potřeby.

Doporučujeme nastavit pravidla brány firewall pro odchozí provoz, podle pole brána dat liberally pevné IP adresy, ve většině případů. Ale můžete použít následující informace pro nastavení pravidla brány firewall na pokročilé, které budete potřebovat pro vytvoření zabezpečeného prostředí.

> [!NOTE]
> - Zařízení (zdroj) IP adresy musí být vždy nastavená na všechna rozhraní sítě povolenou podporu cloudu.
> - Cílové IP adresy musí být nastavená na [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

|     Vzor adresy URL                                                                                                                                                                                                                                                                                                                                                                                                                                       |     Komponenta nebo funkce                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
|    https://*.databoxedge.azure.com/*<br>https://*.servicebus.windows.net/*<br>https://login.windows.net                                                                                                                                                                                                                                                                                                        |    Služba Azure Data Box brány<br>Azure Service Bus<br>Ověřovací služba    |
|    http://*.Backup.windowsazure.com                                                                                                                                                                                                                                                                                                                                                                                                                   |    Aktivace zařízení                                                                                    |
|    http://crl.microsoft.com/pki/*   http://www.microsoft.com/pki/*                                                                                                                                                                                                                                                                                                                                                                                    |    Odvolání certifikátu                                                                               |
|    https://*.core.windows.net/* https://*. data.microsoft.com http://*. msftncsi.com                                                                                                                                                                                                                                                                                                                                                                |    Účty úložiště Azure a monitorování                                                                |
|    http://windowsupdate.microsoft.com<br>http://*. windowsupdate.microsoft.com<br>https://*. windowsupdate.microsoft.com<br>http://*. update.microsoft.com<br>https://*. update.microsoft.com<br>http://*. windowsupdate.com<br>http://download.microsoft.com<br>http://*. download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://*. ws.microsoft.com<br>https://*. ws.microsoft.com<br>http://*.MP.microsoft.com        |    Servery Microsoft Update                                                                             |
|    http://*.deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                             |    Akamai CDN                                                                                           |
|    https://*.partners.extranet.microsoft.com/*                                                                                                                                                                                                                                                                                                                                                                                                        |    Balíček pro podporu                                                                                      |
|    http://*.data.microsoft.com                                                                                                                                                                                                                                                                                                                                                                                                                        |    Telemetrické službě ve Windows, najdete v článku aktualizace pro uživatelské prostředí a telemetrická data diagnostiky      |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                                                                                                         |



## <a name="internet-bandwidth"></a>Šířka pásma sítě Internet

Následující požadavky platí pro minimální šířka pásma sítě Internet k dispozici pro zařízení Data Box brány.

- Vaše služba Data Box Gateway má vyhrazenou šířku internetového pásma 20 Mb/s (nebo více), která je vždy dostupná. Tato šířka pásma by se neměla sdílet s žádnými jinými aplikacemi. 
- Vaše brána dat pole má vyhrazenou šířku pásma 32 MB/s sítě Internet (nebo více) při použití omezení využití sítě.

## <a name="next-step"></a>Další krok

* [Nasazení brány Azure Data Box](data-box-gateway-deploy-prep.md)

