---
title: Specifické chybové zprávy protokolu RDP pro virtuální počítače Azure | Microsoft Docs
description: Srozumitelná specifické chybové zprávy, které se mohou zobrazit při pokusu o použití připojení ke vzdálené ploše do virtuálního počítače s Windows v Azure
keywords: Vzdálené plochy chyba, Chyba připojení ke vzdálené ploše, nelze se připojit k virtuální počítač, řešení potíží vzdálené plochy
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
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 8c027db917216d273b5fb36f8920d6fa2fb4c9a9
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072146"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Řešení potíží s konkrétní chybové zprávy protokolu RDP pro virtuální počítač s Windows v Azure
Při použití připojení ke vzdálené ploše na Windows virtuální počítač (VM) v Azure, může se zobrazit konkrétní chybová zpráva. Tento článek podrobně popisuje některé z nejběžnějších chybových zpráv došlo, společně s řešení potíží s kroky k jejich řešení. Pokud máte problémy s připojením k virtuálnímu počítači pomocí protokolu RDP ale proveďte není stane konkrétní chybová zpráva, přečtěte si [Průvodce řešením potíží pro vzdálené plochy](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Informace o specifické chybové zprávy naleznete v následujících tématech:

* [Vzdálená relace byla odpojena, protože nejsou k dispozici vzdálené plochy licence k dispozici žádné servery k poskytnutí licence](#rdplicense).
* [Vzdálená plocha nenašla počítač "název"](#rdpname).
* [Došlo k chybě ověřování. Místní úřad zabezpečení nelze kontaktovat](#rdpauth).
* [Chyba zabezpečení systému Windows: přihlašovací údaje nebyla úspěšná](#wincred).
* [Počítač se nemůže připojit ke vzdálenému počítači](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>Vzdálená relace byla odpojena, protože nejsou k dispozici vzdálené plochy licence k dispozici žádné servery k poskytnutí licence.
Příčina: 120 licencování dní pro roli serveru vzdálené plochy vypršela platnost a budete muset nainstalovat licence.

Jako alternativní řešení uloží místní kopii souboru RDP z portálu a spusťte tento příkaz na příkazovém řádku prostředí PowerShell pro připojení. Tento krok zakazuje licencování pro právě toto připojení:

        mstsc <File name>.RDP /admin

Pokud nepotřebujete ve skutečnosti víc než dvou souběžných připojení ke vzdálené ploše do virtuálního počítače, můžete použít Správce serveru k odebrání role serveru vzdálené plochy.

Další informace naleznete v příspěvku blogu [virtuálního počítače Azure se nezdaří s "Vzdálené plochy licence k dispozici žádné servery"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Vzdálená plocha nenašla počítač "název".
Příčina: Klient vzdálené plochy ve vašem počítači nelze vyřešit název počítače v nastavení souboru protokolu RDP.

Možná řešení:

* Pokud jste na intranetu organizace, ujistěte se, že váš počítač má přístup k proxy serveru a mohou odesílat provoz HTTPS na ni.
* Pokud používáte místně uložených souborů protokolu RDP, zkuste použít ten, který je generovaný na portálu. Tento krok zajistí, že máte správný název DNS pro virtuální počítač, nebo cloudové služby a port koncový bod virtuálního počítače. Tady je ukázkový soubor RDP vytvořený portál:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

Obsahuje části adresy tohoto souboru protokolu RDP:

* Název plně kvalifikované domény cloudové služby, která obsahuje virtuální počítač ("tailspin-azdatatier.cloudapp.net" v tomto příkladu).
* Externí port TCP koncového bodu pro přenosy vzdálené plochy (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Došlo k chybě ověřování. Místní úřad zabezpečení nelze kontaktovat.
Příčina: Cílový počítač nelze najít oprávnění zabezpečení v části název uživatelské přihlašovací údaje.

Pokud je vaše uživatelské jméno ve tvaru *SecurityAuthority*\\*uživatelské jméno* (Příklad: CORP\User1), *SecurityAuthority* část je buď Virtuálního počítače název počítače (pro místní autority zabezpečení) nebo název domény služby Active Directory.

Možná řešení:

* Pokud je účet místního k virtuálnímu počítači, ujistěte se, že název virtuálního počítače je napsán správně.
* Pokud je účet v doméně služby Active Directory, zkontrolujte správnost názvu domény.
* Pokud je účet domény služby Active Directory a názvu domény je napsán správně, ověřte, zda je řadič domény k dispozici v této doméně. Je běžné problémy, se v Azure virtuální sítě, které obsahují řadiče domény, řadiče domény nedostupný, protože nebylo spuštěno. Jako řešení můžete místo účtu domény místního účtu správce.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Chyba zabezpečení systému Windows: přihlašovací údaje nebyla úspěšná.
Příčina: Cíl virtuálního počítače nelze ověřit název účtu a heslo.

Počítače se systémem Windows můžete ověřit přihlašovací údaje místní účet nebo účet domény.

* Pro místní účty, použijte *ComputerName*\\*uživatelské jméno* syntaxe (Příklad: SQL1\Admin4798).
* Pro účty domén, použijte *DomainName*\\*uživatelské jméno* syntaxe (Příklad: CONTOSO\peterodman).

Pokud jste zvýšili virtuálního počítače pro řadič domény v nové doménové struktury služby Active Directory, účet místního správce, který jste se přihlásili jsou převedeny na ekvivalentní účtu, který má stejné heslo v nové doménové struktuře a doméně. Odstraní místní účet.

Například pokud jste přihlášeni s použitím místní účet DC1\DCAdmin a poté vyzval virtuální počítač jako řadič domény v nové doménové struktury v doméně corp.contoso.com, místní účet DC1\DCAdmin se odstranila a je nový účet domény (CORP\DCAdmin) vytvořené pomocí stejné heslo.

Ujistěte se, že název účtu je název, který virtuální počítač můžete ověřit jako platný účet a heslo je správné.

Pokud potřebujete změnit heslo účtu místního správce, přečtěte si téma [jak resetovat heslo nebo služby Vzdálená plocha pro virtuální počítače s Windows](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Tento počítač se nemůže připojit ke vzdálenému počítači.
Příčina: Účet, který slouží k propojení nemá vzdálené plochy přihlašovací práva.

Každý počítač se systémem Windows má vzdálené plochy uživatele místní skupina, která obsahuje účty a skupiny, které se můžete přihlásit do ní vzdáleně. Členové místní skupiny administrators také mají přístup, i když tyto účty nejsou uvedené v místní skupině uživatelů vzdálené plochy. Pro počítače připojené k doméně místní skupiny administrators obsahuje také správci domény pro doménu.

Ujistěte se, zda má účet, který používáte pro připojení k vzdálené plochy přihlašovací práva. Jako alternativní řešení připojit přes vzdálenou plochu pomocí domény nebo účet místního správce. Požadovaný účet přidat do místní skupiny uživatelů vzdálené plochy, použijte modul snap-in konzoly Microsoft Management Console (**systémové nástroje > Místní uživatelé a skupiny > skupiny > Remote Desktop Users**).

## <a name="next-steps"></a>Další postup
Pokud žádná z těchto chyb došlo k chybě a máte neznámé vydávat pomocí připojení pomocí protokolu RDP, najdete v článku [Průvodce řešením potíží pro vzdálené plochy](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Řešení potíží s kroky v přístupu k aplikacím spuštěným na virtuálním počítači, najdete v části [řešení potíží s přístupem k aplikaci spuštěné na virtuálním počítači Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Pokud máte problémy s Secure Shell (SSH) pro připojení k virtuální počítač s Linuxem v Azure najdete v části [řešení SSH připojení k virtuální počítač s Linuxem v Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

