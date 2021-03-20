---
title: Plánování nabídky virtuálních počítačů – komerční tržiště Microsoftu
description: Tento článek popisuje požadavky na publikování nabídky virtuálního počítače pro Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: 9169d691b58ad4a59796123c64f856128a0f8c83
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879322"
---
# <a name="how-to-plan-a-virtual-machine-offer"></a>Postup plánování nabídky virtuálního počítače

Tento článek popisuje různé možnosti a požadavky pro publikování nabídky virtuálního počítače na komerčním webu Marketplace. Nabídky virtuálních počítačů jsou nasazené a účtované prostřednictvím Azure Marketplace.

Než začnete, [Vytvořte si účet komerčního tržiště v partnerském centru](./partner-center-portal/create-account.md) a ujistěte se, že je váš účet zaregistrovaný v programu pro komerční Marketplace.

### <a name="technical-fundamentals"></a>Technické základy

Proces navrhování, sestavování a testování nabídek trvá čas a vyžaduje odbornost na platformě Azure i v technologiích, které slouží k sestavování vaší nabídky. Váš technický tým by měl mít praktické znalosti o sítích [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)a [Azure](https://azure.microsoft.com/services/?filter=networking#networking)a také o [návrhu a architektuře aplikací Azure](https://azure.microsoft.com/solutions/architecture/). Podívejte se na tyto další technické materiály: 

- Kurzy
  - [Virtuální počítače s Linuxem](../virtual-machines/linux/tutorial-manage-vm.md)
  - [Virtuální počítače s Windows](../virtual-machines/windows/tutorial-manage-vm.md)

- ukázky
  - [Ukázky v Azure CLI pro virtuální počítače se systémem Linux](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Azure PowerShell pro virtuální počítače se systémem Linux](https://github.com/Azure/azure-docs-powershell-samples/tree/master/virtual-machine)
  - [Ukázky v Azure CLI pro virtuální počítače s Windows](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Azure PowerShell pro virtuální počítače s Windows](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

## <a name="technical-requirements"></a>Technické požadavky

Nabídky virtuálních počítačů mají následující technické požadavky:

- Musíte připravit jeden virtuální pevný disk operačního systému (VHD). Virtuální pevné disky datového disku jsou volitelné. Tato informace je podrobněji vysvětlena níže.
- Zákazník může vaši nabídku kdykoli zrušit.
- Musíte vytvořit aspoň jeden plán pro vaši nabídku. Ceny plánu se účtují na základě vámi vybrané [možnosti licencování](#licensing-options) .
   > [!IMPORTANT]
   > Každá image virtuálního počítače v plánu musí mít stejný počet datových disků.

Virtuální počítač obsahuje dvě komponenty:

- **Operační virtuální pevný disk** – obsahuje operační systém a řešení, které se nasadí s vaší nabídkou. Proces přípravy VHD se liší v závislosti na tom, jestli se jedná o virtuální počítač se systémem Linux-, Windows nebo vlastní.
- **Virtuální pevné disky** (volitelné) datového disku – vyhrazené a trvalé úložiště pro virtuální počítač. Nepoužívejte virtuální pevný disk operačního systému (například jednotku C:) k ukládání trvalých informací. 
    - Můžete zahrnout až 16 datových disků.
    - Použijte jeden virtuální pevný disk na datový disk, a to i v případě, že je disk prázdný.

    > [!NOTE]
    > Bez ohledu na to, jaký operační systém používáte, přidejte jenom minimální počet datových disků, které řešení potřebuje. Zákazníci nemohou odebrat disky, které jsou součástí bitové kopie v době nasazení, ale mohou vždy přidávat disky během nebo po nasazení.

Podrobné pokyny k přípravě technických prostředků najdete v tématu [Vytvoření virtuálního počítače pomocí schválené základny](azure-vm-create-using-approved-base.md) nebo [Vytvoření virtuálního počítače pomocí vlastní image](azure-vm-create-using-own-image.md).

## <a name="preview-audience"></a>Cílová skupina Preview

Cílová skupina Preview má přístup k vaší nabídce virtuálních počítačů před tím, než se publikuje živě v Azure Marketplace, aby bylo možné otestovat kompletní funkce před jejich publikováním v reálném čase. Na stránce **Preview cílovou skupinu** můžete definovat skupinu omezené verze Preview. 

> [!NOTE]
> Cílová skupina verze Preview se liší od privátního plánu. Soukromý plán je ten, který zpřístupníte jenom pro konkrétní cílovou skupinu, kterou zvolíte. To vám umožní vyjednat vlastní plán s konkrétními zákazníky. Další informace najdete v další části: plány.

Pozvánky můžete odesílat do e-mailových adres účtu Microsoft (MSA) nebo Azure Active Directory (Azure AD). Přidejte až 10 e-mailových adres ručně nebo importujte až 20 souborů. csv. Pokud je vaše nabídka už živá, můžete i nadále definovat cílovou skupinu verze Preview pro testování změn nebo aktualizací vaší nabídky.

## <a name="plans-and-pricing"></a>Plány a ceny

Nabídka virtuálních počítačů vyžaduje aspoň jeden plán. Plán definuje rozsah a omezení řešení a související ceny. Můžete vytvořit více plánů pro vaši nabídku a poskytnout zákazníkům různé možnosti technického a licencování a také bezplatné zkušební verze. Obecné pokyny k plánům, včetně cenových modelů, bezplatných zkušebních verzí a privátních plánů, najdete v tématu [plány a ceny pro komerční web Marketplace](plans-pricing.md) . 

Virtuální počítače jsou plně aktivní a využívají licenční modely (BYOL) s průběžnými platbami nebo s vlastními licencemi. Společnost Microsoft hostuje transakci obchodu a účtuje vaše zákazníky vaším jménem. Výhody používání upřednostňovaného platebního vztahu mezi zákazníkem a Microsoftem, včetně všech smluv Enterprise, získáte s výhodou. Další informace najdete v tématu [Možnosti Transact](./marketplace-commercial-transaction-capabilities-and-considerations.md)-The pro prodej na webu Marketplace.

> [!NOTE]
> Předplatné Azure (dříve označované jako peněžní závazek), které je přidružené k smlouva Enterprise, se dá použít ve vašem VIRTUÁLNÍm počítači s využitím Azure, ale ne na licenční poplatky za software.

### <a name="licensing-options"></a>Možnosti licencování

Při přípravě na publikování nové nabídky virtuálních počítačů se musíte rozhodnout, kterou možnost licencování zvolit. Tím určíte, které další informace budete později potřebovat k dispozici při vytváření vaší nabídky v partnerském centru.

Toto jsou dostupné možnosti licencování pro nabídky virtuálních počítačů:

| Možnost licence | Proces transakce |
| --- | --- |
| Bezplatná zkušební verze | Poskytněte zákazníkům jednu, tři nebo šest měsíců bezplatné zkušební období. |
| Testovací jednotka | Tato možnost zákazníkům umožní vyhodnotit virtuální počítače bez dalších poplatků. Nemusejí být stávajícím zákazníkem Azure, aby mohli začít s zkušebním prostředím. Podrobnosti najdete v tématu [co je testovací jednotka?](./what-is-test-drive.md) . |
| BYOL | Možnost Přineste si vlastní licencování umožní vašim zákazníkům přenášet do Azure existující softwarové licence.\* |
| Na základě využití | Tato možnost se také označuje jako průběžné platby. Tato možnost umožňuje zákazníkům platit za hodinu. |
| Interaktivní ukázka  | Poskytněte svým zákazníkům zkušenosti s vaším řešením pomocí interaktivní ukázky. Výhodou je, že můžete nabízet zkušební prostředí, aniž byste museli poskytovat složitou instalaci komplexního řešení. |
|

\* Jako vydavatel podporujete všechny aspekty licenčních licencí k softwaru, včetně (ale ne omezeného) objednávky, plnění, měření, fakturace, fakturace, platby a shromažďování.

Následující příklad ukazuje nabídku virtuálních počítačů v Azure Marketplace, která má ceny na základě využití.

:::image type="content" source="media/vm/sample-offer-screen.png" alt-text="Ukázková obrazovka nabídky virtuálních počítačů":::

### <a name="private-plans"></a>Soukromé plány

Můžete omezit zjišťování a nasazování virtuálního počítače na konkrétní skupinu zákazníků publikováním image a cen jako privátního plánu. Soukromé plány odemknou možnost vytvářet exkluzivní nabídky pro nejbližší zákazníky a nabízet přizpůsobený software a požadavky. Přizpůsobené výrazy vám umožňují zvýraznit celou řadu scénářů, včetně jejich využívání se specializovanými cenami a podmínkami, a také prvotním přístupem k omezenému softwaru pro vydání. Soukromé plány umožňují poskytovat konkrétní ceny nebo produkty omezené sadě zákazníků.

Další informace najdete v tématu [plány a ceny pro komerční nabídky na webu Marketplace](plans-pricing.md) a [soukromé nabídky na komerčním webu Microsoft Marketplace](private-offers.md).

## <a name="test-drives"></a>Testovací verze

Pro virtuální počítač se můžete rozhodnout pro povolení testovacích jednotek. Testovací jednotky poskytují zákazníkům přístup k předkonfigurovanému prostředí po dobu určitého počtu hodin. Můžete povolit testovací jednotky pro libovolnou možnost publikování, ale tato funkce má další požadavky. Další informace o testovacích jednotkách najdete v tématu [co je testovací jednotka?](what-is-test-drive.md). Informace o konfiguraci různých druhů testovacích jednotek najdete v tématu [Technická konfigurace testovacích](test-drive-technical-configuration.md)jednotek.

> [!TIP]
> Testovací jednotka se liší od [bezplatné zkušební verze](plans-pricing.md#free-trials). Můžete nabízet testovací, bezplatnou zkušební verzi nebo obojí. Poskytují zákazníkům vaše řešení po dobu určitou dobu. Testovací jednotka ale taky obsahuje praktickou a samoobslužnou prohlídku klíčových funkcí vašeho produktu a výhod, které se provedou ve scénáři implementace reálného světa.

## <a name="customer-leads"></a>Potenciální zákazníci

Abyste mohli shromažďovat informace o zákaznících, musíte vaši nabídku připojit k systému správy vztahů se zákazníky (CRM). Zákazník bude požádán o oprávnění ke sdílení svých informací. Tyto podrobnosti o zákaznících spolu s názvem nabídky, ID a online obchodem, kde si vaši nabídku našli, budou odeslány do systému CRM, který jste nakonfigurovali. Komerční tržiště podporuje nejrůznější systémy CRM, společně s možností použití tabulky Azure nebo konfigurace koncového bodu HTTPS pomocí Power automatizuje.

Připojení k CRM můžete kdykoli přidat nebo změnit během vytváření nabídky nebo po ní. Podrobné pokyny najdete v [zákaznických zákaznících z nabídky komerčních webů na webu Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="legal-contracts"></a>Právní smlouvy

Pro zjednodušení procesu nákupu pro zákazníky a omezení právní složitosti pro dodavatele softwaru nabízí společnost Microsoft standardní kontrakt, který můžete použít pro vaše nabídky na komerčním webu Marketplace. Když nabízený software obdržíte na základě standardního kontraktu, zákazníci ho musí přečíst a přijmout jenom jednou a vy nemusíte vytvářet vlastní podmínky.

Pokud se rozhodnete použít standardní smlouvu, máte možnost přidat do standardní smlouvy univerzální změny a až 10 vlastních změn. Místo standardní smlouvy můžete použít také vlastní podmínky a ujednání. Tyto podrobnosti budete spravovat na stránce **vlastnosti** . Podrobné informace najdete v tématu [standardní smlouva pro komerční tržiště Microsoftu](standard-contract.md).

> [!NOTE]
> Po publikování nabídky pomocí standardní smlouvy pro komerční web Marketplace nemůžete použít vlastní podmínky a ujednání. Jedná se o scénář "nebo". Vaše řešení si buď nabídnete v rámci standardní smlouvy, nebo podle vašich vlastních podmínek. Pokud chcete upravit podmínek standardní smlouvy, můžete to udělat prostřednictvím standardních změn smlouvy.

## <a name="cloud-solution-providers"></a>Poskytovatelé cloudových řešení

Při vytváření vaší nabídky v partnerském centru se zobrazí karta pro **prodej** na kartě CSP. Tato možnost umožňuje partnerům, kteří jsou součástí programu Microsoft Cloud poskytovatelé řešení (CSP), aby váš virtuální počítač mohli dál prodávat jako součást sady prostředků v rámci sady. K programu se automaticky přihlásí všechny plány BYOL (Přineste si vlastní licenci). Můžete se také rozhodnout, že se chcete vyjádřit k BYOL plánům. Další informace najdete v tématu [program Cloud Solution Provider](cloud-solution-providers.md) . 

> [!NOTE]
> Výslovný souhlas kanálu partner Cloud Solution Provider (CSP) je nyní k dispozici. Další informace o marketingu vaší nabídky prostřednictvím kanálů partnerů Microsoft CSP najdete v tématu [**poskytovatelé Cloud Solution Provider**](./cloud-solution-providers.md).

## <a name="next-steps"></a>Další kroky

- [Vytvoření nabídky virtuálního počítače na Azure Marketplace](azure-vm-create.md)
- [Vytvořte virtuální počítač pomocí schválené základny](azure-vm-create-using-approved-base.md) nebo [vytvořte virtuální počítač s použitím vlastní image](azure-vm-create-using-own-image.md).
- [Osvědčené postupy nabídky](gtm-offer-listing-best-practices.md)