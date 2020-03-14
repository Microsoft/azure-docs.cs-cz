---
title: Microsoft Azure Data Box Edge požadavky na systém | Microsoft Docs
description: Seznamte se s požadavky na software a sítě pro vaše Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: 458c062eef011363724cb894ce67ba75181ba8ba
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260225"
---
# <a name="azure-data-box-edge-system-requirements"></a>Azure Data Box Edge požadavky na systém

Tento článek popisuje důležité systémové požadavky pro vaše Microsoft Azure Data Box Edge řešení a klienty, kteří se připojují k Azure Data Box Edge. Před nasazením Data Box Edge doporučujeme pečlivě zkontrolovat informace. V případě potřeby můžete v průběhu nasazení a následné operace přejít zpět na tyto informace.

Požadavky na systém pro Data Box Edge zahrnují:

- **Požadavky na software pro hostitele** – popisuje podporované platformy, prohlížeče pro místní uživatelské rozhraní konfigurace, klienty SMB a všechny další požadavky na klienty, kteří přistupují k zařízení.
- **Požadavky na síť pro zařízení** – poskytuje informace o požadavcích na síť pro provoz fyzického zařízení.

## <a name="supported-os-for-clients-connected-to-device"></a>Podporovaný operační systém pro klienty připojené k zařízení

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Podporované protokoly pro klienty, kteří přistupují k zařízení

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Účty úložiště podporuje

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Typy podporovaných úložišť

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Podporované prohlížeče pro místní webové uživatelské rozhraní

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Požadavky na porty sítě

### <a name="port-requirements-for-data-box-edge"></a>Požadavky na port pro Data Box Edge

Následující tabulka uvádí porty, které je třeba v bráně firewall otevřít, aby umožňovaly provoz SMB, cloudu nebo správy. V této tabulce se *v* nebo *příchozí* odkazuje na směr, ze kterého příchozí klient žádá o přístup k vašemu zařízení. *Výstupní* nebo *odchozí* odkazuje na směr, ve kterém vaše zařízení data box Edge odesílá data externě, mimo nasazení, například odchozí na Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Požadavky na port pro IoT Edge

Azure IoT Edge umožňuje odchozí komunikaci z místního hraničního zařízení do cloudu Azure pomocí podporovaných protokolů IoT Hub. Příchozí komunikace se vyžaduje jenom u konkrétních scénářů, ve kterých musí Azure IoT Hub předávat zprávy do Azure IoT Edge zařízení (třeba zasílání zpráv z cloudu do zařízení).

Použijte následující tabulku pro konfiguraci portů pro servery hostující Azure IoT Edge Runtime:

| Číslo portu | V nebo ven | Rozsah portů | Požadováno | Doprovodné materiály |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Mimo       | WAN        | Ano      | Odchozí otevření pro IoT Edge zřizování. Tato konfigurace je nutná, pokud používáte ruční skripty nebo Azure IoT zařízení zřizování služby (DPS).|

Pokud chcete získat úplné informace, přečtěte si [pravidla brány firewall a konfigurace portů pro nasazení IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Vzory adres URL pro pravidla brány firewall

Správci sítě mohou často konfigurovat Rozšířená pravidla brány firewall na základě vzorů adres URL pro filtrování příchozího a odchozího provozu. Vaše zařízení Data Box Edge a služba závisí na dalších aplikacích společnosti Microsoft, například Azure Service Bus, Azure Active Directory Access Control, účtech úložiště a Microsoft Updatech serverech. Ke konfiguraci pravidel brány firewall lze použít vzory adres URL přidružené k těmto aplikacím. Je důležité pochopit, že se vzory adres URL přidružené k těmto aplikacím můžou změnit. Tyto změny vyžadují, aby správce sítě sledoval a aktualizoval pravidla brány firewall pro váš Data Box Edge jako a v případě potřeby.

Doporučujeme, abyste nastavili pravidla brány firewall pro odchozí přenosy na základě Data Box Edge pevné IP adresy, a to ve většině případů. Pomocí níže uvedených informací ale můžete nastavit Rozšířená pravidla brány firewall, která jsou potřeba k vytváření zabezpečených prostředí.

> [!NOTE]
> - IP adresy zařízení (zdrojové) by se měly vždycky nastavit na všechna síťová rozhraní s podporou cloudu.
> - Cílové IP adresy by měly být nastavené na [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Vzory adres URL pro funkci brány

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Vzory adres URL pro funkci COMPUTE

| Vzor adresy URL                      | Součást nebo funkce                     |   
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*. cdn.mscr.io | Microsoft Container Registry (povinné)               |
| https://\*. azurecr.io                     | Registry kontejnerů pro osobní a třetí strany (volitelné) | 
| https://\*. azure-devices.net              | Přístup k IoT Hub (povinné)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Vzory adres URL pro bránu pro Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Vzory adres URL pro výpočetní prostředky pro Azure Government

| Vzor adresy URL                      | Součást nebo funkce                     |  
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*. cdn.mscr.com | Microsoft Container Registry (povinné)               |
| https://\*. azure-devices.us              | Přístup k IoT Hub (povinné)           |
| https://\*. azurecr.us                    | Registry kontejnerů pro osobní a třetí strany (volitelné) | 

## <a name="internet-bandwidth"></a>Šířka pásma internetu

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Požadavky na výpočet velikosti

Využijte své zkušenosti při vývoji a testování vašeho řešení, abyste měli jistotu, že máte na zařízení Data Box Edge dostatek kapacity a získáte optimální výkon vašeho zařízení.

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

- Výpočetní metrika je k dispozici v Azure Portal. Přejít na prostředek Data Box Edge a pak přejít na **monitorování > metriky**. Seznamte se s dostupnými prostředky a s tím, jak se prostředky využívaly, najdete v procentech **využití hraničních výpočetních paměťových** a **hraničních procesorů** .
- Příkazy monitorování dostupné prostřednictvím rozhraní PowerShell zařízení, například:

    - `dkrdbe stats` získat živý datový proud s údaji o využití prostředků kontejnerů. Příkaz podporuje procesor, využití paměti, limit paměti a metriky v/v sítě.
    - `dkrdbe system df` získat informace týkající se množství využitého místa na disku. 
    - `dkrdbe image prune` k vyčištění nepoužívaných imagí a uvolnění místa.
    - `dkrdbe ps --size` zobrazit přibližnou velikost běžícího kontejneru. 

    Další informace o dostupných příkazech najdete v tématu [monitorování a odstraňování potíží s výpočetními moduly](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules).

Nakonec se ujistěte, že vaše řešení ověřujete ve své datové sadě a vyhodnotit výkon na Data Box Edge před nasazením v produkčním prostředí.


## <a name="next-step"></a>Další krok

- [Nasazení Azure Data Box Edge](data-box-edge-deploy-prep.md)
