---
title: Konkrétní chybové zprávy protokolu RDP pro virtuální počítače Azure | Microsoft Docs
description: Pochopení konkrétních chybových zpráv, které se mohou zobrazit při pokusu o použití připojení vzdálené plochy k virtuálnímu počítači s Windows v Azure
keywords: Chyba vzdálené plochy, Chyba připojení ke vzdálené ploše, nelze se připojit k virtuálnímu počítači, odstraňování potíží vzdálené plochy
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
ms.openlocfilehash: 808443cb805b2dee2426198f9cd4f7ba502d8999
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197502"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Řešení konkrétních chybových zpráv protokolu RDP pro virtuální počítač s Windows v Azure
Při použití připojení vzdálené plochy k virtuálnímu počítači s Windows (VM) v Azure se může zobrazit konkrétní chybová zpráva. Tento článek podrobně popisuje některé běžné chybové zprávy, ke kterým došlo, a postup řešení potíží. Pokud máte problémy s připojením k VIRTUÁLNÍmu počítači pomocí protokolu RDP, ale nedošlo k určité chybové zprávě, přečtěte si článek [Průvodce odstraňováním potíží pro vzdálenou plochu](troubleshoot-rdp-connection.md).

Informace o konkrétních chybových zprávách najdete v následujících tématech:

