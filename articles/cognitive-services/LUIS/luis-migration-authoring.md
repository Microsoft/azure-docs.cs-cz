---
title: Migrace do prostředků Azure pro vytváření
titleSuffix: Azure Cognitive Services
description: Migrujte do klíče vývojového prostředku Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: ec6f9592a4c149be382fab66cca27d929644d988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194505"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrace na klíč pro vytváření prostředků Azure

Ověřování ve vytváření jazyka (LUIS) se změnilo z e-mailového účtu na prostředek Azure. I když není aktuálně vyžadováno, přechod na prostředek Azure se bude vynucovat v budoucnu.

## <a name="why-migrate"></a>Proč migrovat?

Použití prostředku Azure pro vytváření umožňuje jako vlastník prostředku řídit přístup k vytváření. Můžete vytvořit a pojmenovat vývojové prostředky pro správu různých skupin autorů.

Například jste vlastníkem 2 aplikací LUIS a máte různé členy, kteří jsou spolupracovníky na každé aplikaci. Můžete vytvořit dva různé vývojové prostředky a přiřadit každou aplikaci ke každému samostatnému prostředku. Pak přiřaďte jednotlivé členy jako přispěvatele k správnému zdroji pro vytváření v závislosti na tom, na které aplikaci spolupracují. Autorizační prostředek Azure řídí autorizaci.

> [!Note]
> Před migrací se spoluautoři označují jako _spolupracovníci_ na úrovni aplikace LUIS. Po migraci se role _přispěvatele_ Azure používá pro stejné funkce, ale na úrovni prostředků Azure.

## <a name="what-is-migrating"></a>Co je migrace?

Migrace zahrnuje:

* Všichni uživatelé služby LUIS, vlastníci a přispěvatelé.
* **Všechny** aplikace.
* **Jednosměrná** migrace.

Vlastník nemůže zvolit podmnožinu aplikací, které chcete migrovat, a proces není reverzibilní.

Migrace není:

* Proces, který shromažďuje spolupracovníky a automaticky přesune nebo přidá do zdrojového prostředku Azure. Tento krok musíte dokončit vy jako vlastník aplikace. Tento krok vyžaduje oprávnění k příslušnému prostředku.
* Proces vytvoření a přiřazení prostředku běhu za běhu předpověď. Pokud potřebujete prostředek běhu předpověď, který je [samostatný proces](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) a je beze změny.

## <a name="how-are-the-apps-migrating"></a>Jak se aplikace migrují?

