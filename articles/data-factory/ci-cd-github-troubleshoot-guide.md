---
title: Řešení potíží s CI-CD, Azure DevOps a GitHubem v ADF
description: K odstraňování potíží s CI-CD v ADF použijte různé metody.
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 03/12/2021
ms.openlocfilehash: 4be015b1a8ba4b6fc6ea3acc74318f9a8b298e8e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103418092"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>Řešení potíží s CI-CD, Azure DevOps a GitHubem v ADF 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje běžné metody řešení potíží pro průběžné nasazování Integration-Continuous (CI-CD), problémy s Azure DevOps a GitHub v Azure Data Factory.

Pokud máte dotazy nebo problémy s používáním technik správy zdrojového kódu nebo DevOps, najdete tady několik článků, které mohou být užitečné:

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

K této chybě dochází, protože často odstraňujeme Trigger, který je parametrizovaný, takže parametry nebudou k dispozici v šabloně ARM (protože aktivační událost už neexistuje). Vzhledem k tomu, že parametr už není v šabloně ARM, musíme aktualizovat přepsané parametry v kanálu DevOps. Jinak se při každé změně parametrů v šabloně ARM musí aktualizovat přepsané parametry v kanálu DevOps (v úloze nasazení).

### <a name="updating-property-type-is-not-supported"></a>Aktualizace typu vlastnosti není podporovaná.

#### <a name="issue"></a>Problém

Chyba kanálu CI/CD se nezdařila s následující chybou:

`
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
`

#### <a name="cause"></a>Příčina

Důvodem je prostředí Integration runtime se stejným názvem v cílovém objektu pro vytváření, ale s jiným typem. Integration Runtime musí být stejného typu při nasazení.

#### <a name="recommendation"></a>Doporučení

- Následující osvědčené postupy pro CI/CD najdete níže:

    https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd 
- Prostředí Integration runtime se často nemění a jsou v rámci CI/CD ve všech fázích podobná, takže Data Factory očekává, že budete mít stejný název a typ prostředí Integration runtime ve všech fázích CI/CD. Pokud se název a typy & liší, ujistěte se, že odpovídají konfiguraci zdrojového a cílového prostředí Integration runtime a pak nasaďte kanál pro vydávání verzí.
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

#### <a name="resolution"></a>Řešení

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

Aby bylo možné operaci přesunu, je nutné odstranit SSIS-IR a sdílený finanční úřad. Pokud nechcete prostředí Integration Runtime odstranit, nejlepším způsobem je postupovat podle kopie a klonovaného dokumentu a provést kopírování a po jeho dokončení odstranit původní Data Factory.

###  <a name="unable-to-export-and-import-arm-template"></a>Nepovedlo se exportovat a importovat šablonu ARM.

#### <a name="issue"></a>Problém

Nepovedlo se exportovat a importovat šablonu ARM. Na portálu se nevyskytla žádná chyba. v trasování prohlížeče se ale zobrazila následující chyba:

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>Příčina

Vytvořili jste roli zákazníka jako uživatel a neměli jste potřebná oprávnění. Když je objekt pro vytváření v uživatelském rozhraní načten, je zaškrtnuta řada hodnot řízení expozice pro objekt factory. V takovém případě nemá role přístupu uživatele oprávnění pro přístup k rozhraní *queryFeaturesValue* API. Pro přístup k tomuto rozhraní API je funkce globálních parametrů vypnutá. Cesta k exportnímu kódu ARM se částečně spoléhá na funkci globálních parametrů.

#### <a name="resolution"></a>Řešení

Aby bylo možné tento problém vyřešit, musíte do své role přidat následující oprávnění: *Microsoft. DataFactory/Factory/queryFeaturesValue/Action*. Toto oprávnění by mělo být ve výchozím nastavení zahrnuto v roli "Data Factory Přispěvatel".

###  <a name="automatic-publishing-for-cicd-without-clicking-publish-button"></a>Automatické publikování pro CI/CD bez kliknutí na tlačítko publikovat  

#### <a name="issue"></a>Problém

Ruční publikování pomocí kliknutí na tlačítko na portálu ADF nepovoluje automatickou operaci CI/CD.

#### <a name="cause"></a>Příčina

Až do poslední doby používala publikování kanálu ADF pro nasazení na portálu ADF kliknutí na tlačítko. Nyní můžete proces automaticky nastavit. 

#### <a name="resolution"></a>Řešení

