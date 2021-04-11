---
title: Vyloučení doporučení Azure Security Center od prostředku, předplatného, skupiny pro správu a zabezpečeného skóre
description: Naučte se vytvářet pravidla pro vyloučení doporučení zabezpečení z předplatných nebo skupin pro správu a zabránit jejich vlivu na vaše zabezpečené skóre.
author: memildin
ms.author: memildin
ms.date: 03/11/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: d3627f6bcda7a18204c24fc2a1347c4a512c5369
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149728"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>Vyloučení prostředků a doporučení ze zabezpečeného skóre 

Základní prioritou každého bezpečnostního týmu je zajistit, aby Analytiki se mohli soustředit na úkoly a incidenty, které jsou v souvislosti s organizací. Security Center má mnoho funkcí pro přizpůsobení prostředí a zajištění, že vaše zabezpečené skóre odráží priority zabezpečení vaší organizace. Možnost **vyloučení** je jedna taková funkce.

Když prozkoumáte doporučení zabezpečení v Azure Security Center, je jedním z prvních informací, které jste si prozkoumali, seznam ovlivněných prostředků.

V některých případech bude prostředek uvedený jako seznam, který by se neměl zahrnovat. Případně se v oboru, ve kterém se domníváte, že nepatří, se zobrazí doporučení. Prostředek mohl být opraven procesem, který není sledován nástrojem Security Center. Doporučení může být pro konkrétní předplatné nevhodné. Nebo možná vaše organizace jednoduše rozhodla přijmout rizika související s konkrétním prostředkem nebo doporučením.

V takových případech můžete vytvořit výjimku pro doporučení pro:

- Zajistěte **výjimku prostředku** , abyste se ujistili, že v budoucnu není v pořádku, a neovlivní vaše zabezpečené skóre. Prostředek bude uveden jako nepoužitý a důvod bude zobrazen jako vyloučený s konkrétním odůvodněním, které vyberete.

- **Vyloučení předplatného nebo skupiny pro správu** , aby se zajistilo, že doporučení neovlivní vaše zabezpečené skóre a v budoucnu se nebude zobrazovat pro předplatné nebo skupinu pro správu. To se týká existujících prostředků a všech, které v budoucnu vytvoříte. Doporučení bude označeno konkrétním odůvodněním, které jste vybrali pro vybraný obor.

## <a name="availability"></a>Dostupnost

