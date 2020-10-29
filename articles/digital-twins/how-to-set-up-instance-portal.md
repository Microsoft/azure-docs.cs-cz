---
title: Nastavení instance a ověřování (portál)
titleSuffix: Azure Digital Twins
description: Podívejte se, jak nastavit instanci služby Azure Digital pomocí Azure Portal
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperfq
ms.openlocfilehash: 2dc19432d2185b95376717c570cd3165ebc85075
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027278"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Nastavení instance a ověřování digitálních vláken Azure (portál)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Tento článek popisuje postup **Nastavení nové instance digitálního vlákna Azure** , včetně vytvoření instance a nastavení ověřování. Po dokončení tohoto článku budete mít instanci digitálních vláken Azure, která je připravená na zahájení programování.

Tato verze tohoto článku prochází těmito kroky ručně, jednou po jednom, pomocí Azure Portal. Azure Portal je unifikovaná webová konzola, která poskytuje alternativu k nástrojům příkazového řádku.
* Pokud chcete projít tyto kroky ručně pomocí rozhraní příkazového řádku, přečtěte si verzi rozhraní příkazového řádku tohoto článku: [*Postupy: nastavení instance a ověřování (CLI)*](how-to-set-up-instance-cli.md).
* Chcete-li provést automatickou instalaci pomocí skriptu nasazení, přečtěte si skriptovaná verze tohoto článku: [*Postupy: nastavení instance a ověřování (skriptované)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Vytvoření instance digitálních vláken Azure

V této části **vytvoříte novou instanci digitálních vláken Azure** pomocí [Azure Portal](https://ms.portal.azure.com/). Přejděte na portál a přihlaste se pomocí svých přihlašovacích údajů.

Na portálu Začněte výběrem možnosti _vytvořit prostředek_ v nabídce domovské stránky služeb Azure.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Na domovské stránce Azure Portal vyberte vytvořit prostředek.":::

Ve vyhledávacím poli vyhledejte *digitální vlákna Azure* a z výsledků vyberte službu **Azure Digital revlákens (Preview)** . Kliknutím na tlačítko _vytvořit_ vytvořte novou instanci služby.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="Na domovské stránce Azure Portal vyberte vytvořit prostředek.":::

Na následující stránce *vytvořit prostředek* vyplňte hodnoty uvedené níže:
* **Předplatné** : předplatné Azure, které používáte
  - **Skupina prostředků** : Skupina prostředků, do které se má instance nasadit. Pokud ještě nemáte existující skupinu prostředků, můžete si ji vytvořit tak, že vyberete odkaz *vytvořit nový* a zadáte název nové skupiny prostředků.
* **Umístění** : oblast s povolenými digitálními podtechnologiemi Azure pro nasazení. Další podrobnosti o místní podpoře najdete v [*produktech Azure dostupných v oblasti (digitální vlákna Azure)*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* **Název prostředku** : název vaší instance digitálního vlákna Azure. Název nové instance musí být v rámci vašeho předplatného jedinečný (to znamená, že pokud má vaše předplatné jinou instanci digitálních vláken Azure v oblasti, která už používá zvolený název, zobrazí se výzva k výběru jiného názvu).

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="Na domovské stránce Azure Portal vyberte vytvořit prostředek.":::

Po dokončení vyberte _zkontrolovat + vytvořit_ . Tím přejdete na stránku Souhrn, kde můžete zkontrolovat podrobnosti instance, které jste zadali, a _vytvořit_ . 

### <a name="verify-success-and-collect-important-values"></a>Ověřit úspěšné a shromažďovat důležité hodnoty

Po nahrání *Vytvoření* můžete zobrazit stav nasazení vaší instance v oznámeních Azure na panelu ikon portálu. Oznámení bude označovat, že nasazení proběhlo úspěšně, a budete moci vybrat tlačítko _Přejít k prostředku_ a zobrazit vytvořenou instanci.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="Na domovské stránce Azure Portal vyberte vytvořit prostředek.":::

Případně, pokud se nasazení nezdaří, oznámení indikuje, proč. Sledujte Rady z chybové zprávy a zkuste instanci vytvořit znovu.

>[!TIP]
>Po vytvoření instance se můžete kdykoli vrátit na svou stránku tak, že na panelu hledání Azure Portal vyhledáte název vaší instance.

Na stránce *Přehled* instance si poznamenejte její *název* , *skupinu prostředků* a *název hostitele* . Jedná se o všechny důležité hodnoty, které možná budete potřebovat, když budete pokračovat v práci s vaší instancí digitálních vláken Azure. Pokud budou jiné uživatele programovat s instancí, měli byste je s těmito hodnotami sdílet.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Na domovské stránce Azure Portal vyberte vytvořit prostředek.":::

Teď máte k dispozici instanci digitálních vláken Azure, která je připravená k přechodu. V dalším kroku poskytnete příslušnému uživateli Azure oprávnění ke správě.

## <a name="set-up-user-access-permissions"></a>Nastavení uživatelských oprávnění pro přístup

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Nejdřív otevřete stránku pro instanci digitálních vláken Azure v Azure Portal. V nabídce instance vyberte *řízení přístupu (IAM)* . V části *Přidat přiřazení role* vyberte tlačítko *Přidat* .

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="Na domovské stránce Azure Portal vyberte vytvořit prostředek.":::

Na následující stránce *Přidat přiřazení role* vyplňte hodnoty (musí být dokončené uživatelem s [dostatečnými oprávněními](#prerequisites-permission-requirements) v předplatném Azure):
* **Role** : vyberte v rozevírací nabídce možnost *vlastník dat digitálních vláken Azure* .
* **Přiřadit přístup k** : vyberte *uživatele, skupinu nebo INSTANČNÍ objekt Azure AD* z rozevírací nabídky.
* **Vyberte** : Vyhledejte jméno nebo e-mailovou adresu uživatele, který chcete přiřadit. Když vyberete výsledek, uživatel se zobrazí v části *Vybrané členy* .

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="Na domovské stránce Azure Portal vyberte vytvořit prostředek.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Až skončíte s zadáváním podrobností, stiskněte tlačítko *Uložit* .

### <a name="verify-success"></a>Ověřit úspěch

Můžete zobrazit přiřazení role, které jste nastavili v části *řízení přístupu (IAM) > přiřazení rolí* . Uživatel by se měl zobrazit v seznamu s rolí *vlastníka dat digitálních vláken Azure* . 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Na domovské stránce Azure Portal vyberte vytvořit prostředek.":::

Teď máte k dispozici instanci digitálních vláken Azure, která je připravená k použití, a máte přiřazená oprávnění ke správě IT.

## <a name="next-steps"></a>Další kroky

Otestujte jednotlivá REST API volání na vaši instanci pomocí příkazů rozhraní příkazového řádku Azure Digital revlákens CLI: 
* [AZ DT reference](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*Postupy: použití rozhraní příkazového řádku Azure Digital zdvojené*](how-to-use-cli.md)

Případně můžete informace o tom, jak připojit klientskou aplikaci k instanci s ověřovacím kódem:
* [*Postupy: psaní kódu ověřování aplikace*](how-to-authenticate-client.md)