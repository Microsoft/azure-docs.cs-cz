---
title: Nejčastější dotazy k Azure Active Directory Connect Health – Azure | Microsoft Docs
description: Tato Nejčastější dotazy vám odpoví na dotazy týkající se Azure AD Connect Health. Jde o dotazy k používání služby, včetně modelu fakturace, schopností, omezení a podpory.
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
ms.openlocfilehash: 6185c5408f74b914ce5ad47634999786ba1d7ab6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367992"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Azure AD Connect Health nejčastějších dotazech
Tento článek obsahuje odpovědi na nejčastější dotazy týkající se služby Azure Active Directory (Azure AD) Connect Health. Tyto nejčastější dotazy obsahují otázky týkající se používání služby, včetně modelu fakturace, možností, omezení a podpory.

## <a name="general-questions"></a>Obecné otázky
**Otázka: můžu spravovat více adresářů služby Azure AD. Návody přepnout na tu, která má Azure Active Directory Premium?**

Pokud chcete přepínat mezi různými klienty Azure AD, vyberte aktuálně přihlášené **uživatelské jméno** v pravém horním rohu a pak zvolte příslušný účet. Pokud zde účet není uveden, vyberte možnost **Odhlásit** se a pak použijte pověření globálního správce adresáře, ve kterém se používá Azure Active Directory Premium (P1 nebo P2), se má povolit přihlášení.

**Otázka: jakou verzi rolí identity podporuje Azure AD Connect Health?**

V následující tabulce jsou uvedeny role a podporované verze operačních systémů.

|Role| Operační systém/verze|
|--|--|
|Active Directory Federation Services (AD FS)| <ul><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> <li> Windows Server 2019  </li> </ul>|
|Azure AD Connect | Verze 1.0.9125 nebo vyšší|
|AD DS (Active Directory Domain Services)| <ul><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> <li> Windows Server 2019  </li> </ul>|

Instalace jádra systému Windows Server nejsou podporovány.

Všimněte si, že funkce poskytované službou se můžou lišit v závislosti na roli a operačním systému. Jinými slovy, některé funkce nemusí být k dispozici pro všechny verze operačního systému. Podrobnosti najdete v popisech funkcí.

**Otázka: kolik licencí potřebuji k monitorování své infrastruktury?**

* První Agent pro stav připojení vyžaduje alespoň jednu licenci Azure AD Premium (P1 nebo P2).
* Každý další registrovaný agent vyžaduje 25 dalších licencí na Azure AD Premium (P1 nebo P2).
* Počet agentů je stejný jako celkový počet agentů, kteří jsou zaregistrovaní ve všech monitorovaných rolích (AD FS, Azure AD Connect nebo služba AD DS).
* Licencování AAD Connect Health nevyžaduje, abyste přiřadili licenci konkrétním uživatelům. Stačí, abyste měli požadovaný počet platných licencí.

