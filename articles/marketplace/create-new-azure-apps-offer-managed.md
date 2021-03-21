---
title: Konfigurace plánu spravované aplikace
description: Naučte se konfigurovat plán spravované aplikace pro nabídku aplikací Azure v partnerském centru.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a67a721b8d94112347282e1cd9be4d43f4ac85da
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96744631"
---
# <a name="configure-a-managed-application-plan"></a>Konfigurace plánu spravované aplikace

Tento článek se týká jenom plánů spravované aplikace pro nabídku aplikací Azure. Pokud konfigurujete plán šablony řešení, přejdete do [Konfigurace plánu šablony řešení](create-new-azure-apps-offer-solution.md).

## <a name="define-markets-pricing-and-availability"></a>Definování trhů, cen a dostupnosti

Každý plán musí být k dispozici alespoň v jednom trhu. Na kartě **ceny a dostupnost** můžete nakonfigurovat trhy, na které bude tento plán dostupný, cenu a to, jestli má být plán viditelný pro všechny nebo jenom pro konkrétní zákazníky (označované také jako soukromý plán).

1. V části **trhy** vyberte odkaz **Upravit trhy** .
1. V dialogovém okně, které se zobrazí, vyberte umístění na trhu, kde má být plán dostupný. Musíte vybrat minimálně jeden z nich, maximálně 141 trhů.

    > [!NOTE]
    > Toto dialogové okno obsahuje vyhledávací pole a možnost filtrovat jenom země "daňové prominuté", ve kterých Microsoft za vás zavazuje prodeje a používání daně.

1. Vyberte **Uložit** a zavřete dialogové okno.

## <a name="define-pricing"></a>Definovat ceny

Do pole **Cena** zadejte cenu za měsíc pro tento plán. Tato cena je navíc k jakékoli infrastruktuře Azure nebo nákladům založeným na využití, které vznikají prostředky nasazenými tímto řešením.

Kromě ceny za měsíc můžete také nastavit ceny za spotřebu nestandardních jednotek pomocí [měřených faktur](partner-center-portal/azure-app-metered-billing.md). V případě, že budete chtít, můžete nastavit cenu za měsíc na nulu a účtovat výhradně pomocí měřených faktur.

Ceny se nastavují v USD (USD = USA dolar) se převádějí do místní měny všech vybraných trhů za použití aktuálních směnných kurzů při uložení. Před publikováním tyto ceny ověřte, a to tak, že vyexportujete tabulku cen a zkontrolujete cenu na každém trhu. Pokud chcete nastavit vlastní ceny v individuálním trhu, upravte a importujte tabulku s cenami.

### <a name="add-a-custom-meter-dimension-optional"></a>Přidat vlastní dimenzi měřiče (volitelné)

1. V části **dimenze služby pro měření na Marketplace** vyberte odkaz **Přidat vlastní dimenzi měřiče (max. 18)** .
1. Do pole **ID** zadejte neproměnlivý odkaz na identifikátor při generování událostí využití.
1. Do pole **Zobrazovaný název** zadejte zobrazovaný název přidružený k dimenzi. Například "textové zprávy jsou odesílány".
1. V poli **Měrná jednotka** zadejte popis fakturační jednotky. Například "na textovou zprávu" nebo "za 100 e-mailů".
1. Do pole **cena za jednotku v USD** zadejte cenu za jednu jednotku dimenze.
1. V poli **měsíční množství zahrnuté do základního** pole zadejte množství (jako celé číslo) dimenze, která je zahrnutá v každém měsíci, pro zákazníky, kteří platíte za opakovaný měsíční poplatek. Chcete-li nastavit neomezené množství, zaškrtněte místo toho zaškrtávací políčko.
1. Chcete-li přidat další vlastní dimenzi měřiče, opakujte kroky 1 až 7.

### <a name="set-custom-prices-optional"></a>Nastavit vlastní ceny (volitelné)

Ceny stanovené v USD (USD = USA dolar) se v místní měně všech vybraných trhů převedou na základě aktuálních směnných kurzů při uložení. Před publikováním tyto ceny ověřte, a to tak, že vyexportujete tabulku cen a zkontrolujete cenu na každém trhu. Pokud chcete nastavit vlastní ceny v individuálním trhu, upravte a importujte tabulku s cenami.

