---
title: Automatizace reakce na hrozby pomocí playbooky ve službě Azure Sentinel | Microsoft Docs
description: Tento článek vysvětluje automatizaci v Azure Sentinel a ukazuje, jak používat playbooky k automatizaci prevence a reakce na hrozby.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: yelevin
ms.openlocfilehash: 0158c9f5b9debf0c47978e816951e25634621645
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609359"
---
# <a name="automate-threat-response-with-playbooks-in-azure-sentinel"></a>Automatizace reakce na hrozby pomocí playbooky ve službě Azure Sentinel

V tomto článku se dozvíte, co jsou Azure Sentinel playbooky a jak se dají používat k implementaci operací orchestrace zabezpečení, automatizace a reakce (společnosti) a dosažení lepších výsledků při ukládání času a prostředků.

## <a name="what-is-a-playbook"></a>Co je PlayBook?

Týmy SIEM/SOC se obvykle inundated s výstrahami zabezpečení a incidenty v pravidelných intervalech, a to na svazcích, aby byli zaměstnanci, kteří jsou k dispozici, velké množství. To je velmi často v situacích, kdy se několik výstrah ignoruje a mnoho incidentů se nezkoumá, takže organizace bude zranitelná vůči útokům, které se neprojevily.

Mnoho z těchto výstrah a incidentů, které jsou v souladu s opakovanými vzorci, které lze řešit konkrétní a definované sady nápravných akcí, není celá řada.

PlayBook je kolekce těchto opravných akcí, které je možné spustit z Azure Sentinel jako rutiny. PlayBook může přispět k automatizaci a orchestraci vaší reakce na hrozby. dá se spustit ručně nebo nastavit tak, aby se spouštěla automaticky v reakci na konkrétní výstrahy nebo incidenty, když se aktivuje pomocí pravidla analýzy nebo pravidla automatizace v uvedeném pořadí.

Playbooky se vytváří a aplikují na úrovni předplatného, ale karta **playbooky** (v okně Nová **Automatizace** ) zobrazuje všechna dostupná playbooky v rámci všech vybraných předplatných.

### <a name="azure-logic-apps-basic-concepts"></a>Azure Logic Apps základní koncepty

Playbooky ve službě Azure Sentinel vychází z pracovních postupů vytvořených v [Azure Logic Apps](../logic-apps/logic-apps-overview.md), cloudové služby, která pomáhá plánovat, automatizovat a orchestrovat úlohy a pracovní postupy napříč systémy v celém podniku. To znamená, že playbooky může využít výhod veškerého výkonu a úprav předdefinovaných šablon Logic Apps.

> [!NOTE]
> Vzhledem k tomu, že Azure Logic Apps jsou samostatný prostředek, mohou platit další poplatky. Další podrobnosti najdete na stránce s cenami [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) .

Azure Logic Apps komunikuje s jinými systémy a službami pomocí konektorů. Následuje stručný popis konektorů a některé z jejich důležitých atributů:

- **Spravovaný konektor:** Sada akcí a triggerů, které zabalí volání rozhraní API do konkrétního produktu nebo služby. Azure Logic Apps nabízí stovky konektorů ke komunikaci se službami Microsoftu i od jiných společností než Microsoft.
  - [Seznam všech konektorů Logic Apps a jejich dokumentace](/connectors/connector-reference/)

- **Vlastní konektor:** Je možné, že budete chtít komunikovat se službami, které nejsou k dispozici jako předem připravené konektory. Vlastní konektory řeší tuto potřebu tím, že vám umožní vytvořit (a dokonce sdílet) konektor a definovat vlastní triggery a akce.
  - [Vytvoření vlastních konektorů Logic Apps](/connectors/custom-connectors/create-logic-apps-connector)

- **Konektor Azure Sentinel:** Pokud chcete vytvořit playbooky, která komunikuje s Sentinel Azure, použijte konektor Azure Sentinel.
  - [Dokumentace ke konektoru služby Azure Sentinel](/connectors/azuresentinel/)

