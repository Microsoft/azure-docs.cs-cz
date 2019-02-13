---
title: Azure Active Directory Connect Health – nejčastější dotazy – Azure | Dokumentace Microsoftu
description: Tyto nejčastější dotazy odpovědi na otázky o Azure AD Connect Health. Jde o dotazy k používání služby, včetně modelu fakturace, schopností, omezení a podpory.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c1b653ee16864f5076cdad9d1dbc33e63b175ca
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167586"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Nejčastější dotazy ke službě Azure AD Connect Health
Tento článek obsahuje odpovědi na nejčastější dotazy (FAQ) týkající se Azure Active Directory (Azure AD) Connect Health. Tyto nejčastější dotazy k pokrytí otázek o tom, jak používat službu, která zahrnuje fakturační model, možnosti, omezení a podpory.

## <a name="general-questions"></a>Obecné otázky
**Otázka: Můžu spravovat více adresářů Azure AD. Jak přepnout na takový, který má Azure Active Directory Premium?**

Chcete-li přepnout mezi různými klienty Azure AD, vyberte aktuálně přihlášeného **uživatelské jméno** v pravém horním rohu a pak vyberte příslušný účet. Pokud tu není uvedený účet, vyberte **Odhlásit**a pak použijte přihlašovací údaje globálního správce adresáře, který má Azure Active Directory Premium povolené pro přihlášení.

**Otázka: Jakou verzi identity rolí jsou podporovány službou Azure AD Connect Health?**

Následující tabulka uvádí role a podporované verze operačního systému.

