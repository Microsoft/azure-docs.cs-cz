---
title: Požadavky na systém Microsoft Azure StorSimple Virtual Array
description: Informace o softwaru a síťových požadavcích na vaše virtuální pole StorSimple
author: alkohli
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 020208a8b67d248c02fc659d4dc48fa22d333839
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298809"
---
# <a name="storsimple-virtual-array-system-requirements"></a>Požadavky systému virtuálních polí StorSimple

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Přehled

Tento článek popisuje důležité systémové požadavky pro vaše virtuální pole Microsoft Azure StorSimple a pro klienty úložiště, kteří přistupují k poli. Doporučujeme, abyste si tyto informace před nasazením systému StorSimple pečlivě prostudovali a pak se k němu vrátili podle potřeby během nasazení a následné operace.

Systémové požadavky zahrnují:

* **Požadavky na software pro klienty úložišť** – popisuje podporované virtualizační platformy, webové prohlížeče, iniciátory iSCSI, klienty SMB, minimální požadavky na virtuální zařízení a veškeré další požadavky na tyto operační systémy.
* **Síťové požadavky pro zařízení StorSimple** – poskytuje informace o portech, které je třeba otevřít v bráně firewall, aby bylo možné provádět přenosy iSCSI, cloudu nebo správy.

Informace o požadavcích na systém StorSimple publikované v tomto článku platí pouze pro virtuální pole StorSimple.

