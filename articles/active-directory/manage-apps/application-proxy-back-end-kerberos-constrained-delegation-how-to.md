---
title: Poradce při potížích s delegováním omezeného protokolu Kerberos – proxy aplikace
description: Poradce při potížích s konfiguracemi delegování s omezeným zabezpečením protokolu Kerberos pro proxy aplikace
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275684"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Poradce při potížích s konfiguracemi delegování protokolu Kerberos pro proxy aplikace

Metody, které jsou k dispozici pro dosažení sponesení a publikování aplikací se může lišit od jedné aplikace do druhé. Jednou z možností, které Azure Active Directory (Azure AD) Proxy aplikace nabízí ve výchozím nastavení je Kerberos omezené delegování (KCD). Pro uživatele můžete nakonfigurovat konektor pro spouštění omezeného ověřování protokolem Kerberos do back-endových aplikací.

Postup pro povolení KCD je jednoduchý. Nevyžaduje více než obecné pochopení různých součástí a tok ověřování, které podporují sso. Ale někdy, KCD SSO nefunguje podle očekávání. K řešení těchto scénářů potřebujete dobré zdroje informací.

Tento článek obsahuje jediný referenční bod, který pomáhá řešit a samonapravit některé nejběžnější problémy. Zahrnuje také diagnostiku složitějších problémů s implementací.

Tento článek umožňuje následující předpoklady:

