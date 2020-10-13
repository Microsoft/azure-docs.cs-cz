---
title: Řešení potíží s vynuceným delegováním protokolu Kerberos – proxy aplikací
description: Řešení potíží s konfigurací vynuceného delegování protokolu Kerberos pro proxy aplikací
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/23/2019
ms.author: kenwith
ms.reviewer: asteen, japere
ms.openlocfilehash: 3ca3df010426347846b29734426edfad4536516b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91568733"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Řešení potíží s konfigurací vynuceného delegování protokolu Kerberos pro proxy aplikací

Metody dostupné pro jednotné přihlašování k publikovaným aplikacím se můžou v různých aplikacích lišit. Jedna z možností, kterou služba proxy aplikací Azure Active Directory (Azure AD) nabízí standardně omezené delegování protokolu Kerberos (KCD). Pro vaše uživatele můžete nakonfigurovat konektor pro spouštění omezeného ověřování pomocí protokolu Kerberos pro back-endové aplikace.

Postup pro povolení KCD je jednoduchý. Nevyžaduje žádné více než obecné porozumění různým komponentám a tokům ověřování, které podporují jednotné přihlašování. V některých případech ale KCD SSO nefunguje podle očekávání. K řešení těchto scénářů potřebujete dobré zdroje informací.

Tento článek poskytuje jediný bod odkazu, který pomáhá řešit problémy a samoně opravovat některé z nejběžnějších problémů. Zahrnuje také diagnostiku složitějších problémů s implementací.

Tento článek přináší následující předpoklady:

