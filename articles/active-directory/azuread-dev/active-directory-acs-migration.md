---
title: Migrace ze služby Řízení přístupu Azure | Dokumenty společnosti Microsoft
description: Další informace o možnostech přesouvání aplikací a služeb ze služby Azure Access Control Service (ACS).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.date: 10/03/2018
ms.author: ryanwi
ms.reviewer: jlu, annaba, hirsin
ROBOTS: NOINDEX
ms.openlocfilehash: 3168d36bf4c2d3c696173725f669b12dc168dcc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154997"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Postup: Migrace ze služby Řízení přístupu Azure

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Služba Microsoft Azure Access Control Service (ACS), služba Azure Active Directory (Azure AD), bude vyřazena 7. Aplikace a služby, které aktuálně používají řízení přístupu, musí být do té doby plně přeneseny do jiného mechanismu ověřování. Tento článek popisuje doporučení pro aktuální zákazníky, protože máte v plánu zapojit použití řízení přístupu. Pokud v současné době nepoužíváte řízení přístupu, nemusíte provádět žádnou akci.

## <a name="overview"></a>Přehled

Řízení přístupu je cloudová ověřovací služba, která nabízí snadný způsob ověřování a autorizace uživatelů pro přístup k webovým aplikacím a službám. Umožňuje, aby mnoho funkcí ověřování a autorizace bylo započítáno z vašeho kódu. Řízení přístupu používají především vývojáři a architekti klientů Microsoft .NET, ASP.NET webových aplikací a webových služeb WCF (Windows Communication Foundation).

Případy použití pro řízení přístupu lze rozdělit do tří hlavních kategorií:

- Ověřování některých cloudových služeb Microsoftu, včetně Azure Service Bus a Dynamics CRM. Klientské aplikace získat tokeny z řízení přístupu k ověření těchto služeb provádět různé akce.
- Přidání ověřování do webových aplikací, vlastních i předbalených (například SharePoint). Pomocí "pasivního" ověřování řízení přístupu mohou webové aplikace podporovat přihlášení pomocí účtu Microsoft (dříve Live ID) a s účty od Společností Google, Facebook, Yahoo, Azure AD a Služby AD FS (AD FS).
- Zabezpečení vlastních webových služeb tokeny vydanými řízením přístupu. Pomocí "aktivního" ověřování mohou webové služby zajistit, že umožňují přístup pouze známým klientům, kteří se ověřili pomocí řízení přístupu.

Každý z těchto případů použití a jejich doporučené strategie migrace jsou popsány v následujících částech.

> [!WARNING]
> Ve většině případů jsou nutné významné změny kódu k migraci existujících aplikací a služeb do novějších technologií. Doporučujeme okamžitě začít plánovat a provádět migraci, abyste se vyhnuli případným výpadkům nebo výpadkům.

Řízení přístupu má následující součásti:

- Služba zabezpečeného tokenu (STS), která přijímá požadavky na ověření a na oplátku vydává tokeny zabezpečení.
- Klasický portál Azure, kde můžete vytvářet, odstraňovat a povolovat a zakazovat obory názvů řízení přístupu.
- Samostatný portál pro správu řízení přístupu, kde můžete přizpůsobit a nakonfigurovat obory názvů řízení přístupu.
- Služba pro správu, kterou můžete použít k automatizaci funkcí portálů.
- Modul pravidel transformace tokenu, který můžete použít k definování složité logiky k manipulaci s výstupním formátem tokenů, které problémy s řízením přístupu.

Chcete-li tyto součásti používat, je nutné vytvořit jeden nebo více oborů názvů řízení přístupu. *Obor názvů* je vyhrazená instance řízení přístupu, se kterou vaše aplikace a služby komunikují. Obor názvů je izolován od všech ostatních zákazníků řízení přístupu. Jiní zákazníci řízení přístupu používají vlastní obory názvů. Obor názvů v řízení přístupu má vyhrazenou adresu URL, která vypadá takto:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Veškerá komunikace s PROVOZS a operace správy jsou prováděny na této adrese URL. Různé cesty se používají pro různé účely. Chcete-li zjistit, zda aplikace nebo služby používají&lt;řízení&gt;přístupu, sledujte všechny přenosy, které mají https:// oboru názvů .accesscontrol.windows.net. Veškerý provoz na tuto adresu URL je zpracován řízením přístupu a je třeba je ukončit. 

