---
title: Návrh pracovních postupů pro Azure Policy jako kód
description: Naučte se navrhovat pracovní postupy pro nasazení Azure Policy definic jako kódu a automatické ověřování prostředků.
ms.date: 10/20/2020
ms.topic: conceptual
ms.openlocfilehash: 74d2097e4db4442e6e65f30541864fb554f7379d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359676"
---
# <a name="design-azure-policy-as-code-workflows"></a>Návrh pracovních postupů pro Azure Policy jako kód

Jak budete postupovat na cestě pomocí zásad správného řízení cloudu, budete chtít posunout z ruční správy každé definice zásad v Azure Portal nebo prostřednictvím různých sad SDK na něco mnohem spravovatelnou a opakovat v podnikovém měřítku. Mezi dva z převládajících přístupů ke správě systémů ve velkém měřítku v cloudu patří:

- Infrastruktura jako kód: postupy pro zpracování obsahu definujícího vaše prostředí, vše od Azure Resource Manager šablon (šablony ARM), které Azure Policy definice do Azure modrotisky jako zdrojový kód.
- DevOps: sjednocení lidí, procesů a produktů umožní průběžné doručování hodnot našim koncovým uživatelům.

Azure Policy jako kód je kombinací těchto nápadů. V podstatě Udržujte definice zásad ve správě zdrojového kódu a pokaždé, když provedete změnu, otestujete a ověříte tuto změnu. Nicméně by neměl být rozsahem zapojení zásad s infrastrukturou jako Code nebo DevOps.

Krok ověření by měl být také součástí dalších pracovních postupů průběžné integrace nebo průběžného nasazování. Příklady zahrnují nasazení prostředí aplikace nebo virtuální infrastruktury. Provedením Azure Policy ověření počáteční komponenty procesu sestavení a nasazení aplikace a provozní týmy zjišťují, zda jsou jejich změny nekompatibilní, dlouho předtím, než jsou příliš pozdě a pokoušejí se nasadit v produkčním prostředí.

## <a name="definitions-and-foundational-information"></a>Definice a základní informace

Než se dostanete k podrobnostem Azure Policy jako pracovní postup kódu, přečtěte si následující definice a příklady:

- [Definice zásady](./definition-structure.md)
- [Definice iniciativy](./initiative-definition-structure.md)

Názvy souborů se zarovnají do částí definice zásady nebo iniciativy:
- `policy(set).json` – Celá definice
- `policy(set).parameters.json` – `properties.parameters` Část definice
- `policy.rules.json` – `properties.policyRule` Část definice
- `policyset.definitions.json` – `properties.policyDefinitions` Část definice

