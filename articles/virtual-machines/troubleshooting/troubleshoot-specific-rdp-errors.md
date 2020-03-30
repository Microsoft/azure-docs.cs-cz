---
title: Konkrétní chybové zprávy RDP pro virtuální počítače Azure | Dokumenty společnosti Microsoft
description: Principy konkrétních chybových zpráv, které se mohou zobrazit při pokusu o použití připojení ke vzdálené ploše k virtuálnímu počítači s Windows v Azure
keywords: Chyba vzdálené plochy,chyba připojení ke vzdálené ploše,nelze se připojit k virtuálnímu počítači,řešení potíží se vzdálenou plochou
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 5feb1d64-ee6f-4907-949a-a7cffcbc6153
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 851c5eb4ebfee4e4a4836a07b51578dd2b0c68cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266868"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Řešení konkrétních chybových zpráv protokolu RDP pro virtuální počítač s Windows v Azure
Při použití připojení ke vzdálené ploše k virtuálnímu počítači (VM) s Windows v Azure se může zobrazit konkrétní chybová zpráva. Tento článek podrobně popisuje některé nejčastější chybové zprávy, které byly zjištěny, spolu s kroky řešení potíží k jejich vyřešení. Pokud máte problémy s připojením k virtuálnímu počítači pomocí programu RDP, ale nesetkáte se s konkrétní chybovou zprávou, [přečtěte si příručku pro řešení potíží pro vzdálenou plochu](troubleshoot-rdp-connection.md).

Informace o konkrétních chybových zprávách naleznete v následujících tématech:

