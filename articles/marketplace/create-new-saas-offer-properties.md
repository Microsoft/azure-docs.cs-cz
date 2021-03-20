---
title: Jak nakonfigurovat vlastnosti nabídky SaaS v partnerském centru Microsoftu
description: Naučte se konfigurovat vlastnosti vaší nabídky software jako služba (SaaS) Microsoft Commercial Marketplace v partnerském centru.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 7d90c9b9b2c9aa97083e17d5ab7d20fc6b471658
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94492119"
---
# <a name="how-to-configure-your-saas-offer-properties"></a>Jak nakonfigurovat vlastnosti nabídky SaaS

Tento článek popisuje, jak nakonfigurovat vlastnosti nabídky software jako služba (SaaS) na komerčním webu Microsoft Marketplace.

Na kartě **vlastnosti** Definujte kategorie a obory, které se vztahují na vaši nabídku, verzi aplikace a platné smlouvy. Ujistěte se, že jste na této stránce zadali úplné a přesné podrobnosti o vaší nabídce, aby se zobrazovaly správně a nabízely správné sadě zákazníků.

## <a name="select-a-category-for-your-offer"></a>Vyberte kategorii pro vaši nabídku.

V části **kategorie** vyberte aspoň jednu a až dvě kategorie pro seskupení nabídky do příslušných oblastí hledání na webu Marketplace. Na základě kategorií, které zvolíte, určíme, na které online obchody se má vaše nabídka vypsat: buď Azure Marketplace, Microsoft AppSource nebo obojí.

## <a name="select-industries-optional"></a>Vybrat obory (volitelné)

V části **odvětví** můžete vybrat až dva obory a až dva dílčí obory (označované také jako svislé) pro každý obor. Tyto obory slouží k zobrazení vaší nabídky, když zákazníci filtrují svá hledání v oborech a pododvětvích v online obchodě.

> [!NOTE]
> Pokud vaše nabídka není specifická pro konkrétní obor, ponechte tuto část prázdnou.

1. V části **odvětví** vyberte odkaz **+ odvětví** .
1. Vyberte odvětví ze seznamu **odvětví** .
1. V seznamu **dílčích oborů** vyberte aspoň jednu a maximálně dvě svislá. Pomocí klávesy CTRL můžete vybrat více dílčích oborů.
1. Pokud chcete přidat další obor a svislý, vyberte **+ odvětví** a opakujte kroky 1 až 3.

## <a name="specify-an-app-version-optional"></a>Zadat verzi aplikace (volitelné)

 Do pole **verze aplikace** zadejte číslo verze. Verze aplikace se používá na webu AppSource Marketplace k identifikaci čísla verze vaší nabídky.

## <a name="provide-terms-and-conditions"></a>Zadání podmínek a ujednání

V části **právní** zadejte podmínky a ujednání pro vaši nabídku. Máte dvě možnosti:

- [Použití standardní smlouvy s volitelnými změnami](#use-the-standard-contract)
- [Použití vlastních podmínek a ujednání](#use-your-own-terms-and-conditions)

Další informace o standardní smlouvě a volitelných změnách najdete v tématu [standardní smlouva o komerčním tržišti společnosti Microsoft](standard-contract.md). Můžete stáhnout [standardní](https://go.microsoft.com/fwlink/?linkid=2041178) pracovní soubor PDF (Ujistěte se, že blokování automaticky otevíraných oken je vypnuté).

### <a name="use-the-standard-contract"></a>Použití standardní smlouvy

Pro zjednodušení procesu nákupu pro zákazníky a omezení právní složitosti pro dodavatele softwaru nabízí společnost Microsoft standardní kontrakt, který můžete použít pro vaše nabídky na komerčním webu Marketplace. Když nabízený software obdržíte na základě standardního kontraktu, zákazníci ho musí přečíst a přijmout jenom jednou a vy nemusíte vytvářet vlastní podmínky.

1. Zaškrtněte políčko **použít standardní smlouvu pro komerčního tržiště Microsoftu** .

   ![Ukazuje zaškrtávací políčko použít standardní kontrakt pro komerční web Marketplace společnosti Microsoft.](partner-center-portal/media/use-standard-contract.png)
1. V **potvrzovacím** dialogovém okně vyberte **přijmout**. Možná se budete muset posunout nahoru a podívat se na něj.
1. Než budete pokračovat, vyberte **Uložit koncept** .

   > [!NOTE]
   > Po publikování nabídky pomocí standardní smlouvy pro komerční web Marketplace nemůžete použít vlastní podmínky a ujednání. Buď poskytněte řešení v rámci standardní smlouvy s volitelnými změnami, nebo podle vašich vlastních podmínek.

### <a name="add-amendments-to-the-standard-contract-optional"></a>Přidat změny standardní smlouvy (volitelné)

K dispozici jsou dva druhy změn: *univerzální* a *vlastní*.

#### <a name="add-universal-amendment-terms"></a>Přidat univerzální výrazy změny

V části **Universal dodatku ke standardní smlouvě pro komerční tržiště Microsoftu** zadejte své univerzální platební údaje. Do tohoto pole můžete zadat neomezený počet znaků. Tyto výrazy se zobrazí zákazníkům v AppSource, Azure Marketplace a/nebo Azure Portal během procesu zjišťování a nákupu.

#### <a name="add-one-or-more-custom-amendments"></a>Přidání jednoho nebo více vlastních změn

1. V části **vlastní změny se za podmínky standardní smlouvy pro komerční tržiště Microsoftu** vyberte odkaz **Přidat vlastní termín změny (max. 10)** .
1. Do pole **vlastní výrazy změny** zadejte své výrazy změny.
1. Do pole **ID tenanta** zadejte ID tenanta. Pouze zákazníci, kteří jsou přiřazeni k těmto uživatelským identifikátorům, které zadáte pro tyto vlastní výrazy, se jim zobrazí v nákupním toku nabídky v Azure Portal.
   > [!TIP]
   > ID tenanta identifikuje zákazníka v Azure. U zákazníka můžete požádat o toto ID a získat ho tak, že se vrátíte na [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **vlastnosti**. Hodnota ID adresáře je ID tenanta (například `50c464d3-4930-494c-963c-1e951d15360e` ). ID tenanta organizace zákazníka můžete také vyhledat pomocí adresy URL svého názvu domény v umístění [moje Microsoft Azure a ID tenanta Office 365?](https://www.whatismytenantid.com/).
1. Do pole **Popis** můžete volitelně zadat popisný popis ID tenanta. Tento popis vám pomůže identifikovat zákazníka, kterému cílíte na změnu.
1. Pokud chcete přidat další ID tenanta, vyberte odkaz **Přidat ID tenanta zákazníka** a opakujte kroky 3 a 4. Můžete přidat až 20 ID klientů.
1. Pokud chcete přidat další termín změny, opakujte kroky 1 až 5. Pro každou nabídku můžete zadat až deset vlastních podmínek pro změnu. 
2. Než budete pokračovat, vyberte **Uložit koncept** .

### <a name="use-your-own-terms-and-conditions"></a>Použití vlastních podmínek a ujednání

Místo standardní smlouvy se můžete rozhodnout, že budete chtít zadat vlastní podmínky a ujednání. Zákazníci musí tyto podmínky přijmout, aby mohli svoji nabídku vyzkoušet.

1. V části **zákon** se ujistěte, že je zaškrtnuté políčko **používat standardní kontrakt pro komerční web Marketplace** .
1. V poli **podmínky a ujednání** zadejte až 10 000 znaků textu.
1. Než budete pokračovat na další **kartu, vyberte** možnost **Uložit koncept** .

## <a name="next-steps"></a>Další kroky

- [Jak nakonfigurovat podrobnosti seznamu nabídky SaaS](create-new-saas-offer-listing.md)
