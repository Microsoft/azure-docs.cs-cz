---
title: Získat pomoc s nasazením řešení Azure VMware nebo chybami zřizování
description: Jak získat potřebné informace z privátního cloudu řešení Azure VMware do souboru žádosti o službu pro nasazení řešení Azure VMware nebo chyby zřizování.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 1f46dde895db417fd2b488a6203d5482e73d3c5e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779488"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>Získat pomoc s nasazením řešení Azure VMware nebo chybami zřizování

Tento článek vám pomůže s chybami při nasazení a zřizování řešení Azure VMware. Pokud máte ve svém privátním cloudu chyby, budete muset v Azure Portal otevřít [žádost o podporu](https://rc.portal.azure.com/#create/Microsoft.Support) (SR). 

Nejdřív budete potřebovat shromažďovat některé klíčové informace v Azure Portal:

- ID korelace
- ID okruhu ExpressRoute

## <a name="collect-the-correlation-id"></a>Shromáždit ID korelace
 
ID korelace se vygeneruje při vytváření privátního cloudu nebo jakéhokoli prostředku v Azure. Každé nasazení Azure Resource Manager také generuje ID korelace. Toto ID umožňuje rychlejší vytváření a řešení SR. 
 
Tady je příklad výstupu z neúspěšného nasazení privátního cloudu se zvýrazněným ID korelace.

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Nasazení privátního cloudu s ID korelace se nezdařilo.":::

Zkopírujte a uložte toto ID korelace, které chcete zahrnout do žádosti o služby. Podrobnosti najdete v tématu [Vytvoření žádosti o podporu](#create-your-support-request) na konci tohoto článku.

Pokud dojde k selhání ve fázích předběžného ověření, nebude vygenerováno žádné ID korelace. V takovém případě můžete zadat informace, které jste použili při vytváření privátního cloudu řešení Azure VMware, včetně těchto:

- Umístění
- Skupina prostředků
- Název prostředku
 
### <a name="collect-a-summary-of-errors"></a>Shromažďování souhrnu chyb

Podrobnosti o jakýchkoli chybách můžou problém vyřešit také. Na předchozí obrazovce vyberte zprávu upozornění pro zobrazení souhrnu chyb.
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Nasazení privátního cloudu s ID korelace se nezdařilo.":::

Znovu tento souhrn zkopírujte a uložte, abyste ho zahrnuli do SR.
 
### <a name="retrieve-past-deployments"></a>Načíst minulá nasazení

Minulá nasazení, včetně chyb, můžete načíst tak, že vyhledáte přidaný protokol aktivit nasazení, a to výběrem ikony oznámení.

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Nasazení privátního cloudu s ID korelace se nezdařilo.":::

V části oznámení vyberte **Další události v protokolu aktivit** .

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Nasazení privátního cloudu s ID korelace se nezdařilo.":::

Pak vyhledejte název prostředku nebo další informace, které se používají k vytvoření prostředku pro vyhledání neúspěšného nasazení a jeho ID korelace. Následující příklad ukazuje výsledky hledání v prostředku privátního cloudu (pc03).
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Nasazení privátního cloudu s ID korelace se nezdařilo.":::
 
Když vyberete název operace neúspěšného nasazení, otevře se okno s podrobnostmi. Vyberte kartu JSON a vyhledejte ID korelace. Zkopírujte a zahrňte do SR. 
 
## <a name="collect-the-expressroute-id-uri"></a>Shromáždit ID ExpressRoute (URI)
 
Možná se snažíte škálovat nebo vytvořit partnerský vztah k existujícímu privátnímu cloudu pomocí okruhu ExpressRoute privátního cloudu a nefunguje. V takovém případě budete potřebovat ID ExpressRoute. 

V Azure Portal vyberte možnost **připojení > ExpressRoute** a zkopírujte **ID ExpressRoute** do schránky.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Nasazení privátního cloudu s ID korelace se nezdařilo."::: 
 
> [!NOTE]
> V některých případech můžou před nasazením selhat kontroly před ověřením a jenom dostupné informace budou chybové zprávy a chyby. To může být užitečné v mnoha selháních, například v případě problémů souvisejících s kvótou a je důležité zahrnout tyto zprávy do žádosti o podporu. Chcete-li je shromáždit, přečtěte si předchozí část [shromáždění souhrnu chyb](#collect-a-summary-of-errors).

## <a name="create-your-support-request"></a>Vytvoření žádosti o podporu

Obecné pokyny k vytvoření žádosti o podporu najdete v tématu [Postup vytvoření žádosti o podporu Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Tady jsou konkrétní pokyny k vytvoření nasazení řešení SR pro Azure VMware nebo selhání zřizování.

1. Vyberte ikonu **pomoci** a potom **+ Nová žádost o podporu** .

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Nasazení privátního cloudu s ID korelace se nezdařilo.":::

2. Vyplňte všechna povinná pole a na kartě **základy** :

    - Jako **typ problému** vyberte **Konfigurace a problémy s instalací** .

    - V případě **podtypu problému** vyberte možnost **zřídit privátní cloud** .

3. Na kartě **Podrobnosti** :

    - Vyplňte všechna povinná pole.

    - Do zadaných polí Vložte ID korelace nebo ID ExpressRoute. Pokud nevidíte konkrétní pole, můžete je vložit do textového pole v části **Zadejte podrobnosti o problému.**

    - Vložte všechny podrobnosti o chybě, včetně souhrnu chyb, které jste zkopírovali, do textového pole v části **Zadejte podrobnosti o problému.**

4. Zkontrolujte a vyberte **vytvořit** a vytvořte tak své rozhraní SR.
