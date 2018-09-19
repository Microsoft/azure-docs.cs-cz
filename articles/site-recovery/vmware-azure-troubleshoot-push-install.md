---
title: Azure Site Recovery řešení potíží z VMware do Azure | Dokumentace Microsoftu
description: Řešení chyb při replikaci virtuálních počítačů Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.author: ramamill
ms.date: 09/17/2018
ms.openlocfilehash: d77b252351c15bea13b0fa1fb42fa062d508fbdc
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126985"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Řešení potíží s nabízenou instalací služby Mobility

Instalace služby Mobility je klíče krokem při povolení replikace. Úspěch tento krok závisí výhradně na splnění požadavků a práci s podporovanou konfigurací. Většina běžných chyb, které se zabývají během instalace služby Mobility se z důvodu

* Chyby připojení a přihlašovacích údajů
* Nepodporovaný operační systémy

Při povolení replikace Azure Site Recovery se pokusí vložit instalace agenta služby mobility na virtuálním počítači. Jako součást tohoto konfiguračního serveru pokusí připojit k virtuálnímu počítači a zkopírujte agenta. Pokud chcete povolit úspěšnou instalaci, postupujte podrobné pokyny k odstraňování uvedena níže

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
  * Ze zdrojového serveru počítače příkazového řádku pomocí Telnetu odešlete zprávu ping konfigurační server / horizontální navýšení kapacity procesový server s port https (standardně 9443), jak vidíte níže, jestli jsou všechny problémy se síťovým připojením nebo brány firewall portu blokující problémy.

     `telnet <CS/ scale-out PS IP address> <port>`

  * Pokud se nemůžete připojit, povolte příchozí port 9443 na konfiguračním serveru / horizontální navýšení kapacity procesového serveru.
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
  * V podokně podrobností klikněte dvakrát na **zabránit uživatelům ve sdílení souborů v rámci jejich profilu**. Zakázání nastavení zásad skupiny a povolení uživatele umožňují sdílet soubory, klikněte na možnost zakázáno. Klikněte na tlačítko OK uložte provedené změny. Další informace, klikněte na tlačítko [tady](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Pro **novější verze**, postupujte podle pokynů uvedených [tady](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) umožňující sdílení souborů a tiskáren

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

## <a name="next-steps"></a>Další postup

[Zjistěte, jak](vmware-azure-tutorial.md) nastavit zotavení po havárii pro virtuální počítače VMware.