---
title: Požadavky na systém Microsoft Azure Data Box Edge| Dokumenty společnosti Microsoft
description: Informace o softwaru a síťových požadavcích pro azure data box edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: 458c062eef011363724cb894ce67ba75181ba8ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260225"
---
# <a name="azure-data-box-edge-system-requirements"></a>Požadavky na systém Azure Data Box Edge

Tento článek popisuje důležité systémové požadavky pro vaše řešení Microsoft Azure Data Box Edge a pro klienty, kteří se připojují k Azure Data Box Edge. Doporučujeme, abyste si tyto informace před nasazením datové schránky Edge pečlivě prostudovali. Můžete odkazovat zpět na tyto informace podle potřeby během nasazení a následné operace.

Systémové požadavky pro datový schránka Edge zahrnují:

- **Požadavky na software pro hostitele** – popisuje podporované platformy, prohlížeče pro místní konfigurační ui, klienty SMB a všechny další požadavky pro klienty, kteří přistupují k zařízení.
- **Síťové požadavky pro zařízení** - poskytuje informace o všech síťových požadavcích na provoz fyzického zařízení.

## <a name="supported-os-for-clients-connected-to-device"></a>Podporovaný operační systém pro klienty připojené k zařízení

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Podporované protokoly pro klienty přistupující k zařízení

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Podporované účty úložiště

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Podporované typy úložiště

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Podporované prohlížeče pro místní webové uživatelské prostředí

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Požadavky na síťový port

### <a name="port-requirements-for-data-box-edge"></a>Požadavky na port pro okraj datové schránky

V následující tabulce jsou uvedeny porty, které je třeba otevřít v bráně firewall, aby bylo možné provádět přenosy s konzolí SMB, cloudnebo správu. V této tabulce *in* nebo *příchozí* odkazuje na směr, ze kterého příchozí klient požaduje přístup k vašemu zařízení. *Odchozí* nebo *odchozí* označuje směr, ve kterém vaše zařízení Data Box Edge odesílá data externě, mimo nasazení, například odchozí do internetu.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Požadavky na port pro IoT Edge

Azure IoT Edge umožňuje odchozí komunikaci z místního zařízení Edge do cloudu Azure pomocí podporovaných protokolů IoT Hub. Příchozí komunikace je vyžadována jenom pro konkrétní scénáře, kde Azure IoT Hub potřebuje tlačit zprávy do zařízení Azure IoT Edge (například zasílání zpráv z cloudu na zařízení).

Pro konfiguraci portů pro servery hostující runtime Azure IoT Edge použijte následující tabulku:

| Port č. | Dovnitř nebo ven | Rozsah portu | Požaduje se | Doprovodné materiály |
|----------|-----------|------------|----------|----------|
| Protokol TCP 443 (HTTPS)| Out       | Síť WAN        | Ano      | Odchozí otevřené pro zřizování okraje IoT. Tato konfigurace je vyžadována při použití ruční skripty nebo Azure IoT device Provisioning Service (DPS).|

