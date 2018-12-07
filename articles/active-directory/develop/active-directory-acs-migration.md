---
title: Migrace z Azure Access Control Service | Dokumentace Microsoftu
description: Další informace o možnostech pro přesun aplikace a služby z Azure Access Control Service (ACS).
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/03/2018
ms.author: celested
ms.reviewer: jlu, annaba, hirsin
ms.openlocfilehash: e68099609e5a4a27dfae7956fa43634d38311a22
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015768"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Postupy: Migrace z Azure Access Control Service

Microsoft Azure Access Control Service (ACS), služba Azure Active Directory (Azure AD), se vyřadí dne 7. května 2018. Aplikací a služeb, které používají řízení přístupu musí být plně migrovat na jiný mechanismus ověřování té. Tento článek popisuje doporučení pro stávající zákazníky, plánujete přestat používat vaše užívání řízení přístupu. Pokud nepoužíváte aktuálně řízení přístupu, není nutné provádět žádnou akci.

## <a name="overview"></a>Přehled

Řízení přístupu je Cloudová služba ověřování, která nabízí snadný způsob, jak ověřovat a autorizovat uživatele pro přístup k webovým aplikacím a službám. To umožňuje mnoho funkcí ověřování a autorizace promítnout z kódu. Řízení přístupu používá primárně vývojářům a architektům klientů Microsoft .NET, webové aplikace ASP.NET a webových služeb Windows Communication Foundation (WCF).

Případy použití pro řízení přístupu můžete rozdělit do tří hlavních kategorií:

- Ověřování v některých cloudových služeb Microsoftu, včetně služby Azure Service Bus a Dynamics CRM. Klientských aplikací získat tokeny od řízení přístupu k ověřování k těmto službám provádět různé akce.
- Přidání ověřování do webové aplikace, vlastní a připravená (jako je SharePoint). Pomocí řízení přístupu "pasivní" ověřování webové aplikace může podporovat přihlášení pomocí účtu Microsoft (dřív Live ID) a pomocí účtů z Google, Facebooku, Yahoo, Azure AD a služby Active Directory Federation Services (AD FS).
- Zabezpečení vlastních webových služeb s tokeny vystavené službou Access Control. Webové služby s použitím "aktivní" ověřování, můžete zajistit, že se povolí přístup pouze k známí klienti, které jste se ověřili pomocí řízení přístupu.

Každá z těchto případů a jejich doporučená migrace, které jsou popsané strategie použít v následujících částech.

> [!WARNING]
> Ve většině případů jsou potřeba změny významné kódu pro migraci stávajících aplikací a služeb na novější technologií. Doporučujeme, abyste ihned začít plánovat a realizaci migrace předcházet veškerým potenciální výpadků nebo výpadek.

Řízení přístupu má následující komponenty:

- Služba tokenů zabezpečení (STS), které přijímá žádosti o ověření a na oplátku vydává tokeny zabezpečení.
- Portál Azure classic, kde můžete vytvořit, odstranit a povolit nebo zakázat obory názvů řízení přístupu.
- Samostatné řízení přístupu na portálu pro správu, kde přizpůsobení a konfiguraci oborů názvů řízení přístupu.
- Služba správy, který můžete použít k automatizaci funkce portály.
- Token transformace pravidlo modul, který můžete použít k definování komplexní logiku pro manipulaci s výstupního formátu tokeny, které vystavuje řízení přístupu.

Pokud chcete použít tyto komponenty, musíte vytvořit jeden nebo více oborů názvů řízení přístupu. A *obor názvů* je vyhrazená instance systému řízení přístupu, které vašim aplikacím a službám komunikovat. Obor názvů je izolovaná od všech ostatních zákazníků řízení přístupu. Ostatním zákazníkům řízení přístupu používat svůj vlastní obor názvů. Obor názvů řízení přístupu má vyhrazené adresy URL, který vypadá takto:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Veškerá komunikace s STS a operace správy se provádějí na této adrese URL. Použití různých cest pro různé účely. Pokud chcete zjistit, jestli vaše aplikace nebo služby, použití řízení přístupu, monitorování pro veškerý provoz na https://&lt;obor názvů&gt;. accesscontrol.windows.net. Veškerý provoz na tuto adresu URL zařizuje služba Access Control a musí být ukončena. 

