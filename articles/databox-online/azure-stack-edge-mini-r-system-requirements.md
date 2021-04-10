---
title: Microsoft Azure Stack minimální požadavky na systém na hraničních zařízeních systému R | Microsoft Docs
description: Seznamte se s požadavky na software a sítě pro Azure Stack hraničních zařízení ve Mini R
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: alkohli
ms.openlocfilehash: 829f89f716a29f09f88a2e02b257aba1b207d072
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102634188"
---
# <a name="azure-stack-edge-mini-r-system-requirements"></a>Azure Stack minimální požadavky na systém na hraničních zařízeních na hranici R

Tento článek popisuje důležité systémové požadavky pro řešení Microsoft Azure Stack Edge Mini R a klienty, kteří se připojují k Azure Stack Edge Mini R. Doporučujeme, abyste pečlivě prostudovali informace před nasazením Azure Stackového Miniu na hraničních zařízeních. V případě potřeby můžete v průběhu nasazení a následné operace přejít zpět na tyto informace.

Požadavky na systém pro Azure Stackovou hranici na Mini R zahrnují:

- **Požadavky na software pro hostitele** – popisuje podporované platformy, prohlížeče pro místní uživatelské rozhraní konfigurace, klienty SMB a všechny další požadavky na klienty, kteří přistupují k zařízení.
- **Požadavky na síť pro zařízení** – poskytuje informace o požadavcích na síť pro provoz fyzického zařízení.

## <a name="supported-os-for-clients-connected-to-device"></a>Podporovaný operační systém pro klienty připojené k zařízení

[!INCLUDE [Supported OS for clients connected to device](../../includes/azure-stack-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Podporované protokoly pro klienty, kteří přistupují k zařízení

[!INCLUDE [Supported protocols for clients accessing device](../../includes/azure-stack-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Podporované účty úložiště

[!INCLUDE [Supported storage accounts](../../includes/azure-stack-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-edge-storage-accounts"></a>Podporované hraniční účty úložiště

Rozhraní REST podporuje následující účty úložiště Edge. Na zařízení se vytvoří hraniční účty úložiště. Další informace najdete v tématu [účty úložiště Edge](azure-stack-edge-gpu-manage-storage-accounts.md#about-edge-storage-accounts) .

|Typ  |Účet úložiště  |Komentáře  |
|---------|---------|---------|
|Standard     |GPv1: objekt blob bloku         |         |


* Objekty blob stránky a soubory Azure se v tuto chvíli nepodporují.

## <a name="supported-local-azure-resource-manager-storage-accounts"></a>Podporované místní účty úložiště Azure Resource Manager

Tyto účty úložiště jsou prostřednictvím místních rozhraní API zařízení, když jste připojeni k místnímu Azure Resource Manager. Podporují se tyto účty úložiště:

|Typ  |Účet úložiště  |Komentáře  |
|---------|---------|---------|
|Standard     |GPv1: objekt blob bloku, objekt blob stránky         | Typ SKU je Standard_LRS        |
|Premium   |GPv1: objekt blob bloku, objekt blob stránky         |Typ SKU je Premium_LRS         |


## <a name="supported-storage-types"></a>Podporované typy úložiště

[!INCLUDE [Supported storage types](../../includes/azure-stack-edge-gateway-supported-storage-types.md)]


## <a name="supported-browsers-for-local-web-ui"></a>Podporované prohlížeče pro místní webové uživatelské rozhraní

[!INCLUDE [Supported browsers for local web UI](../../includes/azure-stack-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Požadavky na porty sítě

### <a name="port-requirements-for-azure-stack-edge-mini-r"></a>Požadavky na porty pro Azure Stack hraniční Mini R

Následující tabulka uvádí porty, které je třeba v bráně firewall otevřít, aby umožňovaly provoz SMB, cloudu nebo správy. V této tabulce se *v* nebo *příchozí* odkazuje na směr, ze kterého příchozí klient žádá o přístup k vašemu zařízení. *Výstupní* nebo *odchozí* odkazuje na směr, ve kterém vaše Azure Stack hraniční Mini R zařízení odesílá data externě, mimo nasazení, například odchozí na Internet.

[!INCLUDE [Port configuration for device](../../includes/azure-stack-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Požadavky na port pro IoT Edge

Azure IoT Edge umožňuje odchozí komunikaci z místního hraničního zařízení do cloudu Azure pomocí podporovaných protokolů IoT Hub. Příchozí komunikace se vyžaduje jenom u konkrétních scénářů, ve kterých musí Azure IoT Hub předávat zprávy do Azure IoT Edge zařízení (třeba zasílání zpráv z cloudu do zařízení).

Použijte následující tabulku pro konfiguraci portů pro servery hostující Azure IoT Edge Runtime:

| Číslo portu | V nebo ven | Rozsah portů | Vyžadováno | Pokyny |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Out       | Síť WAN        | Yes      | Odchozí otevření pro IoT Edge zřizování. Tato konfigurace se vyžaduje při použití ručních skriptů nebo služby Azure IoT Device Provisioning (DPS).|

Pokud chcete získat úplné informace, přečtěte si [pravidla brány firewall a konfigurace portů pro nasazení IoT Edge](../iot-edge/troubleshoot.md).

## <a name="url-patterns-for-firewall-rules"></a>Vzory adres URL pro pravidla brány firewall

Správci sítě mohou často konfigurovat Rozšířená pravidla brány firewall na základě vzorů adres URL pro filtrování příchozího a odchozího provozu. Vaše Azure Stacka na minimu zařízení R a službu závisí na dalších aplikacích společnosti Microsoft, jako jsou Azure Service Bus, Azure Active Directory Access Control, účtech úložiště a Microsoft Updatech serverech. Ke konfiguraci pravidel brány firewall lze použít vzory adres URL přidružené k těmto aplikacím. Je důležité pochopit, že se vzory adres URL přidružené k těmto aplikacím můžou změnit. Tyto změny vyžadují, aby správce sítě sledoval a aktualizoval pravidla brány firewall pro Azure Stack hraničních zařízení ve zkráceném formátu R, a to v případě potřeby.

Doporučujeme, abyste nastavili pravidla brány firewall pro odchozí přenosy, a to na základě Azure Stackch adresních Mini R s pevnými IP adresami, ve většině případů svobodně. Pomocí níže uvedených informací ale můžete nastavit Rozšířená pravidla brány firewall, která jsou potřeba k vytváření zabezpečených prostředí.

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

Využijte své zkušenosti při vývoji a testování vašeho řešení, abyste měli dostatek kapacity na Azure Stack hraničních zařízeních v R, a získáte optimální výkon zařízení.

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
- Příkazy monitorování dostupné prostřednictvím rozhraní PowerShell zařízení, například:

    - `dkr` Statistika pro získání živého streamu statistických údajů o využití prostředků kontejnerů Příkaz podporuje procesor, využití paměti, limit paměti a metriky v/v sítě.
    - `dkr system df` získat informace týkající se množství využitého místa na disku. 
    - `dkr image [prune]` k vyčištění nepoužívaných imagí a uvolnění místa.
    - `dkr ps --size` Chcete-li zobrazit přibližnou velikost běžícího kontejneru. 

    Další informace o dostupných příkazech najdete v části [ ladění problémů Kubernetes](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge).

Nakonec se ujistěte, že vaše řešení ověřujete ve své datové sadě, a před nasazením do produkčního prostředí vyhodnotit výkon na Azure Stack Edge Mini R.

## <a name="next-step"></a>Další krok

- [Nasazení Azure Stack hraničních zařízení na Mini R](azure-stack-edge-placeholder.md)
