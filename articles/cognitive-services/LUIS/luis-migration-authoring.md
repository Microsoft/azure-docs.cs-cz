---
title: Migrace na prostředek Azure pro vytváření obsahu 2
titleSuffix: Azure Cognitive Services
description: Migrujte na klíč prostředku Azure Authoring 2.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/17/2020
ms.author: diberry
ms.openlocfilehash: 5f0778436db7bd8c3a09e3ba346d8a9a6c4af454
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2020
ms.locfileid: "84983800"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrace na klíč pro vytváření prostředků Azure

Language Understanding (LUIS) ověřování při vytváření se změnilo z e-mailového účtu na prostředek Azure. I když to není v současnosti nutné, bude v budoucnu vynutilo přepnutí do prostředku Azure.


## <a name="what-is-migration"></a>Co je migrace?

Migrace je proces změny vytváření ověřování z e-mailového účtu na prostředek Azure. Po dokončení migrace bude váš účet propojený s předplatným Azure a prostředkem pro vytváření prostředků Azure. **Všichni LUIS uživatelé (vlastníci nebo spolupracovníci) nakonec budou muset migrovat.** Migraci je potřeba provést z portálu LUIS. Pokud vytváříte klíče pro vytváření obsahu, například s rozhraním příkazového řádku LUIS, budete muset i nadále proces migrace dokončit na portálu LUIS. Po migraci můžete i nadále mít spoluautory svých aplikací, ale budou přidány na úrovni prostředků Azure místo na úrovni aplikace.

> [!Note]
> Před migrací se jako _spolupracovníci_ na úrovni aplikace Luis označují spoluautoři. Po migraci se role Azure _přispěvatele_ používá pro stejné funkce, ale na úrovni prostředků Azure.

## <a name="note-before-you-migrate"></a>Poznámka před migrací

* Migrace je **jednosměrný** proces. Po dokončení migrace se nemůžete vrátit.
* Pokud jste **vlastníkem** aplikace, aplikace se **automaticky migrují** s vámi.
* Vlastník nemůže zvolit podmnožinu aplikací k migraci a proces nebude vratný.
* Po **migraci vlastníka**zmizí aplikace na **stranách spolupracovníka** .
* Vlastníci jsou vyzváni k odeslání e-mailu spolupracovníkům, kteří je informují o migraci.
* Pokud jste spolupracujete **na aplikaci** , nebudou aplikace **migrovány** s vámi.
* Neexistuje žádný způsob, jak vlastníkovi sdělit, že jeho spolupracovníci migrovali.
* **Migrace** neshromažďuje automaticky spolupracovníky a nepřesouvá je ani nepřidává do prostředku pro vytváření obsahu Azure. Vlastníkem aplikace je ten, který po migraci potřebuje dokončit tento krok. Tento krok vyžaduje [oprávnění k prostředku pro vytváření obsahu Azure](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate).
* Po přiřazení k prostředku Azure **budou spolupracovníci muset migrovat na přístup k aplikacím**. V opačném případě nebudou mít přístup k vytváření aplikací.
* Migrovaného uživatele nelze přidat jako spolupracovníka aplikace.
* Pokud **vlastníte klíč předpovědi, který je přiřazený k aplikacím vlastněných jiným uživatelem**, bude **zablokovaná migrace** pro vlastníka i spolupracovníky. Doporučení najdete níže.

> [!Note]
> Pokud potřebujete vytvořit předpředpověď běhového prostředku, je vhodné vytvořit [samostatný proces](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) .

## <a name="migration-prerequisites"></a>Předpoklady migrace

