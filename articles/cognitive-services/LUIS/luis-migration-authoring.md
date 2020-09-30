---
title: Migrace na klíč pro vytváření prostředků Azure
titleSuffix: Azure Cognitive Services
description: Tento článek popisuje, jak migrovat Language Understanding (LUIS) pro ověřování při vytváření z e-mailového účtu na prostředek Azure.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 926b79e672c14249ec7c2b053dba7eb3a31443a7
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91536035"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrace na klíč pro vytváření prostředků Azure

Language Understanding (LUIS) ověřování při vytváření se změnilo z e-mailového účtu na prostředek Azure. I když to není v současnosti nutné, bude v budoucnu vynutilo přepnutí do prostředku Azure.


## <a name="what-is-migration"></a>Co je migrace?

Migrace je proces změny vytváření ověřování z e-mailového účtu na prostředek Azure. Po dokončení migrace bude váš účet propojený s předplatným Azure a prostředkem pro vytváření prostředků Azure. *Všichni LUIS uživatelé (vlastníci nebo spolupracovníci) nakonec budou muset migrovat.*

Migraci je potřeba provést z portálu LUIS. Pokud vytvoříte vytváření klíčů pomocí rozhraní příkazového řádku LUIS, například budete muset proces migrace dokončit na portálu LUIS. Po migraci můžete i nadále mít spoluautory svých aplikací, ale přidají se na úrovni prostředků Azure místo na úrovni aplikace.

> [!Note]
> Před migrací se na úrovni aplikace LUIS označují spoluautori jako _spolupracovníci_ . Po migraci se role Azure _přispěvatele_ používá pro stejné funkce na úrovni prostředků Azure.

## <a name="note-before-you-migrate"></a>Poznámka před migrací

* Prostředí pro vytváření obsahu je třeba migrovat do **2. listopadu 2020**. 
* Migrace je jednosměrný proces. Po dokončení migrace se nemůžete vrátit.
* Aplikace se automaticky migrují s vámi, pokud jste vlastníkem aplikace.
* Vlastník nemůže zvolit podmnožinu aplikací k migraci a proces nebude vratný.
* Po migraci vlastníka zmizí aplikace na straně spolupracovníka.
* Vlastníci jsou vyzváni k odeslání e-mailu spolupracovníkům, kteří je informují o migraci.
* Pokud jste spolupracujete na aplikaci, nebudou aplikace migrovány s vámi.
* Neexistuje žádný způsob, jak vlastníkovi sdělit, že spolupracovníci migrovali.
* Migrace neshromažďuje automaticky spolupracovníky a nepřesouvá je ani nepřidává do prostředku pro vytváření obsahu Azure. Vlastníkem aplikace je ten, který po migraci potřebuje dokončit tento krok. Tento krok vyžaduje [oprávnění k prostředku pro vytváření obsahu Azure](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate).
* Po přiřazení spolupracovníků k prostředku Azure musí migrovat na přístup k aplikacím. V opačném případě nebudou mít přístup k vytváření aplikací.
* Migrovaného uživatele nelze přidat jako spolupracovníka aplikace.
* Pokud vlastníte klíč předpovědi, který je přiřazený k aplikacím vlastněných jiným uživatelem, bude zablokovaná migrace pro vlastníka i spolupracovníky. Viz doporučení dále v tomto článku.

> [!Note]
> Pokud potřebujete vytvořit předpředpověď běhového prostředku, je vhodné vytvořit [samostatný proces](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) .

## <a name="migration-prerequisites"></a>Předpoklady migrace

