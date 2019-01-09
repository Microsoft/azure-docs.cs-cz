---
title: Odebrání serverů a zakázání ochrany | Dokumentace Microsoftu
description: Tento článek popisuje, jak zrušit registraci serverů z trezoru služby Site Recovery a zakažte ochranu pro virtuální počítače a fyzické servery.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: 64011614c28fc2d94132e0bf4848b9a2c4fad599
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54118320"
---
# <a name="remove-servers-and-disable-protection"></a>Odebrání serverů a zakázání ochrany

Tento článek popisuje, jak zrušit registraci serverů z trezoru služby Recovery Services a jak zakázat ochranu pro počítače chráněné službou Site Recovery.


## <a name="unregister-a--configuration-server"></a>Zrušení registrace konfiguračního serveru

Pokud budete replikovat virtuální počítače VMware nebo fyzické servery Windows nebo Linuxem do Azure, můžete zrušit registraci nepřipojené konfigurační server z trezoru následujícím způsobem:

1. [Zakažte ochranu virtuálních počítačů](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. [Zrušit nebo odstranit](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) zásady replikace.
3. [Odstranění konfiguračního serveru](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Zrušení registrace serveru VMM

1. Zastavte replikaci virtuálních počítačů v cloudy na serveru VMM, který chcete odebrat.
2. Odstraňte všechna mapování sítě používané cloudy na serveru VMM, který chcete odstranit. V **infrastruktura Site Recovery** > **pro System Center VMM** > **mapování sítě**, klikněte pravým tlačítkem na mapování sítě > **Odstranit**.
3. Poznamenejte si ID serveru VMM.
4. Zrušit přidružení zásad replikace z cloudy na serveru VMM, který chcete odebrat.  V **infrastruktura Site Recovery** > **pro System Center VMM** >  **zásady replikace**, dvakrát klikněte na panel přidružené zásady. Klikněte pravým tlačítkem na cloudu > **zrušení spojení**.
5. Odstraňte VMM server nebo aktivní uzel. V **infrastruktura Site Recovery** > **pro System Center VMM** > **servery VMM**, klikněte pravým tlačítkem na server > **odstranit** .
6. Pokud váš server VMM v odpojeném stavu, pak si stáhnout a spustit [skript pro vyčištění](https://aka.ms/asr-cleanup-script-vmm) na serveru VMM. Otevřete prostředí PowerShell se službou **spustit jako správce** možnost, chcete-li změnit zásady spouštění pro obor výchozí (LocalMachine). Ve skriptu zadejte ID serveru VMM, který chcete odebrat. Tento skript odebere registraci a cloudové párování informace ze serveru.
5. Spusťte skript vyčištění na žádném sekundárního serveru VMM.
6. Spusťte skript vyčištění na všechny další pasivní uzly clusteru VMM, které mají nainstalovaný poskytovatel.
7. Odinstalace poskytovatele ručně na serveru VMM. Pokud máte cluster, odeberte ze všech uzlů.
8. Pokud vaše virtuální počítače byly replikaci do Azure, je potřeba odinstalovat agenta služeb zotavení Microsoft z hostitelů Hyper-V v cloudech odstraněné.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Zrušit registraci hostitele Hyper-v do lokality Hyper-V

Hostitelé Hyper-V, které nejsou spravovány nástrojem VMM se shromažďují do lokality Hyper-V. Odeberte hostitele v lokalitě Hyper-V následujícím způsobem:

1. Zakažte replikaci pro virtuální počítače Hyper-V na hostiteli.
2. Zrušit přidružení zásad pro lokalitu Hyper-V. V **infrastruktura Site Recovery** > **pro servery Hyper-V** >  **zásady replikace**, dvakrát klikněte na panel přidružené zásady. Klikněte pravým tlačítkem na webu > **zrušení spojení**.
3. Odstranění hostitele Hyper-V. V **infrastruktura Site Recovery** > **pro servery Hyper-V** > **hostitele Hyper-V**, klikněte pravým tlačítkem na server > **odstranit** .
4. Odstraníte lokalitu Hyper-V po z něj byly odebrány všechny hostitele. V **infrastruktura Site Recovery** > **pro servery Hyper-V** > **lokalit Hyper-V**, klikněte pravým tlačítkem na webu > **odstranit** .
5. Pokud se váš hostitel Hyper-v **odpojeno** stavu a pak spusťte následující skript na každém hostiteli Hyper-V, který jste odebrali. Skript vyčistí nastavení na serveru a zruší jeho registraci v trezoru.


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


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Zakažte ochranu pro virtuální počítač VMware nebo fyzický server (z VMware do Azure)

1. V **chráněné položky** > **replikované položky**, klikněte pravým tlačítkem na počítač > **zakázat replikaci**.
2. V **zakázat replikaci** stránky, vyberte jednu z těchto možností:
    - **Zakázat replikaci a odebrat (doporučeno)** – tato možnost odebere replikovanou položku z Azure Site Recovery a replikace pro počítač se zastaví. Konfigurace replikace na konfiguračním serveru se vyčistí a zastaví se fakturace služby Site Recovery pro tento chráněný server. Všimněte si, že tuto možnost jde použít jenom při konfiguraci serveru v připojeném stavu.
    - **Odebrat** – tato možnost by měla použít pouze v případě, že zdrojové prostředí je Odstraněná nebo není přístupný (nepřipojeno). To odebere replikovanou položku z Azure Site Recovery (účtování se ukončí). Konfigurace replikace na konfiguračním serveru **nebudou** vyčistit. 

> [!NOTE]
> V obou možností, které služba mobility neodinstaluje z chráněných serverů musíte odinstalovat ručně. Pokud budete chránit server znovu pomocí stejný konfigurační server, můžete přeskočit odinstalace služby mobility.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Zakažte ochranu pro virtuální počítač Hyper-V (Hyper-V do Azure)

> [!NOTE]
> Tento postup použijte, pokud provádíte replikaci virtuálních počítačů Hyper-V do Azure bez serveru VMM. Pokud replikujete virtuální počítače pomocí **System Center VMM do Azure** scénář, postupujte podle pokynů [zakažte ochranu Hyper-V virtuálního počítače se replikuje pomocí nástroje System Center VMM do Scénář Azure](#disable-protection-for-a-hyper-v-virtual-machine-replicating-using-the-system-centet-vmm-to-azure-scenario)

1. V **chráněné položky** > **replikované položky**, klikněte pravým tlačítkem na počítač > **zakázat replikaci**.
2. V **zakázat replikaci**, můžete vybrat následující možnosti:
     - **Zakázat replikaci a odebrat (doporučeno)** – tato možnost odebere replikovanou položku z Azure Site Recovery a replikace pro počítač se zastaví. Konfigurace replikace na místním virtuálním počítači se vyčistí a zastaví se fakturace služby Site Recovery pro tento chráněný server.
    - **Odebrat** – tato možnost by měla použít pouze v případě, že zdrojové prostředí je Odstraněná nebo není přístupný (nepřipojeno). To odebere replikovanou položku z Azure Site Recovery (účtování se ukončí). Konfigurace replikace na virtuálním počítači s místními **nebudou** vyčistit. 

    > [!NOTE]
    > Pokud jste zvolili **odebrat** možnost pak spusťte následující sadu skriptů a vyčistit nastavení replikace v místním serveru technologie Hyper-V.
1. Na zdrojovém Hyper-V hostitelském serveru, chcete-li odebrat replikaci pro virtuální počítač. Nahraďte názvem vašeho virtuálního počítače SQLVM1 a spusťte skript ze správy prostředí PowerShell

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Zakažte ochranu pro virtuální počítač Hyper-V, které se replikují do Azure pomocí nástroje System Center VMM do Azure scénáře

1. V **chráněné položky** > **replikované položky**, klikněte pravým tlačítkem na počítač > **zakázat replikaci**.
2. V **zakázat replikaci**, vyberte jednu z těchto možností:

    - **Zakázat replikaci a odebrat (doporučeno)** – tato možnost odebere replikovanou položku z Azure Site Recovery a replikace pro počítač se zastaví. Konfigurace replikace na místním virtuálním počítači se vyčistí a zastaví se fakturace služby Site Recovery pro tento chráněný server.
    - **Odebrat** – tato možnost by měla použít pouze v případě, že zdrojové prostředí je Odstraněná nebo není přístupný (nepřipojeno). To odebere replikovanou položku z Azure Site Recovery (účtování se ukončí). Konfigurace replikace na virtuálním počítači s místními **nebudou** vyčistit. 

    > [!NOTE]
    > Pokud jste zvolili **odebrat** možnost, pak tyto skripty, které vyčistit nastavení replikace tun místní VMM Server.
3. Tento skript spusťte na zdrojovém serveru VMM pomocí konzoly VMM pomocí Powershellu (vyžaduje oprávnění správce). Nahraďte zástupný text **SQLVM1** s názvem virtuálního počítače.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Výše uvedené kroky zrušte nastavení replikace na serveru VMM. Pokud chcete zastavit replikaci pro virtuální počítač provozovaný na hostitelském serveru Hyper-V, spusťte tento skript. SQLVM1 nahraďte názvem vašeho virtuálního počítače a host01.contoso.com s názvem hostitelský server Hyper-V.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Zakažte ochranu virtuálního počítače Hyper-V, které se replikují do sekundární Server VMM pomocí nástroje System Center VMM do VMM scénáře

1. V **chráněné položky** > **replikované položky**, klikněte pravým tlačítkem na počítač > **zakázat replikaci**.
2. V **zakázat replikaci**, vyberte jednu z těchto možností:

    - **Zakázat replikaci a odebrat (doporučeno)** – tato možnost odebere replikovanou položku z Azure Site Recovery a replikace pro počítač se zastaví. Konfigurace replikace na místním virtuálním počítači se vyčistí a zastaví se fakturace služby Site Recovery pro tento chráněný server.
    - **Odebrat** – tato možnost by měla použít pouze v případě, že zdrojové prostředí je Odstraněná nebo není přístupný (nepřipojeno). To odebere replikovanou položku z Azure Site Recovery (účtování se ukončí). Konfigurace replikace na virtuálním počítači s místními **nebudou** vyčistit. Spusťte následující sadu skriptů vyčistit nastavení replikace místních virtuálních počítačů.
> [!NOTE]
> Pokud jste zvolili **odebrat** možnost, pak tyto skripty, které vyčistit nastavení replikace tun místní VMM Server.

3. Tento skript spusťte na zdrojovém serveru VMM pomocí konzoly VMM pomocí Powershellu (vyžaduje oprávnění správce). Nahraďte zástupný text **SQLVM1** s názvem virtuálního počítače.

         $vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Na sekundárním serveru VMM spusťte tento skript k vyčištění nastavení pro sekundární virtuální počítač:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. Na sekundárním serveru VMM aktualizujte virtuální počítač na hostitelském serveru Hyper-V, tak, aby sekundární virtuální počítač získá zjištěn v konzole VMM.
6. Výše uvedené kroky zrušte nastavení replikace na serveru VMM. Pokud chcete zastavit replikaci pro virtuální počítač, spusťte následující skript ale primárního a sekundárního virtuálního počítače. SQLVM1 nahraďte názvem vašeho virtuálního počítače.

        Remove-VMReplication –VMName “SQLVM1”




