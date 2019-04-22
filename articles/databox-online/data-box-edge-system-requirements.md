---
title: Požadavky na systém Microsoft Azure Data Box Edge | Dokumentace Microsoftu
description: Další informace o softwaru a síťové požadavky pro Azure Data Box hranici
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: d1e4af6e73c272a7ccc8996b0ccc854be64dd74b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006356"
---
# <a name="azure-data-box-edge-system-requirements"></a>Požadavky na systém Azure Data Box Edge

Tento článek popisuje důležité systémové požadavky pro vaše řešení Microsoft Azure Data Box Edge a klienti připojení k Azure Data Box Edge. Doporučujeme, abyste si informace o pečlivě před nasazením hranici pole Data. Můžete se vrátit zpět k těmto informacím podle potřeby během nasazení a následná operace.

Požadavky na systém pro okraji pole Data patří:

- **Požadavky na software pro hostitele** -popisuje podporované platformy, prohlížeče pro místní konfiguraci, uživatelského rozhraní, klienti SMB a veškeré další požadavky pro klienty, kteří přístup k zařízení.
- **Požadavky na síť pro zařízení** – poskytuje informace o žádné požadavky na síť pro operaci fyzickém zařízení.

## <a name="supported-os-for-clients-connected-to-device"></a>Podporovaný operační systém pro klienty připojené k zařízení

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Podporované protokoly klientům přístup k zařízení

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Účty úložiště podporuje

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Typy podporovaných úložišť

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Podporované prohlížeče pro místního webového uživatelského rozhraní

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Požadavky na síťové porty

### <a name="port-requirements-for-data-box-edge"></a>Požadavky na porty pro Data Box Edge

Následující tabulka uvádí porty, které je potřeba otevřít v bráně firewall povolit pro protokol SMB, cloud nebo přenos pro správu. V této tabulce *v* nebo *příchozí* odkazuje na směru, které mají přístup příchozí požadavky klienta na vaše zařízení. *Navýšení kapacity* nebo *odchozí* odkazuje na směru, ve kterém zařízení Data Box Edge odesílá data externě, nad rámec nasazení, například odchozí k Internetu.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Požadavky na porty pro IoT Edge

Azure IoT Edge umožňuje odchozí komunikaci z místní hraniční zařízení do cloudu Azure pomocí podporovaných protokolů služby IoT Hub. Příchozí komunikace je pouze požadovaná u konkrétních scénářů, kde je Azure IoT Hub potřeba doručit bez vyžádání zprávy na zařízení Azure IoT Edge (například v cloudu pro zasílání zpráv zařízení).

Použijte následující tabulku pro konfigurace portů pro servery, které hostují modul runtime Azure IoT Edge:

| Port č. | Snížení nebo navýšení kapacity | Rozsah portů | Požaduje se | Doprovodné materiály |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Výstup       | Síť WAN        | Ano      | Odchozí otevřen pro IoT Edge zřizování. Tato konfigurace je nutná, pokud používáte ruční skripty nebo Azure IoT zařízení zřizování služby (DPS).|

