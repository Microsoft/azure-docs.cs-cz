---
title: Odebrat servery a zakázat ochranu | Microsoft Docs
description: Tento článek popisuje, jak zrušit registraci serverů z Site Recoveryového trezoru a zakázat ochranu pro virtuální počítače a fyzické servery.
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: sharrai
ms.openlocfilehash: a4f6c318a7521e1fbc03ff3a47e34e992cce44df
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89424782"
---
# <a name="remove-servers-and-disable-protection"></a>Odebrání serverů a zakázání ochrany

Tento článek popisuje, jak zrušit registraci serverů z Recovery Servicesového trezoru a jak zakázat ochranu pro počítače chráněné pomocí Site Recovery.


## <a name="unregister-a--configuration-server"></a>Zrušení registrace konfiguračního serveru

Pokud virtuální počítače VMware nebo fyzické servery s Windows nebo Linux replikujte do Azure, můžete zrušit registraci nepřipojeného konfiguračního serveru z trezoru následujícím způsobem:

1. [Zakažte ochranu virtuálních počítačů](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. [Zruší přidružení nebo odstranění](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) zásad replikace.
3. [Odstranit konfigurační server](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Zrušení registrace serveru VMM

1. Zastavte replikaci virtuálních počítačů v cloudech na serveru VMM, který chcete odebrat.
2. Odstraňte všechna mapování sítě používaná cloudy na serveru VMM, který chcete odstranit. V **Site Recovery infrastruktury**  >  **pro mapování sítě nástroje System Center VMM**klikněte  >  **Network Mapping**pravým tlačítkem na mapování sítě > **Odstranit**.
3. Poznamenejte si ID serveru VMM.
4. Zrušte přidružení zásad replikace k cloudům na serveru VMM, který chcete odebrat.  V **Site Recovery infrastruktury**  >  **pro**  >   **Zásady replikace**System Center VMM poklikejte na přidruženou zásadu. Klikněte pravým tlačítkem na Cloud > **zrušit přidružení**.
5. Odstraňte server VMM nebo aktivní uzel. V **Site Recovery infrastruktury**  >  **pro servery System Center VMM**  >  **VMM**klikněte pravým tlačítkem na server > **Odstranit**.
6. Pokud byl server VMM v odpojeném stavu, Stáhněte a spusťte [čisticí skript](https://aka.ms/asr-cleanup-script-vmm) na serveru VMM. Otevřete PowerShell pomocí možnosti **Spustit jako správce** a změňte zásady spouštění pro výchozí obor (LocalMachine). Ve skriptu zadejte ID serveru VMM, který chcete odebrat. Skript odebere ze serveru informace o párování registrace a cloudu.
5. Spusťte čisticí skript na jakémkoli sekundárním serveru VMM.
6. Spusťte čisticí skript na všech ostatních pasivních uzlech clusteru VMM, které mají nainstalovaného poskytovatele.
7. Ručně odinstalujte zprostředkovatele na serveru VMM. Pokud máte cluster, odeberte ho ze všech uzlů.
8. Pokud se virtuální počítače replikují do Azure, musíte agenta Microsoft Recovery Servicese odinstalovat z hostitelů Hyper-V v odstraněných cloudech.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Zrušení registrace hostitele Hyper-V v lokalitě Hyper-V

Hostitelé Hyper-V, které nejsou spravovány nástrojem VMM, se shromažďují do lokality Hyper-V. Odeberte hostitele v lokalitě Hyper-V následujícím způsobem:

1. Zakažte replikaci pro virtuální počítače Hyper-V umístěné na hostiteli.
2. Zruší přidružení zásad pro lokalitu Hyper-V. V **Site Recovery infrastruktury**  >  **pro zásady replikace lokalit technologie Hyper-V**  >   **Replication Policies**poklikejte na přidruženou zásadu. Klikněte pravým tlačítkem na lokalitu > **zrušit přidružení**.
3. Odstraňte hostitele Hyper-V. V **Site Recovery infrastruktury**  >  **pro**hostitele Hyper-v na serveru Hyper-  >  **v**klikněte pravým tlačítkem myši na server > **Odstranit**.
4. Po odebrání všech hostitelů odstraňte lokalitu Hyper-V. V **Site Recovery infrastruktury**pro weby Hyper-v v prostředí Hyper-  >  **For Hyper-V Sites**  >  **v**klikněte pravým tlačítkem myši na lokalitu > **Odstranit**.
5. Pokud byl Hostitel Hyper-V v **odpojeném** stavu, spusťte na každém hostiteli Hyper-v, který jste odebrali, následující skript. Skript vyčistí nastavení na serveru a zruší jeho registraci v trezoru.


```powershell
        pushd .
        try
        {
            $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
            $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
            $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
            $isAdmin=$principal.IsInRole($administrators)
            if (!$isAdmin)
            {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;
            }

            $error.Clear()
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
            $idMgmtCloudContainerId='IdMgmtCloudContainerId'


            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
                {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                if($regNode.IdMgmtCloudContainerId -ne $null)
                {
                    "Removing IdMgmtCloudContainerId"
                    Remove-ItemProperty -Path $asrHivePath -Name $idMgmtCloudContainerId
                }
                "Registry keys removed."
            }

            # First retrieve all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {
            [system.exception]
            Write-Host "Error occurred" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd
```


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Zakázání ochrany pro virtuální počítač VMware nebo fyzický server (z VMware do Azure)

1. V části **chráněné položky**  >  **replikované**položky klikněte pravým tlačítkem na počítač > **Zakázat replikaci**.
2. Na stránce **Zakázat replikaci** vyberte jednu z těchto možností:
    - **Zakázat replikaci a odebrat (doporučeno)** – Tato možnost odebere replikovanou položku z Azure Site Recovery a replikace pro tento počítač se zastaví. Konfigurace replikace na konfiguračním serveru se vyčistí a Site Recovery se fakturace pro tento chráněný Server zastavila. Všimněte si, že tuto možnost lze použít pouze v případě, že je konfigurační server v připojeném stavu.
    - **Odebrat** – Tato možnost se má použít jenom v případě, že se zdrojové prostředí odstraní nebo není dostupné (Nepřipojeno). Tím se odebere replikovaná položka z Azure Site Recovery (fakturace je zastavená). Konfigurace replikace na konfiguračním serveru **se** nevyčistí. 

> [!NOTE]
> Ve službě mobility možností se z chráněných serverů neodinstaluje, budete je muset odinstalovat ručně. Pokud plánujete chránit server znovu pomocí stejného konfiguračního serveru, můžete přeskočit odinstalaci služby mobility.

> [!NOTE]
> Pokud jste už provedli převzetí služeb při selhání virtuálního počítače, který je spuštěný v Azure, pamatujte na to, že zakázat ochranu neodebere nebo neovlivní virtuální počítač se službou převzít
## <a name="disable-protection-for-a-azure-vm-azure-to-azure"></a>Zakázání ochrany pro virtuální počítač Azure (Azure do Azure)

-  V části **chráněné položky**  >  **replikované**položky klikněte pravým tlačítkem na počítač > **Zakázat replikaci**.
> [!NOTE]
> Služba mobility nebude odinstalována z chráněných serverů, je nutné ji odinstalovat ručně. Pokud plánujete Server chránit znovu, můžete přeskočit odinstalaci služby mobility.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Zakázání ochrany pro virtuální počítač Hyper-V (Hyper-V do Azure)

> [!NOTE]
> Tento postup použijte v případě, že provádíte replikaci virtuálních počítačů Hyper-V do Azure bez serveru VMM. Pokud provádíte replikaci virtuálních počítačů pomocí scénáře **System Center VMM do Azure** , postupujte podle pokynů pro zakázání ochrany virtuálního počítače Hyper-V replikování pomocí nástroje System Center VMM do Azure.

1. V části **chráněné položky**  >  **replikované**položky klikněte pravým tlačítkem na počítač > **Zakázat replikaci**.
2. V části **Zakázat replikaci**můžete vybrat následující možnosti:
   - **Zakázat replikaci a odebrat (doporučeno)** – Tato možnost odebere replikovanou položku z Azure Site Recovery a replikace pro tento počítač se zastaví. Konfigurace replikace na místním virtuálním počítači se vyčistí a Site Recovery se fakturace pro tento chráněný Server zastaví.
   - **Odebrat** – Tato možnost se má použít jenom v případě, že se zdrojové prostředí odstraní nebo není dostupné (Nepřipojeno). Tím se odebere replikovaná položka z Azure Site Recovery (fakturace je zastavená). Konfigurace replikace na místním virtuálním počítači **se** nevyčistí. 

    > [!NOTE]
    > Pokud jste zvolili možnost **Odebrat** , spusťte následující sadu skriptů pro vyčištění nastavení replikace na místním serveru Hyper-V.

    > [!NOTE]
    > Pokud jste už provedli převzetí služeb při selhání virtuálního počítače, který je spuštěný v Azure, pamatujte na to, že zakázat ochranu neodebere nebo neovlivní virtuální počítač se službou převzít

1. Pro odebrání replikace virtuálního počítače na zdrojovém serveru hostitele Hyper-V. Nahraďte SQLVM1 názvem vašeho virtuálního počítače a spusťte skript z PowerShellu pro správu.

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Zakázání ochrany pro virtuální počítač Hyper-V, který se replikuje do Azure, pomocí scénáře pro System Center VMM do Azure

1. V části **chráněné položky**  >  **replikované**položky klikněte pravým tlačítkem na počítač > **Zakázat replikaci**.
2. V části **Zakázat replikaci**vyberte jednu z těchto možností:

   - **Zakázat replikaci a odebrat (doporučeno)** – Tato možnost odebere replikovanou položku z Azure Site Recovery a replikace pro tento počítač se zastaví. Konfigurace replikace na místním virtuálním počítači se vyčistí a Site Recovery se fakturace pro tento chráněný Server zastavila.
   - **Odebrat** – Tato možnost se má použít jenom v případě, že se zdrojové prostředí odstraní nebo není dostupné (Nepřipojeno). Tím se odebere replikovaná položka z Azure Site Recovery (fakturace je zastavená). Konfigurace replikace na místním virtuálním počítači **se** nevyčistí. 

     > [!NOTE]
     > Pokud jste zvolili možnost **Odebrat** , pak tun následující skripty a vyčistěte tak nastavení replikace na místním serveru VMM.
3. Spusťte tento skript na zdrojovém serveru VMM pomocí PowerShellu (požadovaná oprávnění správce) z konzoly VMM. Nahraďte zástupné symboly **SQLVM1** názvem vašeho virtuálního počítače.

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Set-SCVirtualMachine -VM $vm -ClearDRProtection
    ```

4. Výše uvedené kroky vymažou nastavení replikace na serveru VMM. Pokud chcete zastavit replikaci pro virtuální počítač běžící na hostitelském serveru Hyper-V, spusťte tento skript. Nahraďte SQLVM1 názvem vašeho virtuálního počítače a host01.contoso.com názvem hostitelského serveru Hyper-V.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Zakázání ochrany pro virtuální počítač s technologií Hyper-V, který se replikuje do sekundárního serveru VMM, pomocí scénáře pro System Center VMM do VMM

1. V části **chráněné položky**  >  **replikované**položky klikněte pravým tlačítkem na počítač > **Zakázat replikaci**.
2. V části **Zakázat replikaci**vyberte jednu z těchto možností:

   - **Zakázat replikaci a odebrat (doporučeno)** – Tato možnost odebere replikovanou položku z Azure Site Recovery a replikace pro tento počítač se zastaví. Konfigurace replikace na místním virtuálním počítači se vyčistí a Site Recovery se fakturace pro tento chráněný Server zastavila.
   - **Odebrat** – Tato možnost se má použít jenom v případě, že se zdrojové prostředí odstraní nebo není dostupné (Nepřipojeno). Tím se odebere replikovaná položka z Azure Site Recovery (fakturace je zastavená). Konfigurace replikace na místním virtuálním počítači **se** nevyčistí. Spusťte následující sadu skriptů pro vyčištění nastavení replikace místních virtuálních počítačů.
     > [!NOTE]
     > Pokud jste zvolili možnost **Odebrat** , pak tun následující skripty a vyčistěte tak nastavení replikace na místním serveru VMM.

3. Spusťte tento skript na zdrojovém serveru VMM pomocí PowerShellu (požadovaná oprávnění správce) z konzoly VMM. Nahraďte zástupné symboly **SQLVM1** názvem vašeho virtuálního počítače.

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Set-SCVirtualMachine -VM $vm -ClearDRProtection
    ```

4. Na sekundárním serveru VMM spuštěním tohoto skriptu vyčistěte nastavení pro sekundární virtuální počítač:

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Remove-SCVirtualMachine -VM $vm -Force
    ```

5. Na sekundárním serveru VMM aktualizujte virtuální počítače na hostitelském serveru Hyper-V, aby se sekundární virtuální počítač znovu zjistil v konzole VMM.
6. Výše uvedené kroky vymažou nastavení replikace na serveru VMM. Pokud chcete zastavit replikaci pro virtuální počítač, spusťte následující skript s primárním a sekundárním virtuálním počítačem. Nahraďte SQLVM1 názvem vašeho virtuálního počítače.

    ```powershell
    Remove-VMReplication –VMName "SQLVM1"
    ```
