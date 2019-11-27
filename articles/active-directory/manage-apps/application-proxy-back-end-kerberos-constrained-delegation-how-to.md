---
title: Řešení potíží s vynuceným delegováním protokolu Kerberos – proxy aplikací
description: Řešení potíží s konfigurace omezeného delegování protokolu Kerberos pro Proxy aplikací
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5e866f61409960447e17ecb50b035eabd53dc38
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275684"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Řešení potíží s vynuceným použitím protokolu Kerberos konfigurace delegování pro Proxy aplikací

Metody dostupné pro dosažení jednotného přihlašování k publikovaným aplikacím se může lišit od jedné aplikace do jiné. Jednou z možností, které nabízí Proxy aplikací Azure Active Directory (Azure AD) ve výchozím nastavení je omezené delegování protokolu Kerberos (KCD). Můžete nakonfigurovat konektor, pro vaše uživatele, ke spuštění omezené ověřování protokolu Kerberos na back endové aplikace.

Postup povolení KCD je jednoduché. Vyžaduje více než obecné principy systému různými komponentami a tok ověřování, která podporuje jednotné přihlašování. Ale v některých případech KCD jednotného přihlašování nefunguje podle očekávání. Je třeba dobré zdroje informací k řešení scénářů.

Tento článek obsahuje jediný bod odkaz, který pomáhá při řešení potíží a sami napravit některé z nejběžnějších problémů. Věnuje se také diagnostiku složitějších problémů provádění.

Tento článek neposkytuje následující předpoklady:

