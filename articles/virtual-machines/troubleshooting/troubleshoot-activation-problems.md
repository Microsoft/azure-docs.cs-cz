---
title: Řešení potíží s aktivací virtuálních počítačů s Windows v Azure | Microsoft Docs
description: Popisuje kroky řešení potíží při opravě problémů aktivace virtuálních počítačů s Windows v Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/15/2018
ms.author: genli
ms.openlocfilehash: f3ad58c4094e9f39bcf9782b7b98e351e9d7809b
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058132"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Řešení potíží s aktivací virtuálních počítačů Azure s Windows

Pokud máte potíže při aktivaci virtuálního počítače Azure s Windows, který je vytvořený z vlastní image, můžete k tomuto problému využít informace uvedené v tomto dokumentu. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Principy koncových bodů služby Azure KMS pro aktivaci produktů Windows v Azure Virtual Machines

Azure používá pro aktivaci prostřednictvím služby správy různých koncových bodů v závislosti na oblasti cloudu, ve které se virtuální počítač nachází. Při použití tohoto průvodce odstraňováním potíží použijte příslušný koncový bod služby správy klíčů, který se vztahuje k vaší oblasti.

* Oblasti veřejného cloudu Azure: kms.core.windows.net:1688
* Azure Čína 21Vianet National cloudové oblasti: kms.core.chinacloudapi.cn:1688
* Oblasti národního cloudu Azure Německo: kms.core.cloudapi.de:1688
* Azure US Gov oblasti národního cloudu: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Příznak

Když se pokusíte aktivovat virtuální počítač Azure s Windows, zobrazí se chybová zpráva podobná následující ukázce:

**Chyba: 0xC004F074 software LicensingService ohlásil, že počítač nešlo aktivovat. Nebylo možné kontaktovat službu Key ManagementService (KMS). Další informace najdete v protokolu událostí aplikace.**

## <a name="cause"></a>Příčina

Obecně dochází k problémům s aktivací virtuálních počítačů Azure, pokud virtuální počítač s Windows není nakonfigurovaný pomocí příslušného instalačního klíče klienta služby správy klíčů, nebo virtuální počítač s Windows má problém s připojením ke službě Azure KMS (kms.core.windows.net, port 1688). 

## <a name="solution"></a>Řešení

>[!NOTE]
>Pokud používáte síť VPN typu Site-to-site a vynucené tunelové propojení, přečtěte si téma [použití vlastních tras Azure pro povolení aktivace prostřednictvím služby správy klíčů s vynuceným tunelovým propojením](https://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx). 
>
>Pokud používáte ExpressRoute a máte publikovanou výchozí trasu, přečtěte si článek o tom, že [virtuální počítač Azure nebude moct aktivovat ExpressRoute](https://blogs.msdn.com/b/mast/archive/2015/12/01/azure-vm-may-fail-to-activate-over-expressroute.aspx).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>Krok 1 nakonfigurujte příslušný instalační klíč klienta služby správy klíčů.

Pro virtuální počítač vytvořený z vlastní image musíte nakonfigurovat příslušný instalační klíč klienta služby správy klíčů pro virtuální počítač.

1. Spusťte příkaz **slmgr. vbs/dlv** na příkazovém řádku se zvýšenými oprávněními. Zkontrolujte hodnotu Popis ve výstupu a pak určete, jestli se vytvořila z média pro maloobchodní (maloobchodní kanál) nebo z multilicenčního programu (VOLUME_KMSCLIENT).
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Pokud se ve výstupu příkazu **slmgr.vbs /dlv** zobrazí kanál RETAIL, spuštěním následujících příkazů nastavte [instalační klíč klienta KMS](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) pro používanou verzi Windows Serveru a vynuťte opakování aktivace: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Například pro Windows Server 2016 Datacenter spustíte následující příkaz:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Krok 2 Ověřte připojení mezi virtuálním počítačem a službou Azure KMS.

1. Stáhněte a extrahujte nástroj [PSping](http:/technet.microsoft.com/sysinternals/jj729731.aspx) do místní složky na virtuálním počítači, který se neaktivuje. 

2. Přejděte na Start, vyhledejte Windows PowerShell, klikněte pravým tlačítkem na Windows PowerShell a vyberte Spustit jako správce.

3. Ujistěte se, že je virtuální počítač nakonfigurovaný tak, aby používal správný server Azure KMS. Uděláte to tak, že spustíte následující příkaz:
  
    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    Výstupem příkazu by mělo být: Název počítače služby správy klíčů nastavený na kms.core.windows.net:1688 byl úspěšně nastaven.

4. Pomocí Psping ověřte, že máte připojení k serveru služby správy klíčů. Přejděte do složky, do které jste extrahovali stažený soubor Pstools.zip, a spusťte následující:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
   Ujistěte se, že se na předposledním řádku výstupu zobrazí následující: Odesláno = 4, přijato = 4, ztraceno = 0 (0% ztráta).

   Pokud je ztráta větší než 0 (nula), virtuální počítač nemá připojení k serveru služby správy klíčů. V takové situaci, pokud je virtuální počítač ve virtuální síti a má zadaný vlastní server DNS, musíte zajistit, aby server DNS mohl přeložit kms.core.windows.net. Případně můžete změnit server DNS na takový, který překládá kms.core.windows.net.

   Všimněte si, že když odeberete všechny servery DNS z virtuální sítě, virtuální počítače budou používat interní službu DNS Azure. Tato služba dokáže vyřešit kms.core.windows.net.
  
    Také se ujistěte, že brána firewall ve virtuálním počítači neblokuje odchozí síťový provoz do koncového bodu služby správy klíčů s 1688 portem.

5. Po ověření úspěšného připojení k kms.core.windows.net spusťte následující příkaz na příkazovém řádku Windows PowerShellu se zvýšenými oprávněními. Tento příkaz se několikrát pokusí o aktivaci.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato" ; start-sleep 5 }
    ```

    Po úspěšné aktivaci se vrátí podobné informace:
    
    **Aktivace systému Windows (R), edice ServerDatacenter (12345678-1234-1234-1234-12345678)...  Produkt byl úspěšně aktivován.**

## <a name="faq"></a>Nejčastější dotazy 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Vytvořil (a) jsem Windows Server 2016 z Azure Marketplace. Potřebuji nakonfigurovat klíč služby správy klíčů pro aktivaci Windows serveru 2016? 

 
Ne. Bitová kopie ve Azure Marketplace má již nakonfigurovaný příslušný instalační klíč klienta služby správy klíčů. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Funguje aktivace Windows stejným způsobem bez ohledu na to, jestli virtuální počítač používá zvýhodněné hybridní využití Azure (centrum), nebo ne? 

 
Ano. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>Co se stane, když vyprší doba aktivace Windows? 

 
Po vypršení období odkladu a Windows se stále neaktivuje Windows Server 2008 R2 a novější verze Windows zobrazí další oznámení o aktivaci. Tapeta plochy zůstane černá a web Windows Update bude instalovat pouze zabezpečení a kritické aktualizace, ale ne volitelné aktualizace. Podívejte se na část oznámení v dolní části stránky [licenční podmínky](https://technet.microsoft.com/library/ff793403.aspx) .   

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktujte podporu.

Pokud stále potřebujete pomoc, obraťte se na [podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , abyste mohli rychle vyřešit problém.
