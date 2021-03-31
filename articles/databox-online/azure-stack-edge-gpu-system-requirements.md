---
title: Požadavky na systém Microsoft Azure Stack Edge | Microsoft Docs
description: Seznamte se s požadavky na systém pro vaše řešení Microsoft Azure Stack Edge a u klientů, kteří se připojují k Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: alkohli
ms.custom: contperf-fy21q3
ms.openlocfilehash: 67de5cce3bd5a4ca2b383e2809eb237a6e753bf5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577086"
---
# <a name="system-requirements-for-azure-stack-edge-pro-with-gpu"></a>Požadavky na systém pro Azure Stack Edge pro s grafickým procesorem 

Tento článek popisuje důležité systémové požadavky pro řešení grafického procesoru Microsoft Azure Stack Edge pro a klienty, kteří se připojují k Azure Stack Edge pro. Doporučujeme pečlivě zkontrolovat informace před nasazením Azure Stack Edge pro. V případě potřeby můžete v průběhu nasazení a následné operace přejít zpět na tyto informace.

Mezi požadavky na systém Azure Stack Edge pro:

- **Požadavky na software pro hostitele** – popisuje podporované platformy, prohlížeče pro místní uživatelské rozhraní konfigurace, klienty SMB a všechny další požadavky na klienty, kteří přistupují k zařízení.
- **Požadavky na síť pro zařízení** – poskytuje informace o požadavcích na síť pro provoz fyzického zařízení.

## <a name="supported-os-for-clients-connected-to-device"></a>Podporovaný operační systém pro klienty připojené k zařízení

