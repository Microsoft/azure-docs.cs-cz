---
title: Poradce při potížích s klientem vzdálené plochy Windows Virtual Desktop – Azure
description: Jak vyřešit problémy při nastavení připojení klientů v prostředí klienta Virtuální plocha systému Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: e3a240901ffca2c126e2b61eaee0cf287cc31d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127507"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Poradce při potížích s klientem vzdálené plochy

Tento článek popisuje běžné problémy s klientem vzdálené plochy a jejich opravu.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Klient vzdálené plochy pro Windows 7 nebo Windows 10 přestane reagovat nebo jej nelze otevřít

Pomocí následujících rutin prostředí PowerShell vyčistěte neintegrované registry klientů (OOB).

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Přejděte na **%AppData%\RdClientRadc** a odstraňte veškerý obsah.

Odinstalujte a přeinstalujte klienta Vzdálené plochy pro Windows 7 a Windows 10.

## <a name="web-client-wont-open"></a>Webový klient se neotevře

Nejprve otestujte připojení k internetu otevřením jiné webové stránky ve vašem prohlížeči; například [www.bing.com](https://www.bing.com).

Pomocí **nslookup** potvrďte, že dns může vyřešit hlavní název domény:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Zkuste se připojit k jinému klientovi, jako je klient Vzdálené plochy pro Windows 7 nebo Windows 10, a zkontrolujte, jestli můžete otevřít webového klienta.

### <a name="opening-another-site-fails"></a>Otevření jiného webu se nezdaří

To je obvykle způsobeno problémy se síťovým připojením nebo výpadkem sítě. Doporučujeme kontaktovat síťovou podporu.

### <a name="nslookup-cannot-resolve-the-name"></a>Nslookup nemůže přeložit název

To je obvykle způsobeno problémy se síťovým připojením nebo výpadkem sítě. Doporučujeme kontaktovat síťovou podporu.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>Klient se nemůže připojit, ale ostatní klienti ve vaší síti se mohou připojit

Pokud váš prohlížeč začne pracovat nebo přestane pracovat, když používáte webového klienta, postupujte podle následujících pokynů k jeho řešení:

1. Restartujte prohlížeč.
2. Vymazat soubory cookie prohlížeče. Viz [Jak odstranit soubory cookie v aplikaci Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Smažte mezipaměť prohlížeče. Viz [vymazání mezipaměti prohlížeče pro váš prohlížeč](https://binged.it/2RKyfdU).
4. Otevřete prohlížeč v soukromém režimu.

## <a name="web-client-stops-responding-or-disconnects"></a>Webový klient přestane reagovat nebo se odpojí

Zkuste se připojit pomocí jiného prohlížeče nebo klienta.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Ostatní prohlížeče a klienti také poruchy nebo nepodaří otevřít

Pokud problémy přetrvávají i po přepnutí prohlížeče, problém nemusí být v prohlížeči, ale v síti. Doporučujeme kontaktovat síťovou podporu.

## <a name="web-client-keeps-prompting-for-credentials"></a>Webový klient nanese výzvu k zobrazení pověření

Pokud webový klient nastále vyzývá pověření, postupujte podle následujících pokynů:

1. Zkontrolujte, zda je adresa URL webového klienta správná.
2. Zkontrolujte, zda jsou přihlašovací údaje, které používáte, pro prostředí Virtuální plochy Windows svázané s adresou URL.
3. Vymazat soubory cookie prohlížeče. Další podrobnosti naleznete [v tématu Odstranění souborů souborů cookie v aplikaci Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Smažte mezipaměť prohlížeče. Další podrobnosti naleznete v tématu [Vymazání mezipaměti prohlížeče pro váš prohlížeč](https://binged.it/2RKyfdU).
5. Otevřete prohlížeč v soukromém režimu.

## <a name="next-steps"></a>Další kroky

- Přehled řešení potíží s virtuální plochou Windows a traseskalace najdete [v tématu Přehled řešení potíží, zpětná vazba a podpora](troubleshoot-set-up-overview.md).
- Informace o řešení problémů při vytváření fondu klientů a hostitelů v prostředí Virtuální plochy systému Windows najdete v [tématu Vytvoření klientského a hostitelského fondu](troubleshoot-set-up-issues.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače (VM) ve Windows Virtual Desktop najdete v [tématu Konfigurace virtuálního počítače hostitele relací](troubleshoot-vm-configuration.md).
- Informace o řešení problémů při používání PowerShellu s Windows Virtual Desktop najdete v [tématu Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Chcete-li projít kurz řešení potíží, [přečtěte si článek Návod k řešení potíží s nasazením šablon Správce prostředků](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).