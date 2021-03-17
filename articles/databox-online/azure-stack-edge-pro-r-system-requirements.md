---
title: Požadavky na systém Microsoft Azure Stack Edge pro R | Microsoft Docs
description: Seznamte se s požadavky na software a síť pro Azure Stack Edge pro R
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: alkohli
ms.openlocfilehash: ef81494b6c93f2941cc41b367e566332d6bc7cc9
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831330"
---
# <a name="azure-stack-edge-pro-r-system-requirements"></a>Požadavky na systém Azure Stack Edge pro R

Tento článek popisuje důležité systémové požadavky pro řešení Azure Stack Edge pro R a klienty, kteří se připojují k Azure Stack Edge pro R. Před nasazením Azure Stack Edge pro R doporučujeme pečlivě zkontrolovat informace. V případě potřeby můžete v průběhu nasazení a následné operace přejít zpět na tyto informace.

Požadavky na systém pro Azure Stack Edge pro R zahrnují:

- **Požadavky na software pro hostitele** – popisuje podporované platformy, prohlížeče pro místní uživatelské rozhraní konfigurace, klienty SMB a všechny další požadavky na klienty, kteří přistupují k zařízení.
- **Požadavky na síť pro zařízení** – poskytuje informace o požadavcích na síť pro provoz fyzického zařízení.

## <a name="supported-os-for-clients-connected-to-device"></a>Podporovaný operační systém pro klienty připojené k zařízení

