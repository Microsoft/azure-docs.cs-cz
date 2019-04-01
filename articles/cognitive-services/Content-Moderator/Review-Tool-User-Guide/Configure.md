---
title: Konfigurace nastavení nástroj pro revizi – Content Moderator
titlesuffix: Azure Cognitive Services
description: Použijte nástroj pro recenze ke konfiguraci nebo načíst týmu, značky, konektory, pracovní postupy a přihlašovací údaje pro Content Moderator.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: f88ccbabc925b651abbc06f571a9d4220ed8aeb2
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756522"
---
# <a name="configure-the-review-tool"></a>Konfigurace nástroje pro recenze

[Nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com) má několik důležitých funkcí, které můžete přistupovat prostřednictvím **nastavení** nabídky na řídicím panelu.

![Content Moderator revize příliš nabídka nastavení](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Spravovat tým a podtýmy

**Týmu** karta vám umožní spravovat svůj tým a podtýmy&mdash;skupiny uživatelů, kteří můžou být upozorněni na určité [recenze prováděné lidmi](../review-api.md#reviews) se spouští. Můžete mít jenom jeden tým, (které vytvoříte, když zaregistrovat pomocí nástroje pro recenze), ale můžete vytvořit více podtýmy. Správce týmu můžete zvát členy, nastavit jejich oprávnění a přiřadit je k jiné podtýmy.

![Zkontrolujte nastavení týmu nástroje](images/settings-2-team.png)

Podtýmy jsou užitečné při vytváření eskalace týmy nebo týmy vyhrazené kontrolujeme konkrétní kategorie obsahu. Můžete například odeslat obsah pro dospělé samostatný tým pro další kontrolu.

Tato část vysvětluje, jak vytvořit podtýmy a rychle přiřadit kontroly v reálném čase. Můžete však použít [pracovních postupů](workflows.md) přiřadit revize, závislosti na konkrétních kritériích.

### <a name="create-a-subteam"></a>Vytvoření dílčího týmu

Přejděte **Podtýmy** části a klikněte na tlačítko **přidat dílčího týmu**. V dialogovém okně zadejte název dílčího týmu a klikněte na tlačítko **Uložit**.

![Název dílčího týmu](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Pozvání členů týmu

Nelze přiřazovat někdo dílčího týmu Pokud nejsou již členem týmu výchozí, budete muset nejdřív přidat recenzenty do výchozí tým. Klikněte na tlačítko **pozvat** na **týmu** kartu.

![Pozvat uživatele](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Přiřazení členů týmu k subteam

Klikněte na tlačítko **přidat člen** tlačítko přiřazení členů k jedné nebo více podtýmy z výchozí tým. K dílčího týmu můžete přidávat pouze existující uživatele. Přidat nové uživatele, kteří nejsou v nástroj pro recenze, vyzvat je pomocí tlačítka "Pozvánku" na stránce nastavení týmu.

![Přiřazení členů dílčího týmu](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Revize přiřadit podtýmy

Jakmile vytvoříte vaše podtýmy a přiřadí členům, můžete začít přiřazení obsahu [kontroly](../review-api.md#reviews) do těchto podtýmy. To provedete z **revize** lokality kartu.
Přiřazení obsahu do dílčího týmu, klikněte na tlačítko tří teček v pravém horním rohu vyberte **přesunout do**a vyberte dílčího týmu.

![Přiřadit subteam kontrole bitové kopie](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Přepínání mezi podtýmy

Pokud jste členem více než jednoho dílčího týmu, můžete přepínat mezi těmito podtýmy, chcete-li změnit, které obsahu kontroly se zobrazí za vás. V **revize** kartu, vyberte rozevírací nabídku s popiskem **výchozí** a vyberte **zvolte dílčího týmu**. Můžete zobrazit obsahu kontroly pro jiné podtýmy, ale jenom na ty z nich vaše jsou členem.

![Přepínání mezi podtýmy](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Značky

**Značky** kartu umožňuje definovat vlastní moderování značky kromě značek moderování dvě výchozí&mdash;**isadult** (****) a **isracy**  (**r**). Když vytvoříte vlastní značky, bude k dispozici v revize spolu s výchozí značky. Můžete změnit, které značky se zobrazí v revize přepnutím jejich nastavení viditelnosti.

![Zobrazit značky, včetně zaškrtávací políčka "Je viditelná"](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Vytvoření vlastní značky

Pokud chcete vytvořit novou značku, musíte zadat krátký kód, název a popis do příslušných polí.

- **Krátký kód**: Zadejte kód dvoupísmenné vaší značky. Příklad: **cb**
- **Název**: Zadejte značky krátký a popisný název malými písmeny, bez mezer. Příklad: **isbullying**.
- **Popis**: (volitelné) zadejte popis typu obsahu, který vaše cíle značky. Příklad: **Zobrazení nebo instance kybernetických bullying**.

Klikněte na tlačítko **přidat** přidat značku, a klikněte na **Uložit** po dokončení vytváření značek.

![Nová značka dialogové okno vytvořit nástroj pro recenze](images/settings-3-tags.png)

### <a name="delete-tags"></a>Odstranění značek

Vlastní značky můžete odstranit tak, že vyberete ikonu koše vedle jejich položky na seznamu značky, ale nelze odstranit výchozí značky.

## <a name="connectors"></a>Konektory

**Konektory** karta vám umožní spravovat své konektory, které jsou specifické pro služby moduly plug-in, která dokáže zpracovávat obsah různými způsoby jako součást obsahu [pracovních postupů](../review-api.md#workflows).

Výchozí konektor při vytvoření pracovního postupu je konektor Content Moderator, který můžete označit jako obsah **dospělé** nebo **pikantní**najít vulgárních výrazů a tak dále. Můžete však použít jiných konektorů, které jsou zde uvedeny, dokud máte přihlašovací údaje pro příslušné služby (pro použití konektoru API pro rozpoznávání tváře, například budete muset získat [API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/overview) klíč předplatného).

[Nástroj pro recenze](./human-in-the-loop.md) zahrnuje následující konektory:

- Rozhraní API pro rozpoznávání emocí
- Rozhraní API pro rozpoznávání tváře
- PhotoDNA Cloud Service
- Rozhraní Text Analytics API

### <a name="add-a-connector"></a>Přidat konektor

Chcete-li přidat konektoru (a zpřístupní ji pro použití v obsahu [pracovních postupů](../review-api.md#workflows)), vyberte odpovídající **připojit** tlačítko. V dalším dialogovém okně zadejte váš klíč předplatného za danou službu. Až budete hotovi, svůj nový konektor by se měla zobrazit v horní části stránky.

![Content Moderator konektory nastavení](images/settings-4-connectors.png)

## <a name="workflows"></a>Pracovní postupy

**Pracovních postupů** karta vám umožní spravovat vaše [pracovních postupů](../review-api.md#workflows). Pracovní postupy jsou založené na cloudu filtry pro obsah a pracovat s konektory k řazení obsahu různými způsoby a přijmout vhodná opatření. Tady můžete definovat, upravovat a testovat své pracovní postupy. Zobrazit [definování a použití pracovních postupů](Workflows.md) pokyny, jak to udělat.

![Content Moderator nastavení pracovního postupu](images/settings-5-workflows.png)

## <a name="credentials"></a>Přihlašovací údaje

**Pověření** kartu poskytuje rychlý přístup k vašemu klíči předplatného Content Moderator, které budete potřebovat pro přístup k jakémukoli služeb pro moderování z volání REST nebo Klientská sada SDK.

![Content Moderator pověření](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Použít externí přihlašovací údaje pro pracovní postupy

[Nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com) generuje bezplatné zkušební verze klíče pro služby Azure Content Moderator při registraci, ale můžete taky nakonfigurovat ho na použití existujícího klíče z vašeho účtu Azure. Tato možnost se doporučuje pro scénáře, ve velkém měřítku, s bezplatnou zkušební verzi klíče mít limity využití striktní ([ceny a omezení](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)).

Pokud jste vytvořili [Content Moderator prostředků](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) v Azure, přejděte k němu v Azure portal a vyberte **klíče** okno. Zkopírujte jeden z klíčů.

![Content Moderator klíče na webu Azure Portal](images/credentials-azure-portal-keys.PNG)

V [nástroj pro recenze](https://contentmoderator.cognitive.microsoft.com)společnosti **pověření** záložku, přejděte **nastavení pracovního postupu** vyberte **upravit**a vložte váš klíč do **Ocp-Apim-Subscription-Key** pole. Nyní pracovní postupy, které volají rozhraní API pro moderování použije přihlašovací údaje Azure.

> [!NOTE]
> Další dvě pole v **nastavení pracovního postupu** podokně jsou pro vlastní seznamy termínů a image. Zobrazit [vlastní podmínky](../try-terms-list-api.md) nebo [vlastních imagí](../try-image-list-api.md) průvodci se dozvíte o těchto.

### <a name="use-your-azure-account-with-the-review-apis"></a>Revize rozhraní API pomocí svého účtu Azure

K použití příslušného klíče Azure s revizi rozhraní API, je potřeba načíst ID prostředku. Přejděte do prostředku Content Moderator ve službě Azure portal a vyberte **vlastnosti** okno. Zkopírujte hodnotu ID prostředku a vložte ho do **ID prostředků na seznamu povolených** pole nástroje pro recenze **pověření** kartu.

![Content Moderator ID prostředku na webu Azure Portal](images/credentials-azure-portal-resourceid.PNG)

Pokud váš klíč předplatného jste zadali na obou místech, klíče zkušební verze, která se dodává s vaším účtem nástroj pro kontrolu se nepoužije, ale bude stále dostupný.

## <a name="next-steps"></a>Další postup

Postupujte podle [quickstart nástroj pro revize](../quick-start.md) chcete začít používat nástroj pro recenze ve scénářích pro moderování obsahu.