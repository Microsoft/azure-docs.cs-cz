---
title: Požadavky na systém Microsoft Azure StorSimple virtuální pole | Microsoft Docs
description: Další informace o softwaru a síťové požadavky pro vaše pole virtuální zařízení StorSimple
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
ms.date: 11/14/2017
ms.author: alkohli
ms.openlocfilehash: 5d01523f326bd7e2518bff06e62ae62db8f318d3
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2017
ms.locfileid: "24815224"
---
# <a name="storsimple-virtual-array-system-requirements"></a>Požadavky systému virtuálních polí StorSimple
## <a name="overview"></a>Přehled
Tento článek popisuje důležité systémové požadavky pro vaše virtuální pole Microsoft Azure StorSimple a klienty úložiště přístup k poli. Doporučujeme, abyste si prošli informace pečlivě před nasazením systému StorSimple a pak zpátky na ni odkazuje podle potřeby během nasazení a další operace.

Systémové požadavky:

* **Požadavky na software pro klienty úložiště** -popisuje podporovaný virtualizační platformy, webové prohlížeče, iniciátory iSCSI, klienti protokolu SMB, požadavky na minimální virtuální zařízení a veškeré další požadavky pro tyto operační systémy.
* **Požadavky sítě pro zařízení StorSimple** – poskytuje informace o portech, které musí být otevřen v bráně firewall pro přenosy iSCSI, cloudu nebo správu povolit.

Požadavky na informace o systému StorSimple publikované v tomto článku platí pouze pro pole virtuální zařízení StorSimple.

