---
title: Požadavky na systém Microsoft Azure StorSimple Virtual Array | Dokumentace Microsoftu
description: Další informace o softwaru a síťové požadavky pro StorSimple Virtual Array
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: alkohli
ms.openlocfilehash: 7e5cf79613bdbd62427e99a0d1f2aa29ed8f85be
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245187"
---
# <a name="storsimple-virtual-array-system-requirements"></a>Požadavky systému virtuálních polí StorSimple
## <a name="overview"></a>Přehled
Tento článek popisuje důležité systémové požadavky pro Microsoft Azure StorSimple Virtual Array a pro přístup k poli úložiště klienty. Doporučujeme, abyste si informace o pečlivě před nasazení systému StorSimple a pak zpátky na ni odkazovat podle potřeby během nasazení a následná operace.

Systémové požadavky:

* **Požadavky na software pro klienty úložiště** -popisuje podporovaný virtualizační platformy, webové prohlížeče, iniciátory iSCSI, SMB klienty, požadavky na minimální virtuální zařízení a veškeré další požadavky pro tyto operační systémy.
* **Požadavky na síť pro zařízení StorSimple** – poskytuje informace o portech, které musí být otevřené v bráně firewall povolit pro přenosy iSCSI, cloud nebo správy.

Informace požadavky systému StorSimple publikované v tomto článku se vztahují pouze na virtuálních polí StorSimple.

