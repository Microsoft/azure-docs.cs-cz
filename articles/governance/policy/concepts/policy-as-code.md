---
title: Návrh pracovních postupů pro zásady jako kód
description: Naučte se navrhovat pracovní postupy pro nasazení definic zásad Azure jako kódu a automatické ověření prostředků.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 0914ba6510c9d2ef87d3f83417f97340d42c8bce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74267266"
---
# <a name="design-policy-as-code-workflows"></a>Návrh pracovních postupů pro zásady jako kód

Jak budete postupovat na cestě pomocí zásad správného řízení cloudu, budete chtít přejít od ruční správy jednotlivých definic zásad na webu Azure Portal nebo prostřednictvím různých sad SDK k něčemu lépe spravovatelnému a opakovatelnému v podnikovém měřítku. Dva z převládajících přístupů k řízení systémů ve velkém měřítku v cloudu jsou:

- Infrastruktura jako kód: Praxe zacházení s obsahem, který definuje vaše prostředí, vše od šablon Resource Manager u definic zásad Azure až po Azure Blueprints, jako zdrojový kód.
- DevOps: Sjednocení lidí, procesů a produktů, které umožňují nepřetržité doručování hodnoty našim koncovým uživatelům.

Politika jako kód je kombinací těchto myšlenek. V podstatě uchovávejte definice zásad ve správě zdrojového kódu a při každé změně, otestujte a ověřte tuto změnu. To by však neměl být rozsah zapojení zásad s infrastrukturou jako kód nebo DevOps.

Krok ověření by měl být také součástí jiných pracovních postupů průběžné integrace nebo průběžného nasazení. Mezi příklady patří nasazení aplikačního prostředí nebo virtuální infrastruktury. Tím, že ověření zásad Azure rané součást procesu sestavení a nasazení aplikace a provozní týmy zjistit, pokud jejich změny jsou bez stížnosti, dlouho předtím, než je příliš pozdě a oni se pokoušejí nasadit v produkčním prostředí.

## <a name="workflow-overview"></a>Přehled pracovního postupu

Doporučený obecný pracovní postup zásady jako kód vypadá jako tento diagram:

![Přehled pracovního postupu Zásady jako kód](../media/policy-as-code/policy-as-code-workflow.png)

### <a name="create-and-update-policy-definitions"></a>Vytváření a aktualizace definic zásad

Definice zásad jsou vytvořeny pomocí JSON a uloženy ve smytí zdrojového kódu. Každá zásada má vlastní sadu souborů, jako jsou parametry, pravidla a parametry prostředí, které by měly být uloženy ve stejné složce. Následující struktura je doporučený způsob, jak zachovat definice zásad ve složce zdrojového kódu.

```text
.
|
|- policies/  ________________________ # Root folder for policies
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

Když je přidána nová zásada nebo existující, pracovní postup by měl automaticky aktualizovat definici zásad v Azure. Testování nové nebo aktualizované definice zásad přichází v pozdějším kroku.

### <a name="create-and-update-initiative-definitions"></a>Vytváření a aktualizace definic iniciativ

Stejně tak iniciativy mají svůj vlastní soubor JSON a související soubory, které by měly být uloženy ve stejné složce. Definice iniciativy vyžaduje, aby definice zásadjiž existovala, takže ji nelze vytvořit ani aktualizovat, dokud nebude zdroj zásady aktualizován ve směřovatcím zdroj a poté aktualizován v Azure. Následující struktura je doporučený způsob, jak zachovat definice iniciativy ve složce zdrojového kódu:

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- params.dev.json _____________ # Parameters for a Dev environment
|     |- params.prd.json _____________ # Parameters for a Prod environment
|     |- params.tst.json _____________ # Parameters for a Test environment
|
```

Stejně jako definice zásad, při přidávání nebo aktualizaci existující iniciativy by měl pracovní postup automaticky aktualizovat definici iniciativy v Azure. Testování nové nebo aktualizované definice iniciativy přichází v pozdějším kroku.

### <a name="test-and-validate-the-updated-definition"></a>Testování a ověření aktualizované definice

Jakmile automatizace převzala nově vytvořené nebo aktualizované definice zásad nebo iniciativ a provedla aktualizaci objektu v Azure, je čas otestovat provedené změny. Zásady nebo iniciativy, jehož je součástí, by pak měly být přiřazeny k prostředkům v prostředí, které je nejdále od výroby. Toto prostředí je obvykle _Dev_.

