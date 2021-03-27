---
title: Skripty PowerShellu pro Azure IoT Edge pro Linux ve Windows | Microsoft Docs
description: Referenční informace pro Azure IoT Edge pro Linux na skriptech prostředí Windows PowerShell pro nasazení, zřizování a stavové IoT Edge pro Linux na virtuálních počítačích s Windows.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 02/16/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 41827c5db58f3d4755fb34e46067357cd0255676
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612768"
---
# <a name="powershell-scripts-for-iot-edge-for-linux-on-windows"></a>Skripty PowerShellu pro IoT Edge pro Linux ve Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Pochopení skriptů PowerShellu, které nasazují, zřídí a získávají stav IoT Edge pro Linux ve virtuálním počítači s Windows.

Příkazy popsané v tomto článku jsou ze `AzureEFLOW.psm1` souboru, který se nachází ve vašem systému v `WindowsPowerShell` adresáři `C:\Program Files\WindowsPowerShellModules\AzureEFLOW` .

## <a name="deploy-eflow"></a>Deploy-Eflow

Příkaz **Deploy-eFlow** je hlavní metodou nasazení. Příkaz pro nasazení vytvoří virtuální počítač, zřídí soubory a nasadí modul IoT Edge agenta. I když žádný z parametrů není nutný, dá se použít k zřízení zařízení IoT Edge během nasazování a úpravách nastavení virtuálního počítače během vytváření. Úplný seznam získáte pomocí příkazu `Get-Help Deploy-Eflow -full` .  

>[!NOTE]
>Pro typ zřizování se **v současnosti výhradně** odkazuje na zřizování x509 pomocí [IoT Hub Device Provisioning Service Azure](../iot-dps/about-iot-dps.md). Metoda ručního zřizování x509 není v současné době podporovaná.

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| eflowVhdxDir | Cesta k adresáři | Adresář, ve kterém nasazení ukládá soubor VHDX pro virtuální počítač. |
| provisioningType | **manuální**, **TPM**, **x509** nebo **symetrický** |  Definuje typ zřizování, který chcete použít pro zařízení IoT Edge. |
| devConnString | Připojovací řetězec zařízení existujícího zařízení IoT Edge | Připojovací řetězec zařízení pro ruční zřízení zařízení IoT Edge (**Ruční**) |
| symmKey | Primární klíč pro existující registraci DPS nebo primární klíč stávajícího zařízení IoT Edge zaregistrovaných pomocí symetrických klíčů | Symetrický klíč pro zřízení IoT Edge zařízení (**x509** nebo **symetrický**). |
| Objekt ScopeId | ID oboru pro existující instanci DPS. | ID oboru pro zřízení IoT Edgeho zařízení (**x509** nebo **symetrický**). |
| registrationId | ID registrace existujícího zařízení IoT Edge | ID registrace pro zřízení zařízení IoT Edge (**x509** nebo **symetrický**). |
| identityCertLocVm | Cesta ke složce; musí být ve složce, kterou může služba vlastnit. `iotedge` | Absolutní cílová cesta certifikátu identity na virtuálním počítači pro zřízení zařízení IoT Edge (**x509** nebo **symetrický**). |
| identityCertLocWin | Cesta k adresáři | Absolutní cesta ke zdroji certifikátu identity ve Windows pro zřízení zařízení IoT Edge (**x509** nebo **symetrický**). |
| identityPkLocVm |  | Cesta ke složce; musí být ve složce, kterou může služba vlastnit. `iotedge` | Absolutní cílová cesta k privátnímu klíči identity na virtuálním počítači pro zřízení zařízení IoT Edge (**x509** nebo **symetrický**). |
| identityPkLocWin | Cesta k adresáři | Absolutní cesta ke zdroji privátního klíče identity ve Windows pro zřízení zařízení IoT Edge (**x509** nebo **symetrický**). |
| vmSizeDefintion | Maximálně 30 znaků | Definice počtu jader a dostupné paměti RAM pro virtuální počítač. **Výchozí hodnota**: Standard_K8S_v1. |
| vmDiskSize | Mezi 8 GB a 256 GB | Maximální velikost disku dynamicky se zvětšující virtuální pevný disk. **Výchozí hodnota**: 16 GB. |
| vmUser | Maximálně 30 znaků | Uživatelské jméno pro přihlášení k virtuálnímu počítači. |
| vnetType | **Transparentní** nebo **ICS** | Typ virtuálního přepínače. **Výchozí hodnota**: transparentní Transparentní odkazuje na externí přepínač, zatímco ICS odkazuje na interní přepínač. |
| vnetName | Není delší než 64 znaků. | Název virtuálního přepínače. **Výchozí hodnota**: external |
| enableVtpm | Žádné | **Parametr Switch** Vytvořte virtuální počítač s povoleným nebo zakázaným čipem TPM. |
| mobyPackageVersion | Maximálně 30 znaků |  Verze balíčku Moby, která se má ověřit nebo nainstalovat na virtuálním počítači.  **Výchozí hodnota:** 19.03.11. |
| iotedgePackageVersion | Maximálně 30 znaků | Verze IoT Edgeho balíčku, který se má ověřit nebo nainstalovat na virtuálním počítači. **Výchozí hodnota:** 1.1.0. |
| installPackages | Žádné | **Parametr Switch** Při přepínání se skript pokusí nainstalovat balíčky Moby a IoT Edge místo toho, aby procházel pouze ověření balíčků. |

