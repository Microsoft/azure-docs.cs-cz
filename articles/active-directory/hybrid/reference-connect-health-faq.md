---
title: Nejčastější dotazy ke stavu služby Azure Active Directory Connect – Azure | Dokumenty společnosti Microsoft
description: Tyto nejčastější dotazy odpovídají na otázky týkající se azure ad připojení stavu. Jde o dotazy k používání služby, včetně modelu fakturace, schopností, omezení a podpory.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0c6484f46731e0ff2d16d00cb0038202511d193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331080"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Azure AD Connect Health nejčastější dotazy
Tento článek obsahuje odpovědi na nejčastější dotazy týkající se azure active directory (Azure AD) connect health. Tyto časté dotazy se týkají otázek týkajících se používání služby, která zahrnuje model fakturace, možnosti, omezení a podporu.

## <a name="general-questions"></a>Obecné otázky
**Otázka: Spravuji několik adresářů Azure AD. Jak přepnu na ten, který má Azure Active Directory Premium?**

Pokud chcete přepínat mezi různými klienty Azure AD, vyberte aktuálně přihlášené **uživatelské jméno** v pravém horním rohu a pak zvolte příslušný účet. Pokud zde účet není uvedený, vyberte **Odhlásit**se a potom použijte přihlašovací údaje globálního správce adresáře, ve kterých je povolena služba Azure Active Directory Premium, a přihlaste se.

**Otázka: Jakou verzi rolí identity podporuje Azure AD Connect Health?**

V následující tabulce jsou uvedeny role a podporované verze operačního systému.