* Pro zařízení 8000 series, přejděte na [požadavky na systém pro vaše zařízení StorSimple řady 8000](storsimple-system-requirements.md).
* Zařízení řady 7000 najdete [požadavky na systém pro vaše zařízení řady StorSimple 5000 – 7000](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Požadavky na software
Požadavky na software také informace o podporovaných webových prohlížečů, verze protokolu SMB, virtualizačních platforem a požadavky na minimální virtuální zařízení.

### <a name="supported-virtualization-platforms"></a>Podporovaný virtualizační platformy
| **Hypervisor** | **Verze** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 a novější |
| VMware ESXi |5.0, 5.5, 6.0 nebo 6.5. |

> [!IMPORTANT]
> Neinstalujte na StorSimple Virtual Array; nástroje VMware Výsledkem bude má nepodporovanou konfiguraci.

### <a name="virtual-device-requirements"></a>Požadavky na virtuální zařízení
| **Komponenta** | **Požadavek** |
| --- | --- |
| Minimální počet virtuálních procesorů (jader) |4 |
| Minimální velikost paměti (RAM) |8 GB <br> Pro souborový server, 8 GB pro soubory menší než 2 miliony a 16 GB 2 – 4 miliony souborů|
| Volné místo na disku<sup>1</sup> |Disk s operačním systémem - 80 GB <br></br>Datový disk - 500 GB až 8 TB |
| Minimální počet nejmíň jedno síťové rozhraní |1 |
| Internetovou šířku pásma<sup>2</sup> |Minimální šířka pásma vyžadovaná: 5 MB/s <br> Doporučená šířka pásma: 100 Mb/s <br> Rychlost přenosu dat se dál škáluje s Internetovou šířku pásma. Třeba 100 GB dat trvá 2 dny pro přenos na 5 MB/s, což může vést k selhání zálohování, protože nebylo dokončeno denní zálohy za den. S šířkou pásma 100 MB/s lze přenést 100 GB dat za 2,5 hodin.   |

<sup>1</sup> – dynamického zajišťování zřízené

<sup>2</sup> – požadavky na síť může lišit v závislosti na denní četnost změn dat. Například pokud zařízení potřebuje k zálohování 10 GB nebo více změn během dne, pak denní zálohování přes 5 MB/s připojení může trvat až 4,25 hodin (Pokud data nelze komprimované nebo s odstraněním duplicitních dat).

### <a name="supported-web-browsers"></a>Podporované webové prohlížeče
| **Komponenta** | **Verze** | **Další požadavky a poznámky** |
| --- | --- | --- |
| Microsoft Edge |Nejnovější verze | |
| Internet Explorer |Nejnovější verze |Testovat pomocí aplikace Internet Explorer 11 |
| Google Chrome |Nejnovější verze |Testování s prohlížečem Chrome 46 |

### <a name="supported-storage-clients"></a>Úložiště podporuje klienty
Následující softwarové požadavky jsou určené pro iniciátory iSCSI, které přistupují k StorSimple Virtual Array (nakonfigurovaný jako iSCSI server).

| **Podporované operační systémy** | **Požadovaná verze** | **Další požadavky a poznámky** |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2 |StorSimple můžete vytvářet dynamicky zajištěné a zcela zřizované svazky. Ho nelze vytvářet částečně zřizované svazky. ISCSI svazky zařízení StorSimple jsou podporovaná jenom pro: <ul><li>Jednoduché svazky ve Windows běžné disky.</li><li>Windows: pro formátování svazku systému souborů NTFS.</li> |

Následující softwarové požadavky jsou pro klienty SMB, které přistupují k StorSimple Virtual Array (nakonfigurovali jako souborový server).

| **Verze protokolu SMB** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

> [!IMPORTANT]
> Při kopírování nebo ukládat soubory chráněné službou Windows souboru systému souborů EFS k souborovému serveru StorSimple Virtual Array; Výsledkem bude má nepodporovanou konfiguraci.


### <a name="supported-storage-format"></a>Nepodporuje formát úložiště
Je podporován pouze úložiště objektů blob bloku Azure. Objekty BLOB stránky nejsou podporovány. Další informace o [o objekty BLOB bloku a objekty BLOB stránky](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Požadavky na síť
Následující tabulka uvádí porty, které je potřeba otevřít v bráně firewall povolit pro iSCSI, SMB, cloud nebo přenos pro správu. V této tabulce *v* nebo *příchozí* odkazuje na směru, ze kterého příchozí požadavky klientů, přístup k vašemu zařízení. *Navýšení kapacity* nebo *odchozí* odkazuje na směru, ve kterém zařízení StorSimple, odesílá data externě, nad rámec nasazení: pro příklad, odchozí k Internetu.

| **Číslo portu<sup>1</sup>** | **Snížení nebo navýšení kapacity** | **Rozsah portů** | **Požadováno** | **Poznámky** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Výstup |WAN |Ne |Odchozí port se používá pro přístup k Internetu pro načtení aktualizací. <br></br>Odchozí webový proxy server je konfigurovatelná uživatelem. |
| TCP 443 (HTTPS) |Výstup |WAN |Ano |Odchozí port se používá pro přístup k datům v cloudu. <br></br>Odchozí webový proxy server je konfigurovatelná uživatelem. |
| UDP 53 (DNS) |Výstup |WAN |V některých případech; v části poznámky. |Tento port je povinný, jenom v případě, že používáte server služby Internetová DNS. <br></br> Všimněte si, že pokud nasazení souborového serveru, doporučujeme použít místní server DNS. |
| UDP 123 (NTP) |Výstup |WAN |V některých případech; v části poznámky. |Tento port je povinný, jenom v případě, že používáte server služby Internetová NTP.<br></br> Všimněte si, že pokud nasazení souborového serveru, doporučujeme, abyste synchronizace času se řadiče domény služby Active Directory. |
| TCP 80 (HTTP) |V |LAN |Ano |Toto je příchozí port pro místní uživatelské rozhraní na zařízení StorSimple pro místní správu. <br></br> Všimněte si, že přístup k místním uživatelského rozhraní pomocí protokolu HTTP bude automaticky přesměrovat na protokol HTTPS. |
| TCP 443 (HTTPS) |V |LAN |Ano |Toto je příchozí port pro místní uživatelské rozhraní na zařízení StorSimple pro místní správu. |
| 3260 TCP (iSCSI) |V |LAN |Ne |Tento port se používá pro přístup k datům přes iSCSI. |

<sup>1</sup> žádné příchozí porty je potřeba otevřít na veřejném Internetu.

> [!IMPORTANT]
> Ujistěte se, že brána firewall neupravuje nebo dešifrovat veškerý provoz protokolu SSL mezi zařízení StorSimple a Azure.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>Vzory adres URL pro pravidla brány firewall
Správci sítě můžou často konfigurace pravidla brány firewall na Upřesnit na základě vzory adres URL pro filtrování příchozího a odchozího provozu. Vaše virtuální pole a ve službě Správce zařízení StorSimple závisí na jiné aplikace od Microsoftu, jako je Azure Service Bus, Azure Active Directory Access Control, účty úložiště a serverům Microsoft Update. Vzory adres URL, které jsou přidružené k těmto aplikacím je možné nakonfigurovat pravidla brány firewall. Je důležité pochopit, že můžete změnit vzory adres URL, které jsou přidružené k těmto aplikacím. To zase vyžaduje správce sítě, monitorovat a aktualizovat pravidla brány firewall pro StorSimple jako a v případě potřeby. 

Doporučujeme nastavit pravidla brány firewall pro odchozí provoz, podle StorSimple liberally pevné IP adresy, ve většině případů. Ale můžete použít následující informace pro nastavení pravidla brány firewall na pokročilé, které budete potřebovat pro vytvoření zabezpečeného prostředí.

> [!NOTE]
> 
> * Zařízení (zdroj) IP adresy musí být vždy nastavená na všechna rozhraní sítě povolenou podporu cloudu. 
> * Cílové IP adresy musí být nastavená na [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).
> 
> 

| Vzor adresy URL | Komponenta nebo funkce |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|Služba Správce zařízení StorSimple<br>Access Control Service<br>Azure Service Bus<br>Ověřovací služba|
| `http://*.backup.windowsazure.com` |Registrace zařízení |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |Odvolání certifikátu |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Účty úložiště Azure a monitorování |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Servery Microsoft Update<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |Balíček pro podporu |
| `http://*.data.microsoft.com ` |Telemetrické službě ve Windows, najdete v článku [aktualizace pro uživatelské prostředí a telemetrická data diagnostiky](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>Další postup
* [Příprava portálu nasazení StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md)
