---
title: Plánování nasazení kontrol přístupu Azure Active Directory
description: Průvodce plánováním pro úspěšné nasazení kontrol přístupu
services: active-directory
documentationCenter: ''
author: BarbaraSelden
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4122e645b76751e8944704a6405cf5dee09129f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97932431"
---
# <a name="planning-azure-active-directory-access-reviews-deployment"></a>Plánování nasazení kontrol přístupu Azure Active Directory

Kontroly [přístupu Azure Active Directory (Azure AD)](access-reviews-overview.md) pomůžou vaší organizaci udržovat bezpečnější síť tím, že spravují její [životní cyklus přístupu k prostředkům](identity-governance-overview.md). Pomocí kontrol přístupu můžete:

* Plánování pravidelných kontrol nebo provádění kontrol ad-hoc, které vám umožní zjistit, kdo má přístup ke konkrétním prostředkům, jako jsou aplikace a skupiny

* Sledujte recenze pro přehledy, dodržování předpisů nebo důvody zásad

* Delegovat recenze konkrétním správcům, obchodním vlastníkům nebo koncovým uživatelům, kteří si můžou sami ověřit nutnost pokračování přístupu

* Pomocí přehledů můžete efektivně určit, jestli mají mít uživatelé přístup i nadále.

* Automatizace výsledků kontroly, například odebrání přístupu uživatelů k prostředkům

  ![Diagram znázorňující tok kontrol přístupu](./media/deploy-access-review/1-planning-review.png)

Kontroly přístupu jsou [Azure AD identity Governanceou](identity-governance-overview.md) funkcí. Další možnosti jsou [Správa nároků](entitlement-management-overview.md), [Privileged Identity Management](../privileged-identity-management/pim-configure.md) a [podmínek použití](../conditional-access/terms-of-use.md). Společně mohou organizace řešit tyto čtyři otázky:

* Kteří uživatelé by měli mít přístup k jakým prostředkům?

* Jaké jsou uživatelé s tímto přístupem?

* Je k dispozici efektivní organizační řízení pro správu přístupu?

* Mohou Auditori ověřit, zda ovládací prvky fungují?

Plánování nasazení kontrol přístupu je nezbytné, abyste měli jistotu, že budete pro uživatele ve vaší organizaci dosahovat požadované strategie správy.

### <a name="key-benefits"></a>Klíčové výhody

Mezi klíčové výhody povolování kontrol přístupu patří:

* **Řízení spolupráce**. Kontroly přístupu umožňují organizacím spravovat přístup ke všem prostředkům, které uživatelé potřebují. Když jejich uživatelé sdílejí a spolupracují, můžou si organizace zaručit, že informace jsou jenom mezi autorizovanými uživateli.

* **Řízení rizik**. Kontroly přístupu poskytují organizacím možnost kontrolovat přístup k datům a aplikacím, což snižuje riziko úniku dat a úniku dat. Zahrnuje to i možnosti, které pravidelně kontrolují přístup externích partnerů k podnikovým prostředkům. 

* **Vyřešte dodržování předpisů a zásady správného řízení**. Pomocí kontrol přístupu můžete řídit a znovu certifikovat životní cyklus přístupu ke skupinám, aplikacím a webům. Můžete kontrolovat sledování revizí pro dodržování předpisů nebo aplikací citlivých na rizika, které jsou specifické pro vaši organizaci. 

* **Snižte náklady**. Kontroly přístupu jsou integrované v cloudu a nativně pracují s cloudovým prostředkům, jako jsou skupiny, aplikace a přístupové balíčky. Používání kontrol přístupu je méně nákladné než vytváření vlastních nástrojů nebo jiné upgradu místní sady nástrojů.

### <a name="training-resources"></a>Školicí materiály

Následující videa můžou být užitečná, když se seznámíte s recenzemi přístupu:

* [Co jsou recenze přístupu ve službě Azure AD?](https://youtu.be/kDRjQQ22Wkk)

* [Postup vytvoření kontrol přístupu v Azure AD](https://youtu.be/6KB3TZ8Wi40)

* [Jak povolit kontroly přístupu v Azure AD](https://youtu.be/X1SL2uubx9M)

* [Kontrola přístupu pomocí přístupu](https://youtu.be/tIKdQhdHLXU)

### <a name="licenses"></a>Licenses

Potřebujete platnou licenci Azure AD Premium (P2) pro každou osobu, jinou než globální správce nebo Správce uživatelů, kteří budou vytvářet nebo provádět kontroly přístupu. Další informace najdete v tématu [licenční požadavky kontroly přístupu](access-reviews-overview.md).

Je také možné, že budete potřebovat další funkce zásad správného řízení identit, jako je například [Správa životního cyklu nároků](entitlement-management-overview.md) nebo Správa privilegovaných identit. V takovém případě budete možná potřebovat i související licence. Další informace najdete v tématu [Azure Active Directory ceny](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="plan-the-access-reviews-deployment-project"></a>Plánování projektu nasazení kontroly přístupu

Vezměte v úvahu, že vaše organizace potřebuje určit strategii nasazení kontrol přístupu ve vašem prostředí.

### <a name="engage-the-right-stakeholders"></a>Zapojení správných zúčastněných stran

Když projekty technologie selžou, obvykle to vznikne z důvodu neshodných očekávání na dopad, výsledky a zodpovědnosti. Chcete-li se těmto nástrah vyhnout, [Ujistěte se, že jste připravují správné zúčastněné strany](../fundamentals/active-directory-deployment-plans.md) a že role projektu jsou jasné.

V případě kontrol přístupu budete pravděpodobně zahrnovat zástupce z následujících týmů v rámci vaší organizace:

* **Správa IT** spravuje vaši IT infrastrukturu a spravuje vaše cloudové investice a aplikace SaaS (software jako služba). Tento tým bude:

   * Projděte si privilegovaný přístup k infrastruktuře a aplikacím, včetně Microsoft 365 a Azure AD.

   * Naplánování a spuštění kontrol přístupu pro skupiny, které se používají k údržbě seznamů výjimek nebo projektů pilotního projektu, pro udržování aktuálnosti seznamů přístupu

   * Ujistěte se, že programový (skriptované) přístup k prostředkům prostřednictvím instančních objektů se řídí a přezkoumává.

* **Vývojové týmy** vytvářejí a udržují aplikace pro vaši organizaci. Tento tým bude:

   * Řízení přístupu k komponentám v SaaS, PaaS a IaaSch materiálech, které tvoří vyvinutá řešení, najdete v části.

   * Spravujte skupiny, které mají přístup k aplikacím a nástrojům pro vývoj interních aplikací.

   * Vyžadovat privilegované identity, které mají přístup k produkčnímu softwaru nebo řešením hostovaným pro vaše zákazníky

* **Obchodní jednotky** spravují projekty a vlastní aplikace. Tento tým bude: 

   * Kontrola a schválení nebo zamítnutí přístupu ke skupinám a aplikacím pro interní a externí uživatele.

   * Naplánujte a proveďte recenze k ověření přístupu pro zaměstnance a externí identity, jako jsou obchodní partneři.

* **Řízení podniku** zajišťuje, že organizace bude splňovat interní zásady a bude dodržovat předpisy. Tento tým bude:

   * Vyžádejte nebo naplánujte nové kontroly přístupu.

   * Vyhodnoťte procesy a postupy pro kontrolu přístupu, včetně dokumentace a evidence dodržování předpisů.

   * Zkontrolujte výsledky minulých přezkoumání nejdůležitějších prostředků.

> [!NOTE]
> V recenzích vyžadujících ruční vyhodnocení nezapomeňte naplánovat vhodné kontrolory a zkontrolovat cykly, které splňují vaše požadavky na zásady a dodržování předpisů. Pokud jsou v cyklech Recenze příliš časté nebo je příliš málo kontrolorů, kvalita může být ztracena a příliš mnoho nebo příliš málo lidí může mít přístup. 

### <a name="plan-communications"></a>Plán komunikace

Komunikace je zásadní pro úspěch jakéhokoli nového obchodního procesu. Proaktivně komunikujte uživatelům, jak a kdy se jejich prostředí změní, a jak získat podporu, pokud se jim setkávají problémy. 

#### <a name="communicate-changes-in-accountability"></a>Komunikace se změnami v odpovědnosti

Kontroly přístupu podporují přesunutí zodpovědnosti za kontrolu a jednání o pokračujícím přístupu k majitelům firmy. Přístup k rozhodnutím o přístupu z IT jednotek má přesnější rozhodnutí o přístupu. Jedná se o kulturní změnu zodpovědnosti a zodpovědnosti vlastníka prostředku. Proaktivně tyto změny sdělte a zajistěte, aby vlastníci prostředků byli vyškoleni a mohli využít přehledy k zajištění dobrého rozhodnutí.

Jasně to bude mít za následek řízení pro všechna rozhodnutí o přístupu souvisejících s infrastrukturou a přiřazení privilegovaných rolí. 

#### <a name="customize-email-communication"></a>Přizpůsobení e-mailové komunikace

Po naplánování recenze se určíte uživatele, kteří budou tuto kontrolu provádět. Tito kontroloři pak obdrží e-mailové oznámení o nových kontrolách, které jsou jim přiřazeny, a připomenutí před tím, než vyprší její opětovná kontrola.

Správci se můžou rozhodnout, že toto oznámení pošle buď napůl, před uplynutím doby platnosti recenze nebo za den. 

E-maily odeslané revidujícím se dají přizpůsobit tak, aby obsahovaly vlastní krátkou zprávu, která jim přizpůsobuje jejich fungování na revizi. Doporučujeme použít další text k těmto akcím:

* Zahrňte do revidujících osobní zprávu, aby porozuměla tomu, že je posílá vaše dodržování předpisů nebo oddělení IT.

* Přidejte hypertextový odkaz nebo odkaz na interní informace o tom, co jsou očekávání revize, a další referenční nebo školicí materiál.

* Přidejte odkaz na pokyny, [Jak provést kontrolu přístupu na základě sebe.](review-your-access.md) 

  ![E-mail kontrolora](./media/deploy-access-review/2-plan-reviewer-email.png)

Po výběru možnosti zahájit revizi budou kontroloři přesměrováni na [portál myAccess](https://myapplications.microsoft.com/) pro kontroly přístupu ke skupinám a aplikacím. Portál poskytuje přehled o všech uživatelích, kteří mají přístup k hledanému prostředku, a doporučení k systému na základě posledního přihlášení a přístupu k informacím.

### <a name="plan-a-pilot"></a>Plánování pilotního projektu

Zákazníkům doporučujeme, aby na začátku pilotních kontrol pro přístup měli malé skupiny a nepostradatelné prostředky. Pilotní nasazení vám může pomáhat s úpravou procesů a komunikací podle potřeby a zvyšovat možnosti uživatelů a kontrolorů plnit požadavky na zabezpečení a dodržování předpisů.

V pilotním programu doporučujeme:

* Začněte s recenzemi, ve kterých se výsledky nepoužívají automaticky, a můžete řídit důsledky.

* Zajistěte, aby všichni uživatelé měli platné e-mailové adresy uvedené v Azure AD, a dostanou e-mailovou komunikaci, která provede příslušnou akci 

* V případě, že budete potřebovat rychle obnovit přístup k dokumentu, který jste odebrali jako součást pilotního projektu.

* Sledujte protokoly auditu a ujistěte se, že všechny události jsou správně auditovány.

Další informace najdete v tématu [osvědčené postupy pro pilotní nasazení](../fundamentals/active-directory-deployment-plans.md).

## <a name="introduction-to-access-reviews"></a>Seznámení s recenzemi přístupů

V této části se seznámíte s koncepty kontroly přístupu, které byste měli znát před plánováním recenzí.

### <a name="what-resource-types-can-be-reviewed"></a>Jaké typy prostředků se dají zkontrolovat?

Po integraci prostředků vaší organizace se službou Azure AD (jako jsou uživatelé, aplikace a skupiny) se dají spravovat a kontrolovat. 

Mezi obvyklé cíle pro kontrolu patří:

* [Aplikace integrované do Azure AD pro jednotné přihlašování](../manage-apps/what-is-application-management.md) (například SaaS, obchodní).

* [Členství](../fundamentals/active-directory-manage-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) ve skupinách (synchronizované s Azure AD nebo vytvořené v Azure ad nebo Microsoft 365, včetně Microsoft Teams).

* [Přístup k balíčku](./entitlement-management-overview.md) , který seskupuje prostředky (skupiny, aplikace a weby) do jednoho balíčku pro správu přístupu.

* [Role Azure AD a role prostředků Azure](../privileged-identity-management/pim-resource-roles-assign-roles.md) , jak jsou definované v Privileged Identity Management.

### <a name="who-will-create-and-manage-access-reviews"></a>Kdo bude vytvářet a spravovat kontroly přístupu?

Administrativní role nutná k vytvoření, správě nebo čtení kontroly přístupu závisí na typu prověřeného prostředku. Následující tabulka označuje role vyžadované pro každý typ prostředku:

| Typ prostředku| Vytváření a Správa recenzí přístupu (tvůrci)| Čtení výsledků kontroly přístupu |
| - | - | -|
| Skupina nebo aplikace| Globální správce <p>Správce uživatelů| Tvůrci a správce zabezpečení |
| Privilegované role v Azure AD| Globální správce <p>Správce privilegovaných rolí| Creators <p>Čtenář zabezpečení<p>Správce zabezpečení |
| Privilegované role v Azure (prostředky)| Globální správce<p>Správce uživatelů<p>Vlastník prostředku| Creators |
| Přístup k balíčku| Globální správce<p>Tvůrce přístupového balíčku| Pouze globální správce |


Další informace najdete v tématu [oprávnění role správce v Azure Active Directory](../roles/permissions-reference.md).

### <a name="who-will-review-the-access-to-the-resource"></a>Kdo bude kontrolovat přístup k prostředku?

Autor kontroly přístupu rozhoduje v době vytváření, který provede kontrolu. Toto nastavení nelze po zahájení revize změnit. Revidující jsou reprezentovány třemi osoby:

* Vlastníci prostředků, kteří jsou vlastníci daného prostředku.

* Sada individuálně vybraných delegátů zvolená správcem kontroly přístupu.

* Koncoví uživatelé, kteří každý z sebe samy potvrdí, že budou mít nadále přístup.

Při vytváření kontroly přístupu můžou správci zvolit jednoho nebo více kontrolorů. Všichni revidující mohou spustit a provést kontrolu a vybrat uživatele pro pokračování přístupu k prostředku nebo jejich odebrání. 

### <a name="components-of-an-access-review"></a>Součásti kontroly přístupu

Před implementací kontrol přístupu byste měli naplánovat typy revizí, které jsou relevantní pro vaši organizaci. V takovém případě budete muset učinit podniková rozhodnutí ohledně toho, co chcete zkontrolovat, a akce, které je třeba provést na základě těchto revizí.

Chcete-li vytvořit zásadu kontroly přístupu, je nutné mít následující informace.

* Jaké prostředky se mají zkontrolovat?

* Jehož přístup je kontrolován.

* Jak často se má kontrola provádět?

* Kdo provede kontrolu?

   * Jak budou upozorňovány na kontrolu?

   * Jaké časové osy se mají vyhovět ke kontrole?

* Jaké automatické akce by se měly vyhovět na základě kontroly?

   * Co se stane, když kontrolor neodpoví včas?

* Jaké manuální akce budou provedeny jako výsledek na základě kontroly?

* Jaká komunikace by měla být odeslána na základě provedených akcí?


**Příklad plánu kontroly přístupu**

| Součást| Hodnota |
| - | - |
| **Prostředky ke kontrole**| Přístup k Microsoft Dynamics |
| **Frekvence kontroly**| Měsíčně |
| **Kdo provádí kontrolu**| Správci programu Dynamics Business Group |
| **Oznámení**| Před kontrolou aliasu Dynamics-Pms e-mailem 24 hodin<p>Zahrnutí vlastní zprávy do kontrolorů k zabezpečení jejich nákupu |
| **Časová osa**| 48 hodin z oznámení |
|**Automatické akce**| Odebrání přístupu z libovolného účtu, který nemá v 90 dnech žádné interaktivní přihlášení, odebráním uživatele ze skupiny zabezpečení Dynamics-Access. <p>*Provede akce, pokud se nekontrolují v časové ose.* |
| **Ruční akce**| Recenzenti mohou odebrat schválení před automatickými akcemi v případě potřeby. |
| **Komunikace**| Odeslat interní (členské) uživatele, kteří odeberou e-mail s vysvětlením, že se odebrali a jak znovu získat přístup. |


 

### <a name="automate-actions-based-on-access-reviews"></a>Automatizace akcí na základě kontrol přístupu

Automatické odebrání přístupu můžete vybrat tak, že nastavíte možnost automaticky použít výsledky na prostředek k povolení.

  ![Plánování plánů přístupu](./media/deploy-access-review/3-automate-actions-settings.png)

Až se kontrola dokončí a skončí, uživatelé, kteří se neschválili kontrolorem, se automaticky odstraní z prostředku – nebo budou mít trvalý přístup. To může znamenat odebrání členství ve skupině, přiřazení jejich aplikace nebo odvolání práva na zvýšení privilegované role.

Přijmout doporučení

Doporučení se zobrazí kontrolorům v rámci možnosti kontrolora a označují Poslední přihlášení uživatele k tenantovi nebo poslední přístup k aplikaci. Tyto informace pomáhají kontrolorům dělat správné přístupové rozhodnutí. Po výběru možnosti použít doporučení se budou řídit doporučení pro kontrolu přístupu. Na konci kontroly přístupu systém použije tato doporučení automaticky pro uživatele, kteří kontroloři neodpověděli.

Doporučení jsou založená na kritériích kontroly přístupu. Pokud například nakonfigurujete kontrolu pro odebrání přístupu bez interaktivního přihlášení po dobu 90 dnů, doporučí se odebrání všech uživatelů, kteří splňují tato kritéria. Microsoft neustále pracuje na vylepšení doporučení. 

### <a name="review-guest-user-access"></a>Kontrola přístupu uživatele typu Host

Pomocí kontrol přístupu můžete zkontrolovat a vyčistit identity partnerů pro spolupráci z externích organizací. Konfigurace kontroly pro jednotlivé partnery může vyhovovat požadavkům na dodržování předpisů.

Externím identitám je možné udělit přístup k prostředkům společnosti prostřednictvím jedné z následujících akcí:

* Přidáno do skupiny 

* Pozváno na týmy 

* Přiřazeno k podnikové aplikaci nebo k balíčku přístupu

* Přiřazená privilegovaná role v Azure AD nebo v předplatném Azure

Viz [ukázkový skript](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). Skript zobrazí, kde se použijí externí identity přizvaní do tenanta. V Azure AD můžete zobrazit členství ve skupině externích uživatelů, přiřazení rolí a přiřazení aplikací. Skript nebude zobrazovat žádná přiřazení mimo Azure AD, například přímé přiřazování práv k prostředkům služby SharePoint bez použití skupin.

Když vytváříte kontrolu přístupu pro skupiny nebo aplikace, můžete se rozhodnout, že se má kontrolor soustředit na všechny uživatele s přístupem nebo jenom uživatelům typu Host. Když vyberete jenom uživatele typu Host, kontroloré mají k tomuto prostředku přiřazený seznam externích identit z Azure AD B2B.

 ![Kontrola uživatelů typu Host](./media/deploy-access-review/4-review-guest-users-admin-ui.png)

> [!IMPORTANT]
> Tato akce nebude zahrnovat externí členy, kteří mají userType člena. Tato akce také nezahrnuje uživatele pozvaní mimo spolupráci Azure AD B2B, například ty, kteří mají přímý přístup ke sdílenému obsahu prostřednictvím služby SharePoint.

## <a name="plan-access-reviews-for-access-packages"></a>Plánování kontrol přístupu pro balíčky přístupu

[Balíčky přístupu](entitlement-management-overview.md) můžou značně zjednodušit strategii zásad správného řízení a kontroly přístupu. Balíček pro přístup je sada všech prostředků s přístupem, které uživatel potřebuje k práci na projektu, nebo k provedení úkolu. Můžete například chtít vytvořit balíček pro přístup, který bude obsahovat všechny aplikace, které vývojáři ve vaší organizaci potřebují, nebo všechny aplikace, ke kterým by měli mít přístup externí uživatelé. Správce nebo delegovaný přístup správce balíčků pak seskupuje prostředky (skupiny nebo aplikace) a role, které uživatelé potřebují pro tyto prostředky.

Při [vytváření balíčku pro přístup](entitlement-management-access-package-create.md)můžete vytvořit jednu nebo více zásad přístupu, které nastavují podmínky, pro které si uživatelé mohou vyžádat přístup k balíčku, jak proces schvalování vypadá, a jak často by musela uživatel znovu požádat o přístup. Kontroly přístupu se konfigurují při vytváření nebo úpravách zásad balíčku přístupu.

Otevřete kartu životní cyklus a přejděte dolů k recenzím přístupu.

 ![Snímek obrazovky, který zobrazuje "Upravit zásadu" na kartě životní cyklus.](./media/deploy-access-review/5-plan-access-packages-admin-ui.png)

## <a name="plan-access-reviews-for-groups"></a>Plánování kontrol přístupu pro skupiny

Kromě balíčků přístupu je kontrola členství ve skupinách nejúčinnějším způsobem řízení přístupu. Doporučujeme, aby byl přístup k prostředkům přiřazen prostřednictvím [skupin zabezpečení nebo skupin Microsoft 365](../fundamentals/active-directory-manage-groups.md)a aby se uživatelé do těchto skupin přidali k získání přístupu.

Jednomu skupině se dá udělit přístup ke všem odpovídajícím prostředkům. Přístup ke skupině můžete přiřadit k jednotlivým prostředkům nebo balíčku pro přístup, který seskupuje aplikace a další prostředky. Pomocí této metody můžete zkontrolovat přístup ke skupině místo přístupu pro jednotlivé aplikace k jednotlivým aplikacím. 

Členství ve skupině je možné zkontrolovat: 

* Administrators

* Vlastníci skupiny

* Vybraní uživatelé, funkce delegované revize při vytváření Revize

* Členové skupiny, jejich ověření pro sebe sama

### <a name="group-ownership"></a>Vlastnictví skupiny

Doporučujeme, aby vlastníci skupiny zkontrolovali členství, protože jsou nejlepší pro uživatele, kteří potřebují přístup. Vlastnictví skupin se liší od typu skupiny:

Skupiny vytvořené v Microsoft 365 a Azure AD mají jednoho nebo více dobře definovaných vlastníků. Ve většině případů mají tito vlastníci k dispozici dokonalé kontrolory pro vlastní skupiny, protože ví, kdo má mít přístup. 

Microsoft Teams například používá Microsoft 365 skupiny jako základní autorizační model, aby uživatelům udělil přístup k prostředkům, které jsou v SharePointu, Exchangi, OneNotu nebo jiných službách Microsoft 365. Tvůrce týmu se automaticky stal vlastníkem a měl by být zodpovědný za ověřování členství v této skupině. 

Skupiny vytvořené ručně na portálu Azure AD nebo prostřednictvím skriptování prostřednictvím Microsoft Graph nemusí nutně mít definované vlastníky. Doporučujeme, abyste je definovali buď prostřednictvím portálu Azure AD v části vlastníci skupiny, nebo pomocí grafu.

Skupiny, které jsou synchronizované z místní služby Active Directory, nemůžou mít vlastníka ve službě Azure AD. Při vytváření kontroly přístupu pro ně byste měli vybrat jednotlivce, kteří jsou nejvhodnější k rozhodnutí o členství v nich.

> [!NOTE]
> Doporučujeme definovat obchodní zásady, které definují způsob vytvoření skupin, abyste zajistili vymazání vlastnictví skupiny a zodpovědnosti při běžné kontrole členství. 

### <a name="review-membership-of-exclusion-groups-in-conditional-access-policies"></a>Kontrola členství ve skupinách vyloučení v zásadách podmíněného přístupu 

Existují situace, kdy zásady podmíněného přístupu navržené pro zajištění zabezpečení sítě nebudou platit pro všechny uživatele. Například zásada podmíněného přístupu, která umožňuje uživatelům přihlašovat se jenom v podnikové síti, se nemusí vztahovat k prodejnímu týmu, který se intenzivně rozchází. V takovém případě by se členové prodejního týmu umístili do skupiny a tato skupina by se vyloučila ze zásad podmíněného přístupu. 

Pravidelně kontrolujte Toto členství ve skupině, protože vyloučení představuje potenciální riziko, pokud jsou z tohoto požadavku vyloučeny chybné členy.

Pomocí kontrol [přístupu Azure AD můžete spravovat uživatele vyloučené ze zásad podmíněného přístupu](conditional-access-exclusion.md).

### <a name="review-external-users-group-memberships"></a>Kontrola členství externích uživatelů ve skupinách

K minimalizaci ruční práce a souvisejících potenciálních chyb zvažte použití [dynamických skupin](../enterprise-users/groups-create-rule.md) k přiřazení členství ve skupině na základě atributů uživatele. Možná budete chtít vytvořit jednu nebo více dynamických skupin pro externí uživatele. Interní sponzor se může chovat jako kontrolor pro členství ve skupině. 

Poznámka: externí uživatelé, kteří se ze skupiny odebrali jako výsledek kontroly přístupu, se z tenanta neodstraní. 

Je možné je odstranit z klienta buď ručně, nebo pomocí skriptu.

### <a name="review-access-to-on-premises-groups"></a>Kontrola přístupu k místním skupinám

Kontroly přístupu nemůžou změnit členství ve skupinách, které synchronizujete z místního prostředí, pomocí [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md). Důvodem je to, že zdroj autority je místní.

K naplánování a údržbě běžných kontrol místních skupin můžete i nadále používat recenze přístupu. Recenzenti pak budou provádět akce v místní skupině. Tato strategie udržuje kontroly přístupu jako nástroj pro všechny recenze.

Výsledky kontroly přístupu na místních skupinách a jejich zpracování můžete provádět pomocí těchto akcí:

* Probíhá stahování sestavy CSV z kontroly přístupu a ruční provedení akce.

* Použití Microsoft Graph k programovému přístupu k výsledkům a rozhodnutím v průběhu dokončených kontrol přístupu.

Pokud například chcete získat přístup k výsledkům skupiny spravované službou Windows AD, použijte tento [ukázkový skript PowerShellu](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises). Skript popisuje požadovaná volání grafů a exportuje příkazy Windows AD-PowerShell pro provedení změn.

## <a name="plan-access-reviews-for-applications"></a>Plánování kontrol přístupu pro aplikace 

Když kontrolujete přístup k aplikaci, kontrolujete přístup zaměstnanců a externích identit k informacím a datům v rámci aplikace. Pokud potřebujete zjistit, kdo má přístup k určité aplikaci místo balíčku pro přístup nebo skupiny, vyberte si aplikaci. 

Kontroly aplikací doporučujeme naplánovat v následujících scénářích:

* Uživatelům je udělen přímý přístup k aplikaci (mimo skupinu nebo balíček pro přístup).

* Aplikace zpřístupňuje kritické nebo citlivé informace.

* Aplikace má specifické požadavky na dodržování předpisů, ke kterým je nutné ověřit.

* Máte podezření na nevhodný přístup.

Chcete-li vytvořit kontroly přístupu pro aplikaci, nastavte přiřazení uživatele jako povinné? vlastnost na hodnotu Yes. Pokud je nastaveno na ne, budou mít přístup k aplikaci všichni uživatelé v adresáři, včetně externích identit, a nemůžete zkontrolovat přístup k aplikaci. 

 ![plánování přiřazení aplikací](./media/deploy-access-review/6-plan-applications-assignment-required.png)

Pak musíte [přiřadit uživatele a skupiny](../manage-apps/assign-user-or-group-access-portal.md) , kterým chcete mít přístup. 

### <a name="reviewers-for-an-application"></a>Revidující pro aplikaci

Kontroly přístupu mohou být pro členy skupiny nebo pro uživatele, kteří byli přiřazeni k aplikaci. Aplikace ve službě Azure AD nemají nutně vlastníka, což znamená, že možnost výběru vlastníka aplikace jako kontrolora není možná. Můžete dále určit rozsah recenze a zkontrolovat pouze uživatele typu Host, kteří jsou přiřazeni k aplikaci, a ne kontrolovat veškerý přístup.

## <a name="plan-review-of-azure-ad-and-azure-resource-roles"></a>Plánování přezkoumání rolí prostředků Azure AD a Azure

[Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) zjednodušuje způsob, jakým podniky spravují privilegovaný přístup k prostředkům ve službě Azure AD. Tím se udržuje seznam privilegovaných rolí, jak v [Azure AD](../roles/permissions-reference.md) , tak v [prostředcích Azure](../../role-based-access-control/built-in-roles.md) mnohem menší a zvyšuje celkové zabezpečení adresáře.

Kontroly přístupu umožňují kontrolorům ověřit, jestli se uživatelé pořád potřebují v roli. Stejně jako kontroly přístupu pro balíčky přístupu se recenze pro role Azure AD a prostředek Azure integrují do uživatelského prostředí správce PIM. Doporučujeme pravidelně kontrolovat následující přiřazení rolí:

* Globální správce

* Správce uživatelů

* Správce privilegovaného ověřování

* Správce podmíněného přístupu

* Správce zabezpečení

* Všechny role Microsoft 365 a Dynamics Service Administrative

Zde vybrané role zahrnují trvalou a oprávněnou roli. 

V části revidujícíi vyberte jednu nebo více lidí pro kontrolu všech uživatelů. Nebo můžete vybrat, aby členové zkontrolovali svůj vlastní přístup.

 ![Upravení zásady](./media/deploy-access-review/7-plan-azure-resources-reviewers-selection.png)

## <a name="deploy-access-reviews"></a>Nasazení kontrol přístupu

Po přípravě strategie a plánu pro kontrolu přístupu k prostředkům integrovaným s Azure AD můžete nasadit a spravovat recenze pomocí níže uvedených prostředků.

### <a name="review-access-packages"></a>Kontrola přístupových balíčků

Aby bylo možné snížit riziko zastaralého přístupu, můžou správci povolit pravidelné kontroly uživatelů, kteří mají aktivní přiřazení k balíčku přístupu. Postupujte podle pokynů v následujícím odkazu:

| Články s návody| Description |
| - | - |
| [Vytvořit kontroly přístupu](entitlement-management-access-reviews-create.md)| Povolit recenze balíčku přístupu |
| [Provést kontroly přístupu](entitlement-management-access-reviews-review-access.md)| Provede kontroly přístupu pro ostatní uživatele, kteří jsou přiřazeni k balíčku přístupu. |
| [Samostatná kontrola přiřazených balíčků přístupu](entitlement-management-access-reviews-self-review.md)| Samoobslužné přezkoumání přiřazených balíčků přístupu |


> [!NOTE]
> Koncoví uživatelé, kteří si samy kontrolují a říkají, že už nepotřebují přístup, se okamžitě neodstraňují z balíčku pro přístup. Po skončení kontroly dojde k jejich odebrání z balíčku pro přístup, nebo pokud správce zastaví kontrolu.

### <a name="review-groups-and-apps"></a>Kontrola skupin a aplikací

Přístup k skupinám a aplikacím pro zaměstnance a hosty se nejspíš v průběhu času mění. Aby mohli správci snížit riziko související se zastaralými přiřazeními přístupu, můžou vytvořit kontroly přístupu pro členy skupiny nebo přístup k aplikacím. Postupujte podle pokynů v následujícím odkazu:

| Články s návody| Description |
| - | - |
| [Vytvořit kontroly přístupu](create-access-review.md)| Vytvořte jednu nebo více kontrol přístupu pro členy skupiny nebo přístup k aplikaci. |
| [Provést kontroly přístupu](perform-access-review.md)| Provede kontrolu přístupu pro členy skupiny nebo uživatelů s přístupem k aplikaci. |
| [Vlastní kontrola přístupu](review-your-access.md)| Členové kontrolují vlastní přístup ke skupině nebo aplikaci. |
| [Dokončit kontrolu přístupu](complete-access-review.md)| Zobrazení kontroly přístupu a použití výsledků |
| [Provést akci pro místní skupiny](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)| Ukázkový skript PowerShellu, který bude fungovat při kontrolách přístupu pro místní skupiny |


### <a name="review-azure-ad-roles"></a>Kontrola rolí Azure AD

Pokud chcete snížit riziko související se zastaralými přiřazeními rolí, měli byste pravidelně kontrolovat přístup k privilegovaným rolím Azure AD.

![Snímek obrazovky, který zobrazuje seznam "kontroly členství" rolí Azure A D.](./media/deploy-access-review/8-review-azure-ad-roles-picker.png)

Postupujte podle pokynů na následujících odkazech:

| Články s návody | Description |
| - | - |
 [Vytvořit kontroly přístupu](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Vytváření kontrol přístupu pro privilegované role Azure AD v PIM |
| [Vlastní kontrola přístupu](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Pokud máte přiřazenou roli správce, schvalte nebo odepřete přístup k vaší roli. |
| [Dokončení kontroly přístupu](../privileged-identity-management/pim-how-to-complete-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Zobrazení kontroly přístupu a použití výsledků |


### <a name="review-azure-resource-roles"></a>Kontrola rolí prostředků Azure

Pokud chcete snížit riziko související se zastaralými přiřazeními rolí, měli byste pravidelně kontrolovat přístup k privilegovaným rolím prostředků Azure. 

![Kontrola rolí Azure AD](./media/deploy-access-review/9-review-azure-roles-picker.png)

Postupujte podle pokynů na následujících odkazech:

| Články s návody| Description |
| - | -|
| [Vytvořit kontroly přístupu](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Vytváření kontrol přístupu pro privilegované role prostředků Azure v PIM |
| [Vlastní kontrola přístupu](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Pokud máte přiřazenou roli správce, schvalte nebo odepřete přístup k vaší roli. |
| [Dokončení kontroly přístupu](../privileged-identity-management/pim-resource-roles-complete-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Zobrazení kontroly přístupu a použití výsledků |


## <a name="use-the-access-reviews-api"></a>Použití rozhraní API kontroly přístupu

V tématu [metody rozhraní Graph API](/graph/api/resources/accessreviews-root?view=graph-rest-beta) a [ověření autorizace rolí a aplikací](/graph/api/resources/accessreviews-root?view=graph-rest-beta) můžete pracovat s prostředky, které lze snadno zobrazit, a spravovat je. Metody kontroly přístupu v rozhraní Microsoft Graph API jsou k dispozici pro kontext aplikace i uživatele. Při spouštění skriptů v kontextu aplikace musí být účet použitý ke spuštění rozhraní API (princip služby) udělen oprávnění "AccessReview. Read. All" pro dotazování na informace o kontrolách přístupu.

Populární kontroly přístupu: úlohy pro automatizaci pomocí Graph API pro kontroly přístupu:

* Vytvoření a spuštění kontroly přístupu

* Ruční ukončení kontroly přístupu před jeho plánovaným ukončením

* Vypsat všechny spuštěné recenze přístupu a jejich stav

* Podívejte se na historii revizních řad a rozhodnutí a akce provedené při každé kontrole.

* Shromáždění rozhodnutí z kontroly přístupu

* Shromažďovat rozhodnutí z dokončených revizí, kde revidující prováděl jiné rozhodnutí, než jaké je systém doporučený.

Při vytváření nových dotazů Graph API pro automatizaci doporučujeme použít [Průzkumníka grafů](https://developer.microsoft.com/en-us/graph/graph-explorer). Můžete sestavit a prozkoumat dotazy v grafu před jejich vložením do skriptů a kódu. To vám může přispět k rychlému iterování dotazu, abyste získali přesně výsledky, které hledáte, beze změny kódu vašeho skriptu.

## <a name="monitor-access-reviews"></a>Monitorování kontrol přístupu

Aktivity kontrol přístupu se zaznamenávají a jsou dostupné z [protokolů auditu Azure AD](../reports-monitoring/concept-audit-logs.md). Můžete filtrovat data auditu v kategorii, typu aktivity a rozsahu kalendářních dat. Tady je ukázkový dotaz:

| Kategorie| Zásady |
| - | - |
| Typ aktivity| Vytvoření kontroly přístupu |
| | Aktualizovat kontrolu přístupu |
| | Kontrola přístupu skončila. |
| | Odstranění kontroly přístupu |
| | Schválení rozhodnutí |
| | Zamítnutí rozhodnutí |
| | Resetovat rozhodnutí |
| | Použít rozhodnutí |
| Rozsah dat| sedm dní |


K pokročilejším dotazům a analýzám kontrol přístupu a ke sledování změn a dokončení revizí doporučujeme exportovat protokoly auditu Azure AD do [azure Log Analytics](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) nebo centra událostí Azure. Když je uložená v Azure Log Analytics, můžete použít [výkonný Analytický jazyk](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) – a vytvořit vlastní řídicí panely.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o souvisejících technologiích.

* [Co je Správa nároků Azure AD?](entitlement-management-overview.md)

* [Co je Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)