* Musíte být přidruženi k platnému předplatnému Azure. Požádejte správce tenanta, aby vás přidal do předplatného, nebo [si ho můžete zaregistrovat zdarma.](https://azure.microsoft.com/free/)
* Musíte vytvořit LUIS prostředek pro vytváření prostředků Azure z portálu LUIS nebo z Azure Portal. Vytváření prostředků pro vytváření obsahu z portálu LUIS je součástí toku migrace, který je popsán v následující části.
* Pokud jste **spolupracovníka u aplikací**, aplikace se automaticky nemigrují. **Doporučujeme zálohovat tyto aplikace** jejich exportem nebo použitím [rozhraní API pro export](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40). Po migraci můžete aplikaci importovat zpátky do LUIS. Proces importu vytvoří novou aplikaci s novým ID aplikace, pro kterou jste vlastníkem.
* Pokud jste **vlastníkem aplikace**, nebudete muset exportovat aplikace, protože se migrují automaticky. Doporučuje se **Uložit seznam collaborator's jednotlivých aplikací.** E-mailová šablona, která má tento seznam, je v rámci procesu migrace volitelně k dispozici.


|Portál|Účel|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Vytvořte předpověď a vytváření prostředků.<br>* Přiřaďte přispěvatele k prostředkům.|
|[LUIS](https://www.luis.ai)|* Migrujte na nové prostředky pro vytváření obsahu.<br>* V toku migrace vytvořte nový prostředek pro vytváření.<br>* Přiřaďte nebo zrušte přiřazení předpovědí a vytváření prostředků aplikacím ze stránky **Spravovat – > prostředky Azure** . <br>* Přesuňte aplikace z jednoho zdroje pro vytváření do jiného.  |

> [!Note]
> **Vytváření aplikací Luis je zadarmo**, které uvádí `F0` vrstva. Přečtěte si [Další informace o cenových úrovních](luis-limits.md#key-limits).


## <a name="migration-steps"></a>Kroky migrace

1. Na portálu LUIS, na kterém pracujete, můžete zahájit proces migrace z ikony **Azure** na horním panelu nástrojů.

   > [!div class="mx-imgBorder"]
   > ![Ikona migrace](./media/migrate-authoring-key/migration-button.png)

2. Automaticky otevírané okno migrace vám umožní pokračovat v migraci nebo migrovat později. Vyberte **migrovat nyní**.

   > [!div class="mx-imgBorder"]
   > ![První automaticky otevírané okno v procesu migrace vyberte migrovat nyní.](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. Případně, pokud má kterákoli z vašich aplikací spolupracovníky, budete vyzváni k **odeslání e-mailu s oznámením** o migraci. Toto je volitelný krok.

   U každého spolupracovníka a aplikace se výchozí e-mailová aplikace otevře s lehce formátovaným e-mailem. E-mail můžete před odesláním upravit. Šablona e-mailu obsahuje přesné ID aplikace a název aplikace.
   
   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources
      
   Thank you
   ```
   
   > [!Note]
   > Po dokončení migrace účtu do Azure už nebudou vaše aplikace k dispozici pro spolupracovníky.

4. Pokud jste spolupracujete na libovolné aplikaci, budete vyzváni k **exportu kopie aplikací** výběrem této možnosti během procesu migrace. Po výběru této možnosti se zobrazí stránka níže, kde na levé straně kliknete na tlačítko Stáhnout a vyexportujete požadované aplikace. Tyto aplikace můžete po migraci importovat zpátky, protože se s vámi automaticky nemigrují. Toto je volitelný krok.

   > [!div class="mx-imgBorder"]
   > ![Vyzvat k exportu aplikace](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. Můžete vytvořit nový prostředek pro vytváření obsahu LUIS nebo migrovat na existující prostředek pro vytváření obsahu, pokud jste ho už vytvořili z Azure. Zvolte požadované možnosti výběrem tlačítka v následujícím seznamu.

   > [!div class="mx-imgBorder"]
   > ![Vytvořit prostředek pro vytváření](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>Vytvořit nový prostředek pro vytváření obsahu z LUIS k migraci

Chcete-li vytvořit nový prostředek pro vytváření obsahu, vyberte možnost **vytvořit nový prostředek pro vytváření obsahu** a v dalším okně zadejte následující informace.

> [!div class="mx-imgBorder"]
> ![Vytvořit prostředek pro vytváření](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **Název prostředku** – vlastní název, který zvolíte, se používá jako součást adresy URL pro dotazy na koncový bod vytváření a předpovědi.
* **Název předplatného** – předplatné, které bude přidruženo k prostředku. Pokud máte více než jedno předplatné, které patří do vašeho tenanta, vyberte v rozevíracím seznamu požadovanou položku.
* **Skupina prostředků** – název vlastní skupiny prostředků, kterou zvolíte v rozevíracím seznamu. Skupiny prostředků umožňují seskupit prostředky Azure pro přístup a správu.
* **Tenant** – tenant, ke kterému je předplatné Azure přidružené. Tato možnost je ve výchozím nastavení nastavená na tenanta, kterého si aktuálně zvolíte. Klienty můžete přepnout tak, že vyberete miniaturu, která obsahuje vaše iniciály.

Až zadáte výše uvedené informace, vyberte **Hotovo**.

Všimněte si, že můžete mít 10 volných prostředků pro vytváření na jednotlivé oblasti a na předplatné. Pokud má vaše předplatné více než 10 zdrojů vytváření ve stejné oblasti, nebudete moct vytvořit nový.

* Po vytvoření prostředku pro vytváření se zobrazí zpráva o úspěchu. Kliknutím na **Zavřít** zavřete automaticky otevírané okno.

  > [!div class="mx-imgBorder"]
  > ![Váš prostředek pro vytváření obsahu se úspěšně vytvořil.](./media/migrate-authoring-key/migration-success-2.png)

### <a name="use-existing-authoring-resource-to-migrate"></a>K migraci použít existující prostředek pro vytváření obsahu

Pokud vaše předplatné už je přidružené k LUIS vytváření prostředků Azure nebo pokud jste ho vytvořili z Azure Portal a chcete na něj migrovat místo vytvoření nového prostředku, vyberte **použít existující prostředek pro vytváření obsahu** a v dalším okně zadejte následující informace.

> [!div class="mx-imgBorder"]
>![Vytvořit prostředek pro vytváření](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **Tenant** – tenant, ke kterému je předplatné Azure přidružené. Tato možnost je ve výchozím nastavení nastavená na tenanta, kterého si aktuálně zvolíte.
* **Název předplatného** – předplatné, které bude přidruženo k prostředku. Pokud máte více než jedno předplatné, které patří do vašeho tenanta, vyberte ho v rozevíracím seznamu.
* **Název prostředku** – vyberte prostředek pro vytváření obsahu, na který chcete migrovat.

> [!Note]
> Pokud se prostředek pro vytváření obsahu v rozevíracím seznamu nezobrazuje, ujistěte se, že jste ho vytvořili ve **správném umístění** podle Luis portálu, ke kterému jste přihlášení. Také se ujistěte, že je v tom, co jste vytvořili, skutečně **prostředek pro vytváření** , a ne **předpověď prostředků**.


* Ověřte název prostředku pro vytváření obsahu a klikněte na tlačítko **migrovat nyní** .

 > [!div class="mx-imgBorder"]
 > ![Vytvořit prostředek pro vytváření](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

* Zobrazí se zpráva o úspěchu. Kliknutím na **Zavřít** zavřete automaticky otevírané okno.

 > [!div class="mx-imgBorder"]
 > ![Váš prostředek pro vytváření obsahu se úspěšně vytvořil.](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>Používání aplikací po migraci

* Po dokončení procesu migrace se teď všechny aplikace LUIS, které jste vlastníky, přiřadí k jednomu prostředku pro vytváření LUIS.
* V seznamu **Moje aplikace** se zobrazí aplikace migrované do nového prostředku pro vytváření.
* Než budete mít přístup k vašim aplikacím, vyberte prostředek pro vytváření předplatného a LUIS, abyste viděli aplikace, které můžete vytvořit.

 > [!div class="mx-imgBorder"]
 > ![Pokud chcete zobrazit aplikace, které může vytvořit, vyberte předplatné a LUIS Authoring Resource.](./media/create-app-in-portal-select-subscription-luis-resource.png)

* Pokud chcete pokračovat v úpravách vašich aplikací na portálu LUIS, nemusíte znát klíč prostředku pro vytváření obsahu.
* Pokud plánujete upravit aplikace programově, budete potřebovat hodnoty pro vytváření kódu. Tyto hodnoty se zobrazí na stránce **Správa prostředků Azure >** na portálu Luis a jsou k dispozici také v Azure Portal na stránce **klíče** prostředku. Můžete také vytvořit další prostředky pro vytváření obsahu a přiřadit je ze stejné stránky.

 > [!div class="mx-imgBorder"]
 > ![Spravovat prostředek pro vytváření.](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="add-contributors-to-authoring-resources"></a>Přidání přispěvatelů k vytváření prostředků

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Naučte [se, jak přidat přispěvatele](luis-how-to-collaborate.md) do prostředku pro vytváření obsahu. Přispěvatelé budou mít přístup ke všem aplikacím v rámci daného prostředku.

Přispěvatele můžete přidat do prostředku vytváření z _Azure Portal_na stránce **Access Control (IAM)** daného prostředku. Další informace najdete v tématu [Přidání přístupu přispěvatele](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource).

> [!Note]
> Pokud vlastník aplikace LUIS migrovali a přidal spolupracovníka jako přispěvatele na prostředku Azure, spolupracovníka nebude mít k této aplikaci k dispozici žádný přístup, pokud se ani nepřenáší.

## <a name="luis-portal-migration-reminders"></a>LUISch připomenutí migrace portálu

[Portál Luis](https://www.luis.ai) poskytuje proces migrace.

Budete vyzváni k migraci, pokud:
* Máte aplikace v systému ověřování e-mailů pro vytváření obsahu.
* A jste vlastníkem aplikace.

Každý týden budete vyzváni k migraci svých aplikací. Toto okno můžete zrušit bez migrace. Pokud chcete provést migraci před dalším naplánovaným obdobím, můžete zahájit proces migrace z ikony **Azure** na horním panelu nástrojů portálu Luis.

Proces migrace můžete odložit zrušením z okna. Pravidelně budete požádáni o migraci, dokud neprovedete migraci nebo neuplyne konečný termín migrace. Proces migrace můžete spustit z ikony zámku horní navigační panel.

## <a name="prediction-resources-blocking-migration"></a>Předpověď prostředků blokující migraci
Vaše migrace negativně ovlivňuje všechny aplikace za běhu. Při migraci se z vašich aplikací odeberou všechny spolupracovníci a Vy jste z jiných aplikací odebrali jako spolupracovníka. Tento proces znamená, že se odeberou také klíče, které spolupracovníci přiřadí, což může způsobit přerušení vaší aplikace, pokud je v produkčním prostředí. To je důvod, proč migraci blokujeme, dokud neodeberete spolupracovníky nebo klíče přiřazené k nim ručně.

### <a name="when-does-prediction-resources-block-migration"></a>Kdy prostředky předpovědi blokují migraci?
* Migrace se zablokuje, pokud jste přiřadili prostředky předpovědi a modulu runtime v aplikacích, které nevlastníte.
* Migrace se zablokuje, pokud jste jiným uživatelům přidělili k aplikacím, které vlastníte, prostředky předpovědi a modulu runtime.

### <a name="recommended-steps-to-do-if-you-are-the-owner-of-the-app"></a>Doporučený postup, pokud jste vlastníkem aplikace
Pokud jste vlastníkem některých aplikací a spolupracovníci přiřadili k těmto aplikacím předpověď/běhový klíč, při migraci se zobrazí chyba s ID aplikací, které mají předpovědi přiřazené k nim patřící jiným uživatelům.

Doporučuje se:
* Upozorněte spolupracovníky na migraci.
* Odebere všechny spolupracovníky z aplikací, které jsou uvedené v chybě.
* Absolvujte proces migrace, který by měl být úspěšný v případě ručního odebrání spolupracovníků.
* Přiřaďte spolupracovníky k vašemu novému zdroji pro vytváření.
* Spolupracovníci mají migrovat a znovu přiřazovat prostředky předpovědi zpátky do aplikací.
Všimněte si, že by to vedlo k dočasnému přerušení aplikace, dokud nebudou prostředky předpovědi znovu přiřazeny.

Další řešení: před migrací vlastníků můžou spolupracovníci přidat vlastníky aplikace jako přispěvatele na svých předplatných Azure z Azure Portal. Tím udělíte přístup vlastníka k prostředku předpovědi za běhu. Pokud vlastník migruje pomocí nového předplatného, ke kterému se přidaly (které se budou vyhledávat v rámci nového tenanta), nebude jenom odblokovat proces migrace pro spolupracovníka i vlastníka aplikace, ale umožní vám plynulou migraci aplikací s klíčovým předpovědí, která je pořád přiřazená k těmto aplikacím.


### <a name="recommended-steps-to-do-if-you-are-a-collaborator-on-an-app"></a>Doporučené kroky, pokud jste spolupracujete na aplikaci, kterou máte spolupracovníka
Pokud spolupracujete na aplikacích a přiřadíte k nim klíč předpovědi/běhu, při migraci se zobrazí chyba, která obsahuje ID aplikace a cesty klíčů, které blokují migraci.

Doporučuje se:
* Exportujte aplikace jako zálohu. Tato operace je k dispozici jako volitelný krok v procesu migrace.
* Zrušte přiřazení prostředků předpovědi ze stránky **spravovat > prostředky Azure** .
* Absolvujte proces migrace.
* Po migraci naimportujte zpátky aplikace.
* Znovu přiřaďte klíč předpovědi vašim aplikacím **Správa – > stránku prostředků Azure** .

> [!Note]
> Když po dokončení migrace naimportujete aplikace zpátky, budou mít různá ID aplikací a budou se lišit od těch, které jsou v produkčním prostředí. Nyní budete vlastníkem těchto aplikací.

## <a name="troubleshooting-migration-process"></a>Řešení potíží s procesem migrace

Při pokusu o migraci, ale nemůžete najít vaše předplatné Azure v rozevíracím seznamu:
* Ujistěte se, že máte platné předplatné Azure, které má autorizaci k vytváření prostředků Cognitive Services. Přejít na [Azure Portal](https://ms.portal.azure.com) a ověřit stav předplatného. Pokud ji nemáte, [vytvořte bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
* Ujistěte se, že jste ve správném tenantovi přidruženém k vašemu platnému předplatnému. Klienty můžete z dalších levých miniatur přepínat na panelu nástrojů níže: ![ Přepnout klienty.](./media/migrate-authoring-key/switch-user-tenant-2.png)

 Pokud už máte prostředek pro vytváření obsahu, ale nemůžete ho najít při výběru možnosti použít existující prostředek pro vytváření obsahu:
* Prostředek byl pravděpodobně vytvořen v jiném umístění než na portálu, ke kterému jste se přihlásili. Zkontrolujte prosím [oblasti vytváření Luis a portály](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions) .
* Místo toho vytvořte nový prostředek z portálu LUIS.

Pokud vyberete možnost vytvořit nový prostředek pro vytváření obsahu a migrace se nepovedla. chybová zpráva: Nepodařilo se načíst informace o uživateli Azure, zkuste to znovu později.
* Vaše předplatné může mít 10 nebo více prostředků pro vytváření obsahu na oblast a předplatné. V takovém případě nebudete moci vytvořit nový prostředek pro vytváření obsahu.
* Migrujte tak, že vyberete možnost použít existující prostředek pro vytváření obsahu a vyberete jeden z existujících prostředků v rámci vašeho předplatného.

Pokud se zobrazí následující chyba, přečtěte si [doporučené kroky k tomu, pokud jste vlastníkem oddílu aplikace] ![ migrace se u vlastníků nezdařila.](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

Pokud se zobrazí následující chyba, Projděte si téma [doporučené kroky k tomu, pokud jste spolupracujete v části aplikace] ![ migrace pro spolupracovníky není úspěšná.](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>Další kroky

* Přečtěte si [Koncepty](luis-concept-keys.md) o vytváření a klíčích modulu runtime
* Přečtěte si, [jak přiřadit klíče](luis-how-to-azure-subscription.md) a přidat [přispěvatele](luis-how-to-collaborate.md) .
