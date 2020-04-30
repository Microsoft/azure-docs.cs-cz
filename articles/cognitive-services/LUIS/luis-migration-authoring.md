---
title: Migrace na prostředek Azure pro vytváření obsahu
titleSuffix: Azure Cognitive Services
description: Migrujte na klíč prostředku pro vytváření Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: 679073715588a4a81e69e3e7ba2d18341b1bab4b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82096618"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrace na klíč pro vytváření prostředků Azure

Language Understanding (LUIS) ověřování při vytváření se změnilo z e-mailového účtu na prostředek Azure. I když to není v současnosti nutné, bude v budoucnu vynutilo přepnutí do prostředku Azure.

## <a name="why-migrate"></a>Proč migrovat?

Pomocí prostředku Azure pro vytváření obsahu můžete jako vlastníka prostředku řídit přístup k vytváření obsahu. Můžete vytvořit a pojmenovat prostředky vytváření pro správu různých skupin autorů.

Například jste vlastníkem dvou aplikací LUIS a máte různé členy, kteří jsou spolupracovníky na každé aplikaci. Můžete vytvořit dva různé prostředky pro vytváření obsahu a přiřadit každou aplikaci každému samostatnému prostředku. Pak každému členovi přiřaďte jako přispěvateli správný prostředek pro vytváření obsahu v závislosti na tom, která aplikace na nich spolupracuje. Prostředek pro vytváření Azure řídí autorizaci.

> [!Note]
> Před migrací se jako _spolupracovníci_ na úrovni aplikace Luis označují spoluautoři. Po migraci se role Azure _přispěvatele_ používá pro stejné funkce, ale na úrovni prostředků Azure.

## <a name="what-is-migrating"></a>Co se migruje?

Migrace zahrnuje:

* Všichni uživatelé LUIS, vlastníci a přispěvatelé.
* **Všechny** aplikace
* **Jednosměrná** migrace.

Vlastník nemůže zvolit podmnožinu aplikací k migraci a proces nebude vratný.

Migrace není:

* Proces, který shromažďuje spolupracovníky a automaticky přesouvá nebo přidává do prostředku pro vytváření obsahu Azure. Jako vlastník aplikace je potřeba tento krok dokončit. Tento krok vyžaduje oprávnění k příslušnému prostředku.
* Proces vytvoření a přiřazení prostředku modulu runtime předpovědi. Pokud potřebujete běhový prostředek předpovědi, je to [samostatný proces](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) a nemění se.

## <a name="how-are-the-apps-migrating"></a>Jak se aplikace migrují?

