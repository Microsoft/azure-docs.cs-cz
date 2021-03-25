---
title: Vytvoření nabídky testu
description: Jak vytvořit samostatnou nabídku vývoje pro testování produkční nabídky v programu pro komerční tržišti v partnerském centru Microsoftu.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: dbf284305aa3201faeb77668af4198bafbf578b5
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110538"
---
# <a name="create-a-test-offer"></a>Vytvoření nabídky testu

Pro vývoj v samostatném prostředí z vaší produkční nabídky vytvoříte samostatnou nabídku pro testování a vývoj (DEV) a samostatnou produkci (PROD) nabídku. Informace o výhodách používání samostatné nabídky pro vývoj najdete v tématu [plánování nabídky SaaS](plan-saas-offer.md#test-offer).

Většinu nastavení nakonfigurujete stejně jako nabídky pro vývoj a výroby. Například oficiální marketingový jazyk a prostředky, jako jsou snímky obrazovky a loga, by měly být stejné. V případech, kdy je konfigurace stejná, můžete kopírovat a vkládat pole z plánů v nabídce pro vývoj do plánů v nabídce PROD.

Následující části popisují rozdíly v konfiguraci mezi nabídkami vývoj a výroba.

## <a name="offer-setup-page"></a>Stránka nastavení nabídky

Doporučujeme použít stejný alias v poli **alias** obou nabídek, ale připojovat "_test" k aliasu nabídky pro vývoj. Pokud má například alias nabídky "contososolution", alias nabídky pro vývoj by měl být "contososolution_test". Tímto způsobem můžete snadno identifikovat, kterou nabídku pro vývoj z vaší nabídky na vaší organizaci nabízí.

V části **Customers zájemce** použijte a Azure Table nebo test CRM Environment pro nabídku pro vývoj. Použijte zamýšlený systém správy potenciálních zákazníků pro nabídku výr.

## <a name="properties-page"></a>Stránka Vlastnosti

Tuto stránku konfigurujte i pro nabídky vývoje i výroby.

## <a name="offer-listing-page"></a>Stránka se seznamem nabídek

Tuto stránku konfigurujte i pro nabídky vývoje i výroby.

## <a name="preview-audience"></a>Cílová skupina Preview

V nabídce pro vývoj zadejte hlavní název uživatele Azure Active Directory (AAD) nebo e-mailovou adresu účet Microsoft (MSA) pro vývojáře a testery, včetně sebe sama. Všimněte si, že hlavní název uživatele v AAD se může lišit od e-mailu uživatele. Například nebude jane.doe@contoso.com fungovat, ale janedoe@contoso.com bude fungovat. Jedná se o uživatele, kteří budou mít přístup k této nabídce pro vývoj, když sdílíte odkaz na **verzi Preview** během fáze vývoje a testování.

V nabídce výr. zakázky zahrňte hlavní název uživatele Azure AD nebo e-mailovou adresu účtu Microsoft, který nabídku ověří před výběrem **tlačítka přejít na Live** , aby se nabídka publikovala živě.

## <a name="technical-configuration-page"></a>Stránka technické konfigurace

Tato tabulka popisuje rozdíly mezi nastavením pro nabídky pro vývoj a nabídky produktů z výroby.

***Tabulka 1: rozdíly v technických konfiguracích***

| Nastavení | Nabídka pro vývoj | Nabídka PROD |
| ------------ | ------------- | ------------- |
| Adresa URL cílové stránky | Zadejte svůj koncový bod pro vývoj/testování. | Zadejte svůj produkční koncový bod. |
| Webhook připojení | Zadejte svůj koncový bod pro vývoj/testování. | Zadejte svůj produkční koncový bod. |
| ID tenanta Azure Active Directory | Zadejte ID tenanta registrace aplikace testu (ID adresáře AAD). | Zadejte ID tenanta registrace produkční aplikace. |
| ID aplikace Azure Active Directory | Zadejte ID aplikace registrace testovací aplikace (ID klienta). | Zadejte ID registrační aplikace vaší produkční aplikace. |
||||

## <a name="plan-overview-page"></a>Stránka s přehledem plánu

Při vytváření plánů doporučujeme, abyste používali stejné _ID plánu_ a _název plánu_ v nabídkách pro vývoj i v produkčním prostředí s výjimkou připojení ID plánu v nabídce pro vývoj s **_test**. Pokud je například ID plánu v rámci výrobní nabídky "Enterprise", bude mít ID plánu v nabídce pro vývoj "enterprise_test". Tímto způsobem můžete snadno identifikovat, kterou nabídku pro vývoj z vaší nabídky na vaší organizaci nabízí. V rámci nabídky na výrobním řádku vytvoříte plány, podle kterých určíte cenové modely a ceny, které se rozhodnete nejlépe využít.

### <a name="plan-listing"></a>Výpis plánu

Na kartě **plán – přehled**  >  **plánu výpisu** zadejte stejný popis plánu v plánech vývoje a výroby.

### <a name="pricing-and-availability-page"></a>Stránka s cenami a dostupností

V této části najdete pokyny k dokončení stránky **Přehled**  >  **cen a dostupnosti** .

#### <a name="markets"></a>Trhy

Vyberte stejné trhy pro nabídky pro vývoj a výroby.

#### <a name="pricing"></a>Ceny

Pomocí nabídky pro vývoj můžete experimentovat s cenovými modely. Po ověření, které cenové modely nebo modely fungují nejlépe, vytvoříte plány v rámci nabídky výr. ceny a cenové modely a ceny, které chcete.

Nabídka pro vývoj by měla mít plány s nulovými nebo velmi nízkými cenami v plánech. V nabídce na VÝROBNÍm poli budou ceny, které chcete účtovat zákazníkům.

> [!NOTE]
> Nákupy provedené ve verzi Preview se zpracují pro nabídky pro vývoj i pro produkční účely. Pokud má nabídka cenu za cenu + 100/s, vaše společnost se vám bude účtovat $100. Pokud k tomu dojde, můžete otevřít [lístek podpory](support.md) a my vydáte vydaný výběr pro celou částku (a neuplatní se žádné poplatky za agenturu).

#### <a name="pricing-model"></a>Cenový model

V plánech nabídek vývoje a výroby použijte stejný cenový model. Pokud je například plán v rámci výrobní nabídky paušální sazba, s měsíčním fakturačním obdobím, nakonfigurujte plán v nabídce pro vývoj pomocí stejného modelu.

Pokud chcete snížit náklady na testování cenových modelů, včetně vlastních dimenzí měřičů na webu Marketplace, doporučujeme nakonfigurovat část s **cenami** na kartě **ceny a dostupnost** v nabídce pro vývoj s nižšími cenami, než je nabídka výr. Tady je několik pokynů, pomocí kterých můžete při nastavování cen pro plány v nabídce VÝVOJÁŘů postupovat.

***Tabulka 2: pokyny pro stanovení cen***

| Cena | Komentář |
| ------------ | ------------- |
| $0,00 | Nastavte celkovou cenu za transakci nula, aby nedošlo k finančnímu dopadu. Tuto cenu použijte při volání rozhraní API pro měření nebo testování plánů nákupu v nabídce během vývoje řešení. |
| $0,01 – $49,99 | Tento cenový rozsah slouží k testování analýz, vytváření sestav a procesu nákupu. |
| $50,00 a vyšší | Pomocí tohoto cenového rozsahu můžete testovat výběr. Informace o našem platebním plánu najdete v tématu [plány a procesy výběr](/partner-center/payout-policy-details). |
|||

Pokud se vám neúčtují poplatky za zpracování testu, otevřete [lístek podpory](support.md).

#### <a name="free-trial"></a>Bezplatná zkušební verze

Nepovolujte bezplatnou zkušební verzi nabídky pro vývoj.

## <a name="co-sell-with-microsoft-page"></a>Společný prodej pomocí stránky Microsoftu

Nekonfigurujte **společný prodej pomocí** nabídky vývojářů na kartě Microsoft.

## <a name="resell-through-csps"></a>Prodej prostřednictvím partnerů CSP

V nabídce pro vývoj nekonfigurujte **prodávaný prodej prostřednictvím karty CSP** .

## <a name="next-steps"></a>Další kroky

- [Otestování a publikování nabídky SaaS](test-publish-saas-offer.md)
