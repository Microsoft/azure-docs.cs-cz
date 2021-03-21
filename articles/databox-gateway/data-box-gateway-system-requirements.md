---
title: Microsoft Azure Data Box Gateway požadavky na systém | Microsoft Docs
description: Seznamte se s požadavky na software a sítě pro vaše Azure Data Box Gateway
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: e7c8653b39a3e0333ff6e98783a6e9a1437dba22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739207"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Azure Data Box Gateway požadavky na systém

Tento článek popisuje důležité systémové požadavky pro vaše Microsoft Azure Data Box Gateway řešení a klienty, kteří se připojují k Azure Data Box Gateway. Před nasazením Data Box Gateway doporučujeme pečlivě zkontrolovat informace a pak je v případě potřeby v průběhu nasazení a následné operace vrátit na ni. 

Požadavky na systém pro virtuální zařízení Data Box Gateway zahrnují:

- **Požadavky na software pro hostitele** – popisuje podporované platformy, prohlížeče pro místní uživatelské rozhraní konfigurace, klienty SMB a všechny další požadavky pro hostitele, kteří se k zařízení připojují.
- **Požadavky na síť pro zařízení** – poskytuje informace o požadavcích na síť pro provoz virtuálního zařízení.


## <a name="specifications-for-the-virtual-device"></a>Specifikace pro virtuální zařízení

Základní hostitelský systém pro Data Box Gateway je schopný vyhradit následující prostředky pro zřízení virtuálního zařízení:

| Specifikace                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Virtuální procesory (jádra)   | Minimálně 4 |
| Memory (Paměť)  | Minimální 8 GB. Důrazně doporučujeme aspoň 16 GB. |
| Dostupnost|Jeden uzel|
| Disky| Disk operačního systému: 250 GB <br> Datový disk: minimálně 2 TB, dynamicky zřizovaný, musí být zálohován pomocí disků SSD|
| Síťová rozhraní|1 nebo více rozhraní virtuální sítě|


## <a name="supported-os-for-clients-connected-to-device"></a>Podporovaný operační systém pro klienty připojené k zařízení

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Podporované protokoly pro klienty, kteří přistupují k zařízení

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>Podporované virtualizační platformy pro zařízení

| **Operační systém/platforma**  |**Versions** (Verze)   |**Poznámky**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016 <br> 2019 |         |
|VMware ESXi     | 6.0 <br> 6.5 <br> 6.7       |Nástroje VMware nejsou podporovány.         |


## <a name="supported-storage-accounts"></a>Podporované účty úložiště

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>Podporované typy úložiště

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Podporované prohlížeče pro místní webové uživatelské rozhraní

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Požadavky na porty sítě

Následující tabulka uvádí porty, které je třeba v bráně firewall otevřít, aby umožňovaly provoz SMB, cloudu nebo správy. V této tabulce se *v* nebo *příchozí* odkazuje na směr, ze kterého příchozí klient žádá o přístup k vašemu zařízení. *Výstupní* nebo *odchozí* odkazuje na směr, ve kterém vaše zařízení data box Gateway odesílá data externě, mimo nasazení: například odchozí na Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>Vzory adres URL pro pravidla brány firewall

Správci sítě mohou často konfigurovat Rozšířená pravidla brány firewall na základě vzorů adres URL pro filtrování příchozího a odchozího provozu. Vaše zařízení Data Box Gateway a služba Data Box Gateway závisí na dalších aplikacích společnosti Microsoft, jako jsou Azure Service Bus, Azure Active Directory Access Control, účtů úložiště a Microsoft Updatech serverech. Ke konfiguraci pravidel brány firewall lze použít vzory adres URL přidružené k těmto aplikacím. Je důležité pochopit, že se vzory adres URL přidružené k těmto aplikacím můžou změnit. To pak bude vyžadovat, aby správce sítě sledoval a aktualizoval pravidla brány firewall pro váš Data Box Gateway, a to v případě potřeby.

Doporučujeme, abyste nastavili pravidla brány firewall pro odchozí přenosy na základě Data Box Gateway pevné IP adresy, a to ve většině případů. Pomocí níže uvedených informací ale můžete nastavit Rozšířená pravidla brány firewall, která jsou potřeba k vytváření zabezpečených prostředí.

> [!NOTE]
> - IP adresy zařízení (zdrojové) by se měly vždycky nastavit na všechna síťová rozhraní s podporou cloudu.
> - Cílové IP adresy by měly být nastavené na [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-azure-government"></a>Vzory adres URL pro Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Šířka pásma internetu

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Další krok

* [Nasazení Azure Data Box Gateway](data-box-gateway-deploy-prep.md)

