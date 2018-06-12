---
title: Řešení potíží s vynuceným použitím protokolu Kerberos konfigurace delegování pro Proxy aplikace | Microsoft Docs
description: Řešení potíží s konfigurací omezené delegování Kerberos pro Proxy aplikace
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: harshja
ms.openlocfilehash: 933a5c8a9cde9134071a12dd0aed684358cc8696
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292850"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Řešení potíží s vynuceným použitím protokolu Kerberos konfigurace delegování pro Proxy aplikace

Dostupné metody pro dosažení jednotného přihlašování k publikovaným aplikacím se může lišit od jedné aplikace do jiné. Jedna možnost, která nabízí Proxy aplikace služby Azure Active Directory (Azure AD) ve výchozím nastavení je omezené delegování protokolu Kerberos (použitím KCD). Můžete nakonfigurovat konektor, pro vaše uživatele, spustit omezené ověřování protokolu Kerberos na back-end aplikace.

Postup povolení použitím KCD je jednoduchá. To vyžaduje nejvýše obecné znalosti o různými součástmi a tok ověřování, které podporují jednotné přihlašování. Ale v některých případech použitím KCD SSO nebude fungovat podle očekávání. Je nutné poskytují informace k řešení těchto scénářů.

Tento článek obsahuje jediný bod odkaz, který pomáhá při řešení potíží a samoobslužné napravit některé z nejběžnějších problémů. Také vysvětluje diagnostiku problémů složitější implementace.

Tento článek neposkytuje následující předpoklady:

-   Nasazení služby Azure AD Application Proxy za [začít pracovat s Proxy aplikace](manage-apps/application-proxy-enable.md) a obecné přístup k aplikacím použitím KCD fungovat podle očekávání.

-   Publikované cílová aplikace je založena na Internetové informační služby (IIS) a Microsoft implementace protokolu Kerberos.

