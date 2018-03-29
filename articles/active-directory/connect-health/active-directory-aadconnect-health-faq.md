---
title: Azure Active Directory Connect Health – nejčastější dotazy - Azure | Microsoft Docs
description: Tyto nejčastější dotazy odpovědi na otázky o Azure AD Connect Health. Jde o dotazy k používání služby, včetně modelu fakturace, schopností, omezení a podpory.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 983c2c8aeb4c9d37213061dd70d6d64bae3de9d7
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Nejčastější dotazy ke službě Azure AD Connect Health
Tento článek obsahuje odpovědi na nejčastější dotazy (FAQ) o stavu připojení služby Azure Active Directory (Azure AD). Tyto nejčastější dotazy se týkají dotazy o tom, jak používat službu, která zahrnuje fakturační model, možnosti, omezení a podpory.

## <a name="general-questions"></a>Obecné otázky
**Otázka: lze spravovat několik adresářů Azure AD. Jak přepnout na takový, který má Azure Active Directory Premium**

Chcete-li přepnout mezi různými klienty Azure AD, vyberte aktuálně přihlášeného **uživatelské jméno** v pravém horním rohu a pak vyberte příslušný účet. Pokud zde není uveden účet, vyberte **Odhlásit se**a pak použijte přihlašovací údaje globálního správce adresáře, který má Azure Active Directory Premium povolena pro přihlášení.

**Otázka: jaký verzi role identity podporuje Azure AD Connect Health?**

Následující tabulka uvádí role a podporované verze operačního systému.

