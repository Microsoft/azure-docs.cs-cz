---
title: Řešení potíží s protokolem Kerberos omezeného delegování konfigurace pro Proxy aplikace | Microsoft Docs
description: Řešení potíží s protokolem Kerberos omezeného delegování konfigurace pro Proxy aplikace.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2018
ms.author: markvi
ms.reviewer: harshja
ms.openlocfilehash: 3ba089123198631c443a759ad62cb0ae5ca40ad3
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Řešení potíží s protokolem Kerberos omezeného delegování konfigurace pro Proxy aplikace

Dostupné metody pro dosažení jednotného přihlašování k publikovaným aplikacím může poněkud lišit od jedné aplikace do jiné. Jednou z možností, které nabízí Proxy aplikace služby Azure ve výchozím nastavení, je Kerberos vynuceným delegování použitím (KCD). Můžete nakonfigurovat konektor provádět omezené ověřování protokolu Kerberos na back-end aplikace jménem uživatelů.

Skutečný postup povolení použitím KCD je relativně jednoduché a obvykle vyžaduje více než obecné znalosti o různými součástmi a tok ověřování, která usnadňuje jednotné přihlašování. Hledání poskytují informace k řešení scénáře kde použitím KCD jednotného přihlašování k nebude fungovat podle očekávání, může být Zhuštěno.

V tomto článku se jako takový pokusí poskytovat samostatný bod odkaz, který by měly pomoci při řešení potíží a samoobslužné napravit některé z nejběžnějších problémů vidět. Ve stejnou dobu nabízí další pokyny pro diagnostiku více komplexní a problémových implementace.

Tento článek neposkytuje následující předpoklady:

-   Nasazení Proxy aplikace služby Azure na [dokumentace](manage-apps/application-proxy-enable.md) a obecné přístup k aplikacím jiných použitím KCD funguje podle očekávání.

-   Publikované cílová aplikace je založena na službě IIS a společnosti Microsoft implementace protokolu Kerberos.

