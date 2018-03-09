---
title: "Odeberte servery a zakažte | Microsoft Docs"
description: "Tento článek popisuje, jak zrušit registraci serveru z trezoru Site Recovery a zakažte ochranu pro virtuální počítače a fyzické servery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: raynew
ms.openlocfilehash: 16a5eaac1138d328f81cfa7d50f8705da867e352
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="remove-servers-and-disable-protection"></a>Odebrání serverů a zakázání ochrany

Tento článek popisuje, jak zrušit registraci serveru z trezoru služeb zotavení a jak zakázat ochranu pro počítače chráněné službou Site Recovery.


## <a name="unregister-a--configuration-server"></a>Zrušení registrace konfigurace serveru

Pokud budete replikovat virtuální počítače VMware nebo fyzických serverů Windows nebo Linuxem do Azure, můžete zrušit registraci serveru bez připojení konfiguraci z úložiště následujícím způsobem:

1. [Zakažte ochranu virtuálních počítačů](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. [Zrušte přidružení nebo odstranit](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) zásady replikace.
3. [Odstraňte konfigurační server](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Zrušit registraci serveru VMM

1. Zastavte replikaci virtuálních počítačů v cloudu na VMM serveru, který chcete odebrat.
2. Odstraňte veškerá jeho mapování sítě používá cloudy na serveru VMM, který chcete odstranit. V **infrastruktura Site Recovery** > **pro System Center VMM** > **mapování sítě**, klikněte pravým tlačítkem na mapování sítě > **Odstranit**.
3. Poznamenejte si ID serveru VMM.
4. Zrušit přidružení zásad replikace z cloudy na serveru VMM, který chcete odebrat.  V **infrastruktura Site Recovery** > **pro System Center VMM** >  **zásady replikace**, dvakrát klikněte na přidružené zásady. Klikněte pravým tlačítkem na cloudu > **zrušení spojení**.
5. Odstraňte VMM server nebo aktivní uzel. V **infrastruktura Site Recovery** > **pro System Center VMM** > **servery VMM**, pravým tlačítkem na server > **odstranit** .
6. Pokud byl VMM server v odpojeném stavu, pak stáhněte a spusťte [skript pro vyčištění](http://aka.ms/asr-cleanup-script-vmm) na serveru VMM. Otevřete prostředí PowerShell se **spustit jako správce** možnost, chcete-li změnit zásady spouštění pro obor výchozí (LocalMachine). Ve skriptu zadejte ID serveru VMM, který chcete odebrat. Skript odebere registrace a cloudového párování informací ze serveru.
5. Spusťte skript vyčištění na žádném sekundárního serveru VMM.
6. Spusťte skript vyčištění na žádné jiné pasivní uzly clusteru VMM které mají nainstalovaného zprostředkovatele.
7. Zprostředkovatel na serveru VMM ručně odinstalujte. Pokud máte cluster, odeberte ze všech uzlů.
8. Pokud vaše virtuální počítače byly replikaci do Azure, je potřeba odinstalovat z hostitelů Hyper-V v cloudech odstraněného agenta služeb zotavení Microsoft.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Zrušit registraci hostitele Hyper-V v lokalitě Hyper-V

Hostitelé Hyper-V, které nejsou spravované přes VMM jsou shromážděná do lokality Hyper-V. Odeberte hostitele v lokalitě Hyper-V následujícím způsobem:

1. Zakážete replikaci pro virtuální počítače Hyper-V umístěný na hostiteli.
2. Zrušit přidružení zásad pro web Hyper-V. V **infrastruktura Site Recovery** > **pro weby technologie Hyper-V** >  **zásady replikace**, dvakrát klikněte na přidružené zásady. Klikněte pravým tlačítkem na webu > **zrušení spojení**.
3. Odstraňte hostitele Hyper-V. V **infrastruktura Site Recovery** > **pro weby technologie Hyper-V** > **hostitelů Hyper-V**, pravým tlačítkem na server > **odstranit** .
4. Po odebrání všech hostitelů z něj, odstraníte web Hyper-V. V **infrastruktura Site Recovery** > **pro weby technologie Hyper-V** > **lokalit Hyper-V**, klikněte pravým tlačítkem na webu > **odstranit** .
5. Pokud váš hostitel Hyper-V byl v **odpojeno** stavu, a poté spusťte následující skript na každém hostiteli technologie Hyper-V, který jste odebrali. Skript vyčistí nastavení na serveru a zruší registraci v trezoru.



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
                "Registry keys removed."
            }

            # First retrive all the certificates to be deleted
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
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd



## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Zakažte ochranu pro virtuální počítač VMware nebo fyzických serverů (VMware do Azure)

1. V **chráněné položky** > **replikované položky**, pravým tlačítkem na počítač > **zakažte replikaci**.
2. V **zakažte replikaci** vyberte jednu z těchto možností:
    - **Zakázat replikaci a odebrat (doporučeno)** – tato možnost odebrání replikované položky z Azure Site Recovery a replikaci pro počítač je zastavena. Konfigurace replikace na konfigurační Server je vyčištěna a zastaví se Site Recovery fakturace tohoto chráněného serveru.
    - **Odebrat** – tato možnost by měla používat pouze v případě, že zdrojové prostředí je Odstraněná nebo není dostupný (nepřipojeno). Tím se odebere replikované položky z Azure Site Recovery (zastavena fakturace). Konfigurace replikace na konfiguračním serveru **nikoli** vyčistit. 

> [!NOTE]
> V obou možností, které služba mobility nebude odinstalován ze chráněných serverů musíte odinstalovat ručně. Pokud budete chtít chránit server znovu s použitím na stejný konfigurační server, můžete přeskočit odinstalace služby mobility.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Zakažte ochranu pro virtuální počítač technologie Hyper-V (Hyper-V do Azure)

> [!NOTE]
> Tento postup použijte, pokud replikujete virtuální počítače Hyper-V do Azure bez serveru VMM. Pokud replikujete virtuální počítače pomocí **System Center VMM do Azure** scénář, postupujte podle pokynů [zakažte ochranu technologie Hyper-V probíhá replikace virtuálního počítače pomocí System Center VMM ke Azure scénář](#disable-protection-for-a-hyper-v-virtual-machine-replicating-using-the-system-centet-vmm-to-azure-scenario)

1. V **chráněné položky** > **replikované položky**, pravým tlačítkem na počítač > **zakažte replikaci**.
2. V **zakažte replikaci**, můžete vybrat následující možnosti:
     - **Zakázat replikaci a odebrat (doporučeno)** – tato možnost odebrání replikované položky z Azure Site Recovery a replikaci pro počítač je zastavena. Konfigurace replikace na virtuálním počítači místní se vyčistí a zastaví se Site Recovery fakturace tohoto chráněného serveru.
    - **Odebrat** – tato možnost by měla používat pouze v případě, že zdrojové prostředí je Odstraněná nebo není dostupný (nepřipojeno). Tím se odebere replikované položky z Azure Site Recovery (zastavena fakturace). Konfigurace replikace na virtuálním počítači místní **nikoli** vyčistit. 

    > [!NOTE]
    > Pokud jste zvolili **odebrat** možnost, spusťte následující sadu skripty a vyčistit nastavení replikace na místní Server Hyper-V.
1. Na zdrojovém technologie Hyper-V hostitele serveru odebrat replikaci pro virtuální počítač. Nahraďte SQLVM1 s názvem virtuální počítač a spusťte skript z správu prostředí PowerShell


    
    $vmName = "SQLVM1"  $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"  $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  $replicationService.RemoveReplicationRelationship($vm.__PATH)
    

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Zakažte ochranu pro virtuální počítač technologie Hyper-V replikovat do Azure pomocí System Center VMM do Azure scénáře

1. V **chráněné položky** > **replikované položky**, pravým tlačítkem na počítač > **zakažte replikaci**.
2. V **zakažte replikaci**, vyberte jednu z těchto možností:

    - **Zakázat replikaci a odebrat (doporučeno)** – tato možnost odebrání replikované položky z Azure Site Recovery a replikaci pro počítač je zastavena. Konfigurace replikace na virtuálním počítači místní je vyčištěna a zastaví se Site Recovery fakturace tohoto chráněného serveru.
    - **Odebrat** – tato možnost by měla používat pouze v případě, že zdrojové prostředí je Odstraněná nebo není dostupný (nepřipojeno). Tím se odebere replikované položky z Azure Site Recovery (zastavena fakturace). Konfigurace replikace na virtuálním počítači místní **nikoli** vyčistit. 

    > [!NOTE]
    > Pokud jste zvolili **odebrat** možnost, pak tun následující skriptů a vyčistit nastavení replikace na místním serveru VMM.
3. Spusťte tento skript na zdrojovém serveru VMM, pomocí prostředí PowerShell (vyžaduje oprávnění správce) z konzoly nástroje VMM. Nahraďte zástupný symbol **SQLVM1** s názvem virtuálního počítače.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Výše uvedené kroky vymazat nastavení replikace na serveru VMM. Na zastavení replikace pro virtuální počítač běží na serveru hostitele technologie Hyper-V, spusťte tento skript. Nahraďte SQLVM1 název virtuálního počítače a host01.contoso.com s názvem serveru hostitele technologie Hyper-V.

    
    $vmName = "SQLVM1"  $hostName  = "host01.contoso.com"  $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName  $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName  $replicationService.RemoveReplicationRelationship($vm.__PATH)
    
       
 
## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Zakažte ochranu pro virtuální počítač technologie Hyper-V replikovat na sekundární Server VMM pomocí System Center VMM pro VMM scénář

1. V **chráněné položky** > **replikované položky**, pravým tlačítkem na počítač > **zakažte replikaci**.
2. V **zakažte replikaci**, vyberte jednu z těchto možností:

    - **Zakázat replikaci a odebrat (doporučeno)** – tato možnost odebrání replikované položky z Azure Site Recovery a replikaci pro počítač je zastavena. Konfigurace replikace na virtuálním počítači místní je vyčištěna a zastaví se Site Recovery fakturace tohoto chráněného serveru.
    - **Odebrat** – tato možnost by měla používat pouze v případě, že zdrojové prostředí je Odstraněná nebo není dostupný (nepřipojeno). Tím se odebere replikované položky z Azure Site Recovery (zastavena fakturace). Konfigurace replikace na virtuálním počítači místní **nikoli** vyčistit. Spusťte následující sadu skripty za účelem vyčištění replikace nastavení místní virtuálních počítačů.
> [!NOTE]
> Pokud jste zvolili **odebrat** možnost, pak tun následující skriptů a vyčistit nastavení replikace na místním serveru VMM.

3. Spusťte tento skript na zdrojovém serveru VMM, pomocí prostředí PowerShell (vyžaduje oprávnění správce) z konzoly nástroje VMM. Nahraďte zástupný symbol **SQLVM1** s názvem virtuálního počítače.

         $vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Na sekundárním serveru VMM spusťte tento skript k vyčištění nastavení pro sekundární virtuální počítač:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. Na sekundárním serveru VMM aktualizujte virtuální počítače na serveru hostitele technologie Hyper-V tak, aby sekundární virtuální počítač získá zjistil znovu v konzole nástroje VMM.
6. Výše uvedené kroky vymazat nastavení replikace na serveru VMM. Pokud chcete na zastavení replikace pro virtuální počítač, spusťte následující skript jejda primární a sekundární virtuální počítače. Nahraďte SQLVM1 název virtuálního počítače.

        Remove-VMReplication –VMName “SQLVM1”




