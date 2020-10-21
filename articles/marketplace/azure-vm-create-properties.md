---
title: Konfigurace vlastností nabídky virtuálního počítače na Azure Marketplace
description: Naučte se konfigurovat vlastnosti nabídky virtuálního počítače na Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: cc5702d973150b80188354719333fb7cf22d58b3
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283920"
---
# <a name="how-to-configure-virtual-machine-offer-properties"></a>Postup konfigurace vlastností nabídky virtuálního počítače

Na stránce **vlastnosti** Definujte kategorie, které se použijí k seskupení nabídky virtuálních počítačů na Azure Marketplace, verzi vaší aplikace a právní smlouvy, které vaši nabídku podporují.

## <a name="select-a-category"></a>Vybrat kategorii

Vyberte kategorie a podkategorie, aby se vaše nabídka umístila do odpovídajících oblastí hledání Azure Marketplace. Nezapomeňte popsat, jak vaše nabídka podporuje tyto kategorie v popisu nabídky. Vyberte:

- Aspoň jedna a až dvě kategorie, včetně primární a sekundární kategorie (volitelné).
- Až dvě podkategorie pro každou primární nebo sekundární kategorii. Pokud není pro vaši nabídku k dispozici žádná podkategorie, vyberte možnost **Nepoužito**.

Úplný seznam kategorií a podkategorií v nabídce, kde najdete [Doporučené postupy](gtm-offer-listing-best-practices.md). Nabídky virtuálních počítačů se vždy zobrazují v kategorii **COMPUTE** v Azure Marketplace.

## <a name="provide-terms-and-conditions"></a>Zadání podmínek a ujednání

V části **právní**zadejte podmínky a ujednání pro vaši nabídku. Máte dvě možnosti:

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

1. V části **vlastní změny se za podmínky standardní smlouvy pro komerční tržiště Microsoftu**vyberte odkaz **Přidat vlastní termín změny (max. 10)** .
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

1. V části **zákon**se ujistěte, že je zaškrtnuté políčko **používat standardní kontrakt pro komerční web Marketplace** .
1. V poli **podmínky a ujednání** zadejte až 10 000 znaků textu.

   > [!NOTE]
   > Pokud budete potřebovat delší popis, zadejte jednu webovou adresu, na které odkazuje, kde se vaše podmínky a ujednání můžou najít. Zobrazí se zákazníkům jako aktivní odkaz.

1. Než budete pokračovat na další **kartu, vyberte**možnost **Uložit koncept** .

## <a name="next-steps"></a>Další kroky

- [Konfigurace seznamu nabídek virtuálních počítačů](azure-vm-create-listing.md)
