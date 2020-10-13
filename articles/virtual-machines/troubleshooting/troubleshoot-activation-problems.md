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
ms.openlocfilehash: 987d4c7188c2bdc2ba6264805e33b79e7d2851d6
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91966283"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Řešení potíží s aktivací virtuálního počítače Azure s Windows

Pokud máte potíže při aktivaci virtuálního počítače Azure s Windows, který je vytvořený z vlastní image, můžete k tomuto problému využít informace uvedené v tomto dokumentu. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Principy koncových bodů Azure KMS pro aktivaci produktů pro Windows služby Azure Virtual Machines

Azure v závislosti na oblasti cloudu, ve které se nachází virtuální počítač, používá jiné koncové body pro službu správy klíčů (služby správy klíčů). Při použití tohoto průvodce odstraňováním potíží použijte příslušný koncový bod služby správy klíčů, který se vztahuje k vaší oblasti.

* Oblasti veřejného cloudu Azure: kms.core.windows.net:1688
* Azure Čína 21Vianet National cloudové oblasti: kms.core.chinacloudapi.cn:1688
* Oblasti národního cloudu Azure Německo: kms.core.cloudapi.de:1688
* Azure US Gov oblasti národního cloudu: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Příznak

Když se pokusíte aktivovat virtuální počítač Azure s Windows, zobrazí se chybová zpráva podobná následující ukázce:

**Chyba: 0xC004F074 software LicensingService ohlásil, že počítač nešlo aktivovat. Nebylo možné kontaktovat službu Key ManagementService (KMS). Další informace najdete v protokolu událostí aplikace.**

## <a name="cause"></a>Příčina

K problémům s aktivací virtuálního počítače Azure obecně dochází v případě, že virtuální počítač s Windows není nakonfigurovaný s použitím odpovídajícího instalačního klíče klienta KMS, nebo pokud má virtuální počítač s Windows problém s připojením ke službě Azure KMS (kms.core.windows.net, port 1688). 

## <a name="solution"></a>Řešení

>[!NOTE]
>Pokud používáte síť VPN typu Site-to-site a vynucené tunelové propojení, přečtěte si téma [použití vlastních tras Azure pro povolení aktivace prostřednictvím služby správy klíčů s vynuceným tunelovým propojením](../../vpn-gateway/vpn-gateway-about-forced-tunneling.md). 
>
>Pokud používáte ExpressRoute a máte publikovanou výchozí trasu, přečtěte si téma [Jak mohu blokovat připojení k Internetu k virtuálním sítím připojeným k okruhům ExpressRoute?](../../expressroute/expressroute-faqs.md).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>Krok 1 nakonfigurujte příslušný instalační klíč klienta služby správy klíčů.

Pro virtuální počítač vytvořený z vlastní image musíte nakonfigurovat příslušný instalační klíč klienta služby správy klíčů pro virtuální počítač.

1. Spusťte **slmgr.vbs/DLV** na příkazovém řádku se zvýšenými oprávněními. Zkontrolujte hodnotu Popis ve výstupu a pak určete, jestli se vytvořila z maloobchodního média (MALOOBCHODNÍho kanálu) nebo z multilicenčního programu (VOLUME_KMSCLIENT).
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Pokud se ve výstupu příkazu **slmgr.vbs /dlv** zobrazí kanál RETAIL, spuštěním následujících příkazů nastavte [instalační klíč klienta KMS](/windows-server/get-started/kmsclientkeys) pro používanou verzi Windows Serveru a vynuťte opakování aktivace: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Například pro Windows Server 2016 Datacenter spustíte následující příkaz:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Krok 2 Ověřte připojení mezi virtuálním počítačem a službou Azure KMS.

1. Stáhněte a extrahujte nástroj [PSping](/sysinternals/downloads/psping) do místní složky na virtuálním počítači, který se neaktivuje. 

2. Přejděte na Start, vyhledejte Windows PowerShell, klikněte pravým tlačítkem na Windows PowerShell a vyberte Spustit jako správce.

