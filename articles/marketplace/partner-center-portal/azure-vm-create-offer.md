---
title: Vytvoření nabídky virtuálních počítačů Azure na Azure Marketplace
description: Naučte se publikovat nabídku virtuálního počítače pro Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 08/07/2020
ms.openlocfilehash: 46749015d5141053eaea3f2f251bbba7d9455481
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91709012"
---
# <a name="create-an-azure-virtual-machine-offer-on-azure-marketplace"></a>Vytvoření nabídky virtuálních počítačů Azure na Azure Marketplace

Tento článek popisuje, jak vytvořit a publikovat nabídku virtuálních počítačů Azure pro [Azure Marketplace](https://azuremarketplace.microsoft.com/). Řeší virtuální počítače založené na Windows i Linux, které obsahují operační systém, virtuální pevný disk (VHD) a až 16 datových disků. 

Než začnete, [Vytvořte si účet pro komerční tržiště v partnerském centru](create-account.md). Ujistěte se, že je váš účet zaregistrovaný v programu komerčního tržiště.

## <a name="introduction"></a>Úvod

### <a name="the-benefits-of-publishing-to-azure-marketplace"></a>Výhody publikování na Azure Marketplace

Když publikujete své nabídky na Azure Marketplace, můžete:

- Povýšit svoji společnost o technickou podporu společnosti Microsoft.
- Dosahujte více než 100 000 000 Microsoft 365 a uživatelů Dynamics 365 a více než 200 000 organizací.
- Získejte vysoce kvalitní zájemce z těchto tržišť.
- Získejte služby, které jsou povýšené na prodejní a prodejní týmy pole Microsoftu.

### <a name="before-you-begin"></a>Než začnete

Pokud jste to ještě neudělali, Projděte si [příručku pro publikování nabídek virtuálních počítačů](../marketplace-virtual-machines.md) a materiál tohoto virtuálního počítače Azure:

- Průvodci rychlým startem
  - [Šablony pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/)
  - [Šablony Azure pro rychlý Start pro GitHub](https://github.com/azure/azure-quickstart-templates)
- Kurzy
  - [Virtuální počítače s Linuxem](../../virtual-machines/linux/tutorial-manage-vm.md)
  - [Virtuální počítače s Windows](../../virtual-machines/windows/tutorial-manage-vm.md)
- ukázky
  - [Ukázky v Azure CLI pro virtuální počítače se systémem Linux](../../virtual-machines/linux/cli-samples.md)
  - [Azure PowerShell pro virtuální počítače se systémem Linux](../../virtual-machines/linux/powershell-samples.md)
  - [Ukázky v Azure CLI pro virtuální počítače s Windows](../../virtual-machines/windows/cli-samples.md)
  - [Azure PowerShell pro virtuální počítače s Windows](../../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md)

### <a name="fundamentals-in-technical-knowledge"></a>Základy technického vědomí

Proces navrhování, sestavování a testování nabídek trvá čas a vyžaduje odbornost na platformě Azure i v technologiích, které se používají k sestavení vaší nabídky.

Váš technický tým by měl mít základní znalosti a praktické znalosti následujících technologií Microsoftu:

- [Služby Azure](https://azure.microsoft.com/services/)
- [Návrh a Architektura aplikací Azure](https://azure.microsoft.com/solutions/architecture/)
- Sítě [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)a [Azure](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
2. V levém podokně vyberte přehled **komerčního tržiště**  >  **Overview**.
3. Na stránce **Přehled** vyberte **Nový nabídka**  >  **virtuální počítač Azure**.

    ![Snímek obrazovky s možnostmi levého podokna v nabídce a tlačítkem Nová nabídka](./media/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Po publikování vaší nabídky se všechny úpravy, které provedete v partnerském centru, zobrazí Azure Marketplace až po opětovném publikování nabídky. Nezapomeňte vždy znovu publikovat nabídku po provedení změn.

## <a name="new-offer"></a>Nová nabídka

Zadejte **ID nabídky**. Toto je jedinečný identifikátor každé nabídky ve vašem účtu.

- Toto ID je viditelné pro zákazníky na webové adrese pro nabídku Azure Marketplace a v Azure PowerShell a v Azure CLI, pokud je to možné.
- Použijte při tom jenom malá písmena a číslice. ID může zahrnovat pomlčky a podtržítka, ale ne mezery a je omezeno na 50 znaků. Pokud například zadáte **příkaz Test-nabídky-1**, Webová adresa nabídky bude `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- ID nabídky nelze změnit po výběru možnost **vytvořit**.

Zadejte **alias nabídky**. Alias nabídky je název, který se používá pro nabídku v partnerském centru.

- Tento název se nepoužívá na Azure Marketplace. Liší se od názvu nabídky a dalších hodnot, které se zákazníkům zobrazují.

Vyberte **vytvořit** pro vygenerování nabídky a pokračování.

## <a name="offer-setup"></a>Nastavení nabídky

### <a name="test-drive"></a>Testovací verze

*Testovací jednotka* představuje skvělý způsob, jak předprezentovat vaši nabídku potenciálním zákazníkům. Dává jim možnost "vyzkoušet si, jak si ho koupit", což vám může usnadnit navýšení vašich převodů a generování vysoce kvalifikovaných potenciálních zákazníků. Další informace najdete v tématu [co je testovací jednotka?](../what-is-test-drive.md).

Pokud chcete pro pevný časový úsek povolit testovací jednotku, zaškrtněte políčko **Povolit testovací jednotku** . Pokud chcete ze své nabídky odebrat testovací jednotku, zrušte zaškrtnutí políčka.

Další zdroje testovacích jednotek:

- [Technické osvědčené postupy](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Marketingové osvědčené postupy](../what-is-test-drive.md)
- [Stažení testovací jednotky – přehled](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) Soubor PDF (Ujistěte se, že je vypnutý blokování automaticky otevíraných oken).

### <a name="customer-leads"></a>Potenciální zákazníci

Když publikujete nabídku na komerční Marketplace s partnerským centrem, připojte ji k systému pro správu vztahů se zákazníky (CRM). To vám umožní získat kontaktní údaje zákazníka, jakmile někdo vyjádří zájem nebo použije váš produkt. Pokud chcete povolit testovací jednotku (viz předchozí část), vyžaduje se připojení k CRM. V opačném případě je připojení k CRM volitelné.

1. Vyberte cíl zájemců, kam chcete, abychom posílali potenciální zákazníky. Partnerské centrum podporuje následující systémy CRM:
    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) pro zákaznickou zapojení
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Pokud zde není uvedený systém CRM, můžete k uložení zákaznických dat použít službu [Azure Table Storage](commercial-marketplace-lead-management-instructions-azure-table.md) nebo [koncový bod HTTPS](commercial-marketplace-lead-management-instructions-https.md) . Pak exportujte data do systému CRM.

1. Pokud publikujete v partnerském centru, připojte svoji nabídku k cíli realizace.
1. Potvrďte, že je správně nakonfigurované připojení k cíli realizace. Po publikování v partnerském centru Microsoft ověří připojení a pošle vám testovacího vedoucího. I když si nabídku před živým zobrazením předcházíte, můžete také otestovat své zájemce tím, že se pokusíte o nasazení nabídky sami v prostředí verze Preview.
1. Ujistěte se, že připojení k cíli realizace zůstane aktualizované, takže nepřijdete o žádné zájemce.

1. Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="properties"></a>Vlastnosti

Na stránce **vlastnosti** Definujte kategorie, které se použijí k seskupení vaší nabídky na Azure Marketplace, vaší verzi aplikace a právní smlouvy, které vaši nabídku podporují.

### <a name="category"></a>Kategorie

Vyberte kategorie a podkategorie a vložte nabídku do příslušných oblastí hledání na webu Marketplace. Nezapomeňte popsat, jak vaše nabídka podporuje tyto kategorie v popisu nabídky. Vyberte:

- Aspoň jedna a až dvě kategorie, včetně primární a sekundární kategorie (volitelné).
- Až dvě podkategorie pro každou primární nebo sekundární kategorii. Pokud není pro vaši nabídku k dispozici žádná podkategorie, vyberte možnost **Nepoužito**.

Úplný seznam kategorií a podkategorií v nabídce, kde najdete [Doporučené postupy](../gtm-offer-listing-best-practices.md). Nabídky virtuálních počítačů se vždy zobrazují v kategorii **COMPUTE** v Azure Marketplace.

### <a name="legal"></a>Právní informace

Zákazníkům musíte poskytnout podmínky nabídky a ujednání. Máte dvě možnosti:

- **Použití vlastních podmínek a ujednání**

   Pokud chcete zadat vlastní podmínky a ujednání, zadejte až 10 000 znaků textu v poli **podmínky a ujednání** . Pokud budete potřebovat delší popis, zadejte pro podmínky a ujednání jednu webovou adresu. Zobrazí se zákazníkům jako aktivní odkaz.

   Zákazníci musí tyto podmínky přijmout, aby mohli svoji nabídku vyzkoušet.

- **Použití standardní smlouvy pro komerční tržiště Microsoftu**

   Pro zjednodušení procesu nákupu pro zákazníky a omezení právní složitosti pro dodavatele softwaru nabízí společnost Microsoft standardní smlouvu pro komerční tržiště. Když nabízený software nabídnete pod standardním kontraktem, musí si ho zákazníci přečíst a přijmout jenom jednou a vy nemusíte vytvářet vlastní podmínky a ujednání.

   Použijte standardní smlouvu tak, že vyberete zaškrtávací políčko **použít standardní kontrakt pro komerční web Marketplace** a pak v místním okně vyberte **přijmout** (pokud ho chcete zobrazit, možná se posuňte nahoru).

   ![Snímek obrazovky s právním panelem v partnerském centru se zaškrtávacím políčkem "použít standardní kontrakt pro komerční web Marketplace" společnosti Microsoft.](media/use-standard-contract.png)

  > [!NOTE]
  > Po publikování nabídky pomocí standardní smlouvy pro komerční web Marketplace nemůžete použít vlastní podmínky a ujednání. Řešení můžete nabízet buď podle standardní smlouvy, nebo podle vašich vlastních podmínek.

  Další informace najdete v tématu [standardní smlouva pro komerční tržiště Microsoftu](../standard-contract.md). Stáhněte si standardní soubor PDF o [kontraktu](https://go.microsoft.com/fwlink/?linkid=2041178) (Ujistěte se, že je vypnutý blokování automaticky otevíraných oken).

  **Změny smlouvy Standard**

  Standardní změny smlouvy umožňují vybrat standardní smluvní smlouvy pro jednoduchost a vytvořit si pro svůj produkt nebo firmu i své výrazy. Zákazníci musí prověřit změny smlouvy pouze v případě, že již zkontrolovali a přijali standardní smlouvu společnosti Microsoft. Existují dva typy změn:

  * **Univerzální změny**: tyto změny se aplikují všeobecně na standardní smlouvu pro všechny zákazníky. Zobrazují se u každého zákazníka nabídky v toku nákupu. Zákazníci musí přijmout podmínky standardní smlouvy a změny před tím, než budou moci vaši nabídku použít. Pro každou nabídku můžete zadat jednu univerzální změnu. Do tohoto pole můžete zadat neomezený počet znaků. Tyto výrazy se zobrazí zákazníkům v AppSource, Azure Marketplace a/nebo Azure Portal během toku zjišťování a nákupu.

  * **Vlastní změny**: tyto zvláštní změny standardní smlouvy jsou zaměřené na konkrétní zákazníky prostřednictvím ID klientů Azure. Můžete zvolit tenanta, kterého chcete cílit. S vlastními podmínkami změny v toku nákupu nabídky se zobrazí jenom zákazníci z tenanta. Zákazníci musí přijmout podmínky standardní smlouvy a změny před tím, než budou moci vaši nabídku použít.

    1. Začněte tím, že vyberete **Přidat vlastní výrazy změny (max. 10)**. Pro každou nabídku můžete zadat až deset vlastních podmínek pro změnu. Postupujte následovně:

       a. Do pole **vlastní výrazy pro změnu** zadejte vlastní výrazy. Můžete zadat neomezený počet znaků. Pouze zákazníci z ID tenanta, které zadáte pro tyto vlastní výrazy, se budou zobrazovat v toku nákupu nabídky v Azure Portal.

       b. Požadovanou Zadejte **ID tenanta**. Každé vlastní změně může být cílem až 20 ID klientů. Pokud přidáte vlastní změnu, musíte zadat aspoň jedno ID tenanta, které identifikuje zákazníka v Azure. Vaše zákazníky to může najít v Azure výběrem **Azure Active Directory**  >  **vlastností**. Hodnota ID adresáře je ID tenanta (například 50c464d3-4930-494c-963c-1e951d15360e). ID tenanta organizace zákazníka můžete také najít pomocí webové adresy svého názvu domény na adrese [moje Microsoft Azure a Microsoft 365 ID tenanta?](https://www.whatismytenantid.com/).

       c. Volitelné Zadejte popisný **Popis** ID tenanta, který vám pomůže identifikovat zákazníka, kterému cílíte na změnu.

        > [!NOTE]
        > Tyto dva typy změn jsou vzájemně spárovány. Zákazníci, kteří jsou cílem vlastních změn, budou mít při nákupu také univerzální změny standardní smlouvy.

    1. Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="offer-listing"></a>Seznam nabídek

Na stránce **seznam nabídek** můžete definovat podrobnosti nabídky, jako je název nabídky, popis, odkazy a kontakty.

> [!NOTE]
> Vaše nabídka obsahu, jako je popis, dokumenty, snímky obrazovky a podmínky použití, nemusí být v angličtině, pokud popis nabídky začíná frází "Tato aplikace je k dispozici pouze v nástroji \<non-English language> ." Můžete také zadat adresu URL pro odkaz na web, který nabízí obsah v jiném jazyce, než který je použit v obsahu nabídky.

### <a name="marketplace-details"></a>Podrobnosti Marketplace

#### <a name="name"></a>Name

Název, který zde zadáte, se zákazníkům zobrazí jako název seznamu nabídek. Toto pole se vyplní názvem, který jste zadali do pole **alias nabídky** při vytváření nabídky. Tento název můžete později změnit. Název:

- Může být ochranné známky. Můžete zahrnout symboly ochranné známky a autorských práv.
- Nemůže obsahovat více než 50 znaků.
- Nejde zahrnout emoji.

#### <a name="search-results-summary"></a>Souhrn výsledků hledání

Zadejte krátký popis vaší nabídky, který se zobrazí ve výsledcích hledání Azure Marketplace. Může obsahovat až 100 znaků.

#### <a name="long-summary"></a>Dlouhý souhrn

Zadejte delší popis nabídky, která se zobrazí ve výsledcích hledání Azure Marketplace. Může obsahovat až 256 znaků.

#### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Odkaz zásady ochrany osobních údajů

Zadejte webovou adresu (URL) zásad ochrany osobních údajů vaší organizace. Ujistěte se, že vaše nabídka vyhovuje předpisům a zákonům o ochraně osobních údajů. Na svém webu musíte taky publikovat platné zásady ochrany osobních údajů.

### <a name="useful-links"></a>Užitečné odkazy

Poskytněte dodatečné online dokumenty o vaší nabídce. Chcete-li přidat odkaz, vyberte možnost **Přidat odkaz**a poté vyplňte následující pole:

- **Název**: zákazníci uvidí jméno na stránce s podrobnostmi.
- **Odkaz (URL)**: zadejte odkaz, který zákazníkům umožní zobrazit online dokument.

### <a name="customer-support-links"></a>Odkazy na zákaznickou podporu

Poskytněte web podpory, kde se zákazníci můžou připojit k vašemu týmu podpory.

- Web Azure Global support
- Web podpory Azure Government

### <a name="partner-support-contact"></a>Kontakt na podporu partnerů

Poskytněte kontaktní informace partnerům Microsoftu, kteří se použijí, když vaši zákazníci otevřou lístek podpory. Tyto informace nejsou uvedené na Azure Marketplace.

- Name
- E-mail
- Rozložení

### <a name="engineering-contact"></a>Technický kontakt

Poskytněte kontaktní informace Microsoftu pro použití v případě problémů s vaší nabídkou, včetně problémů s certifikací. Tyto informace nejsou uvedené na Azure Marketplace.

- Name
- E-mail
- Rozložení

### <a name="azure-marketplace-media"></a>Azure Marketplace média

Poskytněte loga a obrázky, které se mají použít s vaší nabídkou. Všechny obrázky musí být ve formátu PNG. Rozmazaný obraz způsobí odmítnutí odeslání.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Pokud máte problém s nahráváním souborů, ujistěte se, že vaše místní síť neblokuje https://upload.xboxlive.com službu, kterou používá Partnerské centrum.

#### <a name="azure-marketplace-logos"></a>Loga Azure Marketplace

Zadejte soubor PNG pro logo **velké** velikosti. Partnerské centrum použije toto k vytvoření **malého** a **středního** loga. Případně je můžete později nahradit různými obrázky.

- **Velký** (od 216 x 216 do 350 × 350 px, požadováno)
- **Střední** (90 x 90 px, volitelné)
- **Malý** (48 x 48 px, volitelné)

Tato loga se používají na různých místech v seznamu:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Screenshoty

Přidejte až pět snímků obrazovky, které ukazují, jak vaše nabídka funguje. Velikost každého snímku obrazovky musí být 1280 &times; 720 pixelů a ve formátu PNG. Každý snímek obrazovky musí obsahovat titulek.

#### <a name="videos"></a>Videa

Přidejte až pět videí, která předvádí vaši nabídku. Videa by se měla hostovat na externí službě video. Zadejte název a webovou adresu každého videa a miniaturu obrázku PNG na 1280 &times; 720 pixelů.

Další materiály k seznamům Marketplace najdete v tématu [osvědčené postupy pro výpisy nabídky na webu Marketplace](../gtm-offer-listing-best-practices.md).

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="preview"></a>Preview

Vyberte kartu **Náhled** a pak vyberte **skupinu omezené verze Preview** pro ověření své nabídky před tím, než ji publikujete na širší skupinu komerčních webů na webu Marketplace.

> [!IMPORTANT]
> Po kontrole nabídky v podokně **náhledu** vyberte možnost přejít na **Live** a publikujte vaši nabídku pro veřejnou cílovou skupinu na komerčním webu Marketplace.

Vaše cílová skupina Preview se identifikuje pomocí identifikátorů GUID předplatného Azure, spolu s volitelným popisem pro každý z nich. Žádná z těchto polí nemohou zákazníci vidět. ID vašeho předplatného Azure najdete na stránce **předplatná** v Azure Portal.

Přidejte aspoň jedno ID předplatného Azure, a to buď samostatně (až 10 ID), nebo nahráním souboru CSV (až 100 ID). Přidáním těchto ID předplatných určíte, kdo může zobrazit náhled vaší nabídky předtím, než se publikuje živě. Je-li již vaše nabídka živá, můžete i nadále definovat cílovou skupinu verze Preview pro testování nabídky změn nebo aktualizací vaší nabídky.

> [!NOTE]
> Cílová skupina verze Preview se liší od soukromé cílové skupiny. Cílová skupina Preview má přístup k vaší nabídce _před tím, než_ se publikuje živě na Azure Marketplace. Cílová skupina verze Preview může zobrazit a ověřit všechny plány, včetně těch, které budou k dispozici pouze pro soukromou cílovou skupinu po úplném publikování vaší nabídky do Azure Marketplace. Privátní skupina (definovaná v podokně ceny plánu **a dostupnost** ) má výhradní přístup ke konkrétnímu plánu.

Než budete pokračovat k další části, vyberte **Uložit koncept** .

## <a name="plan-overview"></a>Přehled plánu

V partnerském centru můžete v rámci stejné nabídky poskytnout nejrůznější možnosti plánu. Nabídka vyžaduje aspoň jeden plán (dříve označovaný jako SKU), který se může lišit podle finanční zhodnocení publikum, oblasti Azure, funkcí nebo imagí virtuálních počítačů.

Pro každou nabídku můžete vytvořit až 100 plánů: až 45 z nich může být privátní. Další informace o privátních plánech najdete v [části soukromé nabídky na komerčním webu Microsoft Marketplace](../private-offers.md).

Po vytvoření plánů vyberte kartu **Přehled plánu** , kterou chcete zobrazit:

- Názvy plánů
- Modely licencí
- Cílová skupina (veřejná nebo soukromá)
- Stav aktuálního publikování
- Dostupné akce

Akce, které jsou k dispozici v podokně **Přehled plánu** , se liší v závislosti na aktuálním stavu plánu.

- Pokud je stav plánu koncept, vyberte **Odstranit koncept**.
- Pokud je stav plánu publikovaný v reálném čase, vyberte **zastavit prodej plánu** nebo **synchronizovat soukromou cílovou skupinu**.

### <a name="create-a-new-plan"></a>Vytvořit nový plán

V horní části vyberte **vytvořit nový plán** . Zobrazí se dialogové okno **Nový plán** .

V poli **ID plánu** Vytvořte jedinečné ID plánu pro každý plán v této nabídce. Toto ID bude pro zákazníky viditelné na webové adrese produktu. Používejte jenom malá písmena a číslice, pomlčky nebo podtržítka a maximálně 50 znaků.

> [!NOTE]
> Po zvolení možnost **vytvořit**se ID plánu nedá změnit.

Do pole **název plánu** zadejte název pro tento plán. Zákazníci uvidí tento název, když rozhodují, který plán vybrat v rámci vaší nabídky. Vytvořte jedinečný název, který bude jasně ukazovat rozdíly mezi plány. Můžete například zadat **Windows Server** s plány průběžných *plateb*, *BYOL*, *Advanced*a *Enterprise* .

Vyberte **Vytvořit**.

### <a name="plan-setup"></a>Nastavení plánu

Nastavte na nejvyšší úrovni konfiguraci pro typ plánu, určete, zda znovu používá technickou konfiguraci z jiného plánu, a Identifikujte oblasti Azure, ve kterých má být plán k dispozici. Vaše výběry určují, která pole se zobrazí v ostatních podoknech pro stejný plán.

#### <a name="reuse-a-technical-configuration"></a>Opětovné použití technické konfigurace

Pokud máte více než jeden plán stejného typu a jsou mezi nimi identické balíčky, můžete vybrat tento plán, který znovu **použije technickou konfiguraci z jiného plánu**. Tato možnost umožňuje vybrat jeden z dalších plánů stejného typu pro tuto nabídku a umožňuje znovu použít jeho technickou konfiguraci.

> [!NOTE]
> Když znovu použijete technickou konfiguraci z jiného plánu, z tohoto plánu zmizí karta s celou **technickou konfigurací** . Pro tento plán se použijí i podrobnosti technické konfigurace z druhého plánu, včetně všech aktualizací, které v budoucnu provedete. Toto nastavení nelze po publikování plánu změnit.

#### <a name="azure-regions"></a>Oblast Azure

Váš plán se musí zpřístupnit aspoň v jedné oblasti Azure.

Vyberte **globální možnost Azure** a zpřístupněte svůj plán zákazníkům ve všech globálních oblastech Azure, které mají integraci komerčního tržiště. Další informace najdete v tématu [geografická dostupnost a podpora měny](../marketplace-geo-availability-currencies.md).

Vyberte možnost **Azure Government** pro zpřístupnění vašeho plánu v oblasti [Azure Government](../../azure-government/documentation-government-welcome.md) . Tato oblast poskytuje zákazníkům řízený přístup na federální, státní, místní nebo samosprávnéové entity USA a také pro partnery, kteří jim mají nárok na jejich obsluhu. Jako vydavatel zodpovídáte za řízení dodržování předpisů, míry zabezpečení a osvědčené postupy. Azure Government používá fyzicky izolovaná datová centra a sítě (jenom v USA).

Před publikováním [Azure Government](../../azure-government/documentation-government-manage-marketplace-partners.md), otestujte a ověřte svůj plán v prostředí, protože některé koncové body se mohou lišit. Pokud chcete nastavit a otestovat svůj plán, vyžádejte si zkušební účet ze stránky [Microsoft Azure Government zkušební verze](https://azure.microsoft.com/global-infrastructure/government/request/) .

> [!NOTE]
> Po publikování a dostupnosti plánu v konkrétní oblasti Azure tuto oblast nemůžete odebrat.

#### <a name="azure-government-certifications"></a>Azure Government certifikace

Tato možnost se zobrazí jenom v případě, že jste v předchozí části vybrali **Azure Government** jako oblast Azure.

Služba Azure Government Services zpracovává data, která se vztahují na určité předpisy a požadavky státní správy. Příklad: FedRAMP, NIST 800,171 (DIB), ITAR, finanční úřad 1075, DoD – L4 a CJIS. Pro zajištění povědomí o certifikaci pro tyto programy můžete poskytnout až 100 odkazů, které je popisují. Můžou to být buď odkazy na váš výpis v programu, nebo odkazy na popisy dodržování předpisů s nimi na vašich vlastních webech. Tyto odkazy jsou viditelné pouze pro Azure Government zákazníky.

Než budete pokračovat, vyberte **Uložit koncept** .

### <a name="plan-listing"></a>Výpis plánu

V této části nakonfigurujete podrobnosti o seznamu plánu. V tomto podokně se zobrazují konkrétní informace, které se mohou lišit od jiných plánů v rámci stejné nabídky.

#### <a name="plan-name"></a>Název plánu

Toto pole je automaticky vyplněno názvem, který jste přiřadili vašemu plánu při jeho vytvoření. Tento název se zobrazí v Azure Marketplace jako název tohoto plánu. Je omezený na 100 znaků.

#### <a name="plan-summary"></a>Souhrn plánu

Zadejte stručný souhrn vašeho plánu, nikoli nabídku. Tento souhrn je omezený na 100 znaků.

#### <a name="plan-description"></a>Popis plánu

Popište, co dělá tento plán softwaru jedinečný, a Popište případné rozdíly mezi plány v rámci vaší nabídky. Popište jenom plán, ne nabídku. Popis plánu může obsahovat až 2 000 znaků.

Než budete pokračovat, vyberte **Uložit koncept** .

### <a name="pricing-and-availability"></a>Ceny a dostupnost

V tomto podokně nakonfigurujete:

- Trhy, kde je tento plán k dispozici. Každý plán musí být k dispozici alespoň v jednom [trhu](../marketplace-geo-availability-currencies.md).
- Cena za hodinu
- Zda má být plán viditelný pro všechny uživatele nebo pouze pro konkrétní zákazníky (soukromá cílová skupina).

#### <a name="markets"></a>Trhy

Každý plán musí být k dispozici alespoň v jednom trhu. Zaškrtněte políčko pro každé místo na trhu, kde by měl být tento plán k dispozici k nákupu. (Uživatelé na těchto trzích si stále můžou nabídku nasadit do všech oblastí Azure vybraných v části ["nastavení plánu"](#plan-setup) .) Tlačítko **daň k dani** zobrazuje země nebo oblasti, ve kterých společnost Microsoft prochází prodej a používání daně vaším jménem. Publikování v Číně je omezené na plány, které jsou buď *bezplatné* , nebo *vlastní licence* (BYOL).

Pokud jste už nastavili ceny plánu v měně US dolar (USD) a přidali další místo na trhu, počítá se cena za nový trh podle aktuálních směnných kurzů. Před publikováním si vždycky Projděte cenu za každý trh. Po uložení změn můžete zkontrolovat ceny výběrem možnosti **exportovat ceny (XLSX)** .

Když odeberete trh, zákazníci z tohoto trhu, kteří používají aktivní nasazení, nebudou moci vytvářet nová nasazení nebo škálovat existující nasazení. Existující nasazení nejsou ovlivněna.

#### <a name="pricing"></a>Ceny

V případě **licenčního modelu**vyberte **měsíčně účtované plány založené na využití** , abyste nakonfigurovali ceny pro tento plán, nebo vyberte možnost **Přineste si vlastní licenci** a umožněte zákazníkům používat tento plán s existující licencí.

V případě měsíčního plánu účtovaného podle využití použijte jednu z následujících možností cenové položky:

- **Za jádro**: zadejte ceny za jádro v USD. Společnost Microsoft vypočítá ceny na základě základní velikosti a převede je na místní měny pomocí aktuálního směnného kurzu.
- **Za jadernou velikost**: zadejte ceny podle velikosti jader v USD. Společnost Microsoft vypočítá ceny a převede je na místní měny pomocí aktuálního směnného kurzu.
- **Podle trhu a základní velikosti**: Poskytněte ceny za jednotlivé základní velikosti pro všechny trhy. Ceny můžete importovat z tabulky.

> [!NOTE]
> Uložte cenové změny, abyste mohli exportovat data o cenách. Po zveřejnění ceny za uvedení na trh ve vašem plánu ji nelze později změnit. Abyste měli jistotu, že jsou ceny před publikováním správné, exportujte tabulku s cenami a Prohlédněte si ceny na každém trhu.

#### <a name="free-trial"></a>Bezplatná zkušební verze

Zákazníkům si můžete nabídnout měsíční nebo měsíční *bezplatnou zkušební verzi* .

#### <a name="visibility"></a>Viditelnost

Jednotlivé plány můžete navrhovat tak, aby je bylo možné zobrazit všem uživatelům nebo pouze pro předem vybranou cílovou skupinu. Přiřaďte členství v této omezené cílové skupině pomocí ID předplatných Azure.

**Veřejné**: váš plán uvidí všichni uživatelé.

**Soukromá skupina**: Nastavte si váš plán viditelný jenom pro předvybranou cílovou skupinu. Po zveřejnění jako soukromého plánu můžete cílovou skupinu aktualizovat nebo ji změnit na veřejné. Po zveřejnění plánu musí být veřejný. Nedá se změnit zpátky na soukromý plán.

> [!NOTE]
> Soukromá nebo omezená skupina se liší od cílové skupiny Preview, kterou jste definovali v podokně **náhledu** . Cílová skupina Preview může získat přístup k vaší nabídce _před tím, než_ se publikuje živě do Azure Marketplace. I když se soukromá cílová skupina vztahuje jenom na konkrétní plán, cílová skupina Preview může zobrazit všechny soukromé a veřejné plány pro účely ověření.

**Skupina s omezeným přístupem (ID předplatných Azure)**: přiřaďte cílovou skupinu, která bude mít přístup k tomuto privátnímu plánu, pomocí ID předplatných Azure. Volitelně můžete zahrnout popis každého ID předplatného Azure, které jste přiřadili. Pokud importujete tabulku CSV, přidejte až 10 ID předplatných ručně nebo až 20 000 ID. ID předplatných Azure se reprezentují jako identifikátory GUID a všechna písmena musí být malá.

>[!Note]
>Pro předplatná Azure vytvořená prostřednictvím prodejce programu Cloud Solution Provider (CSP) nejsou podporovány soukromé nabídky.

#### <a name="hide-a-plan"></a>Skrýt plán

Pokud má být váš virtuální počítač používán pouze nepřímo, pokud je odkazován prostřednictvím jiné šablony řešení nebo spravované aplikace, zaškrtněte toto políčko pro publikování virtuálního počítače, ale jeho skrytí od zákazníků, kteří si ho můžou vyhledat nebo procházet přímo.

> [!NOTE]
> Skryté plány nepodporují odkazy ve verzi Preview.

Než budete pokračovat, vyberte **Uložit koncept** .

### <a name="technical-configuration"></a>Technická konfigurace

Poskytněte image a další technické vlastnosti, které jsou spojené s tímto plánem. Další informace najdete v tématu [Vytvoření technického prostředku virtuálního počítače Azure](create-azure-container-technical-assets.md).

> [!NOTE]
> Karta **Technická konfigurace** se nezobrazí, pokud jste nakonfigurovali tento plán pro opětovné použití balíčků z jiného plánu na kartě **Nastavení plánu** .

#### <a name="operating-system"></a>Operační systém

V podokně **operační systém** postupujte takto:

- V případě **řady operačních systémů**vyberte operační systém **Windows** nebo **Linux** .
- U vydaných **verzí** nebo **dodavatelů**vyberte dodavatel Windows Release nebo Linux.
- V případě **popisného názvu operačního**systému zadejte popisný název operačního systému. Tento název je viditelný pro zákazníky.

#### <a name="recommended-vm-sizes"></a>Doporučené velikosti virtuálních počítačů

Vyberte až šest doporučených velikostí virtuálních počítačů, které se zobrazí na Azure Marketplace.

#### <a name="open-ports"></a>Otevřené porty

Otevřete veřejné nebo soukromé porty na nasazeném virtuálním počítači.

#### <a name="storage-option-for-deployment"></a>Možnost úložiště pro nasazení

Pro **možnost nasazení disku**vyberte typ nasazení disku, který můžou vaši zákazníci pro virtuální počítač použít. Microsoft doporučuje omezení nasazení pouze na **spravované disky** .

#### <a name="properties"></a>Vlastnosti

Pokud chcete **podporovat urychlené síťové služby**, vyberte, jestli váš virtuální počítač podporuje [akcelerované síťové služby](https://go.microsoft.com/fwlink/?linkid=2124513).

#### <a name="vm-images"></a>Image virtuálních počítačů

Zadejte verzi disku a identifikátor URI sdíleného přístupového podpisu (SAS) pro image virtuálních počítačů. Přidejte k každé imagi virtuálního počítače až 16 datových disků. Zadejte v zadaném odeslání jenom jednu novou verzi image na plán. Po publikování je obrázek možné upravovat, ale můžete ho odstranit. Odstranění verze zabrání novým i stávajícím uživatelům nasazovat novou instanci odstraněné verze.

- **Verze disku**: verze image, kterou poskytujete.
- **Identifikátor URI SAS**: umístění v účtu služby Azure Storage, kam jste uložili virtuální pevný disk operačního systému. Informace o tom, jak získat identifikátor URI SAS, najdete v tématu [Získání identifikátoru URI sdíleného přístupového podpisu pro vaši image virtuálního počítače](get-sas-uri.md).
- Image datových disků jsou taky identifikátory URI sdíleného přístupového virtuálního pevného disku, které jsou uložené ve svých účtech úložiště Azure.
- Přidá do plánu pouze jeden obrázek pro každé odeslání.

Bez ohledu na to, jaký operační systém používáte, přidejte jenom minimální počet datových disků, které řešení vyžaduje. Během nasazení nemohou zákazníci odebrat disky, které jsou součástí bitové kopie, ale mohou vždy přidávat disky během nebo po nasazení.

Než budete pokračovat, vyberte **Uložit koncept** a vraťte se do **přehledu plánu**.

## <a name="resell-through-csps"></a>Prodávejte dál prostřednictvím CSP

Rozšiřte nabídku své nabídky tím, že ji zpřístupníte partnerům v programu [Cloud Solution Provider (CSP)](https://azure.microsoft.com/offers/ms-azr-0145p/) . K programu se automaticky přihlásí všechny plány BYOL (Přineste si vlastní licenci). Můžete se také rozhodnout, že se chcete vyjádřit k BYOL plánům.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="test-drive"></a>Testovací verze

Nastavte ukázkovou nebo *testovací jednotku*, která zákazníkům umožní vyzkoušení vaší nabídky po určitou dobu, než si ji koupí. Chcete-li vytvořit ukázkové prostředí pro vaše zákazníky, přečtěte si téma [Test Drive nabídky na komerčním webu Marketplace](test-drive.md).

Chcete-li povolit testovací jednotku, zaškrtněte políčko **Povolit testovací jednotku** v podokně **nastavení nabídky** . Pokud chcete ze své nabídky odebrat testovací jednotku, zrušte zaškrtnutí políčka.

Další zdroje testovacích jednotek:

- [Marketingové osvědčené postupy](../what-is-test-drive.md)
- [Technické osvědčené postupy](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Přehled testovacích jednotek](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) Soubor PDF (Ujistěte se, že je vypnutý blokování automaticky otevíraných oken)

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="review-and-publish"></a>Kontrola a publikování

Po dokončení všech požadovaných částí nabídky ji můžete odeslat pro kontrolu a publikování.

V pravém horním rohu vyberte **zkontrolovat a publikovat**.

V tomto podokně můžete:

- Zobrazit stav dokončení pro jednotlivé části nabídky:

  - **Nezahájeno**: oddíl není spuštěný a je potřeba ho dokončit.
  - **Nedokončeno**: oddíl obsahuje chyby, které je třeba opravit, nebo vyžaduje zadání dalších informací. Pokyny k aktualizaci neúplných informací najdete v předchozích částech tohoto článku.
  - **Dokončeno**: oddíl je dokončený a nejsou k dispozici žádné chyby. Aby bylo možné nabídku odeslat, musí být všechny oddíly nabídky dokončené.
- Poskytněte vám **poznámky pro certifikaci** certifikačního týmu, které vám pomůžou zajistit správné testování vaší aplikace. Zahrňte pokyny k testování a všechny doplňkové poznámky, které mohou týmu porozumět vaší aplikaci.

Pokud chcete odeslat nabídku pro publikování, vyberte **zkontrolovat a publikovat**.

Microsoft pošle e-mailovou zprávu, která vám poskytne informace o tom, kdy je verze Preview dostupná pro kontrolu a schválení. Pokud chcete publikovat vaši nabídku na veřejném (nebo, pokud se jedná o soukromou nabídku, publikujte ji na soukromou cílovou skupinu), přejít do partnerského centra, přejít na stránku **Přehled** vaší nabídky a pak vyberte **Přejít-Live**. Stav nabídky se zobrazí v horní části stránky, když probíhá publikování.

### <a name="errors-and-review-feedback"></a>Chyby a kontrola zpětné vazby

Krok **ručního ověření** v procesu publikování představuje rozsáhlou kontrolu vaší nabídky a jejích přidružených technických prostředků. Pokud tento proces zjistí chyby s vaší nabídkou, obdržíte zprávu o certifikaci, která podrobně popisuje problémy. Stačí provést požadované opravy a znovu publikovat vaši nabídku.

## <a name="offer-overview"></a>Přehled nabídky

Na stránce **Přehled nabídky** se zobrazuje vizuální reprezentace kroků, dokončených i probíhajících, které jsou nezbytné k publikování vaší nabídky a dobu, po kterou by každý krok měl být dokončen.

Tato stránka obsahuje také odkazy, které vám pomůžou s touto nabídkou pracovat v závislosti na jeho stavu:

- Pokud je nabídka koncept: [Nabídka odstranit koncept](update-existing-offer.md#delete-a-draft-offer)
- Pokud je nabídka živá: [ukončit prodej nabídky](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Pokud je nabídka ve verzi Preview: [jít-Live](publishing-status.md#publisher-approval)
- Pokud jste nedokončili odhlášení vydavatele: [Zrušit publikování](update-existing-offer.md#cancel-publishing)

## <a name="marketplace-examples"></a>Příklady Marketplace

Tady je příklad toho, jak se zobrazují informace o nabídce v Azure Marketplace:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-offer.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Velké logo
2. Cena
3. Kategorie
4. podmínky a ujednání
5. Adresa zásad ochrany osobních údajů (odkaz)
6. Název nabídky
7. Description
8. Užitečné odkazy
9. Snímky obrazovky a videa

<br>Tady je příklad toho, jak se zobrazují informace nabídky ve výsledcích hledání Azure Marketplace:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-search-results.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Malé logo
2. Název nabídky
3. Souhrn výsledků hledání
4. Zkušební verze

<br>Tady je příklad Azure Marketplace podrobnosti o plánu:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-plan-details.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Název a souhrn plánu
2. Doporučené velikosti virtuálních počítačů
3. Ceny plánu

<br>Tady je příklad toho, jak se zobrazují informace o nabídce v Azure Portal:

:::image type="content" source="media/example-azure-portal-virtual-machine-offer.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Název
2. Popis
3. Užitečné odkazy
4. Snímky obrazovky a videa

<br>Tady je příklad toho, jak se ve výsledcích hledání Azure Portal zobrazí informace o nabídce:

:::image type="content" source="media/example-azure-portal-virtual-machine-search-results.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Malé logo
2. Název nabídky
3. Souhrn výsledků hledání

<br>Tady je příklad podrobností plánu Azure Portal:

:::image type="content" source="media/example-azure-portal-virtual-machine-plan-details.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Název plánu
2. Popis plánu

## <a name="next-step"></a>Další krok

- [Aktualizace stávající nabídky na komerčním webu Marketplace](update-existing-offer.md)
