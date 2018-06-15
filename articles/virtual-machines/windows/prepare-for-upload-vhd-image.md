---
title: Příprava virtuálního pevného disku Windows nahrát do Azure | Microsoft Docs
description: Postup přípravy Windows VHD nebo VHDX před nahráním do Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 038a9be813367d130dd8bb02b24879d1e2e573b5
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072245"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Příprava systému Windows VHD nebo VHDX, který chcete nahrát do Azure
Před nahráním Windows virtuální počítače (VM) z místní do služby Microsoft Azure, musíte připravit virtuální pevný disk (VHD nebo VHDX). Azure podporuje jenom virtuální počítače generace 1, které jsou ve formátu souboru virtuálního pevného disku a mají pevnou velikostí disku. Maximální velikost povolenou pro virtuální pevný disk je 1,023 GB. Můžete převést generace 1 virtuální počítač z je disk VHDX souborů systému na virtuální pevný disk a z dynamicky se zvětšující disk do pevnou velikostí. Ale generaci Virtuálního počítače nelze změnit. Další informace najdete v tématu [měli vytvořit generace 1 nebo 2 virtuálních počítačů Hyper-v](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

Další informace o zásadách podpory pro virtuální počítač Azure, najdete v části [podporu serveru softwaru společnosti Microsoft pro virtuální počítače Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!Note]
> Pokyny v tomto článku platí pro 64bitovou verzi Windows Server 2008 R2 a novější operační systém Windows server. Informace o spuštěných 32bitovou verzi operačního systému v Azure najdete v tématu [podpora pro 32bitové operační systémy ve virtuálních počítačích Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>Převést virtuální disk na virtuální pevný disk a disk pevné velikosti 
Pokud potřebujete převést virtuální disk na požadovaný formát pro Azure, použijte jednu z metod v této části. Zálohování virtuálního počítače před spuštěním procesu převodu virtuálního disku a ujistěte se, že virtuální pevný disk systému Windows správně funguje na místním serveru. Vyřešte všechny chyby v rámci virtuální počítač, než se pokusíte převést nebo ho nahrát do Azure.

Převedete-li disk, vytvořte virtuální počítač, který používá převedený disk. Spuštění a přihlášení do virtuálního počítače na dokončení Příprava virtuálního počítače pro odesílání.

### <a name="convert-disk-using-hyper-v-manager"></a>Převedení disku pomocí Správce technologie Hyper-V
1. Otevřete Správce technologie Hyper-V a vyberte místního počítače na levé straně. V nabídce nahoře seznamu počítačů, klikněte na tlačítko **akce** > **upravit Disk**.
2. Na **najít virtuální pevný Disk** obrazovky, vyhledejte a vyberte virtuální disk.
3. Na **zvolit akci** obrazovky a pak vyberte **převést** a **Další**.
4. Pokud musíte převést z VHDX, vyberte **virtuálního pevného disku** a pak klikněte na tlačítko **další**
5. Pokud musíte převést z dynamicky se zvětšující disku, vyberte **pevnou velikost** a pak klikněte na tlačítko **další**
6. Vyhledejte a vyberte cestu pro uložení nového souboru virtuálního pevného disku.
7. Klikněte na **Dokončit**.

>[!NOTE]
>Příkazy v tomto článku se musí spustit v relaci prostředí PowerShell zvýšenými oprávněními.

### <a name="convert-disk-by-using-powershell"></a>Převést disk pomocí prostředí PowerShell
Můžete převést virtuální disk pomocí [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) příkazu v prostředí Windows PowerShell. Vyberte **spustit jako správce** při spuštění prostředí PowerShell. 

Následující ukázkový příkaz převede z VHDX na virtuální pevný disk a z dynamicky se zvětšující disk do pevné velikosti:

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
V tomto příkazu nahraďte hodnotu pro "-cestu" cestou k virtuální pevný disk, který chcete převést a hodnota "-DestinationPath" s novou cestu a název převedený disku.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Převod z formátu VMware VMDK disku
Pokud máte bitovou kopii virtuálního počítače s Windows [formát souboru VMDK](https://en.wikipedia.org/wiki/VMDK), ho převést na virtuální pevný disk pomocí [převaděč virtuálních počítačů Microsoft](https://www.microsoft.com/download/details.aspx?id=42497). Další informace najdete v článku na blogu [jak převést VMware VMDK na VHD Hyper-V](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Nastavení konfigurace systému Windows Azure

Na virtuálním počítači, který chcete nahrát do Azure, spusťte v následujících krocích z všechny příkazy [okno příkazového řádku se zvýšenými oprávněními](https://technet.microsoft.com/library/cc947813.aspx):

1. Odeberte všechny statické trasy trvalé ve směrovací tabulce:
   
   * Chcete-li zobrazit směrovací tabulka, spusťte `route print` na příkazovém řádku.
   * Zkontrolujte **trvalost trasy** oddíly. Pokud je trvalé trasy, použijte [trasy odstranění](https://technet.microsoft.com/library/cc739598.apx) jeho odebrání.
2. Odebrání proxy serveru WinHTTP:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```
3. Nastavte zásady sítě SAN disku na [Onlineall](https://technet.microsoft.com/library/gg252636.aspx). 
   
    ```PowerShell
    diskpart 
    ```
    V otevřené okno příkazového řádku zadejte následující příkazy:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Nastavit čas koordinovaný světový čas (UTC) pro systém Windows a typ spouštění služby Systémový čas (w32time) **automaticky**:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" 1 -Type DWord

    Set-Service -Name w32time -StartupType Auto
    ```
5. Nastavte profil spotřeby na **vysoký výkon**:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```

## <a name="check-the-windows-services"></a>Zkontrolujte služby systému Windows
Ujistěte se, že každý z následujících služeb systému Windows je nastavena na **Windows výchozí hodnoty**. Toto jsou minimální čísla služeb, které musí nastavit tak, abyste měli jistotu, že virtuální počítač má připojení. Chcete-li obnovit nastavení při spuštění, spusťte následující příkazy:
   
```PowerShell
Set-Service -Name bfe -StartupType Auto
Set-Service -Name dhcp -StartupType Auto
Set-Service -Name dnscache -StartupType Auto
Set-Service -Name IKEEXT -StartupType Auto
Set-Service -Name iphlpsvc -StartupType Auto
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Auto
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Auto
Set-Service -Name RemoteRegistry -StartupType Auto
```

## <a name="update-remote-desktop-registry-settings"></a>Aktualizace nastavení registru vzdálené plochy
Zkontrolujte, že následující nastavení jsou správně nakonfigurovány pro připojení ke vzdálené ploše:

>[!Note] 
>Při spuštění, může se zobrazit chybová zpráva **Set-ItemProperty-cesta ' HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal služby - název &lt;název objektu&gt; &lt;hodnotu&gt;**  v těchto krocích. Chybová zpráva můžete bezpečně ignorovat. Znamená jen, že doména není vkládání tato konfigurace prostřednictvím objektu zásad skupiny.
>
>

1. Je povolen protokol RDP (Remote Desktop):
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord
    ```
   
2. Správné nastavení portu RDP (výchozí port 3389):
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" 3389 -Type DWord
    ```
    Při nasazení virtuálního počítače, výchozí pravidla vytvářejí proti portu 3389. Pokud chcete změnit číslo portu, udělat po nasazení virtuálního počítače v Azure.

3. V každé síťové rozhraní naslouchá naslouchací proces:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" 0 -Type DWord
   ```
4. Konfigurace režimu ověřování na úrovni sítě pro připojení RDP:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" 1 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" 1 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" 1 -Type DWord
     ```

5. Udržování hodnotu:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" 1 -Type DWord
    ```
6. Připojení:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" 0 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" 0 -Type DWord
    ```
7. Omezte počet souběžných připojení:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" 4294967295 -Type DWord
    ```
8. Pokud jsou všechny certifikáty podepsané svým držitelem svázané s naslouchací proces protokolu RDP, odeberte je:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash"
    ```
    Toto je zajistit, že se můžete připojit na začátku při nasazení virtuálního počítače. Můžete také zkontrolovat to v pozdější fázi po nasazení virtuálního počítače v Azure v případě potřeby.

9. Pokud virtuální počítač bude součástí domény, zkontrolujte všechny následující nastavení a ujistěte se, že předchozí nastavení se nevrátí. Zásady, které se musí kontrolovat jsou následující:
    
    - Je povolen protokol RDP:

         Počítač Konfigurace uživatele\Zásady\Nastavení systému Settings\Administrative Templates\ Windows\Vzdálená plocha\Hostitel relace vzdálené plochy\Připojení:
         
         **Povolit uživatelům vzdálené připojení pomocí vzdálené plochy**

    - NLA zásad skupiny:

        Settings\Administrative Templates\Components\Remote plocha\Hostitel relace plochy plochy\Zabezpečení: 
        
        **Uživatel ověřování vyžadovat pro vzdálená připojení pomocí ověření úrovně sítě**
    
    - Ponechat Alive nastavení:

        Počítač Konfigurace uživatele\Zásady\Nastavení systému Settings\Administrative pro správu\Součásti systému Windows\Vzdálená plocha\Hostitel relace vzdálené plochy\Připojení: 
        
        **Nakonfigurujte interval udržování připojení**

    - Znovu připojte nastavení:

        Počítač Konfigurace uživatele\Zásady\Nastavení systému Settings\Administrative pro správu\Součásti systému Windows\Vzdálená plocha\Hostitel relace vzdálené plochy\Připojení: 
        
        **Automatické obnovení připojení**

    - Omezení počtu připojení nastavení:

        Počítač Konfigurace uživatele\Zásady\Nastavení systému Settings\Administrative pro správu\Součásti systému Windows\Vzdálená plocha\Hostitel relace vzdálené plochy\Připojení: 
        
        **Omezení počtu připojení**

## <a name="configure-windows-firewall-rules"></a>Konfigurace pravidel brány Windows Firewall
1. Zapněte bránu Windows Firewall na tři profily (domény, Standard a veřejný):

   ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 1 -Type DWord
   ```

2. V prostředí PowerShell povolit WinRM pomocí tří profilů brány firewall (Domain, Private a Public) a povolte službu vzdáleného prostředí PowerShell, spusťte následující příkaz:
   
   ```PowerShell
    Enable-PSRemoting -force
    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   ```
3. Povolte následující pravidla brány firewall umožňuje provoz protokolu RDP 

   ```PowerShell
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
   ```   
4. Povolení sdílení souborů a tiskáren pravidla, aby virtuální počítač mohli odpovídat na příkaz ping uvnitř virtuální sítě:

   ```PowerShell
    netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes
   ``` 
5. Pokud virtuální počítač bude součástí domény, zkontrolujte následující nastavení a ujistěte se, že předchozí nastavení se nevrátí. AD zásady, které se musí kontrolovat jsou následující:

    - Povolení profilů brány Windows Firewall

        Počítač Konfigurace uživatele\Zásady\Nastavení systému Settings\Administrative správu\Síť\Síťová Connection\Windows Firewall\Domain Profile\Windows brány Firewall: **chránit všechna síťová připojení**

       Počítač Konfigurace uživatele\Zásady\Nastavení systému Settings\Administrative správu\Síť\Síťová Connection\Windows Firewall\Standard Profile\Windows brány Firewall: **chránit všechna síťová připojení**

    - Povolení protokolu RDP 

        Počítač Konfigurace uživatele\Zásady\Nastavení systému Settings\Administrative správu\Síť\Síťová Connection\Windows Firewall\Domain Profile\Windows brány Firewall: **povolit příchozí výjimky vzdálené plochy**

        Počítač Konfigurace uživatele\Zásady\Nastavení systému Settings\Administrative správu\Síť\Síťová Connection\Windows Firewall\Standard Profile\Windows brány Firewall: **povolit příchozí výjimky vzdálené plochy**

    - Povolit protokol ICMP V4

        Počítač Konfigurace uživatele\Zásady\Nastavení systému Settings\Administrative správu\Síť\Síťová Connection\Windows Firewall\Domain Profile\Windows brány Firewall: **povolit výjimky protokolu ICMP**

        Počítač Konfigurace uživatele\Zásady\Nastavení systému Settings\Administrative správu\Síť\Síťová Connection\Windows Firewall\Standard Profile\Windows brány Firewall: **povolit výjimky protokolu ICMP**

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Ověřte, zda že virtuální počítač je v pořádku, zabezpečenou a přístupné pomocí protokolu RDP 
1. Pokud chcete mít jistotu, že disk je v pořádku a konzistentní, spusťte disková operace kontroly v dalším restartování virtuálního počítače:

    ```PowerShell
    Chkdsk /f
    ```
    Ujistěte se, že tato sestava zobrazuje v pořádku a vyčištění disku.

2. Nastavení konfiguračních dat spouštění (BCD). 

    > [!Note]
    > Zajistěte, aby spuštění těchto příkazů v okně CMD se zvýšenými oprávněními a **není** na prostředí PowerShell:
   
   ```CMD
   bcdedit /set {bootmgr} integrityservices enable
   
   bcdedit /set {default} device partition=C:
   
   bcdedit /set {default} integrityservices enable
   
   bcdedit /set {default} recoveryenabled Off
   
   bcdedit /set {default} osdevice partition=C:
   
   bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
   ```
3. Ověřte, že úložiště Windows Management Instrumentations konzistentní. Chcete-li to provést, spusťte následující příkaz:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Pokud úložiště je poškozený, přečtěte si téma [rozhraní WMI: poškození úložiště, nebo Ne](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

4. Ujistěte se, že není jiná aplikace pomocí portu 3389. Tento port se používá pro službu protokolu RDP v Azure. Můžete spustit **netstat - anob** zobrazíte v používaných portů ve virtuálním počítači:

    ```PowerShell
    netstat -anob
    ```

5. Pokud virtuální pevný disk systému Windows, který chcete nahrát je řadič domény, postupujte podle těchto kroků:

    A. Postupujte podle [tyto další kroky](https://support.microsoft.com/kb/2904015) Příprava disku.

    B. Zajistěte, aby v případě, že budete muset spustit virtuální počítač v režimu obnovení adresářových služeb v určitém okamžiku znát heslo režimu obnovení adresářových služeb. Můžete chtít odkazovat na tento odkaz nastavit [heslo režimu obnovení adresářových služeb](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

6. Ujistěte se, že jsou pro vás známé předdefinovaného účtu Administrator a heslo. Můžete obnovit aktuální heslo místního správce a ujistěte se, že můžete tento účet pro přihlášení k systému Windows přes připojení RDP. Objekt zásad skupiny "Povolit přihlášení prostřednictvím vzdálené plochy" řídí toto oprávnění přístupu. Tento objekt můžete zobrazit v editoru zásad skupiny místní v části:

    Počítač Konfigurace počítače\Nastavení systému Windows\Nastavení Settings\Local přiřazení uživatelských práv

7. Zkontrolujte následující AD zásady a ujistěte se, že nejsou blokuje přístup RDP prostřednictvím protokolu RDP ani ze sítě:

    - Konfigurace počítače\Nastavení systému Windows\Nastavení Settings\Local Policies\User Rights Assignment\Deny přístup počítačů k tomuto počítači ze sítě.

    - Počítač Konfigurace počítače\Nastavení systému Windows\Nastavení Settings\Local Policies\User Rights Assignment\Deny přihlášení prostřednictvím vzdálené plochy


8. Restartování virtuálního počítače a ujistěte se, že je stále v pořádku Windows jsou dostupné přes připojení RDP. V tomto okamžiku můžete vytvořit virtuální počítač s vaší místní Hyper-v a ujistěte se, že virtuální počítač se spouští úplně pak otestovat, zda je dostupný protokolu RDP.

9. Odeberte všechny filtry navíc rozhraní TDI, jako je například software, která analyzuje TCP paketů nebo další brány firewall. Můžete také zkontrolovat to v pozdější fázi po nasazení virtuálního počítače v Azure v případě potřeby.

10. Odinstalujte všechny ostatní software třetích stran a ovladač, který se vztahuje k fyzické komponenty nebo jakoukoli jinou technologii virtualizace.

### <a name="install-windows-updates"></a>Instalace aktualizací systému Windows
Ideální konfigurace **mít počítač na nejnovější úroveň opravy**. Pokud to není možné, ujistěte se, zda jsou nainstalovány následující aktualizace:

|                       |                   |           |                                       Minimální verze x64       |                                      |                                      |                            |
|-------------------------|-------------------|------------------------------------|---------------------------------------------|--------------------------------------|--------------------------------------|----------------------------|
| Komponenta               | Binární hodnota            | Windows 7 & Windows Server 2008 R2 | Windows 8 & Windows Server 2012             | Windows 8.1 a Windows Server 2012 R2 | Windows 10 & Windows Server 2016 RS1 | Windows 10 RS2             |
| Úložiště                 | disk.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061           | -                                    | -                          |
|                         | storport.sys      | 6.1.7601.23403 - KB3125574         | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.332             |
|                         | ntfs.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726           | 10.0.14393.1198 - KB4022715          | 10.0.15063.447             |
|                         | Iologmsg.dll      | 6.1.7601.23403 - KB3125574         | 6.2.9200.16384 - KB2995387                  | -                                    | -                                    | -                          |
|                         | Classpnp.sys      | 6.1.7601.23403 - KB3125574         | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614           | 10.0.14393.953 - KB4022715           | -                          |
|                         | Volsnap.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384           | -                                    | 10.0.15063.0               |
|                         | partmgr.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850           | 10.0.14393.953 - KB4022715           | 10.0.15063.0               |
|                         | volmgr.sys        |                                    |                                             |                                      |                                      | 10.0.15063.0               |
|                         | Volmgrx.sys       | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | 10.0.15063.0               |
|                         | Msiscsi.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726           | 10.0.14393.1066 - KB4022715          | 10.0.15063.447             |
|                         | Msdsm.sys         | 6.1.7601.23403 - KB3125574         | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726           | -                                    | -                          |
|                         | Mpio.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726           | 10.0.14393.1198 - KB4022715          | -                          |
|                         | Fveapi.dll        | 6.1.7601.23311 - KB3125574         | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614           | 10.0.14393.576 - KB4022715           | -                          |
|                         | Fveapibase.dll    | 6.1.7601.23403 - KB3125574         | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614           | 10.0.14393.206 - KB4022715           | -                          |
| Síť                 | netvsc.sys        | -                                  | -                                           | -                                    | 10.0.14393.1198 - KB4022715          | 10.0.15063.250 - KB4020001 |
|                         | mrxsmb10.sys      | 6.1.7601.23816 - KB4022722         | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726           | 10.0.14393.479 - KB4022715           | 10.0.15063.483             |
|                         | mrxsmb20.sys      | 6.1.7601.23816 - KB4022722         | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726           | 10.0.14393.953 - KB4022715           | 10.0.15063.483             |
|                         | mrxsmb.sys        | 6.1.7601.23816 - KB4022722         | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726           | 10.0.14393.953 - KB4022715           | 10.0.15063.0               |
|                         | tcpip.sys         | 6.1.7601.23761 - KB4022722         | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.447             |
|                         | http.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726           | 10.0.14393.251 - KB4022715           | 10.0.15063.483             |
|                         | vmswitch.sys      | 6.1.7601.23727 - KB4022719         | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.138             |
| Jádro                    | ntoskrnl.exe      | 6.1.7601.23807 - KB4022719         | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.483             |
| Vzdálená plocha | rdpcorets.dll     | 6.2.9200.21506 - KB4022719         | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726           | 10.0.14393.1198 - KB4022715          | 10.0.15063.0               |
|                         | termsrv.dll       | 6.1.7601.23403 - KB3125574         | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850           | 10.0.14393.0 - KB4022715             | 10.0.15063.0               |
|                         | termdd.sys        | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
|                         | win32k.sys        | 6.1.7601.23807 - KB4022719         | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726           | 10.0.14393.594 - KB4022715           | -                          |
|                         | rdpdd.dll         | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
|                         | rdpwd.sys         | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
| Zabezpečení                | Z důvodu WannaCrypt | KB4012212                          | KB4012213                                   | KB4012213                            | KB4012606                            | KB4012606                  |
|                         |                   |                                    | KB4012216                                   |                                      | KB4013198                            | KB4013198                  |
|                         |                   | KB4012215                          | KB4012214                                   | KB4012216                            | KB4013429                            | KB4013429                  |
|                         |                   |                                    | KB4012217                                   |                                      | KB4013429                            | KB4013429                  |
       
### Kdy použít nástroj sysprep <a id="step23"></a>    

Nástroj Sysprep je proces, který můžete spustit do instalace systému windows, který resetuje instalace systému a budou tak poskytovat "ihned prostředí" odebráním všechna osobní data a resetování několik komponent. Obvykle to uděláte Pokud chcete vytvořit šablonu, ze kterého můžete nasadit několik ostatních virtuálních počítačů, které mají určitou konfiguraci. Tento postup se nazývá **zobecněn image**.

Pokud místo toho chcete jenom pro účely vytvoření jeden virtuální počítač z jednoho disku, nemáte pomocí nástroje sysprep. V takovém případě můžete pouze vytvořit virtuální počítač z, která se označuje jako **specializované image**.

Další informace o tom, jak vytvořit virtuální počítač z specializované disku najdete v tématu:

- [Vytvoření virtuálního počítače z specializované disku](create-vm-specialized.md)
- [Vytvoření virtuálního počítače z specializované disku VHD](https://azure.microsoft.com/resources/templates/201-vm-specialized-vhd/)

Pokud chcete vytvořit bitovou kopii zobecněný, budete muset spustit nástroj sysprep. Další informace o nástroji Sysprep najdete v tématu [postup použití nástroje Sysprep: Úvod](http://technet.microsoft.com/library/bb457073.aspx). 

Ne každý role nebo aplikace, který je nainstalován na počítači se systémem Windows podporuje tento generalizace. Proto před spuštěním tohoto postupu, naleznete v následujícím článku, abyste měli jistotu, že role tento počítač podporuje nástroj sysprep. Další informace najdete [podpora nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="steps-to-generalize-a-vhd"></a>Postup generalize virtuální pevný disk

>[!NOTE]
> Po spuštění sysprep.exe uvedeného v následujících krocích vypněte virtuální počítač a není znovu zapnout až do vytvoření bitové kopie z něj v Azure.

1. Přihlaste se k systému Windows virtuálního počítače.
2. Spustit **příkazového řádku** jako správce. 
3. Změňte adresář na: **%windir%\system32\sysprep**a poté spusťte **sysprep.exe**.
3. V **nástroj pro přípravu systému** dialogové okno, vyberte **prostředí Out-of-Box zadejte systému (při prvním zapnutí)** a ujistěte se, že **generalizace** je zaškrtnuté políčko.

    ![Nástroj pro přípravu systému](media/prepare-for-upload-vhd-image/syspre.png)
4. V **možnosti vypnutí**, vyberte **vypnutí**.
5. Klikněte na **OK**.
6. Po dokončení nástroj Sysprep, vypněte virtuální počítač. Nepoužívejte **restartujte** vypnutí virtuálního počítače.
7. Virtuální pevný disk je nyní připraven k odeslání. Další informace o tom, jak vytvořit virtuální počítač z zobecněný disku najdete v tématu [nahrát zobecněný virtuální pevný disk a použít ho k vytvoření nové virtuální počítače v Azure](sa-upload-generalized.md).


## <a name="complete-recommended-configurations"></a>Dokončete doporučené konfigurace
Následující nastavení neovlivní odesílání virtuálního pevného disku. Ale důrazně doporučujeme nakonfigurovat je.

* Nainstalujte [virtuální počítače Azure agenta](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Potom můžete povolit rozšíření virtuálního počítače. Rozšíření virtuálního počítače implementovat většinu důležitých funkcí, které může používat s virtuální počítače jako je resetování hesla, konfigurace RDP a tak dále. Další informace naleznete v tématu:

    - [Agent virtuálního počítače a rozšíření – část 1](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-1/)
    - [Agent virtuálního počítače a rozšíření – část 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)
* Výpis protokolu může být užitečné při řešení problémů s havárií systému Windows. Povolení protokolu kolekce výpisu stavu:
  
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "CrashDumpEnable" -Value "2" -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "DumpFile" -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "AutoReboot" -Value 0 -Type DWord
    New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpFolder" -Value "c:\CrashDumps"
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpCount" -Value 10 -Type DWord
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpType" -Value 2 -Type DWord
    Set-Service -Name WerSvc -StartupType Manual
    ```
    Pokud se zobrazí všechny chyby během procedurální kroků v tomto článku, znamená to, že klíče registru již existuje. V takovém případě použijte následující příkazy:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "CrashDumpEnable" -Value "2" -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "DumpFile" -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpFolder" -Value "c:\CrashDumps"
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpCount" -Value 10 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpType" -Value 2 -Type DWord
    Set-Service -Name WerSvc -StartupType Manual
    ```
*  Po vytvoření virtuálního počítače v Azure doporučujeme umístit stránkovací soubor na svazek "Dočasné jednotka" ke zlepšení výkonu. Můžete nastavit to následujícím způsobem:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile"
    ```
Pokud je datový disk, který je připojen k virtuálnímu počítači, písmeno jednotky svazku dočasné jednotky je obvykle "D." Toto označení se může lišit v závislosti na počtu dostupných jednotek a nastavení, které provedete.

## <a name="next-steps"></a>Další postup
* [Nahrajte image virtuálního počítače Windows Azure pro nasazení Resource Manager](upload-generalized-managed.md)
* [Řešení problémů aktivace virtuálního počítače Azure Windows](troubleshoot-activation-problems.md)

