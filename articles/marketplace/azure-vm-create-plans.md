---
title: Vytvoření plánů nabídky virtuálního počítače na Azure Marketplace
description: Naučte se vytvářet plány pro nabídku virtuálních počítačů na Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: bc5e98484560fcc15e0ea3e289069c84687f158c
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040581"
---
# <a name="how-to-create-plans-for-a-virtual-machine-offer"></a>Vytvoření plánů pro nabídku virtuálního počítače

V partnerském centru můžete v rámci stejné nabídky poskytnout nejrůznější možnosti plánu. Nabídka vyžaduje aspoň jeden plán (dříve označovaný jako SKU), který se může lišit podle finanční zhodnocení publikum, oblasti Azure, funkcí nebo imagí virtuálních počítačů.

Pro každou nabídku můžete vytvořit až 100 plánů: až 45 z nich může být privátní. Další informace o privátních plánech najdete v [části soukromé nabídky na komerčním webu Microsoft Marketplace](private-offers.md).

Po vytvoření plánů vyberte kartu **Přehled plánu** , kterou chcete zobrazit:

- Názvy plánů
- Modely licencí
- Cílová skupina (veřejná nebo soukromá)
- Stav aktuálního publikování
- Dostupné akce

Akce, které jsou k dispozici v podokně **Přehled plánu** , se liší v závislosti na aktuálním stavu plánu.

- Pokud je stav plánu koncept, vyberte **Odstranit koncept** .
- Pokud je stav plánu publikovaný v reálném čase, vyberte **zastavit prodej plánu** nebo **synchronizovat soukromou cílovou skupinu** .

## <a name="create-a-new-plan"></a>Vytvořit nový plán

V horní části vyberte **vytvořit nový plán** . Zobrazí se dialogové okno **Nový plán** .

V poli **ID plánu** Vytvořte jedinečné ID plánu pro každý plán v této nabídce. Toto ID bude pro zákazníky viditelné na webové adrese produktu. Používejte jenom malá písmena a číslice, pomlčky nebo podtržítka a maximálně 50 znaků.

> [!NOTE]
> Po zvolení možnost **vytvořit** se ID plánu nedá změnit.

Do pole **název plánu** zadejte název pro tento plán. Zákazníci uvidí tento název, když rozhodují, který plán vybrat v rámci vaší nabídky. Vytvořte jedinečný název, který bude jasně ukazovat rozdíly mezi plány. Můžete například zadat **Windows Server** s plány průběžných *plateb* , *BYOL* , *Advanced* a *Enterprise* .

Vyberte **Vytvořit** .

## <a name="plan-setup"></a>Nastavení plánu

Nastavte na nejvyšší úrovni konfiguraci pro typ plánu, určete, zda znovu používá technickou konfiguraci z jiného plánu, a Identifikujte oblasti Azure, ve kterých má být plán k dispozici. Vaše výběry určují, která pole se zobrazí v ostatních podoknech pro stejný plán.

### <a name="reuse-a-technical-configuration"></a>Opětovné použití technické konfigurace

Pokud máte více než jeden plán stejného typu a jsou mezi nimi identické balíčky, můžete vybrat tento plán, který znovu **použije technickou konfiguraci z jiného plánu** . Tato možnost umožňuje vybrat jeden z dalších plánů stejného typu pro tuto nabídku a umožňuje znovu použít jeho technickou konfiguraci.

> [!NOTE]
> Když znovu použijete technickou konfiguraci z jiného plánu, z tohoto plánu zmizí karta s celou **technickou konfigurací** . Pro tento plán se použijí i podrobnosti technické konfigurace z druhého plánu, včetně všech aktualizací, které v budoucnu provedete. Toto nastavení nelze po publikování plánu změnit.

### <a name="azure-regions"></a>Oblast Azure

Váš plán se musí zpřístupnit aspoň v jedné oblasti Azure.

Vyberte **globální možnost Azure** a zpřístupněte svůj plán zákazníkům ve všech globálních oblastech Azure, které mají integraci komerčního tržiště. Další informace najdete v tématu [geografická dostupnost a podpora měny](marketplace-geo-availability-currencies.md).

