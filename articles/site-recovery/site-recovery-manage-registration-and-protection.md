---
title: Odebrání serverů a zakázání ochrany | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak zrušit registraci serverů z trezoru obnovení webu a zakázat ochranu virtuálních počítačů a fyzických serverů.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: rajanaki
ms.openlocfilehash: a411fc9a95bef595a8fc49cad77189bb88fb7661
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257625"
---
# <a name="remove-servers-and-disable-protection"></a>Odebrání serverů a zakázání ochrany

Tento článek popisuje, jak zrušit registraci serverů z trezoru služby Recovery Services a jak zakázat ochranu počítačů chráněných službou Site Recovery.


## <a name="unregister-a--configuration-server"></a>Zrušení registrace konfiguračního serveru

Pokud replikujete virtuální počítače VMware nebo fyzické servery Windows/Linux do Azure, můžete zrušit registraci nepřipojeného konfiguračního serveru z trezoru následujícím způsobem:

1. [Zakázat ochranu virtuálních počítačů](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. [Zrušte přidružení nebo odstraňte](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) zásady replikace.
3. [Odstranění konfiguračního serveru](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Zrušení registrace serveru VMM

1. Zastavte replikaci virtuálních počítačů v cloudech na serveru VMM, který chcete odebrat.
2. Odstraňte všechna síťová mapování používaná cloudy na serveru VMM, který chcete odstranit. V **části Site Recovery Infrastructure** > **For System Center VMM** > **Network Mapping**klepněte pravým tlačítkem myši na mapování sítě > **Odstranit**.
3. Poznamenejte si ID serveru VMM.
4. Odpojte zásady replikace od cloudů na serveru VMM, který chcete odebrat.  V **části Infrastruktura** > obnovení sítě pro**zásady replikace****v systémovém centru VMM** >  poklepejte na přidružené zásady. Klepněte pravým tlačítkem myši na cloud > **zrušit přidružení**.
5. Odstraňte server VMM nebo aktivní uzel. V **části Site Recovery Infrastructure** > **For System Center VMM** > **VMM Servers**klepněte pravým tlačítkem myši na server > **Odstranit**.
6. Pokud byl server VMM ve stavu Odpojeno, stáhněte a spusťte [skript vyčištění](https://aka.ms/asr-cleanup-script-vmm) na serveru VMM. Otevřete powershell s možností **Spustit jako správce** a změňte zásady spuštění pro výchozí obor (LocalMachine). Ve skriptu zadejte ID serveru VMM, který chcete odebrat. Skript odebere informace o registraci a cloudu párování ze serveru.
5. Spusťte skript vyčištění na libovolném sekundárním serveru VMM.
6. Spusťte skript vyčištění na všech jiných pasivních uzlech clusteru VMM, které mají nainstalovaného zprostředkovatele.
7. Odinstalujte zprostředkovatele ručně na serveru VMM. Pokud máte cluster, odeberte ze všech uzlů.
8. Pokud se vaše virtuální počítače replikovaly do Azure, musíte odinstalovat agenta služby Microsoft Recovery Services z hostitelů Hyper-V v odstraněných cloudech.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Zrušení registrace hostitele Hyper-V na webu Hyper-V

Hostitelé hyper-V, které nejsou spravovány vvm jsou shromážděny do webu Hyper-V. Odeberte hostitele na webu Hyper-V následujícím způsobem:

1. Zakažte replikaci pro virtuální počítači Hyper-V umístěné na hostiteli.
2. Odpojte zásady pro web Hyper-V. V **části Infrastruktura** > obnovení sítě pro**zásady replikace****webů** >  hyperv . Klepněte pravým tlačítkem myši na web > **zrušit přidružení**.
3. Odstraňte hostitele Hyper-V. V **části Infrastruktura** > pro obnovení webu**pro hostitele hyper-v. sítě** > **Hyper-V**klepněte pravým tlačítkem myši na server > **Odstranit**.
4. Odstraňte web Hyper-V poté, co z něj byli odebráni všichni hostitelé. V **části Infrastruktura** > pro obnovení webu**pro weby** > **Hyper-V sítě Hyper-V**klikněte pravým tlačítkem myši na web > **Odstranit**.
5. Pokud byl váš hostitel Hyper-V ve **stavu Odpojeno,** spusťte následující skript na každém odebraném hostiteli Hyper-V. Skript vyčistí nastavení na serveru a zruší jeho registraci z úložiště.


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


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Zakázání ochrany virtuálního počítače VMware nebo fyzického serveru (VMware do Azure)

1. V **části Replikované položky chráněné** > **položky**klepněte pravým tlačítkem myši na počítač > **Zakázat replikaci**.
2. V **části Zakázat stránku replikace** vyberte jednu z těchto možností:
    - **Zakázat replikaci a odebrat (doporučeno)** – Tato možnost odebere replikovanou položku z azure site recovery a replikace pro počítač se zastaví. Konfigurace replikace na konfiguračním serveru je vyčištěna a fakturace obnovení sítě pro tento chráněný server je zastavena. Všimněte si, že tuto možnost lze použít pouze v případě, že konfigurační server je v připojeném stavu.
    - **Odebrat** – Tato možnost se má použít pouze v případě, že zdrojové prostředí je odstraněno nebo není přístupné (není připojeno). Tím odeberete replikovanou položku z Azure Site Recovery (fakturace se zastaví). Konfigurace replikace na konfiguračním serveru **nebude** vyčištěna. 

> [!NOTE]
> V obou možnostech služba mobility nebude odinstalována z chráněných serverů, je třeba ji odinstalovat ručně. Pokud plánujete server znovu chránit pomocí stejného konfiguračního serveru, můžete odinstalovat službu mobility.

> [!NOTE]
> Pokud jste už došlo k selhání přes virtuální počítač a běží v Azure, všimněte si, že zakázat ochranu neodebere nebo ovlivnit převzetí počítače převzetí počítače.
## <a name="disable-protection-for-a-azure-vm-azure-to-azure"></a>Zakázání ochrany virtuálního počítače Azure (Azure to Azure)

-  V **části Replikované položky chráněné** > **položky**klepněte pravým tlačítkem myši na počítač > **Zakázat replikaci**.
> [!NOTE]
> služba mobility nebude odinstalována z chráněných serverů, je třeba ji odinstalovat ručně. Pokud plánujete server znovu chránit, můžete odinstalovat službu mobility.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Zakázání ochrany virtuálního počítače Hyper-V (Hyper-V až Azure)

> [!NOTE]
> Tento postup použijte, pokud replikujete virtuální počítače Hyper-V do Azure bez serveru VMM. Pokud replikujete virtuální počítače pomocí scénáře **System Center VMM to Azure,** postupujte podle pokynů Zakázat ochranu virtuálního počítače Hyper-V, který se replikuje pomocí scénáře System Center VMM to Azure

1. V **části Replikované položky chráněné** > **položky**klepněte pravým tlačítkem myši na počítač > **Zakázat replikaci**.
2. V **části Zakázat replikaci**můžete vybrat následující možnosti:
   - **Zakázat replikaci a odebrat (doporučeno)** – Tato možnost odebere replikovanou položku z azure site recovery a replikace pro počítač se zastaví. Konfigurace replikace v místním virtuálním počítači bude vyčištěna a fakturace obnovení webu pro tento chráněný server se zastaví.
   - **Odebrat** – Tato možnost se má použít pouze v případě, že zdrojové prostředí je odstraněno nebo není přístupné (není připojeno). Tím odeberete replikovanou položku z Azure Site Recovery (fakturace se zastaví). Konfigurace replikace v místním virtuálním počítači **nebude** vyčištěna. 

 > [!NOTE]
     > Pokud jste zvolili možnost **Odebrat,** spusťte následující sadu skriptů, abyste vyčistili místní server Hyper-V Server.

> [!NOTE]
> Pokud jste už došlo k selhání přes virtuální počítač a běží v Azure, všimněte si, že zakázat ochranu neodebere nebo ovlivnit převzetí počítače převzetí počítače.

1. Na zdrojovém hostitelském serveru Hyper-V odeberte replikaci virtuálního počítače. Nahrazení SQLVM1 názvem virtuálního počítače a spuštění skriptu z prostředí PowerShell pro správu

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Zakázání ochrany virtuálního počítače Hyper-V, který se replikuje do Azure pomocí scénáře VMM system center do Azure

1. V **části Replikované položky chráněné** > **položky**klepněte pravým tlačítkem myši na počítač > **Zakázat replikaci**.
2. V **části Zakázat replikaci**vyberte jednu z těchto možností:

   - **Zakázat replikaci a odebrat (doporučeno)** – Tato možnost odebere replikovanou položku z azure site recovery a replikace pro počítač se zastaví. Konfigurace replikace v místním virtuálním počítači je vyčištěna a fakturace obnovení webu pro tento chráněný server je zastavena.
   - **Odebrat** – Tato možnost se má použít pouze v případě, že zdrojové prostředí je odstraněno nebo není přístupné (není připojeno). Tím odeberete replikovanou položku z Azure Site Recovery (fakturace se zastaví). Konfigurace replikace v místním virtuálním počítači **nebude** vyčištěna. 

     > [!NOTE]
     > Pokud jste zvolili možnost **Odebrat,** nalaďte následující skripty, abyste vyčistili nastavení replikace v místním serveru VMM.
3. Spusťte tento skript na zdrojovém serveru VMM pomocí prostředí PowerShell (vyžadována oprávnění správce) z konzoly VMM. Nahraďte zástupný **sqlvm1** názvem virtuálního počítače.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Výše uvedené kroky vymazat nastavení replikace na serveru VMM. Chcete-li zastavit replikaci virtuálního počítače spuštěného na hostitelském serveru Hyper-V, spusťte tento skript. Nahraďte SQLVM1 názvem virtuálního počítače a host01.contoso.com názvem hostitelského serveru Hyper-V.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Zakázání ochrany virtuálního počítače Hyper-V, který se replikuje se sekundárním serverem VMM pomocí scénáře VMM v systémovém centru do nástroje VMM

1. V **části Replikované položky chráněné** > **položky**klepněte pravým tlačítkem myši na počítač > **Zakázat replikaci**.
2. V **části Zakázat replikaci**vyberte jednu z těchto možností:

   - **Zakázat replikaci a odebrat (doporučeno)** – Tato možnost odebere replikovanou položku z azure site recovery a replikace pro počítač se zastaví. Konfigurace replikace v místním virtuálním počítači je vyčištěna a fakturace obnovení webu pro tento chráněný server je zastavena.
   - **Odebrat** – Tato možnost se má použít pouze v případě, že zdrojové prostředí je odstraněno nebo není přístupné (není připojeno). Tím odeberete replikovanou položku z Azure Site Recovery (fakturace se zastaví). Konfigurace replikace v místním virtuálním počítači **nebude** vyčištěna. Spusťte následující sadu skriptů a vyčistěte místní virtuální počítače nastavení replikace.
     > [!NOTE]
     > Pokud jste zvolili možnost **Odebrat,** nalaďte následující skripty, abyste vyčistili nastavení replikace v místním serveru VMM.

3. Spusťte tento skript na zdrojovém serveru VMM pomocí prostředí PowerShell (vyžadována oprávnění správce) z konzoly VMM. Nahraďte zástupný **sqlvm1** názvem virtuálního počítače.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Na sekundárním serveru VMM spusťte tento skript a vyčistěte nastavení sekundárního virtuálního počítače:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. Na sekundárním serveru VMM aktualizujte virtuální počítače na hostitelském serveru Hyper-V, aby sekundární virtuální počítač byl znovu rozpoznán v konzole VMM.
6. Výše uvedené kroky vymažte nastavení replikace na serveru VMM. Pokud chcete zastavit replikaci pro virtuální počítač, spusťte následující skript oh primární a sekundární virtuální počítače. Nahraďte SQLVM1 názvem virtuálního počítače.

        Remove-VMReplication –VMName “SQLVM1”




