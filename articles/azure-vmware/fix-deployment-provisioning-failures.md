---
title: Získat pomoc s nasazením řešení Azure VMware nebo chybami zřizování
description: Jak získat potřebné informace z privátního cloudu řešení Azure VMware do souboru žádosti o službu pro nasazení řešení Azure VMware nebo chyby zřizování.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 63d5440a9e2b15463e465e1d32762889508feca1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88752233"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>Získat pomoc s nasazením řešení Azure VMware nebo chybami zřizování

V tomto článku se dozvíte, jak získat pomoc s nasazením řešení Azure VMware nebo selháním zřizování ve vašem privátním cloudu otevřením žádosti o služby (SR) v Azure Portal. Nejdřív ale potřebujete shromažďovat některé klíčové informace v Azure Portal. Ve většině případů potřebujete:

- ID korelace (u neúspěšného nasazení)
- ID okruhu ExpressRoute (při pokusu o škálování nebo navázání partnerského vztahu se stávajícím privátním cloudem pomocí okruhu privátního cloudu ExpressRoute

## <a name="collect-the-correlation-id"></a>Shromáždit ID korelace
 
Pojďme se nejdřív podívat na ID korelace. Při vytváření privátního cloudu (nebo jakéhokoli prostředku v Azure) se vygeneruje přidružené ID korelace. Každé nasazení Azure Resource Manager také generuje jedinečné ID korelace. Toto ID umožňuje rychlejší vytváření a řešení SR. 
 
Tady je příklad výstupu z neúspěšného nasazení privátního cloudu se zvýrazněným ID korelace.

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Nasazení privátního cloudu s ID korelace se nezdařilo.":::

Zkopírujte a uložte toto ID korelace, které chcete zahrnout do žádosti o služby. Podrobnosti najdete v tématu [Vytvoření žádosti o podporu](#create-your-support-request) na konci tohoto článku.

Pokud dojde k selhání v fázích před ověřením, před nasazením privátního cloudu se nevygeneruje žádné ID korelace. V takovém případě můžete jednoduše zadat informace, které jste použili při vytváření privátního cloudu řešení Azure VMware, včetně těchto:

- Umístění
- Skupina prostředků
- Název prostředku
 
### <a name="collect-a-summary-of-errors"></a>Shromažďování souhrnu chyb

Podrobnosti o jakýchkoli chybách můžou být užitečné také při řešení vašeho problému. Na předchozí obrazovce vyberte **kliknutím sem** zobrazíte podrobnosti (zvýrazněno) a zobrazí se souhrn chyb, jak je znázorněno na následujícím snímku obrazovky.
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Nasazení privátního cloudu s ID korelace se nezdařilo.":::

Znovu tento souhrn zkopírujte a uložte, abyste ho zahrnuli do SR.
 
### <a name="retrieve-past-deployments"></a>Načíst minulá nasazení

Minulá nasazení, včetně chyb, můžete načíst tak, že vyhledáte přidaný protokol aktivit nasazení, a to výběrem ikony oznámení.

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Nasazení privátního cloudu s ID korelace se nezdařilo.":::

V části oznámení vyberte **Další události v protokolu aktivit**.

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Nasazení privátního cloudu s ID korelace se nezdařilo.":::

Pak vyhledejte název prostředku nebo jiné jedinečné informace, které jste použili při vytváření prostředku, a vyhledejte neúspěšné nasazení a ID korelace. Následující příklad ukazuje výsledky hledání v prostředku privátního cloudu (pc03).
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Nasazení privátního cloudu s ID korelace se nezdařilo.":::
 
Když vyberete název operace neúspěšného nasazení, otevře se okno s podrobnostmi. Vyberte kartu JSON a vyhledejte ID korelace. Zkopírujte a zahrňte do SR. 
 
## <a name="collect-the-expressroute-id-uri"></a>Shromáždit ID ExpressRoute (URI)
 
Možná už máte privátní cloud a dojde k selhání při pokusu o horizontální navýšení kapacity nebo partnerského okruhu privátního cloudu ExpressRoute. V takovém případě se ExpressRoute ID privátního cloudu dá použít k jeho identifikaci při vytváření rozhraní SR.

Při prohlížení privátního cloudu na portálu vyberte možnost **připojení > ExpressRoute** a ZKOPÍRUJTE **ExpressRoute ID** do schránky.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Nasazení privátního cloudu s ID korelace se nezdařilo."::: 
 
Vložte ID ExpressRoute do příslušného pole v nové žádosti o podporu. Další informace najdete v následující části [Vytvoření žádosti o podporu](#create-your-support-request).
 
> [!NOTE]
> V některých případech mohou být před nasazením neúspěšné kontroly před ověřením a k dispozici jsou pouze informace o chybách a/nebo chybách. To může být užitečné při několika selháních, pro problémy související s kvótou a je důležité zahrnout tyto zprávy do žádosti o podporu. Chcete-li je shromáždit, přečtěte si předchozí část [shromáždění souhrnu chyb](#collect-a-summary-of-errors).

## <a name="create-your-support-request"></a>Vytvoření žádosti o podporu

Obecné pokyny k vytvoření žádosti o podporu najdete v tématu [Postup vytvoření žádosti o podporu Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Tady jsou další doprovodné materiály, které jsou specifické pro vytváření nasazení řešení SR pro Azure VMware nebo jejich zřizování.

1. Vyberte ikonu **pomoci** a potom **+ Nová žádost o podporu**.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Nasazení privátního cloudu s ID korelace se nezdařilo.":::

2. Vyplňte všechna povinná pole a na kartě **základy** :

    - Jako **typ problému**vyberte **Konfigurace a problémy s instalací**.

    - V případě **podtypu problému**vyberte možnost **zřídit privátní cloud**.

3. Na kartě **Podrobnosti** :

    - Vyplňte všechna povinná pole.

    - Do zadaných polí Vložte ID korelace nebo ID ExpressRoute. Pokud se pro ně nezobrazuje konkrétní pole, můžete je vložit do textového pole v části **Zadejte podrobnosti o problému.**

    - Vložte všechny podrobnosti o chybě, včetně souhrnu chyb, které jste zkopírovali, do textového pole v části **Zadejte podrobnosti o problému.**

4. Zkontrolujte a vyberte **vytvořit** a vytvořte tak své rozhraní SR.
