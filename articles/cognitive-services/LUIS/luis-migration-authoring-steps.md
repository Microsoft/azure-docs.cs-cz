---
title: Migrace na prostředek pro vytváření obsahu Azure
titleSuffix: Azure Cognitive Services
description: Migrujte na prostředek pro vytváření obsahu Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: 9c92a3ad2fc6a372b58aa651228a7bbccdf8ba0a
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552014"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>Postup migrace do prostředku pro vytváření obsahu Azure

Na portálu Language Understanding (LUIS) migrujte všechny aplikace, které vlastníte, abyste mohli používat prostředek pro vytváření obsahu Azure.

## <a name="prerequisites"></a>Požadavky

* **Volitelně můžete**aplikace zálohovat ze seznamu aplikací portálu Luis, a to tak, že jednotlivé aplikace exportujete nebo použijete [rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)pro export.
* **Volitelně můžete**Uložit seznam collaborator's jednotlivých aplikací. V rámci procesu migrace je možné odeslat e-mail všem spolupracovníkům.
* Je **potřeba mít** [předplatné Azure](https://azure.microsoft.com/free/). Součástí procesu předplatného je vyžadování fakturačních informací. Když ale použijete LUIS, můžete použít cenové úrovně Free (F0). Po zvýšení využití možná budete potřebovat placenou úroveň.

Pokud nemáte předplatné Azure, [Zaregistrujte](https://azure.microsoft.com/free/)se.

## <a name="access-the-migration-process"></a>Přístup k procesu migrace

Každý týden budete vyzváni k migraci svých aplikací. Toto okno můžete zrušit bez migrace. Pokud chcete provést migraci před dalším naplánovaným obdobím, můžete zahájit proces migrace z ikony **zámku** na horním panelu nástrojů portálu Luis.

## <a name="app-owner-begins-the-migration-process"></a>Vlastník aplikace zahájí proces migrace.

Pokud jste vlastníkem libovolných aplikací LUIS, je proces migrace k dispozici.

1. Přihlaste se k [portálu Luis](https://www.luis.ai) a odsouhlaste podmínky použití.
1. Automaticky otevírané okno migrace vám umožní pokračovat v migraci nebo migrovat později. Vyberte **migrovat nyní**. Pokud se rozhodnete migrovat později, máte 9 měsíců na migraci nového vývojového klíče v Azure.

    ![První automaticky otevírané okno v procesu migrace vyberte migrovat nyní.](./media/migrate-authoring-key/migrate-now.png)

1. Případně, pokud má kterákoli z vašich aplikací spolupracovníky, budete vyzváni k **odeslání e-mailu s oznámením** o migraci. Tento krok je volitelný.

    Po dokončení migrace účtu do Azure už nebudou vaše aplikace k dispozici pro spolupracovníky.

    U každého spolupracovníka a aplikace se výchozí e-mailová aplikace otevře s lehce formátovaným e-mailem. E-mail můžete před odesláním upravit.

    Šablona e-mailu obsahuje přesné ID aplikace a název aplikace.

    ```html
    Dear Sir/Madam,

    I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

    App Id: <app-ID-omitted>
    App name: Human Resources

    Thank you
    ```

1. Vyberte, pokud chcete vytvořit prostředek pro vytváření obsahu LUIS výběrem možnosti použít existující prostředek pro vytváření obsahu, nebo vytvořte nový prostředek pro vytváření.

    > [!div class="mx-imgBorder"]
    > ![vytvořit prostředek pro vytváření obsahu](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

1. V dalším okně zadejte informace o svém klíči prostředku. Po zadání informací vyberte **vytvořit prostředek**. Můžete mít 10 volných prostředků pro vytváření na jednotlivých oblastech a na jedno předplatné.

    ![Vytvořit prostředek pro vytváření](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    Při **vytváření nového prostředku pro vytváření obsahu**zadejte následující informace:

    * **Název prostředku** – vlastní název, který zvolíte, se používá jako součást adresy URL pro dotazy na koncový bod vytváření a předpovědi.
    * **Tenant** – tenant, ke kterému je předplatné Azure přidružené.
    * **Název předplatného** – předplatné, které se bude fakturovat za prostředek.
    * **Skupina prostředků** – název vlastní skupiny prostředků, který zvolíte nebo vytvoříte. Skupiny prostředků umožňují seskupit prostředky Azure pro přístup a správu.
    * **Umístění** – Volba umístění je založená na výběru **skupiny prostředků** .
    * **Cenová úroveň** – cenová úroveň určuje maximální transakci za sekundu a měsíc.

1. Ověřte prostředek pro vytváření obsahu a **migrujte ho hned teď**.

    ![Vytvořit prostředek pro vytváření](./media/migrate-authoring-key/choose-authoring-resource-and-migrate.png)

1. Po vytvoření prostředku pro vytváření se zobrazí zpráva o úspěchu. Kliknutím na **Zavřít** zavřete automaticky otevírané okno.

    ![Váš prostředek pro vytváření obsahu se úspěšně vytvořil.](./media/migrate-authoring-key/migration-success.png)

    V seznamu **Moje aplikace** se zobrazí aplikace migrované do nového prostředku pro vytváření.

    Pokud chcete pokračovat v úpravách vašich aplikací na portálu LUIS, nemusíte znát klíč prostředku pro vytváření obsahu. Pokud plánujete upravovat aplikace programově, budete potřebovat hodnoty pro vytváření kódu. Tyto hodnoty se zobrazí na stránce **Správa prostředků Azure >** na portálu Luis a jsou k dispozici také v Azure Portal na stránce **klíče** prostředku.

1. Než budete mít přístup k vašim aplikacím, vyberte prostředek pro vytváření předplatného a LUIS, abyste viděli aplikace, které můžete vytvořit.

    ![Pokud chcete zobrazit aplikace, které může vytvořit, vyberte předplatné a LUIS Authoring Resource.](./media/migrate-authoring-key/app-list-by-subscription-and-resource.png)


## <a name="app-contributor-begins-the-migration-process"></a>Přispěvatel aplikace zahájí proces migrace.

Použijte stejný postup jako u vlastníka aplikace pro migraci. Proces vytvoří nový prostředek pro vytváření obsahu typu `LUIS.Authoring`.

Abyste se mohli přidat jako přispěvatel do migrovaných aplikací vlastněných ostatními, musíte svůj účet migrovat.

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>Po dokončení procesu migrace přidejte přispěvatele do prostředku pro vytváření obsahu.

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Přečtěte si, [jak přidat přispěvatele](luis-how-to-collaborate.md).

## <a name="troubleshooting-errors-with-the-migration-process"></a>Řešení chyb v procesu migrace

Pokud se zobrazí chyba `MissingSubscriptionRegistration` na portálu LUIS s červeným oznamovacím pruhem během procesu migrace, vytvořte v [Azure Portal](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) nebo v [Azure CLI](luis-how-to-azure-subscription.md#create-resources-in-azure-cli)prostředek služby vnímání. Přečtěte si další informace o [příčinách této chyby](../../azure-resource-manager/templates/error-register-resource-provider.md#cause).

## <a name="next-steps"></a>Další kroky


* Přečtěte si [Koncepty](luis-concept-keys.md) o vytváření a klíčích modulu runtime
* Přečtěte si, [jak přiřadit klíče](luis-how-to-azure-subscription.md) a přidat [přispěvatele](luis-how-to-collaborate.md) .