Vyberte možnost **Azure Government** pro zpřístupnění vašeho plánu v oblasti [Azure Government](../azure-government/documentation-government-welcome.md) . Tato oblast poskytuje zákazníkům řízený přístup na federální, státní, místní nebo samosprávnéové entity USA a také pro partnery, kteří jim mají nárok na jejich obsluhu. Jako vydavatel zodpovídáte za řízení dodržování předpisů, míry zabezpečení a osvědčené postupy. Azure Government používá fyzicky izolovaná datová centra a sítě (jenom v USA).

Před publikováním [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md), otestujte a ověřte svůj plán v prostředí, protože některé koncové body se mohou lišit. Pokud chcete nastavit a otestovat svůj plán, vyžádejte si zkušební účet ze stránky [Microsoft Azure Government zkušební verze](https://azure.microsoft.com/global-infrastructure/government/request/) .

> [!NOTE]
> Po publikování a dostupnosti plánu v konkrétní oblasti Azure tuto oblast nemůžete odebrat.

### <a name="azure-government-certifications"></a>Azure Government certifikace

Tato možnost se zobrazí jenom v případě, že jste v předchozí části vybrali **Azure Government** jako oblast Azure.

Služba Azure Government Services zpracovává data, která se vztahují na určité předpisy a požadavky státní správy. Příklad: FedRAMP, NIST 800,171 (DIB), ITAR, finanční úřad 1075, DoD – L4 a CJIS. Pro zajištění povědomí o certifikaci pro tyto programy můžete poskytnout až 100 odkazů, které je popisují. Můžou to být buď odkazy na váš výpis v programu, nebo odkazy na popisy dodržování předpisů s nimi na vašich vlastních webech. Tyto odkazy jsou viditelné pouze pro Azure Government zákazníky.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="plan-listing"></a>Výpis plánu

V této části nakonfigurujete podrobnosti o seznamu plánu. V tomto podokně se zobrazují konkrétní informace, které se mohou lišit od jiných plánů v rámci stejné nabídky.

### <a name="plan-name"></a>Název plánu

Toto pole je automaticky vyplněno názvem, který jste přiřadili vašemu plánu při jeho vytvoření. Tento název se zobrazí v Azure Marketplace jako název tohoto plánu. Je omezený na 100 znaků.

### <a name="plan-summary"></a>Souhrn plánu

Zadejte stručný souhrn vašeho plánu, nikoli nabídku. Tento souhrn je omezený na 100 znaků.

### <a name="plan-description"></a>Popis plánu

Popište, co dělá tento plán softwaru jedinečný, a Popište případné rozdíly mezi plány v rámci vaší nabídky. Popište jenom plán, ne nabídku. Popis plánu může obsahovat až 2 000 znaků.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="pricing-and-availability"></a>Ceny a dostupnost

V tomto podokně nakonfigurujete:

- Trhy, kde je tento plán k dispozici. Každý plán musí být k dispozici alespoň v jednom [trhu](marketplace-geo-availability-currencies.md).
- Cena za hodinu
- Zda má být plán viditelný pro všechny uživatele nebo pouze pro konkrétní zákazníky (soukromá cílová skupina).

### <a name="markets"></a>Trhy

Každý plán musí být k dispozici alespoň v jednom trhu. Zaškrtněte políčko pro každé místo na trhu, kde by měl být tento plán k dispozici k nákupu. (Uživatelé na těchto trzích si stále můžou nabídku nasadit do všech oblastí Azure vybraných v části ["nastavení plánu"](#plan-setup) .) Tlačítko **daň k dani** zobrazuje země nebo oblasti, ve kterých společnost Microsoft prochází prodej a používání daně vaším jménem. Publikování v Číně je omezené na plány, které jsou buď *bezplatné* , nebo *vlastní licence* (BYOL).

Pokud jste už nastavili ceny plánu v měně US dolar (USD) a přidali další místo na trhu, počítá se cena za nový trh podle aktuálních směnných kurzů. Před publikováním si vždycky Projděte cenu za každý trh. Po uložení změn můžete zkontrolovat ceny výběrem možnosti **exportovat ceny (XLSX)** .

Když odeberete trh, zákazníci z tohoto trhu, kteří používají aktivní nasazení, nebudou moci vytvářet nová nasazení nebo škálovat existující nasazení. Existující nasazení nejsou ovlivněna.

### <a name="pricing"></a>Ceny

V případě **licenčního modelu** vyberte **měsíčně účtované plány založené na využití** , abyste nakonfigurovali ceny pro tento plán, nebo vyberte možnost **Přineste si vlastní licenci** a umožněte zákazníkům používat tento plán s existující licencí.

V případě měsíčního plánu účtovaného podle využití použijte jednu z následujících možností cenové položky:

- **Za jádro** : zadejte ceny za jádro v USD. Společnost Microsoft vypočítá ceny na základě základní velikosti a převede je na místní měny pomocí aktuálního směnného kurzu.
- **Za jadernou velikost** : zadejte ceny podle velikosti jader v USD. Společnost Microsoft vypočítá ceny a převede je na místní měny pomocí aktuálního směnného kurzu.
- **Podle trhu a základní velikosti** : Poskytněte ceny za jednotlivé základní velikosti pro všechny trhy. Ceny můžete importovat z tabulky.

> [!NOTE]
> Uložte cenové změny, abyste mohli exportovat data o cenách. Po zveřejnění ceny za uvedení na trh ve vašem plánu ji nelze později změnit. Abyste měli jistotu, že jsou ceny před publikováním správné, exportujte tabulku s cenami a Prohlédněte si ceny na každém trhu.

### <a name="free-trial"></a>Bezplatná zkušební verze

Zákazníkům si můžete nabídnout měsíční nebo měsíční *bezplatnou zkušební verzi* .

### <a name="visibility"></a>Viditelnost

Jednotlivé plány můžete navrhovat tak, aby je bylo možné zobrazit všem uživatelům nebo pouze pro předem vybranou cílovou skupinu. Přiřaďte členství v této omezené cílové skupině pomocí ID předplatných Azure.

**Veřejné** : váš plán uvidí všichni uživatelé.

**Soukromá skupina** : Nastavte si váš plán viditelný jenom pro předvybranou cílovou skupinu. Po zveřejnění jako soukromého plánu můžete cílovou skupinu aktualizovat nebo ji změnit na veřejné. Po zveřejnění plánu musí být veřejný. Nedá se změnit zpátky na soukromý plán.

> [!NOTE]
> Soukromá nebo omezená skupina se liší od cílové skupiny Preview, kterou jste definovali v podokně **náhledu** . Cílová skupina Preview může získat přístup k vaší nabídce _před tím, než_ se publikuje živě do Azure Marketplace. I když se soukromá cílová skupina vztahuje jenom na konkrétní plán, cílová skupina Preview může zobrazit všechny soukromé a veřejné plány pro účely ověření.

**Skupina s omezeným přístupem (ID předplatných Azure)** : přiřaďte cílovou skupinu, která bude mít přístup k tomuto privátnímu plánu, pomocí ID předplatných Azure. Volitelně můžete zahrnout popis každého ID předplatného Azure, které jste přiřadili. Pokud importujete tabulku CSV, přidejte až 10 ID předplatných ručně nebo až 20 000 ID. ID předplatných Azure se reprezentují jako identifikátory GUID a všechna písmena musí být malá.

>[!Note]
>Pro předplatná Azure vytvořená prostřednictvím prodejce programu Cloud Solution Provider (CSP) nejsou podporovány soukromé nabídky.

### <a name="hide-a-plan"></a>Skrýt plán

Pokud má být váš virtuální počítač používán pouze nepřímo, pokud je odkazován prostřednictvím jiné šablony řešení nebo spravované aplikace, zaškrtněte toto políčko pro publikování virtuálního počítače, ale jeho skrytí od zákazníků, kteří si ho můžou vyhledat nebo procházet přímo.

> [!NOTE]
> Skryté plány nepodporují odkazy ve verzi Preview.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="technical-configuration"></a>Technická konfigurace

Poskytněte image a další technické vlastnosti, které jsou spojené s tímto plánem. Další informace najdete v tématu [Konfigurace informací o seznamu nabídek virtuálních počítačů](azure-vm-create-listing.md).

> [!NOTE]
> Karta **Technická konfigurace** se nezobrazí, pokud jste nakonfigurovali tento plán pro opětovné použití balíčků z jiného plánu na kartě **Nastavení plánu** .

### <a name="operating-system"></a>Operační systém

V podokně **operační systém** postupujte takto:

- V případě **řady operačních systémů** vyberte operační systém **Windows** nebo **Linux** .
- U vydaných **verzí** nebo **dodavatelů** vyberte dodavatel Windows Release nebo Linux.
- V případě **popisného názvu operačního** systému zadejte popisný název operačního systému. Tento název je viditelný pro zákazníky.

### <a name="recommended-vm-sizes"></a>Doporučené velikosti virtuálních počítačů

Vyberte až šest doporučených velikostí virtuálních počítačů, které se zobrazí na Azure Marketplace.

### <a name="open-ports"></a>Otevřené porty

Otevřete veřejné nebo soukromé porty na nasazeném virtuálním počítači.

### <a name="storage-option-for-deployment"></a>Možnost úložiště pro nasazení

Pro **možnost nasazení disku** vyberte typ nasazení disku, který můžou vaši zákazníci pro virtuální počítač použít. Microsoft doporučuje omezení nasazení pouze na **spravované disky** .

### <a name="properties"></a>Vlastnosti

Pokud chcete **podporovat urychlené síťové služby** , vyberte, jestli váš virtuální počítač podporuje [akcelerované síťové služby](https://go.microsoft.com/fwlink/?linkid=2124513).

### <a name="generations"></a>Všechna

Vygenerování virtuálního počítače definuje virtuální hardware, který používá. Na základě potřeb vašeho zákazníka můžete publikovat virtuální počítač 1. generace, virtuální počítač 2. generace nebo obojí.

1. Při vytváření nové nabídky vyberte **typ generace** a zadejte požadované podrobnosti o imagi:

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details.png" alt-text="Zobrazení rozevíracího seznamu generování":::

2. Pokud chcete do plánu přidat další generaci, vyberte **Přidat generaci** :

    :::image type="content" source="./media/create-vm/azure-vm-generations-add.png" alt-text="Zobrazení rozevíracího seznamu generování":::

    Pak zadejte podrobnosti generování:

    :::image type="content" source="./media/create-vm/azure-vm-generations-details.png" alt-text="Zobrazení rozevíracího seznamu generování":::

    **ID generování** , které zvolíte, bude zákazníkům viditelné na místech, jako jsou adresy URL produktů a šablony ARM (pokud jsou k dispozici). Používejte jenom malá písmena, alfanumerické znaky, pomlčky nebo podtržítka. Po publikování ji nelze změnit.

3. Chcete-li aktualizovat existující virtuální počítač, který má již vytvořenou generaci 1, jednoduše upravte podrobnosti na stránce **Technická konfigurace** :

    :::image type="content" source="./media/create-vm/azure-vm-generations-updating.png" alt-text="Zobrazení rozevíracího seznamu generování":::

Další informace o rozdílech mezi možnostmi generace 1 a generace 2 najdete v tématu [Podpora virtuálních počítačů 2. generace v Azure](../virtual-machines/generation-2.md).

### <a name="vm-images"></a>Image virtuálních počítačů

Zadejte verzi disku a identifikátor URI sdíleného přístupového podpisu (SAS) pro image virtuálních počítačů. Přidejte k každé imagi virtuálního počítače až 16 datových disků. Zadejte v zadaném odeslání jenom jednu novou verzi image na plán. Po publikování je obrázek možné upravovat, ale můžete ho odstranit. Odstranění verze zabrání novým i stávajícím uživatelům nasazovat novou instanci odstraněné verze.

- **Verze disku** : verze image, kterou poskytujete.
- **Identifikátor URI SAS** : umístění v účtu služby Azure Storage, kam jste uložili virtuální pevný disk operačního systému. Informace o tom, jak získat identifikátor URI SAS, najdete v tématu [Získání identifikátoru URI sdíleného přístupového podpisu pro vaši image virtuálního počítače](azure-vm-get-sas-uri.md).
- Image datových disků jsou taky identifikátory URI sdíleného přístupového virtuálního pevného disku, které jsou uložené ve svých účtech úložiště Azure.
- Přidá do plánu pouze jeden obrázek pro každé odeslání.

Bez ohledu na to, jaký operační systém používáte, přidejte jenom minimální počet datových disků, které řešení vyžaduje. Během nasazení nemohou zákazníci odebrat disky, které jsou součástí bitové kopie, ale mohou vždy přidávat disky během nebo po nasazení.

Než budete pokračovat, vyberte **Uložit koncept** a vraťte se do **přehledu plánu** .

## <a name="next-steps"></a>Další kroky

- [Přidání cílové skupiny pro verzi Preview](azure-vm-create-preview.md)
