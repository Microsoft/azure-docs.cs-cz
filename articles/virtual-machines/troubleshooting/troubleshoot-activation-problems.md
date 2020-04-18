---
title: Poradce při potížích s aktivací virtuálního počítače windows v Azure| Dokumenty společnosti Microsoft
description: Obsahuje postup řešení potíží s aktivací virtuálního počítače s Windows v Azure.
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
ms.openlocfilehash: fd38f646b8dfc58839cd2645f7fadf7332693854
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605987"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Řešení potíží s aktivací virtuálního počítače Azure s Windows

Pokud máte potíže při aktivaci virtuálního počítače Azure Windows (VM), který je vytvořen z vlastní image, můžete použít informace uvedené v tomto dokumentu k řešení problému. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Principy koncových bodů Azure KMS pro aktivaci produktů pro Windows služby Azure Virtual Machines

Azure používá různé koncové body pro aktivaci KMS (Key Management Services) v závislosti na cloudové oblasti, kde se virtuální počítač nachází. Při použití tohoto průvodce odstraňováním potíží použijte příslušný koncový bod SLUŽBY SPRÁVY KLÍČŮ, který se vztahuje na vaši oblast.

* Oblasti veřejného cloudu Azure: kms.core.windows.net:1688
* Oblasti národního cloudu Azure China 21Vianet: kms.core.chinacloudapi.cn:1688
* Národní cloudové oblasti Azure Germany: kms.core.cloudapi.de:1688
* Oblasti národního cloudu Azure US Gov: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Příznak

Při pokusu o aktivaci virtuálního počítače Azure Windows se zobrazí chybová zpráva se podobá následující ukázce:

**Chyba: 0xC004F074 Služba Software LicensingService oznámila, že počítač nelze aktivovat. Nelze kontaktovat službu Key ManagementService (KMS). Další informace naleznete v protokolu událostí aplikace.**

## <a name="cause"></a>Příčina

K problémům s aktivací virtuálního počítače Azure obecně dochází v případě, že virtuální počítač s Windows není nakonfigurovaný s použitím odpovídajícího instalačního klíče klienta KMS, nebo pokud má virtuální počítač s Windows problém s připojením ke službě Azure KMS (kms.core.windows.net, port 1688). 

## <a name="solution"></a>Řešení

