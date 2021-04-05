---
title: Konfigurace plánu šablony řešení
description: Naučte se konfigurovat plán šablon řešení pro nabídku aplikací Azure v partnerském centru.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 8469cad02009d054bd8ba97fb4aabfdae84ef842
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96744614"
---
# <a name="configure-a-solution-template-plan"></a>Konfigurace plánu šablony řešení

Tento článek se týká pouze plánů šablon řešení pro nabídku aplikací Azure. Pokud konfigurujete plán spravované aplikace, pokračujte tím, že [nakonfigurujete plán spravované aplikace](create-new-azure-apps-offer-managed.md).

## <a name="choose-who-can-see-your-plan"></a>Zvolit, kdo může zobrazit váš plán

Každý plán můžete nakonfigurovat tak, aby byl viditelný pro všechny uživatele nebo jenom pro konkrétní cílovou skupinu. Přístup k soukromé cílové skupině udělíte pomocí ID předplatných Azure s možností zahrnutí popisu každého ID předplatného, které přiřadíte. Pomocí můžete přidat maximálně 10 ID předplatných nebo až 10 000 ID předplatných. Soubor CSV. ID předplatných Azure jsou reprezentovaná jako identifikátory GUID a písmena musí být malá.

> [!NOTE]
> Pokud publikujete soukromý plán, můžete později změnit jeho viditelnost na veřejné. Po publikování veřejného plánu ale nemůžete změnit jeho viditelnost na Private.

Na kartě **dostupnost** v části **plán viditelnost** proveďte jednu z následujících akcí:

- Pokud chcete nastavit plán jako veřejný, vyberte možnost **veřejné** (označuje se také jako _přepínač_).
- Pokud chcete nastavit plán jako soukromý, vyberte možnost **Private** a pak přidejte ID předplatného Azure ručně nebo se souborem CSV.

    > [!NOTE]
    > Soukromá nebo omezená skupina je odlišná od cílové skupiny Preview, kterou jste definovali na kartě **Preview** . Cílová skupina Preview má přístup k vaší nabídce před tím, než se zveřejní na webu Marketplace. I když se soukromá skupina zvolí jenom pro konkrétní plán, cílová skupina Preview může pro účely ověřování Zobrazit všechny plány (soukromě nebo ne).

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Ruční přidání ID předplatných Azure pro soukromý plán

1. V části **plán viditelnost** vyberte možnost **privátní** .
1. Do pole **ID předplatného Azure** , které se zobrazí, zadejte ID předplatného Azure cílové skupiny, které chcete udělit přístup k tomuto privátnímu plánu. Vyžaduje se minimálně jedno ID předplatného.
1. Volitelné Do pole **Popis** zadejte popis této cílové skupiny.
1. Pokud chcete přidat jiné ID předplatného, vyberte odkaz **Přidat ID (max. 10)** a opakujte kroky 2 a 3.

## <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Použijte. Soubor CSV pro přidání ID předplatných Azure pro privátní plán

1. V části **plán viditelnost** vyberte možnost **privátní** .
1. Vyberte odkaz **exportovat cílovou skupinu (CSV)** .
1. Otevřete. Soubor CSV a přidejte ID předplatného Azure, kterým chcete udělit přístup k soukromé nabídce, do sloupce **ID** .
1. V případě potřeby zadejte popis pro každou cílovou skupinu ve sloupci **Popis** .
1. Do sloupce **typ** přidejte "SubscriptionId" pro každý řádek s ID předplatného.
1. Uložte. Soubor CSV.
1. Na kartě **dostupnost** v části **plán viditelnost** vyberte odkaz **importovat cílovou skupinu (CSV)** .
1. V dialogovém okně, které se zobrazí, vyberte **Ano**.
1. Vyberte. Soubor CSV a pak vyberte **otevřít**. Zobrazí se zpráva oznamující, že. Soubor CSV byl úspěšně importován.

### <a name="hide-your-plan"></a>Skrýt plán

Pokud má být šablona řešení určena k nasazení pouze nepřímo, pokud se na ni odkazuje i na jinou šablonu řešení nebo spravovanou aplikaci, zaškrtněte políčko v části **Skrýt plán** pro publikování šablony řešení, ale skryjete je od zákazníků, kteří ji hledají a vyhledávají přímo.

Než budete pokračovat k další části, vyberte **Uložit koncept** : Definujte technickou konfiguraci.

## <a name="define-the-technical-configuration"></a>Definování technické konfigurace

Na kartě **Technická konfigurace** nahrajete balíček pro nasazení, který umožní zákazníkům nasadit váš plán a zadat číslo verze balíčku.

> [!NOTE]
> Tato karta se nezobrazí, pokud se rozhodnete znovu použít balíčky z jiného plánu na kartě **Nastavení plánu** . Pokud ano, přejít k [zobrazení vašich plánů](#view-your-plans).

### <a name="assign-a-version-number-for-the-package"></a>Přiřaďte číslo verze balíčku.

V poli **verze** zadejte aktuální verzi technické konfigurace. Zvyšte tuto verzi pokaždé, když publikujete změnu na této stránce. Číslo verze musí být ve formátu Integer. Integer. Integer. Například, `1.0.2`.

### <a name="upload-a-package-file"></a>Nahrání souboru balíčku

V části **soubor balíčku (. zip)** přetáhněte soubor balíčku na šedé pole nebo vyberte odkaz **Vyhledat soubory** .

> [!NOTE]
> Pokud máte problém s nahráváním souborů, ujistěte se, že vaše místní síť neblokuje `https://upload.xboxlive.com` službu používanou partnerským centrem.

### <a name="previously-published-packages"></a>Dřív publikované balíčky

Po publikování vaší nabídky na webu se na stránce **Technická konfigurace** zobrazí dílčí karta **dříve publikované balíčky** . Tato karta obsahuje všechny dříve publikované verze vaší technické konfigurace.

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