[Portál Luis](https://www.luis.ai) poskytuje proces migrace.

Budete vyzváni k migraci, pokud:

* Máte aplikace v systému ověřování e-mailů pro vytváření obsahu.
* A jste vlastníkem aplikace.

Proces migrace můžete odložit zrušením z okna. Pravidelně budete požádáni o migraci, dokud neprovedete migraci nebo neuplyne konečný termín migrace. Proces migrace můžete spustit z ikony zámku horní navigační panel.

## <a name="migration-for-the-app-owner"></a>Migrace pro vlastníka aplikace

### <a name="before-you-migrate"></a>Před migrací

* Je **potřeba mít** [předplatné Azure](https://azure.microsoft.com/free/). Součástí procesu předplatného je vyžadování fakturačních informací. Při použití LUIS ale můžete použít cenovou`F0`úroveň Free ().
* **Volitelně můžete**aplikace zálohovat ze seznamu aplikací portálu Luis, a to tak, že jednotlivé aplikace exportujete nebo použijete [rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)pro export.
* **Volitelně můžete**Uložit seznam collaborator's jednotlivých aplikací. Tento e-mailový seznam se poskytuje jako součást procesu migrace.


**Vytváření aplikací Luis je zadarmo**, které uvádí `F0` vrstva. Přečtěte si [Další informace o cenových úrovních](luis-limits.md#key-limits).

Pokud nemáte předplatné Azure, [Zaregistrujte](https://azure.microsoft.com/free/)se.

### <a name="migration-steps"></a>Kroky migrace

Postupujte podle [těchto kroků migrace](luis-migration-authoring-steps.md).

### <a name="after-you-migrate"></a>Po dokončení migrace

Po dokončení procesu migrace se teď všechny vaše aplikace LUIS přiřadí k jednomu prostředku pro vytváření LUIS.

Na _portálu Luis_můžete vytvořit další prostředky pro vytváření obsahu a přiřadit je na stránce **Spravovat > prostředky Azure** .

Přispěvatele můžete přidat do prostředku vytváření z _Azure Portal_na stránce **Access Control (IAM)** daného prostředku. Další informace najdete v tématu [Přidání přístupu přispěvatele](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource).

|Portál|Účel|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Vytvořte předpověď a vytváření prostředků.<br>* Přiřaďte přispěvatele.|
|[LUIS](https://www.luis.ai)|* Migrujte na nové prostředky pro vytváření obsahu.<br>* Přiřaďte nebo zrušte přiřazení předpovědí a vytváření prostředků aplikacím ze stránky **Spravovat – > prostředky Azure** .|

## <a name="migration-for-the-app-contributor"></a>Migrace pro přispěvatele aplikací

Každý uživatel LUIS musí migrovat, včetně spolupracovníků a přispěvatelů. Spolupracovníka musí migrovat, aby měl přístup k aplikaci.

> [!Note]
> Pokud vlastník aplikace LUIS migrovali a přidal spolupracovníka jako přispěvatele na prostředku Azure, spolupracovníka nebude mít k této aplikaci k dispozici žádný přístup, pokud se ani nepřenáší.

### <a name="before-the-app-is-migrated"></a>Před migrací aplikace

Můžete zvolit export aplikace, kterou jste spolupracovníka, a pak aplikaci importovat zpátky do LUIS. Proces importu vytvoří novou aplikaci s novým ID aplikace, pro kterou jste vlastníkem.

### <a name="after-the-app-is-migrated"></a>Po migraci aplikace

Vlastník aplikace musí [Přidat váš e-mail do prostředku pro tvorbu Azure jako spolupracovníka](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource).

Po dokončení procesu migrace budou všechny aplikace, které vlastníte, k dispozici na stránce **Moje aplikace** na portálu Luis.

## <a name="troubleshooting-the-migration-process-for-luis-authoring"></a>Řešení potíží s procesem migrace pro vytváření LUIS

* Klíče pro vytváření LUIS se po dokončení procesu migrace zobrazují jenom na portálu LUIS. Pokud vytváříte klíče pro vytváření obsahu, například s rozhraním příkazového řádku LUIS, uživatel pořád potřebuje dokončit proces migrace na portálu LUIS.
* Pokud migrovaný uživatel přidá nemigrovaného uživatele jako přispěvatele na svém prostředku Azure, nemigrovaný uživatel nebude mít přístup k aplikacím, dokud nemigruje.
* Pokud nemigrovaný uživatel není vlastníkem žádné aplikace, ale spolupracuje s jinými aplikacemi, které vlastní jiní uživatelé, a vlastníci prošli procesem migrace, bude muset tento uživatel migrovat, aby měl přístup k aplikacím.
* Pokud uživatelem nemigrovaný uživatel přidal do své aplikace dalšího migrovaného uživatele, dojde k chybě, protože nebudete moct do aplikace přidat migrovaného uživatele jako spolupracovníka. Nemigrovaný uživatel pak bude muset projít procesem migrace a vytvořit prostředek Azure a přidat migrovaného uživatele jako přispěvatele k tomuto prostředku.

Během procesu migrace se zobrazí chyba, pokud:
* Vaše předplatné vám neopravňuje k vytváření Cognitive Servicesch prostředků.
* Vaše migrace negativně ovlivňuje všechny aplikace za běhu. Při migraci se z vašich aplikací odeberou všechny spolupracovníci a Vy jste z jiných aplikací odebrali jako spolupracovníka. Tento proces znamená, že klíče, které jste přiřadili, se taky odebraly. Migrace se zablokuje, pokud jste přiřadili klíče v jiných aplikacích. Před migrací odeberte klíč, který jste přiřadili bezpečně. Pokud víte, že klíč, který jste přiřadili, se v modulu runtime nepoužívá, musíte ho odebrat, aby bylo možné v migraci provést průběh migrace.

Přístup k seznamu prostředků Azure vaší aplikace pomocí následujícího formátu adresy URL:

`https://www.luis.ai/applications/REPLACE-WITH-YOUR-APP-ID/versions/REPLACE-WITH-YOUR-VERSION-ID/manage/resources`

## <a name="next-steps"></a>Další kroky

* [Postup migrace aplikace do prostředku pro vytváření](luis-migration-authoring-steps.md)