| Aspekt                          | Podrobnosti                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stav vydaných verzí:                  | Preview<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                                                                                                                                                                                                             |
| Stanov                        | Toto je funkce zásad Azure Premium, která je nabízená pro zákazníky v Azure Defenderu bez dalších nákladů. Pro ostatní uživatele se můžou v budoucnu uplatňovat poplatky.                                                                                                                                                                 |
| Požadované role a oprávnění: | **Vlastník předplatného** nebo **Přispěvatel zásad** pro vytvoření výjimky<br>Chcete-li vytvořit pravidlo, potřebujete oprávnění k úpravám zásad v Azure Policy.<br>Další informace najdete v [Azure Policy oprávnění služby Azure RBAC](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).                                            |
| Omezení:                    | Výjimky je možné vytvořit jenom pro doporučení zahrnutá v výchozích iniciativách Security Center, srovnávacích testech zabezpečení Azure nebo kterékoli ze zadaných regulativních standardních iniciativ. Doporučení, která jsou generována z vlastních iniciativ, nelze vyloučit. Přečtěte si další informace o vztazích mezi [zásadami, iniciativami a doporučeními](security-policy-concept.md). |
| Cloud                         | ![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![No](./media/icons/no-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)                                                                                                                                                                                         |
|                                 |                                                                                                                                                                                                                                                                                                                                    |

## <a name="define-an-exemption"></a>Definovat výjimku

Chcete-li vyladit doporučení zabezpečení, která Security Center provádí pro vaše předplatná, skupinu pro správu nebo prostředky, můžete vytvořit pravidlo výjimky pro:

- Označte konkrétní **doporučení** nebo jako "zmírňované" nebo "přijatelné riziko". Můžete vytvořit výjimky doporučení pro předplatné, více předplatných nebo celou skupinu pro správu.
- Označení **jednoho nebo více prostředků** jako "zmírňované" nebo "přijatelné riziko" pro konkrétní doporučení.

> [!NOTE]
> Výjimky je možné vytvořit jenom pro doporučení zahrnutá ve výchozí iniciativě Security Center, srovnávacím testu zabezpečení Azure nebo kterékoli ze zadaných regulativních standardních iniciativ. Doporučení, která jsou vygenerována ze všech vlastních iniciativ přiřazených k vašim předplatným, nelze vyloučit. Přečtěte si další informace o vztazích mezi [zásadami, iniciativami a doporučeními](security-policy-concept.md).

> [!TIP]
> Můžete také vytvořit výjimky pomocí rozhraní API. Příklad JSON a vysvětlení relevantních struktur naleznete v tématu [struktura Azure Policych výjimek](../governance/policy/concepts/exemption-structure.md).

Vytvoření pravidla výjimky:

1. Otevřete stránku podrobností doporučení pro konkrétní doporučení.

1. Na panelu nástrojů v horní části stránky vyberte **vyloučit**.

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="Vytvořte pravidlo výjimky pro doporučení, které se má vyloučit z předplatného nebo skupiny pro správu.":::

1. V podokně s **výjimkou** :
    1. Vyberte obor pro toto pravidlo výjimky:
        - Pokud vyberete skupinu pro správu, doporučení se vyloučí ze všech předplatných v této skupině.
        - Pokud vytváříte toto pravidlo pro vyloučení jednoho nebo více prostředků z doporučení, zvolte možnost vybrané prostředky a vyberte příslušné položky ze seznamu.

    1. Zadejte název tohoto pravidla výjimky.
    1. Volitelně můžete nastavit datum vypršení platnosti.
    1. Vyberte kategorii pro výjimku:
        - **Vyřešeno prostřednictvím třetí strany (s omezením)** – Pokud používáte službu třetí strany, která Security Center nebyla identifikována. 

            > [!NOTE]
            > Pokud vyloučíte doporučení jako zmírňovaná, nebudete mít body směrem ke svému zabezpečenému skóre. Ale vzhledem k tomu, že body nejsou *odebrané* pro prostředky, které nejsou v pořádku, je výsledkem, že se vaše skóre zvýší.

        - **Přijatá rizika (zřeknutí se)** – Pokud se rozhodnete přijmout riziko, že toto doporučení nebude zmírnit
    1. Volitelně zadejte popis.
    1. Vyberte **Vytvořit**.

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="Postup vytvoření pravidla výjimky pro vyloučení doporučení z předplatného nebo skupiny pro správu":::

    Když se výjimka uplatní (může to trvat až 30 minut):
    - Doporučení nebo prostředky nebudou mít vliv na vaše zabezpečené skóre.
    - Pokud jste vyloučili konkrétní prostředky, budou uvedeny na kartě **nepoužitelné** na stránce s podrobnostmi o doporučení.
    - Pokud jste si vyzkoušeli doporučení, bude ve výchozím nastavení na stránce doporučení Security Center skrytá. Důvodem je, že výchozí možnosti filtru **stavu doporučení** na této stránce mají vyloučit **nepoužitá** doporučení. Totéž platí, pokud vyloučíte všechna doporučení v ovládacím prvku zabezpečení.

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="Výchozí filtry na stránce s doporučeními Azure Security Center skryjí nepoužitá doporučení a kontroly zabezpečení.":::

    - Informační pruh v horní části stránky s podrobnostmi o doporučení aktualizuje počet vyloučených prostředků:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Počet vyloučených prostředků":::

1. Pokud chcete zkontrolovat vyloučené prostředky, otevřete kartu **Nepoužito** :

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Úprava výjimky":::

    Důvody pro každou výjimku jsou obsaženy v tabulce (1).

    Chcete-li změnit nebo odstranit výjimku, vyberte nabídku se třemi tečkami ("..."), jak je zobrazeno (2).

1. Pokud chcete zkontrolovat všechna pravidla výjimek v rámci vašeho předplatného, vyberte **Zobrazit výjimky** z informačního pruhu:

    > [!IMPORTANT]
    > Pokud chcete zobrazit konkrétní výjimky, které jsou relevantní pro jedno doporučení, vyfiltrujte seznam podle příslušného oboru a názvu doporučení.

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Stránka s výjimkou Azure Policy":::

    > [!TIP]
    > Alternativně můžete [pomocí grafu prostředků Azure vyhledat doporučení s výjimkami](#find-recommendations-with-exemptions-using-azure-resource-graph).

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>Monitorování výjimek vytvořených ve vašich předplatných

Jak je vysvětleno dříve na této stránce, pravidla výjimek představují výkonný nástroj, který poskytuje detailní kontrolu nad doporučeními, která mají vliv na prostředky ve vašich předplatných a skupinách pro správu. 

Abychom si udrželi přehled o tom, jak uživatelé tuto schopnost využívají, vytvořili jsme šablonu Azure Resource Manager (ARM), která nasadí PlayBook aplikaci logiky a všechna potřebná připojení rozhraní API, která vás upozorní, když se vytvoří výjimka.

- Další informace o PlayBook najdete v příspěvku na blogu pro technickou komunitu, [jak sledovat výjimky prostředků v Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580)
- Šablonu ARM najdete v [úložišti Azure Security Center GitHubu](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption) .
- K nasazení všech potřebných součástí [použijte tento automatizovaný proces](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json) .


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>Hledání doporučení s výjimkami pomocí Azure Resource graphu

Azure Resource Graph (ARG) poskytuje okamžitý přístup k informacím o prostředcích napříč vašimi cloudovým prostředím pomocí robustních funkcí filtrování, seskupování a řazení. Jedná se o rychlý a efektivní způsob dotazování informací v rámci předplatných Azure prostřednictvím Azure Portal.

Zobrazení všech doporučení, která mají pravidla výjimky:

1. Otevřete **Průzkumníka Azure Resource graphu**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Spouští se stránka pro doporučení pro Azure Resource Graph Explorer * *" :::

1. Zadejte následující dotaz a vyberte **Spustit dotaz**.

    ```kusto
    securityresources
    | where type == "microsoft.security/assessments"
    // Get recommendations in useful format
    | project
    ['TenantID'] = tenantId,
    ['SubscriptionID'] = subscriptionId,
    ['AssessmentID'] = name,
    ['DisplayName'] = properties.displayName,
    ['ResourceType'] = tolower(split(properties.resourceDetails.Id,"/").[7]),
    ['ResourceName'] = tolower(split(properties.resourceDetails.Id,"/").[8]),
    ['ResourceGroup'] = resourceGroup,
    ['ContainsNestedRecom'] = tostring(properties.additionalData.subAssessmentsLink),
    ['StatusCode'] = properties.status.code,
    ['StatusDescription'] = properties.status.description,
    ['PolicyDefID'] = properties.metadata.policyDefinitionId,
    ['Description'] = properties.metadata.description,
    ['RecomType'] = properties.metadata.assessmentType,
    ['Remediation'] = properties.metadata.remediationDescription,
    ['Severity'] = properties.metadata.severity,
    ['Link'] = properties.links.azurePortal
    | where StatusDescription contains "Exempt"    
    ```


Další informace najdete na následujících stránkách:
- [Přečtěte si další informace o Azure Resource graphu](../governance/resource-graph/index.yml).
- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md)
- [KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/)





## <a name="faq---exemption-rules"></a>Nejčastější dotazy – pravidla výjimek

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>Co se stane, když je jedno doporučení v několika iniciativách k zásadám?

V některých případech se doporučení zabezpečení objevuje ve více než jedné iniciativě zásad. Pokud máte ke stejnému předplatnému přiřazené víc instancí stejného doporučení a pro doporučení vytvoříte výjimku, bude to mít vliv na všechny iniciativy, které máte oprávnění upravovat. 

Například doporučení * * * * je součástí výchozí iniciativy zásad, která je přiřazená všem předplatným Azure pomocí Azure Security Center. Také v XXXXX.

Pokud se pokusíte vytvořit výjimku pro toto doporučení, zobrazí se jedna z následujících dvou zpráv:

- Pokud máte potřebná oprávnění k úpravám obou iniciativ, uvidíte:

    *Toto doporučení je součástí několika iniciativ zásad: [názvy iniciativ oddělené čárkou]. Na všech z nich se vytvoří výjimky.*  

- Pokud v obou iniciativách nemáte dostatečná oprávnění, zobrazí se místo toho tato zpráva:

    *Máte omezená oprávnění, abyste použili výjimku ze všech iniciativ zásad. výjimky budou vytvořeny pouze v iniciativách s dostatečnými oprávněními.*


## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak vyloučit prostředek z doporučení, aby neovlivnilo vaše zabezpečené skóre. Další informace o zabezpečeném skóre najdete v těchto tématech:

- [Skóre zabezpečení ve službě Azure Security Center](secure-score-security-controls.md)