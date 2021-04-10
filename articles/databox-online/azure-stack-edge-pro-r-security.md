---
title: Azure Stack zabezpečení Edge pro R | Microsoft Docs
description: V této části najdete popis funkcí zabezpečení a ochrany osobních údajů, které chrání Azure Stack hraničních zařízení pro R a Azure Stack Edge Mini R, služby a data v místním prostředí i v cloudu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: bd90a16c09dce65115cea2f097d18f2e0ced931a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632029"
---
# <a name="security-and-data-protection-for-azure-stack-edge-pro-r-and-azure-stack-edge-mini-r"></a>Zabezpečení a ochrana dat pro Azure Stack Edge pro R a Azure Stack Edge Mini R

[!INCLUDE [applies-to-pro-r-and-mini-r--skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

Zabezpečení je zásadním problémem při přijímání nové technologie, zejména v případě, že se technologie používá s důvěrnými nebo proprietárními daty. Azure Stack Edge pro R a Azure Stack Edge Mini R vám pomůžou zajistit, aby data mohla zobrazovat, upravovat nebo odstraňovat jenom autorizované entity.

Tento článek popisuje funkce Azure Stack Edge pro R a Azure Stack Edge Mini R, které vám pomůžou chránit jednotlivé součásti řešení a data, která jsou v nich uložená.

Řešení se skládá ze čtyř hlavních součástí, které vzájemně spolupracují:

- **Služba Azure Stack Edge hostovaná ve veřejném cloudu Azure nebo v cloudu Azure Government**. Prostředek správy, který použijete k vytvoření pořadí zařízení, nakonfigurovat zařízení a pak sledovat pořadí dokončení.
- **Zařízení robustní z Azure Stack Edge**. Robustní a fyzické zařízení, které vám bylo dodány, abyste mohli naimportovat vaše místní data do veřejného nebo Azure Governmentho cloudu Azure. Zařízení může být Azure Stack Edge pro R nebo Azure Stack Edge Mini R.
- **Klienti/hostitelé připojení k zařízení**. Klienti v infrastruktuře, kteří se připojují k zařízení a obsahují data, která je třeba chránit.
- **Cloudové úložiště**. Umístění na cloudové platformě Azure, kde jsou uložená data. Toto umístění obvykle je účet úložiště propojený s vytvořeným prostředkem Azure Stack Edge.

## <a name="service-protection"></a>Ochrana služby

Služba Azure Stack Edge je služba pro správu, která je hostovaná v Azure. Služba se používá ke konfiguraci a správě zařízení.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-service-protection.md)]

## <a name="device-protection"></a>Ochrana zařízení

Robustní zařízení je místní zařízení, které pomáhá transformovat data tak, že je zpracovává místně a odesílá je do Azure. Vaše zařízení:

- Pro přístup ke službě Azure Stack Edge potřebuje aktivační klíč.
- Je chráněna za všech okolností heslem zařízení.
- Je uzamčené zařízení. Řadič pro správu základní desky zařízení (BMC) a systém BIOS jsou chráněné heslem. Řadič pro správu základní desky je chráněný omezeným přístupem uživatelů.
- Má povoleno zabezpečené spouštění, které zajistí, že se zařízení spouští jenom pomocí důvěryhodného softwaru poskytovaného Microsoftem.
- Spustí řízení aplikací v programu Windows Defender (WDAC). WDAC umožňuje spouštět jenom důvěryhodné aplikace, které definujete v zásadách integrity kódu.
- Má čip TPM (Trusted Platform Module), který provádí funkce související se zabezpečením založené na hardwaru. ČIP TPM konkrétně spravuje a chrání tajné a data, která musí být na zařízení trvalá.
- V zařízení jsou otevřené jenom požadované porty a všechny ostatní porty jsou blokované. Další informace najdete v seznamu [požadavků na porty pro zařízení](azure-stack-edge-pro-r-system-requirements.md) .
- Veškerý přístup k hardwaru zařízení i k softwaru se zaznamená do protokolu. 
    - Pro software zařízení se výchozí protokoly brány firewall shromažďují pro příchozí a odchozí provoz ze zařízení. Tyto protokoly jsou zabalené v balíčku pro podporu.
    - Pro hardware zařízení se v zařízení přihlásí všechny události skříně zařízení, jako je třeba otevírání a zavírání skříně zařízení.

    Další informace o konkrétních protokolech, které obsahují události vniknutí hardwaru a softwaru a o tom, jak protokoly získat, najdete v tématu [shromáždění pokročilých protokolů zabezpečení](azure-stack-edge-gpu-troubleshoot.md).


