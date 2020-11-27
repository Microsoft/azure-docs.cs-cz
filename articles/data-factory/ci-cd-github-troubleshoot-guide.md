---
title: Řešení potíží s CI-CD, Azure DevOps a GitHubem v ADF
description: K odstraňování potíží s CI-CD v ADF použijte různé metody.
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 11/26/2020
ms.openlocfilehash: f07cc8e3d5e9d6f59671a3c8c2efd9f5fb9f27b7
ms.sourcegitcommit: 236014c3274b31f03e5fcee5de510f9cacdc27a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96299052"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>Řešení potíží s CI-CD, Azure DevOps a GitHubem v ADF 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje běžné metody řešení potíží pro CI-CD, Azure DevOps a GitHub problémy v Azure Data Factory.

Pokud máte dotazy nebo problémy s používáním technik správy zdrojového kódu nebo DevOps, najdete tady několik článků, které mohou být užitečné.:

- Informace o tom, jak je Správa zdrojového kódu v ADF, najdete [v tématu Správa zdrojového kódu v ADF](source-control.md) . 
- Další informace o tom, jak se DevOps CI-CD funguje v ADF, najdete  [v tématu CI-CD v ADF](continuous-integration-deployment.md) .
 
## <a name="common-errors-and-messages"></a>Běžné chyby a zprávy

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>Připojení k úložišti Git se nepovedlo kvůli jinému tenantovi.

#### <a name="issue"></a>Problém
    
Někdy dochází k potížím s ověřováním, jako je stav HTTP 401. Hlavně když máte více tenantů s účtem hosta, můžou být věci složitější.

#### <a name="cause"></a>Příčina

Zjistili jsme, že token byl získán z původního tenanta, ale ADF je v tenantovi host a při pokusu o použití tokenu k návštěvě DevOps v tenantovi hosta. Nejedná se o očekávané chování.

#### <a name="recommendation"></a>Doporučení

Místo toho byste měli použít token vydaný z tenanta hosta. Například musíte přiřadit stejný Azure Active Directory jako váš tenant hosta i vaše DevOps, aby mohl správně nastavit chování tokenu a používat správného tenanta.

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>Parametry šablony v souboru parametrů nejsou platné.

#### <a name="issue"></a>Problém

Pokud odstraníme Trigger ve vývojářské větvi, který je už dostupný v testovací nebo produkční větvi se **stejnou** konfigurací (například frekvence a interval), pak se nasazení kanálu vydává úspěšně a odpovídající Trigger se odstraní v příslušných prostředích. Pokud ale máte **jinou** konfiguraci (například četnost a interval) triggeru v testovacím nebo produkčním prostředí, a pokud odstraníte stejnou aktivační událost ve vývojovém prostředí, nasazení se v případě chyby nezdařila.

#### <a name="cause"></a>Příčina

Kanál CI/CD se nezdařil s následující chybou:

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>Doporučení

K této chybě dochází, protože často odstraňujeme Trigger, který je parametrizovaný, takže parametry nebudou v šabloně ARM dostupné (protože aktivační událost už neexistuje). Vzhledem k tomu, že parametr už není v šabloně ARM, musíme aktualizovat přepsané parametry v kanálu DevOps. Jinak se při každé změně parametrů v šabloně ARM musí aktualizovat přepsané parametry v kanálu DevOps (v úloze nasazení).

### <a name="updating-property-type-is-not-supported"></a>Aktualizace typu vlastnosti není podporovaná.

#### <a name="issue"></a>Problém

Chyba kanálu CI/CD se nezdařila s následující chybou:

`
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
`

#### <a name="cause"></a>Příčina

Důvodem je Integration Runtime se stejným názvem v cílové továrně, ale s jiným typem. Integration Runtime musí být stejného typu při nasazení.

#### <a name="recommendation"></a>Doporučení

- Následující osvědčené postupy pro CI/CD najdete níže:

    https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd 
- Prostředí Integration runtime se často nemění a jsou v rámci CI/CD ve všech fázích podobná, takže Data Factory očekává, že budete mít stejný název a typ prostředí Integration runtime ve všech fázích CI/CD. Pokud se názvy a typy & liší, ujistěte se, že se shodují se zdrojovou a cílovou konfigurací IR a pak nasaďte kanál pro vydávání verzí.
- Pokud chcete sdílet prostředí Integration runtime ve všech fázích, zvažte použití Ternární továrny jenom k zahrnutí sdílených prostředí Integration runtime. Tuto sdílenou továrnu můžete použít ve všech prostředích jako typ propojeného prostředí Integration runtime.

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>Vytvoření nebo aktualizace dokumentu se nezdařila z důvodu neplatného odkazu.

#### <a name="issue"></a>Problém

Při pokusu o publikování změn Data Factory se zobrazí následující chybová zpráva:

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`

#### <a name="symptom"></a>Příznak

Ododpojili jste konfiguraci Gitu a znovu ji nastavili s vybraným příznakem importovat prostředky, který nastaví Data Factory jako "synchronizované". To znamená, že se nemění žádné změny k publikování.

**Řešení**

Odpojte konfiguraci Gitu a znovu ji nastavte a ujistěte se, že jste zaškrtli políčko importovat existující prostředky.

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>Neúspěšné přesunutí z jedné skupiny prostředků do jiné Data Factory

#### <a name="issue"></a>Problém

Nemůžete přesunout Data Factory z jedné skupiny prostředků do jiné. chyba se nezdařila s následující chybou:

`
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>Řešení

Aby bylo možné operaci přesunu, je nutné odstranit SSIS-IR a sdílený finanční úřad. Pokud nechcete toto finanční úřad odstranit, nejlepším způsobem, jak postupovat podle kopie a klonovaného dokumentu, provést kopírování a po jeho dokončení odstranit starou datovou továrnu.

###  <a name="unable-to-export-and-import-arm-template"></a>Nepovedlo se exportovat a importovat šablonu ARM.

#### <a name="issue"></a>Problém

Nepovedlo se exportovat a importovat šablonu ARM. Na portálu se nevyskytla žádná chyba. v trasování prohlížeče se ale zobrazila následující chyba:

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>Příčina

Vytvořili jste roli zákazníka jako uživatel a neměli jste potřebná oprávnění. Když je objekt pro vytváření v uživatelském rozhraní načten, je zaškrtnuta řada hodnot řízení expozice pro objekt factory. V takovém případě nemá role přístupu uživatele oprávnění pro přístup k rozhraní *queryFeaturesValue* API. Pro přístup k tomuto rozhraní API je funkce globálních parametrů vypnutá. Cesta k exportnímu kódu ARM se částečně spoléhá na funkci globálních parametrů.

#### <a name="resolution"></a>Řešení

Aby bylo možné tento problém vyřešit, musíte do své role přidat následující oprávnění: *Microsoft. DataFactory/Factory/queryFeaturesValue/Action*. Toto oprávnění by mělo být ve výchozím nastavení zahrnuto v roli "Data Factory Přispěvatel".

## <a name="next-steps"></a>Další kroky

Další pomoc při řešení potíží najdete v následujících zdrojích informací:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stránka s otázkou Microsoft Q&](/answers/topics/azure-data-factory.html)
*  [Fórum přetečení zásobníku pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)


 
