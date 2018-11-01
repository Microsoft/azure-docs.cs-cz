---
title: Konkrétních chybových zpráv protokolu RDP pro virtuální počítače Azure | Dokumentace Microsoftu
description: Vysvětlení, že konkrétní chybové zprávy, které se zobrazí při pokusu o použití připojení ke vzdálené ploše pro virtuální počítače s Windows v Azure
keywords: Chyba vzdálené plochy, Chyba připojení ke vzdálené ploše, se nemůže připojit k virtuálnímu počítači, řešení potíží vzdálené plochy
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 5feb1d64-ee6f-4907-949a-a7cffcbc6153
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: f4d733e29d2ba8213e1832f2c604b726283ab3e1
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50417391"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Řešení problémů konkrétních chybových zpráv protokolu RDP k virtuálnímu počítači s Windows v Azure
Při použití připojení ke vzdálené ploše na Windows virtuální počítač (VM) v Azure, může se zobrazit zpráva konkrétní chyba. Tento článek podrobně popisuje některé z běžnějších chybové zprávy došlo k spolu se řešení potíží s kroky k jejich řešení. Pokud máte problémy s připojením k virtuálnímu počítači pomocí protokolu RDP není ale proveďte dojde k určité chybové zprávě, najdete v článku [Průvodce odstraňováním potíží pro vzdálenou plochu](troubleshoot-rdp-connection.md).

Informace o určité chybové zprávy naleznete v následujících tématech:

