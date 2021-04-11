---
title: Přesun virtuálních počítačů Azure mezi oblastmi pomocí Azure Resource stěhovací
description: Přečtěte si, jak přesunout virtuální počítače Azure do jiné oblasti pomocí Azure Resource stěhovacího.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5712448c8c5248d3c84ce43f8a41c669355f1d43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565729"
---
# <a name="tutorial-move-azure-vms-across-regions"></a>Kurz: přesunutí virtuálních počítačů Azure napříč oblastmi

V tomto článku se dozvíte, jak přesunout virtuální počítače Azure a související prostředky sítě a úložiště do jiné oblasti Azure pomocí nástroje [Azure Resource stěhovací](overview.md).
.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověřte požadavky a požadavky.
> * Vyberte prostředky, které chcete přesunout.
> * Vyřešte závislosti prostředků.
> * Připravte a přesuňte zdrojovou skupinu prostředků. 
> * Příprava a přesun dalších prostředků.
> * Rozhodněte, zda chcete přesun zrušit nebo potvrdit. 
> * Volitelně můžete po přesunutí odebrat prostředky ve zdrojové oblasti.

> [!NOTE]
> Kurzy ukazují nejrychlejší cestu k vyzkoušení scénáře a používají výchozí možnosti. 

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/). Pak se přihlaste k [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Požadavky
**Požadavek** | **Popis**
--- | ---
**Podpora Resource stěhovací** | [Zkontrolujte](common-questions.md) podporované oblasti a další časté otázky.
**Oprávnění předplatného** | Ověřte, že máte přístup *vlastníka* k předplatnému obsahující prostředky, které chcete přesunout.<br/><br/> **Proč potřebuji přístup k vlastníkovi?** Při prvním přidání prostředku pro konkrétní dvojici zdroje a cíle v rámci předplatného Azure vytvoří [Správce prostředků spravovanou identitu přiřazenou systémem](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (dříve známou jako služba zjištění spravované služby (MSI)), která je pro předplatné důvěryhodná. Pokud chcete vytvořit identitu a přiřadit jí požadovanou roli (přispěvatel nebo správce přístupu uživatele ve zdrojovém předplatném), účet, který použijete k přidání prostředků, potřebuje oprávnění *vlastníka* k tomuto předplatnému. [Přečtěte si další informace](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) o rolích Azure.
**Podpora virtuálních počítačů** |  Ověřte, že virtuální počítače, které chcete přesunout, jsou podporované.<br/><br/> - [Ověřte](support-matrix-move-region-azure-vm.md#windows-vm-support) podporované virtuální počítače s Windows.<br/><br/> - [Ověřte](support-matrix-move-region-azure-vm.md#linux-vm-support) podporované virtuální počítače se systémem Linux a verze jádra.<br/><br/> – Ověřte podporovaná nastavení [COMPUTE](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [úložiště](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)a [sítě](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) .
**Cílové předplatné** | Aby bylo možné vytvořit prostředky, které přesouváte v cílové oblasti, musí předplatné v cílové oblasti mít dostatečnou kvótu. Pokud nemá kvótu, [požádejte o další omezení](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Poplatky za cílovou oblast** | Ověřte ceny a poplatky spojené s cílovou oblastí, do které přesouváte virtuální počítače. Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) vám pomůžeme.
    

## <a name="prepare-vms"></a>Příprava virtuálních počítačů

1. Po kontrole, že virtuální počítače splňují požadavky, se ujistěte, že jsou zapnuté virtuální počítače, které chcete přesunout. Všechny disky virtuálních počítačů, které mají být k dispozici v cílové oblasti, musí být připojeny a inicializovány ve virtuálním počítači.
1. Ujistěte se, že virtuální počítače mají nejnovější důvěryhodné kořenové certifikáty a aktualizovaný seznam odvolaných certifikátů (CRL). Použijte následující postup:
    - Na virtuálních počítačích s Windows nainstalujte nejnovější aktualizace Windows.
    - V případě virtuálních počítačů se systémem Linux postupujte podle pokynů k distributorům, aby počítače měly nejnovější certifikáty a seznam CRL. 
1. Povolení odchozího připojení z virtuálních počítačů:
    - Pokud k řízení odchozího připojení používáte proxy server brány firewall založený na adrese URL, povolte přístup k těmto [adresám URL](support-matrix-move-region-azure-vm.md#url-access) .
    - Pokud k řízení odchozího připojení používáte pravidla skupiny zabezpečení sítě (NSG), vytvořte tato [pravidla značek služeb](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources"></a>Vybrat prostředky 

Vyberte prostředky, které chcete přesunout.

- Zobrazí se všechny podporované typy prostředků ve skupinách prostředků v rámci vybrané zdrojové oblasti.
- Prostředky, které již byly přidány pro přesun mezi oblastmi, nejsou zobrazeny.
- Prostředky přesunete do cílové oblasti ve stejném předplatném jako zdrojová oblast. Pokud chcete změnit předplatné, můžete to udělat po přesunu prostředků.

1. V Azure Portal vyhledejte *prostředek Resource stěhovací*. Pak v části **služby** vyberte **Azure Resource stěhovací**.

    ![Výsledky hledání pro prostředek Resource stěhovací v Azure Portal](./media/tutorial-move-region-virtual-machines/search.png)

2. V **přehledu** klikněte na **Začínáme.**

    ![Tlačítko pro přidání prostředků pro přesun do jiné oblasti](./media/tutorial-move-region-virtual-machines/get-started.png)

3. V části **přesunout prostředky**  >  **zdroj + cíl** vyberte zdrojové předplatné a oblast.
4. V části **cíl** vyberte oblast, do které chcete virtuální počítače přesunout. Potom klikněte na **Další**.

    ![Stránka pro výběr zdrojové a cílové oblasti](./media/tutorial-move-region-virtual-machines/source-target.png)

6. V nabídce **prostředky k přesunutí** klikněte na **vybrat prostředky**.
7. V části **vybrat prostředky** vyberte virtuální počítač. Je možné přidat pouze [prostředky podporované pro Move](#prepare-vms). Pak klikněte na **Hotovo**.

    ![Na stránce vyberte virtuální počítače, které se mají přesunout.](./media/tutorial-move-region-virtual-machines/select-vm.png)

8.  V nabídce **prostředky k přesunutí** klikněte na **Další**.
9. V části **Kontrola** zkontrolujte nastavení zdroje a cíle. 

    ![Stránku ke kontrole nastavení a pokračování v přesunutí](./media/tutorial-move-region-virtual-machines/review.png)
10. Klikněte na **pokračovat** a začněte přidávat prostředky.
11. Po úspěšném dokončení procesu přidávání klikněte na ikonu oznámení na **Přidat prostředky pro přesun** .
12. Po kliknutí na oznámení zkontrolujte prostředky na stránce **napříč oblastmi** .

> [!NOTE]
> - Přidané prostředky jsou ve stavu *Příprava čeká na vyřízení* .
> - Skupina prostředků pro virtuální počítače se přidá automaticky.
> - Pokud chcete odebrat prostředek z kolekce přesunutí, metoda pro to závisí na tom, kde se nacházíte v procesu přesunutí. [Další informace](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Vyřešit závislosti

1. Pokud zdroje zobrazují zprávu *ověřit závislosti* ve sloupci **problémy** , klikněte na tlačítko **ověřit závislosti** . Spustí se proces ověřování.
2. Pokud se najde závislosti, klikněte na **přidat závislosti**. 
3. V části **přidat závislosti** ponechte výchozí možnost **Zobrazit všechny závislosti** .

    - Zobrazit všechny závislosti prochází všemi přímými a nepřímými závislostmi prostředku. Například pro virtuální počítač se zobrazí síťové rozhraní, virtuální síť, skupiny zabezpečení sítě (skupin zabezpečení sítě) atd.
    - Zobrazit závislosti na první úrovni zobrazuje pouze přímé závislosti. Například pro virtuální počítač zobrazuje síťovou kartu, ale ne virtuální síť.


4. Vyberte závislé prostředky, které chcete přidat > **přidat závislosti**. Sledujte průběh oznámení.

    ![Přidat závislosti](./media/tutorial-move-region-virtual-machines/add-dependencies.png)

4. Znovu ověřte závislosti. 
    ![Stránka pro přidání dalších závislostí](./media/tutorial-move-region-virtual-machines/add-additional-dependencies.png)



## <a name="move-the-source-resource-group"></a>Přesunout zdrojovou skupinu prostředků 

Než budete moct připravit a přesunout virtuální počítače, musí být v cílové oblasti přítomna skupina prostředků virtuálních počítačů. 

### <a name="prepare-to-move-the-source-resource-group"></a>Příprava na přesun zdrojové skupiny prostředků

Během procesu přípravy Resource dogeneruje pomocí nastavení skupiny prostředků Azure Resource Manager (ARM) šablony. Prostředky ve skupině prostředků nejsou ovlivněny.

Připravte následujícím způsobem:

1. V **různých oblastech** vyberte zdrojovou skupinu prostředků > **připravit**.
2. V nabídce **Příprava prostředků** klikněte na **připravit**.

    ![Příprava skupiny prostředků](./media/tutorial-move-region-virtual-machines/prepare-resource-group.png)

> [!NOTE]
> Po přípravě skupiny prostředků je stav *zahájit přesun čeká na vyřízení* . 

 
### <a name="move-the-source-resource-group"></a>Přesunout zdrojovou skupinu prostředků

Zahajte přesun následujícím způsobem:

1. V **různých oblastech** vyberte skupinu prostředků, > **zahájit přesun** .
2. LN **přesunout prostředky**, klikněte na **Spustit přesun**. Skupina prostředků se přesune do stavu *zahájení probíhajícího přesunu* .
3. Po zahájení přesunu se vytvoří cílová skupina prostředků na základě vygenerované šablony ARM. Zdrojová skupina prostředků se přesune do stavu *čeká na přesunutí* .

    ![Klikněte na tlačítko Zahájit přesun.](./media/tutorial-move-region-virtual-machines/commit-move-pending.png)

Potvrzení a dokončení procesu přesunutí:

1. V **oblasti napříč oblastmi** vyberte skupinu prostředků > **Potvrdit přesunutí**.
2. LN **přesunout prostředky**, klikněte na **Potvrdit**.

> [!NOTE]
> Po potvrzení přesunutí je zdrojová skupina prostředků ve stavu *čekání na odstranění zdroje* .

## <a name="prepare-resources-to-move"></a>Příprava prostředků k přesunu

Teď, když je zdrojová skupina prostředků přesunutá, můžete připravit na přesun dalších prostředků, které jsou ve stavu *Příprava čeká na vyřízení* .

1. V **různých oblastech** ověřte, že prostředky jsou nyní ve stavu *Příprava čeká na vyřízení* bez problémů. Pokud ne, ověřte je znovu a vyřešte případné nedokončené problémy.

    ![Stránka znázorňující prostředky v připraveném stavu Příprava](./media/tutorial-move-region-virtual-machines/prepare-pending.png)

2. Pokud chcete před zahájením přesunu upravit nastavení cíle, vyberte odkaz ve sloupci **Konfigurace cíle** pro daný prostředek a upravte nastavení. Pokud upravíte nastavení cílového virtuálního počítače, velikost cílového virtuálního počítače by neměla být menší než velikost zdrojového virtuálního počítače.  

Teď, když je zdrojová skupina prostředků přesunutá, můžete připravit na přesun dalších prostředků.

3. Vyberte prostředky, které chcete připravit. 

    ![Stránka pro výběr přípravy na další prostředky](./media/tutorial-move-region-virtual-machines/prepare-other.png)

2. Vyberte **připravit**. 

> [!NOTE]
> - Během procesu přípravy se na virtuální počítače nainstalují agent Azure Site Recovery mobility, aby se replikoval.
> - Data virtuálních počítačů se pravidelně replikují do cílové oblasti. To nemá vliv na zdrojový virtuální počítač.
> - Možnost přesunout prostředek generuje šablony ARM pro ostatní zdrojové prostředky.
> - Po přípravě prostředků se stav *čeká na zahájení přesunu* .

![Stránka znázorňující prostředky v iniciování stavu čeká na přesunutí](./media/tutorial-move-region-virtual-machines/initiate-move-pending.png)


## <a name="initiate-the-move"></a>Zahájit přesun

Díky připraveným prostředkům teď můžete zahájit přesun. 

1. V **různých oblastech** vyberte prostředky se stavem *zahájit přesun čeká na vyřízení*. Pak klikněte na **Spustit přesun**.
2. V nabídce **přesunout prostředky** klikněte na možnost **Spustit přesun**.

    ![Kliknutí pro tlačítko Zahájit přesun](./media/tutorial-move-region-virtual-machines/initiate-move.png)

3. Sledujte průběh přesunu na panelu oznámení.

> [!NOTE]
> - Virtuální počítače repliky se v cílové oblasti vytvoří. Zdrojový virtuální počítač je vypnutý a dojde k nějakému výpadku (obvykle minuty).
> - Stěhovací společnosti znovu vytvoří další prostředky pomocí připravených šablon ARM. Obvykle se nejedná o výpadky.
> - Po přesunutí prostředků se stav přesune do stavu *čeká na potvrzení* .

![Stránka znázorňující prostředky ve zdroji * čeká na odstranění zdroje * stav](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


## <a name="discard-or-commit"></a>Zahodit nebo potvrdit?

Po počátečním přesunu se můžete rozhodnout, jestli chcete přesunutí potvrdit, nebo ho zahodit. 

- **Zahodit**: při testování můžete zrušit jeho přesunutí a nechcete skutečně přesunout zdrojový prostředek. Zrušením přesunutí se daný prostředek vrátí do stavu *zahájení přesunu čeká na vyřízení*.
- **Potvrdit**: potvrzení dokončí přesun do cílové oblasti. Po potvrzení bude zdrojový prostředek ve stavu *čeká na odstranění zdroje* a Vy se můžete rozhodnout, jestli ho chcete odstranit.


## <a name="discard-the-move"></a>Zrušit přesun 

Přesunutí můžete zrušit následujícím způsobem:

1. V **různých oblastech** vyberte prostředky s *potvrzením změny stavu čeká na přesunutí* a klikněte na **zrušit přesun**.
2. V nabídce **Zrušit přesunutí** klikněte na **Zahodit**.
3. Sledujte průběh přesunu na panelu oznámení.


> [!NOTE]
> Po zahození prostředků jsou virtuální počítače v *nedokončeném stavu přesunutí* .

## <a name="commit-the-move"></a>Potvrdit přesun

Pokud chcete dokončit proces přesunutí, potvrďte přesunutí. 

1. V **různých oblastech** vyberte prostředky s *potvrzením změny stavu čeká* na potvrzení a klikněte na **Potvrdit přesunutí**.
2. V nabídce **potvrzení prostředků** klikněte na **Potvrdit**.

    ![Stránka pro potvrzení přesunutí prostředků](./media/tutorial-move-region-virtual-machines/commit-resources.png)

3. Sledujte průběh potvrzení na panelu oznámení.

> [!NOTE]
> - Po potvrzení přesunu se virtuální počítače přestanou replikovat. Zdrojový virtuální počítač není ovlivněný potvrzením změn.
> - Potvrzení změn nemá vliv na zdrojové síťové prostředky.
> - Po potvrzení přesunu jsou prostředky ve stavu *odstranění zdroje, který čeká na vyřízení* .

![Stránka znázorňující prostředky ve zdroji * čeká na odstranění zdroje * stav](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


## <a name="configure-settings-after-the-move"></a>Konfigurovat nastavení po přesunutí

- Služba mobility není automaticky odinstalována z virtuálních počítačů. Odinstalujte ji ručně nebo ji nechte v případě, že plánujete přesunout server znovu.
- Upravte pravidla řízení přístupu na základě role Azure (Azure RBAC) po přesunutí.


## <a name="delete-source-resources-after-commit"></a>Odstranit zdrojové prostředky po potvrzení změn

Po přesunutí můžete případně Odstranit prostředky ve zdrojové oblasti. 

> [!NOTE]
> Několik prostředků, například trezory klíčů a servery SQL Server, nejde odstranit z portálu a musí se odstranit ze stránky vlastností prostředku.

1. V **různých oblastech** klikněte na název zdrojového prostředku, který chcete odstranit.
2. Vyberte **Odstranit zdroj**.

## <a name="delete-additional-resources-created-for-move"></a>Odstranění dalších prostředků vytvořených pro přesun

Po přesunutí můžete ručně odstranit kolekci přesunů a Site Recovery prostředky, které byly vytvořeny.

- Kolekce přesunů je ve výchozím nastavení skrytá. Pokud se chcete podívat, jak to potřebujete, zapněte skryté prostředky.
- Úložiště mezipaměti má zámek, který je třeba odstranit, aby jej bylo možné odstranit.

Odstraňte následujícím způsobem: 
1. Vyhledejte prostředky ve skupině prostředků ```RegionMoveRG-<sourceregion>-<target-region>``` .
2. Ověřte, že se všechny virtuální počítače a další zdrojové prostředky ve zdrojové oblasti přesunuly nebo odstranily. Tím se zajistí, že se nepoužívají žádné nedokončené prostředky.
2. Odstranit prostředky:

    - Název kolekce přesunutí je ```movecollection-<sourceregion>-<target-region>``` .
    - Název účtu úložiště mezipaměti je ```resmovecache<guid>```
    - Název trezoru je ```ResourceMove-<sourceregion>-<target-region>-GUID``` .
## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Virtuální počítače Azure se přesunuly do jiné oblasti Azure.
> * Přesunuly se prostředky přidružené k virtuálním počítačům do jiné oblasti.

Nyní se snažíte přesunout databáze SQL Azure a elastické fondy do jiné oblasti.

> [!div class="nextstepaction"]
> [Přesunout prostředky SQL Azure](./tutorial-move-region-sql.md)
