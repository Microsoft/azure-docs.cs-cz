---
title: Požadavky na systém Microsoft Azure Data Box Gateway | Dokumentace Microsoftu
description: Další informace o softwaru a požadavky na síť pro bránu Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: be51e6c829c5f12d17c6302bb951039711f12075
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113222"
---
# <a name="azure-data-box-gateway-system-requirements-preview"></a>Azure Data Box brány požadavky na systém (Preview)

Tento článek popisuje důležité systémové požadavky pro vaše řešení Microsoft Azure Data Box brány a klienti připojení k Azure Data Box brány. Doporučujeme, abyste si informace o pečlivě před nasazení brány dat pole a pak zpátky na ni odkazovat podle potřeby během nasazení a následná operace.

Požadavky na systém pro virtuální zařízení Data Box brány patří:

- **Požadavky na software pro hostitele** -popisuje podporované platformy, prohlížeče pro místní konfiguraci, uživatelského rozhraní, klienti SMB a veškeré další požadavky pro hostitele, které se připojují k zařízení.
- **Požadavky na síť pro zařízení** – poskytuje informace o všech požadavků na síť pro operaci virtuálního zařízení.

> [!IMPORTANT]
> Data Box Gateway je ve verzi Preview. Před nasazením tohoto řešení si prosím přečtěte [podmínky použití verze Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## <a name="specifications-for-the-virtual-device"></a>Specifikace pro virtuální zařízení

Základní systém hostitele pro pole brána dat je možné vyhradit následující prostředky pro zřízení virtuálního zařízení:

| Specifikace                                          | Popis              |
|---------------------------------------------------------|--------------------------|
| Virtuální procesory (jádra)   | Minimálně 4 |            
| Memory (Paměť)  | Minimálně 8 GB|
| Dostupnost|Jeden uzel|
| Disky| Disk s operačním systémem: 250 GB <br> Datový disk: Minimálně 2 TB, dynamického zajišťování zřízené a musí být založená na jednotkách SSD|
| Síťová rozhraní|1 nebo více rozhraní virtuální sítě|


## <a name="supported-os-for-clients-connected-to-device"></a>Podporovaný operační systém pro klienty připojené k zařízení

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Podporované protokoly klientům přístup k zařízení

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>Podporovaný virtualizační platformy pro zařízení

| **Operačního systému a platformy**  |**Verze**   |**Poznámky**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016  |         |
|VMware ESXi     | 6.0 <br> 6.5 <br> 6.7       |Nástroje VMware nejsou podporovány.         |


## <a name="supported-storage-accounts"></a>Účty úložiště podporuje

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>Typy podporovaných úložišť

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Podporované prohlížeče pro místního webového uživatelského rozhraní

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Požadavky na síťové porty

Následující tabulka uvádí porty, které je potřeba otevřít v bráně firewall povolit pro protokol SMB, cloud nebo přenos pro správu. V této tabulce *v* nebo *příchozí* odkazuje na směru, které mají přístup příchozí požadavky klienta na vaše zařízení. *Navýšení kapacity* nebo *odchozí* odkazuje na směru, ve kterém zařízení Data Box brány odesílá data externě, nad rámec nasazení: pro příklad, odchozí k Internetu.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>Vzory adres URL pro pravidla brány firewall

Správci sítě můžou často konfigurace pravidla brány firewall na Upřesnit na základě vzory adres URL pro filtrování příchozího a odchozího provozu. Vaše zařízení Data Box brány a služba brány dat pole závisí na jiné aplikace od Microsoftu, jako je Azure Service Bus, Azure Active Directory Access Control, účty úložiště a serverům Microsoft Update. Vzory adres URL, které jsou přidružené k těmto aplikacím je možné nakonfigurovat pravidla brány firewall. Je důležité pochopit, že můžete změnit vzory adres URL, které jsou přidružené k těmto aplikacím. To zase vyžaduje správce sítě, monitorovat a aktualizovat pravidla brány firewall pro vaši bránu Data pole jako a v případě potřeby.

Doporučujeme nastavit pravidla brány firewall pro odchozí provoz, podle pole brána dat liberally pevné IP adresy, ve většině případů. Ale můžete použít následující informace pro nastavení pravidla brány firewall na pokročilé, které budete potřebovat pro vytvoření zabezpečeného prostředí.

> [!NOTE]
> - Zařízení (zdroj) IP adresy musí být vždy nastavená na všechna rozhraní sítě povolenou podporu cloudu.
> - Cílové IP adresy musí být nastavená na [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Šířka pásma sítě Internet

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Další krok

* [Nasazení brány Azure Data Box](data-box-gateway-deploy-prep.md)

