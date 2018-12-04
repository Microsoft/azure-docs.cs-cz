---
title: Řešení potíží se selháním instalace služby Mobility nabízených oznámení při povolení replikace pro zotavení po havárii | Dokumentace Microsoftu
description: Řešení chyb při instalaci služby Mobility, při povolení replikace pro zotavení po havárii
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 11/27/2018
ms.openlocfilehash: b3e2beb0245fa790dc60cf742d6ad8938de187f4
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52832577"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Řešení potíží s nabízenou instalací služby Mobility

Instalace služby Mobility je klíče krokem při povolení replikace. Úspěch tento krok závisí výhradně na splnění požadavků a práci s podporovanou konfigurací. Většina běžných chyb, které se zabývají během instalace služby Mobility se z důvodu

* Přihlašovací údaje nebo oprávnění chyby
* Chyby připojení
* Nepodporovaný operační systémy
* Chyby při instalaci stínové kopie svazku

Při povolení replikace Azure Site Recovery se pokusí vložit instalace agenta služby mobility na virtuálním počítači. Jako součást tohoto konfiguračního serveru pokusí připojit k virtuálnímu počítači a zkopírujte agenta. Pokud chcete povolit úspěšnou instalaci, postupujte podrobné pokyny k odstraňování uvedena níže.

## <a name="credentials-check-errorid-95107--95108"></a>Zkontrolujte přihlašovací údaje (ID chyby: 95107 & 95108)

* Ověřte, jestli uživatelský účet vybrána při povolení replikace **platný, přesné**.
* Azure Site Recovery vyžaduje **oprávnění správce** k provedení nabízené instalace.
  * Pro Windows, ověření, pokud uživatelský účet nemá přístup pro správu, místní nebo doméně na zdrojovém počítači.
  * Pokud nepoužíváte účet domény, je nutné zakázat vzdálené řízení přístupu uživatele v místním počítači.
    * Chcete-li zakázat vzdálené řízení přístupu uživatele, pod klíčem registru HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System přidat novou hodnotu DWORD: LocalAccountTokenFilterPolicy. Nastavte hodnotu na 1. K provedení tohoto kroku, spusťte následující příkaz z příkazového řádku:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Pro Linux musíte zvolit účet root pro úspěšnou instalaci agenta mobility.