Výjimkou je jakýkoli provoz `https://accounts.accesscontrol.windows.net`na . Provoz na tuto adresu URL je již zpracovánji jinou službou a **není** ovlivněn vyřazením řízení přístupu. 

Další informace o řízení přístupu naleznete v [tématu Access Control Service 2.0 (archivováno).](https://msdn.microsoft.com/library/hh147631.aspx)

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Zjistěte, které z vašich aplikací budou mít vliv

Podle pokynů v této části zjistíte, které z vašich aplikací budou mít vliv na vyřazení služby ACS.

### <a name="download-and-install-acs-powershell"></a>Stažení a instalace prostředí ACS PowerShell

1. Přejděte do Galerie prostředí PowerShell a stáhněte si [položku Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
2. Nainstalujte modul spuštěním

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

3. Získejte seznam všech možných příkazů spuštěním

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Chcete-li získat nápovědu k určitému příkazu, spusťte:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    kde `[Command-Name]` je název příkazu ACS.

### <a name="list-your-acs-namespaces"></a>Seznam oborů názvů acs

1. Připojte se ke službě ACS pomocí rutiny **Connect-AcsAccount.**
  
    Možná budete muset `Set-ExecutionPolicy -ExecutionPolicy Bypass` spustit před spuštěním příkazů a být správcem těchto předplatných, aby bylo možné příkazy spustit.

2. Seznam dostupných předplatných Azure pomocí rutiny **Get-AcsSubscription.**
3. Seznam jmenných prostorů služby ACS pomocí rutiny **Get-AcsNamespace.**

### <a name="check-which-applications-will-be-impacted"></a>Zkontrolujte, které aplikace budou ovlivněny

1. Použijte obor názvů z předchozího kroku a přejděte na`https://<namespace>.accesscontrol.windows.net`

    Pokud je například jeden z oborů názvů contoso-test, přejděte na`https://contoso-test.accesscontrol.windows.net`

2. V části **Vztahy důvěryhodnosti**vyberte **možnost Přepínat aplikace strany,** chcete-li zobrazit seznam aplikací, které budou ovlivněny vyřazením služby ACS.
3. Opakujte kroky 1-2 pro všechny ostatní obory názvů acs, které máte.

## <a name="retirement-schedule"></a>Důchodový plán

Od listopadu 2017 jsou všechny součásti řízení přístupu plně podporovány a funkční. Jediným omezením je, že [nelze vytvořit nové obory názvů řízení přístupu prostřednictvím klasického portálu Azure](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Tady je plán pro zastaralé součásti řízení přístupu:

- **Listopad 2017**: Prostředí pro správu Azure AD na klasickém portálu Azure [je vyřazené](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). V tomto okamžiku je správa oboru názvů pro řízení přístupu k dispozici na nové vyhrazené adrese URL: `https://manage.windowsazure.com?restoreClassic=true`. Tento upulinslouží k zobrazení existujících oborů názvů, povolení a zakázání oborů názvů a k odstranění oborů názvů, pokud se tak rozhodnete.
- **2. dubna 2018**: Klasický portál Azure je kompletně vyřazený, což znamená, že správa oboru názvů řízení přístupu už není dostupná přes žádnou adresu URL. V tomto okamžiku nelze zakázat nebo povolit, odstranit nebo výčet oborů názvů řízení přístupu. Portál správy řízení přístupu však bude `https://\<namespace\>.accesscontrol.windows.net`plně funkční a bude umístěn na adrese . Všechny ostatní součásti řízení přístupu nadále fungují normálně.
- **7. listopadu 2018**: Všechny součásti řízení přístupu jsou trvale vypnuty. To zahrnuje portál správy řízení přístupu, službu pro správu, STS a modul pravidel transformace tokenu. V tomto okamžiku se nezdaří všechny požadavky \<odeslané řízení přístupu (umístěné v oboru názvů\>.accesscontrol.windows.net). Měli jste migrovat všechny existující aplikace a služby do jiných technologií v dobré masivu do této doby.

> [!NOTE]
> Zásada zakáže obory názvů, které nepožadovaly token po určitou dobu. Od začátku září 2018 je toto časové období v současné době na 14 dnech nečinnosti, ale v nadcházejících týdnech bude zkráceno na 7 dní nečinnosti. Pokud máte aktuálně zakázané obory názvů řízení přístupu, můžete [stáhnout a nainstalovat prostředí ACS PowerShell](#download-and-install-acs-powershell) a znovu povolit obory názvů.

## <a name="migration-strategies"></a>Strategie migrace

Následující části popisují doporučení vysoké úrovně pro migraci z řízení přístupu do jiných technologií společnosti Microsoft.

### <a name="clients-of-microsoft-cloud-services"></a>Klienti cloudových služeb Microsoftu

Každá cloudová služba Microsoftu, která přijímá tokeny vydané řízením přístupu, nyní podporuje alespoň jednu alternativní formu ověřování. Správný mechanismus ověřování se liší pro každou službu. Doporučujeme, abyste se pro oficiální pokyny mohli podívat do konkrétní dokumentace pro každou službu. Pro větší pohodlí je zde k dispozici každá sada dokumentace:

| Služba | Doprovodné materiály |
| ------- | -------- |
| Azure Service Bus | [Migrace na sdílené přístupové podpisy](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Relé azure sběrnice | [Migrace na sdílené přístupové podpisy](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Spravovaná mezipaměť Azure | [Migrace do mezipaměti Azure pro Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Datový trh Azure | [Migrace do api kognitivních služeb](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk služby | [Migrace do funkce Logic Apps služby Azure App Service](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Mediální služby Azure | [Migrace na ověřování Azure AD](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Upgrade agenta azure backup](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>Zákazníci služby SharePoint

Zákazníci SharePointu 2013, 2016 a SharePointu Online už dlouho používají Službu ACS pro účely ověřování v cloudu, v místním prostředí a hybridních scénářích. Některé funkce a případy použití služby SharePoint budou ovlivněny vyřazením služby ACS, zatímco jiné nikoli. Níže uvedená tabulka shrnuje pokyny k migraci pro některé z nejpopulárnějších funkcí SharePointu, které využívají službu ACS:

| Funkce | Doprovodné materiály |
| ------- | -------- |
| Ověřování uživatelů z Azure AD | Dříve Azure AD nepodporoval saml 1.1 tokeny vyžadované SharePoint pro ověřování a ACS se používá jako zprostředkovatel, který dělal SharePoint kompatibilní s formáty tokenů Azure AD. Teď můžete [SharePoint připojit přímo k Azure AD pomocí aplikace Azure AD App Gallery SharePoint v místním prostředí](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial). |
| [Ověřování aplikací & ověřování mezi servery v SharePointu v místním prostředí](https://technet.microsoft.com/library/jj219571(v=office.16).aspx) | Není ovlivněna odchodem Do důchodu ACS; není nutné provést žádné změny. | 
| [Autorizace s nízkou důvěryhodností pro doplňky SharePointu (hostovaný poskytovatel a hostovaný SharePoint)](https://docs.microsoft.com/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Není ovlivněna odchodem Do důchodu ACS; není nutné provést žádné změny. |
| [Hybridní vyhledávání v cloudu SharePointu](https://blogs.msdn.microsoft.com/spses/2015/09/15/cloud-hybrid-search-service-application/) | Není ovlivněna odchodem Do důchodu ACS; není nutné provést žádné změny. |

### <a name="web-applications-that-use-passive-authentication"></a>Webové aplikace používající pasivní ověřování

Pro webové aplikace, které používají řízení přístupu pro ověřování uživatelů, poskytuje řízení přístupu vývojářům a architektům webových aplikací následující funkce a možnosti:

- Hluboká integrace s Windows Identity Foundation (WIF).
- Federaci s účty Google, Facebook, Yahoo, Azure Active Directory a AD FS a Účty Microsoft.
- Podpora následujících ověřovacích protokolů: OAuth 2.0 Draft 13, WS-Trust a Federation (WS-Federation).
- Podpora pro následující formáty tokenů: JSON Web Token (JWT), SAML 1.1, SAML 2.0 a Simple Web Token (SWT).
- Prostředí pro zjišťování domovské sféry integrované do wif, které uživatelům umožňuje vybrat typ účtu, který používají k přihlášení. Toto prostředí je hostováno webovou aplikací a je plně přizpůsobitelné.
- Transformace tokenu, která umožňuje bohaté přizpůsobení deklarací přijatých webovou aplikací z řízení přístupu, včetně:
    - Předají deklarace identity od poskytovatelů identity.
    - Přidání dalších vlastních deklarací.
    - Jednoduché if-then logika vydávat deklarace identity za určitých podmínek.

Bohužel neexistuje jedna služba, která nabízí všechny tyto ekvivalentní funkce. Měli byste vyhodnotit, které možnosti řízení přístupu potřebujete, a pak si vybrat mezi použitím [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), Azure Active Directory [B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) nebo jiné cloudové ověřovací služby.

#### <a name="migrate-to-azure-active-directory"></a>Migrace do služby Azure Active Directory

Cesta, kterou je třeba zvážit, je integrace aplikací a služeb přímo s Azure AD. Azure AD je poskytovatel cloudových identit pro pracovní nebo školní účty Microsoftu. Azure AD je poskytovatel identit pro Office 365, Azure a mnoho dalšího. Poskytuje podobné funkce federovaného ověřování pro řízení přístupu, ale nepodporuje všechny funkce řízení přístupu. 

Primárním příkladem je federace s poskytovateli sociální identity, jako je Facebook, Google a Yahoo. Pokud se uživatelé přihlašují pomocí těchto typů přihlašovacích údajů, Azure AD není řešení pro vás. 

Azure AD také nemusí nutně podporovat přesně stejné ověřovací protokoly jako řízení přístupu. Například i když řízení přístupu a Azure AD podporují OAuth, existují jemné rozdíly mezi každou implementaci. Různé implementace vyžadují úpravu kódu jako součást migrace.

Azure AD však poskytuje několik potenciálních výhod zákazníkům řízení přístupu. Nativně podporuje pracovní nebo školní účty Microsoft hostované v cloudu, které zákazníci řízení přístupu běžně používají. 

Klient Azure AD může být také federován do jedné nebo více instancí místní služby Active Directory prostřednictvím služby AD FS. Tímto způsobem vaše aplikace můžete ověřit cloudové uživatele a uživatele, kteří jsou hostované v místním prostředí. Podporuje také protokol WS-Federation, díky němuž je relativně jednoduché integrovat s webovou aplikací pomocí WIF.

Následující tabulka porovnává funkce řízení přístupu, které jsou relevantní pro webové aplikace s funkcemi, které jsou k dispozici ve službě Azure AD. 

Na vysoké úrovni *je služba Azure Active Directory pravděpodobně nejlepší volbou pro vaši migraci, pokud uživatelům umožníte přihlásit se pouze pomocí svých pracovních nebo školních účtů Microsoftu*.

| Schopnost | Podpora řízení přístupu | Podpora Azure AD |
| ---------- | ----------- | ---------------- |
| **Typy účtů** | | |
| Pracovní nebo školní účty Microsoftu | Podporuje se | Podporuje se |
| Účty ze služby Windows Server Active Directory a služby AD FS |- Podporováno prostřednictvím federace s klientem Azure AD <br />- Podporováno prostřednictvím přímé federace s AD FS | Podporované jenom prostřednictvím federace s klientem Azure AD | 
| Účty z jiných podnikových systémů správy identit |- Možné prostřednictvím federace s tenantem Azure AD <br />- Podporováno prostřednictvím přímé federace | Možné prostřednictvím federace s tenantem Azure AD |
| Účty Microsoft pro osobní použití | Podporuje se | Podporováno prostřednictvím protokolu OAuth služby Azure AD v2.0, ale ne přes žádné jiné protokoly | 
| Facebook, Google, Yahoo účty | Podporuje se | Není podporováno vůbec |
| **Protokoly a kompatibilita sady SDK** | | |
| WIF | Podporuje se | Podporováno, ale k dispozici jsou omezené pokyny. |
| WS-Federation | Podporuje se | Podporuje se |
| OAuth 2.0 | Podpora návrhu 13 | Podpora rfc 6749, nejmodernější specifikace |
| WS-Trust | Podporuje se | Nepodporuje se |
| **Formáty tokenů** | | |
| JWT | Podporováno v beta verzi | Podporuje se |
| SAML 1,1 | Podporuje se | Preview |
| SAML 2.0 | Podporuje se | Podporuje se |
| Swt | Podporuje se | Nepodporuje se |
| **Přizpůsobení** | | |
| Přizpůsobitelné domácí sféry discovery / account-picking UI | Kód ke stažení, který lze začlenit do aplikací | Nepodporuje se |
| Nahrání vlastních podpisových certifikátů tokenů | Podporuje se | Podporuje se |
| Přizpůsobení deklarací identity v tokenech |- Průchod vstupních deklarací od poskytovatelů identity<br />- Získat přístupový token od poskytovatele identity jako deklaraci identity<br />- Vydávat výstupní pohledávky na základě hodnot vstupních pohledávek<br />- Vydávat výstupní deklarace s konstantními hodnotami |- Nelze předat nároky od poskytovatelů federovaných identit.<br />- Nelze získat přístupový token od poskytovatele identity jako deklaraci identity.<br />- Nelze vydávat výstupní deklarace na základě hodnot vstupních deklarací.<br />- Může vydávat výstupní deklarace s konstantními hodnotami<br />- Může vydávat výstupní deklarace na základě vlastností uživatelů synchronizovaných s Azure AD |
| **Automatizace** | | |
| Automatizace úloh konfigurace a správy | Podporováno prostřednictvím služby řízení přístupu | Podporováno pomocí rozhraní Microsoft Graph API |

Pokud se rozhodnete, že Azure AD je nejlepší cesta migrace pro vaše aplikace a služby, měli byste si být vědomi dvou způsobů, jak integrovat vaši aplikaci s Azure AD.

Chcete-li použít WS-Federation nebo WIF k integraci s Azure AD, doporučujeme dodržovat přístup popsaný v [konfigurovat federované jednotné přihlašování pro aplikaci bez galerie](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Tento článek odkazuje na konfiguraci Azure AD pro jednotné přihlašování založené na SAML, ale funguje také pro konfiguraci WS-Federation. Podle tohoto přístupu vyžaduje licenci Azure AD Premium. Tento přístup má dvě výhody:

- Získáte plnou flexibilitu přizpůsobení tokenu Azure AD. Můžete přizpůsobit deklarace identity, které jsou vydané Službou Azure AD tak, aby odpovídaly deklaracím, které jsou vydané řízením přístupu. To zahrnuje zejména ID uživatele nebo deklarace identifikátoru názvu. Chcete-li i nadále přijímat konzistentní uživatelské IDentifiers pro vaše uživatele po změně technologií, ujistěte se, že ID uživatelů vydané Azure AD odpovídají těm, které vydává řízení přístupu.
- Můžete nakonfigurovat podpisový certifikát tokenu, který je specifický pro vaši aplikaci a s životností, kterou ovládáte.

> [!NOTE]
> Tento přístup vyžaduje licenci Azure AD Premium. Pokud jste zákazníkem řízení přístupu a požadujete prémiovou licenci pro nastavení jednotného přihlášení k aplikaci, kontaktujte nás. Rádi vám poskytneme vývojářské licence.

Alternativní přístup je postupujte podle [tohoto kódu ukázku](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), která poskytuje mírně odlišné pokyny pro nastavení WS-Federation. Tato ukázka kódu nepoužívá WIF, ale spíše ASP.NET middleware 4.5 OWIN. Pokyny pro registraci aplikací jsou však platné pro aplikace používající WIF a nevyžadují licenci Azure AD Premium. 

Pokud zvolíte tento přístup, musíte [pochopit, podpisový klíč rollover ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Tento přístup používá globální podpisový klíč Azure AD k vydávání tokenů. Ve výchozím nastavení wif automaticky neaktualizuje podpisové klíče. Když Azure AD střídá své globální podpisové klíče, vaše implementace WIF musí být připravena přijmout změny. Další informace najdete [v tématu Důležité informace o podepisování klíče rollover ve službě Azure AD](https://msdn.microsoft.com/library/azure/dn641920.aspx).

Pokud můžete integrovat s Azure AD prostřednictvím protokolů OpenID Connect nebo OAuth, doporučujeme tak. Máme rozsáhlou dokumentaci a pokyny o tom, jak integrovat Azure AD do webové aplikace, která je dostupná v našem [průvodci pro vývojáře Azure AD](https://aka.ms/aaddev).

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migrace do služby Azure Active Directory B2C

Další cesta migrace, kterou je třeba zvážit, je Azure AD B2C. Azure AD B2C je cloudová ověřovací služba, která stejně jako řízení přístupu umožňuje vývojářům zadat logiku ověřování a správy identit do cloudové služby. Jedná se o placenou službu (s bezplatnými a prémiovými úrovněmi), která je určena pro spotřebitelské aplikace, které mohou mít až miliony aktivních uživatelů.

Stejně jako řízení přístupu, jednou z nejatraktivnějších funkcí Azure AD B2C je, že podporuje mnoho různých typů účtů. S Azure AD B2C se můžete přihlásit pomocí jejich účtu Microsoft nebo účtů Facebook, Google, LinkedIn, GitHub nebo Yahoo a dalších. Azure AD B2C také podporuje "místní účty" nebo uživatelské jméno a hesla, které uživatelé vytvářejí speciálně pro vaši aplikaci. Azure AD B2C také poskytuje bohaté rozšiřitelnost, které můžete použít k přizpůsobení toků přihlášení. 

Azure AD B2C však nepodporuje šíři ověřovacích protokolů a formátů tokenů, které mohou zákazníci řízení přístupu vyžadovat. Také nelze použít Azure AD B2C získat tokeny a dotaz na další informace o uživateli od poskytovatele identity, Microsoft nebo jinak.

Následující tabulka porovnává funkce řízení přístupu, které jsou relevantní pro webové aplikace s těmi, které jsou k dispozici v Azure AD B2C. Na vysoké úrovni *Azure AD B2C je pravděpodobně správná volba pro migraci, pokud vaše aplikace čelí spotřebiteli, nebo pokud podporuje mnoho různých typů účtů.*

| Schopnost | Podpora řízení přístupu | Podpora Azure AD B2C |
| ---------- | ----------- | ---------------- |
| **Typy účtů** | | |
| Pracovní nebo školní účty Microsoftu | Podporuje se | Podporováno pomocí vlastních zásad  |
| Účty ze služby Windows Server Active Directory a služby AD FS | Podporováno prostřednictvím přímé federace se službou AD FS | Podporováno prostřednictvím federace SAML pomocí vlastních zásad |
| Účty z jiných podnikových systémů správy identit | Podporováno prostřednictvím přímé federace prostřednictvím WS-Federation | Podporováno prostřednictvím federace SAML pomocí vlastních zásad |
| Účty Microsoft pro osobní použití | Podporuje se | Podporuje se | 
| Facebook, Google, Yahoo účty | Podporuje se | Facebook a Google podporovány nativně, Yahoo podporované prostřednictvím OpenID Connect federace pomocí vlastních zásad |
| **Protokoly a kompatibilita sady SDK** | | |
| Windows Identity Foundation (WIF) | Podporuje se | Nepodporuje se |
| WS-Federation | Podporuje se | Nepodporuje se |
| OAuth 2.0 | Podpora návrhu 13 | Podpora rfc 6749, nejmodernější specifikace |
| WS-Trust | Podporuje se | Nepodporuje se |
| **Formáty tokenů** | | |
| JWT | Podporováno v beta verzi | Podporuje se |
| SAML 1,1 | Podporuje se | Nepodporuje se |
| SAML 2.0 | Podporuje se | Nepodporuje se |
| Swt | Podporuje se | Nepodporuje se |
| **Přizpůsobení** | | |
| Přizpůsobitelné domácí sféry discovery / account-picking UI | Kód ke stažení, který lze začlenit do aplikací | Plně přizpůsobitelné ui přes vlastní CSS |
| Nahrání vlastních podpisových certifikátů tokenů | Podporuje se | Vlastní podpisové klíče, nikoli certifikáty, podporované pomocí vlastních zásad |
| Přizpůsobení deklarací identity v tokenech |- Průchod vstupních deklarací od poskytovatelů identity<br />- Získat přístupový token od poskytovatele identity jako deklaraci identity<br />- Vydávat výstupní pohledávky na základě hodnot vstupních pohledávek<br />- Vydávat výstupní deklarace s konstantními hodnotami |- Může projít prostřednictvím pohledávek od poskytovatelů identity; vlastní zásady požadované pro některé deklarace identity<br />- Nelze získat přístupový token od poskytovatele identity jako deklaraci identity.<br />- Může vydávat výstupní deklarace na základě hodnot vstupních deklarací prostřednictvím vlastních zásad<br />- Může vydávat výstupní deklarace s konstantními hodnotami prostřednictvím vlastních zásad |
| **Automatizace** | | |
| Automatizace úloh konfigurace a správy | Podporováno prostřednictvím služby řízení přístupu |- Vytváření uživatelů povolených pomocí Rozhraní API Microsoft Graph<br />- Nelze programově vytvořit klienty, aplikace nebo zásady B2C. |

Pokud se rozhodnete, že Azure AD B2C je nejlepší cesta migrace pro vaše aplikace a služby, začněte s následujícími prostředky:

- [Dokumentace Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Vlastní zásady Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Ceny Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrace na identitu příkazu Ping nebo auth0

V některých případech můžete zjistit, že Azure AD a Azure AD B2C nejsou dostatečné k nahrazení řízení přístupu ve vašich webových aplikacích bez provedení zásadních změn kódu. Některé běžné příklady mohou zahrnovat:

- Webové aplikace, které používají WIF nebo WS-Federation pro přihlášení s poskytovateli sociální identity, jako je Google nebo Facebook.
- Webové aplikace, které provádějí přímé federaci k poskytovateli podnikových identit přes protokol WS-Federation.
- Webové aplikace, které vyžadují přístupový token vydaný poskytovatelem sociální identity (například Google nebo Facebook) jako deklaraci identity v tokenech vydaných řízením přístupu.
- Webové aplikace se složitými pravidly transformace tokenů, které azure ad nebo Azure AD B2C nelze reprodukovat.
- Víceklientské webové aplikace, které používají službu ACS k centrální správě federace pro mnoho různých poskytovatelů identit

V těchto případech můžete zvážit migraci webové aplikace do jiné služby cloudové ověřování. Doporučujeme prozkoumat následující možnosti. Každá z následujících možností nabízí možnosti podobné řízení přístupu:

|     |     |
| --- | --- |
| ![Na tomto obrázku je logo Auth0](./media/active-directory-acs-migration/rsz-auth0.png) | [Auth0](https://auth0.com/acs) je flexibilní služba cloudových identit, která vytvořila [pokyny pro migraci na vysoké úrovni pro zákazníky řízení přístupu](https://auth0.com/acs)a podporuje téměř všechny funkce, které služba ACS provádí. |
| ![Tento obrázek znázorňuje logo Identity ping](./media/active-directory-acs-migration/rsz-ping.png) | [Ping Identity](https://www.pingidentity.com) nabízí dvě řešení podobná ACS. PingOne je cloudová identita, která podporuje mnoho stejných funkcí jako ACS, a PingFederate je podobný místně identitní produkt, který nabízí větší flexibilitu. Další podrobnosti o používání těchto produktů naleznete v pokynech pro [vyřazení služby Ping do důchodu.](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) |

Naším cílem při práci s Identitou ping a Auth0 je zajistit, aby všichni zákazníci řízení přístupu měli cestu migrace pro své aplikace a služby, která minimalizuje množství práce potřebné k přechodu z řízení přístupu.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Webové služby používající aktivní ověřování

Pro webové služby, které jsou zabezpečeny tokeny vydané řízením přístupu, nabízí řízení přístupu následující funkce a možnosti:

- Možnost zaregistrovat jednu nebo více *identit služby* v oboru názvů řízení přístupu. Identity služby lze použít k vyžádání tokenů.
- Podpora protokolů OAuth WRAP a OAuth 2.0 Draft 13 pro vyžádání tokenů pomocí následujících typů pověření:
    - Jednoduché heslo vytvořené pro identitu služby
    - Podepsaný SWT pomocí symetrického klíče nebo certifikátu X509
    - Token SAML vydaný důvěryhodným poskytovatelem identity (obvykle instance služby AD FS)
- Podpora pro následující formáty tokenů: JWT, SAML 1.1, SAML 2.0 a SWT.
- Jednoduchá pravidla transformace tokenu.

Identity služeb v řízení přístupu se obvykle používají k implementaci ověřování mezi servery. 

#### <a name="migrate-to-azure-active-directory"></a>Migrace do služby Azure Active Directory

Doporučujeme pro tento typ toku ověřování migrovat do [služby Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/). Azure AD je poskytovatel cloudových identit pro pracovní nebo školní účty Microsoftu. Azure AD je poskytovatel identit pro Office 365, Azure a mnoho dalšího. 

Azure AD můžete také použít pro ověřování mezi servery pomocí implementace Azure AD grantu pověření klienta OAuth. Následující tabulka porovnává možnosti řízení přístupu v ověřování mezi servery s možnostmi, které jsou k dispozici ve službě Azure AD.

| Schopnost | Podpora řízení přístupu | Podpora Azure AD |
| ---------- | ----------- | ---------------- |
| Jak zaregistrovat webovou službu | Vytvoření předávající strany na portálu správy řízení přístupu | Vytvoření webové aplikace Azure AD na webu Azure Portal |
| Jak zaregistrovat klienta | Vytvoření identity služby na portálu správy řízení přístupu | Vytvoření jiné webové aplikace Azure AD na webu Azure Portal |
| Použitý protokol |- Protokol OAuth WRAP<br />- OAuth 2.0 Návrh 13 klientpověření grant | Udělení přihlašovacích údajů klienta OAuth 2.0 |
| Metody ověřování klienta |- Jednoduché heslo<br />- Podepsané SWT<br />- Token SAML od poskytovatele federované identity |- Jednoduché heslo<br />- Podepsáno JWT |
| Formáty tokenů |- JWT<br />- SAML 1,1<br />- SAML 2,0<br />- SWT<br /> | Pouze JWT |
| Transformace tokenu |- Přidat vlastní nároky<br />- Jednoduché if-then deklarace emisní logiky | Přidání vlastních deklarací | 
| Automatizace úloh konfigurace a správy | Podporováno prostřednictvím služby řízení přístupu | Podporováno pomocí rozhraní Microsoft Graph API |

Pokyny k implementaci scénářů mezi servery naleznete v následujících zdrojích:

- Část Service-to-Service v [průvodci vývojářem Azure AD](https://aka.ms/aaddev)
- [Ukázka kódu daemonu pomocí jednoduchých přihlašovacích údajů klienta hesla](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Ukázka kódu daemonu pomocí pověření klienta certifikátu](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrace na identitu příkazu Ping nebo auth0

V některých případech můžete zjistit, že přihlašovací údaje klienta Azure AD a implementace udělení OAuth nejsou dostatečné k nahrazení řízení přístupu ve vaší architektuře bez velkých změn kódu. Některé běžné příklady mohou zahrnovat:

- Ověřování mezi servery pomocí jiných formátů tokenů než JWTs.
- Ověřování mezi servery pomocí vstupního tokenu poskytnutého externím zprostředkovatelem identity.
- Ověřování mezi servery s pravidly transformace tokenů, která azure ad nelze reprodukovat.

V těchto případech můžete zvážit migraci webové aplikace do jiné služby cloudauthentication. Doporučujeme prozkoumat následující možnosti. Každá z následujících možností nabízí možnosti podobné řízení přístupu:

|     |     |
| --- | --- |
| ![Na tomto obrázku je logo Auth0](./media/active-directory-acs-migration/rsz-auth0.png) | [Auth0](https://auth0.com/acs) je flexibilní služba cloudových identit, která vytvořila [pokyny pro migraci na vysoké úrovni pro zákazníky řízení přístupu](https://auth0.com/acs)a podporuje téměř všechny funkce, které služba ACS provádí. |
| ![Tento obrázek znázorňuje logo Identity ping](./media/active-directory-acs-migration/rsz-ping.png) | [Ping Identity](https://www.pingidentity.com) nabízí dvě řešení podobná ACS. PingOne je cloudová identita, která podporuje mnoho stejných funkcí jako ACS, a PingFederate je podobný místně identitní produkt, který nabízí větší flexibilitu. Další podrobnosti o používání těchto produktů naleznete v pokynech pro [vyřazení služby Ping do důchodu.](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) |

Naším cílem při práci s Identitou ping a Auth0 je zajistit, aby všichni zákazníci řízení přístupu měli cestu migrace pro své aplikace a služby, která minimalizuje množství práce potřebné k přechodu z řízení přístupu.

#### <a name="passthrough-authentication"></a>Ověření průchodu

Pro passthrough ověřování s libovolnou transformaci tokenu neexistuje žádná ekvivalentní technologie společnosti Microsoft acs. Pokud je to to, co vaši zákazníci potřebují, Auth0 může být ten, kdo poskytuje nejbližší aproximaci řešení.

## <a name="questions-concerns-and-feedback"></a>Otázky, obavy a zpětná vazba

Chápeme, že mnoho zákazníků řízení přístupu nenajde jasnou cestu migrace po přečtení tohoto článku. Možná budete potřebovat nějakou pomoc nebo pokyny při určování správného plánu. Pokud byste chtěli diskutovat o scénářích a otázkách migrace, zanechte prosím komentář na této stránce.
