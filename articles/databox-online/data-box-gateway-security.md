---
title: Zabezpečení Azure Data Box Gateway | Microsoft Docs
description: Popisuje funkce zabezpečení a ochrany osobních údajů, které chrání vaše Azure Data Box Gateway virtuální zařízení, služby a data v místním prostředí i v cloudu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 45506b87bf13b44e035655dbdf0fac03ea50612c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90893858"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure Data Box Gateway zabezpečení a ochrana dat

Zabezpečení je zásadním problémem při přijímání nové technologie, zejména v případě, že se technologie používá s důvěrnými nebo proprietárními daty. Azure Data Box Gateway vám pomůže zajistit, aby data mohla zobrazovat, upravovat nebo odstraňovat jenom autorizované entity.

Tento článek popisuje funkce zabezpečení Azure Data Box Gateway, které vám pomůžou chránit jednotlivé součásti řešení a data, která jsou v nich uložená.

Data Box Gateway řešení se skládá ze čtyř hlavních součástí, které vzájemně spolupracují:

- **Služba data box Gateway hostovaná v Azure**. Prostředek správy, který použijete k vytvoření pořadí zařízení, nakonfigurovat zařízení a pak sledovat pořadí dokončení.
- **Data box Gateway zařízení**. Virtuální zařízení, které jste zřídili v hypervisoru systému, který zadáte. Toto virtuální zařízení slouží k importu místních dat do Azure.
- **Klienti/hostitelé připojení k zařízení**. Klienti v infrastruktuře, kteří se připojují k Data Box Gateway zařízení a obsahují data, která je třeba chránit.
- **Cloudové úložiště**. Umístění na cloudové platformě Azure, kde jsou uložená data. Toto umístění obvykle je účet úložiště propojený s vámi vytvořeným prostředkem Data Box Gateway.

## <a name="data-box-gateway-service-protection"></a>Ochrana Data Box Gateway služby

Služba Data Box Gateway je služba pro správu, která je hostovaná v Azure. Služba se používá ke konfiguraci a správě zařízení.

[!INCLUDE [data-box-edge-gateway-service-protection](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Data Box Gateway Ochrana zařízení

Zařízení Data Box Gateway je virtuální zařízení, které je zřízené v hypervisoru místního systému, který zadáte. Zařízení pomáhá posílat data do Azure. Vaše zařízení:

- Pro přístup ke službě Azure Stack Edge pro/Data Box Gateway potřebuje aktivační klíč.
- Je chráněna za všech okolností heslem zařízení.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Chránit zařízení pomocí aktivačního klíče

Ke službě Data Box Gateway, kterou vytvoříte ve vašem předplatném Azure, se smí připojit jenom autorizované Data Box Gateway zařízení. K autorizaci zařízení musíte použít aktivační klíč k aktivaci zařízení ve službě Data Box Gateway.

[!INCLUDE [data-box-edge-gateway-activation-key](../../includes/data-box-edge-gateway-activation-key.md)]

Další informace najdete v tématu [získání aktivačního klíče](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Chránit zařízení pomocí hesla

Hesla zajišťují, že k datům budou mít přístup jenom autorizovaní uživatelé. Zařízení Data Box Gateway se spouští v uzamčeném stavu.

Další možnosti:

- Připojte se k místnímu webovému uživatelskému rozhraní zařízení přes prohlížeč a pak zadejte heslo pro přihlášení k zařízení.
- Vzdáleně se připojte k rozhraní PowerShell zařízení přes HTTP. Vzdálená správa je ve výchozím nastavení zapnutá. Pak můžete zadat heslo zařízení pro přihlášení k zařízení. Další informace najdete v tématu [vzdálené připojení k vašemu data box Gateway zařízení](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-password-best-practices](../../includes/data-box-edge-gateway-password-best-practices.md)]
- [Změňte heslo](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)pomocí místního webového uživatelského rozhraní. Pokud změníte heslo, nezapomeňte se informovat o všech uživatelích vzdáleného přístupu tak, aby k nim nevznikly problémy s přihlášením.

## <a name="protect-your-data"></a>Ochrana dat

Tato část popisuje funkce zabezpečení Data Box Gateway, které chrání při přenosu a uložená data.

### <a name="protect-data-at-rest"></a>Ochrana neaktivních uložených dat

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Ochrana dat v letu

[!INCLUDE [data-box-edge-gateway-data-flight](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-using-storage-accounts"></a>Ochrana dat pomocí účtů úložiště

[!INCLUDE [data-box-edge-gateway-data-storage-accounts](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]

- Pravidelně otáčejte a [synchronizujte klíče účtu úložiště](data-box-gateway-manage-shares.md#sync-storage-keys) , abyste chránili svůj účet úložiště před neoprávněnými uživateli.

### <a name="protect-the-device-data-using-bitlocker"></a>Ochrana dat zařízení pomocí nástroje BitLocker

Pro zabezpečení virtuálních disků na vašem Data Box Gatewayovém virtuálním počítači doporučujeme povolit nástroj BitLocker. Ve výchozím nastavení není BitLocker povolený. Další informace naleznete v tématu:

- [Nastavení podpory šifrování ve Správci technologie Hyper-V](hhttps://docs.microsoft.com/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
- [Podpora BitLockeru ve virtuálním počítači](https://kb.vmware.com/s/article/2036142)

## <a name="manage-personal-information"></a>Správa osobních údajů

Služba Data Box Gateway shromažďuje osobní údaje v následujících scénářích:

[!INCLUDE [data-box-edge-gateway-manage-personal-data](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Chcete-li zobrazit seznam uživatelů, kteří mají přístup nebo odstranit sdílenou složku, postupujte podle pokynů v části [Správa sdílených složek na data box Gateway](data-box-gateway-manage-shares.md).

Další informace najdete v zásadách ochrany osobních údajů Microsoftu na webu [Trust Center](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Další kroky

[Nasazení zařízení Data Box Gateway](data-box-gateway-deploy-prep.md)