3. Ujistěte se, že je virtuální počítač nakonfigurovaný tak, aby používal správný server Azure KMS. Provedete to spuštěním následujícího příkazu:
  
    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    Příkaz by měl vracet: název počítače služby správy klíčů nastavený tak, aby se kms.core.windows.net:1688 úspěšně.

4. Pomocí nástroje PsPing ověřte, že máte připojení k serveru KMS. Přejděte do složky, do které jste extrahovali stažený soubor Pstools.zip, a spusťte následující:
  
    ```
    .\psping.exe kms.core.windows.net:1688
    ```
   V druhém řádku výstupu se ujistěte, že vidíte: odesláno = 4, přijato = 4, ztraceno = 0 (0% ztráta).

   Pokud je ztráta větší než 0 (nula), virtuální počítač nemá připojení k serveru služby správy klíčů. V takové situaci, pokud je virtuální počítač ve virtuální síti a má zadaný vlastní server DNS, musíte zajistit, aby server DNS mohl přeložit kms.core.windows.net. Případně můžete změnit server DNS na takový, který překládá kms.core.windows.net.

   Všimněte si, že když odeberete všechny servery DNS z virtuální sítě, virtuální počítače budou používat interní službu DNS Azure. Tato služba dokáže vyřešit kms.core.windows.net.
  
    Také se ujistěte, že brána firewall ve virtuálním počítači neblokuje odchozí síťový provoz do koncového bodu služby správy klíčů s 1688 portem.

5. Ověřte použití [Network Watcher dalšího směrování](../../network-watcher/network-watcher-next-hop-overview.md) , že typ dalšího segmentu směrování z daného virtuálního počítače na cílovou IP adresu 23.102.135.246 (pro KMS.Core.Windows.NET) nebo IP adresu příslušného koncového bodu služby správy klíčů, který se vztahuje k vaší oblasti, je **Internet**.  Pokud je výsledek VirtualAppliance nebo VirtualNetworkGateway, je pravděpodobné, že existuje výchozí trasa.  Obraťte se na správce sítě a pracujte s nimi a určete správný postup.  Může se jednat o [vlastní trasu](./custom-routes-enable-kms-activation.md) , pokud je toto řešení v souladu se zásadami vaší organizace.

6. Po ověření úspěšného připojení k adrese kms.core.windows.net spusťte na stejném příkazovém řádku Windows PowerShellu se zvýšenými oprávněními následující příkaz. Tento příkaz se několikrát pokusí o aktivaci.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato" ; start-sleep 5 }
    ```

    Po úspěšné aktivaci se vrátí podobné informace:
    
    **Aktivace systému Windows (R), edice ServerDatacenter (12345678-1234-1234-1234-12345678)...  Produkt byl úspěšně aktivován.**

## <a name="faq"></a>Nejčastější dotazy 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Vytvořil (a) jsem Windows Server 2016 z Azure Marketplace. Potřebuji nakonfigurovat klíč služby správy klíčů pro aktivaci Windows serveru 2016? 

 
Ne. Bitová kopie ve Azure Marketplace má již nakonfigurovaný příslušný instalační klíč klienta služby správy klíčů. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Funguje aktivace Windows stejným způsobem bez ohledu na to, jestli virtuální počítač používá zvýhodněné hybridní využití Azure (centrum), nebo ne? 

 
Yes. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>Co se stane, když vyprší doba aktivace Windows? 

 
Po vypršení období odkladu a Windows se stále neaktivuje Windows Server 2008 R2 a novější verze Windows zobrazí další oznámení o aktivaci. Tapeta plochy zůstane černá a web Windows Update bude instalovat pouze zabezpečení a kritické aktualizace, ale ne volitelné aktualizace. Podívejte se na část oznámení v dolní části stránky [licenční podmínky](/previous-versions/tn-archive/ff793403(v=technet.10)) .   

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Obraťte se na podporu.

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.