---
title: Poradce při potížích aktivace virtuálního počítače Windows v Azure | Dokumentace Microsoftu
description: Obsahuje postup řešení potíží pro řešení problémů aktivace virtuálního počítače Windows v Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/15/2018
ms.author: genli
ms.openlocfilehash: 16876a7831ab374637e28165c44d47e0ab059712
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976356"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Poradce při potížích aktivace virtuálního počítače Windows Azure

Pokud máte potíže při aktivaci Windows Azure virtuální počítač (VM), který je vytvořen z vlastní image, můžete k odstranění tohoto problému informací uvedených v tomto dokumentu. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Principy koncových bodů Azure prostřednictvím služby správy KLÍČŮ pro aktivaci produktu Windows Azure Virtual Machines
Azure používá různé koncové body pro aktivaci prostřednictvím služby správy KLÍČŮ v závislosti na oblasti cloudu, ve které se nachází virtuální počítač. Při použití tohoto průvodce odstraňováním potíží, použijte vhodný koncový bod služby správy KLÍČŮ, které platí pro vaši oblast.

* Veřejných cloudových oblastech Azure: kms.core.windows.net:1688
* Oblasti národních cloudů Azure China 21Vianet: kms.core.chinacloudapi.cn:1688
* Oblasti národních cloudů Azure Germany: kms.core.cloudapi.de:1688
* Azure národních cloudů oblastech US Gov: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Příznak

Při pokusu o aktivaci virtuálního počítače Windows Azure, se zobrazí chybová zpráva vypadá přibližně takto:

**Chyba: 0xC004F074, které LicensingService softwaru oznámila, že počítač se nepovedlo aktivovat. Nebylo možné kontaktovat žádný ManagementService klíčů (KMS). Podrobnosti najdete v protokolu událostí aplikací Další informace.**

## <a name="cause"></a>Příčina
Obecně platí problémů s aktivací virtuálního počítače Azure dojít, pokud virtuální počítač Windows není nakonfigurovaný pomocí příslušný instalační klíč klienta služby správy KLÍČŮ nebo má virtuální počítač Windows problému s připojením ke službě Azure prostřednictvím služby správy KLÍČŮ (kms.core.windows.net, port 1688). 

## <a name="solution"></a>Řešení