* U zařízení řady 8000 přejděte na [části Systémové požadavky pro zařízení řady StorSimple 8000](storsimple-system-requirements.md).
* U zařízení řady 7000 přejděte na [části Systémové požadavky pro zařízení řady StorSimple 5000-7000](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Požadavky na software
Požadavky na software zahrnují informace o podporovaných webových prohlížečích, verzích SMB, virtualizačních platformách a minimálních požadavcích na virtuální zařízení.

### <a name="supported-virtualization-platforms"></a>Podporované virtualizační platformy
| **Hypervisor** | **Verze** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 a novější |
| VMware ESXi |5,0, 5,5, 6,0 a 6,5. |

> [!IMPORTANT]
> Neinstalujte nástroje VMware do virtuálního pole StorSimple; Výsledkem bude nepodporovaná konfigurace.

### <a name="virtual-device-requirements"></a>Požadavky na virtuální zařízení
| **Komponenta** | **Požadavek** |
| --- | --- |
| Minimální počet virtuálních procesorů (jader) |4 |
| Minimální paměť (RAM) |8 GB <br> Pro souborový server 8 GB pro méně než 2 miliony souborů a 16 GB pro 2 - 4 miliony souborů|
| Místo na disku<sup>1</sup> |Disk operačního systému - 80 GB <br></br>Datový disk - 500 GB až 8 TB |
| Minimální počet síťových rozhraní |1 |
| Šířka pásma internetu<sup>2</sup> |Minimální požadovaná šířka pásma: 5 Mb/s <br> Doporučená šířka pásma: 100 Mb/s <br> Rychlost přenosu dat se škáluje s šířkou pásma internetu. Například přenos dat o velikosti 100 GB dat trvá 2 dny, což by mohlo vést k selhání zálohování, protože denní zálohování by se nedokončilo za den. S šířkou pásma 100 Mb/s lze přenést 100 GB dat za 2,5 hodiny.   |

<sup>1</sup> - Tenký zřízený

<sup>2</sup> - Požadavky na síť se mohou lišit v závislosti na denní rychlosti změny dat. Pokud například zařízení potřebuje zálohovat 10 GB nebo více změn během dne, může denní zálohování přes připojení 5 Mb/s trvat až 4,25 hodiny (pokud data nelze komprimovat nebo deduplikovat).

### <a name="supported-web-browsers"></a>Podporované webové prohlížeče
| **Komponenta** | **Verze** | **Další požadavky/poznámky** |
| --- | --- | --- |
| Microsoft Edge |Nejnovější verze | |
| Internet Explorer |Nejnovější verze |Testováno pomocí aplikace Internet Explorer 11 |
| Google Chrome |Nejnovější verze |Testováno s Chrome 46 |

### <a name="supported-storage-clients"></a>Podporovaní klienti úložiště
Následující požadavky na software jsou určeny pro iniciátory iSCSI, které přistupují k virtuálnímu poli StorSimple (nakonfigurovanému jako server iSCSI).

| **Podporované operační systémy** | **Požadovaná verze** | **Další požadavky/poznámky** |
| --- | --- | --- |
| Windows Server |2008R2 SP1, 2012, 2012R2 |StorSimple můžete vytvořit tence zřízená a plně zřízené svazky. Nemůže vytvořit částečně zřízené svazky. Svazky ISCSI jsou podporovány pouze pro: <ul><li>Jednoduché svazky na základních discích systému Windows.</li><li>Systém Windows NTFS pro formátování svazku.</li> |

Následující požadavky na software jsou určeny pro klienty SMB, kteří přistupují k virtuálnímu poli StorSimple (nakonfigurovanému jako souborový server).

| **Verze protokolu SMB** |
| --- |
| SMB 2.x |
| SMB 3.0 |
| SMB 3,02 |

> [!IMPORTANT]
> Nekopírujte ani neuklápějte soubory chráněné systémem SOUBORŮ (EFS) systému Windows na souborový server StorSimple Virtual Array. Výsledkem bude nepodporovaná konfigurace.


### <a name="supported-storage-format"></a>Podporovaný formát úložiště
Je podporováno jenom úložiště objektů blob bloku Azure. Objekty BLOB stránky nejsou podporovány. Další informace [o objektech BLOB bloku a objektech BLOB stránky](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="networking-requirements"></a>Požadavky na vytváření sítí
V následující tabulce jsou uvedeny porty, které je třeba otevřít v bráně firewall, aby bylo možné provádět přenosy iSCSI, SMB, cloud nebo správa. V této tabulce *in* nebo *příchozí* odkazuje na směr, ze kterého příchozí klient požaduje přístup k zařízení. *Odchozí* nebo *odchozí* odkazuje na směr, ve kterém vaše zařízení StorSimple odesílá data externě, mimo nasazení: například odchozí do Internetu.

| **Přístav č.<sup>1</sup>** | **Dovnitř nebo ven** | **Rozsah portu** | **Požadováno** | **Poznámky** |
| --- | --- | --- | --- | --- |
| Protokol TCP 80 (HTTP) |Out |Síť WAN |Ne |Odchozí port se používá pro přístup k Internetu k načtení aktualizací. <br></br>Odchozí webový proxy server lze konfigurovat uživatelem. |
| Protokol TCP 443 (HTTPS) |Out |Síť WAN |Ano |Odchozí port se používá pro přístup k datům v cloudu. <br></br>Odchozí webový proxy server lze konfigurovat uživatelem. |
| UDP 53 (DNS) |Out |Síť WAN |V některých případech; viz poznámky. |Tento port je vyžadován pouze v případě, že používáte internetový server DNS. <br></br> Všimněte si, že pokud nasazujete souborový server, doporučujeme použít místní server DNS. |
| UDP 123 (NTP) |Out |Síť WAN |V některých případech; viz poznámky. |Tento port je vyžadován pouze v případě, že používáte internetový server NTP.<br></br> Všimněte si, že při nasazení souborového serveru doporučujeme synchronizovat čas s řadiči domény služby Active Directory. |
| Protokol TCP 80 (HTTP) |V |LAN |Ano |Toto je vstupní port pro místní uI na zařízení StorSimple pro místní správu. <br></br> Všimněte si, že přístup k místnímu ui přes HTTP automaticky přesměruje na HTTPS. |
| Protokol TCP 443 (HTTPS) |V |LAN |Ano |Toto je vstupní port pro místní uI na zařízení StorSimple pro místní správu. |
| Protokol TCP 3260 (iSCSI) |V |LAN |Ne |Tento port se používá pro přístup k datům přes iSCSI. |

<sup>1</sup> Na veřejném Internetu není třeba otevírat žádné vstupní porty.

> [!IMPORTANT]
> Ujistěte se, že brána firewall neupravuje nebo nedešifruje žádný provoz TLS mezi zařízením StorSimple a Azure.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>Vzory adres URL pro pravidla brány firewall
Správci sítě mohou často konfigurovat pokročilá pravidla brány firewall na základě vzorů adres URL tak, aby filtrovali příchozí a odchozí přenosy. Vaše virtuální pole a služba StorSimple Device Manager závisí na jiných aplikacích Microsoftu, jako je Azure Service Bus, Azure Active Directory Access Control, účty úložiště a servery Microsoft Update. Vzory adres URL přidružené k těmto aplikacím lze použít ke konfiguraci pravidel brány firewall. Je důležité si uvědomit, že vzory adres URL přidružené k těmto aplikacím se mohou změnit. To zase bude vyžadovat, aby správce sítě sledovat a aktualizovat pravidla brány firewall pro Vaše StorSimple podle potřeby. 

Doporučujeme nastavit pravidla brány firewall pro odchozí provoz na základě Pevných IP adres StorSimple, ve většině případů liberálně. Níže uvedené informace však můžete použít k nastavení rozšířených pravidel brány firewall, která jsou potřebná k vytvoření zabezpečeného prostředí.

> [!NOTE]
> 
> * Ip adresy zařízení (zdroje) by měly být vždy nastaveny na všechna síťová rozhraní s podporou cloudu. 
> * Cílové IP adresy by měly být nastaveny na [rozsahy IP adres datového centra Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).
> 
> 

| Vzor adresy URL | Součást/funkce |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|Služba Správce zařízení StorSimple<br>Služba řízení přístupu<br>Azure Service Bus<br>Ověřovací služba|
| `http://*.backup.windowsazure.com` |Registrace zařízení |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Odvolání certifikátu |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Účty úložiště Azure a monitorování |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Servery Microsoft Update<br> |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |
| `https://*.partners.extranet.microsoft.com/*` |Balíček podpory |
| `https://*.data.microsoft.com` |Telemetrická služba v systému Windows, podívejte se na [aktualizaci pro zákaznickou zkušenost a diagnostickou telemetrii](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>Další kroky
* [Příprava portálu k nasazení virtuálního pole StorSimple](storsimple-virtual-array-deploy1-portal-prep.md)
