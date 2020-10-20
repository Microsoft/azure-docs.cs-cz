---
title: DevOps pro Azure API Management s využitím šablon ARM
description: Úvod do rozhraní API DevOps s využitím Azure API Management, použití šablon Azure Resource Manager ke správě nasazení rozhraní API v kanálu CI/CD
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: apimpm
ms.openlocfilehash: 144baa0944451adaf08f0df84325b58079e19d65
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209714"
---
# <a name="cicd-for-api-management-using-azure-resource-manager-templates"></a>CI/CD pro API Management používání šablon Azure Resource Manager

Tento článek zavádí rozhraní API DevOps s využitím Azure API Management pomocí šablon Azure Resource Manager. Díky strategické hodnotě rozhraní API se kanál průběžné integrace (CI) a průběžného nasazování (CD) stane důležitým aspektem vývoje rozhraní API. Umožňuje organizacím automatizovat nasazení změn rozhraní API bez chybově náchylných kroků, detekovat problémy dříve a nakonec doručovat hodnoty koncovým uživatelům rychleji. 

Podrobnosti, nástroje a ukázky kódu pro implementaci přístupu DevOps popsaného v tomto článku najdete v tématu Open source [Sada Resource Kit pro Azure API Management DevOps](https://github.com/Azure/azure-api-management-devops-resource-kit) v GitHubu. Vzhledem k tomu, že zákazníci přinášejí celou škálu inženýrských kultur a stávající řešení pro automatizaci, není přístup jedním řešením.

## <a name="the-problem"></a>Problém

Organizace dnes mají obvykle několik prostředí nasazení (například vývoj, testování, produkce) a pro každé prostředí používají samostatné instance API Management. Některé instance sdílí více vývojových týmů, kteří zodpovídají za různá rozhraní API s různou verzí tempem.

Výsledkem je, že zákazníci čelí následujícím problémům:

* Jak automatizovat nasazení rozhraní API do API Management?
* Jak migrovat konfigurace z jednoho prostředí do jiného?
* Jak se vyhnout rušení mezi různými vývojovými týmy, které sdílejí stejnou instanci API Management?

## <a name="manage-configurations-in-resource-manager-templates"></a>Správa konfigurací v šablonách Správce prostředků

Navrhovaný přístup je znázorněn na následujícím obrázku. 

:::image type="content" source="media/devops-api-development-templates/apim-devops.png" alt-text="DevOps s API Management":::

V tomto příkladu jsou k dispozici dvě prostředí nasazení: *vývoj* a *produkce*. Každá z nich má svou vlastní instanci API Management. 

* Vývojáři rozhraní API mají přístup k instanci vývoje a můžou ji použít pro vývoj a testování svých rozhraní API. 
* Produkční instance je spravovaná určeným týmem, který se nazývá *vydavatelé rozhraní API*.

Klíčem k tomuto navrhovanému přístupu je udržování všech konfigurací API Management v [šablonách Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Tyto šablony by se měly uchovávat v systému správy zdrojového kódu, jako je třeba Git. Jak je znázorněno na obrázku, existuje úložiště vydavatelů, které obsahuje všechny konfigurace produkční API Management instance v kolekci šablon:

|Šablona  |Popis  |
|---------|---------|
|Šablona služby     | Konfigurace na úrovni služby API Management instance, jako je například cenová úroveň a vlastní domény         |
|Sdílené šablony     |  Sdílené prostředky v rámci instance API Management, jako jsou skupiny, produkty a protokolovací nástroje    |
|Šablony rozhraní API     |  Konfigurace rozhraní API a jejich subsources: operace, zásady, nastavení diagnostiky        |
|Šablona hlavní (hlavní)     |   Spojí vše společně s [propojením](../azure-resource-manager/resource-group-linked-templates.md) se všemi šablonami a jejich nasazením v daném pořadí. Pro nasazení všech konfigurací do instance API Management nasaďte hlavní šablonu. Každou šablonu lze nasadit také jednotlivě.       |

Vývojáři rozhraní API budou rozvětvit úložiště vydavatelů do úložiště pro vývojáře a pracovat se změnami pro svá rozhraní API. Ve většině případů se zaměřuje na šablony rozhraní API pro svá rozhraní API a nepotřebují měnit sdílené šablony nebo šablony služeb.

## <a name="migrate-configurations-to-templates"></a>Migrace konfigurací do šablon
Při práci se šablonami Správce prostředků jsou k dispozici problémy vývojářům rozhraní API:

* Vývojáři rozhraní API často pracují se [specifikací openapi](https://github.com/OAI/OpenAPI-Specification) a nemusí být obeznámeni s správce prostředků schématy. Ruční vytváření šablon může být náchylné k chybám. 

   Nástroj s názvem [**Creator**](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#Creator) v sadě prostředků může přispět k automatizaci vytváření šablon rozhraní API založených na otevřeném souboru specifikace API. Kromě toho můžou vývojáři poskytovat API Management zásady pro rozhraní API ve formátu XML. 

* Pro zákazníky, kteří už používají API Management, je další výzvou k extrakci stávajících konfigurací do šablon Správce prostředků. Pro tyto zákazníky může nástroj s názvem [**extraktor**](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#extractor) v sadě prostředků pomáhat generovat šablony extrakcí konfigurací z jejich API Management instancí.  

## <a name="workflow"></a>Pracovní postup

* Jakmile vývojáři rozhraní API dokončí vývoj a testování rozhraní API a vygenerovali šablony rozhraní API, můžou odeslat žádost o přijetí změn, aby se změny sloučily do úložiště vydavatelů. 

* Vydavatelé rozhraní API můžou žádost o přijetí změn ověřit a ujistit se, že jsou tyto změny bezpečné a kompatibilní. Mohou například kontrolovat, zda může komunikovat s rozhraním API pouze protokol HTTPS. Většinu ověření můžete automatizovat jako krok v kanálu CI/CD.

* Jakmile se změny schvalují a sloučí se úspěšně, vydavatelé rozhraní API je můžou zvolit, aby je nasadili do provozní instance, a to buď podle plánu, nebo na vyžádání. Nasazení šablon lze automatizovat pomocí [akcí GitHubu](https://github.com/Azure/apimanagement-devops-samples), [Azure Pipelines](/devops/pipelines/), [Azure POWERSHELL](../azure-resource-manager/templates/deploy-powershell.md), rozhraní příkazového [řádku Azure](../azure-resource-manager/templates/deploy-cli.md)a dalších nástrojů.

Díky tomuto přístupu se nasazení změn rozhraní API do instancí API Management může automatizovat a je snadno možné povýšit změny z jednoho prostředí na jiný. Vzhledem k tomu, že různé vývojové týmy rozhraní API budou pracovat na různých sadách šablon a souborů rozhraní API, zabraňuje rušení mezi různými týmy.

## <a name="video"></a>Video

> [!VIDEO https://www.youtube.com/embed/4Sp2Qvmg6j8]

## <a name="next-steps"></a>Další kroky

- Další informace, nástroje a ukázkové šablony najdete v open source [sadě Azure API Management DevOps Resource Kit](https://github.com/Azure/azure-api-management-devops-resource-kit) .