[!INCLUDE [Supported OS for clients connected to device](../../includes/azure-stack-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Podporované protokoly pro klienty, kteří přistupují k zařízení

[!INCLUDE [Supported protocols for clients accessing device](../../includes/azure-stack-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Podporované účty úložiště

[!INCLUDE [Supported storage accounts](../../includes/azure-stack-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-tiered-storage-accounts"></a>Podporované účty vrstveného úložiště

Při správě z Azure Stack jsou následující vrstvené účty úložiště podporovány pomocí rozhraní SMB/NFS/REST.

|Typ  |Účet úložiště  |Komentáře  |
|---------|---------|---------|
|Standard     |GPv1: objekt blob bloku         |         |
|    |  BLOB Storage: objekt blob bloku       | Podporováno pouze pro NAS     |

* Objekty blob stránky a soubory Azure se v Azure Stack v tuto chvíli nepodporují.
* * Horká a studená vrstva neexistuje v Azure Stack. Po nahrání dat můžete data přesunout do archivní úrovně pomocí Azure PowerShell. Podrobné pokyny najdete v tématu [použití Azure PowerShell k nastavení úrovně objektu BLOB]() .

## <a name="supported-storage-types"></a>Podporované typy úložiště

[!INCLUDE [Supported storage types](../../includes/azure-stack-edge-gateway-supported-storage-types.md)]


## <a name="supported-browsers-for-local-web-ui"></a>Podporované prohlížeče pro místní webové uživatelské rozhraní

[!INCLUDE [Supported browsers for local web UI](../../includes/azure-stack-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Požadavky na porty sítě

### <a name="port-requirements-for-azure-stack-edge-pro-r"></a>Požadavky na porty pro Azure Stack Edge pro R

Následující tabulka uvádí porty, které je třeba v bráně firewall otevřít, aby umožňovaly provoz SMB, cloudu nebo správy. V této tabulce se *v* nebo *příchozí* odkazuje na směr, ze kterého příchozí klient žádá o přístup k vašemu zařízení. *Výstupní* nebo *odchozí* odkazuje na směr, ve kterém vaše Azure Stack hraniční zařízení pro R odesílá data externě, mimo nasazení, například odchozí na Internet.

[!INCLUDE [Port configuration for device](../../includes/azure-stack-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Požadavky na port pro IoT Edge

Azure IoT Edge umožňuje odchozí komunikaci z místního hraničního zařízení do cloudu Azure pomocí podporovaných protokolů IoT Hub. Příchozí komunikace se vyžaduje jenom u konkrétních scénářů, ve kterých musí Azure IoT Hub předávat zprávy do Azure IoT Edge zařízení (třeba zasílání zpráv z cloudu do zařízení).

Použijte následující tabulku pro konfiguraci portů pro servery hostující Azure IoT Edge Runtime:

| Číslo portu | V nebo ven | Rozsah portů | Vyžadováno | Pokyny |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Out       | Síť WAN        | Ano      | Odchozí otevření pro IoT Edge zřizování. Tato konfigurace se vyžaduje při použití ručních skriptů nebo služby Azure IoT Device Provisioning (DPS).|

Pokud chcete získat úplné informace, přečtěte si [pravidla brány firewall a konfigurace portů pro nasazení IoT Edge](../iot-edge/troubleshoot.md).

## <a name="url-patterns-for-firewall-rules"></a>Vzory adres URL pro pravidla brány firewall

Správci sítě mohou často konfigurovat Rozšířená pravidla brány firewall na základě vzorů adres URL pro filtrování příchozího a odchozího provozu. Vaše zařízení Azure Stack Edge pro R a službu závisí na dalších aplikacích společnosti Microsoft, jako jsou Azure Service Bus, Azure Active Directory Access Control, účtech úložiště a Microsoft Updatech serverech. Ke konfiguraci pravidel brány firewall lze použít vzory adres URL přidružené k těmto aplikacím. Je důležité pochopit, že se vzory adres URL přidružené k těmto aplikacím můžou změnit. Tyto změny vyžadují, aby správce sítě sledoval a aktualizoval pravidla brány firewall pro Azure Stack Edge pro R, a to v případě potřeby.

Doporučujeme, abyste nastavili pravidla brány firewall pro odchozí přenosy, a to na základě standardu Azure Stack hraničních IP adres pro R, ve většině případů svobodně. Pomocí níže uvedených informací ale můžete nastavit Rozšířená pravidla brány firewall, která jsou potřeba k vytváření zabezpečených prostředí.

> [!NOTE]
> - IP adresy zařízení (zdrojové) by se měly vždycky nastavit na všechna síťová rozhraní s podporou cloudu.
> - Cílové IP adresy by měly být nastavené na [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Vzory adres URL pro funkci brány

[!INCLUDE [URL patterns for firewall](../../includes/azure-stack-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Vzory adres URL pro funkci COMPUTE

| Vzor adresy URL                      | Součást nebo funkce                     |   
|----------------------------------|---------------------------------------------|
| https: \/ /MCR.Microsoft.com<br></br>https:// \* . CDN.mscr.IO | Microsoft Container Registry (povinné)               |
| https:// \* . azurecr.IO                     | Registry kontejnerů pro osobní a třetí strany (volitelné) | 
| https:// \* . Azure-Devices.NET              | Přístup k IoT Hub (povinné)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Vzory adres URL pro bránu pro Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/azure-stack-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Vzory adres URL pro výpočetní prostředky pro Azure Government

| Vzor adresy URL                      | Součást nebo funkce                     |  
|----------------------------------|---------------------------------------------|
| https: \/ /MCR.Microsoft.com<br></br>https:// \* . CDN.mscr.com | Microsoft Container Registry (povinné)               |
| https:// \* . Azure-Devices.us              | Přístup k IoT Hub (povinné)           |
| https:// \* . azurecr.us                    | Registry kontejnerů pro osobní a třetí strany (volitelné) | 

## <a name="internet-bandwidth"></a>Šířka pásma internetu

[!INCLUDE [Internet bandwidth](../../includes/azure-stack-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Požadavky na výpočet velikosti

Využijte své zkušenosti s vývojem a testováním svého řešení, abyste měli dostatek kapacity na zařízení Azure Stack Edge pro R a dosáhli jste optimálního výkonu vašeho zařízení.

Mezi faktory, které byste měli vzít v úvahu, patří:

- **Specifické pro kontejner** – Zamyslete se nad následujícími.

    - Kolik kontejnerů je ve vašem zatížení? Je možné, že máte spoustu lehkých kontejnerů oproti několika prostředkům náročným na prostředky.
    - Jaké jsou prostředky přidělené těmto kontejnerům oproti prostředkům, které spotřebovávají?
    - Kolik vrstev vaše kontejnery sdílí?
    - Existují nepoužívané kontejnery? Zastavený kontejner stále zabírá místo na disku.
    - V jakém jazyce jsou kontejnery zapsané?
- **Velikost zpracovaných dat** – kolik dat budou vaše kontejnery zpracovávat? Budou tato data spotřebovávat místo na disku, jinak budou data zpracována v paměti?
- **Očekávaný výkon** – Jaké jsou požadované charakteristiky výkonu vašeho řešení? 

Pro pochopení a vylepšení výkonu řešení můžete použít:

- Výpočetní metrika je k dispozici v Azure Portal. Přejít na svůj prostředek Azure Stack Edge pro R a pak přejít na **monitorování > metriky**. Seznamte se s dostupnými prostředky a s tím, jak se prostředky využívaly, najdete v procentech **využití hraničních výpočetních paměťových** a **hraničních procesorů** .
- [Příkazy monitorování dostupné prostřednictvím rozhraní PowerShell zařízení](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge).

Nakonec se ujistěte, že vaše řešení ověřujete ve své datové sadě a vyhodnotit výkon v Azure Stack Edge pro R před nasazením v produkčním prostředí.

## <a name="next-step"></a>Další krok

- [Nasazení Azure Stack Edge pro R](azure-stack-edge-pro-r-deploy-prep.md)