|Role| Operační systém / Verze|
|--|--|
|Active Directory Federation Services (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | Verze 1.0.9125 nebo vyšší|
|AD DS (Active Directory Domain Services)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

Všimněte si, že funkce poskytované službou se mohou lišit v závislosti na roli a operačním systému. Jinými slovy, všechny funkce nemusí být k dispozici pro všechny verze operačního systému. Podrobnosti najdete v popisech funkcí.

**Otázka: Kolik licencí potřebuji ke sledování infrastruktury?**

* První agent connect health vyžaduje alespoň jednu licenci Azure AD Premium.
* Každý další registrovaný agent vyžaduje dalších 25 licencí Azure AD Premium.
* Počet agentů je ekvivalentní celkovému počtu agentů, kteří jsou registrováni ve všech sledovaných rolích (AD FS, Azure AD Connect a/nebo AD DS).
* Licencování služby AAD Connect Health nevyžaduje přiřazení licence konkrétním uživatelům. Potřebujete pouze požadovaný počet platných licencí.

Informace o licencích se také nacházejí na [stránce Ceny Azure AD](https://aka.ms/aadpricing).

Příklad:

| Registrovaní agenti | Potřebné licence | Příklad konfigurace monitorování |
| ------ | --------------- | --- |
| 1 | 1 | 1 Server Azure AD Connect |
| 2 | 26| 1 Server Azure AD Connect a 1 řadič domény |
| 3 | 51 | 1 server služby AD FS (Active Directory Federation Services), 1 proxy server služby AD FS a 1 řadič domény |
| 4 | 76 | 1 server služby AD FS, 1 proxy server služby AD FS a 2 řadiče domény |
| 5 | 101 | 1 server Azure AD Connect, 1 server Služby AD FS, 1 proxy server služby AD FS a 2 řadiče domény |

**Otázka: Podporuje Azure AD Connect Health Azure Germany Cloud?**

Azure AD Connect Health není v Německu podporováno s výjimkou [funkce sestavy chyb synchronizace](how-to-connect-health-sync.md#object-level-synchronization-error-report).

| Role | Funkce | Podporováno v německém cloudu |
| ------ | --------------- | --- |
| Stav připojení pro synchronizaci | Monitoring / Insight / Upozornění / Analýza | Ne |
|  | Zpráva o chybách synchronizace | Ano |
| Stav připojení pro službu ADFS | Monitoring / Insight / Upozornění / Analýza | Ne |
| Připojit stav pro přidává | Monitoring / Insight / Upozornění / Analýza | Ne |

Chcete-li zajistit připojení agenta služby Connect Health pro synchronizaci, nakonfigurujte odpovídajícím způsobem [požadavek na instalaci.](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints)

## <a name="installation-questions"></a>Otázky k instalaci

**Otázka: Jaký je dopad instalace agenta azure ad připojení stavu na jednotlivých serverech?**

Dopad instalace Microsoft Azure AD Connect Health Agent, AD FS, proxy servery webových aplikací, Servery Azure AD Connect (synchronizace), řadiče domény je minimální s ohledem na procesor, využití paměti, šířku pásma sítě a úložiště.

Následující čísla jsou aproximace:

* Spotřeba procesoru: ~ 1-5% nárůst.
* Spotřeba paměti: Až 10 % celkové systémové paměti.

> [!NOTE]
> Pokud agent nemůže komunikovat s Azure, agent ukládá data místně pro definovaný maximální limit. Agent přepíše data "v mezipaměti" na základě "nejméně nedávno obsluhované" základ.
>
>

* Místní úložiště vyrovnávací paměti pro agenty stavu Azure AD Connect: ~20 MB.
* Pro servery služby AD FS doporučujeme zřídit místo na disku 1 024 MB (1 GB) pro kanál auditu služby AD FS pro agenty stavu Azure AD Connect ke zpracování všech dat auditu před jejich přepsáním.

**Otázka: Budu muset restartovat své servery během instalace agentů azure ad připojení stavu?**

Ne. Instalace agentů nebude vyžadovat restartování serveru. Instalace některých nezbytných kroků však může vyžadovat restartování serveru.

Například v systému Windows Server 2008 R2 vyžaduje instalace rozhraní .NET 4.5 Framework restartování serveru.

**Otázka: Funguje azure ad připojení stavu prostřednictvím předávací http proxy?**

Ano. Pro probíhající operace můžete nakonfigurovat agenta stavu tak, aby k předávání odchozích požadavků HTTP používal proxy server HTTP.
Přečtěte si další informace o [konfiguraci proxy protokolu HTTP pro agenty stavu](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Pokud potřebujete nakonfigurovat proxy server během registrace agenta, bude pravděpodobně nutné předem upravit nastavení proxy serveru aplikace Internet Explorer.

1. Sem > Nastavení**aplikace Internet** >  **Explorer** > **Nastavení připojení k** > **síti LAN**.
2. Vyberte **použít proxy server pro vaši síť LAN**.
3. Pokud máte různé **porty** proxy pro protokoly HTTP a HTTPS/Secure, vyberte upřesnit.

**Otázka: Podporuje Azure AD Connect Health základní ověřování při připojování k proxy servery HTTP?**

Ne. Mechanismus pro určení libovolného uživatelského jména a hesla pro základní ověřování není aktuálně podporován.

**Otázka: Jaké porty brány firewall je potřeba otevřít pro Azure AD Connect Health Agent pracovat?**

V [části požadavky](how-to-connect-health-agent-install.md#requirements) naleznete seznam portů brány firewall a další požadavky na připojení.

**Otázka: Proč se na portálu Azure AD Connect Health zobrazují dva servery se stejným názvem?**

Když odeberete agenta ze serveru, server se automaticky neodebere z portálu Azure AD Connect Health. Pokud ručně odeberete agenta ze serveru nebo odeberete samotný server, budete muset ručně odstranit položku serveru z portálu Azure AD Connect Health.

Můžete znovu zobrazit server nebo vytvořit nový server se stejnými podrobnostmi (například názvem počítače). Pokud jste neodebrali již registrovaný server z portálu Azure AD Connect Health a nainstalovali jste agenta na nový server, mohou se zobrazit dvě položky se stejným názvem.

V takovém případě ručně odstraňte položku, která patří ke staršímu serveru. Data pro tento server by měla být zastaralá.

## <a name="health-agent-registration-and-data-freshness"></a>Registrace agenta stavu a čerstvost dat

**Otázka: Jaké jsou běžné důvody selhání registrace agenta stavu a jak lze řešit problémy?**

Agent stavu může selhat zaregistrovat z následujících možných důvodů:

* Agent nemůže komunikovat s požadovanými koncovými body, protože brána firewall blokuje provoz. To je běžné zejména na proxy serverech webových aplikací. Ujistěte se, že jste povolili odchozí komunikaci s požadovanými koncovými body a porty. Podrobnosti naleznete v [části požadavky.](how-to-connect-health-agent-install.md#requirements)
* Odchozí komunikace je podrobena kontrole TLS síťovou vrstvou. To způsobí, že certifikát, který agent používá k nahrazení kontrolní server nebo entita a kroky k dokončení registrace agenta nezdaří.
* Uživatel nemá přístup k provedení registrace agenta. Globální správci mají ve výchozím nastavení přístup. Řízení [přístupu založené na rolích](how-to-connect-health-operations.md#manage-access-with-role-based-access-control) můžete použít k delegování přístupu ostatním uživatelům.

**Otázka: Dostávám upozornění, že "Data zdravotní služby nejsou aktuální." Jak lze problém vyřešit?**

Azure AD Connect Health generuje výstrahu, když neobdrží všechny datové body ze serveru v posledních dvou hodinách. [Přečtěte si další informace](how-to-connect-health-data-freshness.md).

## <a name="operations-questions"></a>Provozní otázky
**Otázka: Je nutné povolit auditování na proxy serverech webové aplikace?**

Ne, auditování nemusí být povoleno na proxy serverech webové aplikace.

**Otázka: Jak se vyřeší výstrahy stavu Azure AD Connect?**

Výstrahy stavu Azure AD Connect se vyřeší za podmínek úspěchu. Agenti stavu Azure AD Connect pravidelně zjišťují a hlásí podmínky úspěchu službě. U několika výstrah je potlačení založeno na čase. Jinými slovy, pokud stejný chybový stav není pozorován do 72 hodin od generování výstrahy, výstraha je automaticky vyřešena.

**Otázka: Dostávám upozornění, že "Test ověřovací požadavek (syntetická transakce) se nepodařilo získat token." Jak lze problém vyřešit?**

Azure AD Connect Health pro službu AD FS generuje tuto výstrahu, když agent aagent stavu nainstalovaný na serveru služby AD FS nezíská token jako součást syntetické transakce iniciované agentem stavu. Agent stavu používá kontext místního systému a pokusí se získat token pro sebepředávající stranu. Toto je catch-all test k zajištění, že AD FS je ve stavu vydávání tokenů.

Nejčastěji se tento test nezdaří, protože agent stavu není schopen přeložit název farmy služby AD FS. K tomu může dojít, pokud servery služby AD FS jsou za síťovými prostředky pro vyrovnávání zatížení a požadavek je iniciován z uzlu, který je za ekvizérem zatížení (na rozdíl od běžného klienta, který je před balancerem zatížení). To lze opravit aktualizací souboru "hosts" umístěného pod položkou "C:\Windows\System32\drivers\etc" tak, aby zahrnoval IP adresu serveru služby AD FS nebo adresu IP zpětné smyčky (127.0.0.1) pro název farmy služby AD FS (například sts.contoso.com). Přidání hostitelského souboru zkratuje volání sítě, což agentovi stavu umožní získat token.

**Otázka: Dostal jsem e-mail, který naznačuje, že mé stroje nejsou opraveny pro nedávné útoky ransomware. Proč jsem obdržel(a) tento e-mail?**

Služba Azure AD Connect Health naskenovala všechny počítače, které monitoruje, aby zajistila, že byly nainstalovány požadované opravy. E-mail byl odeslán správcům klienta, pokud alespoň jeden počítač neměl kritické opravy. Následující logika byla použita k tomuto určení.
1. Vyhledejte všechny opravy hotfix nainstalované v zařízení.
2. Zkontrolujte, zda je k dispozici alespoň jedna z oprav hotfix z definovaného seznamu.
3. Pokud ano, je zařízení chráněno. Pokud ne, stroj je ohrožen útokem.

K provedení této kontroly můžete použít následující skript prostředí PowerShell ručně. Implementuje výše uvedenou logiku.

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

**Otázka: Proč rutina prostředí PowerShell <i>Get-MsolDirSyncProvisioningError</i> zobrazuje méně chyb synchronizace ve výsledku?**

<i>Get-MsolDirSyncProvisioningError</i> vrátí pouze chyby zřizování DirSync. Kromě toho portál Connect Health také zobrazuje další typy chyb synchronizace, jako jsou chyby exportu. To je konzistentní s výsledkem delta Azure AD Connect. Přečtěte si další informace o [chybách synchronizace připojení Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors).

**Otázka: Proč nejsou generovány mé audity adfs?**

Použijte rutinu prostředí PowerShell <i>Get-AdfsProperties -AuditLevel,</i> abyste zajistili, že protokoly auditu nejsou v zakázaném stavu. Přečtěte si další informace o [protokolech auditu systému ADFS](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016). Všimněte si, pokud jsou na server ADFS posununa upřesňující nastavení auditu, budou všechny změny pomocí auditpol.exe přepsány (událost, pokud není generována aplikace). V takovém případě nastavte místní zásady zabezpečení pro protokolování selhání a úspěchu generovaného aplikací.

**Otázka: Kdy bude certifikát agenta automaticky obnoven před vypršením platnosti?**
Certifikace agenta bude automaticky obnovena **6 měsíců** před datem vypršení platnosti. Pokud není obnovena, ujistěte se, že síťové připojení agenta je stabilní. Problém může vyřešit také restartování služeb agenta nebo aktualizace na nejnovější verzi.


## <a name="related-links"></a>Související odkazy
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Instalace agenta služby Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Operace služby Azure AD Connect Health](how-to-connect-health-operations.md)
* [Používání služby Azure AD Connect Health se službou AD FS](how-to-connect-health-adfs.md)
* [Použití azure ad připojení stavu pro synchronizaci](how-to-connect-health-sync.md)
* [Používání služby Azure AD Connect Health se službou AD DS](how-to-connect-health-adds.md)
* [Historie verzí stavu azure a připojení](reference-connect-health-version-history.md)
