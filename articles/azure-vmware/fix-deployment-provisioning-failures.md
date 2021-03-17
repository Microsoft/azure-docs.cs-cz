---
title: Podpora nasazení řešení Azure VMware nebo selhání zřizování
description: Získejte informace z privátního cloudu řešení Azure VMware a zajistěte si soubor žádosti o službu pro nasazení řešení Azure VMware nebo chybu zřizování.
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 27b645f4ca225fdd74bca6499b6581b3803e41a4
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542401"
---
# <a name="open-a-support-request-for-an-azure-vmware-solution-deployment-or-provisioning-failure"></a>Otevření žádosti o podporu pro nasazení řešení Azure VMware nebo selhání zřizování

V tomto článku se dozvíte, jak otevřít [žádost o podporu](https://rc.portal.azure.com/#create/Microsoft.Support) a zadat klíčové informace pro nasazení řešení Azure VMware nebo chybu zřizování. 

Pokud máte ve svém privátním cloudu selhání, musíte v Azure Portal otevřít žádost o podporu. Chcete-li otevřít žádost o podporu, nejprve získejte některé klíčové informace v Azure Portal:

- ID korelace
- ID okruhu Azure ExpressRoute
- Chybové zprávy

## <a name="get-the-correlation-id"></a>Získat ID korelace
 
Při vytváření privátního cloudu nebo jakéhokoli prostředku v Azure se pro prostředek automaticky vygeneruje korelační ID pro daný prostředek. Do žádosti o podporu zahrňte ID korelace privátního cloudu, abyste mohli žádost rychleji otevřít a vyřešit.

V Azure Portal můžete získat ID korelace pro prostředek dvěma způsoby:

* Podokno **přehledu**
* Protokoly nasazení
 
 ### <a name="get-the-correlation-id-from-the-resource-overview"></a>Získat ID korelace z přehledu prostředků

Tady je příklad podrobností o operacích s neúspěšným nasazením privátního cloudu s vybraným ID korelace:

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Snímek obrazovky, který zobrazuje neúspěšné nasazení privátního cloudu s vybraným ID korelace.":::

Přístup k výsledkům nasazení v podokně **Přehled** privátního cloudu:

1. V Azure Portal vyberte svůj privátní cloud.

1. V nabídce vlevo vyberte **Přehled**.

Po zahájení nasazení se výsledky nasazení zobrazí v podokně **Přehled** privátního cloudu.

Zkopírujte a uložte ID korelace nasazení privátního cloudu, které chcete zahrnout do žádosti o služby.

### <a name="get-the-correlation-id-from-the-deployment-log"></a>Získat ID korelace z protokolu nasazení

ID korelace pro nasazení, které selhalo, můžete získat tak, že v Azure Portal provedete hledání v protokolu aktivit nasazení.

Přístup k protokolu nasazení:

1. V Azure Portal vyberte svůj privátní cloud a pak vyberte ikonu oznámení.

   :::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Snímek obrazovky, který zobrazuje ikonu oznámení v Azure Portal.":::

1. V podokně **oznámení** vyberte **Další události v protokolu aktivit** :

    :::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Snímek obrazovky, který zobrazuje další události v odkazu protokolu aktivit vybrané v podokně oznámení.":::

1. Pokud chcete najít neúspěšné nasazení a jeho ID korelace, vyhledejte název prostředku nebo další informace, které jste použili k vytvoření prostředku. 

    Následující příklad ukazuje výsledky hledání pro prostředek privátního cloudu s názvem pc03.
 
    :::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Snímek obrazovky, který zobrazuje výsledky hledání pro ukázkový prostředek privátního cloudu a podokno vytvořit nebo aktualizovat PrivateCloud.":::
 
1. Ve výsledcích hledání v podokně **Protokol aktivit** vyberte název operace neúspěšného nasazení.

1. V podokně **vytvořit nebo aktualizovat PrivateCloud** vyberte kartu **JSON** a pak `correlationId` ji vyhledejte v zobrazeném protokolu. Zkopírujte `correlationId` hodnotu tak, aby byla zahrnuta do žádosti o podporu. 
 
## <a name="copy-error-messages"></a>Kopírovat chybové zprávy

Chcete-li vyřešit potíže s nasazením, zahrňte všechny chybové zprávy, které jsou zobrazeny v Azure Portal. Vyberte zprávu upozornění pro zobrazení souhrnu chyb:
 
:::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Snímek obrazovky zobrazující podrobnosti o chybě na kartě Souhrn v podokně chyby s vybranou ikonou kopírování":::

Chcete-li zkopírovat chybovou zprávu, vyberte ikonu Kopírovat. Uložte zkopírovanou zprávu, kterou chcete zahrnout do žádosti o podporu.
 
## <a name="get-the-expressroute-id-uri"></a>Získat ID ExpressRoute (URI)
 
Možná se snažíte škálovat nebo vytvořit partnerský vztah k existujícímu privátnímu cloudu pomocí okruhu ExpressRoute privátního cloudu a nefunguje. V takovém případě potřebujete ID ExpressRoute zahrnout do žádosti o podporu.

Zkopírování ID ExpressRoute:

1. V Azure Portal vyberte svůj privátní cloud.
1. V nabídce vlevo v části **Spravovat** vyberte možnost **připojení**. 
1. V pravém podokně vyberte kartu **ExpressRoute** .
1. Vyberte ikonu kopírování pro **EXPRESSROUTE ID** a uložte hodnotu, která se má použít v žádosti o podporu.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Zkopírujte ID ExpressRoute do schránky."::: 
 
## <a name="pre-validation-failures"></a>Selhání předběžného ověření

Pokud se kontrola před ověřením privátního cloudu nezdařila (před nasazením), ID korelace nebude vygenerováno. V tomto scénáři můžete v žádosti o podporu zadat následující informace:

- Chybové zprávy a chyby Tyto zprávy mohou být užitečné v mnoha selháních, například pro problémy související s kvótou. Je důležité tyto zprávy zkopírovat a zahrnout je do žádosti o podporu, jak je popsáno v tomto článku.
- Informace, které jste použili k vytvoření privátního cloudu řešení Azure VMware, včetně těchto:
  - Umístění
  - Skupina prostředků
  - Název prostředku

## <a name="create-your-support-request"></a>Vytvoření žádosti o podporu

Obecné informace o vytvoření žádosti o podporu najdete v tématu [Postup vytvoření žádosti o podporu Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Vytvoření žádosti o podporu pro nasazení řešení Azure VMware nebo selhání zřizování:

1. V Azure Portal vyberte ikonu **pomoci** a pak vyberte **Nová žádost o podporu**.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Snímek obrazovky s novým podoknem žádosti o podporu v Azure Portal.":::

1. Zadejte nebo vyberte požadované informace:

   1. Vyberte kartu **Základy** :

      1. Jako **typ problému** vyberte **Konfigurace a problémy s instalací**.

      1. V případě **podtypu problému** vyberte možnost **zřídit privátní cloud**.

   1. Na kartě **Podrobnosti** :

      1. Zadejte nebo vyberte požadované informace.

      1. Vložte ID korelace nebo ID ExpressRoute, kde se vyžadují tyto informace. Pokud pro tyto hodnoty nevidíte konkrétní textové pole, vložte je do textového pole **Zadejte podrobnosti o problému** .

    1. Vložte všechny podrobnosti o chybě, včetně chybových zpráv o chybách a chybách, které jste zkopírovali, do textového pole **Zadejte podrobnosti o problému** .

1. Zkontrolujte položky a potom vyberte **vytvořit** a vytvořte žádost o podporu.
