---
title: Nejčastější dotazy týkající se přechodu z portál partnerů cloudu do partnerského centra – Microsoft Commercial Marketplace
description: Odpovědi na nejčastější dotazy týkající se přechodu nabídek z portál partnerů cloudu do partnerského centra.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 122de64e33778966d563cc8e94b46515e54769f7
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331205"
---
# <a name="frequently-asked-questions-about-transitioning-from-the-cloud-partner-portal-to-partner-center"></a>Nejčastější dotazy týkající se přechodu z portál partnerů cloudu do partnerského centra

Portál partnerů cloudu přešla do partnerského centra. Partnerské centrum nabízí zjednodušené integrované prostředí pro publikování nových nabídek [Microsoft AppSource](https://appsource.microsoft.com/) nebo [Azure Marketplace](https://azuremarketplace.microsoft.com/) a pro správu stávajících nabídek, které byly migrovány z portál partnerů cloudu. Všechny funkce portál partnerů cloudu jsou dostupné v partnerském centru. Tento článek se zabývá nejčastějšími dotazy.

## <a name="what-does-the-transition-to-partner-center-mean-for-me"></a>Co znamená přechod do partnerského centra pro mě?

Můžete pokračovat v podnikání v partnerském centru:

| Plošný<img src="" width=200px> | Změny |
| --- | --- |
| Účet | Není potřeba vytvořit nový účet partnerského centra. pomocí svých stávajících přihlašovacích údajů portál partnerů cloudu můžete přihlašovat se k partnerskému centru, kde teď budete spravovat svůj účet, uživatele, oprávnění a fakturaci. Informace o smlouvě o publikování a profilu společnosti se migrují na nový účet partnerského centra spolu s případnými informacemi o profilu výběr, uživatelskými účty a oprávněními a aktivními nabídkami. Další informace najdete v [centru pro správu účtu komerčního tržiště v partnerském centru](partner-center-portal/manage-account.md). |
| Nabídka prostředí pro publikování a poskytování nabídek | Přesunuli jsme vaše data nabídky z portál partnerů cloudu do partnerského centra. Teď budete mít přístup k Vašim nabídkám v partnerském centru, které nabízí vylepšené uživatelské prostředí a intuitivní rozhraní. Přečtěte si, jak [aktualizovat existující nabídku na komerčním webu Marketplace](partner-center-portal/update-existing-offer.md). |
| Dostupnost nabídek na komerčním webu Marketplace | Žádné změny. Pokud je vaše nabídka živá na komerčním webu Marketplace, bude i nadále živá. |
| Nové nákupy a nasazení | Žádné změny. Vaši zákazníci můžou dál kupovat a nasazovat vaše nabídky bez přerušení. |
| Výplaty | Jakékoli nákupy a nasazení budou nadále platit jako normální. Přečtěte si další informace o tom, jak [se na komerčním webu Marketplace hradí](/partner-center/marketplace-get-paid?context=/azure/marketplace/context/context). |
| Integrace rozhraní API se stávajícími [rozhraními api portál partnerů cloudu](cloud-partner-portal-api-overview.md) | Existující rozhraní API portál partnerů cloudu jsou stále podporována a stávající integrace stále fungují. Další informace najdete na stránce [portál partnerů cloudu rozhraní REST API se podporují?](#are-the-cloud-partner-portal-rest-apis-still-supported) |
| Analýzy | Zobrazením analýz v partnerském centru můžete dál monitorovat prodej, hodnotit výkon a optimalizovat vaše nabídky na komerčním webu Marketplace. Existují rozdíly mezi způsoby zobrazení analytických sestav v CPP a partnerském centru. Například **prodávající Insights** v CPP obsahuje **objednávky & karta použití** , která zobrazuje data pro nabídky založené na využití a nabídky bez použití, zatímco v partnerském centru stránka **objednávky** obsahuje samostatnou kartu pro nabídky SaaS. Další informace najdete v [části přístup k analytickým sestavám pro komerční tržiště v partnerském centru](partner-center-portal/analytics.md). |
|||

## <a name="do-i-need-to-create-a-new-account-to-manage-my-offers-in-partner-center"></a>Potřebuji vytvořit nový účet pro správu mých nabídek v partnerském centru?

Ne, váš účet se zachová. To znamená, že pokud jste stávající partner, můžete k přihlášení do partnerského centra použít stávající přihlašovací údaje účtu portál partnerů cloudu. Nevytvářejte nový účet ani žádné nabídky vytvořené v portál partnerů cloudu a přesunuté do partnerského centra se k němu nepřidruží.

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Které stránky v partnerském centru odpovídají stránkám používaným v portál partnerů cloudu?

Následují odkazy partnerského centra na stránky běžně používané v portál partnerů cloudu. Pokud jste uložili odkazy portál partnerů cloudu jako záložky, budete je chtít aktualizovat.

| Stránka portál partnerů cloudu <img src="" width=100px>| Odkaz na portál partnerů cloudu stránku | Odkaz na stránku partnerského centra |
| --- | --- | --- |
| Stránka Všechny nabídky | [https://cloudpartner.azure.com/#alloffers](https://cloudpartner.azure.com/#alloffers) | [https://partner.microsoft.com/dashboard/commercial-marketplace/overview](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) |
| Stránka Všichni vydavatelé | [https://cloudpartner.azure.com/#publishers](https://cloudpartner.azure.com/#publishers) | [https://partner.microsoft.com/dashboard/account/v3/publishers/list](https://partner.microsoft.com/dashboard/account/v3/publishers/list) |
| Profil vydavatele | [https://cloudpartner.azure.com/#profile](https://cloudpartner.azure.com/#profile) | [https://partner.microsoft.com/dashboard/account/management](https://partner.microsoft.com/dashboard/account/management) |
| Stránka Uživatelé | [https://cloudpartner.azure.com/#users](https://cloudpartner.azure.com/#users) | [https://partner.microsoft.com/dashboard/account/usermanagement](https://partner.microsoft.com/dashboard/account/usermanagement) |
| Stránka historie | [https://cloudpartner.azure.com/#history](https://cloudpartner.azure.com/#history) | Funkce historie ještě není v partnerském centru podporovaná. |
| Řídicí panel Insights | [https://cloudpartner.azure.com/#insights](https://cloudpartner.azure.com/#insights) | [https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) |
| Výběr sestavy | [https://cloudpartner.azure.com/#insights/payout](https://cloudpartner.azure.com/#insights/payout) | [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
|||

## <a name="payout-report-differences"></a>Výběr rozdílů sestav

Jedná se o rozdíly v sestavě výběr mezi vyřazeným portál partnerů cloudu a aktuálním partnerským centrem:

| Portál Cloud Partner | Partnerské centrum |
| --- | --- |
| **Odkaz**: https://cloudpartner.azure.com/ | **Odkaz**: https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory a https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navigace**: vytváření sestav o výběrech poskytované ve výběrech Insights | **Navigace**: vytváření sestav o výběrech poskytované v partnerském centru – ikona výběr |
| **Rozsah**:<ul><li>Transakce pro položku na řádku je viditelná, pro probíhající shromažďování, shromažďování a platby.</li><li>Vytváření sestav – zobrazí všechny položky řádků po vytvoření objednávky nákupu, včetně probíhajícího shromažďování a fakturace probíhá, a stavu kolekce a položek řádků, které ještě nejsou způsobilé k placení.</li></ul> | **Rozsah**:<ul><li>Zobrazuje položky řádků poté, co se považují za oprávněné tržby.</li><li>Zákazníci se nejprve účtují do Microsoftu a potom prodejci uvidí, že sestava výběru začíná.</li><li>Sestava výběru nezobrazuje probíhající shromažďování a probíhá fakturace.</li></ul> |
| **Transakce není připravená na výběr**: probíhá fakturace. | **Transakce není připravená na výběr**: další odhadovaná platba: stav vyvýběru je v nezpracovaném stavu. |
| **Stav výběr**: není k dispozici | **Stav výběr**:<ul><li>Nezpracováno: zisk má nárok na platbu.</li><li>Nadcházející: vzdálení bude odesláno vydavateli v nejbližším měsíčním výběrovém.</li><li>Odesláno: platba byla odeslána do vaší banky.</li></ul> |
|||

## <a name="what-about-offers-i-published-in-the-cloud-partner-portal"></a>Jaké nabídky jsem publikovali v portál partnerů cloudu?

Nabídky byly přesunuty do partnerského centra a budou vám po přihlášení do partnerského centra dostupné, s výjimkou spravované služby Dynamics NAV a Cortana Intelligence nabídek. Pokud byla vaše nabídka živá na komerčním webu Marketplace, bude i nadále živá a zákazníci budou moct nakupovat a nasazovat bez přerušení. Další podrobnosti najdete v další otázce, **jaké nabídky se přesunuly do partnerského centra?**.

## <a name="what-offers-were-moved-to-partner-center"></a>Jaké nabídky se přesunuly do partnerského centra?

Všechny typy nabídek, které byly dříve podporovány v portál partnerů cloudu, jsou podporovány v partnerském centru s výjimkou spravované služby Dynamics NAV a Cortana Intelligence nabídek.

Pro typy nabídek podporované v partnerském centru se všechny nabídky přesunuly bez ohledu na jejich stav. přesunuli jsme také nabídky konceptů, zrušení v seznamu a verze Preview.

| Typ nabídky <img src="" width=150px>| Přesunuli jste se do partnerského centra? <img src="" width=100px>| Další kroky |
| --- | --- | --- |
| SaaS | Ano | Přihlaste se do partnerského centra, abyste mohli vytvářet nové nabídky a spravovat nabídky vytvořené v portál partnerů cloudu. Další informace najdete v [části plánování nabídky SaaS pro komerční tržiště](plan-saas-offer.md). |
| Virtuální počítač | Ano | Přihlaste se do partnerského centra, abyste mohli vytvářet nové nabídky a spravovat nabídky vytvořené v portál partnerů cloudu. Další informace najdete v [plánu nabídky virtuálních počítačů](marketplace-virtual-machines.md). |
| Aplikace Azure | Ano | Přihlaste se do partnerského centra, abyste mohli vytvářet nové nabídky a spravovat nabídky vytvořené v portál partnerů cloudu. Další informace najdete v [nabídce vytvoření nabídky aplikací Azure](partner-center-portal/create-new-azure-apps-offer.md). |
| Dynamics 365 Business Central | Ano | Přihlaste se do partnerského centra, abyste mohli vytvářet nové nabídky a spravovat nabídky vytvořené v portál partnerů cloudu. Další informace najdete v [nabídce vytvoření nabídky podnikového centra Dynamics 365](partner-center-portal/create-new-business-central-offer.md). |
| Dynamics 365 pro Customer Engagement & PowerApps | Ano | Přihlaste se do partnerského centra, abyste mohli vytvářet nové nabídky a spravovat nabídky vytvořené v portál partnerů cloudu. Další informace najdete v [nabídce vytvoření Dynamics 365 pro zákaznickou zapojení & nabídky PowerApps](partner-center-portal/create-new-customer-engagement-offer.md). |
| Dynamics 365 for Operations | Ano | Přihlaste se do partnerského centra, abyste mohli vytvářet nové nabídky a spravovat nabídky vytvořené v portál partnerů cloudu. Další informace najdete v [nabídce vytvoření nabídky Dynamics 365 for Operations](partner-center-portal/create-new-operations-offer.md). |
| Aplikace Power BI | Ano | Přihlaste se do partnerského centra, abyste mohli vytvářet nové nabídky a spravovat nabídky vytvořené v portál partnerů cloudu. Další informace najdete v informacích o [Vytvoření aplikace Power BI pro AppSource](partner-center-portal/create-power-bi-app-offer.md). |
| Modul IoT Edge | Ano | Přihlaste se do partnerského centra, abyste mohli vytvářet nové nabídky a spravovat nabídky vytvořené v portál partnerů cloudu. Další informace najdete [v Azure Marketplace vytvoření, konfigurace a publikování IoT Edge modulu nabídky](partner-center-portal/azure-iot-edge-module-creation.md). |
| Kontejner | Ano | Přihlaste se do partnerského centra, abyste mohli vytvářet nové nabídky a spravovat nabídky vytvořené v portál partnerů cloudu. Další informace najdete v informacích o [Vytvoření nabídky kontejnerů Azure](partner-center-portal/create-azure-container-offer.md). |
| Konzultační služba | Ano | Přihlaste se do partnerského centra, abyste mohli vytvářet nové nabídky a spravovat nabídky vytvořené v portál partnerů cloudu. Další informace najdete v informacích o [Vytvoření konzultační nabídky služby](partner-center-portal/create-consulting-service-offer.md). |
| Spravovaná služba | Ano | Přihlaste se do partnerského centra, abyste mohli vytvářet nové nabídky a spravovat nabídky vytvořené v portál partnerů cloudu. Další informace najdete v [nabídce vytvoření spravované služby](partner-center-portal/create-new-managed-service-offer.md). |
| Spravovaná služba Dynamics NAV | Ne | Společnost Microsoft vyvinula službu Dynamics NAV Managed Service do [Dynamics 365 Business Central](https://docs.microsoft.com/dynamics365/business-central/), takže ze AppSource deuvádíme živé nabídky spravované služby Dynamics NAV. Tyto nabídky už zákazníci nezjišťují a nepřesunuli se do partnerského centra. Aby vaše nabídky byly dostupné v AppSource, přizpůsobte si je na Dynamics 365 Business Central nabízí a odešlete je v [partnerském centru](https://partner.microsoft.com/). Další informace najdete v [nabídce vytvoření nabídky podnikového centra Dynamics 365](partner-center-portal/create-new-business-central-offer.md). |
| Cortana Intelligence | Ne | Společnost Microsoft vyvinula mapu rozšíření produktu pro Cortana Intelligence, takže v seznamu Cortana Intelligence živé nabídky z AppSource. Tyto nabídky už zákazníci nezjišťují a nepřesunuli se do partnerského centra. Pokud chcete zpřístupnit nabídky na komerčním webu Marketplace, přizpůsobte si nabídky k software jako služby (SaaS) a odešlete je v [partnerském centru](https://partner.microsoft.com/). Další informace najdete v tématu [Kontrolní seznam pro vytvoření nabídky SaaS v partnerském centru](partner-center-portal/offer-creation-checklist.md). |

## <a name="i-cant-find-my-cloud-partner-portal-offers-in-partner-center"></a>Nemůžu najít moje portál partnerů cloudu nabídky v partnerském centru

Co vidíte v partnerském centru, závisí na programech, které jste si zaregistrovali, na účtech, do kterých patříte, a rolích uživatelů a oprávnění, která jste jim přiřadili. K dispozici je mnoho programů partnerského centra a může být zaregistrováno ve více programech. Můžete mít také přístup k několika účtům se stejnými přihlašovacími údaji uživatele.

Nabídky, které jste vytvořili v portál partnerů cloudu, jsou k dispozici v partnerském centru pod programem **komerčního tržiště** a pod účtem, který jste použili k vytvoření nabídek. Abyste se ujistili, že zobrazujete správný program a správný účet, postupujte podle následujících kroků. Další tipy pro řešení potíží najdete v tématu [Správa účtu partnerského centra](https://docs.microsoft.com/partner-center/partner-center-account-setup).

### <a name="access-the-right-program-in-partner-center"></a>Přístup ke správnému programu v partnerském centru

1. Přihlaste se do [partnerského centra](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) se stejnými přihlašovacími údaji, které jste použili k přihlášení do portál partnerů cloudu. Navigační podokno na levé straně zobrazuje možnosti přidružené k programům, které jste zaregistrovali.

    Příklad: Předpokládejme, že máte přístup ke třem programům: program MPN, program s odkazy a program komerčního obchodu Marketplace. Když se přihlásíte do partnerského centra, v navigačním podokně se zobrazí tyto tři programy.

2. Vyberte si přehled **komerčního tržiště**  >  **Overview** , abyste měli přístup k Vašim nabídkám.

    Pokud v navigačním podokně na levé straně nevidíte program komerčního tržiště, možná jste v nesprávném účtu. Pro přístup ke správnému účtu použijte postup v následující části.

    [![Snímek obrazovky, který zobrazuje nabídku s přehledem partnerského centra](media/cpp-pc-faq/overview-menu.png "Zobrazí nabídku s přehledem partnerského centra.")](media/cpp-pc-faq/overview-menu.png#lightbox)

### <a name="access-the-right-account-in-partner-center"></a>Přístup ke správnému účtu v partnerském centru

Pokud pracujete v rámci více účtů, v partnerském centru se zobrazí tlačítko pro výběr účtu označené dvěma šipkami v navigační nabídce na levé straně. Výběrem tlačítka pro výběr účtu zobrazíte seznam všech účtů, do kterých patříte. Vyberte libovolný účet v seznamu, abyste se k němu mohli přepnout a zobrazit všechny programy a informace týkající se tohoto účtu. Pokud v navigační nabídce nevidíte tlačítko pro výběr účtu, jste členem jednoho účtu.

[![Snímek obrazovky se zobrazí na tlačítku pro výběr účtu partnerského centra.](media/cpp-pc-faq/picker-button.png "Zobrazuje tlačítko pro výběr účtu partnerského centra.")](media/cpp-pc-faq/picker-button.png#lightbox)

## <a name="how-do-i-create-new-offers"></a>Návody vytvořit nové nabídky?

Přístup k programu komerčního tržiště v [partnerském centru](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) pro vytváření nových nabídek. Na stránce Přehled vyberte **+ Nová nabídka**.

[![Snímek obrazovky se zobrazí v nabídce přehled partnerského centra.](media/cpp-pc-faq/new-offer.png "Zobrazí nabídku s přehledem partnerského centra.")](media/cpp-pc-faq/new-offer.png#lightbox)

## <a name="i-cant-sign-in-and-need-to-open-a-support-ticket"></a>Nemůžu se přihlásit a potřebovat otevřít lístek podpory

Pokud se nemůžete přihlásit ke svému účtu, můžete tady otevřít [lístek podpory](https://partner.microsoft.com/support/v2/?stage=1) .

## <a name="where-are-instructions-for-using-partner-center"></a>Kde jsou pokyny pro používání partnerského centra?

Přejít na [komerční dokumentaci k webu Marketplace](index.yml)a pak rozbalte **portál komerčního tržiště v partnerském centru**. Chcete-li zobrazit články s nápovědu pro vytváření nabídek v partnerském centru, rozbalte položku **vytvořit novou nabídku**.

## <a name="what-are-the-publishing-and-offer-management-differences"></a>Jaké jsou rozdíly v publikování a správě nabídek?

Tady je několik rozdílů mezi portál partnerů cloudu a Partnerským centrem.

### <a name="modular-publishing-capabilities"></a>Modulární možnosti publikování

Partnerské centrum poskytuje modulární možnost publikování, která umožňuje vybrat změny, které chcete publikovat, místo aby se vždy publikovaly všechny aktualizace najednou. Například na následující obrazovce vidíte, že pouze změny vybrané k publikování jsou změny **vlastností** a  **seznamu nabídek**. Změny, které provedete na stránce verze Preview, nebudou publikovány.

[![Snímek obrazovky zobrazující stránku pro kontrolu a publikování partnerského centra](media/cpp-pc-faq/review-page.png "Zobrazuje stránku pro kontrolu a publikování partnerského centra.")](media/cpp-pc-faq/review-page.png#lightbox)

Aktualizace, které nepublikujete, se ukládají jako koncepty. Pokračujte v používání vaší nabídky Preview a ověřte si vaši nabídku před tím, než ji provedete ve veřejném provozu.

### <a name="enhanced-preview-options"></a>Rozšířené možnosti náhledu

Partnerské centrum zahrnuje [funkci porovnání](partner-center-portal/update-existing-offer.md#compare-changes-to-commercial-marketplace-offers) s vylepšenými možnostmi filtrování. To vám umožní porovnat verze této nabídky s verzí Preview a živými verzemi.

[![Snímek obrazovky s funkcí porovnání partnerského centra](media/cpp-pc-faq/compare.png "Zobrazuje funkci Compare centra pro porovnání.")](media/cpp-pc-faq/compare.png#lightbox)

### <a name="branding-and-navigation-changes"></a>Změny brandingu a navigace

Všimnete si, že se změní branding. Například *skladové jednotky* jsou v partnerském centru ve formě označení jako *plány* :

[![Snímek obrazovky se zobrazí na stránce plány partnerského centra.](media/cpp-pc-faq/plans.png "Zobrazuje stránku plánů partnerského centra.")](media/cpp-pc-faq/plans.png#lightbox)

Informace, které jste dříve zadali na **webu Marketplace** nebo v části **prezentace**  (konzultační služba, Power BI App) v portál partnerů cloudu, se teď shromažďují na stránce **seznam nabídek** v partnerském centru:

[! [Snímek obrazovky se zobrazí stránka s nabídkou pro partnerské Centrum.] (Media/cpp-PC-časté otázky/offer-listing.png](media/cpp-pc-faq/offer-listing.png#lightbox)

Informace, které jste dříve zadali pro SKU na jedné stránce portál partnerů cloudu, se teď můžou shromažďovat v rámci několika stránek v partnerském centru:

- Stránka nastavení plánu
- Stránka se seznamem plánů
- Stránka dostupnosti plánu
- Stránku naplánovat technickou konfiguraci, jak je znázorněno zde:

[![Zobrazuje stránku technické konfigurace partnerského centra.](media/cpp-pc-faq/technical-configuration.png)](media/cpp-pc-faq/technical-configuration.png#lightbox)

ID vaší nabídky se teď zobrazuje na levém navigačním panelu nabídky:

![Zobrazuje umístění ID nabídky partnerského centra.](media/cpp-pc-faq/offer-id.png)

### <a name="stop-selling-an-offer"></a>Zastavení prodeje nabídky

Můžete požádat o [zastavení prodeje nabídky](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan) na webu Marketplace přímo z portálu partnerského centra. Možnost je k dispozici na stránce **Přehled nabídky** pro vaši nabídku.

[![Snímek obrazovky zobrazující stránku partnerského centra, která ukončí prodej nabídky.](media/cpp-pc-faq/stop-sell.png "Zobrazí stránku partnerského centra, na které se zastaví prodej nabídky.")](media/cpp-pc-faq/stop-sell.png#lightbox)
<br><br>

## <a name="are-the-cloud-partner-portal-rest-apis-still-supported"></a>Jsou stále podporována portál partnerů cloudu rozhraní REST API?

Rozhraní API pro portál partnerů cloudu jsou integrovaná do partnerského centra a budou fungovat i nadále. Přechod do partnerského centra přináší malé změny. Zkontrolujte následující tabulku a ujistěte se, že váš kód bude i nadále fungovat v partnerském centru.

| Rozhraní API <img src="" width=100px>| Popis změny | Dopad |
| --- | --- | --- |
| Publikovat, GoLive, zrušit | V případě migrovaných nabídek bude hlavička odpovědi v jiném formátu, ale bude i nadále fungovat stejným způsobem, aby se zaznamenala relativní cesta pro načtení stavu operace. | Při odesílání kterékoli z odpovídajících požadavků POST pro nabídku bude mít hlavička umístění jeden ze dvou formátů v závislosti na stavu migrace této nabídky: <ul><li>Nemigrované nabídky: `/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Migrované nabídky: `/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li></ul>|
| Operace GET | U nabídek, které dříve podporovaly pole "Notification-e-mail" v odpovědi, bude toto pole zastaralé a již nebude vráceno pro migrované nabídky. | U migrovaných nabídek už nebudeme odesílat oznámení na seznam e-mailů zadaných v požadavcích. Místo toho bude služba API v partnerském centru pro posílání e-mailů zarovnávat s e-mailovým procesem oznámení. Konkrétně oznámení o průběhu operace budou odeslána na e-mailovou adresu nastavenou v části kontaktní údaje prodejce v nastavení vašeho účtu v partnerském centru.<br><br>Zajistěte, aby byla e-mailová adresa uvedená v části kontaktní informace prodejce v [Nastavení účtu](https://partner.microsoft.com/dashboard/account/management) v partnerském centru správná pro příjem oznámení. |
|||