Před publikováním si pečlivě Projděte své ceny, protože existují určitá omezení, co se může po publikování plánu změnit.

> [!NOTE]
> Po zveřejnění ceny za uvedení na trh ve vašem plánu ji nelze později změnit.

Chcete-li nastavit vlastní ceny v individuálním trhu, exportujte, upravte a pak Importujte tabulku s cenami. Zodpovídáte za ověřování těchto cen a vlastnictví těchto nastavení. Podrobné informace najdete v tématu [vlastní ceny](plans-pricing.md#custom-prices).

1. Aby bylo možné exportovat data o cenách, musíte nejdřív Uložit změny cen. U dolního okraje karty **ceny a dostupnost** vyberte **Uložit koncept**.
1. V části **ceny** vyberte odkaz **exportovat data cen** .
1. Otevřete soubor exportedPrice.xlsx v aplikaci Microsoft Excel.
1. V tabulce proveďte požadované aktualizace informací o cenách a pak soubor uložte.

   Než budete moct soubor aktualizovat, možná budete muset povolit úpravy v Excelu.

1. Na kartě **ceny a dostupnost** v části **ceny** vyberte odkaz **importovat data ceny** .
1. V dialogovém okně, které se zobrazí, klikněte na tlačítko **Ano**.
1. Vyberte soubor exportedPrice.xlsx, který jste aktualizovali, a pak klikněte na **otevřít**.

## <a name="choose-who-can-see-your-plan"></a>Zvolit, kdo může zobrazit váš plán

Každý plán můžete nakonfigurovat tak, aby byl viditelný pro všechny uživatele nebo jenom pro konkrétní cílovou skupinu. Přístup k soukromé cílové skupině udělíte pomocí ID předplatných Azure s možností zahrnutí popisu každého ID předplatného, které přiřadíte. Pomocí můžete přidat maximálně 10 ID předplatných nebo až 10 000 ID předplatných. Soubor CSV. ID předplatných Azure jsou reprezentovaná jako identifikátory GUID a písmena musí být malá.

> [!NOTE]
> Pokud publikujete soukromý plán, můžete později změnit jeho viditelnost na veřejné. Po publikování veřejného plánu ale nemůžete změnit jeho viditelnost na Private.

V části **plán viditelnost** proveďte jednu z následujících akcí:

- Pokud chcete nastavit plán jako veřejný, vyberte možnost **veřejné** (označuje se také jako _přepínač_).
- Pokud chcete nastavit plán jako soukromý, vyberte možnost **Private** a pak přidejte ID předplatného Azure ručně nebo se souborem CSV.

> [!NOTE]
> Soukromá nebo omezená skupina je odlišná od cílové skupiny Preview, kterou jste definovali na kartě **Preview** . Cílová skupina Preview má přístup k vaší nabídce před tím, než se zveřejní na webu Marketplace. I když se soukromá skupina zvolí jenom pro konkrétní plán, cílová skupina Preview může pro účely ověřování Zobrazit všechny plány (soukromě nebo ne).

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Ruční přidání ID předplatných Azure pro soukromý plán

1. V části **plán viditelnost** vyberte možnost **privátní** .
1. Do pole **ID předplatného Azure** , které se zobrazí, zadejte ID předplatného Azure cílové skupiny, které chcete udělit přístup k tomuto privátnímu plánu. Vyžaduje se minimálně jedno ID předplatného.
1. Volitelné Do pole **Popis** zadejte popis této cílové skupiny.
1. Pokud chcete přidat jiné ID předplatného, vyberte odkaz **Přidat ID (max. 10)** a opakujte kroky 2 a 3.

### <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Použijte. Soubor CSV pro přidání ID předplatných Azure pro privátní plán

1. V části **plán viditelnost** vyberte možnost **privátní** .
1. Vyberte odkaz **exportovat cílovou skupinu (CSV)** .
1. Otevřete. Soubor CSV a přidejte ID předplatného Azure, kterým chcete udělit přístup k soukromé nabídce, do sloupce **ID** .
1. V případě potřeby zadejte popis pro každou cílovou skupinu ve sloupci **Popis** .
1. Do sloupce **typ** přidejte "SubscriptionId" pro každý řádek s ID předplatného.
1. Uložte. Soubor CSV.
1. Na kartě **dostupnost** v části **plán viditelnost** vyberte odkaz **importovat cílovou skupinu (CSV)** .
1. V dialogovém okně, které se zobrazí, vyberte **Ano**.
1. Vyberte. Soubor CSV a pak vyberte **otevřít**. Zobrazí se zpráva oznamující, že. Soubor CSV byl úspěšně importován.

## <a name="define-the-technical-configuration"></a>Definování technické konfigurace

Na kartě **Technická konfigurace** nahrajete balíček pro nasazení, který umožní zákazníkům nasadit váš plán a zadat číslo verze balíčku. Budete také poskytovat další technické informace.

> [!NOTE]
> Tato karta se nezobrazí, pokud se rozhodnete znovu použít balíčky z jiného plánu na kartě **Nastavení plánu** . Pokud ano, přejít k [zobrazení vašich plánů](#view-your-plans).

### <a name="assign-a-version-number-for-the-package"></a>Přiřaďte číslo verze balíčku.

V poli **verze** zadejte aktuální verzi technické konfigurace. Zvyšte tuto verzi pokaždé, když publikujete změnu na této stránce. Číslo verze musí být ve formátu Integer. Integer. Integer. Například, `1.0.2`.

### <a name="upload-a-package-file"></a>Nahrání souboru balíčku

V části **soubor balíčku (. zip)** přetáhněte soubor balíčku na šedé pole nebo vyberte odkaz **Vyhledat soubory** .

> [!NOTE]
> Pokud máte problém s nahráváním souborů, ujistěte se, že vaše místní síť neblokuje `https://upload.xboxlive.com` službu používanou partnerským centrem.

#### <a name="previously-published-packages"></a>Dřív publikované balíčky

Dílčí karta **dříve publikované balíčky** umožňuje zobrazit všechny publikované verze technické konfigurace.

### <a name="enable-just-in-time-jit-access-optional"></a>Povolit přístup JIT (just-in-time) (volitelné)

Pokud chcete pro tento plán povolit přístup JIT, zaškrtněte políčko **Povolit přístup JIT (just-in-time)** . Pokud chcete vyžadovat, aby příjemci spravované aplikace udělily trvalému přístupu ke svému účtu, nechte tuto možnost nezaškrtnutou. Další informace o této možnosti najdete v tématu věnovaném [přístupu JIT (just in time)](plan-azure-app-managed-app.md#just-in-time-jit-access).

### <a name="select-a-deployment-mode"></a>Vybrat režim nasazení

Vyberte buď režim **úplného** nebo **přírůstkového** nasazení.

- V režimu **úplného** nasazení aplikace pro zákazníka dojde k odebrání prostředků ve spravované skupině prostředků, pokud prostředky nejsou definované v [mainTemplate.js](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template).
- V **přírůstkovém** režimu opětovné nasazení aplikace opustí stávající prostředky beze změny.

Další informace o režimech nasazení najdete v tématu [Azure Resource Manager režimy nasazení](../azure-resource-manager/templates/deployment-modes.md).

### <a name="provide-a-notification-endpoint-url"></a>Zadejte adresu URL koncového bodu oznámení.

V poli **Adresa URL koncového bodu oznámení** Zadejte koncový bod Webhooku https, který bude dostávat oznámení o všech operacích CRUD na instancích spravovaných aplikací této verze plánu.

### <a name="customize-allowed-customer-actions-optional"></a>Přizpůsobení povolených akcí zákazníka (volitelné)

1. Chcete-li určit, které akce mohou zákazníci provádět u spravovaných prostředků kromě `*/read` akcí "", které jsou ve výchozím nastavení k dispozici, zaškrtněte políčko **přizpůsobit povolené akce zákazníka** .
1. Do zobrazených polí zadejte další akce řízení a povolené datové akce, které chcete, aby zákazník mohl provést, oddělený středníky. Chcete-li například povolit uživatelům restartování virtuálních počítačů, přidejte `Microsoft.Compute/virtualMachines/restart/action` do pole **povolené akce ovládacího prvku** .

### <a name="choose-who-can-manage-the-application"></a>Zvolit, kdo může aplikaci spravovat

Určete, kdo má mít v každé vybrané oblasti Azure přístup pro správu k této spravované aplikaci: globální _Cloud_ _Azure_ a Azure Government. K identifikaci uživatelů, skupin nebo aplikací, které chcete udělit oprávnění ke spravované skupině prostředků, budete používat identity Azure AD. Další informace najdete v tématu [plánování spravované aplikace Azure pro nabídku aplikací Azure](plan-azure-application-offer.md).

Podle potřeby proveďte následující kroky pro globální cloud Azure a Azure Government.

1. V poli **Azure Active Directory ID tenanta** zadejte ID TENANTA Azure AD (označované také jako ID adresáře) obsahující identity uživatelů, skupin nebo aplikací, kterým chcete udělit oprávnění.
1. V poli **ID objektu zabezpečení** zadejte ID objektu Azure AD pro uživatele, skupinu nebo aplikaci, kterým chcete udělit oprávnění pro spravovanou skupinu prostředků. Identifikujte uživatele podle ID objektu zabezpečení, které najdete v okně [Azure Active Directory uživatelé](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) na Azure Portal.
1. V seznamu **definice role** vyberte předdefinovanou roli Azure AD. Vybraná role popisuje oprávnění, která bude mít objekt zabezpečení u prostředků v rámci předplatného zákazníka.
1. Chcete-li přidat další autorizaci, vyberte odkaz **Přidat autorizaci (max. 100)** a opakujte kroky 1 až 3.

### <a name="policy-settings-optional"></a>Nastavení zásad (volitelné)

Můžete nakonfigurovat maximálně pět zásad a jenom jednu instanci jednotlivých zásad. Některé zásady vyžadují další parametry.

1. V části **nastavení zásad** vyberte odkaz **+ Přidat zásadu (max. 5)** .
1. Do pole **název** zadejte název přiřazení zásady (omezeno na 50 znaků).
1. V seznamu **zásady** vyberte zásadu Azure, která se použije u prostředků vytvořených spravovanou aplikací v rámci zákaznického předplatného.
1. V poli **parametry zásad** zadejte parametr, na kterém by se měly použít zásady auditování a nastavení diagnostiky.
1. V poli se seznamem **SKU zásad** vyberte typ SKU zásad.

    > [!NOTE]
    > Pro zásady auditu se vyžaduje SKU _standardní zásady_ .

## <a name="view-your-plans"></a>Zobrazit vaše plány

- Vyberte **Uložit koncept** a pak v levém horním rohu stránky vyberte **plán – přehled** , který se vrátí na stránku **Přehled plánu** .

Po vytvoření jednoho nebo více plánů uvidíte název svého plánu, ID plánu, typ plánu, dostupnost (veřejné nebo soukromé), aktuální stav publikování a všechny dostupné akce na kartě **Přehled plánu** .

Akce, které jsou k dispozici ve sloupci **Akce** na kartě **Přehled plánu** , se liší v závislosti na stavu plánu a mohou zahrnovat následující:

- Pokud je stav plánu **koncept**, odkaz ve sloupci **Akce** bude vyslovit **Odstranit koncept**.
- Pokud je stav plánu **Live**, odkaz ve sloupci **Akce** buď **zastaví prodej plánu** , nebo **synchronizuje soukromou cílovou skupinu**. Odkaz **synchronizovat soukromou cílovou skupinu** bude publikovat jenom změny vašich privátních cílových skupin, aniž byste museli publikovat další aktualizace, které jste mohli v této nabídce udělat.
- Pokud chcete pro tuto nabídku vytvořit další plán, vyberte v horní části karty **Přehled plánu** možnost **+ vytvořit nový plán**. Pak opakujte postup v tématu [Postup vytvoření plánů pro nabídku aplikace Azure](create-new-azure-apps-offer-plans.md). V opačném případě, pokud jste dokončili vytváření plánů, jděte do další části: další kroky.

## <a name="next-steps"></a>Další kroky

- [Jak otestovat a publikovat nabídku aplikace Azure](create-new-azure-apps-offer-test-publish.md)
- Naučte se, [Jak prodávat nabídku aplikací Azure](create-new-azure-apps-offer-marketing.md) prostřednictvím společného prodeje s Microsoftem a prodávat prostřednictvím programů CSP.