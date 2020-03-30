---
title: Požadavky na systém brány datových schronů Microsoft Azure| Dokumenty společnosti Microsoft
description: Informace o softwaru a síťových požadavcích pro bránu Azure Data Box Gateway
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 05/08/2019
ms.author: alkohli
ms.openlocfilehash: e8932097bcdef782b1a551d386c2872e02d8abfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260186"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Požadavky na systém Azure Data Box Gateway

Tento článek popisuje důležité systémové požadavky pro vaše řešení Brány datových schronů Microsoft Azure a pro klienty, kteří se připojují k bráně Azure Data Box Gateway. Doporučujeme, abyste si tyto informace před nasazením brány datové schránky pečlivě prostudovali a pak se k ní vrátili podle potřeby během nasazení a následné operace.

Systémové požadavky pro virtuální zařízení Brány datových schronů zahrnují:

- **Požadavky na software pro hostitele** – popisuje podporované platformy, prohlížeče pro místní konfigurační ui, klienty SMB a všechny další požadavky pro hostitele, kteří se připojují k zařízení.
- **Síťové požadavky pro zařízení** - poskytuje informace o všech síťových požadavků pro provoz virtuálního zařízení.


## <a name="specifications-for-the-virtual-device"></a>Specifikace pro virtuální zařízení

Základní hostitelský systém pro bránu datové schránky je schopen vyhradit následující prostředky pro zřízení virtuálního zařízení:

| Specifikace                                          | Popis              |
|---------------------------------------------------------|--------------------------|
| Virtuální procesory (jádra)   | Minimálně 4 |
| Memory (Paměť)  | Minimálně 8 GB|
| Dostupnost|Jeden uzel|
| Disky| Disk operačního systému: 250 GB <br> Datový disk: minimálně 2 TB, dynamicky zřizovaný, musí být zálohován pomocí disků SSD|
| Síťová rozhraní|1 nebo více rozhraní virtuální sítě|


## <a name="supported-os-for-clients-connected-to-device"></a>Podporovaný operační systém pro klienty připojené k zařízení

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Podporované protokoly pro klienty přistupující k zařízení

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>Podporované virtualizační platformy pro zařízení

| **Operační systém/platforma**  |**Verze**   |**Poznámky**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016 <br> 2019 |         |
|VMware ESXi     | 6.0 <br> 6.5 <br> 6.7       |Nástroje vmware nejsou podporovány.         |


## <a name="supported-storage-accounts"></a>Podporované účty úložiště

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>Podporované typy úložiště

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Podporované prohlížeče pro místní webové uživatelské prostředí

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Požadavky na síťový port

V následující tabulce jsou uvedeny porty, které je třeba otevřít v bráně firewall, aby bylo možné provádět přenosy s konzolí SMB, cloudnebo správu. V této tabulce *in* nebo *příchozí* odkazuje na směr, ze kterého příchozí klient požaduje přístup k vašemu zařízení. *Odchozí* nebo *odchozí* označuje směr, ve kterém vaše zařízení Brány dat odesílá data externě, mimo nasazení: například odchozí do Internetu.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>Vzory adres URL pro pravidla brány firewall

Správci sítě mohou často konfigurovat pokročilá pravidla brány firewall na základě vzorů adres URL tak, aby filtrovali příchozí a odchozí přenosy. Vaše zařízení Brány datových schronů a služba Brána datové schránky závisí na jiných aplikacích Microsoftu, jako je Azure Service Bus, Azure Active Directory Access Control, účty úložiště a servery Microsoft Update. Vzory adres URL přidružené k těmto aplikacím lze použít ke konfiguraci pravidel brány firewall. Je důležité si uvědomit, že vzory adres URL přidružené k těmto aplikacím se mohou změnit. To zase bude vyžadovat, aby správce sítě monitoroval a aktualizoval pravidla brány firewall pro bránu datové schránky podle potřeby.

Doporučujeme nastavit pravidla brány firewall pro odchozí provoz na základě pevných IP adres brány databoxu, ve většině případů liberálně. Níže uvedené informace však můžete použít k nastavení rozšířených pravidel brány firewall, která jsou potřebná k vytvoření zabezpečeného prostředí.

> [!NOTE]
> - Ip adresy zařízení (zdroje) by měly být vždy nastaveny na všechna síťová rozhraní s podporou cloudu.
> - Cílové IP adresy by měly být nastaveny na [rozsahy IP adres datového centra Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-azure-government"></a>Vzory adres URL pro Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Šířka pásma internetu

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Další krok

* [Nasazení brány datové schránky Azure](data-box-gateway-deploy-prep.md)