* [Vzdálená relace byla odpojena, protože nejsou k dispozici žádné licenční servery vzdálené plochy pro poskytnutí licence](#rdplicense).
* [Vzdálená plocha nemůže najít počítač "název"](#rdpname).
* [Došlo k chybě ověřování. Místní autoritu zabezpečení nelze kontaktovat](#rdpauth).
* [Chyba zabezpečení systému Windows: vaše přihlašovací údaje nefungovaly](#wincred).
* [Tento počítač se nemůže připojit ke vzdálenému počítači](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>Vzdálená relace byla odpojena, protože nejsou k dispozici žádné licenční servery vzdálené plochy pro poskytnutí licence.
Příčina: vypršela doba odkladu licencí pro roli serveru vzdálené plochy 120. je potřeba nainstalovat licence.

Jako alternativní řešení uložte místní kopii souboru RDP z portálu a spusťte tento příkaz na příkazovém řádku PowerShellu pro připojení. Tento krok zakáže licencování jenom pro toto připojení:

```powershell
mstsc <File name>.RDP /admin
```

Pokud nepotřebujete ve skutečnosti více než dvě současná připojení ke vzdálené ploše k virtuálnímu počítači, můžete k odebrání role serveru Vzdálená plocha použít Správce serveru.

Další informace najdete v tématu Blogový příspěvek [virtuálního počítače Azure se nepovede bez dostupných licenčních serverů vzdálené plochy](/archive/blogs/mast/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Vzdálená plocha nemůže najít počítač "název".
Příčina: klient vzdálené plochy v počítači nemůže přeložit název počítače v nastavení souboru RDP.

Možná řešení:

* Pokud pracujete v intranetu organizace, ujistěte se, že váš počítač má přístup k proxy server a může do něj odesílat přenosy přes protokol HTTPS.
* Pokud používáte místně uložený soubor RDP, zkuste použít ten, který je vygenerovaný portálem. Tento krok zajistí, že budete mít správný název DNS pro virtuální počítač nebo cloudovou službu a port koncového bodu virtuálního počítače. Tady je ukázkový soubor RDP generovaný portálem:

    ```output
    full address:s:tailspin-azdatatier.cloudapp.net:55919
    prompt for credentials:i:1
    ```

Část adresy tohoto souboru RDP má následující:

* Plně kvalifikovaný název domény cloudové služby, která obsahuje virtuální počítač (v tomto příkladu "tailspin-azdatatier.cloudapp.net").
* Externí port TCP koncového bodu pro provoz vzdálené plochy (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Došlo k chybě ověřování. Místní autoritu zabezpečení nelze kontaktovat.
Příčina: cílový virtuální počítač nemůže najít autoritu zabezpečení v části uživatelské jméno vašich přihlašovacích údajů.

Pokud je uživatelské jméno ve tvaru *SecurityAuthority* \\ *username* (příklad: CORP\User1.), část *SecurityAuthority* je název počítače (pro místní úřad zabezpečení) nebo název domény služby Active Directory.

Možná řešení:

* Pokud je účet místní k virtuálnímu počítači, ujistěte se, že je název virtuálního počítače napsaný správně.
* Pokud je účet v doméně služby Active Directory, zkontrolujte pravopis názvu domény.
* Pokud se jedná o účet domény služby Active Directory a název domény je zadán správně, ověřte, zda je v této doméně k dispozici řadič domény. Jedná se o běžný problém v Azure Virtual Networks, který obsahuje řadiče domény, které jsou řadičem domény nedostupné, protože nezačaly. Jako alternativní řešení můžete místo účtu domény použít účet místního správce.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Chyba zabezpečení systému Windows: vaše přihlašovací údaje nefungovaly.
Příčina: cílový virtuální počítač nemůže ověřit název vašeho účtu a heslo.

Počítač se systémem Windows může ověřit pověření místního účtu nebo účtu domény.

* Pro místní účty použijte syntaxi *ComputerName* \\ *username* (příklad: SQL1\Admin4798).
* V případě doménových účtů použijte syntaxi *DomainName* \\ *username* (příklad: CONTOSO\peterodman).

Pokud jste virtuální počítač zvýšili na řadič domény v nové doménové struktuře služby Active Directory, účet místního správce, pomocí kterého jste se přihlásili, se převede na ekvivalentní účet se stejným heslem v nové doménové struktuře a doméně. Místní účet se pak odstraní.

Pokud jste například přihlášeni pomocí místního účtu DC1\DCAdmin a potom jste zvýšili virtuální počítač jako řadič domény v nové doménové struktuře pro doménu corp.contoso.com, bude odstraněn místní účet DC1\DCAdmin a vytvoří se nový doménový účet (CORP\DCAdmin) se stejným heslem.

Ujistěte se, že název účtu je název, který může virtuální počítač ověřit jako platný účet a heslo je správné.

Pokud potřebujete změnit heslo účtu místního správce, přečtěte si téma [Postup resetování hesla nebo služby Vzdálená plocha pro virtuální počítače s Windows](reset-rdp.md).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Tento počítač se nemůže připojit ke vzdálenému počítači.
Příčina: účet, který se používá k připojení, nemá oprávnění k přihlášení ke vzdálené ploše.

Každý počítač s Windows má místní skupinu Remote Desktop Users, která obsahuje účty a skupiny, které se můžou vzdáleně přihlašovat. K nim mají přístup i členové místní skupiny Administrators, i když tyto účty nejsou uvedené v místní skupině Remote Desktop Users. Pro počítače připojené k doméně obsahuje Skupina Local Administrators také správce domény pro danou doménu.

Ujistěte se, že účet, který používáte k připojení, má přihlašovací práva ke vzdálené ploše. Alternativním řešením je použít účet domény nebo místního správce pro připojení přes vzdálenou plochu. Chcete-li přidat požadovaný účet do místní skupiny Uživatelé vzdálené plochy, použijte modul snap-in konzoly Microsoft Management Console (**Systémové nástroje > místní uživatelé a skupiny > skupiny > Uživatelé vzdálené plochy**).

## <a name="next-steps"></a>Další kroky
Pokud žádná z těchto chyb nevznikla a došlo k neznámému problému s připojením pomocí protokolu RDP, přečtěte si [Průvodce řešením potíží pro vzdálenou plochu](troubleshoot-rdp-connection.md).

* Postup řešení potíží při přístupu k aplikacím běžícím na virtuálním počítači najdete v tématu [řešení potíží s přístupem k aplikaci spuštěné na virtuálním počítači Azure](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json).
* Pokud máte problémy s použitím Secure Shell (SSH) pro připojení k virtuálnímu počítači se systémem Linux v Azure, přečtěte si téma [řešení potíží s připojením SSH k virtuálnímu počítači se systémem Linux v Azure](./troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json).
