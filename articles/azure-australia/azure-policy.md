---
title: Dodržování předpisů při zabezpečení Azure Policy a Azure modrotisky
description: Zajištění dodržování předpisů a vynucování zabezpečení pomocí Azure Policy a plánů Azure pro státní úřady Austrálie v oblasti IT, které se vztahují k ASD ISM a Essential 8
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0eb3e669f622a9c09c903530cbbab19206ce227f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571741"
---
# <a name="security-compliance-with-azure-policy-and-azure-blueprints"></a>Dodržování předpisů při zabezpečení Azure Policy a Azure modrotisky

V případě, že se jedná o vynucování zásad správného řízení v rámci IT prostředí, ať už jde o místní, cloudové nebo hybridní prostředí, existuje pro všechny organizace. Aby se zajistilo, že Microsoft Azure prostředí vyhovuje požadavkům na návrh, právní a bezpečnostní požadavky, musí být zavedena robustní architektura technického řízení.

U vládních úřadů pro Austrálie mají klíčové kontroly, které je potřeba vzít v úvahu při posuzování rizika v [australském informačním centru acsc (Internet Information Security Center](https://acsc.gov.au/infosec/ism/index.htm) ). Většina ovládacích prvků podrobná v rámci ISM vyžaduje, aby bylo možné efektivně spravovat a vynutit používání technického řízení. Je důležité, abyste měli vhodné nástroje pro vyhodnocení a prosazování konfigurace ve vašich prostředích.

Microsoft Azure poskytuje dvě bezplatné služby, které vám pomůžou s těmito výzvami, Azure Policy a plány Azure.

## <a name="azure-policy"></a>Azure Policy

Azure Policy umožňuje použití technických prvků zásad správného řízení IT v organizaci. Azure Policy obsahuje stále rostoucí knihovnu předdefinovaných zásad. Každá zásada vynutila pravidla a účinky na cílené prostředky Azure.

Po přiřazení zásad k prostředkům je možné vyhodnotit celkový soulad s touto zásadou a v případě potřeby je opravit.

Tato knihovna integrovaných zásad Azure umožňuje organizacím rychle vyhovět typům ovládacích prvků, které najdete v ACSC ISM. Mezi příklady ovládacích prvků patří:

* Monitorování virtuálních počítačů pro chybějící aktualizace systému
* Auditování účtů se zvýšenými oprávněními pro službu Multi-Factor Authentication
* Identifikace nešifrovaných databází SQL
* Monitorování používání vlastního řízení přístupu na základě role v Azure (RBAC)
* Omezení oblastí Azure, ve kterých se dají prostředky vytvářet

Pokud ovládací prvky řízení nebo regulace nejsou splněné pomocí předdefinované definice Azure Policy, může být vytvořena a přiřazena vlastní definice. Všechny definice Azure Policy jsou definovány ve formátu JSON a odpovídají standardní [struktuře definic](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure). Existující definice Azure Policy také mohou být duplikovány a použity k vytvoření základu definice vlastní zásady.

Přiřazování jednotlivých zásad Azure k prostředkům, zejména ve složitých prostředích nebo v prostředí s přísnými zákonnými požadavky, může pro správce vytvořit velkou režii. Pro pomoc s těmito problémy se dá seskupit sada zásad Azure dohromady, aby bylo možné vytvořit Azure Policy iniciativu. Iniciativa zásad se používá ke kombinování souvisejících zásad Azure, které se při společném použití jako skupiny tvoří základem konkrétního cíle zabezpečení nebo dodržování předpisů. Microsoft přidává předdefinované definice iniciativ Azure Policy, včetně definic určených pro splnění konkrétních zákonných požadavků:

![Iniciativy týkající se dodržování předpisů v legislativních zásadách](media/regulatory-initiatives.png)

Všechny zásady a iniciativy Azure jsou přiřazené k oboru přiřazení. Tento obor je definovaný buď na úrovni předplatného Azure, skupiny pro správu Azure, nebo na úrovni skupiny prostředků Azure. Po přiřazení požadovaných zásad Azure nebo iniciativ zásad bude organizace moct vyhovět požadavkům na konfiguraci všech nově vytvořených prostředků Azure.

Přiřazení nové Azure Policy nebo iniciativy nebude mít vliv na existující prostředky Azure. Azure Policy může; ale umožněte organizaci zobrazit dodržování předpisů u stávajících prostředků Azure. Všechny prostředky, které se identifikovaly jako nevyhovující předpisům, se dají opravit na uvážení organizace.

### <a name="azure-policy-and-initiatives-in-action"></a>Azure Policy a iniciativy v akci

Dostupné předdefinované definice Azure Policy a iniciativy najdete pod uzlem definice v části zásady v Azure Portal:

![Předdefinované definice Azure Policy](media/policy-definitions.png)

Pomocí knihovny předdefinovaných definic můžete rychle vyhledat zásady, které splňují požadavky organizace, zkontrolovat definici zásad a přiřadit tyto zásady odpovídajícím prostředkům. Například ISM vyžaduje vícefaktorové ověřování (MFA) pro všechny privilegované uživatele a pro všechny uživatele, kteří mají přístup k důležitým úložištím dat. V Azure Policy můžete pro Azure Policy definice vyhledat "MFA":

![Zásady Azure MFA](media/mfa-policies.png)

Jakmile se identifikují vhodné zásady, přiřadíte ji k požadovanému oboru. Pokud není k dispozici žádná předdefinovaná zásada, která by splňovala vaše požadavky, můžete duplikovat stávající zásady a provést požadované změny:

![Duplikovat existující Azure Policy](media/duplicate-policy.png)

Microsoft taky poskytuje kolekci Azure Policy ukázek na GitHubu [](https://github.com/Azure/azure-policy) jako "rychlý Start", abyste mohli vytvářet vlastní zásady Azure. Tyto ukázky zásad lze zkopírovat přímo do editoru Azure Policy v rámci Azure Portal.

Při vytváření Azure Policy iniciativ můžete seřadit seznam dostupných definic zásad, integrovaných i vlastních, a přidat požadované definice.

Můžete například vyhledat seznam dostupných Azure Policych definic pro všechny zásady týkající se virtuálních počítačů s Windows. Pak jsou tyto definice iniciativy navržené pro zajištění doporučených postupů posílení virtuálních počítačů:

![Seznam zásad Azure](media/initiative-definitions.png)

Když přiřadíte Azure Policy nebo iniciativu zásad k oboru přiřazení, můžete vyloučit prostředky Azure z účinků zásad vyloučením Azure Skupiny pro správu nebo skupin prostředků Azure.

### <a name="real-time-enforcement-and-compliance-assessment"></a>Vynucování a vyhodnocení dodržování předpisů v reálném čase

Azure Policy prověřování dodržování předpisů v rámci oboru prostředků Azure se provádí při splnění následujících podmínek:

* Když je přiřazena iniciativa Azure Policy nebo Azure Policy
* Když se změní rozsah existující Azure Policy nebo iniciativy
* Na vyžádání přes rozhraní API až do maximálního počtu 10 kontrol za hodinu
* Každých 24 hodin – výchozí chování

Kontrola dodržování zásad pro jeden prostředek Azure se provádí 15 minut po provedení změny prostředku.

Přehled Azure Policy dodržování předpisů u prostředků se dá zkontrolovat v rámci Azure Portal prostřednictvím řídicího panelu pro dodržování zásad:

![Azure Policy skóre dodržování předpisů](media/simple-compliance.png)

Celkový počet procent dodržování předpisů prostředků je agregovaný z hlediska dodržování předpisů u všech nasazených prostředků v oboru proti všem vašim přiřazeným zásadám Azure. To vám umožní identifikovat prostředky v prostředí, které nedodržují předpisy, a navrhnout plán, který vám umožní nejlépe napravit tyto prostředky.

Řídicí panel dodržování zásad obsahuje také historii změn pro jednotlivé prostředky. Pokud je prostředek označený jako nekompatibilní s přiřazenou zásadou a automatická náprava není povolená, můžete zobrazit, kdo provedl změnu, co se změnil a kdy byly provedeny změny daného prostředku.

## <a name="azure-blueprints"></a>Azure Blueprint

Azure modrotisky rozšiřuje možnosti Azure Policy tak, že je zkombinujete s:

* Azure RBAC
* Skupiny prostředků Azure
* [Šablony Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

Plány umožňují vytváření návrhů prostředí, které nasazují prostředky Azure ze šablon Správce prostředků, konfigurují RBAC a vystavují a auditují konfiguraci pomocí přiřazování Azure Policy. Plány tvoří upravitelnou a znovu nasazovatelné šablony prostředí. Po vytvoření podrobného plánu je možné ho přiřadit k předplatnému Azure. Po přiřazení se vytvoří všechny prostředky Azure definované v rámci plánu a použijí se zásady Azure. Nasazení a konfiguraci prostředků definovaných v Azure Blueprint lze monitorovat z konzoly Azure Blueprint v Azure Portal.

Plány Azure, které byly upraveny, musí být znovu publikovány v Azure Portal. Pokaždé, když se znovu publikuje podrobný plán, číslo verze podrobného plánu se zvýší. Číslo verze umožňuje určit, která konkrétní verze podrobného plánu byla nasazena do předplatných Azure organizace. V případě potřeby je možné aktuálně přiřazenou verzi podrobného plánu aktualizovat na nejnovější verzi.

Prostředky nasazené pomocí Azure Blueprint se dají nakonfigurovat pomocí [zámků prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) v době nasazení. Zámky prostředků zabraňují nechtěné změně nebo odstranění prostředků.

Microsoft vyvíjí Azure Blueprint šablony pro řadu oborů a zákonných požadavků. Aktuální knihovnu dostupných definicí Azure Blueprint lze zobrazit na Azure Portal nebo na stránce [podrobný plán zabezpečení a dodržování předpisů Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview/) na portálu vztahu důvěryhodnosti služby.

### <a name="azure-blueprint-artifacts"></a>Azure Blueprint artefakty

Pokud chcete vytvořit Azure Blueprint, můžete začít s prázdnou šablonou podrobného plánu nebo použít jako výchozí bod jednu ze stávajících ukázkových plánů. Do podrobného plánu můžete přidat artefakty, které budou nakonfigurovány jako součást nasazení:

![Azure Blueprint artefakty](media/blueprint-artifacts.png)

Mezi tyto artefakty můžou patřit skupiny prostředků Azure a prostředky a související Azure Policy a iniciativy zásad, které vynutily konfiguraci potřebnou pro vaše prostředí, aby vyhovovala vašim zákonným požadavkům, například ovládacím prvkům ISM. pro posílení zabezpečení systému.

U každého z těchto artefaktů je také možné nakonfigurovat parametry. Tyto hodnoty jsou k dispozici v případě, že byl plán plánu přiřazen k předplatnému Azure a nasazen. Parametry umožňují vytvořit jeden podrobný plán a použít ho k nasazení prostředků do různých prostředí, aniž byste museli upravovat základní plán.

Společnost Microsoft vyvíjí rutiny Azure PowerShell a CLI k vytváření a správě plánů Azure s cílem, který organizace může spravovat a nasazovat prostřednictvím kanálu CI/CD.

## <a name="next-steps"></a>Další postup

Tento článek vysvětluje, jak se dá vyhovět zásadám správného řízení a zabezpečení pomocí Azure Policy a Azure modrotisky. Teď, když jste byli vystaveni na vysoké úrovni, se dozvíte, jak používat jednotlivé služby podrobněji:

* [Přehled Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview)
* [Přehled Azure modrotisky](https://azure.microsoft.com/services/blueprints/)
* [Ukázky služby Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/index)
* [Úložiště ukázek Azure Policy](https://github.com/Azure/azure-policy)
* [Azure Policy struktura definice](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)
* [Azure Policy efekty](https://docs.microsoft.com/azure/governance/policy/concepts/effects)