Výjimkou je veškerý provoz do `https://accounts.accesscontrol.windows.net`. Provoz na tuto adresu URL již zpracovává jinou službou a **není** ovlivněny vyřazení řízení přístupu. 

Další informace o řízení přístupu najdete v tématu [2.0 služby Řízení přístupu (archivovaným)](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Zjistěte, které vaše aplikace bude mít vliv

Postupujte podle kroků v této části a zjistěte, které vaše aplikace bude mít vliv vyřazení z provozu služby ACS.

### <a name="download-and-install-acs-powershell"></a>Stáhněte a nainstalujte prostředí PowerShell služby ACS

1. Přejít do Galerie prostředí PowerShell a stáhnout [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
1. Nainstalujte modul spuštěním

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

1. Získat seznam všech možných příkazů

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Pokud chcete zobrazit nápovědu ke konkrétnímu příkazu, spusťte:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    kde `[Command-Name]` je název příkazu ACS.

### <a name="list-your-acs-namespaces"></a>Seznam obory názvů ACS

1. Připojte se k používání služby ACS **AcsAccount připojit** rutiny.
  
    Budete muset spustit `Set-ExecutionPolicy -ExecutionPolicy Bypass` před spuštěním příkazů, a být správce těchto předplatných za účelem provedení příkazu.

1. Dostupná předplatná Azure pomocí seznamu **Get-AcsSubscription** rutiny.
1. Seznam pomocí oborů názvů ACS **Get-AcsNamespace** rutiny.

### <a name="check-which-applications-will-be-impacted"></a>Zkontrolujte, které aplikace budou mít vliv

1. Obor názvů z předchozího kroku a přejděte na `https://<namespace>.accesscontrol.windows.net`

    Například pokud jedna z obory názvů contoso-test, přejděte na `https://contoso-test.accesscontrol.windows.net`

1. V části **vztahy důvěryhodnosti**vyberte **aplikace předávající strany** zobrazíte seznam aplikací, které budou mít vliv vyřazení z provozu služby ACS.
1. Opakujte kroky 1 – 2 pro všechny ostatní služby ACS namespace (s), které máte.

## <a name="retirement-schedule"></a>Plán vyřazení z provozu

Všechny součásti řízení přístupu k listopadu 2017, jsou plně podporované a provozní. Jediným omezením je to, že jste [nelze vytvořit nové obory názvů řízení přístupu prostřednictvím portálu Azure classic](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Tady je plán pro ukončení podpory pro řízení přístupu na komponenty:

- **. Listopadu 2017**: prostředí správce Azure AD na portálu Azure classic [byl vyřazen z provozu](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). V tomto okamžiku je k dispozici na adrese URL nového, vyhrazené Správa oboru názvů pro řízení přístupu: `https://manage.windowsazure.com?restoreClassic=true`. Zobrazit vaše stávající obory názvů, povolovat a zakazovat obory názvů a odstraňovat obory názvů, pokud budete chtít použijte tuto adresu URl.
- **2. dubnem 2018**: na portálu Azure classic portal byl zcela vyřazen z provozu, což znamená, Správa oboru názvů řízení přístupu už nejsou k dispozici prostřednictvím libovolnou adresu URL. V tuto chvíli nelze zakázat nebo povolit, odstranit nebo výčet obory názvů řízení přístupu. Ale na portálu pro správu řízení přístupu budou plně funkční a v `https://\<namespace\>.accesscontrol.windows.net`. Fungovat normálně dál všech ostatních součástí řízení přístupu.
- **7. listopadu 2018**: řízení přístupu na všechny komponenty jsou trvale vypnout. To zahrnuje na portálu pro správu řízení přístupu, služba správy, služba tokenů zabezpečení a stroj pravidel transformace token. V tomto okamžiku všechny požadavky odeslané na řízení přístupu (umístěný ve \<obor názvů\>. accesscontrol.windows.net) selžou. Měli jste migrovali všechny existující aplikace a služby na jiné technologie dobře před tímto časem.

> [!NOTE]
> Zásadu zakazuje obory názvů, které nebyly požadovaný token pro určitou dobu. Od počáteční. září 2018 tuto dobu je aktuálně na 14 dnů nečinnosti, ale to bude zkrátila na 7 dnů nečinnosti v následujících týdnech. Pokud máte obory názvů řízení přístupu, které jsou aktuálně zakázány, můžete si [stáhněte a nainstalujte prostředí PowerShell služby ACS](#download-and-install-acs-powershell) znovu povolit namespace (s).

## <a name="migration-strategies"></a>Strategie migrace

Následující části popisují základní doporučení pro migraci ze řízení přístupu na jiné technologie Microsoftu.

### <a name="clients-of-microsoft-cloud-services"></a>Klienti cloudovým službám Microsoftu

Každé cloudové službě Microsoftu, které přijímá tokeny, které jsou vydány službou Access Control, teď podporuje aspoň jeden alternativní formu ověřování. Mechanismus správné ověřování se liší u každé služby. Doporučujeme vám, že odkazujete na příslušnou část dokumentace k jednotlivým službám oficiální pokyny. Pro usnadnění práce každá sada dokumentace je k dispozici zde:

| Služba | Doprovodné materiály |
| ------- | -------- |
| Azure Service Bus | [Migrace na sdílených přístupových podpisů](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure Service Bus Relay | [Migrace na sdílených přístupových podpisů](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Azure Managed Cache | [Migrace na Azure Cache pro Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Služby Azure DataMarket | [Migrace na rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk Services | [Migrace do funkce Logic Apps služby Azure App Service](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Azure Media Services | [Migrace na ověřování Azure AD](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Upgrade agenta Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>Zákazníci služby SharePoint

SharePoint 2013, 2016, a Sharepointu Online zákazníci využili dlouho ACS pro účely ověřování v cloudu, místní a hybridní scénáře. Některé funkce služby SharePoint a případy použití ovlivňuje vyřazení z provozu služby ACS, zatímco jiné se tak nestane. Níže uvedená tabulka shrnuje pokyny k migraci pro některé z nejoblíbenějších SharePoint funkcí této služby ACS využívat:

| Funkce | Doprovodné materiály |
| ------- | -------- |
| Ověřování uživatelů ze služby Azure AD | Dříve Azure AD nepodporuje tokeny SAML 1.1 vyžaduje SharePoint pro ověřování a služby ACS se použil jako formátuje zprostředkovatele, který vytvořil token služby SharePoint s aktualizací s využitím Azure AD. Teď můžete [připojit přímo k Azure AD pomocí služby Azure AD App Gallery SharePoint na místní aplikace SharePoint](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial). |
| [Aplikace ověřování a ověřování na serveru v SharePoint v místním prostředí](https://technet.microsoft.com/library/jj219571(v=office.16).aspx) | Není ovlivněna vyřazení ACS; nejsou nutné žádné změny. | 
| [Vztah důvěryhodnosti s nízkou autorizace Sharepointových doplňků (Zprostředkovatel hostovaný a hostované služby SharePoint)](https://docs.microsoft.com/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Není ovlivněna vyřazení ACS; nejsou nutné žádné změny. |
| [Vyhledávání hybridní cloudové služby SharePoint](https://blogs.msdn.microsoft.com/spses/2015/09/15/cloud-hybrid-search-service-application/) | Není ovlivněna vyřazení ACS; nejsou nutné žádné změny. |

### <a name="web-applications-that-use-passive-authentication"></a>Webové aplikace, které používají pasivní ověřování

Pro webové aplikace, které používají řízení přístupu k ověřování uživatelů řízení přístupu poskytuje následující funkce a možnosti pro webové aplikace vývojářům a architektům:

- Těsnou integraci s Windows Identity Foundation (WIF).
- Federace se službou Google, Facebooku, Yahoo, Azure Active Directory a AD FS účty a účty Microsoft.
- Podpora pro následující protokoly pro ověřování: koncept 13 OAuth 2.0, WS-Trust a Web Services Federation (WS-Federation).
- Podpora pro token formátech: JSON Web Token (JWT), SAML 1.1, SAML 2.0 a Simple Web Token (SWT).
- Prostředí pro zjišťování domovské sféry, integrované do technologie WIF, který umožňuje uživatelům vybrat si typ účtu, které používají pro přihlášení. Toto prostředí je hostovaný webovou aplikací a je plně přizpůsobitelná.
- Token transformace, která umožňuje bohaté možnosti přizpůsobení deklarací přijatých webové aplikace z řízení přístupu, včetně:
    - Předávání deklarace identity od zprostředkovatelů identity.
    - Přidání dalších vlastních deklarací identity.
    - Jednoduché logiku if-then a vystavovat deklarace identity za určitých podmínek.

Bohužel není jedna služba, která nabízí všechny tyto funkce ekvivalentní. By měla vyhodnotit, jaké možnosti řízení přístupu potřebujete a klikněte na tlačítko mezi použitím [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C), nebo jiné cloudové ověřování Služba.

#### <a name="migrate-to-azure-active-directory"></a>Migrace do Azure Active Directory

Cesta ke zvážení integruje přímo s Azure AD s aplikacemi a službami. Azure AD je poskytovatelem cloudových identit pro Microsoft pracovní nebo školní účty. Azure AD je zprostředkovatel identity pro Office 365, Azure a spoustu dalších věcí. Poskytuje podobné federované ověřování možností na řízení přístupu, ale nepodporuje všechny funkce řízení přístupu. 

Primární příkladem je federace se službou zprostředkovatele sociální identity, jako je Facebook, Google a Yahoo. Pokud vaši uživatelé přihlásit pomocí těchto typů přihlašovacích údajů, není řešení za vás Azure AD. 

Azure AD nepodporuje ani nemusí být přesně stejné ověřovací protokoly jako řízení přístupu. Například i když řízení přístupu a Azure AD podporují OAuth, existují malé rozdíly každá implementace. Jedná o rozdílné implementace vyžadovat úpravy kódu jako součást migrace.

Azure AD poskytuje však řadu potenciálních výhod zákazníkům řízení přístupu. Je nativně podporuje Microsoft pracovní nebo školní účty hostované v cloudu, které se běžně používají zákazníci řízení přístupu. 

Klient služby Azure AD můžete také federovanou se jednu nebo víc instancí v místním Active Directory pomocí služby AD FS. Tímto způsobem, vaše aplikace můžete ověřovat uživatele, založené na cloudu a uživatelů, které jsou hostované místně. Také podporuje protokol WS-Federation, je poměrně přímočarý k integraci s webovou aplikací pomocí technologie WIF.

Následující tabulka porovnává funkce řízení přístupu, které jsou relevantní pro webové aplikace s funkcemi, které jsou k dispozici ve službě Azure AD. 

Na vysoké úrovni *Azure Active Directory je pravděpodobně nejlepší volbou pro migraci je-li umožnit uživatelům přihlášení v pouze s jejich Microsoft pracovní nebo školní účty*.

| Schopnost | Podpora řízení přístupu | Podpora Azure AD |
| ---------- | ----------- | ---------------- |
| **Typy účtů** | | |
| Microsoft pracovní nebo školní účty | Podporováno | Podporováno |
| Účty z Active Directory a AD FS ve Windows serveru |-Podporované prostřednictvím federace s tenantem Azure AD <br />-Podporované prostřednictvím přímé federace se službou AD FS | Podporuje jenom prostřednictvím federace s tenantem Azure AD | 
| Účty z jiných podnikových systémů správy identit |-Možný prostřednictvím federace s tenantem Azure AD <br />-Podporované prostřednictvím federace s přímým přístupem | Možný prostřednictvím federace s tenantem Azure AD |
| Účty Microsoft pro osobní použití | Podporováno | Podporované prostřednictvím Azure AD v2.0 protokolu OAuth, ale nikoli prostřednictvím dalších protokolů | 
| Účty služby Facebook, Google, Yahoo | Podporováno | Nepodporuje se jednání |
| **Protokoly a kompatibility sady SDK** | | |
| TECHNOLOGIE WIF | Podporováno | Podporované, ale omezené pokyny jsou k dispozici |
| WS-Federation | Podporováno | Podporováno |
| OAuth 2.0 | Podpora pro návrh 13 | Podpora pro RFC 6749, většina moderních specifikace |
| WS-Trust | Podporováno | Nepodporuje se |
| **Token formáty** | | |
| JWT | Podporované ve verzi Beta | Podporováno |
| SAML 1.1 | Podporováno | Preview |
| SAML 2.0 | Podporováno | Podporováno |
| SWT | Podporováno | Nepodporuje se |
| **Vlastní nastavení** | | |
| Přizpůsobitelné domovské sféry zjišťování/účet vybere uživatelského rozhraní | Ke stažení kódu, který lze začlenit do aplikace | Nepodporuje se |
| Nahrát vlastní podpisové certifikáty tokenu | Podporováno | Podporováno |
| Přizpůsobení deklarací identity v tokenech |-Předávání vstupní deklarace identity od zprostředkovatelů identity<br />-Získat přístupový token od zprostředkovatele identity jako deklarace identity<br />-Vydat výstupní deklarace identit podle hodnot ze vstupních deklarací identity<br />-Vystavovat deklarace identity výstup s konstantní hodnoty |-Nelze předat prostřednictvím deklarace identity od zprostředkovatelů federovaných identit<br />– Nelze získat přístupový token od zprostředkovatele identity jako deklarace identity<br />-Nemůžou vystavovat výstupní deklarace identit podle hodnot ze vstupních deklarací identity<br />-Můžou vystavovat deklarace identity výstup s konstantní hodnoty<br />-Můžou vystavovat deklarace identity výstup na základě vlastností uživatele synchronizují do Azure AD |
| **Automation** | | |
| Automatizace konfigurace a Správa úloh | Podporováno prostřednictvím služby pro správu řízení přístupu | Podporováno prostřednictvím Microsoft Graph a Azure AD Graph API |

Pokud se rozhodnete, že Azure AD je nejlepšího způsobu migrace vašich aplikací a služeb, byste měli vědět, dva způsoby, jak integrovat aplikace s Azure AD.

Integrace s Azure AD pomocí WS-Federation nebo technologie WIF, doporučujeme tento přístup je popsáno v následující [nakonfigurovat federované jednotné přihlašování pro aplikaci mimo galerii](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Tento článek odkazuje na konfigurace služby Azure AD pro založené na SAML jednotného přihlašování, ale také funguje pro konfiguraci WS-Federation. Tento způsob vyžaduje licenci Azure AD Premium. Tento přístup má dvě výhody:

- Získáte úplnou flexibilitu přizpůsobení tokenu Azure AD. Můžete přizpůsobit deklarace identity, které jsou vydány službou Azure AD tak, aby odpovídaly deklarace identity, které jsou vydány službou Access Control. To zahrnuje zejména uživatelské ID nebo název identifikátoru deklarace identity. Nadále získávat konzistentní uživatelské identifikátory pro vaše uživatele po změně technologie, zkontrolujte, zda uživatelské ID vydán Azure AD odpovídají vydávaných službou Access Control.
- Můžete nakonfigurovat certifikát pro podpis tokenu, která jsou specifická pro vaši aplikaci a s životností, který určujete vy.

> [!NOTE]
> Tento přístup vyžaduje licenci Azure AD Premium. Pokud jste zákazníkem Access Control a potřebujete licenci úrovně premium pro nastavení jednotného přihlašování pro aplikace, kontaktujte nás. Jsme rádi poskytovat vývojářské licence k použití.

Alternativním přístupem, je splnění [tento vzorový kód](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), což dává mírně odlišné pokyny pro nastavení WS-Federation. Tento vzorový kód nebude používat technologie WIF, ale místo toho middleware OWIN technologie ASP.NET 4.5. Pokyny pro registraci aplikace však platí pro aplikace pomocí technologie WIF a nevyžadují licenci Azure AD Premium. 

Pokud zvolíte tuto metodu, musíte pochopit [výměna podpisových klíčů ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Tento přístup používá globální podpisový klíč pro vydávání tokenů Azure AD. Ve výchozím nastavení technologie WIF neaktualizuje automaticky podpisové klíče. Když Azure AD otočí globální podpisového klíče, musí být připraveni přijmout změny vaší implementace technologie WIF. Další informace najdete v tématu [důležité informace o podepisování výměny klíčů ve službě Azure AD](https://msdn.microsoft.com/library/azure/dn641920.aspx).

Pokud můžete integrovat se službou Azure AD prostřednictvím protokoly OpenID Connect nebo OAuth, doporučujeme, abyste to. Máme k dispozici rozsáhlou dokumentaci a pokyny o tom, jak integrovat Azure AD do webové aplikace k dispozici v našich [Příručka pro vývojáře Azure AD](https://aka.ms/aaddev).

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migrace do Azure Active Directory B2C

Další cesty migrace vzít v úvahu je Azure AD B2C. Azure AD B2C je Cloudová služba ověřování, jako je řízení přístupu, umožňuje vývojářům externí správu logiky do cloudové služby, jejich ověřování a identita. Je placené služby (s úrovní free a premium), která je určená pro zákaznické aplikace, které může mít až na úrovni milionů aktivních uživatelů.

Podobně jako řízení přístupu jednou z nejvíce atraktivní funkcí Azure AD B2C je, že podporuje mnoho různých typů účtů. S Azure AD B2C můžete přihlásit uživatele pomocí svého účtu Microsoft nebo Facebook, Google, LinkedIn, GitHub nebo Yahoo účty a další. Azure AD B2C podporuje také "místní účty," nebo uživatelského jména a hesla, které uživatelé vytvářejí speciálně pro danou aplikaci. Azure AD B2C poskytuje také bohaté možnosti rozšiřitelnosti, který můžete použít k přizpůsobení vašich toků přihlaste. 

Však nepodporují škálu protokoly pro ověřování Azure AD B2C token a formáty a toto řízení přístupu, zákazníci můžou vyžadovat. Také nelze použít Azure AD B2C můžete získat tokeny a dotazů pro další informace o uživateli od zprostředkovatele identity, Microsoft nebo jinak.

Následující tabulka porovnává funkce řízení přístupu, které jsou relevantní pro webové aplikace s těmi, které jsou k dispozici v Azure AD B2C. Na vysoké úrovni *Azure AD B2C je pravděpodobně tou správnou volbou pro migraci Pokud je vaše aplikace přístupné spotřebitelům nebo pokud ji podporuje mnoho různých typů účtů.*

| Schopnost | Podpora řízení přístupu | Podpora Azure AD B2C |
| ---------- | ----------- | ---------------- |
| **Typy účtů** | | |
| Microsoft pracovní nebo školní účty | Podporováno | Podporováno prostřednictvím vlastních zásad  |
| Účty z Active Directory a AD FS ve Windows serveru | Podporováno prostřednictvím přímé federace se službou AD FS | Podporováno prostřednictvím SAML federace s využitím vlastních zásad |
| Účty z jiných podnikových systémů správy identit | Podporováno prostřednictvím přímé federace prostřednictvím WS-Federation | Podporováno prostřednictvím SAML federace s využitím vlastních zásad |
| Účty Microsoft pro osobní použití | Podporováno | Podporováno | 
| Účty služby Facebook, Google, Yahoo | Podporováno | Facebook nebo Google nepodporuje nativně, Yahoo podporované prostřednictvím federace s OpenID Connect s použitím vlastní zásady |
| **Protokoly a kompatibility sady SDK** | | |
| Windows Identity Foundation (WIF) | Podporováno | Nepodporuje se |
| WS-Federation | Podporováno | Nepodporuje se |
| OAuth 2.0 | Podpora pro návrh 13 | Podpora pro RFC 6749, většina moderních specifikace |
| WS-Trust | Podporováno | Nepodporuje se |
| **Token formáty** | | |
| JWT | Podporované ve verzi Beta | Podporováno |
| SAML 1.1 | Podporováno | Nepodporuje se |
| SAML 2.0 | Podporováno | Nepodporuje se |
| SWT | Podporováno | Nepodporuje se |
| **Vlastní nastavení** | | |
| Přizpůsobitelné domovské sféry zjišťování/účet vybere uživatelského rozhraní | Ke stažení kódu, který lze začlenit do aplikace | Plně přizpůsobitelná uživatelského rozhraní pomocí vlastní šablony stylů CSS |
| Nahrát vlastní podpisové certifikáty tokenu | Podporováno | Vlastní podpisové klíče, certifikáty, nejsou podporované prostřednictvím vlastních zásad |
| Přizpůsobení deklarací identity v tokenech |-Předávání vstupní deklarace identity od zprostředkovatelů identity<br />-Získat přístupový token od zprostředkovatele identity jako deklarace identity<br />-Vydat výstupní deklarace identit podle hodnot ze vstupních deklarací identity<br />-Vystavovat deklarace identity výstup s konstantní hodnoty |-Můžete předat prostřednictvím deklarace identity od zprostředkovatelů identity; vlastní zásady, vyžaduje se pro některé deklarace identity<br />– Nelze získat přístupový token od zprostředkovatele identity jako deklarace identity<br />-Může vydat výstupní deklarace identit podle hodnot ze vstupních deklarací identity pomocí vlastních zásad<br />-Můžou vystavovat deklarace identity výstup s konstantních hodnot pomocí vlastních zásad |
| **Automation** | | |
| Automatizace konfigurace a Správa úloh | Podporováno prostřednictvím služby pro správu řízení přístupu |– Vytváření uživatelů povoleno přes Azure AD Graph API<br />-Nelze vytvořit tenanti B2C, aplikace nebo zásady prostřednictvím kódu programu |

Pokud se rozhodnete, že Azure AD B2C je nejlepšího způsobu migrace vašich aplikací a služeb, začínají na následujících odkazech:

- [Dokumentace ke službě Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Vlastní zásady služby Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Ceny za Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrace na Ping Identity nebo Auth0

V některých případech můžete zjistit, Azure AD a Azure AD B2C nejsou dostatečná k nahrazení řízení přístupu ve vašich webových aplikacích bez provedení změn hlavní kód. Některé běžné příklady můžou zahrnovat:

- Webové aplikace, které používají technologii WIF nebo WS-Federation pro přihlášení pomocí zprostředkovatele sociální identity, jako je Google nebo Facebook.
- Webové aplikace, které provádějí federace s přímým přístupem ke zprostředkovateli identity enterprise přes protokol WS-Federation.
- Webové aplikace, které vyžadují přístupový token, Vystavitel zprostředkovatele identity v sociálních sítích (jako je Google nebo Facebook) jako deklarace identity v tokeny vystavené službou Access Control.
- Webové aplikace s pravidly komplexní token transformace, které Azure AD nebo Azure AD B2C nemůže reprodukovat.
- Víceklientské webové aplikace, které můžete centrálně spravovat federace na mnoho poskytovatelů jiná identita služby ACS

V těchto případech můžete chtít zvažte migraci vaší webové aplikace na jinou cloudovou službu ověřování. Doporučujeme, abyste zkoumání následující možnosti. Každá z těchto možností nabídky Možnosti podobný řízení přístupu:

|     |     | 
| --- | --- |
| ![Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs) je flexibilní Cloudová služba identit, která si vytvořila [pokyny k migraci vysoké úrovně pro zákazníky využívající řízení přístupu](https://auth0.com/acs)a podporuje téměř všechny funkce, která provádí služby ACS. |
| ![Ping](./media/active-directory-acs-migration/rsz_ping.png) | [Ping Identity](https://www.pingidentity.com) nabízí dvě řešení podobný služby ACS. PingOne je Cloudová služba identit, který podporuje mnoho stejných funkcí jako služby ACS a služby PingFederate je podobný produkt identity v místním prostředí, která nabízí vyšší flexibilitu. Odkazovat na [pokyny vyřazení z provozu služby ACS na příkaz Ping](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) pro další podrobnosti o použití těchto produktů. |

Cílem naší práce s Ping Identity a Auth0 je zajistit, aby měli všichni zákazníci řízení přístupu na cestu migrace pro své aplikace a služby, který minimalizuje množství práce potřebné k přesunutí z řízení přístupu.

<!--

## Sharepoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Webové služby, které používají ověřování active

Řízení přístupu pro webové služby, které jsou zabezpečené pomocí tokeny vystavené službou Access Control, nabízí následující funkce a možnosti:

- Možnost registrovat jednu nebo více *identity služby* ve vašem oboru názvů řízení přístupu. Služba identit umožňuje požádat o tokeny.
- Podpora pro OBTÉKÁNÍ OAuth a OAuth 2.0 koncept 13 protokoly žádosti o tokeny, pomocí následujících typů přihlašovacích údajů:
    - Jednoduchá hesla, který je vytvořen pro identitu služby
    - Znaménkem SWT pomocí symetrického klíče nebo X509 certifikátu
    - Tokenu SAML vydaném pomocí důvěryhodného zprostředkovatele identity (obvykle, instance služby AD FS)
- Podpora pro token formátech: token JWT, SAML 1.1, SAML 2.0 nebo SWT.
- Pravidla transformace jednoduché tokenu.

Identita služby Access Control se obvykle používají k implementaci serveru pro ověřování. 

#### <a name="migrate-to-azure-active-directory"></a>Migrace do Azure Active Directory

Naše doporučení pro tento typ toku ověřování je migrovat do [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/). Azure AD je poskytovatelem cloudových identit pro Microsoft pracovní nebo školní účty. Azure AD je zprostředkovatel identity pro Office 365, Azure a spoustu dalších věcí. 

Můžete také použít Azure AD pro ověřování na serveru pomocí služby Azure AD provádění udělování přihlašovacích údajů klienta OAuth. Následující tabulka porovnává funkce řízení přístupu k serveru ověřování s těmi, které jsou k dispozici ve službě Azure AD.

| Schopnost | Podpora řízení přístupu | Podpora Azure AD |
| ---------- | ----------- | ---------------- |
| Postup při registraci webové služby | Vytvořit předávající strany v portálu pro správu řízení přístupu | Vytvořit webovou aplikaci Azure AD na webu Azure Portal |
| Postup při registraci klienta | Vytvoření identity služby v portálu pro správu řízení přístupu | Vytvořte jinou webovou aplikaci Azure AD na webu Azure Portal |
| Protokol použitý |– Protokol OAuth OBTÉKÁNÍ<br />-Udělení přihlašovacích údajů klienta 13 koncept OAuth 2.0 | Udělení přihlašovacích údajů klienta OAuth 2.0 |
| Metody ověřování klientů |-Jednoduché heslo<br />-Podepsaný SWT<br />-Token SAML od poskytovatele federované identity |-Jednoduché heslo<br />-Podepsaný token JWT |
| Token formáty |- JWT<br />-SAML 1.1<br />-SAML 2.0<br />-SWT<br /> | Pouze tokenů JWT |
| Token transformace |-Přidat vlastní deklarace identity<br />– Logiky vystavení deklarace identity jednoduché if-then | Přidat vlastní deklarace identity | 
| Automatizace konfigurace a Správa úloh | Podporováno prostřednictvím služby pro správu řízení přístupu | Podporováno prostřednictvím Microsoft Graph a Azure AD Graph API |

Pokyny k implementaci scénářů na serveru naleznete v následujících zdrojích:

- Části služba-služba [Příručka pro vývojáře Azure AD](https://aka.ms/aaddev)
- [Démon procesu vzorový kód s použitím přihlašovacích údajů klienta jednoduché heslo](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Vzorový kód démona pomocí certifikátu klienta přihlašovacích údajů](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrace na Ping Identity nebo Auth0

V některých případech můžete zjistit, že přihlašovací údaje klienta služby Azure AD a OAuth udělit nejsou dostatečná k nahrazení řízení přístupu ve vaší architektuře bez jakýchkoli změn kódu hlavní implementace. Některé běžné příklady můžou zahrnovat:

- Na serveru ověřování pomocí tokenu formáty kromě tokeny Jwt.
- Na serveru ověřování pomocí tokenu vstupní poskytované externího zprostředkovatele identity.
- Na serveru ověřování pomocí tokenu transformace pravidla, které nelze reprodukovat Azure AD.

V těchto případech zvažte migraci vaší webové aplikace na jinou cloudovou službu ověřování. Doporučujeme, abyste zkoumání následující možnosti. Každá z těchto možností nabídky Možnosti podobný řízení přístupu:

|     |     | 
| --- | --- |
| ![Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs) je flexibilní Cloudová služba identit, která si vytvořila [pokyny k migraci vysoké úrovně pro zákazníky využívající řízení přístupu](https://auth0.com/acs)a podporuje téměř všechny funkce, která provádí služby ACS. |
| ![Ping](./media/active-directory-acs-migration/rsz_ping.png) | [Ping Identity](https://www.pingidentity.com) nabízí dvě řešení podobný služby ACS. PingOne je Cloudová služba identit, který podporuje mnoho stejných funkcí jako služby ACS a služby PingFederate je podobný produkt identity v místním prostředí, která nabízí vyšší flexibilitu. Odkazovat na [pokyny vyřazení z provozu služby ACS na příkaz Ping](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) pro další podrobnosti o použití těchto produktů. |

Cílem naší práce s Ping Identity a Auth0 je zajistit, aby měli všichni zákazníci řízení přístupu na cestu migrace pro své aplikace a služby, který minimalizuje množství práce potřebné k přesunutí z řízení přístupu.

#### <a name="passthrough-authentication"></a>Předávacího ověřování

Předávacího ověřování s libovolného token transformace není žádná ekvivalentní technologii Microsoftu pro služby ACS. Pokud je to, co vaši zákazníci potřebují, Auth0 může být ten, který má nejvíce aproximace řešení.

## <a name="questions-concerns-and-feedback"></a>Otázky, otázky a zpětná vazba

Chápeme, mnoho zákazníků řízení přístupu nenajdete migraci po přečtení tohoto článku. Může být nutné některé pomoc nebo pokynů k určení správného plánu. Pokud chcete probrat scénáře migrace a dotazy, uveďte ji v poznámce na této stránce.
