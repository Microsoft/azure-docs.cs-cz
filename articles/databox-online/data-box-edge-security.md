---
title: Zabezpečení Azure Data Box Edge | Dokumenty společnosti Microsoft
description: Popisuje funkce zabezpečení a ochrany osobních údajů, které chrání vaše zařízení, služby a data Azure Data Box Edge místně a v cloudu.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 375576dd4a7897c48474fd2af00a99084292d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69970886"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Zabezpečení a ochrana dat v Azure Data Boxu Edge

Zabezpečení je velkým problémem, když přijímáte novou technologii, zejména pokud je tato technologie používána s důvěrnými nebo proprietárními daty. Azure Data Box Edge vám pomůže zajistit, aby vaše data mohly zobrazit, upravit nebo odstranit jenom autorizované entity.

Tento článek popisuje funkce zabezpečení Data Box Edge, které pomáhají chránit každou součást řešení a data v nich uložená.

Azure Data Box Edge se skládá ze čtyř hlavních součástí, které vzájemně spolupracují:

- **Služba Data Box Edge hostovaná v Azure**. Prostředek pro správu, který používáte k vytvoření pořadí zařízení, konfiguraci zařízení a následném sledování objednávky až do dokončení.
- **Zařízení Data Box Edge**. Přenosové zařízení, které je vám dodané, takže můžete importovat místní data do Azure.
- **Klienti/hostitelé připojení k zařízení**. Klienti ve vaší infrastruktuře, které se připojují k zařízení Data Box Edge a obsahují data, která je třeba chránit.
- **Cloudové úložiště**. Umístění v cloudové platformě Azure, kde jsou uložená data. Toto umístění je obvykle účet úložiště propojený s prostředek emitované datovým boxem Edge, který vytvoříte.

## <a name="data-box-edge-service-protection"></a>Ochrana služby Data Box Edge

Služba Data Box Edge je služba pro správu, která je hostovaná v Azure. Služba slouží ke konfiguraci a správě zařízení.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>Ochrana zařízení Data Box Edge

Zařízení Data Box Edge je místní zařízení, které pomáhá transformovat vaše data tím, že je místně zpracuje a pak je odešle do Azure. Vaše zařízení:

- Potřebuje aktivační klíč pro přístup ke službě Data Box Edge.
- Je vždy chráněn heslem zařízení.
- Je uzamčeno zařízení. Zařízení BMC a BIOS jsou chráněny heslem. Systém BIOS je chráněn omezeným přístupem uživatelů.
- Je povoleno zabezpečené spouštění.
- Spustí program Windows Defender Device Guard. Ochrana zařízení umožňuje spouštět pouze důvěryhodné aplikace, které definujete v zásadách integrity kódu.

### <a name="protect-the-device-via-activation-key"></a>Ochrana zařízení pomocí aktivačního klíče

Ke službě Data Box Edge, kterou vytvoříte v předplatném Azure, se může připojit jenom autorizované zařízení Data Box Edge. Chcete-li zařízení autorizovat, musíte k aktivaci zařízení pomocí služby Data Box Edge použít aktivační klíč.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Další informace naleznete [v tématu Získání aktivačního klíče](data-box-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Chraňte zařízení heslem

Hesla zajišťují, že k vašim datům mají přístup pouze oprávnění uživatelé. Zařízení Data Box Edge se spouštějí v uzamčeném stavu.

Můžete:

- Připojte se k místnímu webovému uživatelskému rozhraní zařízení prostřednictvím prohlížeče a zadejte heslo pro přihlášení k zařízení.
- Vzdáleně se připojte k rozhraní PowerShellu zařízení přes protokol HTTP. Vzdálená správa je ve výchozím nastavení zapnutá. Potom můžete zadat heslo zařízení pro přihlášení k zařízení. Další informace naleznete v [tématu Vzdálené připojení k zařízení Data Box Edge](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Pomocí místního [webového uživatelského uživatelského prostředí změňte heslo](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access). Pokud změníte heslo, nezapomeňte upozornit všechny uživatele vzdáleného přístupu, aby neměli problémy s přihlášením.

## <a name="protect-your-data"></a>Ochrana vašich dat

Tato část popisuje funkce zabezpečení Data Box Edge, které chrání data při přenosu a uložená data.

### <a name="protect-data-at-rest"></a>Ochrana neaktivních uložených dat

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- 256bitové šifrování nástroje BitLocker XTS-AES se používá k ochraně místních dat.


### <a name="protect-data-in-flight"></a>Ochrana dat za letu

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Ochrana dat prostřednictvím účtů úložiště

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Pravidelně otáčejte a [synchronizujte klíče účtu úložiště,](data-box-edge-manage-shares.md#sync-storage-keys) abyste ochránili účet úložiště před neoprávněnými uživateli.

## <a name="manage-personal-information"></a>Správa osobních údajů

Služba Data Box Edge shromažďuje osobní údaje v následujících scénářích:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Chcete-li zobrazit seznam uživatelů, kteří mají přístup ke sdílené položce nebo ji odstranit, postupujte podle pokynů v části [Správa sdílených složek na okraji datové schránky](data-box-edge-manage-shares.md).

Další informace naleznete v zásadách ochrany osobních údajů společnosti Microsoft v [Centru zabezpečení](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Další kroky

[Nasazení zařízení Data Box Edge](data-box-edge-deploy-prep.md)
