---
title: Řešení problémů aktivace systému Windows virtuálního počítače v Azure | Microsoft Docs
description: Popisuje kroky poradce při potížích pro řešení potíží s aktivace systému Windows virtuálního počítače v Azure
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
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 3926522c8359587798ea540d13b157832551f24d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Řešení problémů aktivace virtuálního počítače Azure Windows

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Pokud máte potíže při aktivaci Azure Windows virtuální počítač (VM), který je vytvořený z vlastní image, můžete k odstranění potíží informací uvedených v tomto dokumentu. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Principy koncové body Azure služby správy KLÍČŮ pro aktivaci produktu virtuálních počítačů Azure
Azure používá různými koncovými body pro aktivaci služby správy KLÍČŮ v závislosti na cloudu oblasti, kde je umístěn virtuální počítač. Při použití této Průvodci odstraňováním potíží, používejte příslušný koncový bod služby správy KLÍČŮ, který se vztahují na vaši oblast.

* Oblasti Azure veřejného cloudu: kms.core.windows.net:1688
* Oblastí Azure Číně národních cloudů: kms.core.chinacloudapi.cn:1688
* Německo oblasti národních cloudů: kms.core.cloudapi.de:1688
* Oblastí Azure nám verze pro státní správu národních cloudů: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Příznaky

Když se pokusíte aktivovat virtuálního počítače Windows Azure, se zobrazí chybová zpráva vypadá přibližně následující ukázka:

**Chyba: 0xC004F074, které LicensingService softwaru oznámila, že se počítač nepodařilo aktivovat. Klíč ManagementService (KMS) není možné kontaktovat. Najdete v protokolu událostí aplikací Další informace.**

## <a name="cause"></a>Příčina
Obecně platí virtuální počítač Azure problémů s aktivací dojít, pokud virtuální počítač Windows není nakonfigurovaný pomocí příslušný instalační klíč klienta služby správy KLÍČŮ nebo virtuální počítač Windows má potíže s připojením ke službě Azure služby správy KLÍČŮ (kms.core.windows.net, port 1668). 

## <a name="solution"></a>Řešení