Příklady těchto formátů souborů jsou k dispozici v [Azure Policy úložiště GitHub](https://github.com/Azure/azure-policy/):

- Definice zásad: [Přidání značky k prostředkům](https://github.com/Azure/azure-policy/tree/master/samples/Tags/add-tag)
- Definice iniciativy: [fakturační značky](https://github.com/Azure/azure-policy/tree/master/samples/PolicyInitiatives/multiple-billing-tags)

## <a name="workflow-overview"></a>Přehled pracovního postupu

Doporučený obecný pracovní postup Azure Policy jako kód vypadá jako v tomto diagramu:

:::image type="complex" source="../media/policy-as-code/policy-as-code-workflow.png" alt-text="Diagram znázorňující pole pracovního postupu pro Azure Policy jako kód pracovního postupu z vytvořit pro test k nasazení." border="false":::
   Diagram znázorňující pole pracovního postupu Azure Policy jako kód Vytvořit pokrývá vytváření definicí zásad a iniciativ. Test pokrývá přiřazení s povoleným režimem vynucení. Ověření brány pro stav dodržování předpisů je následováno přidělením oprávnění M S I a prostředků oprava.  Nasazení pokrývá aktualizaci přiřazení s povoleným režimem vynucení.
:::image-end:::

### <a name="create-and-update-policy-definitions"></a>Vytvoření a aktualizace definic zásad

Definice zásad jsou vytvořeny pomocí formátu JSON a uloženy ve správě zdrojového kódu. Každá zásada má svou vlastní sadu souborů, jako jsou parametry, pravidla a parametry prostředí, které by měly být uloženy ve stejné složce. Následující struktura je doporučeným způsobem, jak zachovat definice zásad ve správě zdrojového kódu.

```text
.
|
|- policies/  ________________________ # Root folder for policy resources
|  |- policy1/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- assign.<name1>.json _________ # Assignment 1 for this policy definition
|     |- assign.<name2>.json _________ # Assignment 2 for this policy definition
|  |- policy2/  ______________________ # Subfolder for a policy
|     |- policy.json _________________ # Policy definition
|     |- policy.parameters.json ______ # Policy definition of parameters
|     |- policy.rules.json ___________ # Policy rule
|     |- assign.<name1>.json _________ # Assignment 1 for this policy definition
|     |- assign.<name2>.json _________ # Assignment 2 for this policy definition
|
```

Když se přidá nová zásada nebo se aktualizuje stávající, pracovní postup by měl tuto definici zásady v Azure automaticky aktualizovat. Testování nové nebo aktualizované definice zásad se nachází v pozdějším kroku.

Přečtěte si také téma [Export Azure Policy prostředky](../how-to/export-resources.md) a získejte existující definice a přiřazení do [GitHubu](https://www.github.com)prostředí pro správu zdrojového kódu.

### <a name="create-and-update-initiative-definitions"></a>Vytvoření a aktualizace definic iniciativ

Stejně tak iniciativy mají vlastní soubor JSON a související soubory, které by měly být uloženy ve stejné složce. Definice iniciativy vyžaduje, aby definice zásady již existovala, takže ji nelze vytvořit nebo aktualizovat, dokud nebude zdroj pro tuto zásadu aktualizován ve správě zdrojového kódu a následně aktualizován v Azure. Následující struktura je doporučeným způsobem, jak zachovat definice iniciativ ve správě zdrojového kódu:

```text
.
|
|- initiatives/ ______________________ # Root folder for initiatives
|  |- init1/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- assign.<name1>.json _________ # Assignment 1 for this policy initiative
|     |- assign.<name2>.json _________ # Assignment 2 for this policy initiative
|
|  |- init2/ _________________________ # Subfolder for an initiative
|     |- policyset.json ______________ # Initiative definition
|     |- policyset.definitions.json __ # Initiative list of policies
|     |- policyset.parameters.json ___ # Initiative definition of parameters
|     |- assign.<name1>.json _________ # Assignment 1 for this policy initiative
|     |- assign.<name2>.json _________ # Assignment 2 for this policy initiative
|
```

Podobně jako definice zásad při přidávání nebo aktualizaci stávající iniciativy by měl pracovní postup automaticky aktualizovat definici iniciativy v Azure. Testování nové nebo aktualizované definice iniciativy se nachází v pozdějším kroku.

### <a name="test-and-validate-the-updated-definition"></a>Testování a ověření aktualizované definice

Jakmile automatizace povede vaše nově vytvořené nebo aktualizované definice zásad nebo iniciativy a provedla aktualizaci objektu v Azure, je čas otestovat provedené změny. Zásady nebo podněty, které jsou součástí této části, by měly být přiřazeny k prostředkům v prostředí, které jsou nejvíce z výroby. Toto prostředí obvykle slouží jako _vývoj_.

Přiřazení by mělo používat [enforcementMode](./assignment-structure.md#enforcement-mode) _zakázané_ , aby se vytváření a aktualizace prostředků neblokovaly, ale aby se stávající prostředky ještě auditoval na dodržování předpisů aktualizované definice zásad. I v případě enforcementMode se doporučuje, aby obor přiřazení byl buď skupina prostředků, nebo předplatné, které je specifické pro ověřování zásad.

> [!NOTE]
> I když je režim vynucení užitečný, není náhrada za účelem důkladného testování definice zásad za různých podmínek. Definice zásad by měla být testována pomocí `PUT` a `PATCH` REST API volání, kompatibilních a nekompatibilních prostředků a hranové případy, jako je vlastnost chybějící v prostředku.

Po nasazení přiřazení použijte Azure Policy SDK, [akci githubu Azure Policy kontrolu kompatibility](https://github.com/marketplace/actions/azure-policy-compliance-scan)nebo [úlohu hodnocení Azure Pipelines zabezpečení a dodržování předpisů](/azure/devops/pipelines/tasks/deploy/azure-policy) , která [vám umožní získat data dodržování předpisů](../how-to/get-compliance-data.md) pro nové přiřazení. Prostředí, které se používá k testování zásad a přiřazení, by mělo mít odpovídající i nekompatibilní prostředky.
Stejně jako dobrý test jednotek kódu, chcete testovat, že prostředky jsou podle očekávání a že také nemáte žádné falešně pozitivní nebo falešně negativní. Pokud otestujete a ověříte pouze za to, co očekáváte, může dojít k neočekávanému a Neidentifikovanému dopadu zásad. Další informace najdete v tématu [vyhodnocení dopadu nové definice Azure Policy](./evaluate-impact.md).

### <a name="enable-remediation-tasks"></a>Povolit úlohy nápravy

Pokud ověření přiřazení splňuje očekávání, dalším krokem je ověření nápravy.
Zásady, které používají [deployIfNotExists](./effects.md#deployifnotexists) nebo [Modify](./effects.md#modify) , mohou být přeměněny na úlohu nápravy a správné prostředky z nevyhovujícího stavu.

Prvním krokem k opravaí prostředků je udělení přiřazení zásad přiřazení role definované v definici zásady. Přiřazení této role poskytuje zásadám oprávnění ke správě dostatečná práva, aby bylo možné provést potřebné změny pro zajištění kompatibility prostředků.

Jakmile přiřazení zásady má příslušná práva, pomocí sady SDK zásad aktivujte úlohu nápravy proti sadě prostředků, u kterých je známo, že nejsou kompatibilní. Než budete pokračovat, měli byste pro tyto napravované úlohy dokončit tři testy:

- Ověřte, že se úloha opravy úspěšně dokončila.
- Spusťte vyhodnocení zásad, abyste viděli, že se výsledky dodržování zásad aktualizují podle očekávání.
- Spustit test jednotky prostředí proti prostředkům přímo k ověření, že se změnily vlastnosti

Při testování aktualizovaných výsledků hodnocení zásad a prostředí je přímo zajištěno, že se úlohy nápravy změnily očekávaným způsobem a že definice zásad zjistila změnu dodržování předpisů podle očekávání.

### <a name="update-to-enforced-assignments"></a>Aktualizace k vynutilému přiřazení

Po dokončení všech bran ověřování aktualizujte přiřazení tak, aby bylo _povoleno_ používání **enforcementMode** . Doporučuje se tuto změnu zpočátku udělat ve stejném prostředí daleko z výroby. Jakmile se toto prostředí ověří podle očekávání, měla by být tato změna vymezená tak, aby zahrnovala další prostředí, a tak dále, dokud se tato zásada nenasazením do produkčních prostředků.

## <a name="process-integrated-evaluations"></a>Zpracování integrovaných vyhodnocení

Obecný pracovní postup pro Azure Policy jako kód je určen pro vývoj a nasazování zásad a iniciativ do prostředí ve velkém měřítku. Vyhodnocení zásad by ale mělo být součástí procesu nasazení pro libovolný pracovní postup, který nasazuje nebo vytváří prostředky v Azure, jako je nasazení aplikací nebo spouštění šablon ARM, aby bylo možné vytvořit infrastrukturu.

V těchto případech platí, že po nasazení aplikace nebo infrastruktury do testovacího předplatného nebo skupiny prostředků by se mělo provést vyhodnocení zásad pro tento rozsah ověřování všech stávajících zásad a iniciativ. I když můžou být v takovém prostředí nakonfigurované jako **enforcementMode** _zakázané_ , je užitečné znát počáteční informace v případě, že nasazení aplikace nebo infrastruktury v brzké době neporušila definice zásad. Toto vyhodnocení zásad by proto mělo být krok v těchto pracovních postupech a neúspěšné nasazení, které vytváří nekompatibilní prostředky.

## <a name="review"></a>Opakování

Tento článek popisuje obecný pracovní postup pro Azure Policy jako kód a také v případě, kdy by hodnocení zásad mělo být součástí dalších pracovních postupů nasazení. Tento pracovní postup se dá použít v jakémkoli prostředí, které podporuje skriptované kroky a automatizaci na základě triggerů. Kurz týkající se použití tohoto pracovního postupu na GitHubu najdete v tématu [kurz: implementace Azure Policy jako kódu pomocí GitHubu](../tutorials/policy-as-code-github.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [struktuře definic zásad](./definition-structure.md).
- Přečtěte si o [struktuře přiřazení zásad](./assignment-structure.md).
- Zjistěte, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
