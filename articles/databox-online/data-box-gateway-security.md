---
title: Zabezpečení služby Azure Data Box brány | Dokumentace Microsoftu
description: Popisuje funkce zabezpečení a ochrana osobních údajů, které chrání vaše virtuální zařízení Azure Data Box brány, služby a dat, v místním prostředí i v cloudu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 230d1a28ba15a8736e46c02cb08217a28fc18599
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754267"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure Data Box brány zabezpečení a ochranu dat.

Zabezpečení představuje závažný problém při přijetí nové technologie, zejména v případě, že tato technologie se používá s důvěrných nebo vlastnických dat. Azure brána pole dat pomáhá zajistit pouze oprávněné entity můžete zobrazit, upravit nebo odstranit data.

Tento článek popisuje funkce zabezpečení Azure Data Box brány, které pomáhají chránit všechny komponenty řešení a data jsou v nich uložené.

Brána pole dat řešení se skládá z čtyři hlavní součásti, které spolu interagují:

- **Služba Brána pole data, která hostované v Azure**. Prostředek správy, který použijete k vytvoření objednávky zařízení nakonfigurovat zařízení a potom sledovat pořadí do konce.
- **Zařízení data Box brány**. Virtuální zařízení, který zřídíte v hypervisoru systému, který zadáte. Tato virtuální zařízení se používá pro import místních dat do Azure.
- **Klienty a hostitele připojené k zařízení**. Klienti ve vaší infrastruktuře, připojte se k zařízení Data Box brány a obsahující data, která se dají chránit.
- **Cloudové úložiště**. Umístění v cloudové platformy Azure, kde jsou uložená data. Toto umístění je obvykle účtu úložiště propojeném prostředku brány pole dat, který vytvoříte.


## <a name="data-box-gateway-service-protection"></a>Ochrana služby pole brány dat

Služba Data Box brány je služba pro správu, která je hostovaná v Azure. Služba se používá ke konfiguraci a správě zařízení.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Ochrana zařízení data Box brány

Zařízení Data Box brány je virtuální zařízení, pro kterého je zřízené v hypervisoru, který zadáte v místním systému. Zařízení pomáhá odesílání dat do Azure. Vaše zařízení:

- Potřebuje aktivační klíč pro přístup ke službě Data Box Edge/Data pole brány.
- Je chráněn za všech okolností heslo zařízení.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Ochrana zařízení pomocí aktivační kód

Pouze autorizovaného zařízení Data Box brány může připojit k službě Brána pole dat, který vytvoříte ve svém předplatném Azure. K ověření zařízení, budete muset použít aktivační kód k aktivaci zařízení pomocí služby Data Box brány.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Další informace najdete v tématu [získat aktivační klíč](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Ochrana zařízení pomocí hesla

Hesla se ujistěte, že jenom Autorizovaní uživatelé můžou přistupovat k datům. Data Box brány zařízení spuštění v uzamčeném stavu.

Můžete:

- Připojení k místní webové uživatelské rozhraní zařízení přes prohlížeč a zadejte heslo pro přihlášení k zařízení.
- Vzdálené připojení k rozhraní prostředí PowerShell zařízení prostřednictvím protokolu HTTP. Ve výchozím nastavení je zapnutá Vzdálená správa. Zadejte heslo zařízení pro přihlášení k zařízení. Další informace najdete v tématu [připojit vzdáleně na vaše zařízení Data Box brány](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Použití místní webové uživatelské rozhraní pro [změnit heslo](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Pokud změníte heslo, nezapomeňte informovat všechny uživatele vzdáleného přístupu tak, že nebudou mít potíže při přihlašování.


## <a name="protect-your-data"></a>Ochrana dat

Tato část popisuje funkce zabezpečení brána pole dat, které chrání data během přenosu a uložené.

### <a name="protect-data-at-rest"></a>Ochrana dat v klidovém stavu

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Chránit data na cestě

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Ochrana dat přes účet úložiště

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Otočit o a poté [synchronizovat klíče účtu úložiště](data-box-gateway-manage-shares.md#sync-storage-keys) pravidelně k ochraně vašeho účtu úložiště před neoprávněnými uživateli.

## <a name="manage-personal-information"></a>Správa osobních údajů

Služba Data Box brány shromažďuje osobní informace v následujících scénářích:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Chcete-li zobrazit seznam uživatelů, kteří mohou přistupovat k nebo odstranit sdílenou složku, postupujte podle kroků v [spravovat sdílené složky na bráně dat pole](data-box-gateway-manage-shares.md).

Další informace najdete v tématu Zásady ochrany osobních údajů společnosti Microsoft na [centrum](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Další postup

[Nasazení zařízení Data Box brány](data-box-gateway-deploy-prep.md)
