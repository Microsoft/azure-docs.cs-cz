---
title: Jak používat klíč pro vytváření a modul runtime – LUIS
titleSuffix: Azure Cognitive Services
description: Při prvním použití Language Understanding (LUIS) není nutné vytvářet klíč pro vytváření obsahu. Pokud máte v úmyslu publikovat aplikaci, pak použijte koncový bod za běhu, musíte pro aplikaci vytvořit a přiřadit klíč modulu runtime.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: 36d03e20c9a56d7b317b867f01c1c0b5767c802c
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257002"
---
# <a name="using-authoring-and-runtime-resource-keys"></a>Použití klíčů prostředků pro vytváření obsahu a modulu runtime

Prostředky pro vytváření obsahu a modul runtime poskytují ověřování do vaší aplikace LUIS a koncového bodu předpovědi.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

Když se přihlásíte na portál LUIS, můžete se rozhodnout, že budete pokračovat v těchto akcích:

* bezplatný [zkušební klíč](#trial-key) – poskytuje vytváření a několik dotazů koncového bodu předpovědi.
* nový prostředek pro vytváření obsahu Azure LUIS – vytvořte nový prostředek. Nejedná se o stejný prostředek koncového bodu předpovědi. 


<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Přihlaste se k portálu LUIS a začněte vytvářet vytváření.

1. Přihlaste se k [portálu Luis](https://www.luis.ai) a odsouhlaste podmínky použití.
1. Spusťte aplikaci LUIS tak, že zvolíte, který typ klíče pro vytváření LUIS chcete použít: bezplatný zkušební klíč nebo nový klíč pro vytváření obsahu Azure LUIS. 

    ![Zvolit typ prostředku pro vytváření Language Understanding](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Po dokončení procesu výběru prostředků [vytvořte novou aplikaci](luis-how-to-start-new-app.md#create-new-app-in-luis). 

## <a name="trial-key"></a>Zkušební klíč

Zkušební verze (spouštěcí klíč) je k dispozici za vás. Slouží jako ověřovací klíč k dotazování předpovědi modulu runtime koncového bodu, až 1000 dotazů za měsíc. 

Je vidět na stránce **nastavení uživatele** i na stránkách **Spravovat > prostředky Azure** na portálu Luis. 

Až budete připraveni k publikování koncového bodu předpovědi, vytvořte a přiřaďte klíče modulu runtime pro vytváření a předpovědi, aby bylo možné nahradit funkci počátečního klíče. 

## <a name="create-resources-in-the-azure-portal"></a>Vytvoření prostředků v Azure Portal

1. Přihlaste se k webu [Azure Portal](https://azure.microsoft.com/free/). 
1. Vyberte **+ Vytvořit prostředek**.
1. Do vyhledávacího pole zadejte `Language understanding`.
1. Vyberte **Vytvořit** a spusťte proces vytváření. 
1. Vytvořte **obojí** , chcete-li vytvořit vytváření obsahu a předpředpověď klíč runtime koncového bodu. 
1. Zadejte informace požadované k vytvoření prostředku a potom vyberte **vytvořit** a proces dokončete.

    ![Vytvoření prostředku pro porozumění jazyku](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

    |Name|Účel|
    |--|--|
    |Název prostředku| Vlastní název, který zvolíte, se používá jako součást adresy URL pro dotazy na koncový bod vytváření a předpovědi.|
    |Název předplatného| předplatné, které se bude fakturovat za prostředek.|
    |Resource group| Vlastní název skupiny prostředků, který zvolíte nebo vytvoříte. Skupiny prostředků umožňují seskupit prostředky Azure pro přístup a správu ve stejné oblasti.|
    |Umístění pro vytváření obsahu|Oblast přidružená k vašemu modelu|
    |Vytváření cenové úrovně|Cenová úroveň určuje maximální transakci za sekundu a měsíc.|
    |Umístění modulu runtime|Oblast přidružená k publikovanému běhovému koncovému bodu předpovědi|
    |Cenová úroveň za běhu|Cenová úroveň určuje maximální transakci za sekundu a měsíc.|

    Po vytvoření obou prostředků přiřaďte prostředky na portálu LUIS.

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Přiřazení prostředku pro vytváření na portálu LUIS pro všechny aplikace

Můžete přiřadit prostředek pro vytváření obsahu pro jednu aplikaci nebo pro všechny aplikace v LUIS. Následující postup přiřadí všechny aplikace k jednomu prostředku pro vytváření.

1. Přihlaste se k [portálu Luis](https://www.luis.ai).
1. V horním navigačním panelu úplně vpravo vyberte svůj uživatelský účet a pak vyberte **Nastavení**.
1. Na stránce **nastavení uživatele** vyberte **Přidat prostředek pro vytváření** a pak vyberte existující prostředek pro vytváření. Vyberte **Uložit**. 

## <a name="assign-a-resource-to-an-app"></a>Přiřazení prostředku k aplikaci

Pomocí následujícího postupu můžete k aplikaci přiřadit jediný prostředek, vytváření obsahu nebo předpovědi modulu runtime koncového bodu.

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a pak vyberte aplikaci ze seznamu **Moje aplikace** .
1. Přejděte na stránku **Správa prostředků Azure spravovat >** .

    ![Vyberte prostředky Azure spravovat > na portálu LUIS a přiřaďte k aplikaci prostředek.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Vyberte kartu předpověď nebo prostředek pro vytváření obsahu a pak vyberte tlačítko **Přidat prostředek předpovědi** nebo **Přidat prostředek pro vytváření** . 
1. Vyberte pole ve formuláři, abyste našli správný prostředek, a pak vyberte **Uložit**.  

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Přiřazení prostředku modulu runtime bez použití portálu LUIS

Pro účely automatizace, jako je například kanál CI/CD, možná budete chtít automatizovat přiřazení prostředku modulu runtime LUIS do aplikace LUIS. Abyste to mohli udělat, musíte provést následující kroky:

1. Získání tokenu Azure Resource Manageru z tohoto [webu](https://resources.azure.com/api/token?plaintext=true). Vypršení platnosti tohoto tokenu tak okamžitě použít. Požadavek vrátí token Azure Resource Manageru.

    ![Vyžádat token Azure Resource Manager a získat token Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Použijte token k vyžádání prostředků modulu runtime LUIS napříč předplatnými z [rozhraní API pro účty Azure Luis](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), ke kterým má váš uživatelský účet přístup. 

    Toto rozhraní API příspěvku vyžaduje následující nastavení:

    |Hlavička|Hodnota|
    |--|--|
    |`Authorization`|Hodnota `Authorization` je `Bearer {token}`. Všimněte si, že hodnota tokenu musí předcházet slovo `Bearer` a mezerou.| 
    |`Ocp-Apim-Subscription-Key`|Váš klíč pro vytváření obsahu.|

    Toto rozhraní API vrátí pole objektů JSON z vašich předplatných LUIS, včetně ID předplatného, skupiny prostředků a název prostředku, vrátí jako název účtu. Najdete jednu položku v poli, který je zdrojem LUIS přiřazení k aplikaci LUIS. 

1. Přiřadit prostředek LUIS s token [přiřazení LUIS účty azure k aplikaci](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) rozhraní API. 

    Toto rozhraní API příspěvku vyžaduje následující nastavení:

    |Typ|Nastavení|Hodnota|
    |--|--|--|
    |Hlavička|`Authorization`|Hodnota `Authorization` je `Bearer {token}`. Všimněte si, že hodnota tokenu musí předcházet slovo `Bearer` a mezerou.|
    |Záhlaví|`Ocp-Apim-Subscription-Key`|Váš klíč pro vytváření obsahu.|
    |Záhlaví|`Content-type`|`application/json`|
    |Řetězec dotazu|`appid`|ID aplikace LUIS. 
    |Tělo||{"AzureSubscriptionId": "ddda2925-af7f-4b05-9ba1-2155c5fe8a8e"<br>"ResourceGroup": "resourcegroup-2"<br>"AccountName": "luis-uswest-S0-2"}|

    Po úspěšné toto rozhraní API vrátí stav 201 – vytvořeno. 

## <a name="unassign-resource"></a>Zrušit přiřazení prostředků

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a pak vyberte aplikaci ze seznamu **Moje aplikace** .
1. Přejděte na stránku **Správa prostředků Azure spravovat >** .
1. Vyberte kartu předpověď nebo prostředek vytváření a pak vyberte tlačítko zrušit **přiřazení prostředků** pro daný prostředek. 

Když zrušíte přiřazení prostředku, neodstraní se z Azure. Je jenom byl odpojen od služby LUIS. 

## <a name="reset-authoring-key"></a>Obnovit klíč pro tvorbu

**Pro [vytváření prostředků migrovaných](luis-migration-authoring.md) aplikací**: Pokud je váš vlastní klíč napadený, resetujte klíč v Azure Portal na stránce **klíče** pro daný prostředek pro vytváření obsahu. 

**Pro aplikace, které ještě nebyly migrovány**: klíč se resetuje na všech vašich aplikacích na portálu Luis. Pokud vaše aplikace vytváříte prostřednictvím rozhraní API pro vytváření obsahu, musíte změnit hodnotu OCP-APIM-Subscription-Key na nový klíč.

## <a name="regenerate-azure-key"></a>Znovu vygenerovat klíč Azure

Znovu vygenerujte klíče Azure z Azure Portal na stránce **klíče** .

## <a name="delete-account"></a>Odstranit účet

Zobrazit [úložiště dat a odebrání](luis-concept-data-storage.md#accounts) informace o tom, jaká data se odstraní při odstranění účtu.

## <a name="change-pricing-tier"></a>Změnit cenovou úroveň

1.  V [Azure](https://portal.azure.com), vyhledejte své předplatné služby LUIS. Vyberte předplatné LUIS.
    ![Vyhledejte své předplatné služby LUIS](./media/luis-usage-tiers/find.png)
1.  Vyberte **cenovou úroveň** , aby se zobrazily dostupné cenové úrovně. 
    ![Zobrazení cenové úrovně](./media/luis-usage-tiers/subscription.png)
1.  Vyberte cenovou úroveň a výběrem **Vybrat** uložte změnu. 
    ![Změnit úroveň služby LUIS platby](./media/luis-usage-tiers/plans.png)
1.  Po dokončení při změně cen se automaticky otevírané okno ověří novou cenovou úroveň. 
    ![Ověřte svou úroveň platby LUIS](./media/luis-usage-tiers/updated.png)
1. Nezapomeňte [přiřadit tento klíč koncového bodu](#assign-a-resource-to-an-app) na **publikovat** stránce a používat ho v všechny dotazy na koncový bod. 

## <a name="viewing-azure-resource-metrics"></a>Zobrazení metrik prostředků Azure

### <a name="viewing-azure-resource-summary-usage"></a>Zobrazení souhrnu využití prostředků Azure
Informace o použití LUIS můžete zobrazit v Azure. **Přehled** stránka zobrazuje poslední souhrnné informace, včetně volání a chyby. Pokud provedete požadavku koncového bodu LUIS, pak okamžitě podívejte se **stránka s přehledem**, povolit pro použití se zobrazí až pět minut.

![Zobrazení souhrnu využití](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Přizpůsobení grafů využití prostředků Azure
Metriky poskytuje podrobnější pohled na data.

![Výchozí metriky](./media/luis-usage-tiers/metrics-default.png)

Grafy metrik můžete nakonfigurovat pro časové období a typu metrika. 

![Vlastní metriky](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Celkový počet transakcí prahová hodnota výstrahy
Pokud chcete vědět, kdy jste dosáhli určitou transakci prahovou hodnotu, například 10 000 transakcí, můžete vytvořit výstrahu. 

![Výchozí upozornění](./media/luis-usage-tiers/alert-default.png)

Přidat upozornění metriky pro **celkový počet volání** metriky pro určité časové období. Přidáte e-mailové adresy lidí, kteří měli obdržet upozornění. Přidání webhooků pro všechny systémy, které by měla přijímat oznámení. Aplikace logiky můžete spustit také při aktivaci tohoto upozornění. 

## <a name="next-steps"></a>Další kroky

* Naučte se [používat verze](luis-how-to-manage-versions.md) k řízení životního cyklu aplikace.
* Seznamte se s koncepty, včetně [prostředků pro vytváření](/luis-concept-keys.md#authoring-key) a [přispěvatelů](luis-concept-keys.md#contributions-from-other-authors) na tomto prostředku.
* Naučte [se vytvářet prostředky pro](luis-how-to-azure-subscription.md) vytváření obsahu a modul runtime
* Migrace na nový [prostředek pro vytváření](luis-migration-authoring.md) 