- Nasazení Azure Proxy aplikací služby AD v rámci Začínáme [s proxy aplikací](application-proxy-add-on-premises-application.md) a obecný přístup k aplikacím, které KCD, fungují podle očekávání.
- Publikované cílové aplikace je založena na Internetové informační služby (IIS) a Microsoft implementace protokolu Kerberos.
- V jedné doméně Azure Active Directory se nachází hostitelé serveru a aplikace. Podrobné informace o scénářích mezi doménami a doménovými strukturami najdete v [dokumentu White Paper KCD](https://aka.ms/KCDPaper).
- Příslušná aplikace, se publikují v Azure pomocí předběžného ověřování povoleno. Uživatelé se očekává, že ověřování do Azure prostřednictvím ověřování pomocí formulářů. Scénáře ověřování klienta bohaté nejsou uvedené v tomto článku. Může být přidají v určitém okamžiku v budoucnu.

## <a name="prerequisites"></a>Požadavky

Proxy aplikací Azure AD je možné nasadit do mnoho typů prostředí nebo infrastruktury. Architektury lišit mezi organizacemi. Nejběžnější příčiny problémů souvisejících se KCD nejsou v prostředí. Jednoduché nesprávné konfigurace nebo obecné chyby způsobit, že většinu problémů.

Z tohoto důvodu se před zahájením odstraňování potíží ujistěte, že jste splnili všechny předpoklady pro [použití jednotného přihlašování s proxy aplikací KCD](application-proxy-configure-single-sign-on-with-kcd.md) . Všimněte si části týkající se konfigurace omezeného delegování protokolu Kerberos na 2012 R2. Tento proces používá jiný přístup ke konfiguraci KCD na předchozích verzích systému Windows. Také mějte na paměti tyto aspekty:

- Není, členský server domény a otevřete dialogové okno zabezpečený kanál s řadičem konkrétní domény (DC). Server může přesuňte do jiného dialogového okna v daném okamžiku. Konektor hostitelé nejsou tak s omezeným přístupem ke komunikaci s místní lokalita pouze konkrétní řadiče domény.
- Scénáře napříč doménami spoléhat na referenční seznamy sledování, které budou řídit konektor hostitele do řadiče domény, které můžou být mimo hranice místní sítě. V těchto případech je stejně důležité také směrovat provoz dále do řadiče domény, které představují další příslušné domény. V opačném případě delegování se nezdaří.
- Kde je to možné, vyhněte se uvedení aktivních IP adres nebo ID zařízení mezi hostiteli v konektoru a řadiče domény. Tato zařízení jsou někdy příliš rušivá a jsou v konfliktu s jádrem provozu RPC.

Otestujte delegování v jednoduché scénáře. Zavést další proměnné, tím víc bude pravděpodobně nutné potýkat s. Chcete-li ušetřit čas, omezte testování na jeden konektor. Poté, co byl problém vyřešen, přidejte další konektory.

Některé faktorech prostředí může také přispět k problému. Aby se zabránilo tyto faktory, minimalizujte architektura co nejvíc během testování. Například interní firewall nesprávně nakonfigurované seznamy ACL jsou běžné. Pokud je to možné odesílat veškerý přenos z konektoru přímo na řadiče domény a back endové aplikace.

Nejlepším místem pro umístění konektory je co nejblíže k svých cílů. Brána firewall, který je umístěný vložené při testování přidá zbytečné složitosti a může prodloužit vaše šetření.

Co se zobrazí KCD problém? Existuje několik běžných označení, které se nedaří KCD jednotného přihlašování. První příznaky problému se zobrazí v prohlížeči.

![Příklad: nesprávná chyba konfigurace KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Příklad: autorizace se nezdařila z důvodu chybějících oprávnění.](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Obě tyto Image zobrazit stejný příznak: selhání jednotného přihlašování. Byl odepřen přístup uživatelů k aplikaci.

## <a name="troubleshooting"></a>Poradce při potížích

Jak je řešit, závisí na problému a příznaky, které můžete sledovat. Než budete pokračovat žádné vydrží déle, prozkoumejte v následujících článcích. Poskytují užitečné informace o odstraňování potíží:

- [Řešení problémů s proxy aplikací a chybové zprávy](application-proxy-troubleshoot.md)
- [Chyby a příznaky protokolu Kerberos](application-proxy-troubleshoot.md#kerberos-errors)
- [Práce s jednotným přihlašováním, když jsou místní a cloudové identity stejné](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Pokud jste se dostali do této chvíle, hlavní problém se neexistuje. Pokud chcete začít, oddělte toku do následující tři fáze, které je možné řešit.

### <a name="client-pre-authentication"></a>Předběžné ověření klienta

Externí ověřování uživatele do Azure prostřednictvím prohlížeče. Schopnost předběžného ověřování v Azure je nezbytné pro jednotné přihlašování KCD funkce. Test a adresa tuto možnost, pokud existují nějaké problémy. Fáze předběžného ověřování nesouvisí s KCD nebo publikovanou aplikaci. Je snadné, chcete-li opravit nedostatky kontrolou správností, které subjektu účet existuje v Azure. Také zkontrolujte, jestli není zakázaný, nebo blokovaný. Odpovědi na chybu v prohlížeči je dostatečně výstižný, který vysvětluje příčinu. Pokud si nejste jisti, zkontrolujte další řešení problémů s Microsoft články k ověření.

### <a name="delegation-service"></a>Delegování služby

Konektor Proxy služby Azure, který získá lístek služby protokolu Kerberos pro uživatele z protokolu Kerberos Key distribuce Center (KCD).

Externí komunikace mezi klientem a front-endu Azure na KCD mít žádný vliv. Tyto komunikace pouze ujistěte se, že KCD funguje. Proxy služby Azure service se poskytuje platné ID uživatele, který slouží k získání lístku protokolu Kerberos. KCD bez ID, není možné a selže.

Jak už bylo zmíněno dříve, chybové zprávy prohlížeče poskytuje některé dobré příčiny o proč věci selže. Nezapomeňte si poznamenejte si ID aktivity a časové razítko v odpovědi. Tyto informace vám korelaci chování na skutečné události v protokolu událostí Proxy služby Azure.

![Příklad: nesprávná chyba konfigurace KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Odpovídající položky v protokolu událostí se zobrazí jako události 13019 nebo 12027. Vyhledejte protokoly událostí konektoru v **protokolech aplikací a služeb** &gt; &gt; **správce** **konektoru** **Microsoft** &gt; **AadApplicationProxy** &gt;.

![Událost 13019 z protokolu událostí Proxy aplikací](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![12027 událostí z protokolu událostí Proxy aplikací](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. V interní službě DNS použijte pro adresu aplikace záznam **a** , nikoli **záznam CNAME**.
1. Znovu potvrdit, že konektor hostitele bylo uděleno právo pro delegování do určeného cílového účtu hlavní název služby. Znovu potvrďte, že je vybraná **možnost použít libovolný protokol ověřování** . Další informace najdete v článku věnovaném [konfiguraci jednotného přihlašování](application-proxy-configure-single-sign-on-with-kcd.md).
1. Ověřte, zda existuje pouze jedna instance hlavního názvu služby v rámci Azure AD. Vystavení `setspn -x` z příkazového řádku na jakémkoli hostiteli člena domény.
1. Ověřte, že je vynutila zásada domény, která omezuje [maximální velikost vydaných tokenů protokolu Kerberos](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/). Tato zásada zastaví konektoru získat token, pokud je nalezen vysoké.

Trasování sítě, který zachycuje výměn mezi hostitelem konektoru a domény služby KDC je nejlepší dál chcete získat další podrobnosti nižší úrovně na problémy. Další informace najdete na dokumentu věnovaném [řešení potíží s hloubkou podrobně](https://aka.ms/proxytshootpaper).

Vytváření tiketů vypadá v pořádku, uvidíte události v protokolech s informacemi o tom, že ověřování se nezdařilo, protože aplikace vrátila zobrazuje chyba 401. Tato událost znamená, že je cílová aplikace odmítli lístku. Přejděte do další fáze.

### <a name="target-application"></a>Cílové aplikace

Příjemce poskytnuté konektoru lístek služby Kerberos. V této fázi očekávat, že konektor pro odeslání lístků služby Kerberos k back-endu. Tento lístek se hlavička v první žádost o aplikaci.

1. Pomocí aplikace interní adresa URL definované na portálu, ověřte, že aplikace je dostupná přímo z prohlížeče na hostiteli konektoru. Potom můžete přihlásit úspěšně. Podrobnosti najdete na stránce **Poradce při potížích s** konektorem.
1. Stále na hostiteli konektor potvrďte, že ověřování mezi prohlížečem a aplikace používá protokol Kerberos. Proveďte jednu z následujících akcí:
1. Spusťte DevTools (**F12**) v aplikaci Internet Explorer nebo použijte [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) z hostitele konektoru. Přejděte do aplikace pomocí interní adresy URL. Kontrola vrácené v nabízený autorizační hlavičky WWW odpovědi z aplikace, abyste měli jistotu, že buď negotiate, nebo pomocí protokolu Kerberos je k dispozici.

   - Další objekt BLOB Kerberos, který se vrátí v odpovědi z prohlížeče do aplikace, začíná na **YII**. Těmto písmenům zjistit, zda je spuštěna protokolu Kerberos. Microsoft NT LAN Manager (NTLM) na druhé straně vždy začíná **TlRMTVNTUAAB**, což při dekódování protokolu NTLM Security Support Provider (NTLMSSP) čte z base64. Pokud vidíte **TlRMTVNTUAAB** na začátku objektu blob, protokol Kerberos není k dispozici. Pokud nevidíte **TlRMTVNTUAAB**, je pravděpodobně k dispozici protokol Kerberos.

      > [!NOTE]
      > Pokud používáte aplikaci Fiddler, tato metoda vyžaduje, je dočasně zakázat rozšířené ochrany na konfiguraci aplikace ve službě IIS.

      ![Okno kontroly sítě prohlížeče](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - Objekt blob na tomto obrázku nezačíná na **TIRMTVNTUAAB**. V tomto příkladu je k dispozici protokol Kerberos a objekt BLOB protokolu Kerberos nezačíná na **YII**.

1. NTLM dočasně odeberte ze seznamu zprostředkovatelů na web služby IIS. Přístup k aplikaci přímo z aplikace Internet Explorer v hostiteli konektoru. NTLM je již v seznamu poskytovatelů. Aplikace můžete přistupovat pouze pomocí protokolu Kerberos. Pokud přístup selže, může být problém s konfigurací aplikace. Ověřování protokolem Kerberos nebude fungovat správně.

   - Pokud není k dispozici protokol Kerberos, zkontrolujte nastavení ověřování aplikace ve službě IIS. Ujistěte se, že je seznam **Negotiate** uveden v horní části, s protokolem NTLM hned pod ním. Pokud se zobrazí **Nedohadované**, **Kerberos nebo Negotiate**nebo **PKU2U**, pokračujte pouze v případě, že je Kerberos funkční.

     ![Zprostředkovatelé ověřování Windows](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - Pomocí protokolů Kerberos a NTLM v místě dočasně zakážete předběžné ověřování pro aplikaci na portálu. Došlo k pokusu o přístup z Internetu pomocí externí adresu URL. Jste vyzváni k ověření. Budete moct použít stejný účet použitý v předchozím kroku. V opačném případě dojde k nějakému problému s back endové aplikace, ne KCD.
   - Znovu povolte předběžné ověření na portálu. Ověřování prostřednictvím Azure pokouší připojit k aplikaci prostřednictvím jeho externí adresu URL. Jednotné přihlašování se nezdaří, zobrazí zprávu chyby zakázáno v prohlížeči a 13022 událost v protokolu:

     *Microsoft AAD Application proxy Connector nemůže ověřit uživatele, protože back-end Server reaguje na pokusy o ověření pomocí protokolu Kerberos pomocí chyby HTTP 401.*

      ![Zobrazuje chybu HTTTP 401 Forbidden](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - Zkontrolujte aplikace služby IIS. Ujistěte se, že se fond aplikací nakonfigurovaný a hlavní název služby umožňují použít stejný účet ve službě Azure AD. Přejděte ve službě IIS, jak je znázorněno na následujícím obrázku:

      ![Okno Konfigurace aplikace IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      Až budete vědět, identitu, ujistěte se, že tento účet je nakonfigurovaný pomocí hlavního názvu služby dotyčný. Příklad: `setspn –q http/spn.wacketywack.com`. V příkazovém řádku zadejte následující text:

      ![Zobrazuje příkazové okno SetSPN.](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Zkontrolujte název SPN definované pro aplikace, nastavení na portálu. Ujistěte se, že fond aplikací aplikace používají stejný hlavní název služby nakonfigurovaný na cíli účtu Azure AD.

      ![Konfigurace hlavního názvu služby na webu Azure Portal](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - Přejít do služby IIS a vybrat možnost **editoru konfigurace** pro aplikaci. Přejděte na **System. webServer/security/Authentication/windowsAuthentication**. Ujistěte se, že hodnota **UseAppPoolCredentials** je **true**.

      ![Fondy aplikací IIS konfigurace přihlašovacích údajů – možnost](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Změňte tuto hodnotu na **true**. Odeberte všechny lístky protokolu Kerberos v mezipaměti z back endového serveru spuštěním následujícího příkazu:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

Další informace najdete v tématu [vyprázdnění mezipaměti lístků klienta protokolu Kerberos pro všechny relace](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).

Ponecháte-li povolen režim jádra, zlepšuje výkon operací protokolu Kerberos. Ale také způsobí-the-ticket pro požadovanou službu k dešifrování pomocí účtu počítače. Tento účet se také nazývá místní systém. Nastavte tuto hodnotu na **true** , pokud chcete přerušit KCD při hostování aplikace na více než jednom serveru ve farmě.

- Při další kontrole zakažte také **rozšířenou** ochranu. V některých scénářích je **Rozšířená** ochrana podařilo přerušit KCD, když byla povolená v konkrétních konfiguracích. V těchto případech se aplikace publikované jako podsložku výchozího webu. Tato aplikace je nakonfigurována pro jenom anonymní ověřování. Všechna dialogová okna jsou zobrazena šedě, takže je možné, že by všechny aktivní nastavení zdědí podřízené objekty. Doporučujeme, abyste provedli test, ale nezapomněli jste tuto hodnotu obnovit, **Pokud je to**možné.

  Tato další kontrola vám dává na cestě k publikované aplikaci používat. Můžete aktivovat další konektory, které jsou také nakonfigurovány pro delegování. Další informace najdete podrobnější technický návod, který vám ukáže [řešení potíží s proxy aplikací služby AD Azure](https://aka.ms/proxytshootpaper).

Pokud stále nemůžete provádět průběh, může vám pomůže podporu Microsoftu. Vytvoření lístku podpory přímo v rámci portálu. Vás bude kontaktovat pracovník.

## <a name="other-scenarios"></a>Další scénáře

- Proxy aplikací Azure vyžádá lístek protokolu Kerberos před odesláním jeho žádost do aplikace. Některé aplikace od jiných výrobců nezpůsobují tuto metodu ověřování. Tyto aplikace můžete očekávat konvenčnější jednání uskutečnit. První požadavek je anonymní, což umožňuje, aby aplikace reagovat s typy ověřování, podporuje prostřednictvím zobrazuje chyba 401.
- Ověřování s více segmenty směrování se běžně používá ve scénářích kde vrstvenou aplikaci s front-endu a back-endu kde vyžadují ověřování, jako je SQL Server Reporting Services. Pokud chcete nakonfigurovat scénář s více segmenty směrování, přečtěte si článek podpora [omezeného delegování protokolu Kerberos ve scénářích s více segmenty směrování může vyžadovat přechod protokolu](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>Další kroky

[NAKONFIGURUJTE KCD ve spravované doméně](../../active-directory-domain-services/deploy-kcd.md).