-   Hostitelé serveru a aplikace se nacházet v jedné doméně služby Azure Active Directory. Podrobné informace o scénáře napříč doménami a doménovou strukturu, najdete v článku [dokumentu white paper použitím KCD](https://aka.ms/KCDPaper).

-   Příslušná aplikace je publikována v Azure klienta povoleno předběžné ověřování. Očekává se, že uživatelé ověřování v Azure prostřednictvím ověřování pomocí formulářů. Scénáře ověřování klienta bohaté nejsou uvedené v tomto článku. Se může přidat v určitém okamžiku v budoucnu.

## <a name="prerequisites"></a>Požadavky

Proxy aplikace služby Azure AD můžete nasadit do mnoho typů infrastruktury nebo prostředí. Architekturách lišit organizace. Nejběžnější příčiny problémy související s použitím KCD nejsou prostředí. Jednoduché nesprávné konfigurace nebo obecné chyby způsobit většiny problémů.

Z tohoto důvodu je nejvhodnější zajistit splnění všech požadavků v [pomocí jednotného přihlašování použitím KCD s Proxy aplikace](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md) před zahájením řešení potíží. Poznámka: v části o konfiguraci vynuceného delegování protokolu Kerberos na 2012 R2. Tento proces používá jiný přístup ke konfiguraci použitím KCD v dřívějších verzích systému Windows. Navíc mějte na paměti tyto aspekty:

-   Je běžné pro členský server domény a otevřete dialogové okno zabezpečený kanál s určitý řadič domény (DC). Pak server může přesunout do jiné dialogového okna v každém okamžiku. Proto nejsou konektor hostitele s omezeným přístupem pro komunikaci s místní lokality pouze konkrétní řadiče domény.

-   Scénáře napříč doménami spoléhají na referenční seznamy, které budou řídit konektor hostitele do řadiče domény, které může být mimo hraniční místní sítě. V těchto případech je stejně důležité také odesílat provoz dále do řadiče domény, které představují jiných příslušných domén. V opačném případě delegování selže.

-   Pokud je to možné, vyhněte se umístění aktivních IP adresy nebo ID zařízení mezi hostiteli v konektoru a řadiče domény. Tato zařízení jsou někdy overintrusive a narušovat základní přenos pomocí protokolu RPC.

Testování delegování v jednoduchého scénáře. Zavést další proměnné, čím může být nutné soupeří s. Chcete-li ušetřit čas, omezte testování jeden konektor. Po odstranění problému, přidejte další konektory.

Některé faktorech prostředí může přispět také k problému. Abyste se vyhnuli tyto faktory, minimalizujte architektura co nejvíce během testování. Nesprávně nakonfigurované interní brány firewall seznamy ACL jsou například běžné. Pokud je to možné odesílat veškerý přenos z konektoru přímo na řadiče domény a back-end aplikace.

Nejlepší místo k umístit konektory je co nejblíže k jejich cílům. Brány firewall, která se nachází vložené při testování přidá nepotřebné složitost a může prodloužit vaší šetření.

Co se zobrazí použitím KCD problém? Existuje několik běžných údaje, které se nedaří použitím KCD jednotné přihlašování. První známky problém se zobrazí v prohlížeči.

   ![Chyba konfigurace nesprávný použitím KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

   ![Autorizace se nezdařila z důvodu chybějících oprávnění](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Obě tyto Image zobrazit stejné příznaky: jednotného přihlašování k selhání. Uživatelský přístup k aplikaci byl odepřen.

## <a name="troubleshooting"></a>Řešení potíží

Jak řešit závisí na problém a příznaky, které můžete sledovat. Před přechodem žádné dále, prozkoumejte v následujících článcích. Poskytují užitečné informace o odstraňování potíží:

-   [Řešení potíží s Proxy aplikace problémy a chybové zprávy](manage-apps/application-proxy-troubleshoot.md)

-   [Chyby protokolu Kerberos a symptomy](manage-apps/application-proxy-troubleshoot.md#kerberos-errors)

-   [Práce s SSO při místní a cloudové identity nejsou identické](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Vy máte k tomuto bodu, pak existuje hlavní problém. Chcete-li začít, oddělte toku do těchto tří fází, které lze řešit potíže s.

### <a name="client-pre-authentication"></a>Předběžné ověření klienta 
Externí ověřování uživatele do Azure prostřednictvím prohlížeče. Možnost předem ověřování v Azure je nezbytné pro jednotné přihlašování použitím KCD funkce. Testování a vyřešit tuto možnost, pokud jsou nějaké problémy. Předběžné ověření fáze nesouvisí s použitím KCD nebo publikované aplikace. Je snadné a opravte všechny rozporů kontrolou správností subjektu účet existuje v Azure. Také zkontrolujte, zda není zakázána, nebo blokovaný. Odpovědi na chybu v prohlížeči je dostatečně popisný, který vysvětluje příčinu. Pokud si nejste jisti, zkontrolujte další články Microsoft řešení potíží k ověření.

### <a name="delegation-service"></a>Delegování služby 
Konektor Azure Proxy, který získá lístek služby protokolu Kerberos pro uživatele z použitím protokolu Kerberos klíč distribuční Center (KCD).

Externí komunikace mezi klientem a Azure front-endu mít žádný vliv na použitím KCD. Tyto komunikace pouze ujistěte se, že použitím KCD funguje. Službu Azure Proxy poskytuje platné uživatelské ID, které se použije k získání lístku protokolu Kerberos. Bez tohoto ID použitím KCD není možné a selže.

Jak je uvedeno nahoře, prohlížeč chybové zprávy, které poskytuje některé funkční různá vodítka o proč věcí nezdaří. Zajistěte, aby si poznamenat ID aktivity a časové razítko v odpovědi. Tyto informace umožňuje korelovat požadované chování, skutečná události v protokolu událostí Azure Proxy.

   ![Chyba konfigurace nesprávný použitím KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Odpovídající položky zobrazit v protokolu událostí se zobrazí jako události 13019 nebo 12027. Najít protokoly událostí konektor v **protokoly aplikací a služeb** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt;  **Konektor** &gt; **správce**.

   ![Událost 13019 z protokolu událostí aplikace Proxy](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

   ![12027 události z protokolu událostí aplikace Proxy](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1.   Použijte **A** záznam v interní DNS pro adresu aplikace, není **CName**.

2.   Potvrzení, že konektor hostitele má uděleno oprávnění pro delegování do hlavního názvu služby určené cílového účtu. Potvrzení, **používající libovolný protokol pro ověřování** je vybrána. Další informace najdete v tématu [článku Konfigurace jednotného přihlašování k](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md).

3.   Ověřte, zda existuje pouze jedna instance služby SPN v existence ve službě Azure AD. Problém `setspn -x` z příkazového řádku na všechny domény členského hostitele.

4.   Zkontrolujte, že domény zásady se vynucují který omezuje [maximální velikost vystavené tokeny Kerberos](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/). Tato zásada zastaví konektor z získávání tokenu, pokud je zjištěno, že je příliš vysoký.

Trasování sítě, který zachycuje výměn mezi hostitelem konektoru a domény služby KDC je dalším krokem nejlepší získat více nízké úrovně podrobností na problémy. Další informace najdete v tématu [podrobné informace Poradce při potížích dokumentu](https://aka.ms/proxytshootpaper).

Pokud lístků spokojeni, zobrazí se událost v protokolech s informacemi o tom, ověření se nezdařilo, protože aplikace vrátil 401. Tato událost znamená, že cílová aplikace odmítli lístku. Přejděte do další fáze.

### <a name="target-application"></a>Cílové aplikace 
Příjemce poskytované konektor lístek protokolu Kerberos. V této fázi se očekávají, že konektor, které se mají odesílat lístek služby protokolu Kerberos na back-end. Tento lístek se hlavička v první žádost o aplikaci.

1.   Pomocí aplikace interní adresa URL definované v portálu ověřte, že aplikace je přístupný přímo z prohlížeče na hostiteli konektor. Potom můžete přihlásit úspěšně. Podrobnosti najdete v konektoru **Poradce při potížích** stránky.

2.   Stále v hostiteli konektor potvrďte, že použije Kerberos pro ověřování mezi prohlížečem a aplikace. Proveďte jednu z následujících akcí:

3.  Spustit DevTools (**F12**) v Internet Exploreru, nebo použijte [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) z hostitele konektor. Přejděte do aplikace pomocí interní adresa URL. Zkontrolujte nabízený hlavičky WWW ověření vrácený v odpovědi z aplikace do Ujistěte se, že buď vyjednávání nebo protokolu Kerberos je k dispozici. 

    a. Další objektů blob protokolu Kerberos, který je vrácený v odpovědi z prohlížeče do aplikace spustí s **JÍ**. Těmto písmenům zjistíte, zda je spuštěna protokolu Kerberos. Microsoft NT LAN Manager (NTLM), na druhé straně vždy začíná **TlRMTVNTUAAB**, který čte NTLM zabezpečení podpory zprostředkovatele (NTLMSSP) při dekódovat z formátu Base64. Pokud se zobrazí **TlRMTVNTUAAB** na začátku objektu blob, protokolu Kerberos není k dispozici. Pokud nevidíte **TlRMTVNTUAAB**, protokolu Kerberos je pravděpodobně k dispozici.

       > [!NOTE]
       > Pokud chcete použít aplikaci Fiddler, tato metoda vyžaduje, aby dočasně zakázat rozšířené ochrany na konfiguraci aplikace ve službě IIS.

       ![Okno kontroly sítě prohlížeče](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

    b. Objekt blob na tomto obrázku nezačíná **TIRMTVNTUAAB**. Tak, aby v tomto příkladu je k dispozici protokolu Kerberos a protokolu Kerberos blob nezačíná **JÍ**.

4.  NTLM dočasně odeberte ze seznamu poskytovatelů na webu služby IIS. Přístup k aplikaci přímo z Internet Exploreru na hostiteli konektor. NTLM je již v seznamu poskytovatelů. Přístup k aplikaci pouze pomocí protokolu Kerberos. Pokud přístup selže, může být problém s konfigurací aplikace. Ověřování protokolem Kerberos nefunguje.

    a. Pokud pomocí protokolu Kerberos není dostupná, zkontrolujte nastavení ověřování aplikace ve službě IIS. Zajistěte, aby **Negotiate** je uveden v horní části prostřednictvím NTLM pod ní. Pokud se zobrazí **není vyjednávání**, **protokolu Kerberos nebo Negotiate**, nebo **protokolu PKU2U**, pokračovat, pouze pokud protokolu Kerberos je funkční.

       ![Zprostředkovatelé ověřování systému Windows](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)
   
    b. Pomocí protokolu Kerberos a NTLM na místě dočasně zakážete předběžné ověřování pro aplikaci na portálu. Pokuste se k němu přístup z Internetu pomocí externí adresu URL. Se zobrazí výzva k ověření. Jste nepovedlo se stejným účtem použitým v předchozím kroku. Pokud ne, došlo k potížím s back-end aplikace, není použitím KCD.

    c. Předběžné ověření na portálu znovu povolte. Ověřit prostřednictvím Azure tak, že pokus o připojení k aplikaci pomocí jeho externí adresu URL. V případě selhání jednotného přihlašování zobrazí zakázané chybová zpráva v prohlížeči a 13022 událost v protokolu:

       *Microsoft AAD Application Proxy Connector nemůže ověřit uživatele, protože back-end server odpoví na pokusy o ověření pomocí protokolu Kerberos s chybu HTTP 401.*

       ![Je zakázané chyba 401 HTTTP](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

    d. Zkontrolujte aplikace služby IIS. Ujistěte se, že fond aplikací nakonfigurovaný a SPN jsou nakonfigurované na použít stejný účet ve službě Azure AD. Přejděte ve službě IIS, jak je znázorněno na následujícím obrázku:

       ![Okno Konfigurace aplikace IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

       Až budete znát identitu, zajistěte, aby že tento účet je nakonfigurované v hlavní název služby. Příklad: `setspn –q http/spn.wacketywack.com`. V příkazovém řádku zadejte následující text:

       ![SetSPN příkazové okno](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

    e. Zkontrolujte název SPN definované vzhledem nastavení aplikace v portálu. Ujistěte se, že fond aplikací dané aplikace používá stejný název SPN nakonfigurované na cíli účet Azure AD.

       ![Hlavní název služby konfigurace na portálu Azure](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)
   
    f. Přejděte do služby IIS a vyberte **Editor konfigurací** možnost pro aplikaci. Přejděte na **system.webServer/security/authentication/windowsAuthentication**. Ujistěte se, hodnota **UseAppPoolCredentials** je **True**.

       ![Fondy aplikací IIS konfigurace přihlašovacích údajů – možnost](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

       Změna této hodnoty na **True**. Odeberte všechny uložené v mezipaměti lístky protokolu Kerberos z back-end serverů tak, že spustíte následující příkaz:

       ```powershell
       Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
       ``` 

Další informace najdete v tématu [vyprázdnění mezipaměti klienta lístek protokolu Kerberos pro všechny relace](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).



Pokud necháte povoleno v režimu jádra, zlepšuje výkon operací protokolu Kerberos. Můžete ale také způsobuje, že lístek pro požadovaná služba k dešifrování pomocí účtu počítače. Tento účet je také označován místního systému. Nastavte tuto hodnotu na **True** přerušíte použitím KCD, když je aplikace hostované napříč více než jeden server ve farmě.

-   Jako další kontrolu, zakažte **rozšířené** ochrany příliš. V některých scénářích **rozšířené** ochrany překročila použitím KCD, když byl povolen v konkrétní konfigurace. V těchto případech byla aplikace publikována jako podsložkou výchozího webu. Tato aplikace je nakonfigurovaná pro pouze anonymní ověřování. V dialogových oknech jsou zobrazena šedě, což naznačuje, že podřízené objekty nebude dědit nastavení aktivní. Doporučujeme otestovat, ale nezapomeňte obnovení této hodnoty na **povoleno**, kde je to možné.

    Tuto další kontrolu můžete umístí na sledování sloužící k publikované aplikaci. Můžete začne pracovat další konektory, které jsou také nakonfigurovány pro delegování. Další informace najdete v tématu podrobnější technické návodu [řešení potíží s Azure AD Application Proxy](https://aka.ms/proxytshootpaper).

Pokud stále nemůžete provádět průběh, může vám pomůže podporu společnosti Microsoft. Vytvořte lístek podpory přímo v rámci portálu. Technika vás bude kontaktovat.

## <a name="other-scenarios"></a>Další scénáře

- Proxy aplikace služby Azure vyžádá lístek protokolu Kerberos před odesláním požadavku k aplikaci. Některé aplikace jiných výrobců, jako je Tableau Server nelíbí tuto metodu ověřování. Tyto aplikace očekávat konvenční jednání proběhla. První požadavek je anonymní, který umožňuje aplikaci odpovědět s typy ověřování, že podporuje prostřednictvím 401.

- Ověřování vícenásobného předávání se běžně používá ve scénářích kde vrstvené aplikace s back-end a front-endu, kde obě vyžadují ověřování, jako je SQL Server Reporting Services. Ke konfiguraci vícenásobným scénář, najdete v článku na podporu [omezeného delegování může vyžadovat Přechod protokolu Kerberos v scénáře vícenásobného předávání](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>Další postup
[Konfigurace použitím KCD na spravované doméně](../active-directory-domain-services/active-directory-ds-enable-kcd.md).
