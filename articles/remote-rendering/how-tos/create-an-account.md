---
title: Vytvoření účtu vzdáleného vykreslování Azure
description: Popisuje kroky k vytvoření účtu pro vzdálené vykreslování Azure
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: b9b72fb9e80c588eb3e6642d0228bffa50b35c6e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681295"
---
# <a name="create-an-azure-remote-rendering-account"></a>Vytvoření účtu vzdáleného vykreslování Azure

Tato kapitola vás provede kroky k vytvoření účtu pro službu **Vzdálené vykreslování Azure.** Platný účet je povinný pro dokončení některého z rychlých startů nebo kurzů.

## <a name="create-an-account"></a>Vytvoření účtu

K vytvoření účtu pro službu Vzdálené vykreslování Azure jsou potřeba následující kroky:

1. Přejít na [stránku Náhled smíšené reality](https://aka.ms/MixedRealityPrivatePreview)
1. Klikněte na tlačítko Vytvořit zdroj.
1. Do vyhledávacího pole ("Hledat na trhu") zadejte "Vzdálené vykreslování" a stiskněte "enter".
1. V seznamu výsledků klikněte na dlaždici "Vzdálené vykreslování"
1. Na další obrazovce klikněte na tlačítko "Vytvořit". Otevře se formulář pro vytvoření nového účtu vzdáleného vykreslování:
    1. Nastavit název zdroje na název účtu
    1. V případě potřeby aktualizujte "Předplatné"
    1. Nastavení skupiny prostředků na skupinu prostředků podle vašeho výběru
1. Po vytvoření účtu přejděte na něj a:
    1. Na kartě *Přehled* si poznamenejte id účtu.
    1. Na kartě *Nastavení > přístupové klíče* si všimněte "Primární klíč" – toto je klíč tajného účtu účtu

### <a name="retrieve-the-account-information"></a>Načtení informací o účtu

Ukázky a kurzy vyžadují, abyste zadali ID účtu a klíč. Například v souboru **arrconfig.json,** který se používá pro ukázkové skripty prostředí PowerShell:

```json
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure portal>",
        "arrAccountKey": "<fill in the account key from the Azure portal>",
        "region": "<select from available regions>"
    },
```

Podívejte se na [seznam dostupných oblastí](../reference/regions.md) pro vyplnění možnosti *oblasti.*

Hodnoty pro **`arrAccountId`** **`arrAccountKey`** a lze nalézt na portálu, jak je popsáno v následujících krocích:

* Přejděte na web [Azure Portal](https://www.portal.azure.com).
* Najděte svůj **"Vzdálený vykreslování účet"** - to by mělo být v **seznamu "Nedávné zdroje".** Můžete ji také vyhledat na vyhledávacím panelu v horní části. V takovém případě se ujistěte, že odběr, který chcete použít, je vybrán ve filtru výchozí odběr (ikona filtru vedle vyhledávacího panelu):

![Filtr předplatného](./media/azure-subscription-filter.png)

Kliknutím na účet se dostanete na tuto obrazovku, která se ihned zobrazí **ID účtu:**

![ID účtu Azure](./media/azure-account-id.png)

Pro klávesu vyberte **přístupové klávesy** v panelu vlevo. Na další stránce se zobrazí primární a sekundární klíč:

![Přístupové klíče Azure](./media/azure-account-primary-key.png)

Hodnota pro **`arrAccountKey`** může být primární nebo sekundární klíč.

## <a name="link-storage-accounts"></a>Propojit účty úložiště

Tento odstavec vysvětluje, jak propojit účty úložiště s účtem vzdáleného vykreslování. Když je účet úložiště propojen, není nutné generovat identifikátor URI SAS pokaždé, když chcete pracovat s daty ve vašem účtu, například při načítání modelu. Místo toho můžete použít názvy účtů úložiště přímo, jak je popsáno v [části načítání modelu](../concepts/models.md#loading-models).

Kroky v tomto odstavci musí být provedeny pro každý účet úložiště, který by měl používat tuto alternativní metodu přístupu. Pokud jste ještě nevytvořili účty úložiště, můžete projít příslušný krok v [převést model pro vykreslování rychlý start](../quickstarts/convert-model.md#storage-account-creation).

Nyní se předpokládá, že máte účet úložiště. Přejděte na účet úložiště na portálu a přejděte na kartu **Řízení přístupu (IAM)** pro tento účet úložiště:

![Účet úložiště IAM](./media/azure-storage-account.png)

 Ujistěte se, že máte oprávnění vlastníka k tomuto účtu úložiště, abyste zajistili, že můžete přidávat přiřazení rolí. Pokud nemáte přístup, možnost **Přidat přiřazení role** bude zakázána.

 Je třeba přidat tři odlišné role, jak je popsáno v dalších krocích. Pokud nezadáte všechny tři úrovně přístupu, budou při pokusu o přístup k účtu úložiště problémy s oprávněním.

 Kliknutím na tlačítko **Přidat** na dlaždici "Přidat přiřazení role" přidáte první roli:

![Účet úložiště IAM](./media/azure-add-role-assignment.png)

* První role, kterou chcete přiřadit, je **Owner,** jak je znázorněno na obrázku výše. 
* V rozevíracím souboru ***Přiřadit přístup k** rozevíracímu souboru vyberte účet **vzdáleného vykreslování.**
* V yberte předplatné a účet vzdáleného vykreslování v posledních rozevíracích položkách.

Opakování přidání nových rolí ještě dvakrát pro příslušné výběry z rozevíracího **deníku Role:**
* **Přispěvatel účtů úložiště**
* **Přispěvatel dat objektu blob úložiště**

Ostatní rozevírací položky jsou vybrány jako v prvním kroku.

Pokud jste přidali všechny tři role, váš účet vzdáleného vykreslování Azure má přístup k vašemu účtu úložiště pomocí systému přiřazených identit spravovaných služeb.

## <a name="next-steps"></a>Další kroky

* [Authentication](authentication.md)
* [Použití api pro front-end Azure pro ověřování](frontend-apis.md)
* [Příklad skriptů prostředí PowerShell](../samples/powershell-example-scripts.md)