Informace o licencování najdete taky na [stránce s cenami služby Azure AD](https://aka.ms/aadpricing).

Příklad:

| Registrovaní agenti | Potřebné licence | Příklad konfigurace monitorování |
| ------ | --------------- | --- |
| 1 | 1 | 1 Azure AD Connect Server |
| 2 | 26| 1 Azure AD Connect Server a 1 řadič domény |
| 3 | 51 | 1 Active Directory Federation Services (AD FS) (AD FS) Server, 1 AD FS proxy a 1 řadič domény |
| 4 | 76 | 1 AD FS Server, 1 AD FS proxy a 2 řadiče domény |
| 5 | 101 | 1 Azure AD Connect Server, 1 AD FS Server, 1 AD FS proxy a 2 řadiče domény |

**Otázka: Azure AD Connect Health podporuje Azure Německo Cloud?**

Azure AD Connect Health není v německém cloudu podporován s výjimkou [funkce hlášení chyb synchronizace](how-to-connect-health-sync.md#object-level-synchronization-error-report).

| Role | Funkce | Podporováno v německém cloudu |
| ------ | --------------- | --- |
| Připojit stav pro synchronizaci | Monitorování/Insight/výstrahy/analýza | No |
|  | Zpráva o chybách synchronizace | Yes |
| Connect Health pro AD FS | Monitorování/Insight/výstrahy/analýza | No |
| Připojit stav pro přidání | Monitorování/Insight/výstrahy/analýza | No |

Aby bylo zajištěno připojení agenta ke službě Connect Health pro synchronizaci, nakonfigurujte prosím odpovídající [požadavky na instalaci](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) .

## <a name="installation-questions"></a>Otázky k instalaci

**Otázka: Jaký je dopad instalace agenta Azure AD Connect Health na jednotlivé servery?**

Dopad instalace agenta Microsoft Azure AD Connect Health, AD FS, proxy serverů webových aplikací, Azure AD Connect (synchronizovaných), jsou řadiče domény minimální v souvislosti s PROCESORem, spotřebou paměti, šířkou pásma sítě a úložištěm.

Následující čísla představují aproximaci:

* Spotřeba procesoru: ~ 1-5% zvětšit.
* Spotřeba paměti: až 10% z celkové systémové paměti.

> [!NOTE]
> Pokud Agent nemůže komunikovat s Azure, agent uloží data místně pro stanovený maximální limit. Agent přepíše data uložená v mezipaměti v rámci "alespoň nedávno služby".
>
>

* Místní ukládání do vyrovnávací paměti pro agenty Azure AD Connect Health: ~ 20 MB.
* Pro AD FS servery doporučujeme zřídit místo na disku 1 024 MB (1 GB) pro AD FS kanál auditu, aby Azure AD Connect Health agenti zpracovali všechna data auditu předtím, než se přepíší.

**Otázka: bude nutné restartovat servery během instalace agentů Azure AD Connect Health?**

No. Instalace agentů nebude vyžadovat restartování serveru. Instalace některých nezbytných kroků ale může vyžadovat restartování serveru.

Například v systému Windows Server 2008 R2 vyžaduje instalace rozhraní .NET 4,5 restartování serveru.

**Otázka: provádí Azure AD Connect Health práci prostřednictvím předávacího proxy serveru HTTP?**

Ano. U probíhajících operací můžete nakonfigurovat agenta stavu tak, aby používal proxy server HTTP k přeposílání odchozích požadavků HTTP.
Přečtěte si další informace o [konfiguraci proxy serveru HTTP pro agenty stavu](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Pokud během registrace agenta potřebujete nakonfigurovat proxy server, může být nutné změnit nastavení proxy serveru aplikace Internet Explorer předem.

1. Otevřete Internet Explorer > **Nastavení**  >  **Možnosti Internetu**  >  **připojení**  >  **nastavení sítě LAN**.
2. Vyberte možnost **použít proxy server pro vaši síť LAN**.
3. Pokud máte různé proxy porty pro HTTP a HTTPS/zabezpečení, vyberte **Upřesnit** .

**Otázka: podporuje Azure AD Connect Health základní ověřování při připojování k proxy HTTP?**

No. Mechanismus pro zadání libovolného uživatelského jména a hesla pro základní ověřování není v současné době podporován.

**Otázka: jaké porty brány firewall potřebuji k tomu, aby mohl agent Azure AD Connect Health fungovat?**

Seznam portů brány firewall a dalších požadavků na připojení najdete v [části požadavky](how-to-connect-health-agent-install.md#requirements) .

**Otázka: Proč se na portálu Azure AD Connect Health Portal zobrazí dva servery se stejným názvem?**

Když odeberete agenta ze serveru, server se automaticky neodebere z portálu Azure AD Connect Health. Pokud ruční odebrání agenta ze serveru nebo odebrání samotného serveru, je nutné ručně odstranit položku serveru z portálu Azure AD Connect Health.

Můžete obnovit kopii serveru nebo vytvořit nový server se stejnými podrobnostmi (například název počítače). Pokud jste už registrovaný Server z Azure AD Connect Healthového portálu neodebrali a nainstalovali jste agenta na nový server, můžou se zobrazit dvě položky se stejným názvem.

V takovém případě ručně odstraňte položku, která patří do staršího serveru. Data pro tento server by měla být zastaralá.

**Otázka: mohu nainstalovat agenta Azure AD Connect Health v systému Windows Server Core?**

No.  Instalace na jádro serveru není podporovaná.

## <a name="health-agent-registration-and-data-freshness"></a>Registrace a aktuálnost dat agenta stavu

**Otázka: co jsou běžné důvody pro selhání registrace agenta stavu a jak řešit problémy?**

Registraci agenta stavu z následujících možných důvodů se může zdařit:

* Agent nemůže komunikovat s požadovanými koncovými body, protože brána firewall blokuje provoz. To je zvláště běžné na proxy serverech webových aplikací. Ujistěte se, že jste povolili odchozí komunikaci s požadovanými koncovými body a porty. Podrobnosti najdete v [části požadavky](how-to-connect-health-agent-install.md#requirements) .
* Odchozí komunikace se řídí kontrolou TLS v síťové vrstvě. To způsobí, že certifikát, který agent používá, bude nahrazen kontrolním serverem nebo entitou a kroky pro dokončení registrace agenta selžou.
* Uživatel nemá přístup k provedení registrace agenta. Globální správci mají ve výchozím nastavení přístup. K delegování přístupu jiným uživatelům můžete použít [řízení přístupu na základě role Azure (Azure RBAC)](how-to-connect-health-operations.md#manage-access-with-azure-rbac) .

**Otázka: zobrazuje se upozornění, že "Health Service data nejsou aktuální." Návody řešení problému?**

Azure AD Connect Health vygeneruje výstrahu, pokud neobdrží všechny datové body ze serveru za poslední dvě hodiny. [Další informace](how-to-connect-health-data-freshness.md).

## <a name="operations-questions"></a>Dotazy na operace
**Otázka: Potřebuji povolit auditování na proxy serverech webových aplikací?**

Ne, auditování není nutné povolit na proxy serverech webových aplikací.

**Otázka: jak se Výstrahy služby Azure AD Connect Health vyřešit?**

Výstrahy Azure AD Connect Health se vyřeší při splnění podmínky úspěchu. Agenti Azure AD Connect Health pro službu pravidelně zjišťují a nahlásily podmínky úspěchu. U několika výstrah je potlačení založené na čase. Jinými slovy, pokud se stejný chybový stav nepozoruje během 72 hodin od generování výstrahy, výstraha se automaticky vyřeší.

**Otázka: zobrazuje se upozornění, že "žádost o testovací ověření (syntetická transakce) nedokázala získat token." Návody řešení problému?**

Azure AD Connect Health pro AD FS vygeneruje toto upozornění, když Agent stavu nainstalovaný na serveru AD FS nepomůže získat token jako součást syntetické transakce iniciované agentem stavu. Agent stavu používá kontext místního systému a pokouší se získat token pro předávající stranu. Toto je test bez zachycení, který zajistí, že AD FS je ve stavu vydávání tokenů.

Většinou se tento test nezdařil, protože Agent stavu nemůže přeložit název AD FS farmy. K tomu může dojít, pokud AD FS servery jsou za nástroji pro vyrovnávání zatížení sítě a žádost se iniciuje z uzlu, který je za nástrojem pro vyrovnávání zatížení (na rozdíl od normálního klienta, který se nachází před nástrojem pro vyrovnávání zatížení). To se dá opravit tak, že aktualizujete soubor Hosts umístěný v části "C:\Windows\System32\drivers\etc", aby zahrnoval IP adresu AD FSho serveru nebo IP adresu zpětné smyčky (127.0.0.1) pro název AD FS farmy (například sts.contoso.com). Přidáním hostitelského souboru dojde k krátkému okruhu síťového volání, čímž umožníte agentovi stavu získat token.

**Otázka: Mám e-mail s oznámením, že počítače nejsou opraveny pro nedávné útoky ransomwarem. Proč byl tento e-mail doručen?**

Služba Azure AD Connect Health kontrolovala všechny počítače, které monitoruje, aby se zajistilo, že se nainstalovaly požadované opravy. E-mail se poslal správcům tenanta, pokud aspoň jeden počítač neměl důležité opravy. K provedení tohoto určení se použila následující logika.
1. Vyhledá všechny opravy hotfix nainstalované v počítači.
2. Ověřte, zda je k dispozici alespoň jedna z oprav HotFix z definovaného seznamu.
3. Pokud ano, počítač je chráněný. V takovém případě je počítač ohrožený útokem.

Tuto kontrolu můžete provést ručně pomocí následujícího skriptu prostředí PowerShell. Implementuje výše uvedenou logiku.

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

**Otázka: Proč rutina PowerShellu <i>Get-MsolDirSyncProvisioningError</i> zobrazuje méně chyb synchronizace ve výsledku?**

<i>Get-MsolDirSyncProvisioningError</i> bude vracet jenom chyby zřizování DirSync. Kromě toho se na portálu Connect Health zobrazují také další typy chyb synchronizace, jako jsou například chyby exportu. To je konzistentní s Azure AD Connectm rozdílovým výsledkem. Přečtěte si další informace o [Azure AD Connect chybách synchronizace](./tshoot-connect-sync-errors.md).

**Otázka: Proč nejsou generovány moje audity služby AD FS?**

Ověřte prosím pomocí rutiny PowerShellu <i>Get-AdfsProperties-AuditLevel</i> , jestli protokoly auditu nejsou v zakázaném stavu. Přečtěte si další informace o [protokolech auditu služby ADFS](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016). Všimněte si, že v případě, že jsou na server ADFS vložená Pokročilá nastavení auditu, budou všechny změny v auditpol.exe přepsány (událost, pokud aplikace vygenerovaná není). V takovém případě prosím nastavte místní zásady zabezpečení na protokolování chyb generovaných aplikací a úspěchu.

**Otázka: kdy bude certifikát agenta pro vypršení platnosti automaticky obnoven?**
Certifikát agenta se automaticky obnoví na **6 měsíců** před datem vypršení platnosti. Pokud se neobnoví, ujistěte se, že je síťové připojení agenta stabilní. Problém můžete vyřešit i tak, že restartujete služby agenta nebo aktualizujete na nejnovější verzi.



## <a name="related-links"></a>Související odkazy
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalace agenta služby Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health operace](how-to-connect-health-operations.md)
* [Používání služby Azure AD Connect Health se službou AD FS](how-to-connect-health-adfs.md)
* [Použití Azure AD Connect Health k synchronizaci](how-to-connect-health-sync.md)
* [Používání služby Azure AD Connect Health se službou AD DS](how-to-connect-health-adds.md)
* [Historie verzí služby Azure AD Connect Health](reference-connect-health-version-history.md)
