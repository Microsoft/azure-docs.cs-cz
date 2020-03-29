---
title: Konfigurace nastavení nástroje revize – Moderátor obsahu
titleSuffix: Azure Cognitive Services
description: Pomocí nástroje Revize můžete nakonfigurovat nebo načíst tým, značky, konektory, pracovní postupy a přihlašovací údaje pro moderátora obsahu.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 2d685683bdc359b31a5a6c550c19e8c0d858f12a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220124"
---
# <a name="configure-the-review-tool"></a>Konfigurace nástroje pro recenze

[Nástroj Revize](https://contentmoderator.cognitive.microsoft.com) obsahuje několik důležitých funkcí, ke kterým máte přístup prostřednictvím nabídky **Nastavení** na řídicím panelu.

![Nabídka Přejděte na revizi přehoz obsahu](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Správa týmů a podtýmů

Karta **Tým** umožňuje spravovat týmové a&mdash;podpárové skupiny uživatelů, kteří mohou být upozorněni při spuštění určitých [lidských recenzí.](../review-api.md#reviews) Můžete mít pouze jeden tým (který vytvoříte při registraci pomocí nástroje revize), ale můžete vytvořit více podtýmů. Správce týmu může pozvat členy, nastavit jejich oprávnění a přiřadit je k různým podtýmům.

![Zkontrolovat nastavení týmu nástroje](images/settings-2-team.png)

Dílčí týmy jsou užitečné pro vytváření týmů pro eskalaci nebo týmů určených k kontrole konkrétních kategorií obsahu. Můžete například odeslat obsah pro dospělé do samostatného týmu k dalšímu prohledání.

Tato část vysvětluje, jak vytvořit podpáry a rychle přiřadit recenze za chodu. [Pracovní postupy](workflows.md) však můžete použít k přiřazení recenzí na základě konkrétních kritérií.

### <a name="create-a-subteam"></a>Vytvoření podtýmu

Přejděte do části **Podtýmy** a klikněte na **Přidat podtýmový tým**. Do dialogového okna zadejte název podtýmu a klepněte na **tlačítko Uložit**.

![Název podtýmu](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Pozvání spoluhráčů

Pokud ještě není členem výchozího týmu, nemůžete někoho přiřadit k podtýmu, takže nejprve musíte přidat recenzenty do výchozího týmu. Na kartě **Tým** klikněte na **Pozvat.**

![Pozvání uživatelů](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Přiřazení spoluhráčů k podtýmu

Kliknutím na tlačítko **Přidat člena** přiřadíte členy z výchozího týmu do jednoho nebo více podtýmů. Stávající uživatele můžete přidat pouze do podtýmu. Pokud přidáte nové uživatele, kteří nejsou v nástroji pro kontrolu, pozvěte je pomocí tlačítka "Pozvat" na stránce Nastavení týmu.

![Přiřazení členů podtýmu](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Přiřazení recenzí podtýmům

Po vytvoření podtýmů a přiřazených členů můžete začít těmto podtýmům přiřazovat [recenze](../review-api.md#reviews) obsahu. To se provádí z karty **Revize** webu.
Chcete-li přiřadit obsah podtýmu, klepněte na tři tečky v pravém horním rohu, vyberte **Přesunout do**a vyberte podtým.

![Přiřazení kontroly obrázků podtýmu](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Přepínání mezi podtými

Pokud jste členem více než jednoho podtýmu, můžete mezi těmito podtýmy přepínat a změnit, které recenze obsahu se zobrazí za vás. Na kartě **Revize** vyberte rozevírací nabídku s názvem **Výchozí** a vyberte **Zvolit podtýmový .** Můžete zobrazit recenze obsahu pro různé podpáry, ale pouze ty, jejichž jste členem.

![Přepínání mezi podtými](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Značky

Karta **Tagy** umožňuje definovat vlastní značky moderování kromě&mdash;dvou výchozích značek moderování**isadult** **(** a ) a **isracy** (**r**). Když vytvoříte vlastní značku, bude dostupná v recenzích vedle výchozích značek. Značky, které se zobrazují v recenzích, můžete změnit přepnutím jejich nastavení viditelnosti.

![Zobrazení značek, včetně zaškrtávacích políček "Je viditelné".](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Vytvoření vlastních značek

Chcete-li vytvořit novou značku, musíte do příslušných polí zadat krátký kód, název a popis.

- **Krátký kód**: Zadejte dvoupísmenný kód značky. Příklad: **cb**
- **Název**: Zadejte krátký a popisný název značky v malých písmenech bez mezer. Příklad: **isbullying**.
- **Popis**: (nepovinné) Zadejte popis druhu obsahu, na který vaše značka cílí. Příklad: **Vyobrazení nebo případy kybernetické šikany**.

Kliknutím na **Přidat** přidejte značku a po dokončení vytváření značek klikněte na **Uložit.**

![Nástroj revize vytvořit dialogové okno vytvořit nový tag](images/settings-3-tags.png)

### <a name="delete-tags"></a>Odstranit značky

Vlastní značky můžete odstranit výběrem ikony koše vedle jejich položek v seznamu Značky, ale výchozí značky nemůžete odstranit.

## <a name="connectors"></a>Konektory

Karta **Konektory** umožňuje spravovat konektory, což jsou moduly plug-in specifické pro služby, které mohou zpracovávat obsah různými způsoby jako součást [pracovních postupů](../review-api.md#workflows)obsahu .

Výchozí spojnicí při vytváření pracovního postupu je konektor Content Moderator, který může označit obsah jako **dospělý** nebo **pikantní**, najít vulgární výrazy a tak dále. Můžete však použít jiné konektory, které jsou zde uvedeny, pokud máte pověření pro příslušné služby (chcete-li například použít konektor face, budete muset získat klíč předplatného [Face).](https://docs.microsoft.com/azure/cognitive-services/face/overview)

[Nástroj revize](./human-in-the-loop.md) obsahuje následující konektory:

- Emoce
- Tvář
- Cloudová služba PhotoDNA
- Analýza textu

### <a name="add-a-connector"></a>Přidání spojnice

Chcete-li přidat spojnici (a zpřístupnit ji pro použití v [pracovních postupech](../review-api.md#workflows)obsahu ), vyberte příslušné tlačítko **Připojit.** V dalším dialogu zadejte klíč předplatného pro tuto službu. Po dokončení by se nový spojnice měl zobrazit v horní části stránky.

![Nastavení konektorů moderátora obsahu](images/settings-4-connectors.png)

## <a name="workflows"></a>Pracovní postupy

Karta **Pracovní postupy** umožňuje spravovat pracovní [postupy](../review-api.md#workflows). Pracovní postupy jsou cloudové filtry pro obsah a pracují s konektory pro řazení obsahu různými způsoby a příslušné akce. Zde můžete definovat, upravovat a testovat pracovní postupy. Pokyny k tomu, jak to provést, najdete v tématu [Definování a používání pracovních postupů.](Workflows.md)

![Nastavení pracovního postupu moderátora obsahu](images/settings-5-workflows.png)

## <a name="credentials"></a>Přihlašovací údaje

Karta **Přihlašovací údaje** poskytuje rychlý přístup ke klíči předplatného Moderátor obsahu, který budete potřebovat pro přístup ke všem službám moderování z volání REST nebo sady SDK klienta.

![Pověření moderátora obsahu](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Použití externích pověření pro pracovní postupy

[Nástroj revize](https://contentmoderator.cognitive.microsoft.com) generuje bezplatný zkušební klíč pro služby Azure Content Moderator při registraci, ale můžete ho také nakonfigurovat tak, aby používal existující klíč z vašeho účtu Azure. To se doporučuje pro rozsáhlé scénáře, protože bezplatné zkušební klíče mají přísné limity využití[(Ceny a limity).](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)

Pokud jste v Azure vytvořili [prostředek Content Moderator,](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) přejděte na něj na webu Azure portal a vyberte okno **Klíče.** Zkopírujte jeden z vašich klíčů.

![Klíče Moderátor obsahu na webu Azure Portal](images/credentials-azure-portal-keys.PNG)

Na kartě **Pověření** [nástroje revize](https://contentmoderator.cognitive.microsoft.com)přejděte do podokna **Nastavení pracovního postupu,** vyberte **Upravit**a vložte klíč do pole **Ocp-Apim-Subscription-Key.** Pracovní postupy, které volají nastavení API pro moderování, teď budou používat vaše přihlašovací údaje Azure.

> [!NOTE]
> Další dvě pole v podokně **Nastavení pracovního postupu** jsou pro vlastní seznamy termínů a obrázků. Informace o [těchto podmínkách](../try-terms-list-api.md) nebo [v návodu k vlastním obrázkům](../try-image-list-api.md) najdete v návodu k vlastním upočtením.

### <a name="use-your-azure-account-with-the-review-apis"></a>Použití účtu Azure s revizními api

Chcete-li použít klíč Azure s recenzovat API, musíte načíst ID prostředku. Přejděte na svůj prostředek Content Moderator na webu Azure portal a vyberte okno **Vlastnosti.** Zkopírujte hodnotu ID prostředku a vložte ji do pole **Id prostředku uvedeného na seznamu povolených** na kartě **Pověření** nástroje revize.

![ID prostředku moderátora obsahu na webu Azure Portal](images/credentials-azure-portal-resourceid.PNG)

Pokud jste klíč předplatného zadali na obou místech, zkušební klíč, který je dodáván s účtem nástroje revize, nebude použit, ale zůstane k dispozici.

## <a name="next-steps"></a>Další kroky

Chcete-li začít používat nástroj Revize ve scénářích moderování obsahu, postupujte podle [rychlého startu nástroje Revize.](../quick-start.md)