Pokud chcete upravit přihlašovací údaje účtu pro vybrané uživatele, postupujte podle pokynů [tady](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="connectivity-check-errorid-95117--97118"></a>**Kontrola připojení (ID chyby: 95117 & 97118)**

* Ujistěte se, že můžete provádět na příkaz ping zdrojový počítač z konfiguračního serveru. Pokud jste vybrali horizontální navýšení kapacity procesového serveru při povolení replikace, ujistěte se, že můžete provádět na příkaz ping zdrojový počítač z procesového serveru.
  * Ze zdrojového serveru počítače příkazového řádku pomocí Telnetu odešlete zprávu ping konfigurační server / horizontální navýšení kapacity procesového serveru s portem https (135), jak vidíte níže, jestli jsou všechny problémy se síťovým připojením nebo brány firewall portu blokující problémy.

     `telnet <CS/ scale-out PS IP address> <135>`
  * Zkontrolujte, že stav služby **InMage Scout VX Agent – Sentinel/Outpost**. Spusťte službu, pokud není spuštěná.
* Kromě toho **virtuálního počítače s Linuxem**,
  * Zkontrolujte, jestli jsou nainstalovaná nejnovější balíčky openssh, openssh-server a openssl.
  * Zkontrolujte a ujistěte se, že Secure Shell (SSH) zapnutá a běží na portu 22.
  * By měl běžet služba SFTP. Pokud chcete povolit ověřování SFTP subsystému a heslo v souboru sshd_config
    * Přihlaste se jako uživatel root.
    * Přejděte k souboru /etc/ssh/sshd_config, vyhledejte řádek, který začíná PasswordAuthentication.
    * Zrušte na řádku komentář a změňte hodnotu na hodnotu Ano
    * Vyhledejte řádek, který začíná subsystému a zrušte na řádku komentář
    * Restartujte službu sshd.
* Pokus o připojení může mít se nezdařilo, pokud neexistuje žádná správná odpověď po určitou dobu nebo navázané připojení se nezdařila, protože připojený hostitel se nepodařilo odpovědět.
* Připojení/sítě nebo domény může být problém související s. Může být také způsobena název DNS, které řeší problém nebo problém vyčerpání portů TCP. Prosím zkontrolujte, jestli jsou ve vaší doméně všech známých problémů.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Kontrola služby Sdílení souborů a tiskáren (ID chyby: 95105 & 95106)

Po kontrole připojení Ujistěte se, že je ve vašem virtuálním počítači povolit služby Sdílení souborů a tiskáren.

Pro **windows 2008 R2 a v předchozích verzích**,

* Povolit sdílení souborů a tiskáren přes bránu Windows Firewall
  * Otevřete ovládací panely -> systém a zabezpečení -> Brána Windows Firewall. -> v levém podokně, klikněte na tlačítko Upřesnit nastavení -> klikněte ve stromu konzoly na příchozí pravidla.
  * Vyhledejte pravidla a sdílení tiskáren (NB-Session-In) a soubor sdílení souborů a tiskáren (SMB-In). Pro každé pravidlo, klikněte pravým tlačítkem na pravidlo a pak klikněte na tlačítko **Povolit pravidlo**.
* Povolit sdílení pomocí zásad skupiny souborů
  * Přejít na začátek zadejte gpmc.msc a hledání.
  * V navigačním podokně otevřete následující složky: zásady místního počítače, konfigurace uživatele, šablony pro správu, součásti Windows a sdílení v síti.
  * V podokně podrobností klikněte dvakrát na **zabránit uživatelům ve sdílení souborů v rámci jejich profilu**. Zakázání nastavení zásad skupiny a povolení uživatele umožňují sdílet soubory, klikněte na možnost zakázáno. Klikněte na tlačítko OK uložte provedené změny. Další informace, klikněte na tlačítko [tady](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Pro **novější verze**, postupujte podle pokynů uvedených [tady](vmware-azure-install-mobility-service.md) umožňující sdílení souborů a tiskáren.

## <a name="windows-management-instrumentation-wmi-configuration-check"></a>Kontrola konfigurace Windows Management Instrumentation (WMI)

Po kontrole služby souborů a tiskáren, povolte službu WMI přes bránu firewall.

* V Ovládacích panelech klikněte na tlačítko zabezpečení a pak klikněte na možnost Brána Windows Firewall.
* Klikněte na změnit nastavení a potom klikněte na kartu výjimky.
* V okně Výjimky zaškrtněte políčko pro Windows Management Instrumentation (WMI) pro povolení provozu služby WMI přes bránu firewall. 

Můžete také povolit provoz WMI přes bránu firewall na příkazovém řádku. Pomocí následujícího příkazu `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Další články pro řešení problémů WMI nelze nalézt v následujících článcích.

* [Základní testování služby WMI](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Řešení problémů WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Poradce při potížích se skripty WMI a služby rozhraní WMI](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Nepodporovaný operační systémy

Další nejčastější příčinou selhání může být způsobeno nepodporovaný operační systém. Ujistěte se, že používáte podporovanou verzi operačního systému nebo jádra pro úspěšnou instalaci služby Mobility.

Další informace o operačních systémech, které jsou podporovány službou Azure Site Recovery, najdete v našich [dokument matice podpory](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="vss-installation-failures"></a>Selhání instalace služby VSS

Instalace služby VSS je součástí instalace agenta Mobility. Tato služba se používá při generování body obnovení konzistentní vzhledem k aplikaci. K selhání během instalace služby VSS může dojít z několika důvodů. Chcete-li zjistit přesný chyby, přečtěte si **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. V následující části jsou zvýrazněny několik běžných chyb a kroků pro řešení.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Došlo k chybě VSS-2147023170 [0x800706BE] - ukončovací kód 511

Tento problém většinou dochází, když antivirový software blokuje provoz služby Azure Site Recovery. Chcete-li vyřešit,

1. Vyloučit všechny složky, které jsou uvedené [tady](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
2. Postupujte podle pokynů publikovaných poskytovatelem antivirový program odblokujete registrace knihovny DLL ve Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Došlo k chybě VSS 7 [0x7] - ukončovací kód 511

Toto je chyba za běhu a příčinou je nedostatek paměti k instalaci aplikace VSS. Nezapomeňte zvětšete místo na disku pro úspěšné dokončení této operace.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Došlo k chybě VSS-2147023824 [0x80070430] - ukončovací kód. 517

Tato chyba nastane, pokud je služba Azure Site Recovery VSS Provider [označená k odstranění](https://msdn.microsoft.com/en-us/library/ms838153.aspx). Pokus nainstalovat VSS na zdrojovém počítači ručně spuštěním následující příkazový řádek

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Došlo k chybě VSS-2147023841 [0x8007041F] - ukončovací kód 512

Tato chyba nastane, pokud je databáze služby Azure Site Recovery VSS Provider [uzamčen](https://msdn.microsoft.com/en-us/library/ms833798.aspx). Pokus nainstalovat VSS na zdrojovém počítači ručně spuštěním následující příkazový řádek

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-exit-code-806"></a>VSS ukončovací kód 806

Tato chyba nastane, pokud uživatelský účet použitý k instalaci nemá oprávnění k provedení příkazu CSScript. Zadejte potřebná oprávnění pro uživatelský účet pro spuštění skriptu a operaci opakujte.

### <a name="other-vss-errors"></a>Další chyby VSS.

Pokus nainstalovat poskytovatele služby VSS na zdrojovém počítači ručně spuštěním následující příkazový řádek

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="next-steps"></a>Další postup

[Zjistěte, jak](vmware-azure-tutorial.md) nastavit zotavení po havárii pro virtuální počítače VMware.