Podrobnější informace, přejděte na [brány Firewall a pravidla konfigurace portů pro nasazení IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Vzory adres URL pro pravidla brány firewall

Správci sítě můžou často konfigurace pravidla brány firewall na Upřesnit na základě vzory adres URL pro filtrování příchozího a odchozího provozu. Vaše zařízení Data Box Edge a služba závisí na jiné aplikace od Microsoftu, jako je Azure Service Bus, Azure Active Directory Access Control, účty úložiště a serverům Microsoft Update. Vzory adres URL, které jsou přidružené k těmto aplikacím je možné nakonfigurovat pravidla brány firewall. Je důležité pochopit, že můžete změnit vzory adres URL, které jsou přidružené k těmto aplikacím. Tyto změny vyžadují správce sítě, monitorovat a aktualizovat pravidla brány firewall na hranici jako pole Data a v případě potřeby.

Doporučujeme nastavit pravidla brány firewall pro odchozí provoz, založené na okraji pole Data liberally pevné IP adresy, ve většině případů. Ale můžete použít následující informace pro nastavení pravidla brány firewall na pokročilé, které budete potřebovat pro vytvoření zabezpečeného prostředí.

> [!NOTE]
> - Zařízení (zdroj) IP adresy musí být vždy nastavená na všechna rozhraní sítě povolenou podporu cloudu.
> - Cílové IP adresy musí být nastavená na [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Vzory adres URL pro funkce brány

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Vzory adres URL pro výpočetní funkce

| Vzor adresy URL                      | Komponenta nebo funkce                     |   
|----------------------------------|---------------------------------------------|
| https://mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Microsoft registru kontejneru (povinné)               |
| https://\*.azurecr.io                     | Registry kontejnerů osobní a třetí strany (volitelné) | 
| https://\*.azure-devices.net              | Přístup k službě IoT Hub (povinné)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Vzory adres URL pro bránu pro Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Vzory adres URL pro službu compute pro Azure Government

| Vzor adresy URL                      | Komponenta nebo funkce                     |  
|----------------------------------|---------------------------------------------|
| https://mcr.microsoft.com<br></br>https://\*.cdn.mscr.com | Microsoft registru kontejneru (povinné)               |
| https://\*.azure-devices.us              | Přístup k službě IoT Hub (povinné)           |
| https://\*.azurecr.us                    | Registry kontejnerů osobní a třetí strany (volitelné) | 

## <a name="internet-bandwidth"></a>Šířka pásma sítě Internet

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Výpočetní rozvahu dimenzování

Vaše zkušenosti při vývoji a testování vašeho řešení pomocí zajistit má dostatečnou kapacitu na vašem zařízení Data Box Edge a získali optimální výkon na vašem zařízení.

Mezi faktory, byste měli zvážit, patří:

- **Kontejner specifika** -uvažovat o následující.

    - Kolik kontejnerů jsou vaše úlohy? Můžete mít velké množství prostých kontejnerů a několik z nich náročné.
    - Co jsou prostředky přidělené tyto kontejnery a jaké jsou prostředky, které jsou využívání?
    - Kolik vrstvy kontejnery sdílet?
    - Existují nevyužité kontejnery? Kontejner zastavené stále zabírá místo na disku.
    - V jakém jazyce se kontejnery zapisují?
- **Objem dat zpracovaných** – kolik dat bude vaše kontejnery zpracování? Bude tato data využívat místo na disku nebo data se budou zpracovávat v paměti?
- **Očekávaný výkon** – jaké jsou požadované výkonové charakteristiky vašeho řešení? 

Chcete-li pochopit a vylepšit výkon vašeho řešení, můžete použít:

- Metriky výpočetní prostředky k dispozici na webu Azure Portal. Přejděte na váš prostředek okraj pole Data a potom přejděte ke **monitorování > metriky**. Podívejte se na **Edge využití služby compute – paměť** a **Edge compute – využití CPU** pochopit dostupné prostředky a jak jsou prostředky získávání využívat.
- Monitorování příkazů dostupných prostřednictvím rozhraní PowerShell zařízení, jako například:

    - `dkr` Statistika zobrazíte živý stream z kontejnerů statistiky o využití prostředků. Příkaz podporuje CPU, využití paměti, limit paměti a sítě metriky vstupně-výstupních operací.
    - `dkr system df` Chcete-li získat informace týkající se množství využité místo na disku. 
    - `dkr image [prune]` Odstraňte nepoužívané Image a uvolněte místo.
    - `dkr ps --size` Chcete-li zobrazit přibližné velikosti spuštěný kontejner. 

    Další informace o dostupných příkazech najdete v části [monitorování a řešení potíží s výpočetní moduly](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules).

A konečně Ujistěte se, že na vaší datové sadě ověřit vaše řešení a kvantifikovat výkonu na okraji pole Data před nasazením v produkčním prostředí.


## <a name="next-step"></a>Další krok

- [Nasazení Azure Data Box hranici](data-box-edge-deploy-prep.md)