* [Vzdálená relace byla odpojena, protože nejsou žádné vzdálené plochy licenční servery mohly poskytnout licenci k dispozici](#rdplicense).
* [Vzdálená plocha nenašla počítač "name"](#rdpname).
* [Došlo k chybě ověřování. Místní úřad zabezpečení nelze kontaktovat](#rdpauth).
* [Chyba zabezpečení Windows: vaše přihlašovací údaje nefungovala](#wincred).
* [Počítač se nemůže připojit ke vzdálenému počítači](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>Vzdálená relace byla odpojena, protože nejsou žádné vzdálené plochy licenční servery mohly poskytnout licenci k dispozici.
Příčina: 120denní licencování lhůty pro roli serveru vzdálené plochy vypršela a je potřeba nainstalovat licence.

Alternativním řešením je uložit místní kopii souboru RDP z portálu a spusťte tento příkaz na příkazovém řádku prostředí PowerShell pro připojení. Tento krok zakazuje licencování pro právě toto připojení:

        mstsc <File name>.RDP /admin

Pokud doopravdy nepotřebujete více než dvě souběžná připojení vzdálené plochy k virtuálnímu počítači, můžete použít Správce serveru odebrat roli serveru vzdálené plochy.

Další informace naleznete v příspěvku blogu [selhání na virtuálním počítači Azure s "Vzdálené plochy licence k dispozici žádné servery"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Vzdálená plocha nenašla počítač "name".
Příčina: Klient vzdálené plochy ve vašem počítači nelze vyřešit název počítače, v nastavení souboru protokolu RDP.

Možná řešení:

* Pokud jste v síti intranet v organizaci, ujistěte se, že počítač má přístup k proxy serveru a mohou odesílat provoz HTTPS na ni.
* Pokud používáte místně uloženého souboru RDP, zkuste použít jeden, který je generován na portálu. Tento krok zajistí, že máte správný název DNS pro virtuální počítač nebo cloudovou službu a portem koncového bodu virtuálního počítače. Tady je ukázkový soubor protokolu RDP generované portálem:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

Obsahuje části adresy tohoto souboru protokolu RDP:

* Plně kvalifikovaný název cloudové služby, která obsahuje virtuální počítač ("tailspin-azdatatier.cloudapp.net" v tomto příkladu).
* Externí port TCP pro koncový bod pro provoz vzdálené plochy (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Došlo k chybě ověřování. Místní úřad zabezpečení nelze kontaktovat.
Příčina: Cílový virtuální počítač nelze najít oprávnění zabezpečení v části název své přihlašovací údaje uživatele.

Při své uživatelské jméno ve tvaru *SecurityAuthority*\\*uživatelské jméno* (Příklad: CORP\User1), *SecurityAuthority* část je buď Virtuálního počítače název počítače (pro místní autority zabezpečení) nebo název domény služby Active Directory.

Možná řešení:

* Pokud je účet místního k virtuálnímu počítači, ujistěte se, že název virtuálního počítače je napsán správně.
* Pokud je účet v doméně služby Active Directory, zkontrolujte, zda název domény.
* Pokud je účet domény služby Active Directory a je název domény napsaný správně, ověřte, že řadič domény je k dispozici v této doméně. To je běžný problém ve virtuálních sítích Azure, které obsahují řadiče domény se, že řadič domény není k dispozici, protože nebylo spuštěno. Jako alternativní řešení můžete místo účtu domény místního účtu správce.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Chyba zabezpečení Windows: vaše přihlašovací údaje nebyla úspěšná.
Příčina: Cílový virtuální počítač nemůže ověřit název účtu a heslo.

Počítače se systémem Windows můžete ověřit přihlašovací údaje místní účet nebo účet domény.

* Pro místní účty, použijte *ComputerName*\\*uživatelské jméno* syntaxe (Příklad: SQL1\Admin4798).
* Účty domény, použijte *DomainName*\\*uživatelské jméno* syntaxe (Příklad: CONTOSO\peterodman).

Pokud jste zvýšili virtuálního počítače, aby řadič domény v nové doménové struktury služby Active Directory, použitý k přihlášení pomocí účtu místního správce je převedena na ekvivalentní účtu pomocí stejného hesla v nové doménové struktury a domény. Pak odstraní místní účet.

Například pokud jste přihlášení pomocí účtu místního DC1\DCAdmin a pak povýšen virtuální počítač jako řadič domény v nové doménové struktury v doméně corp.contoso.com, DC1\DCAdmin místní účet, se odstraní a je nový účet domény (CORP\DCAdmin) vytvořené pomocí stejné heslo.

Ujistěte se, že název účtu je název virtuálního počítače můžete ověřit jako platný účet a zda je zadáno správné heslo.

Pokud potřebujete změnit heslo účtu místního správce, přečtěte si téma [jak obnovit heslo nebo službu Vzdálená plocha pro virtuální počítače s Windows](reset-rdp.md).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Tento počítač se nemůže připojit ke vzdálenému počítači.
Příčina: Účet, který se používá k připojení nemá vzdálené plochy přihlašovací práva.

Každý počítač Windows má skupinu místní uživatelé vzdálené plochy, která obsahuje účty a skupiny, které se můžete přihlásit do něj vzdáleně. Členové místní skupiny administrators také mají přístup, i když tyto účty nejsou uvedené v místní skupině uživatelů vzdálené plochy. Pro počítače připojené k doméně místní skupiny administrators obsahuje také správci domény pro doménu.

Ujistěte se, zda má účet, který používáte pro připojení k vzdálené ploše přihlašovací práva. Jako alternativní řešení použijte účet domény nebo účet místního správce k připojení přes vzdálenou plochu. Požadovaný účet přidat do místní skupiny uživatelů vzdálené plochy, použijte modul snap-in konzoly Microsoft Management Console (**systémové nástroje > Místní uživatelé a skupiny > skupiny > Remote Desktop Users**).

## <a name="next-steps"></a>Další postup
Pokud žádná z těchto chyb došlo k chybě a mají neznámou potíže s připojením pomocí protokolu RDP najdete v tématu [Průvodce odstraňováním potíží pro vzdálenou plochu](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Řešení potíží s kroky k používání aplikace běžící na virtuálním počítači, naleznete v tématu [řešení potíží s přístupem k aplikaci spuštěné na Virtuálním počítači Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Pokud máte problémy s použitím Secure Shell (SSH) k připojení k virtuálnímu počítači s Linuxem v Azure, najdete v článku [řešení potíží s připojení SSH k virtuálnímu počítači s Linuxem v Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