>[!NOTE]
>Ve výchozím nastavení, pokud proces nemůže najít externí přepínač s názvem `External` , vyhledá všechny existující externí přepínače, pomocí kterých se získá IP adresa. Pokud není k dispozici žádný externí přepínač, bude vyhledán interní přepínač. Pokud není k dispozici žádný interní přepínač, pokusí se vytvořit výchozí přepínač, pomocí kterého se získá IP adresa.

## <a name="verify-eflowvm"></a>Verify-EflowVm

K ověření, že byla vytvořena IoT Edge pro Linux ve virtuálním počítači s Windows, je k dispozici vydaná funkce příkazu **verify-EflowVm** . Přebírá jenom společné parametry a vrátí **hodnotu true** , pokud se vytvořil virtuální počítač, a pokud ne, bude mít **hodnotu false** . Další informace získáte pomocí příkazu `Get-Help Verify-EflowVm -full` .

## <a name="provision-eflowvm"></a>Provision-EflowVm

Příkaz **zřídit-EflowVm** přidá informace o zřizování pro vaše zařízení IoT Edge do IoT Edge souboru virtuálního počítače `config.yaml` . Zřizování se dá dělat taky během fáze nasazení nastavením parametrů v příkazu **Deploy-eFlow** . Další informace získáte pomocí příkazu `Get-Help Provision-EflowVm -full` .

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| vmUser | Maximálně 30 znaků | Uživatelské jméno pro přihlášení k virtuálnímu počítači. |
| provisioningType | **manuální**, **TPM**, **x509** nebo **symetrický** |  Definuje typ zřizování, který chcete použít pro zařízení IoT Edge. |
| devConnString | Připojovací řetězec zařízení existujícího zařízení IoT Edge | Připojovací řetězec zařízení pro ruční zřízení zařízení IoT Edge (**Ruční**) |
| symmKey | Primární klíč pro existující registraci DPS nebo primární klíč stávajícího zařízení IoT Edge zaregistrovaných pomocí symetrických klíčů | Symetrický klíč pro zřízení zařízení IoT Edge (**DPS**, **symetrické**). |
| Objekt ScopeId | ID oboru pro existující instanci DPS. | ID oboru pro zřízení IoT Edgeho zařízení (**DPS**). |
| registrationId | ID registrace existujícího zařízení IoT Edge | ID registrace pro zřízení zařízení IoT Edge (**DPS**) |
| identityCertLocVm | Cesta ke složce; musí být ve složce, kterou může služba vlastnit. `iotedge` | Absolutní cílová cesta certifikátu identity na virtuálním počítači pro zřízení zařízení IoT Edge (**DPS**, **x509**). |
| identityCertLocWin | Cesta k adresáři | Absolutní cesta ke zdroji certifikátu identity ve Windows pro zřízení zařízení IoT Edge (**DPS**, **x509**). |
| identityPkLocVm |  | Cesta ke složce; musí být ve složce, kterou může služba vlastnit. `iotedge` | Absolutní cílová cesta k privátnímu klíči identity na virtuálním počítači pro zřízení zařízení IoT Edge (**DPS**, **x509**). |
| identityPkLocWin | Cesta k adresáři | Absolutní cesta ke zdroji privátního klíče identity ve Windows pro zřízení zařízení IoT Edge (**DPS**, **x509**). |

## <a name="get-eflowvmname"></a>Get-EflowVmName

Příkaz **Get-EflowVmName** se používá k dotazování aktuálního názvu hostitele virtuálního počítače. Tento příkaz má účet pro skutečnost, že se název hostitele systému Windows může v průběhu času měnit. Přebírá jenom společné parametry a vrátí aktuální název hostitele virtuálního počítače. Další informace získáte pomocí příkazu `Get-Help Get-EflowVmName -full` .

## <a name="get-eflowlogs"></a>Get-EflowLogs

