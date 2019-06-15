---
title: Zabezpečení služby Azure Data Box Edge | Dokumentace Microsoftu
description: Popisuje funkce zabezpečení a ochrana osobních údajů, které chrání váš Azure Data Box hraniční zařízení, služby a data v místním prostředí i v cloudu.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 8823aebe17a5446b3c507878833c2525c338dde1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64717999"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Azure Data Box Edge zabezpečení a ochranu dat.

Zabezpečení představuje závažný problém při přijetí nové technologie, zejména v případě, že tato technologie se používá s důvěrných nebo vlastnických dat. Azure Data Box Edge pomůže vám zajistit pouze oprávněné entity můžete zobrazit, upravit nebo odstranit data.

Tento článek popisuje funkce zabezpečení okraj pole dat, které pomáhají chránit všechny komponenty řešení a data jsou v nich uložené.

Azure Data Box Edge se skládá z čtyři hlavní součásti, které spolu interagují:

- **Služba okraj pole data, která hostované v Azure**. Prostředek správy, který použijete k vytvoření objednávky zařízení nakonfigurovat zařízení a potom sledovat pořadí do konce.
- **Zařízení data Box Edge**. Zařízení pro přenos dodávané, takže můžete importovat místních dat do Azure.
- **Klienty a hostitele připojené k zařízení**. Klienti ve vaší infrastruktuře, připojte se k zařízení Data Box Edge a obsahující data, která se dají chránit.
- **Cloudové úložiště**. Umístění v cloudové platformy Azure, kde jsou uložená data. Toto umístění je obvykle účtu úložiště propojeném prostředku okraj pole dat, který vytvoříte.

## <a name="data-box-edge-service-protection"></a>Ochrana služby Edge pole dat

Služba Data Box Edge je služba pro správu, která je hostovaná v Azure. Služba se používá ke konfiguraci a správě zařízení.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Ochrana dat pole hraniční zařízení

Data Box hraniční zařízení je v místním zařízení, který pomáhá získat z dat o zpracování místně a odesílá je do Azure. Vaše zařízení:

- Potřebuje aktivační klíč pro přístup ke službě Data Box Edge.
- Je chráněn za všech okolností heslo zařízení.
- Je zařízení uzamknuté. Zařízení řadiče pro správu desky a systému BIOS jsou chráněné heslem. Systém BIOS je chráněn omezený přístup uživatelů.
- Má povolené zabezpečené spouštění.
- Spuštění Windows Defenderu Device Guard. Device Guard umožňuje spouštět jenom důvěryhodné aplikace, které definujete v zásady integrity kódu.

### <a name="protect-the-device-via-activation-key"></a>Ochrana zařízení pomocí aktivační kód

Pouze autorizovaného zařízení Data Box Edge je povolené pro připojení služby Data Box Edge, který vytvoříte ve svém předplatném Azure. K ověření zařízení, budete muset použít aktivační kód k aktivaci zařízení pomocí služby Data Box Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Další informace najdete v tématu [získat aktivační klíč](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Ochrana zařízení pomocí hesla

Hesla se ujistěte, že jenom Autorizovaní uživatelé můžou přistupovat k datům. Data Box hraniční zařízení spuštění v uzamčeném stavu.

Můžete:

- Připojení k místní webové uživatelské rozhraní zařízení přes prohlížeč a zadejte heslo pro přihlášení k zařízení.
- Vzdálené připojení k rozhraní PowerShell zařízení prostřednictvím protokolu HTTP. Ve výchozím nastavení je zapnutá Vzdálená správa. Zadejte heslo zařízení pro přihlášení k zařízení. Další informace najdete v tématu [připojit vzdáleně na vaše zařízení Data Box Edge](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Použití místní webové uživatelské rozhraní pro [změnit heslo](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Pokud změníte heslo, nezapomeňte informovat všechny vzdálení uživatelé tak nebudou mít potíže při přihlašování.

## <a name="protect-your-data"></a>Ochrana dat

Tato část popisuje funkce zabezpečení okraj pole dat, které chrání data během přenosu a uložené.

### <a name="protect-data-at-rest"></a>Ochrana dat v klidovém stavu

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Chránit data na cestě

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Ochrana dat přes účet úložiště

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Otočit o a poté [synchronizovat klíče účtu úložiště](data-box-edge-manage-shares.md#sync-storage-keys) pravidelně k ochraně vašeho účtu úložiště před neoprávněnými uživateli.

## <a name="manage-personal-information"></a>Správa osobních údajů

Služba Data Box Edge shromažďuje osobní informace v následujících scénářích:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Chcete-li zobrazit seznam uživatelů, kteří mohou přistupovat k nebo odstranit sdílenou složku, postupujte podle kroků v [spravovat sdílené složky na okraji pole Data](data-box-edge-manage-shares.md).

Další informace najdete v tématu Zásady ochrany osobních údajů společnosti Microsoft na [centrum](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Další postup

[Nasazení zařízení Data Box Edge](data-box-edge-deploy-prep.md)