- **Aktivační událost:** Komponenta konektoru, která spustí PlayBook. Definuje schéma, které PlayBook očekává, když se aktivuje. Konektor Azure Sentinel má v současné době dvě triggery:
  - [Aktivační událost výstrahy](/connectors/azuresentinel/#triggers): PlayBook přijme výstrahu jako svůj vstup.
  - [Trigger incidentu](/connectors/azuresentinel/#triggers): PlayBook přijímá incident jako svůj vstup spolu se všemi zahrnutými výstrahami a entitami.

    > [!IMPORTANT]
    >
    > - Funkce **triggeru incidentu** pro playbooky je aktuálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

- **Akce:** Akce jsou všechny kroky, ke kterým dochází po triggeru. Můžou být uspořádané sekvenčně, paralelně nebo v matrici složitých podmínek.

- **Dynamická pole:** Dočasná pole určená výstupním schématem triggerů a akcí a vyplněná jejich skutečným výstupem, které lze použít v následujících akcích.

### <a name="permissions-required"></a>Požadovaná oprávnění

 Aby mohl váš tým SecOps používat Logic Apps pro operace orchestrace, automatizace a reakce zabezpečení (společnosti), což znamená vytvořit a spustit playbooky-in Azure Sentinel, můžete přiřadit role Azure buď ke konkrétním členům vašeho týmu provozu zabezpečení nebo k celému týmu. Následující článek popisuje různé dostupné role a úkoly, ke kterým by měly být přiřazeny:

#### <a name="azure-roles-for-logic-apps"></a>Role Azure pro Logic Apps

- **Přispěvatel aplikace logiky** umožňuje spravovat Logic Apps a spouštět playbooky, ale nemůžete k nim mít přístup, ale nemůžete k nim mít přístup (pro kterou potřebujete roli **vlastníka** ).
- **Operátor aplikace logiky**  umožňuje číst, povolit a zakázat Logic Apps, ale nemůžete je upravovat ani aktualizovat.

#### <a name="azure-roles-for-sentinel"></a>Role Azure pro Sentinel

- Role **přispěvatele Azure Sentinel** umožňuje připojit PlayBook k pravidlu Analytics.
- Role **respondérů služby Azure Sentinel** umožňuje spustit PlayBook ručně.
- **Přispěvatel pro automatizaci Azure Sentinel** umožňuje, aby pravidla automatizace spouštěla playbooky. Nepoužívá se k žádným jiným účelům.

#### <a name="learn-more"></a>Další informace

- [Přečtěte si další informace o rolích Azure v Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md#access-to-logic-app-operations).
- [Přečtěte si další informace o rolích Azure ve službě Azure Sentinel](roles.md).

## <a name="steps-for-creating-a-playbook"></a>Postup vytvoření PlayBook

- [Definujte scénář automatizace](#use-cases-for-playbooks).

- [Sestavte aplikaci logiky Azure](tutorial-respond-threats-playbook.md).

- [Otestujte aplikaci logiky](#run-a-playbook-manually-on-an-alert).

- Připojte PlayBook k [pravidlu automatizace](#incident-creation-automated-response) nebo [analytickému pravidlu](#alert-creation-automated-response)nebo ho v [případě potřeby spouštějte ručně](#run-a-playbook-manually-on-an-alert).

### <a name="use-cases-for-playbooks"></a>Případy použití pro playbooky

Platforma Azure Logic Apps nabízí stovky akcí a triggerů, takže je možné vytvořit skoro jakýkoli scénář automatizace. Sentinel Azure doporučuje začít s následujícími SOC scénáři:

#### <a name="enrichment"></a>Rozšiřování

**Shromažďovat data a připojovat je k incidentu, aby se daly lépe rozhodovat.**

Například:

Pomocí pravidla analýzy, které generuje entity IP adres, byl z výstrahy vytvořen incident Sentinel Azure.

Incident spustí pravidlo automatizace, které spustí PlayBook pomocí následujících kroků:

- Spusťte, když [se vytvoří nový incident Sentinel Azure](/connectors/azuresentinel/#triggers). Entity reprezentované v incidentu jsou uloženy v dynamických polích triggeru incidentu.

- Pro každou IP adresu se dotazuje externího poskytovatele služby Threat Intelligence, jako je například [virus celkem](https://www.virustotal.com/), aby získal více dat.

- Přidejte vracená data a přehledy jako komentáře incidentu.

#### <a name="bi-directional-sync"></a>Obousměrná synchronizace

**Playbooky se dají použít k synchronizaci incidentů Sentinel Azure s jinými systémy pro lístky.**

Například:

Vytvořte pravidlo automatizace pro všechna vytvoření incidentu a připojte PlayBook, které otevře lístek v ServiceNow:

- Spusťte, když [se vytvoří nový incident Sentinel Azure](/connectors/azuresentinel/#triggers).

- Vytvoří nový lístek v [ServiceNow](/connectors/service-now/).

- Zahrňte do lístku název incidentu, důležitá pole a adresu URL incidentu Sentinel Azure pro snadné překlopení.

#### <a name="orchestration"></a>Orchestrace

**Pomocí platformy chatu SOC můžete lépe řídit frontu incidentů.**

Například:

Pomocí pravidla analýzy, které generuje entity uživatelského jména a IP adresy, byl z výstrahy vytvořen incident Sentinel Azure.

Incident spustí pravidlo automatizace, které spustí PlayBook pomocí následujících kroků:

- Spusťte, když [se vytvoří nový incident Sentinel Azure](/connectors/azuresentinel/#triggers).

- Odešlete zprávu na kanál operací zabezpečení v [Microsoft Teams](/connectors/teams/) nebo [časové rezervy](/connectors/slack/) , abyste se ujistili, že analytikům zabezpečení ví o incidentu.

- Odešlete všechny informace v upozornění e-mailem vašemu správci a správci zabezpečení vaší hlavní sítě. E-mailová zpráva bude obsahovat přepínače **blokování** a **Ignorovat** uživatele.

- Počkejte, dokud nebude odpověď přijata od správců, a pak pokračujte v běhu.

- Pokud správci zvolili **blok**, odešlete příkaz do brány firewall, aby zablokoval IP adresu v upozornění, a další službě Azure AD, která uživatele zakáže.

#### <a name="response"></a>Odpověď

**Okamžitě reaguje na hrozby s minimálními lidskými závislostmi.**

Dva příklady:

**Příklad 1:** Reakce na pravidlo analýzy, které indikuje napadeného uživatele, jak zjistil [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md):

   - Spusťte, když [se vytvoří nový incident Sentinel Azure](/connectors/azuresentinel/#triggers).

   - Pro každou entitu uživatele v incidentu, u kterého došlo k ohrožení zabezpečení:

     - Odeslat uživateli zprávu týmu a požádat o potvrzení, že uživatel provedl podezřelou akci.

     - Zkontrolujte Azure AD Identity Protection a [Ověřte, že stav uživatele je ohrožen](/connectors/azureadip/#confirm-a-risky-user-as-compromised). Azure AD Identity Protection označí uživatele jako **rizikové** a použije všechny zásady vynucení, které jsou už nakonfigurované – třeba pro požadavek, aby uživatel při příštím přihlášení použil MFA.

        > [!NOTE]
        > PlayBook nespustí žádnou akci vynucení pro uživatele ani nespustí žádnou konfiguraci zásad vynucení. Oznamuje Azure AD Identity Protection, že podle potřeby uplatní všechny už definované zásady. Jakékoli vynucení závisí zcela na příslušných zásadách, které jsou definovány v Azure AD Identity Protection.

**Příklad 2:** Reakce na pravidlo analýzy, které indikuje napadený počítač, jak zjistil program [Microsoft Defender pro koncový bod](/windows/security/threat-protection/):

   - Spusťte, když [se vytvoří nový incident Sentinel Azure](/connectors/azuresentinel/#triggers).

   - Pomocí akce **entity – získat hostitele** v Azure Sentinel můžete analyzovat podezřelé počítače, které jsou zahrnuté v entitách incidentu.

   - Vydejte příkaz programu Microsoft Defender pro koncové body, který [izoluje počítače](/connectors/wdatp/#actions---isolate-machine) ve výstraze.

## <a name="how-to-run-a-playbook"></a>Jak spustit PlayBook

Playbooky lze spustit **ručně** nebo **automaticky**.

Ruční spouštění znamená, že když obdržíte výstrahu, můžete spustit PlayBook na vyžádání jako reakci na vybranou výstrahu. V tuto chvíli je tato funkce podporovaná jenom pro výstrahy, ne pro incidenty.

Spouští se automaticky tak, že je nastaví jako automatizovaná odpověď v pravidle analýzy (pro výstrahy) nebo jako akce v pravidle automatizace (pro incidenty). [Přečtěte si další informace o pravidlech automatizace](automate-incident-handling-with-automation-rules.md).

### <a name="set-an-automated-response"></a>Nastavení automatické odpovědi

Provozní týmy zabezpečení můžou významně snížit své zatížení tím, že plně automatizují rutinní rutiny na opakující se typy incidentů a výstrah, což vám umožní soustředit se na jedinečné incidenty a výstrahy, analyzovat vzory, lovecké hrozby a další.

Nastavením automatické odpovědi znamená, že při každém spuštění pravidla analýzy se kromě vytvoření výstrahy pravidlo spustí PlayBook, který se dostane jako vstupní výstraha vytvořená pravidlem.

Pokud výstraha vytvoří incident, incident aktivuje pravidlo automatizace, které může spustit PlayBook, který se dostane jako vstup incidentu vytvořený výstrahou.

#### <a name="alert-creation-automated-response"></a>Automatizovaná odpověď vytvoření výstrahy

Pro playbooky, které se aktivují vytvořením výstrahy a přijímají výstrahy jako jejich vstupy (jejich první krok je v případě aktivované výstrahy Azure Sentinel) připojte PlayBook k pravidlu analýzy:

1. Upravte [pravidlo analýzy](tutorial-detect-threats-custom.md) , které generuje výstrahu, pro kterou chcete definovat automatizovanou odpověď.

1. V části **Automatizace výstrah** na kartě **automatizovaná odpověď** vyberte PlayBook nebo playbooky, které toto pravidlo analýzy spustí při vytvoření výstrahy.

#### <a name="incident-creation-automated-response"></a>Automatizovaná odpověď vytvoření incidentu

Pro playbooky, které jsou aktivované vytvořením incidentu a které mají incidenty jako vstupy (jejich první krok je "když se aktivuje incident služby Azure Sentinel"), vytvořte pravidlo automatizace a definujte v něm akci **Run PlayBook** . To se dá udělat dvěma způsoby:

- Upravte pravidlo analýzy, které generuje incident, pro který chcete definovat automatizovanou odpověď. V části **Automatizace incidentů** na kartě **automatizovaná odpověď** vytvořte pravidlo automatizace. Tím se vytvoří automatizovaná odpověď jenom pro toto pravidlo analýzy.

- Na kartě **pravidla automatizace** v okně **Automatizace** vytvořte nové pravidlo automatizace a určete příslušné podmínky a požadované akce. Toto pravidlo automatizace se použije pro každé analytické pravidlo, které splní zadané podmínky.

    > [!NOTE]
    > Pokud chcete spustit PlayBook z pravidla automatizace, služba Azure Sentinel použije účet služby, který je výslovně autorizovaný. Použití tohoto účtu (na rozdíl od vašeho uživatelského účtu) zvyšuje úroveň zabezpečení služby a umožňuje rozhraní API pravidel automatizace podporovat případy použití CI/CD.
    >
    > Tomuto účtu musí být udělená explicitní oprávnění pro skupinu prostředků, ve které se nachází PlayBook. V tomto okamžiku bude jakékoli pravidlo automatizace moci v této skupině prostředků spustit všechny PlayBook.
    >
    > Když přidáte akci **Run PlayBook** do pravidla automatizace, zobrazí se rozevírací seznam playbooky. Playbooky, ke kterým má Azure Sentinel oprávnění, se zobrazí jako nedostupná (šedě vydaná). Kliknutím na odkaz **Spravovat PlayBook oprávnění** můžete udělit oprávnění ke službě Azure Sentinel na místě.
    >
    > Ve scénáři s více klienty ([Lighthouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)) musíte definovat oprávnění pro tenanta, kde PlayBook žije, i když pravidlo automatizace volajícího PlayBook je v jiném tenantovi. K tomu je potřeba mít oprávnění **vlastníka** pro skupinu prostředků PlayBook.

Projděte si [kompletní pokyny pro vytváření pravidel automatizace](tutorial-respond-threats-playbook.md#respond-to-incidents).

### <a name="run-a-playbook-manually-on-an-alert"></a>Ruční spuštění PlayBook na výstraze

Ruční aktivace je k dispozici na portálu Sentinel Azure v následujících oknech:

- V zobrazení **incidenty** , vyberte konkrétní incident, otevřete kartu **výstrahy** a vyberte výstrahu.

- V **šetření** vyberte konkrétní výstrahu.

1. Pro zvolenou výstrahu klikněte na **Playbooky zobrazení** . Zobrazí se seznam všech playbooky, které začínají na, **když se aktivuje výstraha Sentinel Azure** a že máte přístup k.

1. Kliknutím na **Spustit** na řádku konkrétního PlayBook ho aktivujte.

1. Výběrem karty **spuštění** zobrazíte seznam všech pokusů o spuštění všech PlayBook v této výstraze. Může trvat několik sekund, než se v tomto seznamu zobrazí jakékoli právě dokončené spuštění.

1. Kliknutím na konkrétní spuštění otevřete úplný protokol spuštění Logic Apps.

### <a name="run-a-playbook-manually-on-an-incident"></a>Ruční spuštění PlayBook na incidentu

Ještě není podporováno. <!--make this a note instead? -->

## <a name="manage-your-playbooks"></a>Správa playbooky

Na kartě **playbooky** se zobrazí seznam všech playbooky, ke kterým máte přístup, filtrovaný podle předplatných, která jsou aktuálně zobrazená v Azure. Filtr odběry je k dispozici v nabídce **adresář + předplatné** v záhlaví globální stránky.

Kliknutím na PlayBook název vás přesměruje na hlavní stránku PlayBook v Logic Apps. Sloupec **Status (stav** ) označuje, jestli je povolený nebo zakázaný.

**Typ triggeru** představuje Trigger Logic Apps, který spouští tuto PlayBook.

| Druh triggeru | Označuje typy součástí v PlayBook. |
|-|-|
| **Incident/výstraha Sentinel Azure** | PlayBook se spustí s jednou z aktivačních událostí Sentinel (výstraha, incident). |
| **Pomocí akce Sentinel Azure** | PlayBook se spouští s triggerem bez Sentinel, ale používá akci Azure Sentinel. |
| **Další** | PlayBook neobsahuje žádné součásti Sentinel. |
| **Neinicializováno** | PlayBook byl vytvořen, ale neobsahuje žádné součásti (triggery nebo akce). |
|

Na stránce aplikace logiky PlayBook můžete zobrazit další informace o PlayBook, včetně protokolu všech dob spuštění, a výsledku (úspěch nebo neúspěch a další podrobnosti). Můžete také zadat návrháře Logic Apps a upravit PlayBook přímo, pokud máte příslušná oprávnění.

### <a name="api-connections"></a>Připojení rozhraní API

Připojení rozhraní API se používají pro připojení Logic Apps k jiným službám. Pokaždé, když se vytvoří nové ověřování konektoru Logic Apps, vytvoří se nový prostředek typu **připojení rozhraní API** a tyto informace se zobrazí při konfiguraci přístupu ke službě.

Pokud chcete zobrazit všechna připojení rozhraní API, zadejte do pole pro hledání v hlavičce Azure Portal *připojení rozhraní API* . Poznamenejte si sloupce zájmu:

- Zobrazované jméno – popisný název, který udělíte připojení pokaždé, když ho vytvoříte.
- Stav – označuje stav připojení: Chyba, připojeno.
- Skupina prostředků – připojení rozhraní API se vytvoří ve skupině prostředků prostředku PlayBook (Logic Apps).

Dalším způsobem, jak zobrazit připojení rozhraní API, je přejít do okna **všechny prostředky** a filtrovat je podle typu *připojení rozhraní API*. Tímto způsobem lze vybrat, označit a odstranit více připojení najednou.

Chcete-li změnit autorizaci stávajícího připojení, zadejte prostředek připojení a vyberte **Upravit připojení rozhraní API**.

## <a name="next-steps"></a>Další kroky

- [Kurz: použití playbooky k automatizaci odpovědí na hrozby v Azure Sentinel](tutorial-respond-threats-playbook.md)
