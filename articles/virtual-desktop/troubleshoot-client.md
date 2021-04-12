---
title: Řešení potíží s klientem vzdálené plochy na virtuálním počítači s Windows – Azure
description: Řešení problémů při nastavování připojení klienta v prostředí klienta virtuální plochy Windows
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: e31ff0bfdb0ead13c2636ea2f4d175c9ced34581
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445427"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Řešení potíží s klientem vzdálené plochy

Tento článek popisuje běžné problémy s klientem vzdálené plochy a jejich řešení.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Klient služby Vzdálená plocha pro Windows 7 nebo Windows 10 přestane reagovat nebo ho nejde otevřít.

Počínaje verzí 1.2.790 můžete obnovit data uživatelů ze stránky o o aplikaci nebo pomocí příkazu.

Pomocí následujícího příkazu odeberte uživatelská data, obnovte výchozí nastavení a zrušte odběr ze všech pracovních prostorů.

```cmd
msrdcw.exe /reset [/f]
```

Pokud používáte starší verzi klienta vzdálené plochy, doporučujeme odinstalovat a znovu nainstalovat klienta.

## <a name="web-client-wont-open"></a>Webový klient se neotevře.

Nejdřív otestujte připojení k Internetu otevřením jiného webu v prohlížeči. například [www.Bing.com](https://www.bing.com).

Pomocí nástroje **nslookup** potvrďte, že DNS může přeložit plně kvalifikovaný název domény:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Zkuste se připojit pomocí jiného klienta, jako je klient vzdálené plochy pro Windows 7 nebo Windows 10, a zkontrolujte, jestli můžete otevřít webového klienta.

### <a name="cant-open-other-websites-while-connected-to-the-web-client"></a>Během připojení k webovému klientovi nejde otevřít další weby.

Pokud nemůžete otevřít další weby, když jste připojeni ke webovému klientovi, může dojít k potížím se síťovým připojením nebo k výpadku sítě. Doporučujeme, abyste kontaktovali podporu sítě.

### <a name="nslookup-cant-resolve-the-name"></a>Nástroj Nslookup nemůže přeložit název.

Pokud nástroj nslookup nemůže název přeložit, může dojít k potížím se síťovým připojením nebo k výpadku sítě. Doporučujeme, abyste kontaktovali podporu sítě.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>Váš klient se nemůže připojit, ale ostatní klienti ve vaší síti se můžou připojit.

Pokud se v prohlížeči spustí nebo přestane pracovat, když používáte webového klienta, vyřešte potíže podle těchto pokynů:

1. Restartujte prohlížeč.
2. Vymazat soubory cookie prohlížeče. Informace najdete [v tématu Odstranění souborů cookie v aplikaci Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Smažte mezipaměť prohlížeče. Viz [Vymazat mezipaměť prohlížeče pro prohlížeč](https://binged.it/2RKyfdU).
4. Otevřete prohlížeč v privátním režimu.

## <a name="client-doesnt-show-my-resources"></a>Klient nezobrazuje moje prostředky

Nejdřív se podívejte na účet Azure Active Directory, který používáte. Pokud jste se už přihlásili pomocí jiného účtu Azure Active Directory, než který chcete použít pro virtuální plochu Windows, měli byste se buď odhlásit, nebo použít soukromé okno prohlížeče.

Pokud používáte virtuální plochu Windows (Classic), připojte se k prostředkům pomocí odkazu webový klient v [tomto článku](./virtual-desktop-fall-2019/connect-web-2019.md) .

Pokud to nepomůže, ujistěte se, že je vaše skupina aplikací přidružená k pracovnímu prostoru.

## <a name="web-client-stops-responding-or-disconnects"></a>Webový klient přestane reagovat nebo odpojení.

Zkuste se připojit pomocí jiného prohlížeče nebo klienta.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Jiné prohlížeče a klienti také nefunguje správně nebo se nedaří otevřít

Pokud potíže potrvají i po přepnutí prohlížečů, problém se nemusí nacházet v prohlížeči, ale ve vaší síti. Doporučujeme, abyste kontaktovali podporu sítě.

## <a name="web-client-keeps-prompting-for-credentials"></a>Webový klient uchovává výzvy k zadání přihlašovacích údajů

Pokud webový klient zachovává výzvy k zadání přihlašovacích údajů, postupujte podle těchto pokynů:

1. Potvrďte správnost adresy URL webového klienta.
2. Ověřte, že přihlašovací údaje, které používáte, jsou pro prostředí virtuálních počítačů s Windows svázané s adresou URL.
3. Vymazat soubory cookie prohlížeče. Další informace najdete v tématu [odstranění souborů cookie v aplikaci Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Smažte mezipaměť prohlížeče. Další informace najdete v tématu [vymazání mezipaměti prohlížeče pro prohlížeč](https://binged.it/2RKyfdU).
5. Otevřete prohlížeč v privátním režimu.

## <a name="windows-client-blocks-windows-virtual-desktop-classic-feed"></a>Klient Windows blokuje informační kanál virtuální plochy systému Windows (Classic)

Pokud informační kanál klienta Windows nebude zobrazovat aplikace virtuální plochy Windows (Classic), postupujte podle těchto pokynů:

1. Ověřte, jestli zásady podmíněného přístupu zahrnují ID aplikací přidružené k virtuální ploše Windows (Classic).
2. Ověřte, jestli zásady podmíněného přístupu blokují veškerý přístup s výjimkou ID aplikací pro virtuální počítače s Windows (Classic). Pokud ano, budete muset přidat ID aplikace **9cdead84-a844-4324-93f2-b2e6bb768d07** k zásadě, aby klient mohl vyhledat informační kanály.

Pokud v seznamu nemůžete najít ID aplikace 9cdead84-a844-4324-93f2-b2e6bb768d07, bude nutné zaregistrovat poskytovatele prostředků virtuálního počítače s Windows. Registrace poskytovatele prostředků:

1. Přihlaste se k webu Azure Portal.
2. Klikněte na **předplatné** a pak vyberte své předplatné.
3. V nabídce na levé straně stránky vyberte **poskytovatel prostředků**.
4. Vyhledejte a vyberte **Microsoft. DesktopVirtualization** a pak vyberte **znovu registrovat**.

## <a name="next-steps"></a>Další kroky

- Přehled řešení potíží s virtuálním počítačem s Windows a cvičeními eskalace najdete v tématu [věnovaném řešení potíží s přehledem, zpětnou vazbou a podporou](troubleshoot-set-up-overview.md).
- Informace o řešení potíží při vytváření prostředí virtuálních počítačů s Windows a fondu hostitelů v prostředí virtuálních ploch Windows najdete v tématu [Vytvoření fondu prostředí a hostitele](troubleshoot-set-up-issues.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače na virtuálním počítači s Windows najdete v tématu [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration.md).
- Informace o řešení potíží souvisejících s agentem virtuálního počítače s Windows nebo s připojením relace najdete v tématu [řešení běžných potíží s agentem virtuálních počítačů s Windows](troubleshoot-agent.md).
- Pokud chcete řešit problémy při používání PowerShellu s virtuálním počítačem s Windows, přečtěte si téma [virtuální plocha Windows PowerShell](troubleshoot-powershell.md).
- Kurz řešení potíží najdete v tématu [kurz: řešení potíží s nasazením správce prostředků šablon](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