[Portál LUIS](https://www.luis.ai) poskytuje proces migrace.

Budete vyzváni k migraci, pokud:

* Máte aplikace v systému ověřování e-mailu pro vytváření.
* A vy jste majitel aplikace.

Proces migrace můžete zpozdit zrušením z okna. Budete pravidelně vyzváni k migraci, dokud nepředáte migrál nebo dokud neuběhnete konečný termín migrace. Proces migrace můžete spustit z ikony zámku horního navigačního panelu.

## <a name="migration-for-the-app-owner"></a>Migrace pro vlastníka aplikace

### <a name="before-you-migrate"></a>Před migrací

* **Povinné**, musíte mít [předplatné Azure](https://azure.microsoft.com/free/). Část procesu předplatného vyžaduje fakturační údaje. Však můžete použít free`F0`( ) cenovou úroveň při použití LUIS.
* **Volitelně**zálohujte aplikace ze seznamu aplikací portálu LUIS exportem jednotlivých aplikací nebo použijte [exportní rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40).
* **Volitelně**můžete uložit seznam spolupracovníků jednotlivých aplikací. Tento seznam e-mailů je k dispozici jako součást procesu migrace.


**Vytváření aplikace LUIS je zdarma**, `F0` označené vrstvou. Přečtěte si další informace [o cenových úrovních](luis-boundaries.md#key-limits).

Pokud nemáte předplatné Azure, [zaregistrujte se](https://azure.microsoft.com/free/).

### <a name="migration-steps"></a>Kroky migrace

Postupujte [podle těchto kroků migrace](luis-migration-authoring-steps.md).

### <a name="after-you-migrate"></a>Po migraci

Po procesu migrace jsou teď všechny vaše aplikace LUIS přiřazeny k jednomu vývojovému prostředku LUIS.

Můžete vytvořit další vývojové prostředky a přiřadit ze stránky **Spravovat -> prostředků Azure** na _portálu LUIS_.

Přispěvatele můžete přidat k vývojovému prostředku z _portálu Azure_na stránce **Řízení přístupu (IAM)** pro tento prostředek. Další informace naleznete v tématu [přidání přístupu přispěvatele](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource).

|Portál|Účel|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Vytvořte predikci a vytváření zdrojů.<br>* Přiřaďte přispěvatele.|
|[LUIS](https://www.luis.ai)|* Migrovat do nových zdrojů vytváření.<br>* Přiřaďte nebo odřaďte predikci a vytváření prostředků k aplikacím ze stránky **Spravovat -> prostředků Azure.**|

## <a name="migration-for-the-app-contributor"></a>Migrace pro přispěvatele aplikace

Každý uživatel služby LUIS musí migrovat, včetně spolupracovníků nebo přispěvatelů. Aby měl spolupracovník přístup k aplikaci, musí migrovat.

> [!Note]
> Pokud vlastník aplikace LUIS migroval a přidal spolupracovníka jako přispěvatele na prostředek Azure, spolupracovník bude mít stále žádný přístup k aplikaci, pokud také migrovat.

### <a name="before-the-app-is-migrated"></a>Před migrací aplikace

Můžete se rozhodnout exportovat aplikaci, na které jste spolupracovníkem, a pak ji importovat zpět do služby LUIS. Proces importu vytvoří novou aplikaci s novým ID aplikace, pro kterou jste vlastníkem.

### <a name="after-the-app-is-migrated"></a>Po migraci aplikace

Vlastník aplikace musí [přidat váš e-mail do zdrojového prostředku Azure jako spolupracovník](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource).

Po procesu migrace jsou všechny aplikace, které vlastníte, dostupné na stránce **Moje aplikace** na portálu LUIS.

## <a name="troubleshooting-the-migration-process-for-luis-authoring"></a>Poradce při potížích s procesem migrace pro vytváření luis

* Vývojové klíče LUIS jsou viditelné pouze na portálu LUIS po dokončení procesu migrace. Pokud vytvoříte vývojové klíče, například pomocí rozhraní ŘÍZENÍ UŽIVATELSKÝCH ROZHRANÍ LUIS, uživatel stále potřebuje dokončit proces migrace na portálu LUIS.
* Pokud migrovaný uživatel přidá nemigrovaného uživatele jako přispěvatele do svého prostředku Azure, nemigrovaný uživatel nebude mít přístup k aplikacím, pokud se nemigruje.
* Pokud nemigrovaný uživatel není vlastníkem žádné aplikace, ale je spolupracovníkem jiných aplikací, které jsou vlastněny jinými uživateli a vlastníci prošli procesem migrace, bude muset tento uživatel migrovat, aby měl přístup k aplikacím.
* Pokud nemigrovaný uživatel přidal jiného migrovaného uživatele jako spolupracovníka do své aplikace, dojde k chybě, protože migrovaného uživatele nebudete moci přidat jako spolupracovníka do aplikace. Nemigrovaný uživatel pak bude muset projít procesem migrace a vytvořit prostředek azure a přidat migrovaného uživatele jako přispěvatele k tomuto prostředku.

Během procesu migrace se zobrazí chyba, pokud:
* Vaše předplatné neopravňuje k vytváření prostředků služeb Cognitive Services.
* Migrace negativně ovlivňuje všechny aplikace za běhu. Při migraci se z vašich aplikací odeberou všichni spolupracovníci a vy jste odebráni jako spolupracovníci z jiných aplikací. Tento proces znamená, že klíče, které jste přiřadili, budou také odstraněny. Migrace se zablokuje, pokud jste přiřadili klíče v jiných aplikacích. Před migrací odeberte bezpečně přiřazený klíč. Pokud víte, že klíč, který jste přiřadili, se nepoužívá v době běhu, pak je nutné jej odebrat, aby bylo možné postupovat v migraci.

Přístup k seznamu prostředků Azure vaší aplikace pomocí následujícího formátu adresy URL:

`https://www.luis.ai/applications/REPLACE-WITH-YOUR-APP-ID/versions/REPLACE-WITH-YOUR-VERSION-ID/manage/resources`

## <a name="next-steps"></a>Další kroky

* [Jak migrovat aplikaci do zdrojového zdroje](luis-migration-authoring-steps.md)
