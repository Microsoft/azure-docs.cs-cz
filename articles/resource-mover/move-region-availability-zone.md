---
title: Přesun virtuálních počítačů Azure do zón dostupnosti v jiné oblasti pomocí Azure Resource stěhovací
description: Přečtěte si, jak přesunout virtuální počítače Azure do zón dostupnosti pomocí Azure Resource stěhovací.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 88006fb354af2673496c6476090d7f73c8a005e6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95542996"
---
# <a name="move-azure-vms-to-an-availability-zone-in-another-region"></a>Přesun virtuálních počítačů Azure do zóny dostupnosti v jiné oblasti

V tomto článku se dozvíte, jak přesunout virtuální počítače Azure (a související prostředky sítě/úložiště) do zóny dostupnosti v jiné oblasti Azure pomocí nástroje [Azure Resource stěhovací](overview.md).

[Zóny dostupnosti Azure](../availability-zones/az-overview.md#availability-zones) vám pomůžou chránit vaše nasazení Azure při selhání datacentra. Každou zónu dostupnosti tvoří jedno nebo několik datových center vybavených nezávislým napájením, chlazením a sítí. Aby se zajistila odolnost, existuje minimálně tři samostatné zóny ve všech [povolených oblastech](../availability-zones/az-region.md). Pomocí nástroje Resource stěhovací můžete přesunout:

- Virtuální počítač s jednou instancí do zóny dostupnosti/skupiny dostupnosti v cílové oblasti.
- Virtuální počítač v dostupnosti nastavený na zónu dostupnosti nebo skupinu dostupnosti v cílové oblasti.
- Virtuální počítač v zóně dostupnosti zdrojové oblasti se zónou dostupnosti v cílové oblasti.


> [!IMPORTANT]
> Prostředek Azure Resource stěhovací je momentálně ve verzi Public Preview.

Pokud chcete přesunout virtuální počítače do jiné zóny dostupnosti ve stejné oblasti, [Přečtěte si tento článek](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

## <a name="prerequisites"></a>Předpoklady

- Přístup *vlastníka* k předplatnému, ve kterém se nacházejí prostředky, které chcete přesunout.
    - Při prvním přidání prostředku pro konkrétní mapování zdrojového a cílového umístění v rámci předplatného Azure vytvoří [Správce prostředků spravovanou identitu přiřazenou systémem](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (dříve známou jako služba zjištění spravované služby (MSI)), která je pro předplatné důvěryhodná.
    - Pokud chcete vytvořit identitu a přiřadit jí požadovanou roli (přispěvatel nebo správce přístupu uživatele ve zdrojovém předplatném), účet, který použijete k přidání prostředků, potřebuje oprávnění *vlastníka* k tomuto předplatnému. [Přečtěte si další informace](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) o rolích Azure.
- K vytvoření zdrojových prostředků v cílové oblasti vyžaduje předplatné dostatečnou kvótu. Pokud ne, požádejte o další omezení. [Přečtěte si další informace](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Ověřte ceny a poplatky spojené s cílovou oblastí, do které přesouváte virtuální počítače. Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) vám pomůžeme.
    


## <a name="check-vm-requirements"></a>Ověřit požadavky virtuálního počítače

1. Ověřte, že virtuální počítače, které chcete přesunout, jsou podporované.

    - [Ověřte](support-matrix-move-region-azure-vm.md#windows-vm-support) podporované virtuální počítače s Windows.
    - [Ověřte](support-matrix-move-region-azure-vm.md#linux-vm-support) podporované virtuální počítače se systémem Linux a verze jádra.
    - Ověřte podporovaná nastavení [COMPUTE](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [úložiště](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)a [sítě](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) .
2. Ověřte, že jsou virtuální počítače, které chcete přesunout, zapnuté.
3. Ujistěte se, že virtuální počítače mají nejnovější důvěryhodné kořenové certifikáty a aktualizovaný seznam odvolaných certifikátů (CRL). 
    - Na virtuálních počítačích Azure s Windows nainstalujte nejnovější aktualizace Windows.
    - Na virtuálních počítačích se systémem Linux postupujte podle pokynů distributora pro Linux a ujistěte se, že má počítač nejnovější certifikáty a seznam CRL. 
4. Povolení odchozího připojení z virtuálních počítačů:
    - Pokud k řízení odchozího připojení používáte proxy server brány firewall založený na adrese URL, povolte přístup k těmto [adresám URL](support-matrix-move-region-azure-vm.md#url-access) .
    - Pokud k řízení odchozího připojení používáte pravidla skupiny zabezpečení sítě (NSG), vytvořte tato [pravidla značek služeb](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Vyberte prostředky, které se mají přesunout.

Vyberte prostředky, které chcete přesunout.

- Můžete vybrat libovolný podporovaný typ prostředku napříč skupinami prostředků ve zdrojové oblasti, kterou vyberete.
- Prostředky přesunete do cílové oblasti v předplatném zdrojové oblasti. Pokud chcete změnit předplatné, můžete to udělat po přesunu prostředků.

1. V Azure Portal vyhledejte prostředek Resource stěhovací. Pak v části **služby** vyberte **Azure Resource stěhovací**.

    ![Vyhledat Resource stěhovací](./media/move-region-availability-zone/search.png)

2. V **přehledu** **vyberte Začínáme**.

    ![Tlačítko pro začátek](./media/move-region-availability-zone/get-started.png)

3. V části **přesunout prostředky**  >  **zdroj + cíl** vyberte zdrojové předplatné a oblast.
4. V části **cíl** vyberte oblast, do které chcete virtuální počítače přesunout. Potom klikněte na **Další**.

     ![Stránka pro vyplnění zdrojového a cílového předplatného/oblasti](./media/move-region-availability-zone/source-target.png)

6. V nabídce **prostředky k přesunutí** klikněte na **vybrat prostředky**.
7. V části **vybrat prostředky** vyberte virtuální počítač. Je možné přidat pouze prostředky podporované pro Move. Pak klikněte na **Hotovo**. V nabídce **prostředky k přesunutí** klikněte na **Další**.

    ![Na stránce vyberte virtuální počítače, které se mají přesunout.](./media/move-region-availability-zone/select-vm.png)
8. V části **Revize a přidat** zkontrolujte nastavení zdroje a cíle.

    ![Stránku ke kontrole nastavení a pokračování v přesunutí](./media/move-region-availability-zone/review.png)

9. Klikněte na **pokračovat** a začněte přidávat prostředky.
10. Po úspěšném dokončení procesu přidávání klikněte na ikonu oznámení na **Přidat prostředky pro přesun** .

    ![Zpráva v oznámeních](./media/move-region-availability-zone/notification.png)

Po kliknutí na oznámení se prostředky zobrazí na stránce **napříč oblastmi** .

> [!NOTE]
> Po kliknutí na oznámení se prostředky zobrazí na stránce **napříč oblastmi** ve stavu *Příprava čeká na vyřízení* .
> - Pokud chcete odebrat prostředek z kolekce přesunutí, metoda pro to závisí na tom, kde se nacházíte v procesu přesunutí. [Přečtěte si další informace](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Vyřešit závislosti

1. Pokud zdroje zobrazují zprávu *ověřit závislosti* ve sloupci **problémy** , klikněte na tlačítko **ověřit závislosti** . Proces ověření probíhá.
2. Pokud se najde závislosti, klikněte na **přidat závislosti**. 
3. V části **přidat závislosti** vyberte závislé prostředky > **přidat závislosti**. Sledujte průběh oznámení.

    ![Tlačítko pro přidání závislostí](./media/move-region-availability-zone/add-dependencies.png)

3. V případě potřeby přidejte další závislosti a znovu ověřte závislosti. 

    ![Stránka pro přidání dalších závislostí](./media/move-region-availability-zone/add-additional-dependencies.png)

4. Na stránce **napříč oblastmi** ověřte, že prostředky jsou nyní ve stavu *Příprava čeká* bez problémů.

    ![Stránka znázorňující prostředky v připraveném stavu Příprava](./media/move-region-availability-zone/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>Přesunout zdrojovou skupinu prostředků 

Než budete moct připravit a přesunout virtuální počítače, musí být zdrojová skupina prostředků přítomna v cílové oblasti. 

### <a name="prepare-to-move-the-source-resource-group"></a>Příprava na přesun zdrojové skupiny prostředků

Připravte následujícím způsobem:

1. V **různých oblastech** vyberte zdrojovou skupinu prostředků > **připravit**.
2. V nabídce **Příprava prostředků** klikněte na **připravit**.

    ![Tlačítko pro přípravu zdrojové skupiny prostředků](./media/move-region-availability-zone/prepare-resource-group.png)

    Během procesu přípravy Resource dogeneruje pomocí nastavení skupiny prostředků Azure Resource Manager (ARM) šablony. Prostředky ve skupině prostředků nejsou ovlivněny.

> [!NOTE]
>  Po přípravě skupiny prostředků je stav *zahájit přesun čeká na vyřízení* . 

![Stav znázorňující zahájení čekání na stav](./media/move-region-availability-zone/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>Přesunout zdrojovou skupinu prostředků

Zahajte přesun následujícím způsobem:

1. V **různých oblastech** vyberte skupinu prostředků, > **zahájit přesun** .
2. LN **přesunout prostředky**, klikněte na **Spustit přesun**. Skupina prostředků se přesune do stavu *zahájení probíhajícího přesunu* .
3. Po zahájení přesunu se vytvoří cílová skupina prostředků na základě vygenerované šablony ARM. Zdrojová skupina prostředků se přesune do stavu *čeká na přesunutí* .

![Stav ukazující přesunutí potvrzení](./media/move-region-availability-zone/commit-move-pending.png)

Potvrzení a dokončení procesu přesunutí:

1. V **různých oblastech** vyberte skupinu prostředků > **potvrzení změn** .
2. LN **přesunout prostředky**, klikněte na **Potvrdit**.

> [!NOTE]
> Po potvrzení přesunutí je zdrojová skupina prostředků ve stavu *čekání na odstranění zdroje* .


## <a name="add-a-target-availability-zone"></a>Přidat cílovou zónu dostupnosti

Před přesunutím zbývajících prostředků nastavíme cílovou zónu dostupnosti pro virtuální počítač.

1. Na stránce **napříč oblastmi** klikněte na odkaz ve sloupci **konfigurace cílového** virtuálního počítače, který přesouváte.

    ![Vlastnosti virtuálního počítače](./media/move-region-availability-zone/select-vm-settings.png)

3. V **nastavení konfigurace** zadejte nastavení pro cílový virtuální počítač. Virtuální počítač v cílové oblasti můžete nakonfigurovat následujícím způsobem:
    -  Vytvořte nový ekvivalentní prostředek v cílové oblasti. S výjimkou nastavení, která zadáte, je cílový prostředek vytvořen se stejným nastavením jako zdroj.
    - Vyberte existující virtuální počítač v cílové oblasti. 

4. V části **zóny** vyberte zónu, do které chcete virtuální počítač umístit při jeho přesunutí.

    Změny se provedou jenom pro prostředky, které upravujete. Všechny závislé prostředky musíte aktualizovat samostatně.

5. V části **SKU** zadejte [úroveň Azure](..//virtual-machines/sizes.md) , kterou chcete přiřadit k cílovému virtuálnímu počítači.
6. V části skupina **dostupnosti** vyberte skupinu dostupnosti, pokud chcete, aby cílový virtuální počítač běžel v rámci skupiny dostupnosti v zóně dostupnosti.
7. Vyberte **Uložit změny**.

    ![nastavení virtuálního počítače](./media/move-region-availability-zone/vm-settings.png)


## <a name="prepare-resources-to-move"></a>Příprava prostředků k přesunu

Teď, když je zdrojová skupina prostředků přesunutá, můžete připravit na přesun dalších prostředků.

1. V **různých oblastech** vyberte prostředky, které chcete připravit. 

    ![Stránka pro výběr přípravy na další prostředky](./media/move-region-availability-zone/prepare-other.png)

2. Vyberte **připravit**. 

> [!NOTE]
> - Během procesu přípravy se na virtuální počítače nainstalují agent Azure Site Recovery mobility pro replikaci.
> - Data virtuálních počítačů se pravidelně replikují do cílové oblasti. To nemá vliv na zdrojový virtuální počítač.
> - Možnost přesunout prostředek generuje šablony ARM pro ostatní zdrojové prostředky.
> - Po přípravě prostředků se stav *čeká na zahájení přesunu* .

![Stránka znázorňující prostředky v iniciování stavu čeká na přesunutí](./media/move-region-availability-zone/initiate-move-pending.png)

## <a name="initiate-the-move"></a>Zahájit přesun

Díky připraveným prostředkům teď můžete zahájit přesun. 

1. V **různých oblastech** vyberte prostředky se stavem *zahájit přesun čeká na vyřízení*. Pak klikněte na **Spustit přesun** .
2. V nabídce **přesunout prostředky** klikněte na možnost **Spustit přesun**.

    ![Stránka pro zahájení přesunu prostředků](./media/move-region-availability-zone/initiate-move.png)

3. Sledujte průběh přesunu na panelu oznámení.


> [!NOTE]
> - Virtuální počítače repliky se v cílové oblasti vytvoří. Zdrojový virtuální počítač je vypnutý a dojde k nějakému výpadku (obvykle minuty).
> - Stěhovací společnosti znovu vytvoří další prostředky pomocí připravených šablon ARM. Obvykle se nejedná o výpadky.
> - Po přípravě prostředků se stav přesune do stavu *čeká na potvrzení* .


![Stránka pro zobrazení prostředků ve stavu přesunutí čeká na vyřízení](./media/move-region-availability-zone/resources-commit-move-pending.png)

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
> V případě virtuálních počítačů se po zahození prostředků v stavech *zahájit přesun čeká na vyřízení* .

## <a name="commit-the-move"></a>Potvrdit přesun

Pokud chcete dokončit proces přesunutí, potvrďte přesunutí. 

1. V **různých oblastech** vyberte prostředky s *potvrzením změny stavu čeká* na potvrzení a klikněte na **Potvrdit přesunutí**.
2. V nabídce **potvrzení prostředků** klikněte na **Potvrdit**.

    ![Stránka pro potvrzení přesunutí prostředků](./media/move-region-availability-zone/commit-resources.png)

3. Sledujte průběh potvrzení na panelu oznámení.

> [!NOTE]
> - Po potvrzení přesunu se virtuální počítače přestanou replikovat. Zdrojový virtuální počítač není ovlivněný potvrzením změn.
> - Potvrzení změn nemá vliv na zdrojové síťové prostředky.
> - Po potvrzení přesunu jsou prostředky ve stavu *odstranění zdroje, který čeká na vyřízení* .

![Stránka znázorňující prostředky ve zdroji * čeká na odstranění zdroje * stav](./media/move-region-availability-zone/delete-source-pending.png)

## <a name="configure-settings-after-the-move"></a>Konfigurovat nastavení po přesunutí

Služba mobility není automaticky odinstalována z virtuálních počítačů. Odinstalujte ji ručně nebo ji nechte v případě, že plánujete přesunout server znovu.


## <a name="delete-source-resources-after-commit"></a>Odstranit zdrojové prostředky po potvrzení změn

Po přesunutí můžete případně Odstranit prostředky ve zdrojové oblasti.

1. V **různých oblastech** klikněte na název každého zdrojového prostředku, který chcete odstranit.
2. Na stránce vlastnosti každého prostředku vyberte možnost **Odstranit**.

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

[Přečtěte si informace o](about-move-process.md) procesu přesunutí.