Proces CI/CD byl vylepšen. Funkce **automatického publikování** přijímá, ověřuje a exportuje všechny funkce šablon Azure Resource Manager (ARM) z uživatelského prostředí ADF. Díky tomu dá Logical spotřební přístup prostřednictvím veřejně dostupného balíčku npm [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . To vám umožní programově aktivovat tyto akce místo nutnosti přejít do uživatelského rozhraní ADF a provést kliknutí na tlačítko. Díky tomu budou kanály CI/CD **platit skutečným** prostředím průběžné integrace. Podrobnosti najdete v podrobnostech o [vylepšení publikování ADF/CD](./continuous-integration-deployment-improvements.md) . 

###  <a name="cannot-publish-because-of-4mb-arm-template-limit"></a>Nejde publikovat kvůli limitu 4 MB ARM šablony.  

#### <a name="issue"></a>Problém

Nasazení nemůžete nasadit, protože jste dosáhli Azure Resource Manager limitu velikosti 4 MB pro celkovou velikost šablony. Po překročení limitu budete potřebovat řešení k nasazení. 

#### <a name="cause"></a>Příčina

Azure Resource Manager omezuje velikost šablony na 4 MB. Omezte velikost šablony na 4 MB a každý soubor parametrů na 64 KB. Limit velikosti 4 MB se vztahuje na konečný stav šablony po rozbalení pomocí iterativních definic prostředků a hodnot proměnných a parametrů. Ale jste překročili limit. 

#### <a name="resolution"></a>Řešení

U malých až středních řešení je jednodušší pochopit a spravovat jedinou šablonu. Všechny prostředky a hodnoty vidíte v jednom souboru. Ve složitějších scénářích umožňují propojené šablony rozdělit řešení do specializovaných komponent. Dodržujte prosím osvědčené postupy na [používání propojených a vnořených šablon](../azure-resource-manager/templates/linked-templates.md?tabs=azure-powershell).

### <a name="cannot-connect-to-git-enterprise-cloud"></a>Nejde se připojit k GIT Enterprise Cloud 

##### <a name="issue"></a>Problém

Nemůžete se připojit k systému GIT Enterprise Cloud z důvodu problémů s oprávněními. Zobrazí se chyba, jako je například **422 – nedokončená entita.**

#### <a name="cause"></a>Příčina

* Používáte Git Enterprise na serveru Prem. 
* Nenakonfigurovali jste OAuth pro ADF. 
* Vaše adresa URL je chybně nakonfigurovaná.

##### <a name="resolution"></a>Řešení

Nejprve udělíte přístup OAuth k ADF. Pak je nutné použít správnou adresu URL pro připojení k GITU Enterprise. Konfigurace musí být nastavená na organizace zákazníků. Například ADF se pokusí *https://hostname/api/v3/search/repositories?q=user%3 <customer credential> ....* při prvním selhání a selhání. Pak se to pokusí *https://hostname/api/v3/orgs/ <org> / <repo> ...* a úspěch. 
 
### <a name="recover-from-a-deleted-data-factory"></a>Obnovení z odstraněné datové továrny

#### <a name="issue"></a>Problém
Zákazník odstranil datovou továrnu nebo skupinu prostředků obsahující Data Factory. Chtěli bychom se seznámit s tím, jak obnovit odstraněnou datovou továrnu.

#### <a name="cause"></a>Příčina

Data Factory lze obnovit pouze v případě, že zákazník má nakonfigurovanou správu zdrojového kódu (DevOps nebo Git). Tím dojde k převedení všech nejnovějších publikovaných prostředků **a neobnoví se** nepublikovaný kanál, datovou sadu a propojenou službu.

Pokud není k dispozici Správa zdrojového kódu, není možné obnovit odstraněné Data Factory z back-endu, protože jakmile se příkaz dostanou, instance se odstraní a nebude uložena žádná záloha.

#### <a name="resolution"></a>Řešení

Postup obnovení odstraněných Data Factory se správou zdrojových kódů najdete v následujících krocích:

 * Vytvoří nový Azure Data Factory.

 * Překonfigurujte Git se stejnými nastaveními, ale ujistěte se, že jste importovali existující Data Factory prostředky do vybraného úložiště a zvolíte možnost Nová větev.

 * Vytvořte žádost o přijetí změn, která sloučí změny do větve pro spolupráci a publikuje ji.

 * Pokud zákazník měl v odstraněných ADF Integration Runtime v místním prostředí, bude muset vytvořit novou instanci v novém ADF, taky odinstalovat a znovu nainstalovat instanci na svém počítači nebo VIRTUÁLNÍm počítači s Prem s nově získaným klíčem. Po dokončení instalace prostředí IR bude muset zákazník změnit propojenou službu tak, aby odkazovala na nové INFRAČERVENé prostředí a otestovali připojení, nebo selže s chybou **neplatným odkazem.**



## <a name="next-steps"></a>Další kroky

Další pomoc při řešení potíží najdete v následujících zdrojích informací:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum přetečení zásobníku pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)