-   Hostitelé serveru a aplikace se nacházet v jedné doméně služby Active Directory. Podrobné informace o pro různé scénáře domény a doménové struktury naleznete v [dokument White Paper použitím KCD](https://aka.ms/KCDPaper).

-   Příslušná aplikace je publikována v Azure klienta povoleno předběžné ověřování a uživatelé budou k ověřování na Azure prostřednictvím ověřování pomocí formulářů. Scénáře ověřování klienta bohaté nejsou uvedené v tomto článku, ale v určitém okamžiku přidat v budoucnu.

## <a name="prerequisites"></a>Požadavky

Proxy aplikace služby Azure můžete nasadit do mnoho typů infrastruktury nebo prostředí a architekturách už nepochybně lišit organizace. Mezi nejběžnější příčiny problémy související s použitím KCD nejsou prostředí sami, ale spíše jednoduché chybná konfigurace nebo obecné dohledu.

Z tohoto důvodu je vždy vhodné začít tím, že zajistí jste splnili všechny předpoklady nastíněny v [pomocí jednotného přihlašování použitím KCD s Proxy aplikace článku](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md) před spuštěním řešení potíží.

Části se hlavně na konfiguraci použitím KCD na 2012R2, aktivuje se podstatně liší přístup ke konfiguraci použitím KCD v dřívějších verzích systému Windows, ale také při se s vědomím několik dalších hledisek:

-   Je běžné pro členský server domény a otevřete dialogové okno zabezpečený kanál s konkrétním řadičem domény. Pak přesuňte do další dialog v každém okamžiku tak konektor hostitele nesmí být omezen na schopnost komunikovat se pouze konkrétní místní lokality řadiče domény.

-   Podobně jako u předchozí bodu napříč doménami, které scénáře spoléhají na referenční seznamy, které budou řídit konektor hostitele do řadiče domény, které se mohou nacházet mimo hraniční místní sítě. V tomto scénáři je stejně důležité zkontrolujte, jestli jsou také umožňuje provoz na řadičích domény, které představují jiných příslušných domén a vyšší, nebo jiný delegování nezdaří.

-   Kde je to možné, vyhněte se umístění všech aktivních zařízení IP adresy nebo ID mezi hostiteli v konektoru a řadiče domény, jako jsou někdy přes nežádoucí a narušovat přenos pomocí protokolu RPC jádra

Měli byste otestovat delegování v nejjednodušší scénáře. Zavést další proměnné, čím může být nutné soupeří s. Například omezení testování jeden konektor můžete uložit drahocenný čas a další konektory lze přidat po vyřešení problémů.

Některé faktorech prostředí může přispět také k problém. Během testování, minimalizujte architekturu pro úplné minimální, aby se zabránilo těchto faktorech prostředí. Například, nejsou nesprávně nakonfigurované interní brány firewall seznamy ACL, takže pokud je to možné mít veškerý provoz z řadičů domény a back-end aplikace povoleno přímo prostřednictvím konektoru. 

Absolutní nejlepší místo k umístit konektory ve skutečnosti je co nejblíže jejich cíle jako může být. S vložené den brány firewall a přitom se jednoduše testování přidá nepotřebné složitost a může prodloužit jenom vaše šetření.

Ano co se považuje za problém použitím KCD přesto? Existuje několik classic indikace selhání přihlašování použitím KCD a první známky problém obvykle projevují v prohlížeči.

   ![Chyba konfigurace nesprávný použitím KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

   ![Autorizace se nezdařila z důvodu chybějících oprávnění](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

všechny, které jsou opatřeny stejné příznakem nedaří provést jednotné přihlašování a v důsledku odepření přístupu uživatele k aplikaci.

## <a name="troubleshooting"></a>Řešení potíží

Jak řešit závisí na problém a zjištěnou příznaků. Před přechodem žádné další, prozkoumejte obsahovat užitečné informace, které jste ještě nemusí mít pocházet napříč následující odkazy:

-   [Řešení potíží s Proxy aplikace problémy a chybové zprávy](active-directory-application-proxy-troubleshoot.md)

-   [Chyby protokolu Kerberos a symptomy](active-directory-application-proxy-troubleshoot.md#kerberos-errors)

-   [Práce s SSO při místní a cloudové identity nejsou identické](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Pokud máte k dispozici to úplně, pak potíže hlavní výborný existuje. Spusťte oddělením toku do tří různých fází, které lze řešit potíže s.

**Předběžné ověření klienta** – externího uživatele ověřováním v Azure prostřednictvím prohlížeče.

Schopnost předběžné ověřování v Azure je nutné pro jednotné přihlašování použitím KCD funkce. Měli otestovat a to vyřešit, pokud jsou nějaké problémy. Fázi předběžné ověření nemá žádný vztah k použitím KCD nebo publikované aplikace. Očekává se poměrně snadno vyřešit případné nesrovnalosti správností kontrola subjektu účtu v Azure existuje a zda není zakázána nebo zablokovaný. Odpovědi na chybu v prohlížeči je obvykle dostatečně popisný k pochopení příčiny. Naše další dokumentace Poradce při potížích k ověření, pokud si nejste jisti, můžete také zkontrolovat.

**Delegování služby** – konektor Azure Proxy získání lístků služby Kerberos z KDC (Kerberos Distribution Center) jménem uživatele.

Externí komunikace mezi klientem a Azure front-endu by měl mít žádný vliv na použitím KCD jakkoli, jiné než zajistit, že funguje. To je proto službu Azure Proxy lze zadat s platné uživatelské ID, který se používá k získání lístku protokolu Kerberos. Bez toho použitím KCD není možné a dojde k selhání.

Jak je uvedeno nahoře, prohlížeč chybové zprávy, které obvykle nabízejí některé funkční různá vodítka na Proč se nedaří věcí. Zajistěte, aby si poznamenat ID aktivity a časové razítko v odpovědi, protože to umožňuje korelovat požadované chování, skutečná události v protokolu událostí Azure Proxy.

   ![Chyba konfigurace nesprávný použitím KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

A odpovídající položky zobrazit, že protokol událostí bude považovat za událost 13019 nebo 12027. Můžete najít protokoly událostí konektor v **protokoly aplikací a služeb** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **konektor**&gt;**správce**.

   ![Událost 13019 z protokolu událostí aplikace Proxy](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

   ![12027 události z protokolu událostí aplikace Proxy](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

-   Použít záznam v interní DNS pro adresu aplikace a ne záznam CName

-   Potvrzení, že hostitel konektor byla udělena oprávnění pro delegování do určené cílového účtu SPN a že **používající libovolný protokol pro ověřování** je vybrána. Další informace o tomto tématu najdete v tématu [článku Konfigurace jednotného přihlašování](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

-   Ověřte, že je po vydání pouze jednu instanci služby SPN v existence ve službě AD `setspn -x` z příkazový řádek na všechny domény členského hostitele

-   Zkontrolujte, jestli je zásada domény vynucená omezit [maximální velikost vystavené tokeny Kerberos](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/), jak je to brání konektor získání tokenu Pokud nalezen nadměrné

Trasování sítě zaznamenávání výměn mezi hostitelem konektoru a domény služby KDC pak bude nejlepší dál při získávání další nízké úrovně podrobnosti o problémech. Další informace najdete v tématu, [podrobné informace Poradce při potížích dokumentu](https://aka.ms/proxytshootpaper).

Pokud lístků spokojeni, měli byste vidět událost v protokolech s informacemi o tom, ověření se nezdařilo z důvodu aplikace vrací 401. To obvykle označuje, že cílová aplikace odmítat lístku, takže pokračovat s následující další fáze:

**Cílová aplikace** -příjemce poskytované konektor lístek protokolu Kerberos

V této fázi se očekává konektor odeslali lístek služby protokolu Kerberos na back-end, jako hlavičku v rámci první žádost o aplikaci.

-   Pomocí aplikace interní adresa URL definované v portálu, ověřte, že aplikace je přístupný přímo z prohlížeče na hostiteli konektor. Potom se můžete přihlásit úspěšně. Podrobnosti o to naleznete na stránce konektor Poradce při potížích.

-   Stále v hostiteli konektor potvrďte, že ověřování mezi prohlížečem a aplikace používá protokolu Kerberos, jedním z následujících akcí:

1.  Spuštění nástrojů pro vývojáře (**F12**) v Internet Exploreru, nebo použijte [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) z hostitele konektor. Přejděte do aplikace pomocí interní adresa URL a zkontrolovat nabízených hlavičky WWW ověření vrácený v odpovědi z aplikace, ujistěte se, že buď vyjednávání nebo protokolu Kerberos je k dispozici. Následné blob protokolu Kerberos, vrátí se v odpovědi z prohlížeče do aplikace obvykle začínat **JÍ**, takže toto je dobrá indikace toho Kerberos se v play. NTLM na druhé straně vždy začíná **TlRMTVNTUAAB**, který čte NTLMSSP při dekódovat z formátu Base64. Pokud se zobrazí **TlRMTVNTUAAB** na začátku objektu blob, to znamená, že se protokol Kerberos **není** k dispozici. Pokud to nevidíte, pomocí protokolu Kerberos je pravděpodobně k dispozici.
    > [!NOTE]
    > Pokud používáte aplikaci Fiddler, tato metoda by vyžadovaly dočasně zakázat rozšířené ochrany na konfiguraci aplikace ve službě IIS.

     ![Okno kontroly sítě prohlížeče](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

    *Obrázek:* vzhledem k tomu, že to nezačíná TIRMTVNTUAAB, jedná se o příklad Kerberos je k dispozici. Jedná se o příklad objektu Blob protokolu Kerberos, který nezačíná JÍ.

2.  Ze seznamu poskytovatelů na webu a přístup aplikace IIS přímo z prohlížeče IE na hostiteli konektor dočasně odeberte protokolu NTLM. Pomocí protokolu NTLM již v seznamu poskytovatelů byste měli mít přístup k aplikaci pouze pomocí protokolu Kerberos. Pokud se to nezdaří, pak, naznačuje, že došlo k potížím s konfigurací aplikace a ověřování protokolu Kerberos nefunguje.

Protokolu Kerberos není k dispozici, zkontrolovat nastavení ověřování aplikace ve službě IIS zajistěte, aby vyjednávání je uvedena nejhornější prostřednictvím NTLM pod ní. (Není vyjednávání: protokolu Kerberos nebo vyjednávání: protokolu PKU2U). Pokračujte pouze v případě protokolu Kerberos je funkční.

   ![Zprostředkovatelé ověřování systému Windows](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)
   
-   Pomocí protokolu Kerberos a NTLM na místě umožňuje nyní dočasně zakázat předběžné ověřování pro aplikaci na portálu. V tématu, pokud je přístupné z Internetu pomocí externí adresu URL. Musí se výzva k ověření a byste měli mít k tomu se stejným účtem použitým v předchozím kroku. Pokud ne, to znamená problém s back-end aplikace a není použitím KCD vůbec.

-   Nyní znovu povolte předběžné ověření na portálu a ověřit prostřednictvím Azure tak, že pokus o připojení k aplikaci pomocí jeho externí adresu URL. Pokud se nezdařila jednotné přihlašování, měla zobrazit zpráva zakázané chyby v prohlížeči a 13022 událost v protokolu:

    *Microsoft AAD Application Proxy Connector nemůže ověřit uživatele, protože back-end server odpoví na pokusy o ověření pomocí protokolu Kerberos s chybu HTTP 401.*

    ![Je zakázané chyba 401 HTTTP](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

-   Zkontrolujte aplikace služby IIS a zda že fond konfigurovaná aplikace je nakonfigurovaný na použití stejný účet, který SPN nakonfiguroval proti ve službě AD: navigace ve službě IIS jako na následujícím obrázku

    ![Okno Konfigurace aplikace IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

    Jakmile znáte identitu, vydejte následující z příkazový řádek a ujistěte se, že tento účet jednoznačně nastavena dotyčném SPN. Například  `setspn –q http/spn.wacketywack.com`

    ![SetSPN příkazové okno](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

-   Zkontrolujte, jestli hlavní název služby definované vzhledem nastavení aplikace na portálu se stejnou hlavní název služby nakonfigurované proti cílová AD účet používá fond aplikací dané aplikace

   ![Hlavní název služby konfigurace na portálu Azure](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)
   
-   Přejděte do služby IIS a vyberte **Editor konfigurací** možnost pro aplikaci a přejděte na **system.webServer/security/authentication/windowsAuthentication** a ujistěte se hodnota **UseAppPoolCredentials** je **True**

   ![Fondy aplikací IIS konfigurace přihlašovacích údajů – možnost](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

Po změně této hodnoty na **True**, všechny uložené v mezipaměti protokolu Kerberos lístky třeba je odebrat z back-end serveru. To provedete tak, že spustíte následující příkaz:

```powershell
Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
``` 

Další informace najdete v tématu [vyprázdnění mezipaměti klienta lístek protokolu Kerberos pro všechny relace](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).



Když je užitečné v zvýšení výkonu operací protokolu Kerberos, a také povoleno v režimu jádra způsobí, že lístek pro požadovaná služba k dešifrování pomocí účtu počítače. To je také označován místního systému, takže s to nastavené na true zalomení použitím KCD, když je aplikace hostované na více serverech ve farmě.

-   Jako další kontrolu, můžete také chtít zakázat **rozšířené** ochrany příliš. Byly došlo k scénáře, kde to se ukázalo jako rozdělit použitím KCD-li povolena v konkrétní konfigurace, kde je aplikace publikována jako dílčí složku výchozí webové stránky. Tato samotné je nakonfigurován pro pouze anonymní ověřování a celý dialogová okna šedě, které naznačují, že podřízené objekty nebude dědí všechny aktivní nastavení. Ale kde to možné, bude vždy doporučujeme mít tuto funkci povolíte, proto by nestálo testů, ale nezapomeňte toto povolit obnovení.

Tyto další kontroly by měl mít umístí můžete sledovat, pokud chcete začít používat k publikované aplikaci. Můžete pokračovat a začne pracovat další konektory, které jsou také nakonfigurovány pro delegování, ale pokud žádné další věci pak by doporučujeme číst z našich podrobnější návod technické [kompletní Příručka pro řešení potíží s Azure AD Application Proxy](https://aka.ms/proxytshootpaper)

Pokud jste stále se nedaří průběhu problém, podporu bychom více než pomůže a pokračujte odsud. Vytvořte lístek podpory přímo v rámci portálu (technika bude oslovení vám).

## <a name="other-scenarios"></a>Další scénáře

-   Proxy aplikace služby Azure vyžádá lístek protokolu Kerberos před odesláním požadavku k aplikaci. Některé aplikace jiných výrobců, jako je například Tableau serveru nelíbí tuto metodu ověřování a místo očekává více konvenční jednání proběhla. První požadavek je anonymní, umožní aplikaci reagovat s typy ověřování, že podporuje prostřednictvím 401.

-   Double směrování ověřování – běžně používá ve scénářích, kde vrstvené aplikace s back-end a před koncem, oba vyžadují ověřování, jako je služba SQL Reporting Services.

## <a name="next-steps"></a>Další postup
[Nakonfigurujte omezené delegování protokolu kerberos (použitím KCD) na spravované doméně](../active-directory-domain-services/active-directory-ds-enable-kcd.md)
