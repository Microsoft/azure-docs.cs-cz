---
title: Konfigurovat nastavení nástroje pro kontrolu – Content Moderator
titleSuffix: Azure Cognitive Services
description: Pomocí nástroje pro revize můžete nakonfigurovat nebo načíst svůj tým, značky, konektory, pracovní postupy a přihlašovací údaje pro Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 8812fd1e6c1efb2aa44c77573bc4b8f1c099834d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "92912017"
---
# <a name="configure-the-review-tool"></a>Konfigurace nástroje pro recenze

[Nástroj pro kontrolu](https://contentmoderator.cognitive.microsoft.com) obsahuje několik důležitých funkcí, ke kterým můžete přistupovat prostřednictvím nabídky **Nastavení** na řídicím panelu.

![Nabídka pro moc nastavení Content Moderator prostudovat](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Spravovat tým a subtýmy

Karta **tým** umožňuje spravovat skupiny uživatelů a subtýmů &mdash; , kteří mohou být upozorněni při zahájení určitých [lidských](../review-api.md#reviews) kontrol. Můžete mít pouze jeden tým (který vytvoříte při registraci pomocí nástroje pro revizi), ale můžete vytvořit více subtýmů. Správce týmu může pozvat členy, nastavit jejich oprávnění a přiřadit je různým subtýmům.

![Kontrola nastavení týmu nástroje](images/settings-2-team.png)

Subtýmy jsou užitečné pro vytváření týmů eskalace nebo týmů vyhrazených pro kontrolu konkrétních kategorií obsahu. Můžete například odeslat obsah pro dospělé do samostatného týmu za účelem další kontroly.

V této části se dozvíte, jak vytvářet dílčí týmy a rychle přiřazovat recenze za běhu. [Pracovní postupy](workflows.md) ale můžete použít k přiřazení recenzí na základě určitých kritérií.

### <a name="create-a-subteam"></a>Vytvořit subtým

Přejděte do části **subtýmy** a klikněte na **Přidat dílčí tým**. Do dialogového okna zadejte název svého subtýmu a klikněte na **Uložit**.

![Název subtýmu](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Pozvat ostatními týmu

Nemůžete přiřadit někoho do subtýmu, pokud už nejsou členem výchozího týmu, takže musíte nejdřív přidat kontrolory k výchozímu týmu. Na kartě **tým** klikněte na **pozvat** .

![Pozvání uživatelů](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Přiřadit ostatními týmu k subtýmu

Kliknutím na tlačítko **Přidat člena** můžete přiřadit členy z výchozího týmu k jednomu nebo více podtýmům. Do subtýmu můžete přidat pouze existující uživatele. Chcete-li přidat nové uživatele, kteří nejsou v nástroji pro revizi, pozvěte je pomocí tlačítka "pozvat" na stránce Nastavení týmu.

![Přiřazení členů subtýmu](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Přiřadit recenze k podtýmům

Po vytvoření svých subtýmů a přiřazených členů můžete pro tyto subtými začít přiřazovat [Revize](../review-api.md#reviews) obsahu. To se provádí na kartě **Revize** webu.
Chcete-li přiřadit obsah dílčímu týmu, klikněte na tlačítko se třemi tečkami v pravém horním rohu, vyberte možnost **přesunout do** a vyberte dílčí tým.

![Přiřadit k subtýmu kontrolu obrázků](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Přepínání mezi subskupinami

Pokud jste členem více než jednoho subtýmu, můžete mezi těmito seskupeními přepínat a změnit, které recenze obsahu se vám budou zobrazovat. Na kartě **Revize** vyberte rozevírací nabídku s názvem **výchozí** a vyberte **možnost dílčí tým**. Můžete zobrazit revize obsahu pro různé subtýmy, ale pouze ty, které jsou členy.

![Přepínání mezi subskupinami](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Značky

Karta **značky** vám umožňuje definovat vlastní značky moderování kromě dvou výchozích značek moderování &mdash;  (**a**) a **isracy** (**r**). Když vytvoříte vlastní značku, bude k dispozici v recenzích vedle výchozích značek. To, které značky se zobrazí v recenzích, můžete změnit přepnutím jejich nastavení viditelnosti.

![Zobrazení značek, včetně zaškrtávacích políček "je viditelné"](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Vytváření vlastních značek

Chcete-li vytvořit novou značku, je nutné do příslušných polí zadat krátký kód, název a popis.

- **Krátký kód**: zadejte pro značku kód se dvěma písmeny. Příklad: 
- **Název**: zadejte krátký a popisný název značky malými písmeny bez mezer. Příklad: **isbullying**.
- **Popis**: (volitelné) zadejte popis druhu obsahu, který vaše značka cílí. Příklad: **znázornění nebo instance počítačové bullyingy**.

Kliknutím na **Přidat** přidejte značku a po dokončení vytváření značek klikněte na **Uložit** .

![Dialogové okno pro vytvoření nového tagu nástroje pro kontrolu](images/settings-3-tags.png)

### <a name="delete-tags"></a>Odstranit značky

Vlastní značky můžete odstranit tak, že v seznamu značek vyberete ikonu odpadkového koše vedle jejich položek, ale nemůžete odstranit výchozí značky.

## <a name="connectors"></a>Konektory

Karta **konektory** vám umožní spravovat vaše konektory, které jsou moduly plug-in pro konkrétní služby, které mohou zpracovávat obsah různými způsoby jako součást [pracovních postupů](../review-api.md#workflows)obsahu.

Výchozí konektor při vytváření pracovního postupu je konektor Content Moderator, který může označovat obsah jako **dospělý** nebo **pikantní**, Hledat vulgární výrazy atd. Můžete ale použít i jiné konektory, které jsou tady uvedené, pokud máte přihlašovací údaje pro příslušné služby (například pro použití konektoru obličeje, budete potřebovat klíč předplatného pro [obličej](../../face/overview.md) ).

[Nástroj pro kontrolu](./human-in-the-loop.md) zahrnuje následující konektory:

- Emoce
- Rozpoznávání tváře
- Cloudová služba PhotoDNA
- Analýza textu

### <a name="add-a-connector"></a>Přidání konektoru

Pokud chcete přidat konektor (a zpřístupnit ho pro použití v [pracovních postupech](../review-api.md#workflows)obsahu), vyberte příslušné tlačítko **připojit** . V dalším dialogovém okně zadejte svůj klíč předplatného pro danou službu. Až skončíte, váš nový konektor by se měl zobrazit v horní části stránky.

![Nastavení konektorů Content Moderator](images/settings-4-connectors.png)

## <a name="workflows"></a>Pracovní postupy

Karta **pracovní postupy** vám umožní spravovat [pracovní postupy](../review-api.md#workflows). Pracovní postupy jsou cloudové filtry pro obsah a pracují s konektory k řazení obsahu různými způsoby a provádějí příslušné akce. Tady můžete definovat, upravovat a testovat pracovní postupy. Pokyny k tomu, jak to udělat, najdete v tématu [Definování a použití pracovních postupů](Workflows.md) .

![Nastavení pracovního postupu Content Moderator](images/settings-5-workflows.png)

## <a name="credentials"></a>Přihlašovací údaje

Karta **pověření** poskytuje rychlý přístup k vašemu Content moderator klíč předplatného, který budete potřebovat k přístupu ke všem službám pro moderování z volání REST nebo klientské sady SDK.

![Content Moderator přihlašovací údaje](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Použití externích přihlašovacích údajů pro pracovní postupy

[Nástroj pro revize](https://contentmoderator.cognitive.microsoft.com) vygeneruje bezplatný zkušební klíč pro služby Azure Content moderator Services při registraci, ale můžete ho také nakonfigurovat tak, aby používal existující klíč z účtu Azure. Tento postup se doporučuje u rozsáhlých scénářů, protože bezplatné zkušební klíče mají striktní omezení využití ([ceny a omezení](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)).

Pokud jste vytvořili [prostředek Content moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) v Azure, přejděte k němu v Azure Portal a vyberte okno **klíče** . Zkopírujte jeden z vašich klíčů.

![Content Moderator klíče v Azure Portal](images/credentials-azure-portal-keys.PNG)

Na kartě [Kontrola](https://contentmoderator.cognitive.microsoft.com) **přihlašovacích údajů** nástroje otevřete podokno **Nastavení pracovního postupu** , vyberte **Upravit** a vložte svůj klíč do pole **OCP-APIM-Subscription-Key** . Nyní budou pracovní postupy, které volají rozhraní API pro moderování, používat vaše přihlašovací údaje Azure.

> [!NOTE]
> Další dvě pole v podokně **Nastavení pracovního postupu** slouží pro vlastní seznamy termínů a obrázků. Další informace o těchto tématech najdete v příručkách [vlastní podmínka](../try-terms-list-api.md) nebo [vlastní obrázky](../try-image-list-api.md) .

### <a name="use-your-azure-account-with-the-review-apis"></a>Použití účtu Azure s rozhraními API pro revize

Pokud chcete používat klíč Azure s rozhraními API recenze, musíte načíst ID prostředku. V Azure Portal otevřete prostředek Content Moderator a vyberte okno **vlastnosti** . Zkopírujte hodnotu ID prostředku a vložte ji do pole **ID prostředku (s)** na kartě **pověření** nástroje pro kontrolu.

![ID prostředku Content Moderator v Azure Portal](images/credentials-azure-portal-resourceid.PNG)

Pokud jste zadali klíč předplatného na obou místech, nebude se používat zkušební klíč, který je součástí vašeho účtu nástroje pro kontrolu, ale zůstane k dispozici.

## <a name="next-steps"></a>Další kroky

Postupujte podle pokynů k [rychlému zprovoznění nástrojů pro kontrolu](../quick-start.md) a začněte používat nástroj pro kontrolu ve scénářích Moderování obsahu.