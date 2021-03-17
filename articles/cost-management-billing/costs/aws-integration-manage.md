---
title: Správa nákladů a využití AWS ve službě Azure Cost Management
description: V tomto článku se dozvíte, jak můžete analýzu nákladů a rozpočty ve službě Cost Management používat ke správě nákladů a využití AWS.
author: bandersmsft
ms.author: banders
ms.date: 10/16/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: matrive
ms.custom: ''
ms.openlocfilehash: 5fed70ccdbebbd178412c416f37c2e9001a81f38
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188101"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Správa nákladů a využití AWS v Azure

Potom, co si nastavíte a nakonfigurujete integraci sestavy nákladů a využití AWS pro Azure Cost Management, můžete začít se správou nákladů a využití AWS. V tomto článku se dozvíte, jak můžete analýzu nákladů a rozpočty ve službě Cost Management používat ke správě nákladů a využití AWS.

Pokud jste tuto integraci ještě nenakonfigurovali, přečtěte si článek [Nastavení a konfigurace integrace sestavy využití AWS](aws-integration-set-up-configure.md).

_Než začnete:_ Pokud nejste obeznámeni s analýzou nákladů, přečtěte si rychlý start [Prozkoumání a analýza nákladů pomocí analýzy nákladů](quick-acm-cost-analysis.md). Pokud nejste obeznámeni s rozpočty v Azure, přečtěte si kurz [Vytváření a správa rozpočtů Azure](tutorial-acm-create-budgets.md).

## <a name="view-aws-costs-in-cost-analysis"></a>Zobrazení nákladů AWS v analýze nákladů

Náklady AWS jsou v analýze nákladů dostupné v následujících oborech:

- Propojené účty AWS ve skupině pro správu
- Náklady propojených účtů AWS
- Náklady konsolidovaných účtů AWS

V dalších částech je popsáno, jak tyto obory používat k zobrazení jejich nákladů a využití.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Zobrazení propojených účtů AWS ve skupině pro správu

Zobrazení nákladů pomocí oboru skupiny pro správu je jediným způsobem, jak zobrazit agregované náklady pocházející z různých předplatných Azure a propojených účtů AWS. Použití skupiny pro správu zajišťuje zobrazení nákladů z Azure a AWS dohromady napříč cloudy.

V analýze nákladů otevřete výběr oboru a vyberte skupinu pro správu, která obsahuje propojené účty AWS. Zde je obrázek s příkladem webu Azure Portal:

:::image type="content" source="./media/aws-integration-manage/select-scope01.png" alt-text="Příklad zobrazení Vybrat obor s propojenými účty v rámci skupiny pro správu" :::

Zde je příklad znázorňující náklady skupiny pro správu v analýze nákladů seskupené podle poskytovatele (Azure a AWS).

:::image type="content" source="./media/aws-integration-manage/cost-analysis-aws-azure.png" alt-text="Příklad znázorňující náklady na Azure a AWS za čtvrtletí v analýze nákladů" lightbox="./media/aws-integration-manage/cost-analysis-aws-azure.png" :::

> [!NOTE]
> Skupiny pro správu se v současnosti nepodporují pro zákazníky se Smlouvou se zákazníkem Microsoftu (MCA). Zákazníci se smlouvou MCA si mohou vytvořit tento konektor a zobrazit data AWS. Zákazníci se smlouvou MCA si ale nemohou zobrazovat náklady na Azure a náklady na AWS společně v rámci jedné skupiny pro správu.

### <a name="view-aws-linked-account-costs"></a>Zobrazení nákladů propojených účtů AWS

Pokud chcete zobrazit náklady propojeného účtu AWS, otevřete výběr oboru a vyberte propojený účet AWS. Propojené účty jsou přidružené ke skupině pro správu, jak je definováno v konektoru AWS.

Zde je příklad, který ukazuje výběr oboru propojeného účtu AWS.

:::image type="content" source="./media/aws-integration-manage/select-scope02.png" alt-text="Příklad zobrazení Vybrat obor, které zobrazuje propojené účty AWS" :::

### <a name="view-aws-consolidated-account-costs"></a>Zobrazení nákladů konsolidovaných účtů AWS

Pokud chcete zobrazit náklady konsolidovaných účtů AWS, otevřete výběr oboru a vyberte konsolidovaný účet AWS. Zde je příklad, který ukazuje výběr oboru konsolidovaného účtu AWS.

:::image type="content" source="./media/aws-integration-manage/select-scope03.png" alt-text="Příklad zobrazení Vybrat obor s konsolidovanými účty" :::