* Musíte být přidruženi k platnému předplatnému Azure. Požádejte správce tenanta, aby vás přidal do předplatného, nebo si [Zaregistrujte bezplatný](https://azure.microsoft.com/free/cognitive-services)účet.
* Musíte vytvořit LUIS prostředek pro vytváření prostředků Azure z portálu LUIS nebo z Azure Portal. Vytváření prostředků pro vytváření obsahu z portálu LUIS je součástí toku migrace, který je popsaný v následující části.
* Pokud jste spolupracujete na aplikacích, aplikace se automaticky nemigrují. Doporučujeme, abyste tyto aplikace zálohovali exportováním nebo pomocí [rozhraní API pro export](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40). Po migraci můžete aplikaci importovat zpátky do LUIS. Proces importu vytvoří novou aplikaci s novým ID aplikace, pro kterou jste vlastníkem.
* Pokud jste vlastníkem aplikace, nebudete muset exportovat své aplikace, protože se migrují automaticky. Doporučujeme, abyste si uložili seznam spolupracovníků jednotlivých aplikací. E-mailová šablona, která má tento seznam, je v rámci procesu migrace volitelně k dispozici.


|Portál|Účel|
|--|--|
|[Azure](https://azure.microsoft.com/free/cognitive-services)| Vytvořte předpověď a vytváření prostředků.<br> Přiřaďte přispěvatele k prostředkům.|
|[LUIS](https://www.luis.ai)| Migrujte na nové prostředky pro vytváření obsahu.<br> V toku migrace vytvořte nové prostředky pro vytváření obsahu.<br> Přiřaďte nebo zrušte přiřazení předpovědí a vytváření prostředků aplikacím ze stránky **Správa**  >  **prostředků Azure** . <br> Přesuňte aplikace z jednoho zdroje pro vytváření do jiného.  |

> [!Note]
> Vytváření aplikací LUIS je zadarmo, jak je uvedeno ve vrstvě F0. Přečtěte si [Další informace o cenových úrovních](luis-limits.md#key-limits).


## <a name="migration-steps"></a>Kroky migrace

1. Na portálu LUIS, na kterém pracujete, můžete zahájit proces migrace z ikony **Azure** na horním panelu nástrojů.

   > [!div class="mx-imgBorder"]
   > ![Ikona migrace](./media/migrate-authoring-key/migration-button.png)

2. Automaticky otevírané okno migrace vám umožní pokračovat v migraci nebo migrovat později. Vyberte **migrovat nyní**.

   > [!div class="mx-imgBorder"]
   > ![První automaticky otevírané okno v procesu migrace, kde vyberete migrovat hned](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. Pokud má kterákoli z vašich aplikací spolupracovníky, budete vyzváni k odeslání e-mailu, který jim umožní znát migraci. Toto je volitelný krok.

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

4. Pokud jste spolupracujete na libovolné aplikaci, budete vyzváni k exportu kopie aplikací výběrem této možnosti během procesu migrace. Toto je volitelný krok.

   Pokud vyberete možnost, zobrazí se následující stránka. Vyberte tlačítka pro stažení vlevo a exportujte aplikace, které chcete. Tyto aplikace můžete po migraci importovat zpátky, protože se s vámi nemigrují automaticky.

   > [!div class="mx-imgBorder"]
   > ![Vyzvat k exportu aplikací](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. Můžete vytvořit nový prostředek pro vytváření obsahu LUIS nebo migrovat na existující prostředek pro vytváření obsahu, pokud jste ho už vytvořili z Azure. Vyberte jednu z následujících tlačítek a zvolte požadovanou možnost.

   > [!div class="mx-imgBorder"]
   > ![Tlačítka pro vytvoření nového prostředku pro vytváření obsahu a používání existujícího prostředku pro vytváření](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>Vytvořit nový prostředek pro vytváření obsahu z LUIS k migraci

Chcete-li vytvořit nový prostředek pro vytváření obsahu, vyberte možnost **vytvořit nový prostředek pro vytváření obsahu** a v dalším okně zadejte následující informace. Potom vyberte **Done** (Hotovo).

> [!div class="mx-imgBorder"]
> ![Okno pro vytvoření prostředku pro vytváření obsahu](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **Název tenanta**: tenant, ke kterému je přidružené předplatné Azure. Ve výchozím nastavení se jedná o tenanta, kterého aktuálně používáte. Klienty můžete přepnout výběrem miniatury vpravo, která obsahuje vaše iniciály.
* **Název prostředku**: vlastní název, který si zvolíte. Používá se jako součást adresy URL pro dotazy na koncový bod vytváření a předpovědi.
* **Název předplatného**: předplatné, které bude přidruženo k prostředku. Pokud máte více než jedno předplatné, které patří do vašeho tenanta, vyberte v rozevíracím seznamu požadovanou položku.
* **Název skupiny prostředků Azure**: název vlastní skupiny prostředků, který zvolíte v rozevíracím seznamu. Skupiny prostředků umožňují seskupit prostředky Azure pro přístup a správu.

Všimněte si, že můžete mít 10 volných prostředků pro vytváření na jednotlivé oblasti a na předplatné. Pokud má vaše předplatné více než 10 zdrojů vytváření ve stejné oblasti, nebudete moct vytvořit nový.

Po vytvoření prostředku pro vytváření se zobrazí zpráva o úspěchu. Kliknutím na **Zavřít** zavřete automaticky otevírané okno.

  > [!div class="mx-imgBorder"]
  > ![Zpráva, která indikuje, že váš prostředek pro vytváření obsahu byl úspěšně vytvořen.](./media/migrate-authoring-key/migration-success-2.png)


### <a name="use-existing-authoring-resource-to-migrate"></a>K migraci použít existující prostředek pro vytváření obsahu

Pokud je vaše předplatné už přidružené k LUIS vytváření prostředků Azure, nebo pokud jste prostředek vytvořili z Azure Portal a chcete na něj migrovat místo vytvoření nové, vyberte **použít existující prostředek pro vytváření**. V dalším okně zadejte následující informace a potom vyberte **Hotovo**.

> [!div class="mx-imgBorder"]
>![Okno pro změnu existujícího prostředku pro vytváření](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **Název tenanta**: tenant, ke kterému je přidružené předplatné Azure. Ve výchozím nastavení se jedná o tenanta, kterého aktuálně používáte.
* **Název předplatného**: předplatné, které bude přidruženo k prostředku. Pokud máte více než jedno předplatné, které patří do vašeho tenanta, vyberte v rozevíracím seznamu požadovanou položku.
* **Název prostředku**: prostředek pro vytváření, do kterého chcete migrovat.

> [!Note]
> Pokud v rozevíracím seznamu nevidíte prostředek pro vytváření obsahu, ujistěte se, že jste ho vytvořili ve správném umístění podle portálu LUIS, ke kterému jste se přihlásili. Také se ujistěte, že je to, co jste vytvořili, je prostředek pro vytváření, nikoli předpověď prostředků.


Ověřte název prostředku pro vytváření a vyberte tlačítko **migrovat** .

> [!div class="mx-imgBorder"]
> ![Okno, ve kterém se dá vybrat prostředek pro vytváření obsahu. teď je dostupné tlačítko migrovat.](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

Zobrazí se zpráva o úspěchu. Kliknutím na **Zavřít** zavřete automaticky otevírané okno.

> [!div class="mx-imgBorder"]
> ![Zpráva oznamující, že váš prostředek pro vytváření obsahu byl úspěšně migrován.](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>Používání aplikací po migraci

Po dokončení procesu migrace se teď všechny vaše aplikace LUIS, pro které jste vlastníkem, přiřadí k jednomu prostředku pro vytváření LUIS.

V seznamu **Moje aplikace** se zobrazí aplikace migrované do nového prostředku pro vytváření. Než budete mít přístup k vašim aplikacím, vyberte prostředek pro vytváření předplatného a LUIS, abyste viděli aplikace, které můžete vytvořit.

 > [!div class="mx-imgBorder"]
 > ![Pole pro odběr a zdroj obsahu](./media/create-app-in-portal-select-subscription-luis-resource.png)

Pokud chcete pokračovat v úpravách vašich aplikací na portálu LUIS, nemusíte znát klíč prostředku pro vytváření obsahu.

Pokud plánujete upravit aplikace programově, budete potřebovat hodnoty pro vytváření kódu. Tyto hodnoty se zobrazí na stránce **Správa**  >  **prostředků Azure** na portálu Luis. Jsou také k dispozici ve Azure Portal na stránce **klíče** prostředku. Můžete také vytvořit další prostředky pro vytváření obsahu a přiřadit je ze stejné stránky.

 > [!div class="mx-imgBorder"]
 > ![Stránka pro správu vytváření prostředků](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="adding-contributors-to-authoring-resources"></a>Přidávání přispěvatelů k vytváření prostředků

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Naučte [se, jak přidat přispěvatele](luis-how-to-collaborate.md) do prostředku pro vytváření obsahu. Přispěvatelé budou mít přístup ke všem aplikacím v rámci daného prostředku.

Přispěvatele můžete přidat do prostředku vytváření z Azure Portal na stránce **Access Control (IAM)** daného prostředku. Další informace najdete v tématu [Přidání přístupu přispěvatele](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource).

> [!Note]
> Pokud vlastník aplikace LUIS migrovali a přidal spolupracovníka jako přispěvatele na prostředku Azure, spolupracovníka nebude mít k této aplikaci k dispozici žádný přístup, pokud se ani nepřenáší.

## <a name="luis-portal-migration-reminders"></a>LUISch připomenutí migrace portálu

[Portál Luis](https://www.luis.ai) poskytuje proces migrace.

Budete vyzváni k migraci, pokud jsou splněny obě tyto podmínky:
* Máte aplikace v systému ověřování e-mailů pro vytváření obsahu.
* Jste vlastníkem aplikace.

Každý týden budete vyzváni k migraci svých aplikací. Toto okno můžete zavřít bez migrace. Pokud chcete provést migraci před dalším naplánovaným obdobím, můžete zahájit proces migrace z ikony **Azure** na horním panelu nástrojů portálu Luis.

## <a name="prediction-resources-blocking-migration"></a>Předpověď prostředků blokující migraci
Vaše migrace nemá negativně vliv na modul runtime aplikace. Při migraci dojde k odebrání všech spolupracovníků z vašich aplikací a Vy jste z jiných aplikací odebrali jako spolupracovníka. Tento proces znamená, že se odeberou také klíče, které spolupracovníci přiřadí, což může způsobit přerušení vaší aplikace, pokud je v produkčním prostředí. To je důvod, proč migraci blokujeme, dokud ručně neodeberete spolupracovníky nebo klíče přiřazené k nim.

Migrace je zablokovaná, pokud je splněna některá z těchto podmínek:

* Přiřadili jste prostředky předpovědi a modulu runtime v aplikacích, které nevlastníte.
* K aplikacím, které vlastníte, přiřadíte prostředky předpovědi a modulu runtime jiným uživatelům.

### <a name="recommended-steps-if-youre-the-owner-of-the-app"></a>Doporučené kroky, pokud jste vlastníkem aplikace
Pokud jste vlastníkem některých aplikací a spolupracovníci mají pro tyto aplikace přiřazený klíč předpovědi/běhu, při migraci se zobrazí chyba. Tato chyba obsahuje seznam ID aplikací, ke kterým jsou přiřazeny předpověď klíčů, které vlastní jiní uživatelé.

Doporučený postup:
* Upozorněte spolupracovníky na migraci.
* Odebere všechny spolupracovníky z aplikací, které jsou uvedené v chybě.
* Absolvujte proces migrace, který by měl být úspěšný v případě ručního odebrání spolupracovníků.
* Přiřaďte spolupracovníky k vašemu novému zdroji pro vytváření. Spolupracovníci budou migrovat a znovu přiřazovat prostředky předpovědi zpátky do aplikací. Všimněte si, že to způsobí dočasné přerušení aplikace, dokud nebudou znovu přiřazeny prostředky předpovědi.

Tady je další možné řešení. Před migrací vlastníka můžou spolupracovníci přidat vlastníky aplikace jako přispěvatele na svých předplatných Azure z Azure Portal. Tento krok udělí vlastníkem přístup k prostředku předpovědi za běhu. Pokud vlastník migruje pomocí nového předplatného, které jste přidali do nástroje (který bude nalezen v rámci nového tenanta), tento krok neodstraní pouze proces migrace pro spolupracovníka i vlastníka aplikace. Bude taky umožňovat bezproblémovou migraci aplikací. stále se jim ještě přiřadí klíč předpovědi, který aplikace nedělí.


### <a name="recommended-steps-if-youre-a-collaborator-on-an-app"></a>Doporučené kroky, pokud jste spolupracujete na aplikaci
Pokud spolupracujete na aplikacích a k těmto aplikacím jste přiřadili klíč předpovědi/běhu, při migraci se zobrazí chyba. Chyba obsahuje seznam ID aplikací a cesty klíčů, které blokují migraci.

Doporučený postup:
* Exportujte aplikace jako zálohu. Tento krok je volitelný v procesu migrace.
* Zrušte přiřazení prostředků předpovědi ze stránky **Správa**  >  **prostředků Azure** .
* Absolvujte proces migrace.
* Po migraci naimportujte zpátky aplikace.
* K vašim aplikacím znovu přiřaďte klíče předpovědi ze stránky **Správa**  >  **prostředků Azure** .

> [!Note]
> Když po migraci importujete aplikace zpátky, budou mít různí ID aplikací. Budou se taky lišit od těch, které jsou v produkčním prostředí. Nyní budete vlastníkem těchto aplikací.

## <a name="troubleshooting-the-migration-process"></a>Řešení potíží s procesem migrace

Při pokusu o migraci, ale nemůžete najít předplatné Azure v rozevíracím seznamu:
* Ujistěte se, že máte platné předplatné Azure, které je autorizováno pro vytváření Cognitive Servicesch prostředků. Přejdete na [Azure Portal](https://ms.portal.azure.com) a zkontrolujete stav předplatného. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet Azure](https://azure.microsoft.com/free/cognitive-services/).
* Ujistěte se, že jste v rámci správného tenanta přidruženého k vašemu platnému předplatnému. Klienty můžete na tomto panelu nástrojů přepnout z miniatury na levou stranu úvodní obrazovky: ![ panel nástrojů, kde můžete přepnout klienty.](./media/migrate-authoring-key/switch-user-tenant-2.png)

Pokud máte existující prostředek pro vytváření obsahu, ale nemůžete ho najít, když vyberete možnost **použít existující prostředek pro vytváření obsahu** :
* Prostředek se pravděpodobně vytvořil v umístění, které se liší od portálu, ke kterému jste se přihlásili. Ověřte [oblasti a portály pro vytváření Luis](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions).
* Místo toho vytvořte nový prostředek z portálu LUIS.

Pokud vyberete možnost **vytvořit nový prostředek pro vytváření obsahu** a migrace se nezdařila, zobrazí se chybová zpráva s informací o tom, že se nepodařilo načíst informace o Azure uživatele, opakovat akci později:
* Vaše předplatné může mít 10 nebo více prostředků pro vytváření obsahu v jednotlivých oblastech na základě předplatného. V takovém případě nebudete moct vytvořit nový prostředek pro vytváření.
* Migrujte tak, že vyberete možnost **použít existující prostředek pro vytváření obsahu** a vyberete jeden z existujících prostředků v rámci vašeho předplatného.

Pokud se zobrazí následující chyba, zkontrolujte [Doporučené kroky, pokud jste vlastníkem aplikace](#recommended-steps-if-youre-the-owner-of-the-app).
![Chyba, která zobrazuje, že migrace se nezdařila pro vlastníky](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

Pokud se zobrazí následující chyba, zkontrolujte [Doporučené kroky, pokud jste](#recommended-steps-if-youre-a-collaborator-on-an-app)spolupracujete na aplikaci.
![Chyba, která zobrazuje, že migrace se pro spolupracovníky nezdařila](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>Další kroky

* Přečtěte si [koncepty o vytváření a klíčích modulu runtime](luis-how-to-azure-subscription.md).
* Přečtěte si, jak [přiřadit klíče](luis-how-to-azure-subscription.md) a [Přidat přispěvatele](luis-how-to-collaborate.md).
