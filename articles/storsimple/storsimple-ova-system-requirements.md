---
title: Požadavky na systém Microsoft Azure StorSimple Virtual Array
description: Seznamte se s požadavky na software a sítě pro virtuální pole StorSimple.
author: alkohli
ms.assetid: ea1d3bca-e71b-453d-aa82-440d2638f5e3
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 5970e82619667a47ba160c84df2cdeb145b0dab8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966169"
---
# <a name="storsimple-virtual-array-system-requirements"></a>Požadavky systému virtuálních polí StorSimple

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Přehled

Tento článek popisuje důležité systémové požadavky pro vaše Microsoft Azure StorSimple virtuální pole a pro klienty úložiště, kteří přistupují k poli. Doporučujeme, abyste pečlivě prostudovali informace před nasazením systému StorSimple a pak se na něj v průběhu nasazení a následné operace znovu odkazovali.

Požadavky na systém zahrnují:

* **Požadavky na software pro klienty úložiště** – popisuje podporované virtualizační platformy, webové prohlížeče, iniciátory iSCSI, klienty SMB, minimální požadavky na virtuální zařízení a jakékoli další požadavky pro tyto operační systémy.
* **Požadavky na síť pro zařízení StorSimple** – poskytuje informace o portech, které je potřeba v bráně firewall otevřít, aby se povolily přenosy z iSCSI, cloudu nebo správy.

Informace o požadavcích na systém StorSimple publikované v tomto článku se vztahují pouze na virtuální pole StorSimple.

* V případě zařízení řady 8000 pokračujte na [požadavky na systém pro zařízení StorSimple 8000 series](./storsimple-8000-system-requirements.md).
* V případě zařízení řady 7000 pokračujte na [požadavky na systém pro zařízení StorSimple 5000-7000 Series](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).

## <a name="software-requirements"></a>Požadavky na software
Požadavky na software zahrnují informace o podporovaných webových prohlížečích, verzích SMB, virtualizačních platformách a minimálních požadavcích na virtuální zařízení.

### <a name="supported-virtualization-platforms"></a>Podporované virtualizační platformy
| **Hypervisor** | **Verze** |
| --- | --- |
| Hyper-V |Windows Server 2008 R2 SP1 a novější |
| VMware ESXi |5,0, 5,5, 6,0 a 6,5. |

> [!IMPORTANT]
> Neinstalujte nástroje VMware Tools na virtuální pole StorSimple; Výsledkem bude Nepodporovaná konfigurace.

### <a name="virtual-device-requirements"></a>Požadavky na virtuální zařízení
| **Komponenta** | **Požadavek** |
| --- | --- |
| Minimální počet virtuálních procesorů (jader) |4 |
| Minimální paměť (RAM) |8 GB <br> U souborového serveru 8 GB pro méně než 2 000 000 souborů a 16 GB pro 2-4 milionů souborů|
| Místo na disku<sup>1</sup> |Disk s operačním systémem – 80 GB <br></br>Datový disk – 500 GB až 8 TB |
| Minimální počet síťových rozhraní: |1 |
| Šířka pásma internetu<sup>2</sup> |Minimální požadovaná šířka pásma: 5 MB/s <br> Doporučená šířka pásma: 100 MB/s <br> Rychlost přenosu dat se škáluje s využitím šířky pásma internetu. Například 100 GB dat trvá 2 dny na přenos v 5 MB/s, což by mohlo vést k selhání zálohování, protože denní zálohování nebylo dokončeno za den. Díky šířce pásma 100 MB/s se dá 100 GB dat přenést za 2,5 hodin.   |

<sup>1</sup> – dynamicky zřízené

<sup>2</sup> – požadavky na síť se můžou lišit v závislosti na denní četnosti změn dat. Pokud třeba v rámci určitého dne zařízení potřebuje zálohovat 10 GB nebo více změn, pak může trvat až 4,25 hodin (Pokud data nemůžete zkomprimovat nebo odstranit z duplicit).

### <a name="supported-web-browsers"></a>Podporované webové prohlížeče
| **Komponenta** | **Verze** | **Další požadavky/poznámky** |
| --- | --- | --- |
| Microsoft Edge |Nejnovější verze | |
| Internet Explorer |Nejnovější verze |Testováno pomocí aplikace Internet Explorer 11 |
| Google Chrome |Nejnovější verze |Testováno s použitím Chrome 46 |

### <a name="supported-storage-clients"></a>Podporovaní klienti úložiště
Následující požadavky na software jsou pro iniciátory iSCSI, které přistupují k virtuálnímu poli StorSimple (nakonfigurovanému jako server iSCSI).

| **Podporované operační systémy** | **Vyžaduje se verze** | **Další požadavky/poznámky** |
| --- | --- | --- |
| Windows Server |2008 R2 SP1, 2012, 2012R2 |StorSimple může vytvářet dynamicky zřízené a plně zřízené svazky. Nemůže vytvořit částečně zřízené svazky. StorSimple svazky iSCSI se podporují jenom pro: <ul><li>Jednoduché svazky na discích se systémem Windows Basic.</li><li>Systém Windows NTFS pro formátování svazku.</li> |

Následující požadavky na software jsou pro klienty SMB, kteří přistupují k virtuálnímu poli StorSimple (nakonfigurovanému jako souborový server).

| **Verze protokolu SMB** |
| --- |
| SMB 2. x |
| SMB 3.0 |
| SMB 3,02 |