Tento obor poskytuje agregované zobrazení všech propojených účtů AWS přidružených ke konsolidovanému účtu AWS. Zde je příklad znázorňující náklady konsolidovaného účtu AWS seskupené podle názvu služby.

:::image type="content" source="./media/aws-integration-manage/cost-analysis-aws-consolidated.png" alt-text="Příklad znázorňující náklady konsolidovaného účtu AWS v analýze nákladů" lightbox="./media/aws-integration-manage/cost-analysis-aws-consolidated.png" :::

### <a name="dimensions-available-for-filtering-and-grouping"></a>Dimenze dostupné pro filtrování a seskupování

Následující tabulka popisuje dimenze, podle kterých lze v analýze nákladů seskupovat a filtrovat.

| Dimenze | Záhlaví sestavy nákladů a využití Amazon | Obory | Komentáře |
| --- | --- | --- | --- |
| Zóna dostupnosti | lineitem/AvailabilityZone | Vše |   |
| Umístění | product/Region | Vše |   |
| Měřič |   | Vše |   |
| Kategorie měřiče | lineItem/ProductCode | Vše |   |
| Podkategorie měřiče | lineitem/UsageType | Vše |   |
| Operace | lineItem/Operation | Vše |   |
| Prostředek | lineItem/ResourceId | Vše |   |
| Typ prostředku | product/instanceType | Vše | Pokud má product/instanceType hodnotu null, použije se lineItem/UsageType. |
| ResourceGuid | – | Vše | Identifikátor GUID měřiče Azure |
| Název služby | product/ProductName | Vše | Pokud má product/ProductName hodnotu null, použije se lineItem/ProductCode. |
| Úroveň služeb |   |   |   |
| ID předplatného | lineItem/UsageAccountId | Konsolidovaný účet a skupina pro správu |   |
| Název předplatného | – | Konsolidovaný účet a skupina pro správu | Názvy účtů se shromažďují pomocí rozhraní AWS Organization API. |
| Značka | resourceTags | Vše | Kvůli povolení značek různých cloudů je předpona _user:_ odebrána z uživatelem definovaných značek. Předpona _aws:_ zůstane nedotčena. |
| ID fakturačního účtu | bill/PayerAccountId | Skupina pro správu |   |
| Název fakturačního účtu | – | Skupina pro správu | Názvy účtů se shromažďují pomocí rozhraní AWS Organization API. |
| Poskytovatel | – | Skupina pro správu | Buď AWS, nebo Azure |

## <a name="set-budgets-on-aws-scopes"></a>Nastavení rozpočtů v oborech AWS

Rozpočty slouží k proaktivní správě nákladů a posílení zodpovědnosti ve vaší organizaci. Rozpočty se nastavují v oborech konsolidovaného účtu AWS a propojeného účtu AWS. Zde je příklad rozpočtů pro konsolidovaný účet AWS, který se zobrazuje ve službě Cost Management:

:::image type="content" source="./media/aws-integration-manage/budgets-aws-consolidated-account01.png" alt-text="Příklad znázorňující rozpočty pro konsolidovaný účet AWS" :::

## <a name="aws-data-collection-process"></a>Proces shromažďování dat AWS

Po nastavení konektoru AWS se spustí procesy shromažďování a zjišťování dat. Shromáždění všech dat o využití může trvat několik hodin. Doba trvání závisí na:

- Době potřebné ke zpracování souborů sestav nákladů a využití, které jsou v kontejneru AWS S3
- Době potřebné k vytvoření oborů konsolidovaného účtu AWS a propojeného účtu AWS
- Době a četnosti, s jakou AWS zapisuje soubory sestav nákladů a využití v kontejneru S3

## <a name="aws-integration-pricing"></a>Ceny integrace AWS

Každý konektor AWS nabízí 90denní bezplatnou zkušební verzi.

Ceníková cena je 1 % měsíčních nákladů na AWS. Každý měsíc bude účtován na základě fakturovaných nákladů z předchozího měsíce.

Při přístupu k rozhraním API služby AWS mohou v AWS nabíhat další náklady.

## <a name="aws-integration-limitations"></a>Omezení integrace AWS

- Rozpočty ve službě Cost Management nepodporují skupiny pro správu s více měnami. U skupin pro správu s více měnami se nezobrazí vyhodnocení rozpočtu. Pokud při vytváření rozpočtu vyberete skupinu pro správu s několika měnami, zobrazí se chybová zpráva.
- Cloudové konektory nepodporují AWS GovCloud (US), AWS Gov ani AWS China.
- Ve službě Cost Management se zobrazují jen _náklady na využití_ AWS. Daň, podpora, refundace, rezervované instance, kredity nebo jiné typy poplatků zatím nejsou podporovány.