|Role| Operační systém nebo verzi|
|--|--|
|AD FS (Active Directory Federation Services)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Verze 1.0.9125 nebo vyšší|
|Active Directory Domain Services (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Všimněte si, že funkce poskytované službou můžou lišit podle role a operační systém. Jinými slovy všechny funkce nemusí být dostupné pro všechny verze operačního systému. Naleznete v popisu funkce podrobnosti.

**Otázka: kolik licencí je třeba monitorovat Moje infrastrukturu?**

* První Agent Connect Health vyžaduje alespoň jednu licenci Azure AD Premium.
* Každý další registrované agent vyžaduje 25 další licence Azure AD Premium.
* Počet agentů je ekvivalentní celkový počet agentů, které jsou zaregistrovány v rámci všech monitorovaných rolí (služby AD FS, Azure AD Connect a služby AD DS).
* AAD Connect Health licencování nevyžaduje, abyste určitým uživatelům přiřadit licence. Potřebujete mít požadovaný počet platných licencí.

Informace o licencích také nachází na [stránky Azure AD cenová](https://aka.ms/aadpricing).

Příklad:

| Registrovaný agentů | Licence potřeby | Příklad konfigurace monitorování |
| ------ | --------------- | --- |
| 1 | 1 | 1 server azure AD Connect |
| 2 | 26| 1 server azure AD Connect a řadič domény 1 |
| 3 | 51 | 1 server active Directory Federation Services (AD FS), proxy 1 AD FS a řadič domény 1 |
| 4 | 76 | Server služby 1 AD FS, proxy 1 AD FS a 2 řadiče domény |
| 5 | 101 | 1 server azure AD Connect, server 1 AD FS, proxy 1 AD FS a 2 řadiče domény |

**Otázka: podporuje Azure AD Connect Health podporu cloudu Německo Azure?**

Má Azure AD Connect Health [instalace](active-directory-aadconnect-health-agent-install.md) pro Azure v Německu. Všechna data pro zákazníky, němčina cloudu je udržována v rámci cloudu Azure Německu.


## <a name="installation-questions"></a>Instalace otázky

**Otázka: co je dopad instalace Agent Azure AD Connect Health na jednotlivých serverech?**

Dopad instalace agenta služby Microsoft Azure AD Connect Health, služby AD FS, proxy servery webových aplikací, servery Azure AD Connect (sync), řadiče domény je minimální s ohledem na využití procesoru, využití paměti, šířky pásma sítě a úložiště.

Tato čísla jsou sblížení:

* Spotřeba CPU: zvýšení ~ 1-5 %.
* Využití paměti: až 10 % celkové systémové paměti.

> [!NOTE]
> Pokud agenta nemůže komunikovat s Azure, agent ukládá data místně pro definovaný maximální limit. Agent přepíše "v mezipaměti" data na základě "nedávno nejméně servis".
>
>

* Vyrovnávací paměť místní úložiště pro agenty Azure AD Connect Health: ~ 20 MB.
* Pro servery služby AD FS doporučujeme zřízení 1 024 MB (1 GB) místa na disku pro kanál auditu služby AD FS pro Azure agenty AD Connect Health zpracovat všechna data auditu předtím, než se přepíše.

**Otázka: je třeba restartovat Moje servery během instalace agenty Azure AD Connect Health?**

Ne. Instalace agentů nebude vyžadovat restartování serveru. Instalace některé z požadovaných předchozích kroků však může vyžadovat restartování serveru.

Například na Windows Server 2008 R2, vyžaduje instalaci rozhraní .NET Framework 4.5 restartování serveru.

**Otázka: funguje Azure AD Connect Health prostřednictvím průchozí proxy serveru HTTP?**

Ano. Pro probíhající operace můžete nakonfigurovat agenta stavu používat proxy server HTTP k předávání odchozí požadavky HTTP.
Další informace o [konfiguraci proxy serveru HTTP pro agenty stavu](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Pokud potřebujete nakonfigurovat proxy server při registraci agenta, může budete muset předem upravit vaše nastavení proxy serveru Internet Explorer.

1. Otevřete Internet Explorer > **nastavení** > **Možnosti Internetu** > **připojení** > **nastavení místní sítě**.
2. Vyberte **použít Proxy Server pro síť LAN**.
3. Vyberte **Upřesnit** Pokud máte jiný proxy portů pro protokol HTTP a HTTPS/Secure.

**Otázka: podporuje základní ověřování Azure AD Connect Health podporu při připojování k HTTP proxy?**

Ne. Mechanismus zadejte libovolné uživatelské jméno a heslo pro základní ověřování není aktuálně podporován.

**Otázka: jaký porty brány firewall je nutné otevřít pro Agent Azure AD Connect Health pro práci?**

Najdete v článku [v části požadavky na](active-directory-aadconnect-health-agent-install.md#requirements) seznam porty brány firewall a další požadavky na připojení.

**Otázka: Proč vidí dva servery se stejným názvem na portálu Azure AD Connect Health?**

Když odeberete agenta ze serveru, serveru automaticky se neodebere z portálu Azure AD Connect Health. Pokud ručně odebrat agenta ze serveru nebo odeberte samotný server, budete muset ručně odstranit položku serveru z portálu Azure AD Connect Health.

Můžete obnovit z Image serveru a vytvořit nový server se stejným podrobnosti (například název počítače). Pokud již registrovaný server není odebrat z portálu Azure AD Connect Health a jste nainstalovali agenta na novém serveru, může se zobrazit dvě položky se stejným názvem.

V takovém případě ručně odstraňte položku, která patří do starším serveru. Data pro tento server by měl být aktuální.

## <a name="health-agent-registration-and-data-freshness"></a>Registrace a data aktuálnosti agenta stavu

**Otázka: co jsou běžné příčiny selhání registrace agenta stavu a jak odstranit problémy?**

Agent stavu můžete nezdaří jejich registrace z následujících možných důvodů:

* Agent nemůže komunikovat s požadované koncové body, protože brána firewall neblokuje přenosy. To je zvlášť běžné na proxy servery webových aplikací. Ujistěte se, že se povolí odchozí komunikaci požadované koncové body a porty. Najdete v článku [v části požadavky na](active-directory-aadconnect-health-agent-install.md#requirements) podrobnosti.
* Odchozí komunikaci se vztahuje kontrola SSL vrstvou sítě. To způsobí, že certifikát, který agent používá nahradit za kontroly serveru nebo entity a selhání postup dokončit registraci agenta.
* Uživatel nemá přístup k provedení registrace agenta. Globální Správci mají přístup ve výchozím nastavení. Můžete použít [řízení přístupu na základě Role](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) budete chtít udělit přístup ostatním uživatelům.

**Otázka: I mě získávání upozornění, že "služba Health Service data nejsou aktuální." Jak odstranit potíže?**

Azure AD Connect Health vygeneruje výstrahu, pokud neobdrží datových bodů ze serveru za posledních dvou hodin. [Přečtěte si další informace](active-directory-aadconnect-health-data-freshness.md).

## <a name="operations-questions"></a>Operace otázky
**Otázka: Potřebuji povolení auditování na proxy servery webových aplikací?**

Ne, auditování nemusí být povolená na proxy servery webových aplikací.

**Otázka: Jak získat Azure AD Connect Health výstrahy vyřešit?**

Azure AD Connect Health výstrahy získat přeložit na podmínka pro úspěch. Agenty Azure AD Connect Health zjistit a pravidelně zprávy podmínky úspěch ke službě. Několik výstrah je potlačení založené na čase. Jinými slovy Pokud není v rámci 72 hodin od generování výstrah stejnou chybový stav, výstraha se automaticky vyřeší.

**Otázka: I mě získávání upozornění, že "žádosti o testovací ověření (syntetické transakci) se nepodařilo získat token." Jak odstranit potíže?**

Azure AD Connect Health pro službu AD FS generuje upozornění, když Health Agent nainstalovaný na server služby AD FS nepodaří získat token jako součást syntetické transakce iniciovaná agenta stavu. Agent stavu používá kontextu místního systému a pokusí se získat token pro svým předávající strany. Toto je test catch-all zajistit, že se služba AD FS ve stavu vystavování tokenů.

Nejčastěji tento test se nezdaří, protože Agent stavu nemůže přeložit název farmy služby AD FS. To může dojít, pokud jsou servery služby AD FS za nástroje pro vyrovnávání zatížení sítě a požadavek získá inicializována z uzlu, který je za nástrojem pro vyrovnávání zatížení (na rozdíl od regulární klienta, který je před nástroje pro vyrovnávání zatížení). To lze opravit aktualizací "hostitele" soubor nachází v části "C:\Windows\System32\drivers\etc" zahrnují IP adresu serveru služby AD FS nebo IP adresu zpětné smyčky (127.0.0.1) pro název farmy služby AD FS (např. sts.contoso.com). Přidání souboru hostitele bude krátká smyčka – volání sítě, což umožňuje agenta stavu se získat token.

**Otázka: chybové e-mailu oznamující, že mých počítačů není nainstalována oprava poslední ransomeware útokům. Proč zobrazila tato e-mailu?**

Služba Azure AD Connect Health zkontrolovat všechny, které byly nainstalovány na počítače, které sleduje zajistit vyžadovaných oprav. Správci klientů byla odeslána e-mailu, pokud alespoň jeden počítač neměl důležité opravy. Za účelem určení byl použit následující logice.
1. Najít všechny opravy hotfix v počítači nainstalován.
2. Zkontrolujte, zda je přítomen alespoň jeden ze seznamu definované opravy hotfix.
3. Pokud ano, počítač je chráněný. Pokud ne, tento počítač je znamenat nebezpečí útoku.

Následující skript prostředí PowerShell můžete použít k provedení této kontroly ručně. Implementuje logice výše.

```
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```

**Otázka: Proč se rutiny prostředí PowerShell <i>Get-MsolDirSyncProvisioningError</i> zobrazit méně chyby synchronizace ve výsledku?**

<i>Get-MsolDirSyncProvisioningError</i> vrátí jenom DirSync chyby zřizování. Kromě toho, že portál Connect Health také ukazuje další synchronizaci typů chyb, jako je například export chyby. To je konzistentní s Azure AD Connect rozdílů výsledek. Další informace o [Azure AD Connect Sync chyby](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors).

**Otázka: Proč se Moje ADFS audity není generované?**

Použijte rutiny prostředí PowerShell <i>Get-AdfsProperties – AuditLevel</i> zajistit protokoly auditování není v zakázané stavu. Další informace o [protokoly auditu služby AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016)


## <a name="related-links"></a>Související odkazy
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalace agenta služby Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operace služby Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Používání služby Azure AD Connect Health se službou AD FS](active-directory-aadconnect-health-adfs.md)
* [Používání služby Azure AD Connect Health pro synchronizaci](active-directory-aadconnect-health-sync.md)
* [Používání služby Azure AD Connect Health se službou AD DS](active-directory-aadconnect-health-adds.md)
* [Historie verzí služby Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
