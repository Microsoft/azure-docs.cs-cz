---
title: Zabezpečení Azure Stack Edge | Microsoft Docs
description: V této části najdete popis funkcí zabezpečení a ochrany osobních údajů, které chrání vaše Azure Stack hraniční zařízení, služby a data místně i v cloudu.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 3d567ec4d760be24fdbb79ff85bd6db0eb4a66c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "82570067"
---
# <a name="azure-stack-edge-security-and-data-protection"></a>Zabezpečení a ochrana dat v Azure Stack Edge

Zabezpečení je zásadním problémem při přijímání nové technologie, zejména v případě, že se technologie používá s důvěrnými nebo proprietárními daty. Azure Stack Edge vám pomůže zajistit, aby data mohla zobrazovat, upravovat nebo odstraňovat jenom autorizované entity.

Tento článek popisuje funkce zabezpečení Azure Stack Edge, které vám pomůžou chránit jednotlivé součásti řešení a data, která jsou v nich uložená.

Azure Stack Edge se skládá ze čtyř hlavních součástí, které vzájemně spolupracují:

- **Služba Azure Stack Edge hostovaná v Azure**. Prostředek správy, který použijete k vytvoření pořadí zařízení, nakonfigurovat zařízení a pak sledovat pořadí dokončení.
- **Azure Stack hraniční zařízení**. Přenosové zařízení, které vám bylo dodány, abyste mohli importovat vaše místní data do Azure.
- **Klienti/hostitelé připojení k zařízení**. Klienti v infrastruktuře, kteří se připojují k Azure Stack hraničního zařízení a obsahují data, která je třeba chránit.
- **Cloudové úložiště**. Umístění na cloudové platformě Azure, kde jsou uložená data. Toto umístění obvykle je účet úložiště propojený s vytvořeným prostředkem Azure Stack Edge.

## <a name="azure-stack-edge-service-protection"></a>Ochrana služby Azure Stack Edge

Služba Azure Stack Edge je služba pro správu, která je hostovaná v Azure. Služba se používá ke konfiguraci a správě zařízení.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="azure-stack-edge-device-protection"></a>Ochrana zařízení Azure Stack Edge

Azure Stack hraniční zařízení je místní zařízení, které pomáhá transformovat data tak, že je zpracovává místně a odesílá je do Azure. Vaše zařízení:

- Pro přístup ke službě Azure Stack Edge potřebuje aktivační klíč.
- Je chráněna za všech okolností heslem zařízení.
- Je uzamčené zařízení. Zařízení BMC a BIOS jsou chráněná heslem. Systém BIOS je chráněn omezeným přístupem uživatele.
- Má povolené zabezpečené spouštění.
- Spustí ochranu zařízení v programu Windows Defender. Device Guard umožňuje spouštět jenom důvěryhodné aplikace, které definujete v zásadách integrity kódu.

### <a name="protect-the-device-via-activation-key"></a>Chránit zařízení pomocí aktivačního klíče

Ke službě Azure Stack Edge, kterou vytvoříte ve svém předplatném Azure, se smí připojit jenom autorizované Azure Stack hraniční zařízení. K autorizaci zařízení musíte použít aktivační klíč k aktivaci zařízení ve službě Azure Stack Edge.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Další informace najdete v tématu [získání aktivačního klíče](azure-stack-edge-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Chránit zařízení pomocí hesla

Hesla zajišťují, že k datům budou mít přístup jenom autorizovaní uživatelé. Azure Stack hraniční zařízení se spouští v uzamčeném stavu.

Další možnosti:

- Připojte se k místnímu webovému uživatelskému rozhraní zařízení přes prohlížeč a pak zadejte heslo pro přihlášení k zařízení.
- Vzdáleně se připojte k rozhraní PowerShellu zařízení přes HTTP. Vzdálená správa je ve výchozím nastavení zapnutá. Pak můžete zadat heslo zařízení pro přihlášení k zařízení. Další informace najdete v tématu [připojení vzdáleně k vašemu Azure Stack hraničnímu zařízení](azure-stack-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- [Změňte heslo](azure-stack-edge-manage-access-power-connectivity-mode.md#manage-device-access)pomocí místního webového uživatelského rozhraní. Pokud změníte heslo, nezapomeňte se informovat o všech uživatelích vzdáleného přístupu, aby nemuseli mít problémy s přihlášením.

## <a name="protect-your-data"></a>Ochrana dat

Tato část popisuje funkce zabezpečení Azure Stack Edge, které chrání data při přenosu a uložená data.

### <a name="protect-data-at-rest"></a>Ochrana neaktivních uložených dat

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- BitLocker XTS-AES 256-bitové šifrování se používá k ochraně místních dat.


### <a name="protect-data-in-flight"></a>Ochrana dat v letu

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Ochrana dat prostřednictvím účtů úložiště

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Pravidelně otáčejte a [synchronizujte klíče účtu úložiště](azure-stack-edge-manage-shares.md#sync-storage-keys) , abyste chránili svůj účet úložiště před neoprávněnými uživateli.

## <a name="manage-personal-information"></a>Správa osobních údajů

Služba Azure Stack Edge shromažďuje osobní údaje v následujících scénářích:

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Chcete-li zobrazit seznam uživatelů, kteří mají přístup nebo odstranit sdílenou složku, postupujte podle kroků v části [Správa sdílených složek na Azure Stack hraničních](azure-stack-edge-manage-shares.md)zařízeních.

Další informace najdete v zásadách ochrany osobních údajů Microsoftu na webu [Trust Center](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Další kroky

[Nasazení zařízení Azure Stack Edge](azure-stack-edge-deploy-prep.md)