>[!NOTE]
>Pokud používáte síť site-to-site VPN a vynuceného tunelování, najdete v části [Azure použijte vlastní trasy umožňující služby správy KLÍČŮ aktivaci pomocí vynuceného tunelování](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx). 
>
>Pokud používáte ExpressRoute a budete mít výchozí trasa publikování naleznete v tématu [virtuálního počítače Azure se pravděpodobně nepodaří aktivovat přes ExpressRoute](https://blogs.technet.microsoft.com/jpaztech/2016/05/16/azure-vm-may-fail-to-activate-over-expressroute/).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key-for-windows-server-2016-and-windows-server-2012-r2"></a>Krok 1 konfigurace příslušný instalační klíč klienta služby správy KLÍČŮ (pro Windows Server 2016 a Windows Server 2012 R2)

Pro virtuální počítač, který je vytvořený z vlastní image systému Windows Server 2016 nebo Windows Server 2012 R2 je nutné nakonfigurovat příslušný instalační klíč klienta služby správy KLÍČŮ pro virtuální počítač.

Tento krok se nevztahuje na Windows 2012 nebo Windows 2008 R2. Používá funkci automatizace aktivace virtuálního počítače (AVMA), která je podporován pouze Windows Server 2016 nebo Windows Server 2012 R2.

1. Spustit **slmgr.vbs/dlv** na příkazovém řádku se zvýšenými oprávněními. Zkontrolujte hodnotu popis ve výstupu a pak stanovit, zda byl vytvořen z prodejní (prodejní kanál) nebo médium licenci svazek (VOLUME_KMSCLIENT):
  
    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Pokud **slmgr.vbs/dlv** zobrazí prodejní kanál, spusťte následující příkazy nastavit [Instalační klíč klienta služby správy KLÍČŮ](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) pro verzi systému Windows Server používá a vynutit, aby aktivaci opakujte: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Například pro Windows Server 2016 Datacenter, spustili byste následující příkaz:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Krok 2 Ověřte připojení mezi služby virtuálního počítače a služby správy KLÍČŮ Azure

1. Stažení a extrakci [Pspingu](http:/technet.microsoft.com/sysinternals/jj729731.aspx) nástroj do místní složky ve virtuálním počítači, který nemusí aktivovat. 

2. Přejděte na spuštění, vyhledávání v prostředí Windows PowerShell, klikněte pravým tlačítkem na prostředí Windows PowerShell a potom vyberte spustit jako správce.

3. Ujistěte se, že virtuální počítač je nakonfigurován pro použití správné serveru Azure služby správy KLÍČŮ. Chcete-li to provést, spusťte následující příkaz:
  
    ```
    iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms
    kms.core.windows.net:1688
    ```
    Příkaz by měla vrátit: název služby správy klíčů počítače byl úspěšně nastaven na kms.core.windows.net:1688.

4. Ověřte pomocí Pspingu, že jste připojeni k serveru služby správy KLÍČŮ. Přejděte do složky, které jste extrahovali Pstools.zip stahování a pak spusťte následující:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
  
  Ujistěte se, že se zobrazí v sekundu poslední řádek výstupu,: odeslané = 4, přijaté = 4, Ztraceno = 0 (0 % ztráty).

  Pokud ztráty je větší než 0 (nula), virtuální počítač nemá připojení k serveru služby správy KLÍČŮ. V této situaci pokud je virtuální počítač ve virtuální síti a má vlastní server DNS, ujistěte se, že server DNS je schopný přeložit kms.core.windows.net. Nebo zvolit, které řešení kms.core.windows.net serverem DNS.

  Všimněte si, že pokud odeberete všechny servery DNS z virtuální sítě, virtuální počítače používat interní DNS služby Azure. Tato služba může vyřešit kms.core.windows.net.
  
Také ověřte, že brána firewall hosta nebyl nakonfigurován způsobem, který by blokovat pokusy o aktivaci.

5. Po ověření úspěšné připojení k kms.core.windows.net, spusťte na tomto řádku prostředí Windows PowerShell se zvýšenými oprávněními následující příkaz. Tento příkaz se pokusí aktivace vícekrát.

    ```
    1..12 | % { iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato” ; start-sleep 5 }
    ```

Úspěšné aktivaci vrátí informace, které vypadá přibližně takto:

**Aktivace Windows(R) ServerDatacenter edition (12345678-1234-1234-1234-12345678)... Produkt úspěšně aktivoval.**

## <a name="faq"></a>Nejčastější dotazy 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Windows Server 2016 vytvořený z Azure Marketplace. Je potřeba nakonfigurovat klíč služby správy KLÍČŮ pro aktivaci Windows Server 2016? 
 
Ne. Bitovou kopii v Azure Marketplace obsahuje odpovídající klíč klienta instalace služby správy KLÍČŮ, kde je již nakonfigurována. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Aktivace systému Windows funguje stejným způsobem jako bez ohledu na to pokud virtuální počítač používá Azure hybridní použití zvýhodnění (HUB) nebo ne? 
 
Ano. 
 
### <a name="what-happens-if-windows-activation-period-expires"></a>Co se stane, když vyprší platnost období aktivace systému Windows? 
 
Když poskytnutá lhůta vyprší a systému Windows není ještě aktivována, se zobrazí další oznámení o aktivaci služby Windows Server 2008 R2 a novějších verzích Windows. Tapeta plochy zůstane černé a Windows Update bude instalovat, zabezpečení a jenom důležité aktualizace, ale není nepovinný aktualizace. Najdete v části oznámení v dolní části [licenční podmínky](http://technet.microsoft.com/library/ff793403.aspx) stránky.   

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.
Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.