- Nasazení proxy aplikace Azure AD na [Začínáme s proxy aplikací](application-proxy-add-on-premises-application.md) a obecný přístup k aplikacím bez KCD fungují podle očekávání.
- Publikovaná cílová aplikace je založena na Internetové informační službě (IIS) a implementaci protokolu Kerberos společností Microsoft.
- Hostitelé serveru a aplikace jsou umístěni v jedné doméně služby Azure Active Directory. Podrobné informace o scénářích mezi doménami a doménovými strukturami naleznete v [dokumentu white paper kcd](https://aka.ms/KCDPaper).
- Aplikace předmětu se publikuje v tenantovi Azure s povoleným předběžným ověřováním. Očekává se, že uživatelé se ověřují v Azure pomocí ověřování založeného na formulářích. Scénáři ověřování rozšířeného klienta se tento článek nevztahuje. Mohly by být přidány v určitém okamžiku v budoucnu.

## <a name="prerequisites"></a>Požadavky

Proxy aplikace Azure AD lze nasadit do mnoha typů infrastruktur nebo prostředí. Architektury se liší od organizace k organizaci. Nejčastější příčiny problémů souvisejících s KCD nejsou prostředí. Jednoduché chybné konfigurace nebo obecné chyby způsobují většinu problémů.

Z tohoto důvodu je nejlepší se ujistit, že jste splnili všechny požadavky v [použití kcd přizpůsobování s pojem](application-proxy-configure-single-sign-on-with-kcd.md) s proxy aplikací před zahájením řešení potíží. Poznámka: část o konfiguraci kerberos omezené delegování na 2012R2. Tento proces využívá jiný přístup ke konfiguraci KCD v předchozích verzích systému Windows. Také mějte na paměti tyto úvahy:

- Není neobvyklé, že členský server domény otevře dialogové okno zabezpečeného kanálu s určitým řadičem domény (DC). Pak server může přesunout do jiného dialogu v daném okamžiku. Hostitelé konektorů tedy nejsou omezeni na komunikaci pouze s konkrétními místními řadiči domény webu.
- Scénáře mezi doménami spoléhají na odkazy, které směrují hostitele konektoru na řadiče domény, které mohou být mimo obvod místní sítě. V těchto případech je stejně důležité také odesílat provoz dále do řadičů domény, které představují jiné příslušné domény. Pokud ne, delegování se nezdaří.
- Pokud je to možné, neumisťujte aktivní zařízení IPS nebo IDS mezi hostitele konektorů a řadiče domény. Tato zařízení jsou někdy příliš rušivé a narušují hlavní provoz vzdáleného volání procedur.

Testovací delegování v jednoduchých scénářích. Čím více proměnných představíte, tím více budete muset potýkat s. Chcete-li ušetřit čas, omezte testování na jeden konektor. Po vyřešení problému přidejte další konektory.

K problému mohou přispět i některé faktory životního prostředí. Chcete-li se vyhnout tyto faktory, minimalizovat architekturu co nejvíce během testování. Například nesprávně nakonfigurované interní brány firewall seznamy ACJsou běžné. Pokud je to možné, odešlete veškerý provoz z konektoru přímo do řadičů domény a back-end aplikace.

Nejlepší místo pro umístění konektorů je co nejblíže k jejich cílům. Brána firewall, která je při testování vestavěná, zvyšuje zbytečnou složitost a může prodloužit vaše vyšetřování.

Co ukazuje problém KCD? Existuje několik běžných indikací, že kcd sso selhává. První příznaky problému se zobrazí v prohlížeči.

![Příklad: Nesprávná chyba konfigurace KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Příklad: Autorizace se nezdařila z důvodu chybějících oprávnění.](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Oba tyto obrázky ukazují stejný příznak: selhání přisuzušky. Přístup uživatele k aplikaci byl odepřen.

## <a name="troubleshooting"></a>Řešení potíží

Způsob řešení potíží závisí na problému a příznaky, které pozorujete. Než půjdete dál, prozkoumejte následující články. Poskytují užitečné informace o řešení potíží:

- [Poradce při potížích s proxy aplikacemi a chybové zprávy](application-proxy-troubleshoot.md)
- [Chyby a příznaky protokolu Kerberos](application-proxy-troubleshoot.md#kerberos-errors)
- [Práce s přiřazuje k místním u místně a cloudové identity nejsou identické](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Pokud jste se dostali do tohoto bodu, pak váš hlavní problém existuje. Chcete-li začít, oddělit tok do následujících tří fází, které můžete řešit.

### <a name="client-pre-authentication"></a>Předběžné ověření klienta

Externí uživatel ověřování azure prostřednictvím prohlížeče. Možnost předběžného ověření do Azure je nezbytná pro fungování služby ACD. Test a řešení této schopnosti, pokud existují nějaké problémy. Fáze předběžného ověření nesouvisí s diskem KCD nebo publikovanou aplikací. Je snadné opravit všechny nesrovnalosti tím, že zkontroluje, zda účet předmětu existuje v Azure. Zkontrolujte také, zda není zakázána nebo blokována. Odpověď na chybu v prohlížeči je dostatečně popisná, aby vysvětlila příčinu. Pokud si nejste jisti, ověřte další články společnosti Microsoft o řešení potíží.

### <a name="delegation-service"></a>Delegování

Konektor proxy Azure, který získá lístek služby Kerberos pro uživatele z Centra distribuce klíčů protokolu Kerberos (KCD).

Externí komunikace mezi klientem a front-endazure nemají žádný vliv na KCD. Tato komunikace pouze ujistěte se, že KCD funguje. Služba Azure Proxy poskytuje platné ID uživatele, které se používá k získání lístku protokolu Kerberos. Bez tohoto ID, KCD není možné a selže.

Jak již bylo zmíněno dříve, chybové zprávy prohlížeče poskytují některé dobré vodítko o tom, proč věci selžou. Nezapomeňte si v odpovědi poznamenat ID aktivity a časové razítko. Tyto informace vám pomohou korelovat chování skutečné události v protokolu událostí Azure Proxy.

![Příklad: Nesprávná chyba konfigurace KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Odpovídající položky v protokolu událostí zobrazit jako události 13019 nebo 12027. Vyhledejte protokoly událostí konektoru v **protokolech** &gt; aplikací a služeb **Microsoft** &gt; **AadApplicationProxy** &gt; **Connector** &gt; **Admin**.

![Událost 13019 z protokolu událostí proxy aplikace](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![Událost 12027 z protokolu událostí proxy aplikace](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. Použijte záznam **A** v interním DNS pro adresu aplikace, nikoli **pro název CName**.
1. Znovu potvrďte, že hostiteli konektoru bylo uděleno právo delegovat na název spn určeného cílového účtu. Znovu potvrďte, že je vybrán **libovolný ověřovací protokol.** Další informace naleznete v [článku konfigurace přisávací služby](application-proxy-configure-single-sign-on-with-kcd.md).
1. Ověřte, že existuje pouze jedna instance hlavního název služby v existenci ve službě Azure AD. Problém `setspn -x` z příkazového řádku na libovolném hostiteli člena domény.
1. Zkontrolujte, zda je vynucena zásada domény, která omezuje [maximální velikost vydaných tokenů protokolu Kerberos](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/). Tato zásada zastaví konektor z získání tokenu, pokud je zjištěno, že je nadměrné.

Trasování sítě, která zachycuje výměny mezi hostitelem konektoru a doménou KDC je dalším nejlepším krokem k získání další podrobnosti nižší úrovně o problémech. Další informace naleznete v [podrobném podrobném tématu Poradce při potížích .](https://aka.ms/proxytshootpaper)

Pokud prodej lístků vypadá dobře, zobrazí se událost v protokolech oznamující, že ověřování se nezdařilo, protože aplikace vrátila 401. Tato událost označuje, že cílová aplikace odmítla lístek. Přejděte do další fáze.

### <a name="target-application"></a>Cílová aplikace

Příjemce lístku Kerberos poskytované konektoru. V této fázi očekávejte, že konektor odeslal lístek služby Kerberos do back-endu. Tento lístek je záhlaví v první žádosti o aplikaci.

1. Pomocí interní adresy URL aplikace definované na portálu ověřte, zda je aplikace přístupná přímo z prohlížeče na hostiteli konektoru. Poté se můžete úspěšně přihlásit. Podrobnosti naleznete na stránce **Poradce při potížích s konektorem.**
1. Stále na hostiteli konektoru, zkontrolujte, zda ověřování mezi prohlížečem a aplikací používá protokol Kerberos. Proveďte jednu z následujících akcí:
1. Spusťte DevTools **(F12**) v aplikaci Internet Explorer nebo použijte [Šumař](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) z hostitele konektoru. Přejděte do aplikace pomocí interní adresy URL. Zkontrolujte nabízené hlavičky autorizace WWW vrácené v odpovědi z aplikace a ujistěte se, že je k dispozici vyjednávání nebo Kerberos.

   - Další objekt blob protokolu Kerberos, který je vrácen v odpovědi z prohlížeče do aplikace, začíná na **YII**. Tyhle dopisy říkají, že Kerberos běží. Microsoft NT LAN Manager (NTLM), na druhé straně, vždy začíná **TlRMTVNTUAAB**, který čte NTLM Zprostředkovatel podpory zabezpečení (NTLMSSP) při dekódování z Base64. Pokud vidíte **TlRMTVNTUAAB** na začátku objektu blob, Kerberos není k dispozici. Pokud **tlRMTVNTUAAB**nevidíte , kerberos je pravděpodobně k dispozici.

      > [!NOTE]
      > Pokud používáte Šumař, tato metoda vyžaduje dočasné zakázání rozšířené ochrany v konfiguraci aplikace ve službě IIS.

      ![Okno kontroly sítě prohlížeče](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - Blob v tomto obrázku nezačíná **TIRMTVNTUAAB**. Takže v tomto příkladu Kerberos je k dispozici a objekt blob protokolu Kerberos nezačíná **yii**.

1. Dočasně odeberte ntlm ze seznamu zprostředkovatelů na webu iis. Přístup k aplikaci přímo z Aplikace Internet Explorer na hostiteli konektoru. NTLM již není v seznamu zprostředkovatelů. K aplikaci můžete přistupovat pouze pomocí protokolu Kerberos. Pokud se přístup nezdaří, může být problém s konfigurací aplikace. Ověřování protokolem Kerberos nefunguje.

   - Pokud protokol Kerberos není k dispozici, zkontrolujte nastavení ověřování aplikace ve službách IIS. Ujistěte se, **že negotiate** je uveden v horní části, s NTLM těsně pod ním. Pokud se zobrazí **nevyjednávat**, **Kerberos nebo Negotiate**nebo **PKU2U**, pokračujte pouze v případě, že je protokol Kerberos funkční.

     ![Zprostředkovatelé ověřování systému Windows](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - S kerberos a NTLM na místě, dočasně zakázat předběžné ověřování pro aplikaci na portálu. Pokuste se k němu přistupovat z internetu pomocí externí adresy URL. Budete vyzváni k ověření. Můžete tak učinit se stejným účtem, který byl použit v předchozím kroku. Pokud ne, je tu problém s back-end aplikace, ne KCD.
   - Znovu povolte předběžné ověření na portálu. Ověřte prostřednictvím Azure pokusem o připojení k aplikaci prostřednictvím externí adresy URL. Pokud se nezdaří spřihlašování, zobrazí se v prohlížeči a událost 13022 v protokolu:

     *Konektor proxy aplikací Microsoft AAD nemůže ověřit uživatele, protože server back-end reaguje na pokusy o ověření protokolem Kerberos s chybou HTTP 401.*

      ![Zobrazuje zakázanou chybu HTTTP 401.](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - Zkontrolujte aplikaci služby IIS. Ujistěte se, že nakonfigurovaný fond aplikací a hlavní název služby jsou nakonfigurované tak, aby používaly stejný účet ve službě Azure AD. Přejděte ve správě IIS, jak je znázorněno na následujícím obrázku:

      ![Okno konfigurace aplikace služby IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      Po znáte identitu, ujistěte se, že tento účet je nakonfigurován s hlavní hod. Příklad: `setspn –q http/spn.wacketywack.com`. Do příkazového řádku zadejte následující text:

      ![Zobrazuje příkazové okno SetSPN.](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Zkontrolujte název SPN definované proti nastavení aplikace na portálu. Ujistěte se, že stejný název SLUŽBY ZABEZPEČENÍ nakonfigurovaný proti cílovému účtu Azure AD používá fond aplikací.

      ![Konfigurace hlavního virtuálního počítače na webu Azure Portal](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - Přejděte do služby IIS a vyberte možnost **Editor konfigurace** pro aplikaci. Přejděte na **soubor system.webServer/security/authentication/windowsAuthentication**. Ujistěte se, že hodnota **UseAppPoolCredentials** je **True**.

      ![Možnost pověření konfiguračníaplikace služby IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Změňte tuto hodnotu na **Hodnotu True**. Odeberte všechny lístky protokolu Kerberos uložené v mezipaměti ze serveru back-end spuštěním následujícího příkazu:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

Další informace naleznete [v tématu Vymazání mezipaměti lístku klienta Kerberos pro všechny relace](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).

Pokud ponecháte režim jádra zapnutý, zlepší se výkon operací protokolu Kerberos. Ale také způsobí, že lístek pro požadovanou službu, které mají být dešifrovány pomocí účtu počítače. Tento účet se také nazývá místní systém. Nastavte tuto hodnotu na **True** rozbít KCD při aplikace je hostována přes více než jeden server ve farmě.

- Jako další kontrolu, zakázat **rozšířené** ochrany příliš. V některých případech **rozšířená** ochrana porušila KCD, když byla povolena v konkrétních konfiguracích. V těchto případech byla aplikace publikována jako podsložka výchozího webu. Tato aplikace je konfigurována pouze pro anonymní ověřování. Všechna dialogová okna jsou zobrazena šedě, což naznačuje, že podřízené objekty nezdědí žádné aktivní nastavení. Doporučujeme, abyste testovali, ale nezapomeňte obnovit tuto hodnotu na **povolenou**, pokud je to možné.

  Tato dodatečná kontrola vás navede na cestu k použití publikované aplikace. Můžete sčítat další konektory, které jsou také nakonfigurovány pro delegování. Další informace najdete v podrobnější technické procházení [řešení potíží s proxy aplikací Azure AD](https://aka.ms/proxytshootpaper).

Pokud stále nemůžete pokročit, může vám pomoci podpora společnosti Microsoft. Vytvořte lístek podpory přímo v rámci portálu. Inženýr vás bude kontaktovat.

## <a name="other-scenarios"></a>Další scénáře

- Azure Aplikační proxy požaduje lístek Kerberos před odesláním jeho požadavek do aplikace. Některé aplikace třetích stran nelíbí tento způsob ověřování. Tyto aplikace očekávají, že konvenčnější jednání budou probíhat. První požadavek je anonymní, což umožňuje aplikaci reagovat s typy ověřování, které podporuje prostřednictvím 401.
- Vícenásobné ověřování směrování se běžně používá ve scénářích, kde je vrstvená aplikace vrstvená, s back-endem a front-endem, kde oba vyžadují ověření, například SQL Server Reporting Services. Chcete-li nakonfigurovat scénář s více směrováními, přečtěte si článek podpory [Kerberos Constrained Delegation may Require Protocol Transition in Multi-hop Scenarios](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>Další kroky

[Konfigurace kcd ve spravované doméně](../../active-directory-domain-services/deploy-kcd.md).