* Pro řady 8000 zařízení, přejděte na [požadavky na systém pro vaše zařízení řady StorSimple 8000](storsimple-system-requirements.md).
* Pro řady 7000 zařízení, přejděte na [požadavky na systém pro řadu zařízení StorSimple 5000 7000](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Požadavky na software
Požadavky na software zahrnují informace o podporované webové prohlížeče, verze protokolu SMB, virtualizačních platforem a požadavky na minimální virtuální zařízení.

### <a name="supported-virtualization-platforms"></a>Podporovaný virtualizační platformy
| **Hypervisor** | **Verze** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 a novější |
| VMware ESXi |5.0, 5.5 a 6.0 <br> (verze 6.5 není podporována.) |

> [!IMPORTANT]
> Neinstalujte nástroje VMware na pole virtuální zařízení StorSimple; Tato akce způsobí nepodporované konfigurace.

### <a name="virtual-device-requirements"></a>Požadavky na virtuální zařízení
| **Komponenta** | **Požadavek** |
| --- | --- |
| Minimální počet virtuálních procesorů (jader) |4 |
| Minimální paměť (RAM) |8 GB <br> Pro souborový server, 8 GB pro soubory menší než 2 miliony a 16 GB pro soubory 2 – 4 miliony|
| Místo na disku<sup>1</sup> |Disk s operačním systémem - 80 GB <br></br>Datový disk - 500 GB až 8 TB |
| Minimální počet nejmíň jedno síťové rozhraní |1 |
| Šířky pásma Internetu<sup>2</sup> |Minimální šířka pásma požadované: 5 MB/s <br> Doporučená šířky pásma: 100 MB/s <br> Rychlost přenosu dat se dál škáluje s šířky pásma Internetu. Například 100 GB dat přebírá 2 dní pro přenos na 5 MB/s, což může vést k selhání zálohování, protože by dokončení denní zálohy za den. Šířka pásma 100 MB/s 100 GB dat lze přenést 2,5 hodin.   |

<sup>1</sup> – dynamické zřídit

<sup>2</sup> – požadavky na síť může lišit v závislosti na denní míry změny dat. Například pokud zařízení potřebuje k zálohování 10 GB nebo další změny během dne, pak denní zálohování přes 5 MB/s připojení může trvat až 4,25 hodin (Pokud data nelze komprimované nebo odstranění duplicit).

### <a name="supported-web-browsers"></a>Podporované webové prohlížeče
| **Komponenta** | **Verze** | **Další požadavky a poznámky** |
| --- | --- | --- |
| Microsoft Edge |nejnovější verzi | |
| Internet Explorer |nejnovější verzi |Testování s aplikací Internet Explorer 11 |
| Google Chrome |nejnovější verzi |Testovány s Chrome 46 |

### <a name="supported-storage-clients"></a>Klienti podporované úložiště
Následující softwarové požadavky jsou pro iniciátory iSCSI, která přistupují k pole virtuální zařízení StorSimple (nakonfigurovaný jako server se službou iSCSI).

| **Podporované operační systémy** | **Požadovaná verze** | **Další požadavky a poznámky** |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012 R2 |StorSimple můžete vytvořit dynamicky zajištěné a zcela zřizované svazky. Ji nelze vytvářet částečně zřizované svazky. Svazky zařízení StorSimple iSCSI jsou podporovány pouze pro: <ul><li>Jednoduché svazky na základní disky systému Windows.</li><li>Windows: pro formátování svazku systému souborů NTFS.</li> |

Následující softwarové požadavky jsou pro klienty SMB, kteří přistupují pole virtuální zařízení StorSimple (nakonfigurovaný jako souborový server).

| **Verze protokolu SMB** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3.02 |

> [!IMPORTANT]
> Zkopírujte nebo ukládat soubory chráněné pomocí Windows systém souborů (Encrypting File System) na souborovém serveru pole virtuální zařízení StorSimple; Tato akce způsobí nepodporované konfigurace.


### <a name="supported-storage-format"></a>Podporované formát úložiště
Je podporován pouze Azure blokové úložiště objektů blob. Objekty BLOB stránky nejsou podporovány. Další informace [o objekty BLOB bloku a objekty BLOB stránky](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Požadavky sítě
Následující tabulka uvádí porty, které je potřeba otevřít v bráně firewall povolit pro iSCSI, SMB, cloudu nebo provoz správy. V této tabulce *v* nebo *příchozí* odkazuje na směru, ve kterém příchozí žádosti klientů, přístup k zařízení. *Out* nebo *odchozí* odkazuje na směru, ve kterém zařízení StorSimple odešle data externě, kromě nasazení: například odchozí k Internetu.

| **Číslo portu<sup>1</sup>** | **Příchozí nebo odchozí** | **Rozsah portů** | **Požadované** | **Poznámky k** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |na více systémů |WAN |Ne |Odchozí port se používá pro přístup k Internetu, abyste získali aktualizace. <br></br>Odchozí webový proxy server je konfigurovatelná uživatelem. |
| TCP 443 (HTTPS) |na více systémů |WAN |Ano |Odchozí port se používá pro přístup k datům v cloudu. <br></br>Odchozí webový proxy server je konfigurovatelná uživatelem. |
| UDP 53 (DNS) |na více systémů |WAN |V některých případech; v části poznámky. |Tento port je povinný, jenom v případě, že používáte adresu serveru DNS pro internetový. <br></br> Všimněte si, že pokud nasazení souborového serveru, doporučujeme používat místní server DNS. |
| UDP 123 (NTP) |na více systémů |WAN |V některých případech; v části poznámky. |Tento port je povinný, jenom v případě, že používáte server NTP založené na Internetu.<br></br> Všimněte si, že pokud nasazení souborového serveru, doporučujeme, abyste synchronizaci času se řadiče domény služby Active Directory. |
| TCP 80 (HTTP) |V |LAN |Ano |Toto je portu pro příchozí spojení pro místní uživatelské rozhraní v zařízení StorSimple pro místní správu. <br></br> Všimněte si, že přístup k rozhraní místní přes protokol HTTP se automaticky přesměruje na HTTPS. |
| TCP 443 (HTTPS) |V |LAN |Ano |Toto je portu pro příchozí spojení pro místní uživatelské rozhraní v zařízení StorSimple pro místní správu. |
| TCP 3260 (iSCSI) |V |LAN |Ne |Tento port se používá pro přístup k datům přes iSCSI. |

<sup>1</sup> žádné příchozí porty muset otevřít na veřejného Internetu.

> [!IMPORTANT]
> Ujistěte se, zda brána firewall upravit nebo dešifrovat veškerou komunikaci SSL mezi zařízení StorSimple a Azure.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>Adresa URL vzory pro pravidla brány firewall
Správci sítě často můžete nakonfigurovat pravidla rozšířené brány firewall na základě vzorů adresy URL pro filtrování příchozí a odchozí provoz. Vaše virtuální pole a služby StorSimple Manager zařízení závisí na jiných aplikací společnosti Microsoft, například Azure Service Bus, Azure Active Directory řízení přístupu, účty úložiště a servery Microsoft Update. Adresa URL vzory související s těmito aplikacemi slouží ke konfiguraci pravidel brány firewall. Je důležité si uvědomit, že můžete změnit adresu URL vzory související s těmito aplikacemi. Správce sítě a monitorovat a aktualizovat pravidla brány firewall pro vaše zařízení StorSimple jako a v případě potřeby pak bude nutné. 

Doporučujeme vám, že nastavíte vašich pravidlech brány firewall pro odchozí přenosy, založené na StorSimple liberally pevné IP adresy, ve většině případů. Můžete však použít níže uvedené informace pravidel rozšířené brány firewall, které jsou potřebné k vytvoření zabezpečeného prostředí.

> [!NOTE]
> 
> * Zařízení (zdroj) IP adres by měl být vždy nastavená na všechna rozhraní sítě povolenou podporu cloudu. 
> * Cílové IP adresy musí být nastavena na [rozsahy IP adres Azure datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653).
> 
> 

| Vzor adresy URL | Komponenta nebo funkce |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|Služba StorSimple Manager zařízení<br>Access Control Service<br>Azure Service Bus<br>Služba ověřování|
| `http://*.backup.windowsazure.com` |Registrace zařízení |
| `http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*` |Odvolání certifikátu |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Monitorování a účty úložiště Azure |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com` |Servery Microsoft Update<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |Balíček pro podporu |
| `http://*.data.microsoft.com ` |Telemetrie služby v systému Windows, najdete v článku [aktualizace pro zkušeností zákazníků a diagnostiky telemetrii](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>Další kroky
* [Příprava na portálu pro nasazení pole virtuální zařízení StorSimple](storsimple-virtual-array-deploy1-portal-prep.md)
