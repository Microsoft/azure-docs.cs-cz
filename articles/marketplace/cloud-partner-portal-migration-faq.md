---
title: Běžné otázky týkající se migrace do partnerského centra – Microsoft Commercial Marketplace
description: Odpovědi na nejčastější dotazy týkající se migrace nabídek z portál partnerů cloudu do partnerského centra.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mingshen
ms.openlocfilehash: c4fdde94ea703d194e2de27a8df429f62ea374f4
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727666"
---
# <a name="common-questions-about-migrating-from-the-cloud-partner-portal-to-partner-center"></a>Běžné otázky týkající se migrace z portál partnerů cloudu do partnerského centra

Tento článek popisuje Nejčastější dotazy týkající se migrace nabídek z portál partnerů cloudu do partnerského centra.

## <a name="what-does-offer-migration-mean"></a>Co nabízí migrační význam?

Data vaší nabídky z portál partnerů cloudu přesouváme do partnerského centra se změnami v možnosti publikování a správy nabídky.

| Oblast  | Změny  |
|-------|----------|
| **Publikování a nabídka správy** | V partnerském centru budete mít vylepšené uživatelské prostředí s intuitivním rozhraním. Další podrobnosti najdete v tématu [Jaké jsou rozdíly mezi partnerským centrem a portál partnerů cloudu?](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **Dostupnost nabídek na webu Marketplace** | Žádné změny. Pokud je vaše nabídka na webu Marketplace živá, bude i nadále v provozu i po dokončení migrace. |
| **Nové nákupy a nasazení** | Žádné změny. Vaši zákazníci budou moct nakupovat a nasazovat vaše nabídky bez přerušení. |
| **Výplaty** | Při jakémkoli nákupu a nasazení, ke kterým dojde během migrace nebo po ní, se bude dál platit jako normálně. |
|**Integrace rozhraní API se stávajícími [rozhraními api portál partnerů cloudu](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)** | Existující rozhraní API portál partnerů cloudu budou po migraci nadále podporována a stávající integrace budou i nadále fungovat. Další podrobnosti najdete v tématu [portál partnerů cloudu jsou rozhraní REST API podporovaná po migraci?](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>Můžu stále přístup k portál partnerů cloudu a spravovat moje nabídky během migrace?

Všechny nabídky byly migrovány do partnerského centra. Doba migrace každé nabídky by měla být kratší než 24 hodin. Po migraci vaší nabídky byste měli obdržet e-mailové oznámení.

Až budou vaše nabídky migrovány, budou v režimu jen pro čtení po **omezené** době v portál partnerů cloudu. Jejich stav bude zobrazovat "přesunuto do partnerského centra" a zahrnovat odkaz na vaši nabídku v partnerském centru, jak je znázorněno na následujících snímcích obrazovky. Od tohoto okamžiku budete spravovat aktualizace všech nabídek nebo vytvoříte nové nabídky výhradně prostřednictvím partnerského centra,

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="Ukazuje zprávu pro nabídky, které byly migrovány do partnerského centra.":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="Znázorňuje stránku portál partnerů cloudu pro migrovaná nabídku.":::

## <a name="how-will-i-create-new-offers"></a>Jak se vytvoří nové nabídky?

Z portál partnerů cloudu budete přesměrováni na vytváření nových nabídek v partnerském centru.

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="Znázorňuje nabídku pro vytvoření nové nabídky v portál partnerů cloudu":::

Po výběru nové nabídky se zobrazí zpráva, například následující.

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="Ukazuje zprávu přijatou při vytváření nové nabídky v CPP.":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>Potřebuji vytvořit nový účet pro správu nabídek v partnerském centru?

No. Váš základní účet se zachová a Vy už ho budete moct spravovat v partnerském centru. To znamená, že pokud jste stávající partner, můžete k přihlášení do partnerského centra po migraci použít stávající přihlašovací údaje účtu portál partnerů cloudu. Z portál partnerů cloudu do partnerského centra se přesouvá jenom nabídky a související prostředí pro správu. Požádáme, abyste nevytvořili žádné nové účty, protože vaše nabídky nebudou přidružené k novému účtu.

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>V portál partnerů cloudu se zobrazí zpráva o aktivaci mého účtu, co to znamená?

Abychom mohli správně migrovat váš účet do partnerského centra, potřebujeme několik dalších údajů a po dokončení migrace nabídky vám umožní spravovat nabídky v partnerském centru. Další podrobnosti o aktivaci účtu najdete v tématu [migrace účtu z portál partnerů cloudu do partnerského centra](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc).

Kroky potřebné k dokončení aktivace účtu se liší v závislosti na vaší roli účtu.

| Role účtu | Kroky aktivace |
|--------------|----------------|
|Vlastník | Přejděte na stránku [profil vydavatele](https://cloudpartner.azure.com/#profile) v portál partnerů cloudu a potom kliknutím na odkaz na informačním panelu aktivujte. K dokončení aktivace účtu budete přesměrováni do partnerského centra. |
| Přispěvatel | Účet může aktivovat jenom uživatel v účtu s rolí vlastníka. Pro dokončení aktivace účtu se obraťte na svého vlastníka účtu. Vlastníci vašeho účtu by měli být uvedeni v hlavičce zprávy. |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>Mám potíže s přihlášením k mému účtu a otevření lístku podpory

Pokud se nemůžete přihlásit ke svému účtu, můžete otevřít [lístek podpory](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>Kde najdu dokumentaci o novém prostředí pro publikování v partnerském centru?

Přejít na [komerční dokumentaci k webu Marketplace](https://docs.microsoft.com/azure/marketplace/). Pak rozbalte **portál komerčního tržiště v partnerském centru**   >  **vytvořit novou nabídku** , ve které najdete témata nápovědy pro vytvoření každého typu nabídky.

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="Znázorňuje témata nápovědy pro partnerské Centrum.":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>Jaké jsou rozdíly mezi partnerským centrem a portál partnerů cloudu?

Mezi portál partnerů cloudu a Partnerským centrem si můžete všimnout následujících rozdílů.

### <a name="modular-publishing-capabilities"></a>Modulární možnosti publikování

Partnerské centrum poskytuje modulární možnost publikování, která umožňuje vybrat změny, které chcete publikovat, místo aby se vždy publikovaly všechny aktualizace najednou. Například na následujícím snímku obrazovky vidíte, že pouze změny vybrané k publikování jsou změny **vlastností** a **seznamu nabídek**.

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="Znázorňuje stránku náhledu a publikování.":::

Aktualizace, které nepublikujete, se ukládají jako koncepty. Pokračujte v používání vaší nabídky Preview a ověřte si vaši nabídku před tím, než ji provedete ve veřejném provozu.

### <a name="rich-text-format"></a>Rich Text Format

Vylepšete svůj popis nabídky a plánu pomocí editoru formátovaného textu na stránce seznam nabídek a seznam plánů.

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="Znázorňuje Editor formátovaného textu.":::

### <a name="enhanced-preview-options"></a>Rozšířené možnosti náhledu

Partnerské centrum zahrnuje [funkci porovnání](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers) s vylepšenými možnostmi filtrování. To vám umožní porovnat verze této nabídky s verzí Preview a živými verzemi.

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="Znázorňuje funkci Compare.":::

### <a name="branding-and-navigation-changes"></a>Změny brandingu a navigace

Všimnete si, že se změní branding. Například "SKU" jsou v partnerském centru označeny jako "plány".

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="Znázorňuje přehled plánu.":::

Také informace, které jste použili k zadání na **webu Marketplace** nebo**Torefront** (konzultační služba, Power BI App) v portál partnerů cloudu, se shromažďují na stránce **seznam nabídek** v partnerském centru.

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="Znázorňuje stránku se seznamem nabídek.":::

Informace, které jste použili k poskytování SKU na jedné stránce v portál partnerů cloudu, se teď můžou shromažďovat v rámci několika stránek v partnerském centru:

* Stránka nastavení plánu
* Stránka se seznamem plánů
* Stránka dostupnosti plánu
* Stránku naplánovat technickou konfiguraci, jak je znázorněno na tomto snímku obrazovky.

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="Ukazuje stránku plánování technické konfigurace.":::

ID vaší nabídky se teď zobrazuje na levém navigačním panelu nabídky.

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="Znázorňuje levou navigační nabídku s ID nabídky.":::

### <a name="stop-selling-an-offer"></a>Zastavení prodeje nabídky

Můžete požádat o [zastavení prodeje nabídky](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) na webu Marketplace přímo z portálu partnerského centra. Možnost je k dispozici na stránce **Přehled nabídky** pro vaši nabídku.

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="Znázorňuje stránku s přehledem nabídky s možností zastavit prodej.":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Které stránky v partnerském centru odpovídají stránkám používaným v portál partnerů cloudu?

V následující tabulce jsou uvedeny odpovídající odkazy mezi dvěma portály.

| stránka | Odkaz na portál partnerů cloudu | Odkaz na partnerské Centrum |
|------|---------------------------|---------------------|
| **Stránka Všechny nabídky** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **Stránka Všichni vydavatelé** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **Profil vydavatele** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **Stránka Uživatelé** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **Stránka historie** | https://cloudpartner.azure.com/#history | Funkce historie ještě není v partnerském centru podporovaná. |
| **Řídicí panel Insights** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **Výběr sestavy** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>Budou rozhraní REST API pro portál partnerů cloudu podporovat po migraci?

Rozhraní API pro portál partnerů cloudu jsou integrovaná do partnerského centra a budou fungovat i po migraci nabídek do partnerského centra. Integrace přináší malé změny. Zkontrolujte změny v následující tabulce, abyste zajistili, že váš kód bude i po migraci do partnerského centra fungovat i nadále.

| **Rozhraní API** | **Popis změny** | **Dopad** |
| ------- | ---------------------- | ---------- |
| Publikovat, GoLive, zrušit | V případě migrovaných nabídek bude hlavička odpovědi v jiném formátu, ale bude i nadále fungovat stejným způsobem, aby se zaznamenala relativní cesta pro načtení stavu operace. | Při odesílání kterékoli z odpovídajících požadavků POST pro nabídku bude mít hlavička umístění v závislosti na stavu migrace v jednom ze dvou formátů:<ul><li>Nemigrované nabídky<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Migrované nabídky<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Operace GET | U nabídek, které dříve podporovaly pole "Notification-e-mail" v odpovědi, bude toto pole zastaralé a již nebude vráceno pro migrované nabídky. | U migrovaných nabídek už nebudeme odesílat oznámení na seznam e-mailů zadaných v požadavcích. Místo toho bude služba API v partnerském centru pro posílání e-mailů zarovnávat s e-mailovým procesem oznámení. Konkrétně se oznámení odešlou na e-mailovou adresu nastavenou v části kontaktní informace prodejce v nastavení vašeho účtu v partnerském centru, aby vás upozornila na průběh operace.<br><br>Zkontrolujte e-mailovou adresu nastavenou v části kontaktní údaje prodejce v [Nastavení účtu](https://partner.microsoft.com/dashboard/account/management) v partnerském centru, abyste měli jistotu, že je pro oznámení k dispozici správný e-mail.  |
| | | |
