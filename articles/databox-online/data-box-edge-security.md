---
title: Zabezpečení služby Azure Data Box Edge | Dokumentace Microsoftu
description: Popisuje funkce zabezpečení a ochrana osobních údajů, které chrání vaše zařízení Azure Data Box Edge, služby a dat v místním prostředí i v cloudu.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 5316ddf9d456731f2789241434926366f732993a
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682096"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Azure Data Box Edge zabezpečení a ochranu dat.

Zabezpečení představuje závažný problém při přijetí nové technologie, zejména v případě, že tato technologie se používá s důvěrných nebo vlastnických dat. Řešení Microsoft Azure Data Box Edge pomáhá zajistit, že jen autorizované entity můžete zobrazit, upravit nebo odstranit data.

Tento článek popisuje funkce zabezpečení okraj pole dat, které pomáhají chránit všechny komponenty řešení a data uložená na ně.

Řešení Azure Data Box Edge se skládá z čtyři hlavní součásti, které spolu interagují:

- **Data Box Edge služby hostované v Azure** – prostředku správy, který použijete k vytvoření objednávky zařízení, nakonfigurovat zařízení a pak sledovat pořadí do konce.
- **Zařízení data Box Edge** – přenos zařízení dodávané do import místních dat do Azure.
- **Klienty a hostitele připojené k zařízení** – klienti ve vaší infrastruktuře, připojte se k zařízení Data Box Edge a obsahující data, která se dají chránit.
- **Cloudové úložiště** – umístění v cloudu Azure, kam se data uloží. Toto umístění je obvykle účtu úložiště propojeném prostředku okraj pole dat, který jste vytvořili.

## <a name="data-box-edge-service-protection"></a>Ochrana služby Edge pole dat

Služba Data Box Edge je služba pro správu hostované v Microsoft Azure. Služba se používá ke konfiguraci a správě zařízení.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Ochrana dat pole hraniční zařízení

Data Box hraniční zařízení je místní zařízení, která pomáhá transformovat data pomocí zpracování místně a odesílá je do Azure. Vaše zařízení:

- Potřebuje aktivační klíč pro přístup ke službě Data Box Edge.
- Je chráněn za všech okolností heslo zařízení.
- Je zařízení uzamknuté. Zařízení chráněném heslem s omezeným přístupem uživatele pro systém BIOS jsou BMC a systému BIOS.
- Má povolené zabezpečené spouštění.
- Spuštění Windows Defenderu Device Guard. Device Guard umožňuje spouštět jenom důvěryhodné aplikace, které definujete v zásadách integrity kódu.

### <a name="protect-the-device-via-activation-key"></a>Ochrana zařízení pomocí aktivační kód

Pouze autorizovaného zařízení Data Box Edge je povolené pro připojení služby Data Box Edge, který jste vytvořili ve vašem předplatném Azure. K ověření zařízení, musíte použít aktivační kód k aktivaci zařízení ve službě Data Box Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Další informace najdete v části [získat aktivační klíč](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Ochrana zařízení pomocí hesla

Hesla Ujistěte se, že vaše data jsou přístupné pouze oprávněným uživatelům. Data Box hraniční zařízení spuštění v uzamčeném stavu.

Můžete:

- Připojení k místní webové uživatelské rozhraní zařízení přes prohlížeč a zadejte heslo pro přihlášení na zařízení.
- Vzdálené připojení k rozhraní PowerShell zařízení prostřednictvím protokolu HTTP. Ve výchozím nastavení je zapnutá Vzdálená správa. Zadejte heslo zařízení pro přihlášení na zařízení. Další informace najdete v části [připojit vzdáleně na vaše zařízení Data Box Edge](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Použití místní webové uživatelské rozhraní pro [změnit heslo](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Pokud změníte heslo, nezapomeňte informovat všechny uživatele vzdáleného přístupu tak, aby nesetkávají přihlášením.

## <a name="protect-the-data"></a>Ochrana dat

Tato část popisuje funkce zabezpečení okraj pole dat, které chrání přenášená data a uložená data.

### <a name="protect-data-at-rest"></a>Ochrana dat v klidovém stavu

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Chránit data na cestě

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Ochrana dat přes účet úložiště

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Otočit o a poté [synchronizovat klíče účtu úložiště](data-box-edge-manage-shares.md#sync-storage-keys) pravidelně k zajištění, že váš účet úložiště není přístup neoprávnění uživatelé.

## <a name="manage-personal-information"></a>Správa osobních údajů

Služba Data Box Edge shromažďuje osobní informace v následujících případech klíče:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Chcete-li zobrazit seznam uživatelů, kteří mohou přistupovat k nebo odstranit sdílenou složku, postupujte podle kroků v [spravovat sdílené složky na okraji pole Data](data-box-edge-manage-shares.md).

Další informace najdete v Zásadách ochrany osobních údajů společnosti Microsoft v [Centru zabezpečení](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Další postup

[Nasazení zařízení Data Box Edge](data-box-edge-deploy-prep.md).