>[!NOTE]
>Pokud používáte VPN typu site-to-site a vynucené tunelování, naleznete v tématu [Azure použijte vlastní trasy umožňující aktivace služby správy KLÍČŮ se vynucené tunelování](https://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx). 
>
>Pokud používáte ExpressRoute a vy musíte výchozí trasa publikování naleznete v tématu [virtuálního počítače Azure se pravděpodobně nezdaří aktivovat přes ExpressRoute](https://blogs.msdn.com/b/mast/archive/2015/12/01/azure-vm-may-fail-to-activate-over-expressroute.aspx).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key-for-windows-server-2016-and-windows-server-2012-r2"></a>Krok 1 konfigurace příslušný instalační klíč klienta služby správy KLÍČŮ (pro Windows Server 2016 a Windows Server 2012 R2)

Pro virtuální počítač, který je vytvořen z vlastní image Windows serveru 2016 nebo Windows Server 2012 R2 je nutné nakonfigurovat příslušný instalační klíč klienta služby správy KLÍČŮ pro tento virtuální počítač.

Tento krok se nevztahují na Windows 2012 nebo Windows 2008 R2. Používá funkci automatizace aktivace virtuálního počítače (AVMA), který je podporován pouze ve Windows serveru 2016 a Windows Server 2012 R2.

1. Spustit **slmgr.vbs/dlv** příkazového řádku se zvýšenými oprávněními. Zkontrolujte popis hodnotu ve výstupu a určit, zda byl vytvořen z maloobchodního prodeje (RETAIL channel) nebo svazek (VOLUME_KMSCLIENT) licence média:
  
    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Pokud **slmgr.vbs/dlv** zobrazí prodejní kanál, spusťte následující příkazy pro nastavení [Instalační klíč klienta služby správy KLÍČŮ](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) se používají pro verzi systému Windows Server a vynutit u něj k aktivaci opakujte: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Například by pro Windows Server 2016 Datacenter, spusťte následující příkaz:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Krok 2 ověření připojení mezi služby virtuálního počítače a služby správy KLÍČŮ Azure

1. Stažení a extrakci [PSping](http:/technet.microsoft.com/sysinternals/jj729731.aspx) nástroj do místní složky ve virtuálním počítači, který neaktivuje. 

2. Přejít na Start, Hledat v prostředí Windows PowerShell, klikněte pravým tlačítkem na prostředí Windows PowerShell a vybrat příkaz Spustit jako správce.

3. Ujistěte se, že virtuální počítač je nakonfigurován na použití správný server služby správy KLÍČŮ Azure. Chcete-li to provést, spusťte následující příkaz:
  
    ```
    iex "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```
    Příkaz by měl vrátit: Název počítače služby správy klíčů na kms.core.windows.net:1688 byl úspěšně nastaven.

4. Ověřte pomocí Pspingu, že máte připojení k serveru služby správy KLÍČŮ. Přejděte do složky, které jste extrahovali Pstools.zip stahování a pak spusťte následující příkaz:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
  
  V druhé poslední řádek výstupu Ujistěte se, že se zobrazí: Odeslání = 4, přijaté = 4, bylo ztraceno = 0 (ztráty 0 %).

  Pokud bylo ztraceno je větší než 0 (nula), virtuální počítač nemá připojení k serveru služby správy KLÍČŮ. V takovém případě pokud je virtuální počítač ve virtuální síti a má vlastní server DNS zadán, je nutné tento server DNS je schopen převést kms.core.windows.net. Nebo změnit DNS server, který kms.core.windows.net vyřešit.

  Všimněte si, že pokud odeberete všechny servery DNS z virtuální sítě, virtuální počítače používají interní služba DNS Azure. Tato služba dokáže přeložit kms.core.windows.net.
  
Dál ověřte, že není nakonfigurovaná brána firewall hosta způsobem, který se bude blokovat pokusy o aktivaci.

5. Po ověření úspěšného připojení k kms.core.windows.net, spusťte následující příkaz v tomto řádku se zvýšenými oprávněními prostředí Windows PowerShell. Tento příkaz se pokusí aktivace více než jednou.

    ```
    1..12 | % { iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato” ; start-sleep 5 }
    ```

Úspěšné aktivaci vrátí informace, které vypadá přibližně takto:

**Aktivace Windows(R) ServerDatacenter edition (12345678-1234-1234-1234-12345678)... Produkt se úspěšně aktivoval.**

## <a name="faq"></a>Nejčastější dotazy 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Jsem vytvořil Windows serveru 2016 z webu Azure Marketplace. Je potřeba nakonfigurovat klíč služby správy KLÍČŮ pro aktivaci Windows serveru 2016? 
 
Ne. Image na webu Azure Marketplace má příslušné služby správy KLÍČŮ klientský Instalační klíč už nakonfigurovaná. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Aktivace Windows funguje stejným způsobem bez ohledu na to pokud virtuální počítač používá Azure hybridní použití výhody (HUB) nebo ne? 
 
Ano. 
 
### <a name="what-happens-if-windows-activation-period-expires"></a>Co se stane, když vyprší platnost období aktivace Windows? 
 
Po období odkladu vypršelo a ještě není aktivováno Windows, Windows Server 2008 R2 a novějších verzích Windows se zobrazí další oznámení o aktivaci služby Azure. Zůstane černé tapetu plochy a Windows Update budou instalovat zabezpečení a pouze kritické aktualizace, ale ne volitelné aktualizace. V části upozornění v dolní části [licenční podmínky](https://technet.microsoft.com/library/ff793403.aspx) stránky.   

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktujte podporu.
Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.
