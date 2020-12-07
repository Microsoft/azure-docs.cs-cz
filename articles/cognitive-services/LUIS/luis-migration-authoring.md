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
ms.date: 12/07/2020
ms.openlocfilehash: 243c9834aa256e26d620c00ac0fa7a262919aabd
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96762637"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migrace na klíč pro vytváření prostředků Azure

> [!IMPORTANT]
>  Od 3. prosince musí stávající LUIS uživatelé dokončit proces migrace, aby mohli pokračovat v vytváření aplikací LUIS.

Language Understanding (LUIS) pro ověřování se změnila z e-mailového účtu na prostředek Azure. V tomto článku se dozvíte, jak migrovat účet, pokud jste ještě nemigrovali.  


## <a name="what-is-migration"></a>Co je migrace?

Migrace je proces změny vytváření ověřování z e-mailového účtu na prostředek Azure. Po dokončení migrace bude váš účet propojený s předplatným Azure a prostředkem pro vytváření prostředků Azure. *Všichni uživatelé LUIS (vlastníci aplikací a spolupracovníci) nakonec budou muset migrovat.*

Migraci je potřeba provést z [portálu Luis](https://www.luis.ai). Pokud vytvoříte vytváření klíčů pomocí rozhraní příkazového řádku LUIS, například budete muset proces migrace dokončit na portálu LUIS. Po migraci můžete i nadále mít spoluautory svých aplikací, ale budou přidány na úrovni prostředků Azure místo na úrovni aplikace.

> [!Note]
> Před migrací se na úrovni aplikace LUIS označují spoluautori jako _spolupracovníci_ . Po migraci se role Azure _přispěvatele_ používá pro stejné funkce na úrovni prostředků Azure.

## <a name="notes-before-you-migrate"></a>Poznámky před migrací

* Migraci nejde vrátit zpět.
* Pokud jste přihlášeni k více než jednomu [Luis oblastnímu portálu](./luis-reference-regions.md#luis-authoring-regions), budete požádáni o migraci v několika oblastech najednou.
* Aplikace se automaticky migrují s vámi, pokud jste vlastníkem aplikace.
* Vlastník nemůže zvolit podmnožinu aplikací k migraci a proces nebude vratný.
* Po migraci vlastníka zmizí aplikace z účtu spolupracovníka.
* Vlastníci jsou vyzváni k odeslání e-mailu spolupracovníkům, kteří je informují o migraci.
* Pokud jste spolupracujete na aplikaci, nebudou aplikace migrovány s vámi. Spolupracovníci budou ale vyzváni k exportu aplikací, které potřebují.
* Neexistuje žádný způsob, jak by měl vlastník zjistit, jestli se spolupracovníci migrovali.
* Migrace nepřesouvá automaticky ani nepřidává spolupracovníky do prostředku pro vytváření obsahu Azure. Vlastníkem aplikace je ten, který po migraci potřebuje dokončit tento krok. Tento krok vyžaduje [oprávnění k prostředku pro vytváření obsahu Azure](./luis-how-to-collaborate.md).
* Po přiřazení spolupracovníků k prostředku Azure bude nutné provést migraci, aby mohli získat přístup k aplikacím. V opačném případě nebudou mít přístup k vytváření aplikací.
* Migrovaného uživatele nelze přidat jako spolupracovníka aplikace.


> [!Note]
> Pokud potřebujete vytvořit předpředpověď běhového prostředku, je vhodné vytvořit [samostatný proces](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) .

## <a name="migration-prerequisites"></a>Předpoklady migrace

* Musíte být přidruženi k platnému předplatnému Azure. Požádejte správce tenanta, aby vás přidal do předplatného, nebo si [Zaregistrujte bezplatný](https://azure.microsoft.com/free/cognitive-services)účet.
* Musíte vytvořit LUIS prostředek pro vytváření prostředků Azure z portálu LUIS nebo z [Azure Portal](https://portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne). Vytvoření prostředku pro vytváření obsahu z portálu LUIS je součástí procesu migrace, který je popsaný v následující části.
* Pokud jste spolupracujete na aplikacích, aplikace se automaticky nemigrují. Při přechodu na průběh migrace se zobrazí výzva k exportu těchto aplikací. Můžete také použít [rozhraní API pro export](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40). Po migraci můžete aplikaci importovat zpátky do LUIS. Proces importu vytvoří novou aplikaci s novým ID aplikace, pro kterou jste vlastníkem.
* Pokud jste vlastníkem aplikace, nebudete muset exportovat své aplikace, protože se migrují automaticky. K dispozici je e-mailová šablona se seznamem všech spolupracovníků pro jednotlivé aplikace, aby se mohla zobrazit oznámení o procesu migrace.

> [!Note]
> Vytváření aplikací LUIS je zadarmo, jak je uvedeno ve vrstvě F0. Přečtěte si [Další informace o cenových úrovních](luis-limits.md#key-limits).


## <a name="migration-steps"></a>Kroky migrace

1. Když se přihlašujete k [portálu Luis](https://www.luis.ai), otevře se okno migrace Azure s postupem migrace. Pokud ho zavřete, nebudete moct pokračovat ve vytváření aplikací LUIS a jediná zobrazená akce bude pokračovat v migraci.

    > [!div class="mx-imgBorder"]
    > ![Úvod do okna migrace](./media/migrate-authoring-key/notify-azure-migration.png)

2. Pokud máte spolupracovníky na všech aplikacích, zobrazí se seznam názvů aplikací, které vlastníte, a také oblast pro vytváření obsahu a e-maily spolupracovníky na každé aplikaci. Vašim spolupracovníkům doporučujeme poslat e-mail s upozorněním na migraci kliknutím na tlačítko **Odeslat** symbol nalevo od názvu aplikace.
`*`Symbol se zobrazí vedle názvu aplikace v případě, že spolupracovníka má k vaší aplikaci přiřazený prostředek předpovědi. Po migraci budou mít tyto aplikace i nadále tyto prostředky předpovědi přiřazené, i když spolupracovníci nebudou mít přístup k vytváření aplikací. Toto přiřazení se ale bude rušit, pokud vlastník prostředku předpovědi [znovu vygeneroval klíče](./luis-how-to-azure-subscription.md#regenerate-an-azure-key) z Azure Portal.  

   > [!div class="mx-imgBorder"]
   > ![Oznamovat spolupracovníky](./media/migrate-authoring-key/notify-azure-migration-collabs.png)


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

3. Pokud jste spolupracovníka u všech aplikací, zobrazí se spolu s oblastí pro vytváření obsahu a e-maily vlastníků v každé aplikaci seznam názvů aplikací, které jsou v něm sdílené. Je doporučeno exportovat kopii aplikací kliknutím na tlačítko Exportovat nalevo od názvu aplikace. Tyto aplikace můžete po migraci importovat zpátky, protože se s vámi nemigrují automaticky.
`*`Symbol se zobrazí vedle názvu aplikace v případě, že máte k aplikaci přiřazený prostředek předpovědi. Po migraci se k těmto aplikacím pořád přiřadí váš předpovědní prostředek, i když už nebudete mít přístup k vytváření těchto aplikací. Pokud chcete zrušit přiřazení mezi prostředkem předpovědi a aplikací, musíte přejít na Azure Portal a [znovu vygenerovat klíče](./luis-how-to-azure-subscription.md#regenerate-an-azure-key).

   > [!div class="mx-imgBorder"]
   > ![Exportujte aplikace.](./media/migrate-authoring-key/migration-export-apps.png)


4. V okně pro migraci oblastí budete požádáni o migraci vašich aplikací do prostředku Azure ve stejné oblasti, ve které byly vytvořeny. LUIS má tři oblasti vytváření obsahu [a portálů](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions). V okně se zobrazí oblasti, ve kterých byly vytvořené vlastní aplikace. Zobrazené oblasti migrace se můžou lišit v závislosti na místním portálu, který používáte, a aplikacích, které jste vytvořili. 

   > [!div class="mx-imgBorder"]
   > ![Migrace s více oblastmi.](./media/migrate-authoring-key/migration-regional-flow.png)

5. Pro každou oblast vyberte možnost vytvořit nový prostředek pro vytváření obsahu LUIS nebo migrovat na existující prostředek pomocí tlačítek.

   > [!div class="mx-imgBorder"]
   > ![zvolit vytvoření nebo existující prostředek pro vytváření obsahu](./media/migrate-authoring-key/migration-multiregional-resource.png)

   Zadejte tyto informace:

   * **Název tenanta**: tenant, ke kterému je přidružené předplatné Azure. Ve výchozím nastavení se tato hodnota nastavuje na tenanta, kterého aktuálně používáte. Klienty můžete přepínat zavřením tohoto okna a kliknutím na miniaturu v pravém horním rohu obrazovky, která obsahuje vaše iniciály. Kliknutím na **migrovat do Azure** okno znovu otevřete.
   * **Název předplatného Azure**: předplatné, které bude přidruženo k prostředku. Pokud máte více než jedno předplatné, které patří do vašeho tenanta, vyberte v rozevíracím seznamu požadovanou položku.
   * **Název prostředku pro vytváření**: vlastní název, který si zvolíte. Používá se jako součást adresy URL pro dotazy na koncový bod vytváření a předpovědi. Pokud vytváříte nový prostředek pro vytváření obsahu, Všimněte si, že název prostředku může obsahovat jenom alfanumerické znaky, `-` a nemůže začínat ani končit `-` . Pokud název obsahuje nějaké jiné symboly, vytvoření prostředku a migrace se nezdaří.
   * **Název skupiny prostředků Azure**: název vlastní skupiny prostředků, který zvolíte v rozevíracím seznamu. Skupiny prostředků umožňují seskupit prostředky Azure pro přístup a správu. Pokud v tomto předplatném nemáte skupinu prostředků, nebudete ji moct vytvořit na portálu LUIS. Přejděte na [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) a vytvořte si ho tak, že přejdete na Luis a budete pokračovat v procesu přihlašování.

6. Po úspěšné migraci všech oblastí klikněte na Dokončit. Teď budete mít přístup k vašim aplikacím. Můžete pokračovat ve vytváření a údržbě všech aplikací ve všech oblastech na portálu.


## <a name="using-apps-after-migration"></a>Používání aplikací po migraci

Po dokončení procesu migrace se teď všechny vaše aplikace LUIS, pro které jste vlastníkem, přiřadí k jednomu prostředku pro vytváření LUIS.
V seznamu **Moje aplikace** se zobrazí aplikace migrované do nového prostředku pro vytváření. Než budete mít přístup k aplikacím, vyberte **zvolit jiný prostředek pro vytváření obsahu** a vyberte si předplatné a prostředek pro vytváření obsahu, abyste mohli zobrazit aplikace, které se dají vytvářet.

> [!div class="mx-imgBorder"]
> ![Vybrat odběr a prostředek pro vytváření](./media/migrate-authoring-key/select-sub-and-resource.png)


Pokud plánujete upravit aplikace programově, budete potřebovat hodnoty pro vytváření kódu. Tyto hodnoty se zobrazí tak, že v horní části obrazovky na portálu LUIS kliknete na **Spravovat** a pak vyberete **prostředky Azure**. Jsou také k dispozici v Azure Portal na stránce **klíč a koncové body** prostředku. Můžete také vytvořit další prostředky pro vytváření obsahu a přiřadit je ze stejné stránky.

## <a name="adding-contributors-to-authoring-resources"></a>Přidávání přispěvatelů k vytváření prostředků

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Naučte [se, jak přidat přispěvatele](luis-how-to-collaborate.md) do prostředku pro vytváření obsahu. Přispěvatelé budou mít přístup ke všem aplikacím v rámci daného prostředku.

Přispěvatele můžete přidat do prostředku vytváření z Azure Portal na stránce **Access Control (IAM)** daného prostředku. Další informace najdete v tématu [Přidání přispěvatelů do aplikace](luis-how-to-collaborate.md).

> [!Note]
> Pokud vlastník aplikace LUIS migrovali a přidal spolupracovníka jako přispěvatele na prostředku Azure, spolupracovníka nebude mít k této aplikaci k dispozici žádný přístup, pokud se ani nepřenáší.


## <a name="troubleshooting-the-migration-process"></a>Řešení potíží s procesem migrace

Pokud v rozevíracím seznamu nemůžete najít své předplatné Azure:
* Ujistěte se, že máte platné předplatné Azure, které je autorizováno pro vytváření Cognitive Servicesch prostředků. Přejdete na [Azure Portal](https://ms.portal.azure.com) a zkontrolujete stav předplatného. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet Azure](https://azure.microsoft.com/free/cognitive-services/).
* Ujistěte se, že jste v rámci správného tenanta přidruženého k vašemu platnému předplatnému. Klienty můžete přepnout výběrem miniatury v pravém horním rohu obrazovky, která obsahuje vaše iniciály.

  > [!div class="mx-imgBorder"]
  > ![Stránka pro přepínání adresářů](./media/migrate-authoring-key/switch-directories.png)

Pokud máte existující prostředek pro vytváření obsahu, ale nemůžete ho najít, když vyberete možnost **použít existující prostředek pro vytváření obsahu** :
* Váš prostředek byl pravděpodobně vytvořen v jiné oblasti než ta, ve které se pokoušíte migrovat.
* Místo toho vytvořte nový prostředek z portálu LUIS.

Pokud vyberete možnost **vytvořit nový prostředek pro vytváření obsahu** a migrace se nezdařila, zobrazí se chybová zpráva s informací o tom, že se nepodařilo načíst informace o Azure uživatele, opakovat akci později:
* Vaše předplatné může mít 10 nebo více prostředků pro vytváření obsahu v jednotlivých oblastech na základě předplatného. V takovém případě nebudete moct vytvořit nový prostředek pro vytváření.
* Migrujte tak, že vyberete možnost **použít existující prostředek pro vytváření obsahu** a vyberete jeden z existujících prostředků v rámci vašeho předplatného.

## <a name="create-new-support-request"></a>Vytvořit novou žádost o podporu

Pokud máte v rámci migrace nějaké problémy, které nejsou popsané v části řešení potíží, [vytvořte prosím téma podpory](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) a zadejte následující informace s následujícími poli:

   * **Typ problému**: technický
   * **Předplatné**: vyberte předplatné z rozevíracího seznamu.
   * **Služba**: vyhledejte a vyberte "Cognitive Services"
   * **Prostředek**: Vyberte prostředek Luis, pokud existuje nějaký existující. Pokud ne, vyberte Obecné otázky.

## <a name="next-steps"></a>Další kroky

* Přečtěte si [koncepty o vytváření a klíčích modulu runtime](luis-how-to-azure-subscription.md)
* Přečtěte si, jak [přiřadit klíče](luis-how-to-azure-subscription.md) a [Přidat přispěvatele](luis-how-to-collaborate.md) .