Příkaz **Get-EflowLogs** se používá ke shromáždění a kompletování protokolů z IoT Edge pro Linux při nasazení Windows. Ve formě složky vypíše do nich připojené protokoly `.zip` . Další informace získáte pomocí příkazu `Get-Help Get-EflowLogs -full` .

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| vmUser | Maximálně 30 znaků | Uživatelské jméno pro přihlášení k virtuálnímu počítači. |

## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

Příkaz **Get-EflowVmTpmProvisioningInfo** se používá ke shromáždění a zobrazení informací o zřizování vTPM virtuálního počítače. Pokud byl virtuální počítač vytvořen bez vTPM, příkaz vrátí informaci, že nedokázal najít informace o zřizování čipu TPM. Další informace získáte pomocí příkazu `Get-Help Get-EflowVmTpmProvisioningInfo -full` .

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| vmUser | Maximálně 30 znaků | Uživatelské jméno pro přihlášení k virtuálnímu počítači. |

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

Příkaz **Get-EflowVmAddr** slouží k vyhledání a zobrazení adresy IP a adres MAC virtuálního počítače. Přebírá jenom společné parametry. Další informace získáte pomocí příkazu `Get-Help Get-EflowVmAddr -full` .

## <a name="get-eflowvmsysteminformation"></a>Get-EflowVmSystemInformation

Příkaz **Get-EflowVmSystemInformation** se používá ke shromažďování a zobrazování systémových informací z virtuálního počítače, jako je například využití paměti a úložiště. Další informace získáte pomocí příkazu `Get-Help Get-EflowVmSystemInformation -full` .

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| vmUser | Maximálně 30 znaků | Uživatelské jméno pro přihlášení k virtuálnímu počítači. |

## <a name="get-eflowvmedgeinformation"></a>Get-EflowVmEdgeInformation

Příkaz **Get-EflowVmEdgeInformation** se používá ke shromáždění a zobrazení informací o IoT Edge z virtuálního počítače, jako je například verze IoT Edge, na které je virtuální počítač spuštěný. Další informace získáte pomocí příkazu `Get-Help Get-EflowVmEdgeInformation -full` .

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| vmUser | Maximálně 30 znaků | Uživatelské jméno pro přihlášení k virtuálnímu počítači. |

## <a name="get-eflowvmedgemodulelist"></a>Get-EflowVmEdgeModuleList

Příkaz **Get-EflowVmEdgeModuleList** se používá k dotazování a zobrazení seznamu modulů IoT Edge běžících na virtuálním počítači. Další informace získáte pomocí příkazu `Get-Help Get-EflowVmEdgeModuleList -full` .

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| vmUser | Maximálně 30 znaků | Uživatelské jméno pro přihlášení k virtuálnímu počítači. |

## <a name="get-eflowvmedgestatus"></a>Get-EflowVmEdgeStatus

Příkaz **Get-EflowVmEdgeStatus** se používá k dotazování a zobrazení stavu modulu runtime IoT Edge na virtuálním počítači. Další informace získáte pomocí příkazu `Get-Help Get-EflowVmEdgeStatus -full` .

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| vmUser | Maximálně 30 znaků | Uživatelské jméno pro přihlášení k virtuálnímu počítači. |

## <a name="get-eflowvmusername"></a>Get-EflowVmUserName

Příkaz **Get-EflowVmUserName** se používá k dotazování a zobrazení uživatelského jména virtuálního počítače, které se použilo k vytvoření virtuálního počítače z registru. Přebírá jenom společné parametry. Další informace získáte pomocí příkazu `Get-Help Get-EflowVmUserName -full` .

## <a name="get-eflowvmsshkey"></a>Get-EflowVmSshKey

Příkaz **Get-EflowVmSshKey** se používá k dotazování a zobrazení klíče SSH používaného virtuálním počítačem. Přebírá jenom společné parametry. Další informace získáte pomocí příkazu `Get-Help Get-EflowVmSshKey -full` .

## <a name="ssh-eflowvm"></a>Ssh-EflowVm

Příkaz **SSH-EflowVm** se používá k SSH do virtuálního počítače. Jediným účtem povoleným pro SSH k virtuálnímu počítači je uživatel, který ho vytvořil. Další informace získáte pomocí příkazu `Get-Help Ssh-EflowVm -full` .

| Parametr | Přípustné hodnoty | Komentáře |
| --------- | --------------- | -------- |
| vmUser | Maximálně 30 znaků | Uživatelské jméno pro přihlášení k virtuálnímu počítači. |

## <a name="next-steps"></a>Další kroky

Naučte se používat tyto příkazy v následujícím článku:

* [Nainstalovat Azure IoT Edge pro Linux ve Windows](./how-to-install-iot-edge-windows-on-windows.md)

* Všechny příkazy dostupné prostřednictvím PowerShellu najdete v [referenčních informacích ke skriptům IoT Edge pro Linux v prostředí Windows PowerShell](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) .