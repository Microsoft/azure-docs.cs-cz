---
title: Nejčastější dotazy týkající se migrace do Partnerského centra | Azure Marketplace
description: Tento článek se zabývá nejčastějšími dotazy týkajícími se migrace nabídek z portálu partnerů cloudu do centra partnerů.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mingshen
ms.openlocfilehash: 672153eba4aa2b739b67694f939c4796b39ac4c6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274377"
---
# <a name="frequently-asked-questions-for-migrating-from-the-cloud-partner-portal-to-partner-center"></a>Nejčastější dotazy týkající se migrace z portálu partnerů cloudu do centra partnerů

Tento článek se zabývá běžně kladenými dotazy týkajícími se migrace nabídek z portálu partnerů cloudu do Centra partnerů.

## <a name="what-does-offer-migration-mean"></a>Co znamená nabídka migrace?

Data vaší nabídky přesouváme z portálu partnerů cloudu do Partnerského centra se změnami v prostředí publikování a správy nabídek.

| Oblast  | Změny  |
|-------|----------|
| **Publikační a nabízejí manažerské zkušenosti** | Díky intuitivnímu rozhraní v Centru partnerů budete mít lepší uživatelské prostředí. Další podrobnosti najdete [v tématu Jaké jsou rozdíly mezi Partnerským centrem a portálem pro partnery cloudu?](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **Dostupnost vašich nabídek na trhu** | Žádné změny. Pokud je vaše nabídka aktivní na trhu, bude i nadále aktivní během a po dokončení migrace. |
| **Nové nákupy a nasazení** | Žádné změny. Vaši zákazníci budou i nadále moci nakupovat a nasazovat vaše nabídky bez přerušení. |
| **Výplaty** | Všechny nákupy a nasazení, ke kterým dojde během migrace nebo po ní, vám budou nadále vypláceny jako obvykle. |
|**Integrace rozhraní API s existujícími [rozhraními API portálu pro partnery cloudu](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)** | Existující cloudpartner portál u api bude i nadále podporována po migraci a vaše stávající integrace bude i nadále fungovat. Další podrobnosti najdete [v tématu Bude být po migraci podporována uživatelská centra REST portálu cloudových partnerů?](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>Můžu mít i nadále přístup k portálu cloudových partnerů a spravovat své nabídky během migrace?

Nabídky budou migrovány do Partnerského centra od 13. Během tohoto časového období budete mít přístup k portálu partnerů cloudu obvyklým způsobem, s výjimkou času, který nabízíte, je naplánována migrace.
Během migrace nabídek budou mít stav Uzamčeno – přesun do Partnerského centra,jak je vidět na následujícím snímku obrazovky. Toto období migrace by mělo být kratší než 24 hodin. Během této doby nebudete moci své nabídky aktualizovat. Po dokončení migrace vašich nabídek obdržíte e-mailové oznámení.

:::image type="content" source="media/migration-faq/all-offers-1.png" alt-text="Znázorňuje stav přenesených nabídek.":::

Po migraci nabídek budou na portálu partnerů cloudu **po omezenou dobu** v režimu jen pro čtení. Jejich stav se zobrazí "Přesunuto do partnerského centra" a obsahuje odkaz na vaši nabídku v Partnerském centru, jak je znázorněno na následujících snímcích obrazovky. Od tohoto okamžiku budete spravovat aktualizace všech svých nabídek nebo vytvářet nové nabídky výhradně prostřednictvím Partnerského centra,

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="Ilustruje zprávu pro nabídky, které byly migrovány do Centra partnerů.":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="Ilustruje stránku Portál partnerů cloudu pro migrouje nabídku.":::

## <a name="how-will-i-create-new-offers"></a>Jak vytvořím nové nabídky?

Z portálu partnerů cloudu budete vyzváni k vytváření nových nabídek v Partnerském centru.

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="Ilustruje nabídku pro vytvoření nové nabídky na portálu partnerů cloudu":::

Po výběru nové nabídky se zobrazí zpráva, například následující.

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="Ilustruje zprávu přijatou při vytváření nové nabídky v CPP":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>Musím si vytvořit nový účet pro správu nabídek v Centru partnerů?

Ne. Váš podkladový účet bude zachován a v centru partnerů byste jej již měli spravovat. To znamená, že pokud jste stávajícím partnerem, můžete se přihlásit do Centra partnerů po migraci pomocí stávajících přihlašovacích údajů k účtu cloudového portálu partnerů. Z portálu partnerů cloudu do Centra partnerů se přesouvají pouze nabídky a související možnosti správy. Žádáme vás, abyste si nevytvářeli žádné nové účty, protože vaše nabídky nebudou spojeny s novým účtem.

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>Co to znamená, když se na portálu pro partnery v cloudu zobrazí zpráva pro aktivaci účtu?

Potřebujeme od vás několik dalších podrobností, abychom mohli správně migrovat svůj účet do Centra pro partnery a umožnit vám spravovat nabídky v Centru partnerů po dokončení migrace nabídky. Další podrobnosti o aktivaci účtu najdete v [tématu Migrace účtu z portálu partnerů cloudu do Centra partnerů](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc).

Postup potřebný k dokončení aktivace účtu se liší v závislosti na roli vašeho účtu.

| Role účtu | Kroky aktivace |
|--------------|----------------|
|Vlastník | Přejděte na stránku [Profil vydavatele](https://cloudpartner.azure.com/#profile) na portálu partnerů cloudu a kliknutím na odkaz v banneru aktivujte. K dokončení aktivace účtu budete přesměrováni do Centra partnerů. |
| Přispěvatel | Účet může aktivovat pouze uživatel v účtu s rolí vlastníka. Chcete-li dokončit aktivaci účtu, obraťte se na vlastníky účtu. Vlastníci vašeho účtu by měli být uvedeni v bannerové zprávě. |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>Mám potíže s přihlášením k účtu a otevřením lístku podpory

Pokud se nemůžete přihlásit ke svému účtu, můžete si otevřít [lístek podpory](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>Kde najdu dokumentaci k novému prostředí pro publikování v Centru partnerů?

Přejděte do [dokumentace komerčního tržiště](https://docs.microsoft.com/azure/marketplace/). Pak rozbalte **portál Commercial Marketplace v Centru**  > partnerů**Vytvořte novou nabídku** a podívejte se na témata nápovědy pro vytváření jednotlivých typů nabídek.

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="Ilustruje témata nápovědy pro Partnerské centrum.":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>Jaké jsou rozdíly mezi Partnerským centrem a portálem partnerů v cloudu?

Můžete si všimnout následujících rozdílů mezi portálem partnerů cloudu a partnerským centrem.

### <a name="modular-publishing-capabilities"></a>Modulární možnosti publikování

Centrum partnerů poskytuje modulární možnost publikování, která umožňuje vybrat změny, které chcete publikovat, namísto publikování všech aktualizací najednou. Následující snímek obrazovky například ukazuje, že jediné změny, které mají být publikovány, jsou změny **v položkách Vlastnosti** a **Výpis nabídek**.

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="Ilustruje stránku Náhled a Publikovat":::

Aktualizace, které nepublikujete, se uloží jako koncepty. Před tím, než ji předvezete veřejnosti, můžete pokračovat v používání náhledu nabídky k ověření nabídky.

### <a name="rich-text-format"></a>Formát RTF

Vylepšete svou nabídku a popis plánu pomocí editoru RTF na stránce Výpis nabídky a Výpis plánu.

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="Ilustruje editor rtf":::

### <a name="enhanced-preview-options"></a>Rozšířené možnosti náhledu

Centrum partnerů obsahuje [funkci porovnání](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers) s vylepšenými možnostmi filtrování. To vám dává možnost porovnat s náhledem a živými verzemi nabídky.

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="Ilustruje funkci porovnání":::

### <a name="branding-and-navigation-changes"></a>Změny značky a navigace

Všimněte si některých změn značky. Například "SKU" jsou označeny jako "Plány" v Partnerském centru.

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="Ilustruje přehled plánu.":::

Informace, které jste použili k poskytnutí na stránkách **Marketplace** nebo S**torefront Details** (poradenská služba, aplikace Power BI) na portálu partnerů cloudu, se shromažďují také na stránce **Nabídka v** Centru partnerů.

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="Ilustruje stránku nabídky.":::

Informace, které jste použili k poskytnutí slok na jedné stránce na portálu partnerů cloudu, mohou být nyní shromažďovány na několika stránkách v Centru partnerů:

* Stránka nastavení plánu
* Stránka nabídky plánu
* Stránka dostupnosti plánu
* Naplánujte stránku technické konfigurace, jak je znázorněno na tomto snímku obrazovky.

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="Ilustruje stránku technické konfigurace Plán.":::

ID nabídky se nyní zobrazí na levém navigačním panelu nabídky.

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="Ilustruje levou navigační nabídku s ID nabídky.":::

### <a name="stop-selling-an-offer"></a>Ukončení prodeje nabídky

Můžete požádat o [zastavení prodeje nabídky](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) na trhu přímo z portálu Partnerského centra. Tato možnost je k dispozici na stránce **Přehled nabídky** pro vaši nabídku.

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="Ilustruje stránku Přehled nabídek s možností ukončit prodej.":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Které stránky v Centru partnerů odpovídají stránkám, které jsem použil na portálu partnerů v cloudu?

V následující tabulce jsou uvedena odpovídající propojení mezi dvěma portály.

| stránka | Odkaz na portál pro partnery cloudu | Odkaz Na Centrum partnerů |
|------|---------------------------|---------------------|
| **Stránka Všechny nabídky** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **Stránka Všichni vydavatelé** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **Profil vydavatele** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **Stránka Uživatelé** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **Stránka Historie** | https://cloudpartner.azure.com/#history | Funkce Historie ještě není v Centru partnerů podporována. |
| **Řídicí panel Přehledů** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **Výplatní sestava** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>Budou po migraci podporována uživatelská laureáta REST portálu cloudových partnerů?

Api portálu pro partnery cloudu jsou integrovaná s Partnerským centrem a budou fungovat i po migraci nabídek do Centra partnerů. Integrace přináší malé změny. Zkontrolujte změny v následující tabulce a ujistěte se, že váš kód bude i nadále fungovat i po migraci do Centra partnerů.

| **rozhraní API** | **Popis změny** | **Dopad** |
| ------- | ---------------------- | ---------- |
| POST Publikovat, GoLive, Zrušit | U migrovaných nabídek bude mít hlavička odpovědi jiný formát, ale bude nadále fungovat stejným způsobem, což bude upozorovat na relativní cestu k načtení stavu operace. | Při odesílání odpovídajících požadavků POST na nabídku bude mít hlavička Umístění jeden ze dvou formátů v závislosti na stavu migrace nabídky:<ul><li>Nemigrované nabídky<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Migrované nabídky<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Operace GET | U nabídek, které dříve podporovaly pole "oznamovací e-mail" v odpovědi, bude toto pole zastaralé a již nebude vráceno pro migrované nabídky. | U migrovaných nabídek již nebudeme odesílat oznámení na seznam e-mailů uvedených v požadavcích. Místo toho bude služba rozhraní API zarovnána s procesem e-mailu s oznámením v Centru partnerů pro odesílání e-mailů. Konkrétně budou oznámení odeslána na e-mailovou adresu nastavenou v části Kontaktní údaje prodejce v nastavení účtu v Centru partnerů, aby vás upozornili na průběh operace.<br><br>Zkontrolujte e-mailovou adresu nastavenou v části Informace o kontaktech prodejce v [nastavení účtu](https://partner.microsoft.com/dashboard/account/management) v Centru partnerů, abyste se ujistili, že pro oznámení je k dispozici správný e-mail.  |
| | | |