[!INCLUDE [Supported OS for clients connected to device](../../includes/azure-stack-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Podporované protokoly pro klienty, kteří přistupují k zařízení

[!INCLUDE [Supported protocols for clients accessing device](../../includes/azure-stack-edge-gateway-supported-client-protocols.md)]

## <a name="supported-azure-storage-accounts"></a>Podporované účty Azure Storage

[!INCLUDE [Supported storage accounts](../../includes/azure-stack-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-edge-storage-accounts"></a>Podporované hraniční účty úložiště

Rozhraní REST podporuje následující účty úložiště Edge. Na zařízení se vytvoří hraniční účty úložiště. Další informace najdete v tématu [účty úložiště Edge](azure-stack-edge-gpu-manage-storage-accounts.md#about-edge-storage-accounts).

|Typ  |Účet úložiště  |Komentáře  |
|---------|---------|---------|
|Standard     |GPv1: objekt blob bloku         |         |

* Objekty blob stránky a soubory Azure se v tuto chvíli nepodporují.

## <a name="supported-local-azure-resource-manager-storage-accounts"></a>Podporované místní účty úložiště Azure Resource Manager

Tyto účty úložiště se vytvářejí prostřednictvím místních rozhraní API zařízení, když se připojujete k místnímu Azure Resource Manager. Podporují se tyto účty úložiště:

|Typ  |Účet úložiště  |Komentáře  |
|---------|---------|---------|
|Standard     |GPv1: objekt blob bloku, objekt blob stránky        | Typ SKU je Standard_LRS       |
|Premium     |GPv1: objekt blob bloku, objekt blob stránky        | Typ SKU je Premium_LRS        |


## <a name="supported-storage-types"></a>Podporované typy úložiště

[!INCLUDE [Supported storage types](../../includes/azure-stack-edge-gateway-supported-storage-types.md)]


## <a name="supported-browsers-for-local-web-ui"></a>Podporované prohlížeče pro místní webové uživatelské rozhraní

[!INCLUDE [Supported browsers for local web UI](../../includes/azure-stack-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Požadavky na porty sítě

### <a name="port-requirements-for-azure-stack-edge-pro"></a>Požadavky na port pro Azure Stack Edge pro

Následující tabulka uvádí porty, které je třeba v bráně firewall otevřít, aby umožňovaly provoz SMB, cloudu nebo správy. V této tabulce se *v* nebo *příchozí* odkazuje na směr, ze kterého příchozí klient žádá o přístup k vašemu zařízení. *Výstupní* nebo *odchozí* znamená směr, ve kterém vaše zařízení Azure Stack Edge pro odesílá data externě, mimo nasazení, například odchozí na Internet.

[!INCLUDE [Port configuration for device](../../includes/azure-stack-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Požadavky na port pro IoT Edge

Azure IoT Edge umožňuje odchozí komunikaci z místního hraničního zařízení do cloudu Azure pomocí podporovaných protokolů IoT Hub. Příchozí komunikace se vyžaduje jenom u konkrétních scénářů, ve kterých musí Azure IoT Hub předávat zprávy do Azure IoT Edge zařízení (třeba zasílání zpráv z cloudu do zařízení).

Použijte následující tabulku pro konfiguraci portů pro servery hostující Azure IoT Edge Runtime:

| Číslo portu | V nebo ven | Rozsah portů | Vyžadováno | Pokyny |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Out       | Síť WAN        | Yes      | Odchozí otevření pro IoT Edge zřizování. Tato konfigurace se vyžaduje při použití ručních skriptů nebo služby Azure IoT Device Provisioning (DPS).|

Pokud chcete získat úplné informace, přečtěte si [pravidla brány firewall a konfigurace portů pro nasazení IoT Edge](../iot-edge/troubleshoot.md).

## <a name="url-patterns-for-firewall-rules"></a>Vzory adres URL pro pravidla brány firewall

Správci sítě mohou často konfigurovat Rozšířená pravidla brány firewall na základě vzorů adres URL pro filtrování příchozího a odchozího provozu. Vaše zařízení Azure Stack Edge pro a službu závisí na dalších aplikacích společnosti Microsoft, jako jsou Azure Service Bus, Azure Active Directory Access Control, účtech úložiště a Microsoft Updatech serverech. Ke konfiguraci pravidel brány firewall lze použít vzory adres URL přidružené k těmto aplikacím. Je důležité pochopit, že se vzory adres URL přidružené k těmto aplikacím můžou změnit. Tyto změny vyžadují, aby správce sítě sledoval a aktualizoval pravidla brány firewall pro Azure Stack Edge pro a v případě potřeby.

Doporučujeme, abyste nastavili pravidla brány firewall pro odchozí přenosy na základě pevných IP adres Azure Stack Edge pro, a to ve většině případů svobodně. Pomocí níže uvedených informací ale můžete nastavit Rozšířená pravidla brány firewall, která jsou potřeba k vytváření zabezpečených prostředí.

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

Využijte své zkušenosti při vývoji a testování vašeho řešení, abyste měli dostatek kapacity na zařízení Azure Stack Edge pro, a získáte optimální výkon ze zařízení.

Mezi faktory, které byste měli vzít v úvahu, patří:

- **Specifické pro kontejner** – Zamyslete se nad následujícími.

    - Jaké jsou vaše kontejnery? Kolik paměti, úložiště a procesoru je váš kontejner náročný?
    - Kolik kontejnerů je ve vašem zatížení? Je možné, že máte spoustu lehkých kontejnerů oproti několika prostředkům náročným na prostředky.
    - Jaké jsou prostředky přidělené těmto kontejnerům a jaké prostředky využívají (nároky)?
    - Kolik vrstev vaše kontejnery sdílí? Image kontejneru jsou sada souborů uspořádaných do zásobníku vrstev. V případě image kontejneru určete, kolik vrstev a jejich velikosti se mají vypočítat spotřebu prostředků.
    - Existují nepoužívané kontejnery? Zastavený kontejner stále zabírá místo na disku.
    - V jakém jazyce jsou kontejnery zapsané?
- **Velikost zpracovaných dat** – kolik dat budou vaše kontejnery zpracovávat? Budou tato data spotřebovávat místo na disku, jinak budou data zpracována v paměti?
- **Očekávaný výkon** – Jaké jsou požadované charakteristiky výkonu vašeho řešení? 

Pro pochopení a vylepšení výkonu řešení můžete použít:

- Výpočetní metrika je k dispozici v Azure Portal. Přejít na prostředek Azure Stack Edge a pak přejít na **monitorování > metriky**. Seznamte se s dostupnými prostředky a s tím, jak se prostředky využívaly, najdete v procentech **využití hraničních výpočetních paměťových** a **hraničních procesorů** .
- Pokud chcete monitorovat a řešit problémy s výpočetními moduly, přečtěte si [témata ladění Kubernetes problémů](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge).

Nakonec se ujistěte, že vaše řešení ověříte ve své datové sadě a vyhodnotit výkon na Azure Stack Edge pro před nasazením v produkčním prostředí.

## <a name="next-step"></a>Další krok

- [Nasazení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-prep.md)