* [Vzdálená relace byla odpojena, protože nejsou k dispozici žádné licenční servery vzdálené plochy, které by poskytovaly licenci](#rdplicense).
* [Vzdálená plocha nemůže najít "název" počítače](#rdpname).
* [Došlo k chybě ověřování. Místní bezpečnostní úřad nelze kontaktovat](#rdpauth).
* [Chyba zabezpečení systému Windows: Pověření nefungovala](#wincred).
* [Tento počítač se nemůže připojit ke vzdálenému počítači](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>Vzdálená relace byla odpojena, protože k dispozici nejsou žádné licenční servery vzdálené plochy, které by poskytovaly licenci.
Příčina: Platnost 120denní licenční lhůty pro roli Serveru vzdálené plochy vypršela a je třeba nainstalovat licence.

Jako řešení uložte místní kopii souboru RDP z portálu a spusťte tento příkaz na příkazovém řádku prostředí PowerShell pro připojení. Tento krok zakáže licencování pouze pro toto připojení:

        mstsc <File name>.RDP /admin

Pokud ve skutečnosti nepotřebujete více než dvě souběžná připojení ke vzdálené ploše k virtuálnímu počítači, můžete roli Server Server Manager odebrat pomocí Správce serveru.

Další informace najdete v tématu blog post [Azure Virtuální počítač selže s "Žádné licenční servery vzdálené plochy k dispozici"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Vzdálená plocha nemůže najít "název" počítače.
Příčina: Klient vzdálené plochy v počítači nemůže přeložit název počítače v nastavení souboru RDP.

Možná řešení:

* Pokud používáte intranet organizace, ujistěte se, že váš počítač má přístup k proxy serveru a může do něj odesílat přenosy https.
* Pokud používáte místně uložený soubor RDP, zkuste použít ten, který je generován portálem. Tento krok zajistí, že máte správný název DNS pro virtuální počítač nebo cloudové služby a port koncového bodu virtuálního počítače. Zde je ukázkový soubor RDP generovaný portálem:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

Část adresy tohoto souboru RDP obsahuje:

* Plně kvalifikovaný název domény cloudové služby, která obsahuje virtuální hovirtuální ho ("tailspin-azdatatier.cloudapp.net" v tomto příkladu).
* Externí port TCP koncového bodu pro provoz vzdálené plochy (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Došlo k chybě ověřování. Místní bezpečnostní úřad nelze kontaktovat.
Příčina: Cílový virtuální počítač nemůže najít autoritu zabezpečení v části uživatelského jména vašich přihlašovacích údajů.

Pokud je vaše uživatelské jméno ve formuláři *SecurityAuthority*\\*UserName* (například CORP\User1), je část *SecurityAuthority* buď název počítače virtuálního počítače (pro místní úřad zabezpečení) nebo název domény služby Active Directory.

Možná řešení:

* Pokud je účet místní pro virtuální hod, ujistěte se, že název virtuálního klienta je napsánsprávně.
* Pokud se účet nachází v doméně služby Active Directory, zkontrolujte pravopis názvu domény.
* Pokud se jedná o účet domény služby Active Directory a název domény je napsán správně, ověřte, zda je v této doméně k dispozici řadič domény. Ve virtuálních sítích Azure, které obsahují řadiče domény, je běžný problém, že řadič domény není k dispozici, protože nebyl spuštěn. Jako řešení můžete místo účtu domény použít účet místního správce.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Chyba zabezpečení systému Windows: Pověření nefungovala.
Příčina: Cílový virtuální počítač nemůže ověřit název účtu a heslo.

Počítač se systémem Windows může ověřit pověření místního účtu nebo účtu domény.

* Pro místní účty použijte syntaxi *UserName*\\*ComputerName* (například SQL1\Admin4798).
* Pro účty domény použijte syntaxi\\*Name UserName* *domainname*(příklad: CONTOSO\peterodman).

Pokud jste virtuální počítač povýšili na řadič domény v nové doménové struktuře služby Active Directory, bude účet místního správce, pomocí kterého jste se přihlásili, převeden na ekvivalentní účet se stejným heslem v nové doménové struktuře a doméně. Místní účet je pak odstraněn.

Pokud jste se například přihlásili pomocí místního účtu DC1\DCAdmin a poté jste virtuální počítač propagovali jako řadič domény v nové doménové struktuře pro doménu corp.contoso.com, místní účet DC1\DCAdmin bude odstraněn a nový účet domény (CORP\DCAdmin) bude vytvořeny se stejným heslem.

Ujistěte se, že název účtu je název, který virtuální počítač můžete ověřit jako platný účet a že heslo je správné.

Pokud potřebujete změnit heslo místního účtu správce, přečtěte si článek [Jak obnovit heslo nebo službu Vzdálená plocha pro virtuální počítače s Windows](reset-rdp.md).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Tento počítač se nemůže připojit ke vzdálenému počítači.
Příčina: Účet, který se používá k připojení, nemá práva k přihlášení ke vzdálené ploše.

Každý počítač se systémem Windows má místní skupinu uživatelů vzdálené plochy, která obsahuje účty a skupiny, které se k němu mohou vzdáleně přihlásit. Přístup mají také členové místní skupiny administrators, přestože tyto účty nejsou uvedeny v místní skupině uživatelů vzdálené plochy. U počítačů spojených s doménou obsahuje skupina místnísprávci také správce domény pro doménu.

Ujistěte se, že účet, se kterým se připojujete, má práva k přihlášení ke vzdálené ploše. Jako řešení použijte k připojení přes vzdálenou plochu účet domény nebo místního správce. Chcete-li přidat požadovaný účet do místní skupiny Uživatelé vzdálené plochy, použijte modul snap-in Konzoly mconsole **(Systémové nástroje > místní uživatele a skupiny > skupiny > uživatelé vzdálené plochy**).

## <a name="next-steps"></a>Další kroky
Pokud nedošlo k žádné z těchto chyb a máte neznámý problém s připojením pomocí protokolu RDP, [přečtěte si příručku pro řešení potíží pro vzdálenou plochu](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Postup řešení potíží při přístupu k aplikacím spuštěným na virtuálním počítači najdete [v tématu Poradce při potížích s přístupem k aplikaci spuštěné na virtuálním počítači Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Pokud máte problémy s používáním zabezpečeného prostředí (SSH) pro připojení k virtuálnímu počítači SZvu v Azure, [přečtěte si článek Řešení potíží s připojením SSH k virtuálnímu počítači s Linuxem v Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