- Nasazení Azure Proxy aplikací služby AD v rámci Začínáme [s proxy aplikací](application-proxy-add-on-premises-application.md) a obecný přístup k aplikacím, které KCD, fungují podle očekávání.
- Publikovaná cílová aplikace je založená na Internetová informační služba (IIS) a implementaci protokolu Kerberos od Microsoftu.
- Hostitelé serveru a aplikace se nacházejí v jedné Azure Active Directory doméně. Podrobné informace o scénářích mezi doménami a doménovými strukturami najdete v [dokumentu White Paper KCD](https://aka.ms/KCDPaper).
- Předmět aplikace je publikovaný v tenantovi Azure s povoleným předběžným ověřením. Při ověřování pomocí formulářů se očekává, že se uživatelé ověřují v Azure. V tomto článku se nevztahují na rozsáhlé scénáře ověřování klientů. Můžou být přidané v nějakém okamžiku v budoucnu.

## <a name="prerequisites"></a>Požadavky

Azure Proxy aplikací služby AD můžete nasadit do mnoha typů infrastruktur nebo prostředí. Architektury se liší od organizace až po organizaci. Nejběžnější příčiny problémů souvisejících s KCD nejsou prostředí. Jednoduché nesprávné konfigurace nebo obecné chyby způsobují většinu problémů.

Z tohoto důvodu se před zahájením odstraňování potíží ujistěte, že jste splnili všechny předpoklady pro [použití jednotného přihlašování s proxy aplikací KCD](application-proxy-configure-single-sign-on-with-kcd.md) . Poznamenejte si část týkající se konfigurace omezeného delegování protokolu Kerberos v 2012R2. Tento proces využívá jiný přístup ke konfiguraci KCD v předchozích verzích Windows. Nezapomeňte taky tyto požadavky:

- Pro členský server domény není pro otevření dialogového okna zabezpečeného kanálu s konkrétním řadičem domény (DC) neobvyklá. Server se pak může v daném okamžiku přesunout do jiného dialogového okna. Takže hostitelé konektorů nejsou omezeni na komunikaci pouze s určitými řadiči domény místní lokality.
- Scénáře mezi doménami spoléhají na odkazy, které směrují hostitele konektoru na řadiče domény, které by mohly být mimo hranice místní sítě. V těchto případech je stejně důležité taky odesílat provoz dál do řadičů domény, které reprezentují jiné příslušné domény. V takovém případě se delegování nepovede.
- Pokud je to možné, vyhněte se umístění jakýchkoli aktivních IP adres nebo zařízení ID mezi hostiteli konektorů a řadiči domény. Tato zařízení jsou někdy příliš rušivá a jsou v konfliktu s jádrem provozu RPC.

Otestujte delegování v jednoduchých scénářích. Další proměnné, které zavedete, může být nutné soupeří s. Chcete-li ušetřit čas, omezte testování na jeden konektor. Přidejte další konektory po vyřešení problému.

Některé faktory prostředí můžou přispět i k problému. Chcete-li se těmto faktorům vyhnout, minimalizujte při testování co nejvíce architektury. Například nenakonfigurované interní seznamy ACL brány firewall jsou běžné. Pokud je to možné, pošlete veškerý provoz z konektoru přímo do řadičů domény a back-endové aplikace.

Nejlepším místem pro umístění konektorů je co nejblíže k jejich cílům. Brána firewall, která je vložená při testování, přidává zbytečné složitosti a může prodloužit vaše vyšetřování.

Co zobrazuje KCD problém? Existuje několik běžných indikací, které KCD SSO při selhání. První znaménko problému se zobrazí v prohlížeči.

![Příklad: nesprávná chyba konfigurace KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Příklad: autorizace se nezdařila z důvodu chybějících oprávnění.](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Oba tyto obrázky zobrazují stejný příznak: selhání jednotného přihlašování. Přístup uživatele k aplikaci byl odepřen.

## <a name="troubleshooting"></a>Řešení potíží

Způsob řešení potíží závisí na problému a symptomech, které sledujete. Než začnete, Projděte si následující články. Poskytují užitečné informace pro řešení potíží:

- [Řešení potíží s Proxy aplikací a souvisejícími chybovými zprávami](application-proxy-troubleshoot.md)
- [Chyby a příznaky protokolu Kerberos](application-proxy-troubleshoot.md#kerberos-errors)
- [Práce s jednotným přihlašováním, když jsou místní a cloudové identity stejné](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Pokud jste k tomuto okamžiku dostali, pak hlavní problém existuje. Chcete-li začít, oddělte tok do následujících tří fází, které můžete řešit.

### <a name="client-pre-authentication"></a>Předběžné ověření klienta

Externí uživatel, který se v Azure ověřuje přes prohlížeč. Možnost předběžného ověření v Azure je nezbytná, aby KCD jednotné přihlašování fungovalo. Otestujte a adresujte tuto možnost, pokud dojde k nějakým problémům. Fáze předběžného ověřování nesouvisí s KCD nebo publikovanou aplikací. Je snadné opravit případné nesrovnalosti správnosti kontrolou, že účet předmětu existuje v Azure. Také ověřte, že není zakázané nebo blokované. Odpověď na chybu v prohlížeči je dostatečně přizpůsobená, aby mohla vysvětlovat příčinu. Pokud si nejste jistí, podívejte se na další články týkající se řešení potíží se systémem Microsoft.

### <a name="delegation-service"></a>Služba delegování

Konektor proxy serveru Azure, který načte lístek služby protokolu Kerberos pro uživatele z služba KDC (Key Distribution Center) protokolu Kerberos (KCD).

Externí komunikace mezi klientem a front-end Azure nemá žádné vliv na KCD. Tato komunikace zajistí, že KCD funguje. Služba proxy serveru Azure poskytuje platné ID uživatele, které se používá k získání lístku protokolu Kerberos. Bez tohoto ID KCD není možné a selžou.

Jak už bylo zmíněno dříve, chybové zprávy prohlížeče poskytují nějaké dobré informace o tom, proč se věci nezdařily. Nezapomeňte si poznamenat ID aktivity a časové razítko v odpovědi. Tyto informace vám pomůžou sladit chování se skutečnými událostmi v protokolu událostí proxy serveru Azure.

![Příklad: nesprávná chyba konfigurace KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Odpovídající záznamy, které se zobrazují v protokolu událostí, se zobrazí jako události 13019 nebo 12027. Najděte protokoly událostí konektoru v **aplikacích a službách protokoluje** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **správce konektoru** Microsoft AadApplicationProxy &gt; **Admin**.

![Událost 13019 z protokolu událostí proxy aplikace](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![Událost 12027 z protokolu událostí proxy aplikace](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. V interní službě DNS použijte pro adresu aplikace záznam **a** , nikoli **záznam CNAME**.
1. Znovu potvrďte, že hostiteli konektoru bylo uděleno právo delegovat na hlavní název služby (SPN) určeného cílového účtu. Znovu potvrďte, že je vybraná **možnost použít libovolný protokol ověřování** . Další informace najdete v článku věnovaném [konfiguraci jednotného přihlašování](application-proxy-configure-single-sign-on-with-kcd.md).
1. Ověřte, že v Azure AD existuje jenom jedna instance hlavního názvu služby (SPN). Problém `setspn -x` z příkazového řádku na libovolném hostiteli člena domény.
1. Ověřte, že je vynutila zásada domény, která omezuje [maximální velikost vydaných tokenů protokolu Kerberos](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/). Tato zásada zastaví konektor z získání tokenu, pokud byl nalezen jako nadměrný.

Trasování sítě, které zachycuje výměny mezi hostitelem konektoru a službou KDC (Domain KDC), je dalším nejlepším krokem k získání podrobných podrobností o problémech nízké úrovně. Další informace najdete na dokumentu věnovaném [řešení potíží s hloubkou podrobně](https://aka.ms/proxytshootpaper).

Pokud je lístky v pořádku, zobrazí se v protokolech událost s oznámením, že ověření se nezdařilo, protože aplikace vrátila 401. Tato událost indikuje, že cílová aplikace odmítla váš lístek. Přejít na další fázi.

### <a name="target-application"></a>Cílová aplikace

Příjemce lístku Kerberos, který poskytuje konektor. V této fázi očekáváte, že konektor odeslal lístek služby protokolu Kerberos do back-endu. Tento lístek je hlavičkou prvního požadavku aplikace.

1. Pomocí interní adresy URL aplikace definované na portálu ověřte, že je aplikace přístupná přímo z prohlížeče na hostiteli konektoru. Pak se můžete úspěšně přihlásit. Podrobnosti najdete na stránce **Poradce při potížích s** konektorem.
1. Pořád na hostiteli konektoru potvrďte, že ověřování mezi prohlížečem a aplikací používá Kerberos. Proveďte jednu z následujících akcí:
1. Spusťte DevTools (**F12**) v aplikaci Internet Explorer nebo použijte [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) z hostitele konektoru. Do aplikace se dostanete pomocí interní adresy URL. Zkontrolujte nabízená záhlaví pro autorizaci webů vrácená v odpovědi z aplikace, abyste se ujistili, že je k dispozici buď dohadování, nebo Kerberos.

   - Další objekt BLOB Kerberos, který se vrátí v odpovědi z prohlížeče do aplikace, začíná na **YII**. Tato písmena vám sdělí, že je spuštěný protokol Kerberos. Microsoft NT LAN Manager (NTLM) na druhé straně vždy začíná **TlRMTVNTUAAB**, což při dekódování protokolu NTLM Security Support Provider (NTLMSSP) čte z base64. Pokud vidíte **TlRMTVNTUAAB** na začátku objektu blob, protokol Kerberos není k dispozici. Pokud nevidíte **TlRMTVNTUAAB**, je pravděpodobně k dispozici protokol Kerberos.

      > [!NOTE]
      > Pokud používáte Fiddler, tato metoda vyžaduje, abyste dočasně zakázali rozšířenou ochranu v konfiguraci aplikace ve službě IIS.

      ![Okno kontroly sítě prohlížeče](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - Objekt blob na tomto obrázku nezačíná na **TIRMTVNTUAAB**. V tomto příkladu je k dispozici protokol Kerberos a objekt BLOB protokolu Kerberos nezačíná na **YII**.

1. Dočasně odeberte protokol NTLM ze seznamu zprostředkovatelů na webu služby IIS. Přihlaste se k aplikaci přímo z Internet Exploreru na hostiteli konektoru. Protokol NTLM již není v seznamu zprostředkovatelů. K aplikaci můžete přistupovat jenom pomocí protokolu Kerberos. Pokud dojde k chybě, může dojít k potížím s konfigurací aplikace. Ověřování protokolem Kerberos nefunguje.

   - Pokud není protokol Kerberos k dispozici, ověřte nastavení ověřování aplikace ve službě IIS. Ujistěte se, že je seznam **Negotiate** uveden v horní části, s protokolem NTLM hned pod ním. Pokud se zobrazí **Nedohadované**, **Kerberos nebo Negotiate**nebo **PKU2U**, pokračujte pouze v případě, že je Kerberos funkční.

     ![Zprostředkovatelé ověřování systému Windows](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - Pomocí protokolu Kerberos a NTLM na místě dočasně zakažte předběžné ověřování pro aplikaci na portálu. Pokuste se k němu přistoupit z Internetu pomocí externí adresy URL. Budete vyzváni k ověření. Můžete tak učinit stejným účtem, jaký jste použili v předchozím kroku. V takovém případě se vyskytl problém s back-end aplikací, nikoli KCD.
   - Opětovné povolení předběžného ověření na portálu. Proveďte ověření prostřednictvím Azure tím, že se pokusíte připojit k aplikaci prostřednictvím své externí adresy URL. Pokud se SSO nepovede, zobrazí se v prohlížeči chybová zpráva o zakázaném přihlášení a v protokolu událost 13022:

     *Microsoft AAD Application proxy Connector nemůže ověřit uživatele, protože back-end Server reaguje na pokusy o ověření pomocí protokolu Kerberos pomocí chyby HTTP 401.*

      ![Zobrazuje chybu HTTTP 401 Forbidden](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - Ověřte aplikaci IIS. Ujistěte se, že konfigurovaný fond aplikací a hlavní název služby (SPN) jsou nakonfigurované tak, aby používaly stejný účet ve službě Azure AD. Přejděte do služby IIS, jak je znázorněno na následujícím obrázku:

      ![Okno Konfigurace aplikace služby IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      Po zjištění identity se ujistěte, že je tento účet nakonfigurovaný s tímto hlavním názvem služby (SPN). Příklad: `setspn –q http/spn.wacketywack.com`. Do příkazového řádku zadejte následující text:

      ![Zobrazuje příkazové okno SetSPN.](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Podívejte se na hlavní název služby (SPN) definovaný pro nastavení aplikace na portálu. Ujistěte se, že fond aplikací aplikace používá stejný hlavní název služby (SPN) nakonfigurovaný pro cílový účet Azure AD.

      ![Konfigurace hlavního názvu služby (SPN) v Azure Portal](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - Přejít do služby IIS a vybrat možnost **editoru konfigurace** pro aplikaci. Přejděte na **System. webServer/security/Authentication/windowsAuthentication**. Ujistěte se, že hodnota **UseAppPoolCredentials** je **true**.

      ![Možnost přihlašovacích údajů fondů aplikace konfigurace služby IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Změňte tuto hodnotu na **true**. Pomocí následujícího příkazu odeberte všechny lístky protokolu Kerberos v mezipaměti ze serveru back-end:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

Další informace najdete v tématu [vyprázdnění mezipaměti lístků klienta protokolu Kerberos pro všechny relace](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).

Pokud necháte režim jádra povolený, zlepší se výkon operací Kerberos. Ale také způsobí dešifrování lístku požadované služby pomocí účtu počítače. Tento účet se také označuje jako místní systém. Nastavte tuto hodnotu na **true** , pokud chcete přerušit KCD při hostování aplikace na více než jednom serveru ve farmě.

- Při další kontrole zakažte také **rozšířenou** ochranu. V některých scénářích je **Rozšířená** ochrana podařilo přerušit KCD, když byla povolená v konkrétních konfiguracích. V těchto případech byla aplikace publikována jako podsložka výchozího webu. Tato aplikace je nakonfigurovaná jenom pro anonymní ověřování. Všechna dialogová okna jsou šedá, což naznačuje, že podřízené objekty nedědí žádná aktivní nastavení. Doporučujeme, abyste provedli test, ale nezapomněli jste tuto hodnotu obnovit, **Pokud je to**možné.

  Tato další revize vám umožní sledovat, jestli chcete používat publikovanou aplikaci. Můžete aktivovat další konektory, které jsou také nakonfigurované na delegování. Další informace najdete podrobnější technický návod, který vám ukáže [řešení potíží s proxy aplikací služby AD Azure](https://aka.ms/proxytshootpaper).

Pokud pořád nemůžete dělat svůj průběh, může vám pomoct podpora Microsoftu. Vytvořte lístek podpory přímo v rámci portálu. Pracovník vás bude kontaktovat.

## <a name="other-scenarios"></a>Další scénáře

- Azure Application proxy vyžaduje lístek Kerberos před odesláním žádosti do aplikace. Některé aplikace od jiných výrobců nezpůsobují tuto metodu ověřování. Tyto aplikace očekávají, že budou probíhat přísnější jednání. První požadavek je anonymní, což aplikaci umožňuje reagovat na typy ověřování, které podporuje přes 401. Tento typ vyjednávání Kerberos se dá povolit pomocí kroků popsaných v tomto dokumentu: [omezené delegování protokolu Kerberos pro jednotné přihlašování](application-proxy-configure-single-sign-on-with-kcd.md).
- Ověřování s více segmenty se obvykle používá ve scénářích, kdy je aplikace vrstvena s back-end a front-end, kde obě vyžadují ověřování, například SQL Server Reporting Services. Pokud chcete nakonfigurovat scénář s více segmenty směrování, přečtěte si článek podpora [omezeného delegování protokolu Kerberos ve scénářích s více segmenty směrování může vyžadovat přechod protokolu](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>Další kroky

[NAKONFIGURUJTE KCD ve spravované doméně](../../active-directory-domain-services/deploy-kcd.md).