|Role| Operační systém a verze|
|--|--|
|AD FS (Active Directory Federation Services)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Verze 1.0.9125 nebo vyšší|
|Active Directory Domain Services (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Všimněte si, že funkce poskytované službou můžou lišit podle role a operační systém. Jinými slovy všechny funkce nemusí být dostupné pro všechny verze operačního systému. Zobrazit popis funkcí podrobnosti.

**Otázka: Kolik licencí je nutné sledovat své infrastruktury?**

* První Agent Connect Health vyžaduje alespoň jednu licenci Azure AD Premium.
* Každý další agent registrované vyžaduje 25 další licence Azure AD Premium.
* Počet agentů je stejná jako celkový počet agentů, které jsou registrované ve všech monitorovaných role (AD FS, Azure AD Connect a služby AD DS).
* Licence AAD Connect Health nevyžaduje přiřazení licence na konkrétní uživatele. Stačí mít požadovaný počet platných licencí.

Licenční informace také najdete v [stránce s cenami služby Azure AD](https://aka.ms/aadpricing).

Příklad:

| Registrovaných agentů | Licence potřebné | Příklad konfigurace monitorování |
| ------ | --------------- | --- |
| 1 | 1 | 1 server azure AD Connect |
| 2 | 26| 1 server azure AD Connect a řadiče domény 1 |
| 3 | 51 | 1 server active Directory Federation Services (AD FS), 1 AD FS proxy a řadič domény 1 |
| 4 | 76 | Server 1 AD FS, proxy 1 AD FS a 2 řadiče domény |
| 5 | 101 | 1 server azure AD Connect, server 1 AD FS, proxy 1 AD FS a 2 řadiče domény |

**Otázka: Podporuje Azure AD Connect Health Azure německý Cloud?**

Azure AD Connect Health se nepodporuje v německého cloudu s výjimkou [funkce sestavy chyb synchronizace](how-to-connect-health-sync.md#object-level-synchronization-error-report).

| Role | Funkce | Podporované v německém cloudu |
| ------ | --------------- | --- |
| Connect Health pro synchronizaci | Monitorování / přehled / výstrahy a analýzy | Ne |
|  | Sestava chyb synchronizace | Ano |
| Connect Health pro AD FS | Monitorování / přehled / výstrahy a analýzy | Ne |
| Connect Health pro AD DS | Monitorování / přehled / výstrahy a analýzy | Ne |

K zajištění připojení agenta služby Connect Health pro synchronizaci, nakonfigurujte [požadavky na instalaci](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) odpovídajícím způsobem.

## <a name="installation-questions"></a>Dotazy ohledně instalace

**Otázka: Jaký bude dopad instalace Agent Azure AD Connect Health na jednotlivých serverech?**

Instalace agenta služby Microsoft Azure AD Connect Health, služby AD FS, proxy serverech webových aplikací, servery Azure AD Connect (sync), řadiče domény se minimální s ohledem na využití procesoru, využití paměti, šířky pásma sítě a úložiště.

Přibližný jsou následující čísla:

* Využití procesoru: ~ 1-5 % zvýšení.
* Spotřeba paměti: Až 10 % celkové systémové paměti.

> [!NOTE]
> Pokud agent nemůže komunikovat s Azure, agent ukládá data místně pro definovaný maximální limit. Agent přepíše "v mezipaměti" data na základě "nejdéle Údržba".
>
>

* Úložiště místní vyrovnávací paměti pro agenty Azure AD Connect Health: ~ 20 MB.
* Pro servery služby AD FS doporučujeme pro kanál auditu služby AD FS pro agenty služby Azure AD Connect Health ke zpracování všech dat auditu předtím, než je přepsán, zřídíte 1 024 MB (1 GB) místo na disku.

**Otázka: Budu muset restartovat své servery během instalace Azure AD agenty služby Connect Health?**

Ne. Instalace agentů nebude vyžadovat restartování serveru. Instalace některé požadované kroky však může vyžadovat restartování serveru.

Například v systému Windows Server 2008 R2, vyžaduje instalaci rozhraní .NET Framework 4.5 restartování serveru.

**Otázka: Funguje přes předávací proxy server HTTP Azure AD Connect Health?**

Ano. Pro probíhající operace můžete nakonfigurovat agenta stavu pro použití proxy serveru HTTP ke směrování odchozích žádostí HTTP.
Další informace o [konfiguraci proxy serveru HTTP pro agenty služby Health](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Pokud je potřeba konfigurace proxy serveru během registrace agenta, můžete potřebovat předem upravovat vaše nastavení proxy serveru Internet Explorer.

1. Spusťte aplikaci Internet Explorer > **nastavení** > **Možnosti Internetu** > **připojení** > **nastavení místní sítě** .
2. Vyberte **používat Proxy Server pro síť LAN**.
3. Vyberte **Upřesnit** Pokud máte jiný server proxy portů pro protokol HTTP a HTTPS/Secure.

**Otázka: Podporuje Azure AD Connect Health základní ověřování při připojování k HTTP proxy?**

Ne. Mechanismus pro zadejte libovolné uživatelské jméno a heslo pro základní ověřování není aktuálně podporováno.

**Otázka: Jaké porty brány firewall je potřeba otevřít agenta Azure AD Connect Health pro práci?**

Najdete v článku [části věnované požadavkům](how-to-connect-health-agent-install.md#requirements) seznam porty brány firewall a další požadavky na připojení.

**Otázka: Proč se zobrazují dva servery se stejným názvem na portálu Azure AD Connect Health?**

Když odeberete agenta ze serveru, není server automaticky odebere z portálu Azure AD Connect Health. Pokud ručně odebrat agenta ze serveru nebo samotný server odebrat, musíte ručně odstranit záznam serveru z portálu Azure AD Connect Health.

Může znovu připojit bitovou kopii serveru nebo vytvořit nový server pomocí stejné informace (například název počítače). Pokud jste neodebral již registrovaného serveru z portálu Azure AD Connect Health a jste agenta nainstalovali na novém serveru, může se zobrazit dvě položky se stejným názvem.

V takovém případě ručně odstraňte položku, která patří do staršího serveru. Data pro tento server by měl být zastaralá.

## <a name="health-agent-registration-and-data-freshness"></a>Agent stavu registrace a data aktuálnosti

**Otázka: Jaké jsou běžné důvody pro selhání registrace agenta stavu a jak řešit problémy?**

Agent stavu může selhat registrace z následujících možných důvodů:

* Agent nemůže komunikovat s požadované koncové body, protože brána firewall blokuje provoz. Toto je obzvlášť častým na proxy serverech webových aplikací. Ujistěte se, že se povolí odchozí komunikaci požadované koncové body a porty. Najdete v článku [části věnované požadavkům](how-to-connect-health-agent-install.md#requirements) podrobnosti.
* Odchozí komunikace je podléhá kontrole SSL ze strany síťové vrstvy. To způsobí, že certifikát, který používá agent bude nahrazen kontroly serveru na entitu a kroky k dokončení registrace agenta se nezdaří.
* Uživatel nemá přístup k provedení registrace agenta. Globální Správci mají ve výchozím nastavení přístup. Můžete použít [řízení přístupu na základě rolí](how-to-connect-health-operations.md#manage-access-with-role-based-access-control) delegovat přístup k ostatním uživatelům.

**Otázka: Můžu jsem získávání upozorněni, že "data služby Health Service není aktuální." Jak řešit potíže?**

Azure AD Connect Health vygeneruje výstrahu, pokud neobdrží všechny datové body od serveru za posledních dvou hodin. [Další informace](how-to-connect-health-data-freshness.md).

## <a name="operations-questions"></a>Operace dotazy
**Otázka: Je nutné povolit auditování na proxy serverech webových aplikací?**

Ne, auditování nemusí být povolené na proxy serverech webových aplikací.

**Otázka: Jak vyřešila upozornění Azure AD Connect Health?**

Upozornění Azure AD Connect Health se vyřešila na podmínka pro úspěch. Agenti Azure AD Connect Health detekovat a pravidelně informuje službu podmínek úspěchu. Několik upozornění potlačení je podle času. Jinými slovy Pokud není během 72 hodin od generování výstrah stejnou chybový stav, výstraha automaticky vyřeší.

**Otázka: Můžu jsem získávání upozorněni, že "žádosti o testovací ověření (syntetické transakci) se nepodařilo získat token." Jak řešit potíže?**

Azure AD Connect Health pro AD FS generuje toto upozornění v případě chyby stavu agenta nainstalovali na server služby AD FS k získání tokenu jako součást syntetické transakce iniciovaných agenta stavu. Agent stavu používá kontextu místního systému a pokusí se získat token pro samoobslužné předávající strany. Toto je test pokrývající vše k zajištění, že služba AD FS je ve stavu vydávání tokenů.

Nejčastěji tento test se nezdaří, protože je Agent stavu nejde přeložit název farmy služby AD FS. To může nastat, pokud jsou servery služby AD FS za nástroje pro vyrovnávání zatížení sítě a inicializuje se žádost získá z uzlu, který je za nástroj pro vyrovnávání zatížení (na rozdíl od pravidelných klienta, který je před nástroje pro vyrovnávání zatížení). To lze opravit aktualizací "hostitelů" soubor umístěný v části "C:\Windows\System32\drivers\etc" IP adresu serveru služby AD FS nebo adresu IP zpětné smyčky (127.0.0.1) pro název farmy služby AD FS (například sts.contoso.com). Přidání souboru hostitele bude zkrácenou síťová volání, což umožní agenta stavu se získat token.

**Otázka: Zobrazilo se mi e-mail s oznámením, že mých počítačů není nainstalována oprava poslední ransomeware útoků. Proč tento e-mail dostali?**

Služba Azure AD Connect Health prohledávány všechno, co byly nainstalovány na počítače, které monitorují zajistit vyžadovaných oprav. E-mail jste dostali správci klientů, pokud alespoň jeden počítač neměl důležité opravy. Za účelem určení byl použit podle následujícího postupu.
1. Najdete všechny opravy hotfix, na počítači nainstalovaný.
2. Zkontrolujte, jestli je k dispozici aspoň jeden opravy hotfix z definovaném seznamu.
3. Pokud ano, je chráněný počítač. Pokud ne, tento počítač je hrozí nebezpečí útoku.

Následující skript prostředí PowerShell můžete použít k provedení této kontroly ručně. Implementuje logiku výše.

```powershell
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

**Otázka: Proč tuto rutinu Powershellu <i>Get-MsolDirSyncProvisioningError</i> zobrazit méně chybám synchronizace ve výsledku?**

<i>Get-MsolDirSyncProvisioningError</i> vrátí pouze DirSync chyby zřizování. Kromě toho portál služby Connect Health také ukazuje další synchronizace typy chyb, jako je například export chyb. To je konzistentní s výsledkem rozdílů služby Azure AD Connect. Další informace o [chyb Azure AD Connect Sync](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors).

**Otázka: Proč se Moje služby AD FS Audituje naprostou vygenerovat?**

Použijte prosím rutinu Powershellu <i>Get-AdfsProperties - AuditLevel</i> zajistit protokolů auditování není v zakázaném stavu. Další informace o [protokoly auditu služby AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016). Oznámení, že pokud existuje pokročilé nastavení auditu nahrány na server ADFS, veškeré změny s auditpol.exe budou přepsány (událost když vytvářených aplikací není nakonfigurované). V takovém případě nastavte prosím místní zásady zabezpečení do protokolu chyb aplikací vygenerované a úspěšné dokončení.


## <a name="related-links"></a>Související odkazy
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Instalace agenta služby Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Operace služby Azure AD Connect Health](how-to-connect-health-operations.md)
* [Používání služby Azure AD Connect Health se službou AD FS](how-to-connect-health-adfs.md)
* [Používání služby Azure AD Connect Health pro synchronizaci](how-to-connect-health-sync.md)
* [Používání služby Azure AD Connect Health se službou AD DS](how-to-connect-health-adds.md)
* [Historie verzí služby Azure AD Connect Health](reference-connect-health-version-history.md)
