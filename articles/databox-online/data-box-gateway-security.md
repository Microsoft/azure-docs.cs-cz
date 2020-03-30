---
title: Zabezpečení brány datové schránky Azure | Dokumenty společnosti Microsoft
description: Popisuje funkce zabezpečení a ochrany osobních údajů, které chrání vaše virtuální zařízení, služby a data azure brány data boxů, místně i v cloudu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 89f981fcda8f40daff49ebdf796b896d90ce1754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900601"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Zabezpečení a ochrana dat služby Azure Data Box Gateway

Zabezpečení je velkým problémem, když přijímáte novou technologii, zejména pokud je tato technologie používána s důvěrnými nebo proprietárními daty. Azure Data Box Gateway pomáhá zajistit, že data můžou zobrazit, upravit nebo odstranit jenom autorizované entity.

Tento článek popisuje funkce zabezpečení Azure Data Box Gateway, které pomáhají chránit každou součást řešení a data v nich uložená.

Řešení Brány datových schronů se skládá ze čtyř hlavních součástí, které vzájemně spolupracují:

- **Služba Brána datových schronů hostovaná v Azure**. Prostředek pro správu, který používáte k vytvoření pořadí zařízení, konfiguraci zařízení a následném sledování objednávky až do dokončení.
- **Zařízení brány datové schránky**. Virtuální zařízení, které zřídíte v hypervisoru systému, který zadáte. Toto virtuální zařízení se používá k importu místních dat do Azure.
- **Klienti/hostitelé připojení k zařízení**. Klienti ve vaší infrastruktuře, kteří se připojují k zařízení Brány datové schránky a obsahují data, která je třeba chránit.
- **Cloudové úložiště**. Umístění v cloudové platformě Azure, kde jsou uložená data. Toto umístění je obvykle účet úložiště propojený s prostředek brány datové schránky, který vytvoříte.


## <a name="data-box-gateway-service-protection"></a>Ochrana služby Brány datové schránky

Služba Brána datových schronů je služba pro správu, která je hostovaná v Azure. Služba slouží ke konfiguraci a správě zařízení.

[!INCLUDE [data-box-edge-gateway-service-protection](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Ochrana zařízení brány datové schránky

Zařízení Brány datové schránky je virtuální zařízení, které je zřízeno v hypervisoru místního systému, který poskytujete. Zařízení pomáhá odesílat data do Azure. Vaše zařízení:

- Potřebuje aktivační klíč pro přístup ke službě Data Box Edge/Data Box Gateway.
- Je vždy chráněn heslem zařízení.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Ochrana zařízení pomocí aktivačního klíče

Ke službě Brána datových schvičníku, kterou vytvoříte ve svém předplatném Azure, se může připojit jenom autorizované zařízení Brány datových schronů. Chcete-li zařízení autorizovat, musíte k aktivaci zařízení pomocí služby Data Box Gateway použít aktivační klíč.

[!INCLUDE [data-box-edge-gateway-activation-key](../../includes/data-box-edge-gateway-activation-key.md)]

Další informace naleznete [v tématu Získání aktivačního klíče](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Chraňte zařízení heslem

Hesla zajišťují, že k vašim datům mají přístup pouze oprávnění uživatelé. Zařízení brány datové schránky se spouštějí v uzamčeném stavu.

Můžete:

- Připojte se k místnímu webovému uživatelskému rozhraní zařízení prostřednictvím prohlížeče a zadejte heslo pro přihlášení k zařízení.
- Vzdáleně se připojte k rozhraní PowerShellu zařízení přes PROTOKOL HTTP. Vzdálená správa je ve výchozím nastavení zapnutá. Potom můžete zadat heslo zařízení pro přihlášení k zařízení. Další informace najdete v [tématu Vzdálené připojení k zařízení Brány datové schránky](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-password-best-practices](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Pomocí místního [webového uživatelského uživatelského prostředí změňte heslo](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Pokud změníte heslo, nezapomeňte upozornit všechny uživatele vzdáleného přístupu, aby neměli problémy s přihlášením.


## <a name="protect-your-data"></a>Ochrana vašich dat

Tato část popisuje funkce zabezpečení brány datové schránky, které chrání data při přenosu a uložená data.

### <a name="protect-data-at-rest"></a>Ochrana neaktivních uložených dat

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Ochrana dat za letu

[!INCLUDE [data-box-edge-gateway-data-flight](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Ochrana dat prostřednictvím účtů úložiště

[!INCLUDE [data-box-edge-gateway-data-storage-accounts](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Pravidelně otáčejte a [synchronizujte klíče účtu úložiště,](data-box-gateway-manage-shares.md#sync-storage-keys) abyste ochránili účet úložiště před neoprávněnými uživateli.

## <a name="manage-personal-information"></a>Správa osobních údajů

Služba Brána datových schronů shromažďuje osobní údaje v následujících scénářích:

[!INCLUDE [data-box-edge-gateway-manage-personal-data](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Chcete-li zobrazit seznam uživatelů, kteří mají přístup ke sdílené položce nebo ji odstranit, postupujte podle pokynů v části [Správa sdílených složek v bráně datové schránky](data-box-gateway-manage-shares.md).

Další informace naleznete v zásadách ochrany osobních údajů společnosti Microsoft v [Centru zabezpečení](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Další kroky

[Nasazení zařízení brány datové schránky](data-box-gateway-deploy-prep.md)