> [!IMPORTANT]
> Nekopírujte ani neukládá soubory chráněné systémem Windows systém souborů EFS (Encrypting File System) (EFS) do serveru souborů virtuálního pole StorSimple. Výsledkem bude Nepodporovaná konfigurace.


### <a name="supported-storage-format"></a>Podporovaný formát úložiště
Podporuje se jenom úložiště objektů blob bloku Azure. Objekty blob stránky nejsou podporovány. Další informace [o objektech blob bloku a objektech blob stránky](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

## <a name="networking-requirements"></a>Požadavky na síť
Následující tabulka uvádí porty, které je třeba v bráně firewall otevřít, aby umožňovaly přenosy z iSCSI, SMB, cloudu nebo správy. V této tabulce se *v* nebo *příchozí* odkazuje na směr, ze kterého příchozí klient žádá o přístup k vašemu zařízení. *Výstupní* nebo *odchozí* odkazuje na směr, ve kterém vaše zařízení StorSimple odesílá data externě, mimo nasazení: například odchozí na Internet.

| **Číslo portu.<sup>1</sup>** | **V nebo ven** | **Rozsah portů** | **Povinné** | **Poznámky** |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP) |Out |Síť WAN |Ne |Odchozí port se používá pro přístup k Internetu k načtení aktualizací. <br></br>Odchozí webový proxy server je uživatelsky konfigurovatelné. |
| TCP 443 (HTTPS) |Out |Síť WAN |Yes |Odchozí port se používá pro přístup k datům v cloudu. <br></br>Odchozí webový proxy server je uživatelsky konfigurovatelné. |
| UDP 53 (DNS) |Out |Síť WAN |V některých případech; viz poznámky. |Tento port je vyžadován pouze v případě, že používáte internetový server DNS. <br></br> Upozorňujeme, že pokud nasazujete souborový server, doporučujeme použít místní server DNS. |
| UDP 123 (NTP) |Out |Síť WAN |V některých případech; viz poznámky. |Tento port je vyžadován pouze v případě, že používáte internetový server NTP.<br></br> Počítejte s tím, že pokud nasazujete souborový server, doporučujeme synchronizovat čas s řadiči domény služby Active Directory. |
| TCP 80 (HTTP) |V |Síť LAN |Yes |Toto je příchozí port pro místní uživatelské rozhraní na zařízení StorSimple pro místní správu. <br></br> Všimněte si, že přístup k místnímu uživatelskému rozhraní přes HTTP se automaticky přesměruje na HTTPS. |
| TCP 443 (HTTPS) |V |Síť LAN |Yes |Toto je příchozí port pro místní uživatelské rozhraní na zařízení StorSimple pro místní správu. |
| TCP 3260 (iSCSI) |V |Síť LAN |Ne |Tento port se používá pro přístup k datům přes iSCSI. |

<sup>1</sup> na veřejném Internetu není třeba otevřít žádné příchozí porty.

> [!IMPORTANT]
> Ujistěte se, že brána firewall nemění ani nešifruje žádný přenos TLS mezi zařízením StorSimple a Azure.
> 
> 

### <a name="url-patterns-for-firewall-rules"></a>Vzory adres URL pro pravidla brány firewall
Správci sítě mohou často konfigurovat Rozšířená pravidla brány firewall na základě vzorů adres URL pro filtrování příchozího a odchozího provozu. Vaše virtuální pole a Služba StorSimple Device Manager závisí na dalších aplikacích společnosti Microsoft, jako jsou Azure Service Bus, Azure Active Directory Access Control, účtech úložiště a Microsoft Updatech serverech. Ke konfiguraci pravidel brány firewall lze použít vzory adres URL přidružené k těmto aplikacím. Je důležité pochopit, že se vzory adres URL přidružené k těmto aplikacím můžou změnit. To zase vyžaduje, aby správce sítě sledoval a aktualizoval pravidla brány firewall pro StorSimple jako a v případě potřeby. 

Doporučujeme, abyste nastavili pravidla brány firewall pro odchozí přenosy na základě pevných IP adres StorSimple, a to ve většině případů. Pomocí níže uvedených informací ale můžete nastavit Rozšířená pravidla brány firewall, která jsou potřeba k vytváření zabezpečených prostředí.

> [!NOTE]
> 
> * IP adresy zařízení (zdrojové) by se měly vždycky nastavit na všechna síťová rozhraní s podporou cloudu. 
> * Cílové IP adresy by měly být nastavené na [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).
> 
> 

| Vzor adresy URL | Součást/funkce |
| --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*` <br>`https://login.windows.net`|Služba Správce zařízení StorSimple<br>Access Control Service<br>Azure Service Bus<br>Ověřovací služba|
| `http://*.backup.windowsazure.com` |Registrace zařízení |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |Odvolání certifikátu |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Účty a monitorování Azure Storage |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update servery<br> |
| `http://*.deploy.akamaitechnologies.com` |CDN Akamai |
| `https://*.partners.extranet.microsoft.com/*` |Balíček pro podporu |
| `https://*.data.microsoft.com` |Služba telemetrie ve Windows, přečtěte si část [aktualizace prostředí pro zákazníky a diagnostické telemetrie](https://support.microsoft.com/en-us/kb/3068708) |

## <a name="next-steps"></a>Další kroky
* [Příprava portálu na nasazení virtuálního pole StorSimple](storsimple-virtual-array-deploy1-portal-prep.md)