### <a name="protect-the-device-via-activation-key"></a>Chránit zařízení pomocí aktivačního klíče

Ke službě Azure Stack Edge, kterou vytvoříte ve svém předplatném Azure, se smí připojit pouze autorizovaný Azure Stack hraničního zařízení pro R nebo Azure Stack Edge Mini R. K autorizaci zařízení musíte použít aktivační klíč k aktivaci zařízení ve službě Azure Stack Edge.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-activation-key.md)]

Další informace najdete v tématu [získání aktivačního klíče](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Chránit zařízení pomocí hesla

Hesla zajišťují, že k datům budou mít přístup jenom autorizovaní uživatelé. Azure Stack hraničních zařízení pro R se spouští v uzamčeném stavu.

Další možnosti:

- Připojte se k místnímu webovému uživatelskému rozhraní zařízení přes prohlížeč a pak zadejte heslo pro přihlášení k zařízení.
- Vzdáleně se připojte k rozhraní PowerShellu zařízení přes HTTP. Vzdálená správa je ve výchozím nastavení zapnutá. Vzdálená správa je taky nakonfigurovaná tak, aby používala dostatečně správu (JEA) k omezení toho, co můžou uživatelé dělat. Pak můžete zadat heslo zařízení pro přihlášení k zařízení. Další informace najdete v tématu [vzdálené připojení k vašemu zařízení](azure-stack-edge-gpu-connect-powershell-interface.md).
- Místní uživatel hraniční aplikace v zařízení má omezený přístup k zařízení pro počáteční konfiguraci a řešení potíží. Výpočetní úlohy, které běží na zařízení, přenos dat a úložiště, jsou přístupné z veřejného portálu Azure nebo z portálu pro státní správu pro prostředek v cloudu.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-password-best-practices.md)]
- [Změňte heslo](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#change-device-password)pomocí místního webového uživatelského rozhraní. Pokud změníte heslo, nezapomeňte se informovat o všech uživatelích vzdáleného přístupu, aby nemuseli mít problémy s přihlášením.

### <a name="establish-trust-with-the-device-via-certificates"></a>Navázání vztahu důvěryhodnosti se zařízením pomocí certifikátů

Azure Stack robustní zařízení umožňuje přenášet vlastní certifikáty a instalovat je, aby je bylo možné použít pro všechny veřejné koncové body. Další informace najdete v [poznámkách k nahrání certifikátu](azure-stack-edge-gpu-manage-certificates.md#upload-certificates). Seznam všech certifikátů, které se dají na svém zařízení nainstalovat, najdete v části [Správa certifikátů v zařízení](azure-stack-edge-gpu-manage-certificates.md).

- Při konfiguraci výpočetní kapacity na zařízení se vytvoří zařízení IoT a zařízení IoT Edge. Těmto zařízením jsou automaticky přiřazeny symetrické přístupové klíče. Z hlediska zabezpečení je nejvhodnější tyto klíče pravidelně střídat prostřednictvím služby IoT Hub.

## <a name="protect-your-data"></a>Ochrana dat

Tato část popisuje funkce zabezpečení, které chrání při přenosu a uložená data.

### <a name="protect-data-at-rest"></a>Ochrana neaktivních uložených dat

Všechna neaktivní data v zařízení jsou dvojitě zašifrovaná, přístup k datům se řídí a jakmile je zařízení deaktivováno, data jsou bezpečně smazána z datových disků.

#### <a name="double-encryption-of-data"></a>Dvojité šifrování dat

Data na discích jsou chráněná dvěma vrstvami šifrování:

- První vrstva šifrování je BitLocker XTS-AES 256-bit Encryption na datových svazcích.
- Druhá vrstva je pevné disky, které mají integrované šifrování.
- Svazek s operačním systémem má jako jednu vrstvu šifrování nástroj BitLocker.

> [!NOTE]
> Disk s operačním systémem má šifrování softwaru s jednou vrstvou XTS-AES-256.

Až se zařízení aktivuje, zobrazí se výzva k uložení souboru klíče, který obsahuje klíče pro obnovení, které vám pomůžou obnovit data v zařízení, pokud se zařízení nespustí. V souboru jsou dva klíče:

- Jeden klíč obnoví konfiguraci zařízení na svazcích s operačním systémem.
<!-- - Second key is to unlock the BitLocker on the data disks. -->
- Druhý klíč odemkne hardwarové šifrování v datových discích.

> [!IMPORTANT]
> Uložte soubor klíče na bezpečné místo mimo samotné zařízení. Pokud se zařízení nespustí a nemáte klíč, může to způsobit ztrátu dat.

- Některé scénáře obnovení vás vyzve k zadání souboru s klíčem, který jste uložili. 
<!--- If a node isn't booting up, you will need to perform a node replacement. You will have the option to swap the data disks from the failed node to the new node. For a 4-node device, you won't need a key file. For a 1-node device, you will be prompted to provide a key file.-->

#### <a name="restricted-access-to-data"></a>Omezený přístup k datům

Přístup k datům uloženým ve sdílených složkách a účtech úložiště je omezený.

- Klienti SMB, kteří mají přístup ke sdíleným datům, potřebují přihlašovací údaje uživatele přidružené ke sdílené složce. Tyto přihlašovací údaje se definují při vytvoření sdílené složky.
- Klienti NFS, kteří přistupují ke sdílené složce, musí mít při vytvoření sdílené složky explicitně přidané IP adresy.
- Hraniční účty úložiště, které jsou vytvořené v zařízení, jsou místní a jsou chráněné šifrováním na datových discích. Účty služby Azure Storage, na které jsou tyto účty hraničního úložiště mapované, se chrání pomocí předplatného a přístupového klíče 2 512 úložiště přidruženého k účtu úložiště Edge (tyto klíče se liší od těch, které jsou spojené s vaším účtem Azure Storage). Další informace najdete v tématu [Ochrana dat v účtech úložiště](#protect-data-in-storage-accounts).
- BitLocker XTS-AES 256-bitové šifrování se používá k ochraně místních dat.

#### <a name="secure-data-erasure"></a>Vymazávání zabezpečených dat

Když se zařízení obnoví na pevný stav, na zařízení se provede zabezpečené vymazání. Zabezpečené vymazání provádí mazání dat na discích pomocí vyčištění NIST SP 800 88r1.

### <a name="protect-data-in-flight"></a>Ochrana dat v letu

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-data-flight.md)]

### <a name="protect-data-in-storage-accounts"></a>Ochrana dat v účtech úložiště

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-protect-data-storage-accounts.md)]

- Pravidelně otáčejte a [synchronizujte klíče účtu úložiště](azure-stack-edge-gpu-manage-storage-accounts.md) , abyste chránili svůj účet úložiště před neoprávněnými uživateli.

## <a name="manage-personal-information"></a>Správa osobních údajů

Služba Azure Stack Edge shromažďuje osobní údaje v následujících scénářích:

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-manage-personal-data.md)]

Chcete-li zobrazit seznam uživatelů, kteří mají přístup nebo odstranit sdílenou složku, postupujte podle kroků v části [Správa sdílených složek na Azure Stack hraničních](azure-stack-edge-gpu-manage-shares.md)zařízeních.

Další informace najdete v zásadách ochrany osobních údajů Microsoftu na webu [Trust Center](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Další kroky

[Nasazení zařízení Azure Stack Edge pro R](azure-stack-edge-gpu-deploy-prep.md)
