---
title: Migrace do vývojového prostředku Azure
titleSuffix: Azure Cognitive Services
description: Migrujte do vývojového prostředku Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: f5197ae79670e4543c58224a33838706edae6218
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194630"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>Kroky migrace do vývojového prostředku Azure

Z portálu language understanding (LUIS) migrujte všechny aplikace, které vlastníte, abyste používali zdrojový prostředek Azure.

## <a name="prerequisites"></a>Požadavky

* **Volitelně**zálohujte aplikace ze seznamu aplikací portálu LUIS exportem jednotlivých aplikací nebo použijte [exportní rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40).
* **Volitelně**můžete uložit seznam spolupracovníků jednotlivých aplikací. Všichni spolupracovníci mohou být zaslány e-mailem jako součást procesu migrace.
* **Povinné**, musíte mít [předplatné Azure](https://azure.microsoft.com/free/). Část procesu předplatného vyžaduje fakturační údaje. Můžete však použít volné (F0) cenové úrovně při použití LUIS. Možná nakonec zjistíte, že potřebujete placenou úroveň, protože vaše využití se zvyšuje.

Pokud nemáte předplatné Azure, [zaregistrujte se](https://azure.microsoft.com/free/).

## <a name="access-the-migration-process"></a>Přístup k procesu migrace

Každý týden budete vyzváni k migraci aplikací. Toto okno můžete zrušit bez migrace. Pokud chcete migrovat před dalším naplánovaným obdobím, můžete zahájit proces migrace z ikony **Azure** na horním panelu nástrojů portálu LUIS.

> [!div class="mx-imgBorder"]
> ![Ikona migrace](./media/migrate-authoring-key/migration-button.png)

## <a name="app-owner-begins-the-migration-process"></a>Vlastník aplikace zahájí proces migrace

Proces migrace je k dispozici, pokud jste vlastníkem všech aplikací LUIS.

1. Přihlaste se k [portálu LUIS](https://www.luis.ai) a odsouhlaste podmínky použití.
1. Automaticky otevírané okno migrace umožňuje pokračovat v migraci nebo migrovat později. Vyberte **Možnost Migrovat .** Pokud se rozhodnete migrovat později, máte 9 měsíců na migraci na nový klíč pro vytváření v Azure.

    ![První automaticky otevírané okno v procesu migrace, vyberte Migrovat.](./media/migrate-authoring-key/migrate-now.png)

1. Volitelně, pokud má některá z vašich aplikací spolupracovníky, budete vyzváni k **odeslání e-mailu** s informací o migraci. Jedná se o volitelný krok.

    Po migraci účtu do Azure už vaše aplikace nebudou dostupné spolupracovníkům.

    Pro každého spolupracovníka a aplikaci se otevře výchozí e-mailová aplikace s lehce formátaným e-mailem. E-mail můžete před odesláním upravit.

    Šablona e-mailu obsahuje přesné ID aplikace a název aplikace.

    ```html
    Dear Sir/Madam,

    I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

    App Id: <app-ID-omitted>
    App name: Human Resources

    Thank you
    ```

1. Zvolte vytvoření vývojového prostředku LUIS výběrem použití existujícího vývojového prostředku nebo vytvoření nového vývojového prostředku.

    > [!div class="mx-imgBorder"]
    > ![Vytvoření vývojového prostředku](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

1. V dalším okně zadejte informace o klíči zdroje. Po zadání informací vyberte **Vytvořit zdroj**. Můžete mít 10 bezplatných prostředků pro vytváření na oblast a předplatné.

    ![Vytvoření vývojového prostředku](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    Při **vytváření nového zdrojového zdroje**pro vytváření zadejte následující informace:

    * **Název prostředku** – vlastní název, který zvolíte, který se používá jako součást adresy URL pro dotazy koncového bodu vytváření a předpovědi.
    * **Tenant** – tenant, ke kterého je vaše předplatné Azure přidruženo.
    * **Název předplatného** - předplatné, které se bude účtovat pro prostředek.
    * **Skupina prostředků** – vlastní název skupiny prostředků, který zvolíte nebo vytvoříte. Skupiny prostředků umožňují seskupit prostředky Azure pro přístup a správu.
    * **Umístění** - volba umístění je založena na výběru **skupiny prostředků.**
    * **Cenová úroveň** – cenová úroveň určuje maximální transakci za sekundu a měsíc.

1. Ověřte zdroj pro vytváření a **migrujte nyní**.

    ![Vytvoření vývojového prostředku](./media/migrate-authoring-key/choose-authoring-resource-and-migrate.png)

1. Při vytvoření zdrojového zdroje se zobrazí zpráva o úspěchu. Chcete-li zavřít, zavřete možnost **Zavřít.**

    ![Zdroj pro vytváření byl úspěšně vytvořen.](./media/migrate-authoring-key/migration-success.png)

    V seznamu **Moje aplikace** jsou uvedeny aplikace migrované na nový zdroj autora.

    K pokračování úprav aplikací na portálu LUIS nemusíte znát klíč vývojového prostředku. Pokud plánujete upravovat aplikace programově, potřebujete hodnoty vývojového klíče. Tyto hodnoty se zobrazují na stránce **Spravovat > prostředků Azure** na portálu LUIS a jsou taky dostupné na portálu Azure na stránce **klíčů** prostředků.

1. Před přístupem k aplikacím vyberte zdroj předplatného a vývojového prostředku LUIS, abyste viděli aplikace, které můžete vytvářet.

    ![Chcete-li zobrazit aplikace, které můžete vytvářet, vyberte zdroj předplatného a vytváření luis.](./media/migrate-authoring-key/app-list-by-subscription-and-resource.png)


## <a name="app-contributor-begins-the-migration-process"></a>Přispěvatel aplikace zahájí proces migrace

Postupujte podle stejných kroků jako vlastník aplikace pro migraci. Tento proces vytvoří nový zdroj `LUIS.Authoring`vytváření druhu .

Chcete-li přidat jako přispěvatel do migrovaných aplikací vlastněných jinými uživateli, musíte svůj účet migrovat.

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>Po procesu migrace přidejte přispěvatele do zdrojového zdroje pro vytváření.

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Přečtěte [si, jak přidat přispěvatele](luis-how-to-collaborate.md).

## <a name="troubleshooting-errors-with-the-migration-process"></a>Poradce při potížích s procesem migrace

Pokud se `MissingSubscriptionRegistration` během procesu migrace zobrazí chyba na portálu LUIS s červeným oznamovacím pruhem, vytvořte prostředek kognitivní služby na [webu Azure Portal](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) nebo Azure [CLI](luis-how-to-azure-subscription.md#create-resources-in-azure-cli). Další informace o [příčinách této chyby](../../azure-resource-manager/templates/error-register-resource-provider.md#cause).

## <a name="next-steps"></a>Další kroky


* Kontrola [konceptů](luis-concept-keys.md) týkajících se vytváření a kláves runtime
* Kontrola [způsobu přiřazování klíčů](luis-how-to-azure-subscription.md) a [přidávání přispěvatelů](luis-how-to-collaborate.md)
