---
title: Vytvoření účtu Azure Remote Renderingu
description: Popisuje kroky pro vytvoření účtu pro vzdálené vykreslování Azure.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: 83bd4a7ae0082d24f7ac617719e628f4db4baeb9
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197624"
---
# <a name="create-an-azure-remote-rendering-account"></a>Vytvoření účtu Azure Remote Renderingu

Tato kapitola vás provede postupem vytvoření účtu pro službu **Azure Remote rendering** . Platný účet je povinný pro dokončení některého z rychlých startů nebo kurzů.

## <a name="create-an-account"></a>Vytvoření účtu

Pro vytvoření účtu pro službu Azure Remote rendering je potřeba provést tyto kroky:

1. Přejít na [stránku Preview ve smíšeném realitě](https://aka.ms/MixedRealityPrivatePreview)
1. Klikněte na tlačítko vytvořit prostředek.
1. Do vyhledávacího pole ("Hledat na webu Marketplace") zadejte "vzdálené vykreslování" a stiskněte klávesu ENTER.
1. V seznamu výsledků klikněte na dlaždici vzdálené vykreslování.
1. Na další obrazovce klikněte na tlačítko vytvořit. Otevře se formulář, ve kterém se vytvoří nový účet vzdáleného vykreslování:
    1. Nastavte název prostředku na název účtu.
    1. Pokud je to potřeba, aktualizujte předplatné.
    1. Nastavte skupinu prostředků na skupinu prostředků podle vašeho výběru.
    1. V rozevíracím seznamu umístění vyberte oblast, ve které se má prostředek vytvořit. Podívejte se na části poznámky v níže uvedených [oblastech účtů](create-an-account.md#account-regions) .
1. Po vytvoření účtu přejděte k němu a:
    1. Na kartě *Přehled* si poznamenejte "ID účtu".
    1. Na kartě *nastavení > přístupové klíče* si poznamenejte "primární klíč" – Jedná se o klíč tajného účtu účtu.

### <a name="account-regions"></a>Oblasti účtů
Umístění, které je zadáno během vytváření účtu, určuje, ke které oblasti je přiřazen prostředek účtu. Tuto změnu nelze po vytvoření změnit. Účet se ale dá použít pro připojení k relaci vzdáleného vykreslování v libovolné [podporované oblasti](./../reference/regions.md)bez ohledu na umístění účtu.

### <a name="retrieve-the-account-information"></a>Načtení informací o účtu

Ukázky a kurzy vyžadují, abyste zadali ID účtu a klíč. Například v **arrconfig.js** souboru, který se používá pro ukázkové skripty prostředí PowerShell:

```json
"accountSettings": {
    "arrAccountId": "<fill in the account ID from the Azure portal>",
    "arrAccountKey": "<fill in the account key from the Azure portal>",
    "region": "<select from available regions>"
},
```

Projděte si [seznam dostupných oblastí](../reference/regions.md) pro vyplňování možnosti *region* .

Hodnoty pro **`arrAccountId`** a **`arrAccountKey`** lze nalézt na portálu, jak je popsáno v následujícím postupu:

* Přejděte na web [Azure Portal](https://www.portal.azure.com).
* Najít **účet vzdáleného vykreslování** – měl by být v seznamu **Poslední prostředky** . Můžete ji také vyhledat na panelu hledání v horní části. V takovém případě se ujistěte, že odběr, který chcete použít, je vybraný ve výchozím filtru předplatného (ikona filtru vedle panelu hledání):

![Filtr předplatných](./media/azure-subscription-filter.png)

Kliknutím na svůj účet přejdete na tuto obrazovku, ve které se hned zobrazuje  **ID účtu** :

![ID účtu Azure](./media/azure-account-id.png)

Pro klíč vyberte **přístupové klíče** na panelu vlevo. Na další stránce se zobrazuje primární a sekundární klíč:

![Přístupové klíče Azure](./media/azure-account-primary-key.png)

Hodnota pro **`arrAccountKey`** může být buď primární, nebo sekundární klíč.

## <a name="link-storage-accounts"></a>Propojit účty úložiště

Tento článek vysvětluje, jak propojit účty úložiště s účtem vzdáleného vykreslování. Pokud je propojen účet úložiště, není nutné vygenerovat identifikátor URI SAS pokaždé, když chcete pracovat s daty ve vašem účtu, například při načítání modelu. Místo toho můžete použít názvy účtů úložiště přímo, jak je popsáno v [části načtení modelu](../concepts/models.md#loading-models).

Kroky v tomto odstavci je třeba provést pro každý účet úložiště, který by měl používat tuto metodu přístupu. Pokud jste ještě nevytvořili účty úložiště, můžete si projít příslušný krok v části [převedení modelu pro rychlý Start vykreslování](../quickstarts/convert-model.md#storage-account-creation).

Nyní se předpokládá, že máte účet úložiště. Přejděte na portál na účet úložiště a přejděte na kartu **Access Control (IAM)** pro tento účet úložiště:

![Úložiště IAM účtu úložiště](./media/azure-storage-account.png)

Ujistěte se, že máte oprávnění vlastníka pro tento účet úložiště, abyste měli jistotu, že můžete přidat přiřazení rolí. Pokud nemáte přístup, bude možnost **Přidat přiřazení role** zakázaná.

Kliknutím na tlačítko **Přidat** na dlaždici přidat přiřazení role přidejte roli.

![Účet úložiště IAM – přidání přiřazení role](./media/azure-add-role-assignment.png)

* Přiřaďte roli **Přispěvatel dat objektu BLOB úložiště** , jak je znázorněno na snímku obrazovky výše.
* Vyberte spravovanou identitu přiřazenou systémem **účtu vzdáleného vykreslování**  z rozevíracího seznamu **přiřadit přístup k** .
* V posledních rozevíracích seznamech vyberte své předplatné a účet vzdáleného vykreslování.
* Kliknutím na Uložit uložte změny.

> [!WARNING]
> V případě, že váš účet vzdáleného vykreslování není uveden, přečtěte si [téma věnované tomuto problému](../resources/troubleshoot.md#cant-link-storage-account-to-arr-account).

> [!IMPORTANT]
> Přiřazení rolí Azure se Azure Storage ukládá do mezipaměti, takže může trvat až 30 minut, než udělíte přístup k účtu vzdáleného vykreslování a když ho můžete použít pro přístup k účtu úložiště. Podrobnosti najdete v [dokumentaci řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/troubleshooting.md#role-assignment-changes-are-not-being-detected) .

## <a name="next-steps"></a>Další kroky

* [Authentication](authentication.md)
* [Použití rozhraní API Azure front-endu k ověřování](frontend-apis.md)
* [Ukázkové skripty PowerShellu](../samples/powershell-example-scripts.md)