>[!NOTE]
>Pokud používáte síť VPN mezi lokalitami a vynucené tunelové propojení, přečtěte [si informace o povolení aktivace služby KMS s vynuceným tunelovým propojením pomocí vlastních tras Azure.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) 
>
>Pokud používáte ExpressRoute a máte být publikována výchozí trasa, přečtěte si informace o [tom, že lze blokovat připojení k Internetu k virtuálním sítím připojeným k obvodům ExpressRoute?](https://docs.microsoft.com/azure/expressroute/expressroute-faqs).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>Krok 1 Konfigurace příslušného klíče nastavení klienta SLUŽBY SPRÁVY KLÍČŮ

Pro virtuální počítač, který je vytvořen z vlastní image, je nutné nakonfigurovat příslušný klíč nastavení klienta KMS pro virtuální počítač.

1. Spusťte **soubor slmgr.vbs /dlv** na příkazovém řádku se zvýšenými oprávněními. Zkontrolujte hodnotu Description ve výstupu a zjistěte, zda byla vytvořena z maloobchodního (maloobchodního kanálu) nebo z média licence svazku (VOLUME_KMSCLIENT):
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Pokud se ve výstupu příkazu **slmgr.vbs /dlv** zobrazí kanál RETAIL, spuštěním následujících příkazů nastavte [instalační klíč klienta KMS](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) pro používanou verzi Windows Serveru a vynuťte opakování aktivace: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Například pro Windows Server 2016 Datacenter byste spustit následující příkaz:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Krok 2 Ověření připojení mezi službou Virtuální počítač a Azure KMS

1. Stáhněte a extrahujte nástroj [PSping](https://docs.microsoft.com/sysinternals/downloads/psping) do místní složky ve virtuálním ms, která se neaktivuje. 

2. Přejděte na úvodní obrazovku, vyhledejte prostředí Windows PowerShell, klikněte pravým tlačítkem myši na Windows PowerShell a vyberte Spustit jako správce.

3. Ujistěte se, že je virtuální počítač nakonfigurovaný tak, aby používal správný server Azure KMS. Provedete to spuštěním následujícího příkazu:
  
    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    Příkaz by měl vrátit: Název počítače služby správy klíčů nastaven na kms.core.windows.net:1688 úspěšně.

4. Pomocí nástroje PsPing ověřte, že máte připojení k serveru KMS. Přejděte do složky, do které jste extrahovali stažený soubor Pstools.zip, a spusťte následující:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
   V předposledním řádku výstupu se ujistěte, že vidíte: Odesláno = 4, Přijato = 4, Lost = 0 (0% ztráta).

   Pokud Lost je větší než 0 (nula), virtuální ho disponuje připojení k serveru KMS. V takovém případě pokud virtuální počítač je ve virtuální síti a má vlastní dns server zadaný, musíte se ujistit, že server DNS je schopen vyřešit kms.core.windows.net. Nebo změňte server DNS na server, který kms.core.windows.net vyřeší.

   Všimněte si, že pokud odeberete všechny servery DNS z virtuální sítě, virtuální počítače používají interní službu DNS Azure. Tato služba může vyřešit kms.core.windows.net.
  
    Také se ujistěte, že odchozí síťový provoz na koncový bod KMS s portem 1688 není blokován bránou firewall ve virtuálním počítače.

5. Pomocí [dalšího směrování sledování sítě](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) ověřte, zda je dalším typem směrování z příslušného virtuálního počítače do cílové IP 23.102.135.246 (pro kms.core.windows.net) nebo IP adresy příslušného koncového bodu SLUŽBY SPRÁVY klíčů, který se vztahuje k vaší oblasti, **Internet**.  Pokud je výsledkem VirtualAppliance nebo VirtualNetworkGateway, je pravděpodobné, že existuje výchozí trasa.  Obraťte se na správce sítě a ve skutcích s nimi určete správný postup.  To může být [vlastní trasa,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation) pokud toto řešení je v souladu se zásadami vaší organizace.

6. Po ověření úspěšného připojení k adrese kms.core.windows.net spusťte na stejném příkazovém řádku Windows PowerShellu se zvýšenými oprávněními následující příkaz. Tento příkaz se několikrát pokusí o aktivaci.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato" ; start-sleep 5 }
    ```

    Po úspěšné aktivaci se vrátí podobné informace:
    
    **Aktivace Windows (R), ServerDatacenter vydání (12345678-1234-1234-1234-1234-12345678) ...  Produkt byl úspěšně aktivován.**

## <a name="faq"></a>Nejčastější dotazy 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Windows Server 2016 jsem vytvořil z Azure Marketplace. Musím nakonfigurovat klíč KMS pro aktivaci systému Windows Server 2016? 

 
Ne. Image na Azure Marketplace má příslušný klíč nastavení klienta SLUŽBY SPRÁVY KLÍČŮ, který je již nakonfigurovaný. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Funguje aktivace Windows stejným způsobem bez ohledu na to, jestli virtuální počítač používá Azure Hybrid Use Benefit (HUB) nebo ne? 

 
Ano. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>Co se stane, když vyprší doba aktivace systému Windows? 

 
Po uplynutí lhůty a systém Windows stále není aktivován, systém Windows Server 2008 R2 a novější verze systému Windows zobrazí další oznámení o aktivaci. Tapeta plochy zůstane černá a služba Windows Update nainstaluje pouze zabezpečení a důležité aktualizace, nikoli však volitelné aktualizace. Viz část Oznámení v dolní části stránky [Licenční podmínky.](https://technet.microsoft.com/library/ff793403.aspx)   

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Obraťte se na podporu.

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.
