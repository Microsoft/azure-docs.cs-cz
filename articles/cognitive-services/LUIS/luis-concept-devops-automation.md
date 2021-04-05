---
title: Nepřetržité pracovní postupy pro aplikace LUIS
description: Jak implementovat pracovní postupy CI/CD pro DevOps pro Language Understanding (LUIS)
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.openlocfilehash: 215399e4b131162097e54c15b84cb6fa7dac72e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "98932535"
---
# <a name="continuous-integration-and-continuous-delivery-workflows-for-luis-devops"></a>Pracovní postupy průběžné integrace a průběžného doručování pro LUIS DevOps

Technici softwaru, kteří vyvíjí aplikaci Language Understanding (LUIS), mohou uplatnit DevOps postupy kolem [správy zdrojového kódu](luis-concept-devops-sourcecontrol.md), [automatizované sestavení](luis-concept-devops-automation.md), [testování](luis-concept-devops-testing.md)a [správy](luis-concept-devops-automation.md#release-management)vydaných verzí. Tento článek popisuje koncepty pro implementaci automatizovaných sestavení pro LUIS.

## <a name="build-automation-workflows-for-luis"></a>Vytváření pracovních postupů automatizace pro LUIS

![Pracovní postupy CI](./media/luis-concept-devops-automation/luis-automation.png)

V systému správy zdrojového kódu (SCM) nakonfigurujte automatizované kanály sestavení tak, aby běžely na následujících událostech:

1. **Pracovní postup PR** aktivovaný při vyvolání [žádosti o](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) přijetí změn (PR). Tento pracovní postup ověří obsah žádosti o přijetí změn *předtím, než* se aktualizace sloučí do hlavní větve.
1. **Pracovní postup CI/CD** byl aktivován při vložení aktualizací do hlavní větve, například při sloučení změn z žádosti o přijetí změn. Tento pracovní postup zajišťuje kvalitu všech aktualizací v hlavní větvi.

**Pracovní postup CI/CD** kombinuje dva doplňkové vývojové procesy:

* [Průběžná integrace](/azure/devops/learn/what-is-continuous-integration) (CI) je technický postup často potvrzujícího kód ve sdíleném úložišti a provádění automatizovaného sestavení na něm. Nepřetržitá integrace s přístupem k automatizovanému [testování](luis-concept-devops-testing.md) umožňuje průběžnou integraci ověřit si, že pro každou aktualizaci je zdroj LUDown stále platný a je možné ho importovat do aplikace Luis, ale také předá skupinu testů, které ověří, že aplikace může rozpoznat záměry a entity vyžadované pro vaše řešení.

* [Průběžné doručování](/azure/devops/learn/what-is-continuous-delivery) (CD) pořizuje koncept plynulé integrace, který automaticky nasadí aplikaci do prostředí, kde můžete provádět podrobnější testování. CD vám umožní rychle se seznámit s případnými neočekávanými problémy, které vznikají v našich změnách co nejrychleji, a také se dozvědět o mezerách v rámci našeho pokrytí testu.

Cílem průběžné integrace a průběžného doručování je zajistit, že "hlavní" je vždycky zasílané oznámení, ". V případě aplikace LUIS to znamená, že jsme v případě potřeby mohli využít libovolnou verzi z hlavní aplikace LUIS a dodat ji v produkčním prostředí.

### <a name="tools-for-building-automation-workflows-for-luis"></a>Nástroje pro vytváření pracovních postupů automatizace pro LUIS

K dispozici jsou různé technologie automatizace sestavení pro vytváření pracovních postupů automatizace sestavení. Všechny z nich vyžadují skriptování kroků pomocí rozhraní příkazového řádku (CLI) nebo volání REST, aby je bylo možné spustit na serveru sestavení.

Pro vytváření pracovních postupů automatizace pro LUIS použijte následující nástroje:

* [Rozhraní bot Framework Tools Luis CLI](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) pro práci s Luis aplikacemi a verzemi, jejich výukou, otestováním a publikováním v rámci služby Luis.

* [Azure CLI](/cli/azure/) pro dotazování na předplatná Azure, načtení klíčů pro vytváření a předpovědi Luis a vytvoření [instančního objektu](/cli/azure/ad/sp) Azure používaného pro ověřování Automation

* [NLU. Nástroj DevOps](https://github.com/microsoft/NLU.DevOps) pro [testování Luis aplikace](luis-concept-devops-testing.md) a k analýze výsledků testů.

### <a name="the-pr-workflow"></a>Pracovní postup žádosti o přijetí změn

Jak už jsme uvedli, nakonfigurujete tento pracovní postup tak, aby běžel, když vývojář vyvolá žádost o přijetí změn, která navrhuje změny, které se mají sloučit z větve funkce, do hlavní větve. Jeho účelem je ověřit kvalitu změn v žádosti o přijetí změn před jejich sloučením do hlavní větve.

Tento pracovní postup by měl:

* Vytvořte dočasnou aplikaci LUIS importem zdroje v žádosti o přijetí změn `.lu` .
* Vyškolení a publikování verze aplikace LUIS
* Spustí pro něj všechny [testy jednotek](luis-concept-devops-testing.md) .
* Předat pracovní postup, pokud všechny testy proběhnou, jinak selže.
* Vyčistěte a odstraňte dočasnou aplikaci.

Pokud je vaše SCM podporuje, nakonfigurujte pravidla ochrany větví, aby se tento pracovní postup musel úspěšně dokončit, aby bylo možné žádost o přijetí změn dokončit.

### <a name="the-main-branch-cicd-workflow"></a>Pracovní postup CI/CD hlavní větve

Nakonfigurujte tento pracovní postup tak, aby běžel po sloučení aktualizací v žádosti o přijetí změn do hlavní větve. Jeho účelem je udržet si vysokou úroveň kvality pro hlavní větev tím, že otestujete aktualizace. Pokud aktualizace vyhovují panelu kvality, tento pracovní postup nasadí novou verzi aplikace LUIS do prostředí, kde můžete provádět podrobnější testování.

Tento pracovní postup by měl:

* Vytvořte novou verzi v primární aplikaci LUIS (aplikace, kterou udržujete pro hlavní větev), pomocí aktualizovaného zdrojového kódu.

* Vyškolení a publikování verze aplikace LUIS

  > [!NOTE]
  > Jak je vysvětleno v tématu [spouštění testů v procesu automatizovaného sestavení](luis-concept-devops-testing.md#running-tests-in-an-automated-build-workflow) , je nutné publikovat verzi aplikace Luis v rámci testu tak, aby nástroje jako NLU. DevOps k němu má přístup. LUIS podporuje pouze dva pojmenované sloty publikace, *přípravu* a *produkci* pro aplikaci Luis, ale můžete také [Publikovat přímo verzi](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) a [dotazovat](./luis-migration-api-v3.md#changes-by-slot-name-and-version-name)se na verzi. Publikujte pomocí přímé verze publikování v pracovních postupech automatizace, abyste se vyhnuli omezení na použití pojmenovaných slotů publikování.

* Spusťte všechny [testy jednotek](luis-concept-devops-testing.md).

* Volitelně můžete spustit [dávkové testy](luis-concept-devops-testing.md#how-to-do-unit-testing-and-batch-testing) pro měření kvality a přesnosti verze aplikace Luis a porovnat je s některými směrnými plány.

* Pokud byly testy úspěšně dokončeny:
  * Označte zdroj v úložišti.
  * Spusťte úlohu průběžného doručování (CD) k nasazení verze aplikace LUIS do prostředí pro další testování.

### <a name="continuous-delivery-cd"></a>Průběžné doručování (CD)

Úloha CD v pracovním postupu CI/CD se spouští podmíněně po úspěšném sestavení a automatizované testy jednotek. Jeho úkolem je automaticky nasadit aplikaci LUIS do prostředí, kde můžete provádět více testování.

Neexistuje žádné doporučené řešení, jak nejlépe nasadit aplikaci LUIS, a musíte implementovat proces, který je vhodný pro váš projekt. Úložiště [šablon Luis DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template) implementuje jednoduché řešení, které umožňuje [Publikovat novou verzi Luis aplikace](./luis-how-to-publish-app.md) do *produkční* přihrádky pro publikování. To je přesné pro jednoduché nastavení. Pokud ale potřebujete podporovat několik různých produkčních prostředí současně, například *vývoj*, *přípravu* a *UAT*, pak se limit dvou pojmenovaných slotů publikování na aplikaci neprojeví jako nedostatečné.

K dalším možnostem nasazení verze aplikace patří:

* Ponechte verzi aplikace publikovanou na koncový bod přímé verze a implementujte proces pro konfiguraci navazujících produkčních prostředí s přímým koncovým bodem verze podle potřeby.
* Udržujte různé aplikace LUIS pro každé provozní prostředí a napíšete kroky automatizace pro import `.lu` do nové verze v aplikaci Luis pro cílové provozní prostředí, ke školení a publikování.
* Exportujte testované verze aplikace LUIS do [kontejneru Docker Luis](./luis-container-howto.md?tabs=v3) a nasaďte kontejner Luis do služby Azure [Container Instances](../../container-instances/index.yml).

## <a name="release-management"></a>Správa vydaných verzí

Obecně doporučujeme průběžné doručování pouze do neprodukčních prostředí, jako je například vývoj a příprava. Většina týmů vyžaduje ruční kontrolu a proces schvalování pro nasazení do produkčního prostředí. V případě produkčního nasazení si můžete být jisti, že k němu dojde, když jsou klíčová lidé ve vývojovém týmu k dispozici pro podporu nebo v obdobích s nízkým provozem.

## <a name="next-steps"></a>Další kroky

* Naučte se [implementovat DevOps pro Luis s GitHubem](luis-how-to-devops-with-github.md) .
* Naučte [se psát pracovní postup akcí GitHubu pomocí NLU. DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template/blob/master/docs/4-pipeline.md)