Přiřazení by mělo používat [enforcementMode](./assignment-structure.md#enforcement-mode) _zakázáno_ tak, aby nebylo blokováno vytváření prostředků a aktualizace, ale že existující prostředky jsou stále auditovány z důvodu dodržování aktualizované definice zásad. I s enforcementMode, doporučuje se, aby obor přiřazení je skupina prostředků nebo odběr, který se používá speciálně pro ověřování zásad.

> [!NOTE]
> Zatímco režim vynucení je užitečný, není náhradou za důkladné testování definice zásad za různých podmínek. Definice zásad by měla `PUT` být `PATCH` testována s voláním rozhraní REST API, kompatibilními a nekompatibilními prostředky a hraničními případy, jako je vlastnost, která chybí v prostředku.

Po nasazení přiřazení použijte sadku Policy SDK k [získání dat dodržování předpisů](../how-to/get-compliance-data.md) pro nové přiřazení. Prostředí používané k testování zásad a přiřazení by mělo mít kompatibilní i nekompatibilní prostředky. Stejně jako dobrý test částí pro kód, chcete otestovat, že prostředky jsou podle očekávání a že také nemáte žádné falešně pozitivní nebo falešně negativní. Pokud testujete a ověřujete pouze pro očekávané, může být neočekávaný a neidentifikovaný dopad ze zásady. Další informace najdete [v tématu Vyhodnocení dopadu nové zásady Azure](./evaluate-impact.md).

### <a name="enable-remediation-tasks"></a>Povolení nápravných úkolů

Pokud ověření přiřazení splňuje očekávání, dalším krokem je ověření nápravy.
Zásady, které používají [buď deployIfNotExists](./effects.md#deployifnotexists) nebo [upravit](./effects.md#modify) může být převedena na nápravu úlohy a opravit prostředky z nevyhovujícího stavu.

Prvním krokem k tomu je udělit přiřazení zásad přiřazení role definované v definici zásady. Toto přiřazení role poskytuje spravované identitě přiřazení zásad dostatečná práva k provádění potřebných změn, aby byl prostředek kompatibilní.

Jakmile má přiřazení zásad příslušná práva, použijte sadu Policy SDK k aktivaci nápravné úlohy proti sadě prostředků, o kterých je známo, že nejsou kompatibilní. Před pokračováním by měly být dokončeny tři zkoušky proti těmto opraveným úkolům:

- Ověření, zda byl nápravný úkol úspěšně dokončen
- Spuštění vyhodnocení zásad zobrazíte, zda jsou výsledky dodržování zásad aktualizovány očekávaným způsobem.
- Spuštění testu jednotky prostředí proti prostředkům přímo k ověření jejich vlastnosti se změnily

Testování aktualizovaných výsledků vyhodnocení zásad i prostředí přímo poskytuje potvrzení, že nápravné úkoly změnily očekávané a že definice zásad zaznamenala změnu dodržování předpisů podle očekávání.

### <a name="update-to-enforced-assignments"></a>Aktualizace vynucených přiřazení

Po dokončení všech bran ověření aktualizujte přiřazení tak, aby **používalo režim vynucení** _povoleno_. Tato změna by měla být zpočátku provedena ve stejném prostředí, které je daleko od výroby. Jakmile je toto prostředí ověřeno podle očekávání, měla by být změna vymezena tak, aby zahrnovala další prostředí a tak dále, dokud nebude zásada nasazena do produkčních prostředků.

## <a name="process-integrated-evaluations"></a>Proces integrovaných hodnocení

Obecný pracovní postup pro Policy as Code je pro vývoj a nasazování zásad a iniciativ do prostředí ve velkém měřítku. Vyhodnocení zásad by však mělo být součástí procesu nasazení pro jakýkoli pracovní postup, který nasazuje nebo vytváří prostředky v Azure, jako je například nasazení aplikací nebo spuštění šablon Resource Manager k vytvoření infrastruktury.

V těchto případech po aplikaci nebo infrastruktury nasazení provede testovací předplatné nebo skupinu prostředků, vyhodnocení zásad by mělo být provedeno pro tento obor kontroly ověření všech existujících zásad a iniciativ. Zatímco mohou být nakonfigurovány jako **enforcementMode** _zakázáno_ v takovém prostředí, je užitečné vědět brzy, pokud aplikace nebo infrastruktury nasazení je v rozporu s definicemi zásad brzy. Toto vyhodnocení zásad by proto mělo být krokem v těchto pracovních postupech a selhání nasazení, které vytvářejí nekompatibilní prostředky.

## <a name="review"></a>Revize

Tento článek popisuje obecný pracovní postup pro zásady jako kód a také kde hodnocení zásad by měla být součástí jiných pracovních postupů nasazení. Tento pracovní postup lze použít v jakémkoli prostředí, které podporuje skriptované kroky a automatizace založené na aktivačních událostech.

## <a name="next-steps"></a>Další kroky

- Informace o [struktuře definice zásad](./definition-structure.md).
- Informace o [struktuře přiřazení zásad](./assignment-structure.md).
- Pochopit, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [napravit nekompatibilní prostředky](../how-to/remediate-resources.md).
- Zkontrolujte, co je skupina pro správu [pomocí organizace Uspořádat prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).