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
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 8e732496c4523646f02ef461b7d3c52314c28dae
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932833"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrace na klíč pro vytváření prostředků Azure

Language Understanding (LUIS) ověřování při vytváření se změnilo z e-mailového účtu na prostředek Azure. 

## <a name="why-migrate"></a>Proč migrovat?

Pomocí prostředku Azure pro vytváření obsahu můžete jako vlastníka prostředku řídit přístup k vytváření obsahu. Můžete vytvořit a pojmenovat prostředky vytváření pro správu různých skupin autorů. 

Například pokud máte 2 typy aplikací LUIS, které vytváříte, s různými členy, můžete vytvořit dva různé prostředky pro vytváření obsahu a přiřadit přispěvatele. Prostředek pro vytváření Azure řídí autorizaci. 

> [!Note]
> Před migrací se jako _spolupracovníci_označují spoluautoři. Po migraci se pro stejnou funkci používá role Azure _Přispěvatel_ .

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

* **Volitelně můžete**aplikace zálohovat ze seznamu aplikací portálu Luis, a to tak, že jednotlivé aplikace exportujete nebo použijete [rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)pro export.
* **Volitelně můžete**Uložit seznam collaborator's jednotlivých aplikací. Tento e-mailový seznam se poskytuje jako součást procesu migrace.
* Je **potřeba mít** [předplatné Azure](https://azure.microsoft.com/free/). Součástí procesu předplatného je vyžadování fakturačních informací. Při použití Luis ale můžete použít cenovou`F0`úroveň Free (). 

**Vytváření aplikací Luis je zadarmo**, které uvádí `F0` vrstva. Přečtěte si [Další informace o cenových úrovních](luis-boundaries.md#key-limits).

Pokud nemáte předplatné Azure, [Zaregistrujte](https://azure.microsoft.com/free/)se. 

### <a name="migration-steps"></a>Kroky migrace

Postupujte podle [těchto kroků migrace](luis-migration-authoring-steps.md).

### <a name="after-you-migrate"></a>Po migraci 

Po dokončení procesu migrace se teď všechny vaše aplikace LUIS přiřadí k jednomu prostředku pro vytváření LUIS.

Na _portálu Luis_můžete vytvořit další prostředky pro vytváření obsahu a přiřadit je na stránce **Spravovat > prostředky Azure** . 

Přispěvatele můžete přidat do prostředku vytváření z _Azure Portal_na stránce **Access Control (IAM)** daného prostředku. Další informace najdete v tématu [Přidání přístupu přispěvatele](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource) .

|Portál|Účel|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Vytvořte předpověď a vytváření prostředků.<br>* Přiřaďte přispěvatele.|
|[LUIS](https://www.luis.ai)|* Migrujte na nové prostředky pro vytváření obsahu.<br>* Přiřaďte nebo zrušte přiřazení předpovědí a vytváření prostředků aplikacím ze stránky **Spravovat – > prostředky Azure** .| 

## <a name="migration-for-the-app-contributor"></a>Migrace pro přispěvatele aplikací

Každý uživatel LUIS musí migrovat, včetně spolupracovníků a přispěvatelů. 

### <a name="before-the-app-is-migrated"></a>Před migrací aplikace

Můžete zvolit export aplikace, kterou jste spolupracovníka, a pak aplikaci importovat zpátky do LUIS. Proces importu vytvoří novou aplikaci s novým ID aplikace, pro kterou jste vlastníkem.

### <a name="after-the-app-is-migrated"></a>Po migraci aplikace

Vlastník aplikace musí [Přidat váš e-mail do prostředku pro tvorbu Azure jako spolupracovníka](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource). 

Po dokončení procesu migrace budou všechny aplikace, které vlastníte, k dispozici na stránce **Moje aplikace** na portálu Luis.  

## <a name="next-steps"></a>Další postup

* [Postup migrace aplikace do prostředku pro vytváření](luis-migration-authoring-steps.md)