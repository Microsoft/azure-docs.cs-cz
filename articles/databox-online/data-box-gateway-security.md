---
title: Zabezpečení služby Azure Data Box brány | Dokumentace Microsoftu
description: Popisuje funkce zabezpečení a ochrana osobních údajů, které chrání vaše virtuální zařízení Azure Data Box brány, služby a data v místním prostředí i v cloudu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: b27b712128ddfbb07a7a7f68f616c20ec3fb53d3
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685792"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure Data Box brány zabezpečení a ochranu dat.

Zabezpečení představuje závažný problém při přijetí nové technologie, zejména v případě, že tato technologie se používá s důvěrných nebo vlastnických dat. Řešení Microsoft Azure Data Box Gateway pomáhá zajistit, že jen autorizované entity můžete zobrazit, upravit nebo odstranit data.

Tento článek popisuje funkce zabezpečení Azure Data Box brány, které pomáhají chránit všechny komponenty řešení a data uložená na ně.

Brána pole dat řešení se skládá z čtyři hlavní součásti, které spolu interagují:

- **Data Box brány služby hostované v Azure** – prostředku správy, který použijete k vytvoření objednávky zařízení, nakonfigurovat zařízení a pak sledovat pořadí do konce.
- **Zařízení data Box brány** – virtuálního zařízení, kterou jste zřídili v hypervisoru systému, který jste zadali. Tato virtuální zařízení se používá pro import místních dat do Azure.
- **Klienty a hostitele připojené k zařízení** – klienti ve vaší infrastruktuře, připojte se k zařízení Data Box brány a obsahující data, která se dají chránit.
- **Cloudové úložiště** – umístění v cloudu Azure, kam se data uloží. Toto umístění je obvykle účtu úložiště propojeném prostředku brány Data Box, kterou jste vytvořili.


## <a name="data-box-gateway-service-protection"></a>Ochrana služby pole brány dat

Služba Data Box brány je služba pro správu hostované v Microsoft Azure. Služba se používá ke konfiguraci a správě zařízení.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Ochrana zařízení data Box brány

Zařízení Data Box brány je virtuální zařízení zřízenou ve službě hypervisoru, který zadáte v místním systému. Zařízení pomáhá odesílání dat do Azure. Vaše zařízení:

- Potřebuje aktivační klíč pro přístup ke službě Data Box Edge/Data pole brány.
- Je chráněn za všech okolností heslo zařízení.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Ochrana zařízení pomocí aktivační kód

Pouze autorizovaného zařízení Data Box brány se moci připojit službu Brána pole dat, který jste vytvořili ve vašem předplatném Azure. K ověření zařízení, musíte použít aktivační kód k aktivaci zařízení pomocí služby Data Box brány.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Další informace najdete v části [získat aktivační klíč](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Ochrana zařízení pomocí hesla

Hesla Ujistěte se, že vaše data jsou přístupné pouze oprávněným uživatelům. Data Box brány zařízení spuštění v uzamčeném stavu.

Můžete:

- Připojení k místní webové uživatelské rozhraní zařízení přes prohlížeč a zadejte heslo pro přihlášení na zařízení.
- Vzdálené připojení k rozhraní PowerShell zařízení prostřednictvím protokolu HTTP. Ve výchozím nastavení je zapnutá Vzdálená správa. Zadejte heslo zařízení pro přihlášení na zařízení. Další informace najdete v části [připojit vzdáleně na vaše zařízení Data Box brány](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Použití místní webové uživatelské rozhraní pro [změnit heslo](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Pokud změníte heslo, nezapomeňte informovat všechny uživatele vzdáleného přístupu tak, aby nesetkávají přihlášením.


## <a name="protect-the-data"></a>Ochrana dat

Tato část popisuje funkce zabezpečení brána pole dat, které chrání přenášená data a uložená data.

### <a name="protect-data-at-rest"></a>Ochrana dat v klidovém stavu

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Chránit data na cestě

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Ochrana dat přes účet úložiště

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Otočit o a poté [synchronizovat klíče účtu úložiště](data-box-gateway-manage-shares.md#sync-storage-keys) pravidelně k zajištění, že váš účet úložiště není přístup neoprávnění uživatelé.

## <a name="manage-personal-information"></a>Správa osobních údajů

Služba Data Box brány shromažďuje osobní informace v následujících případech klíče:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Chcete-li zobrazit seznam uživatelů, kteří mohou přistupovat k nebo odstranit sdílenou složku, postupujte podle kroků v [spravovat sdílené složky na bráně dat pole](data-box-gateway-manage-shares.md).

Další informace najdete v Zásadách ochrany osobních údajů společnosti Microsoft v [Centru zabezpečení](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Další postup

[Nasazení zařízení Data Box brány](data-box-gateway-deploy-prep.md).