## <a name="troubleshooting-aws-integration"></a>Řešení problémů s integrací AWS

Následující informace můžete použít k řešení běžných problémů.

### <a name="no-permission-to-aws-linked-accounts"></a>Chybějící oprávnění k propojeným účtům AWS

**Kód chyby:** _Unauthorized_

Oprávnění pro přístup k nákladům propojených účtů AWS můžete získat dvěma způsoby:

- Získejte přístup ke skupině pro správu, která obsahuje propojené účty AWS.
- Požádejte někoho, aby vám udělil oprávnění k propojenému účtu AWS.

Autor konektoru AWS je standardně vlastníkem všech objektů, které tento konektor vytvořil. Sem patří také konsolidovaný účet AWS a propojený účet AWS.

Aby bylo možné ověřit nastavení konektoru, budete potřebovat alespoň roli přispěvatel; čtenář nemůže nastavení konektoru ověřit.

### <a name="collection-failed-with-assumerole"></a>Shromažďování selhalo s chybou AssumeRole

**Kód chyby:** _FailedToAssumeRole_

Tato chyba znamená, že služba Cost Management nemůže volat rozhraní AWS AssumeRole API. K tomu může dojít kvůli nějakému problému s definicí role. Ověřte, jestli platí následující podmínky:

- Externí ID je stejné jako ID v definici role a definici konektoru.
- Typ role je nastavený na **Another AWS account Belonging to you or 3rd party** (Jiný účet AWS, který patří vám nebo třetí straně).
- Políčko **Require MFA** (Vyžadovat vícefaktorové ověřování) není zaškrtnuté.
- Důvěryhodný účet AWS v této roli AWS je _432263259397_.

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>Shromažďování selhalo s chybou odepření přístupu – definice sestavy nákladů a využití

**Kód chyby:** _AccessDeniedReportDefinitions_

Tato chyba znamená, že služba Cost Management nevidí definice sestavy nákladů a využití. Toto oprávnění slouží k ověření, že sestava nákladů a využití je definována tak, jako to služba Azure Cost Management očekává. Viz [Vytvoření sestavy nákladů a využití v AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="collection-failed-with-access-denied---list-reports"></a>Shromažďování selhalo s chybou odepření přístupu – výpis sestav

**Kód chyby:** _AccessDeniedListReports_

Tato chyba znamená, že služba Cost Management nemůže vypsat objekt v kontejneru S3, kde se nachází sestava nákladů a využití. Zásada AWS IAM vyžaduje oprávnění k tomuto kontejneru a objektům v tomto kontejneru. Viz [Vytvoření role a zásady v AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-with-access-denied---download-report"></a>Shromažďování selhalo s chybou odepření přístupu – stažení sestavy

**Kód chyby:** _AccessDeniedDownloadReport_

Tato chyba znamená, že služba Cost Management nemůže zpřístupnit a stáhnout soubory sestav nákladů a využití uložené v kontejneru Amazon S3. Ověřte, že se zásada AWS JSON připojená k dané roli podobá příkladu, který se zobrazuje dole v části [Vytvoření role a zásady v AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>Shromažďování selhalo, protože se nepodařilo najít sestavu nákladů a využití

**Kód chyby:** _FailedToFindReport_

Tato chyba znamená, že služba Cost Management nemůže najít sestavu nákladů a využití, která byla definována v konektoru. Ověřte, že není odstraněna a že se zásada AWS JSON připojená k dané roli podobá příkladu, který se zobrazuje dole v části [Vytvoření role a zásady v AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>Konektor nelze vytvořit nebo ověřit kvůli neshodě definic sestavy nákladů na využití

**Kód chyby:** _ReportIsNotValid_

Tato chyba souvisí s definicí sestavy nákladů a využití AWS. Pro tuto sestavu se vyžaduje specifické nastavení, viz požadavky v článku [Vytvoření sestavy nákladů a využití v AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="internal-error-when-creating-connector"></a>Interní chyba při vytváření konektoru

**Kód chyby:** _Vytvořit konektor – Nepovedlo se vytvořit konektor &lt;ConnectorName&gt;. Důvod: Vnitřní chyba. Ověřte, že byly poskytnuty správné vlastnosti AWS._

K této chybě může dojít, pokud konektor AWS a předplatné jsou v různých skupinách pro správu. Konektor AWS a předplatné musí být ve stejné skupině pro správu.

## <a name="next-steps"></a>Další kroky

- Pokud jste prostředí Azure ještě nenakonfigurovali pomocí skupin pro správu, přečtěte si téma [Počáteční nastavení skupin pro správu](../../governance/management-groups/overview.md#initial-setup-of-management-groups).