Úplné informace naleznete v části [Brána firewall a pravidla konfigurace portu pro nasazení IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Vzory adres URL pro pravidla brány firewall

Správci sítě mohou často konfigurovat pokročilá pravidla brány firewall na základě vzorů adres URL tak, aby filtrovali příchozí a odchozí přenosy. Zařízení A služba Data Box Edge závisí na jiných aplikacích Microsoftu, jako je Azure Service Bus, Azure Active Directory Access Control, účty úložiště a servery Microsoft Update. Vzory adres URL přidružené k těmto aplikacím lze použít ke konfiguraci pravidel brány firewall. Je důležité si uvědomit, že vzory adres URL přidružené k těmto aplikacím se mohou změnit. Tyto změny vyžadují, aby správce sítě monitoroval a aktualizoval pravidla brány firewall pro funkci Data Box Edge podle potřeby.

Doporučujeme nastavit pravidla brány firewall pro odchozí provoz na základě pevných IP adres Data Box Edge, ve většině případů liberálně. Níže uvedené informace však můžete použít k nastavení rozšířených pravidel brány firewall, která jsou potřebná k vytvoření zabezpečeného prostředí.

> [!NOTE]
> - Ip adresy zařízení (zdroje) by měly být vždy nastaveny na všechna síťová rozhraní s podporou cloudu.
> - Cílové IP adresy by měly být nastaveny na [rozsahy IP adres datového centra Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Vzory adres URL pro funkci brány

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Vzory adres URL pro výpočetní funkci

| Vzor adresy URL                      | Součást nebo funkce                     |   
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Registr kontejnerů Společnosti Microsoft (povinné)               |
| https://\*.azurecr.io                     | Osobní registry kontejnerů a registrů kontejnerů třetích stran (nepovinné) | 
| https://\*azure-devices.net              | Přístup k ioT hubu (povinné)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Vzory adres URL pro bránu pro Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Vzory adres URL pro výpočetní prostředky pro Azure Government

| Vzor adresy URL                      | Součást nebo funkce                     |  
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.com | Registr kontejnerů Společnosti Microsoft (povinné)               |
| https://\*.azure-devices.us              | Přístup k ioT hubu (povinné)           |
| https://\*.azurecr.us                    | Osobní registry kontejnerů a registrů kontejnerů třetích stran (nepovinné) | 

## <a name="internet-bandwidth"></a>Šířka pásma internetu

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Důležité informace o velikosti výpočtu

Využijte své zkušenosti při vývoji a testování svého řešení, abyste zajistili dostatečnou kapacitu na zařízení Data Box Edge a získáte optimální výkon ze zařízení.

Mezi faktory, které byste měli zvážit, patří:

- **Specifika kontejneru** - Přemýšlejte o následujících.

    - Kolik kontejnerů je ve vaší pracovní zátěži? Můžete mít spoustu lehkých kontejnerů versus několik prostředků náročných.
    - Jaké prostředky jsou přiděleny těmto kontejnerům oproti jaké jsou prostředky, které spotřebovávají?
    - Kolik vrstev sdílejí vaše kontejnery?
    - Jsou tam nepoužité kontejnery? Zastavený kontejner stále zabírá místo na disku.
    - V jakém jazyce jsou vaše kontejnery napsány?
- **Velikost zpracovávaných dat** – kolik dat budou vaše kontejnery zpracovávat? Budou tato data spotřebovávat místo na disku nebo budou data zpracována v paměti?
- **Očekávaný výkon** - Jaké jsou požadované charakteristiky výkonu vašeho řešení? 

Chcete-li pochopit a upřesnit výkon vašeho řešení, můžete použít:

- Výpočetní metriky dostupné na webu Azure Portal. Přejděte do zdroje Data Box Edge a přejděte na **> metriky monitorování**. Podívejte se na **edge compute – využití paměti** a edge výpočetní – procento **procesoru** pochopit dostupné prostředky a jak jsou prostředky stále spotřebovává.
- Příkazy monitorování dostupné prostřednictvím rozhraní prostředí PowerShell zařízení, například:

    - `dkrdbe stats`chcete-li získat živý proud statistiky využití prostředků kontejnerů. Příkaz podporuje metriky procesoru, využití paměti, omezení paměti a vi síťových vi.
    - `dkrdbe system df`získáte informace o množství využitého místa na disku. 
    - `dkrdbe image prune`vyčištění nepoužívaných obrázků a uvolnění místa.
    - `dkrdbe ps --size`zobrazení přibližné velikosti spuštěného kontejneru. 

    Další informace o dostupných příkazech naleznete v části [Sledování a řešení potíží s výpočetními moduly](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules).

Nakonec se ujistěte, že ověříte své řešení v datové sadě a kvantifikujete výkon na okraji datové schránky před nasazením v produkčním prostředí.


## <a name="next-step"></a>Další krok

- [Nasazení azure data boxu Edge](data-box-edge-deploy-prep.md)
