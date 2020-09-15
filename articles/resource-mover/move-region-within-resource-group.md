---
title: Přesunutí prostředků do jiné oblasti pomocí Azure Resource stěhovací
description: Přečtěte si, jak přesunout prostředky v rámci skupiny prostředků do jiné oblasti pomocí Azure Resource stěhovací.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 716928761d23c2cf04ebcc72e253ad7884408065
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90061834"
---
# <a name="move-resources-across-regions-from-resource-group"></a>Přesunutí prostředků mezi oblasti (ze skupiny prostředků)

V tomto článku se dozvíte, jak přesunout prostředky z konkrétní skupiny prostředků do jiné oblasti Azure. Ve skupině prostředků vyberte prostředky, které chcete přesunout. Pak je přesunete pomocí nástroje [Azure Resource stěhovací](overview.md).

> [!IMPORTANT]
> Prostředek Azure Resource stěhovací je momentálně ve verzi Public Preview.


## <a name="prerequisites"></a>Předpoklady

- Pro předplatné, ve kterém se nacházejí prostředky, které chcete přesunout, potřebujete přístup *vlastníka* .
    - Při prvním přidání prostředku pro konkrétní mapování zdrojového a cílového umístění v rámci předplatného Azure vytvoří [Správce prostředků spravovanou identitu přiřazenou systémem](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (dříve známou jako služba zjištění spravované služby (MSI)), která je pro předplatné důvěryhodná.
    - Pokud chcete vytvořit identitu a přiřadit jí požadovanou roli (přispěvatel nebo správce přístupu uživatele ve zdrojovém předplatném), účet, který použijete k přidání prostředků, potřebuje oprávnění *vlastníka* k tomuto předplatnému. [Přečtěte si další informace](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) o rolích Azure.
- K vytvoření zdrojových prostředků v cílové oblasti vyžaduje předplatné dostatečnou kvótu. Pokud ne, požádejte o další omezení. [Přečtěte si další informace](/azure/azure-resource-manager/management/azure-subscription-service-limits).
- Ověřte ceny a poplatky spojené s cílovou oblastí, do které přesouváte virtuální počítače. Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) vám pomůžeme.
- Přečtěte si, že prostředky, které chcete přesunout, jsou podporovány nástrojem Resource stěhovací:
    - Virtuální počítače Azure a přidružené disky
    - Síťové karty
    - Skupiny dostupnosti
    - Virtuální sítě Azure
    - Veřejné IP adresy
    - Skupiny zabezpečení sítě (NSG)
    - Interní a veřejné nástroje pro vyrovnávání zatížení
    - Databáze SQL Azure a elastické fondy


## <a name="check-vm-requirements"></a>Ověřit požadavky virtuálního počítače

1. Ověřte, že virtuální počítače, které chcete přesunout, jsou podporované.

    - [Ověřte](support-matrix-move-region-azure-vm.md#windows-vm-support) podporované virtuální počítače s Windows.
    - [Ověřte](support-matrix-move-region-azure-vm.md#linux-vm-support) podporované virtuální počítače se systémem Linux a verze jádra.
    - Ověřte podporovaná nastavení [COMPUTE](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [úložiště](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)a [sítě](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) .
2. Ujistěte se, že virtuální počítače mají nejnovější důvěryhodné kořenové certifikáty a aktualizovaný seznam odvolaných certifikátů (CRL). 
    - Na virtuálních počítačích Azure s Windows nainstalujte nejnovější aktualizace Windows.
    - Na virtuálních počítačích se systémem Linux postupujte podle pokynů distributora pro Linux a ujistěte se, že má počítač nejnovější certifikáty a seznam CRL. 
3. Povolení odchozího připojení z virtuálních počítačů:
    - Pokud k řízení odchozího připojení používáte proxy server brány firewall založený na adrese URL, povolte přístup k těmto [adresám URL](support-matrix-move-region-azure-vm.md#url-access) .
    - Pokud k řízení odchozího připojení používáte pravidla skupiny zabezpečení sítě (NSG), vytvořte tato [pravidla značek služeb](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Vyberte prostředky, které se mají přesunout.

Vyberte prostředky, které chcete přesunout. Prostředky přesunete do cílové oblasti v předplatném zdrojové oblasti. Pokud chcete změnit předplatné, můžete to udělat po přesunu prostředků.

1. V Azure Portal otevřete příslušnou skupinu prostředků.
2. Na stránce skupina prostředků vyberte prostředky, které chcete přesunout.
3. Vyberte **přesunout**  >  **přesunout do jiné oblasti**.

    ![Výběr pro přesunutí prostředků do jiné oblasti](./media/move-region-within-resource-group/select-move-region.png)
    
4. V části **zdroj + cíl**vyberte cílovou oblast, do které chcete prostředky přesunout. Pak vyberte **Další**.


    ![Zdrojová a cílová stránka pro výběr cílové oblasti](./media/move-region-within-resource-group/source-target.png)


7. V **prostředcích k přesunu**vyberte **Další**.  
8. V **Vyberte prostředky**vyberte prostředek, který chcete přesunout. Je možné přidat pouze prostředky podporované pro Move. Potom vyberte **Done** (Hotovo).
9. V **Přesunutí prostředků**vyberte **Další**. 
10. V části **Revize a přidat**zkontrolujte podrobnosti o zdroji a cíli.
11. Ujistěte se, že jste pochopili, že metadata o přesouvaných prostředcích se budou ukládat do skupiny prostředků vytvořené pro tento účel a že se povolí, aby měl stěhovací systém vytvořit identitu spravovanou systémem pro přístup k prostředkům předplatného.
1. Vyberte **pokračovat** a začněte přidávat prostředky.

    ![Stránka Souhrn pro kontrolu podrobností a pokračování v přesunutí](./media/move-region-within-resource-group/summary.png)    

11. Spustí se operace přidání prostředku. Po dokončení operace oznámení ukazují, že se prostředky přidaly, a nasazení bylo úspěšné.
13. V části oznámení vyberte **Přidat prostředky pro přesun**.

    ![Zpráva zobrazená v oznámeních](./media/move-region-within-resource-group/notification.png)    


14. Po výběru oznámení se vybrané prostředky přidají do kolekce přesunů v centru prostředků Azure Resource hub.  Resource source vám pomůže kontrolovat závislosti a pak začít přesouvat prostředky do cílové oblasti.

## <a name="resolve-dependencies"></a>Vyřešit závislosti

Prostředky, které přesouváte, se zobrazují na stránce pro **různé oblasti** ve stavu *Příprava čeká na vyřízení* . Spusťte ověřování následujícím způsobem:

1. Pokud zdroje zobrazí zprávu *ověřit závislosti* ve sloupci **problémy** , vyberte tlačítko **ověřit závislosti** . Spustí se proces ověřování.

    ![Tlačítko pro ověření závislostí](./media/move-region-within-resource-group/validate-dependencies.png)

2. Pokud se najde závislosti, vyberte **přidat závislosti**. 
3. V části **přidat závislosti**vyberte závislé prostředky > **přidat závislosti**. Sledujte průběh oznámení.

    ![Tlačítko pro přidání závislostí](./media/move-region-within-resource-group/add-dependencies.png)

3. V případě potřeby přidejte další závislosti a podle potřeby ověřte závislosti. Výběrem **aktualizovat** zajistěte, aby prostředky zobrazovaly aktuální stav.

4. Na stránce **napříč oblastmi** ověřte, že prostředky jsou nyní ve stavu *Příprava čeká* bez problémů.

    ![Stránka pro zobrazení připraveného stavu pro všechny prostředky](./media/move-region-within-resource-group/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>Přesunout zdrojovou skupinu prostředků 

Než budete moct připravit a přesunout prostředky, musí být zdrojová skupina prostředků přítomna v cílové oblasti. 

### <a name="prepare-to-move-the-source-resource-group"></a>Příprava na přesunutí zdrojové skupiny prostředků

Připravte následujícím způsobem:

1. V **různých oblastech**vyberte zdrojovou skupinu prostředků > **připravit**.
2. V **Příprava prostředků**vyberte **připravit**.
1. 
    ![Tlačítko pro přípravu zdrojové skupiny prostředků](./media/move-region-within-resource-group/prepare-source-resource-group.png)

    Během procesu přípravy Resource dogeneruje pomocí nastavení skupiny prostředků Azure Resource Manager (ARM) šablony. Prostředky ve skupině prostředků nejsou ovlivněny.
    
> [!NOTE]
>  Po přípravě skupiny prostředků je stav *zahájit přesun čeká na vyřízení* . Aktualizujte, aby se zobrazil nejnovější stav.

![Stav znázorňující zahájení čekání na stav](./media/move-region-within-resource-group/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>Přesunout zdrojovou skupinu prostředků

Zahajte přesun následujícím způsobem:

1. V **různých oblastech**vyberte skupinu prostředků, > **zahájit přesun** .
2. LN **přesune prostředky**, vyberte **zahájit přesun**. Skupina prostředků se přesune do stavu *zahájení probíhajícího přesunu* .
3. Po zahájení přesunu se vytvoří cílová skupina prostředků na základě vygenerované šablony ARM. Zdrojová skupina prostředků se přesune do stavu *čeká na přesunutí* .

![Stav ukazující přesunutí potvrzení](./media/move-region-availability-zone/commit-move-pending.png)

Potvrzení a dokončení procesu přesunutí:

1. V **různých oblastech**vyberte skupinu prostředků > **potvrzení změn** .
2. LN **přesunout prostředky**, vyberte **Potvrdit**.

> [!NOTE]
> Po potvrzení přesunutí je zdrojová skupina prostředků ve stavu *čekání na odstranění zdroje* .

## <a name="modify-target-settings"></a>Upravit nastavení cíle

Pokud nechcete přesunout zdrojový prostředek, můžete provést jednu z následujících akcí:

- Vytvoří prostředek v cílové oblasti se stejným názvem a nastavením jako prostředek ve zdrojové oblasti.
- Vytvořte nový ekvivalentní prostředek v cílové oblasti. S výjimkou nastavení, která zadáte, je cílový prostředek vytvořen se stejným nastavením jako zdroj.
- Použijte existující prostředek v cílové oblasti.

Upravte nastavení následujícím způsobem:

1. Chcete-li upravit nastavení, vyberte položku ve sloupci **Konfigurace cíle** pro daný prostředek.
2. Na stránce **cílová konfigurace** zadejte cílové nastavení, které chcete použít.
    Změny se provedou jenom pro prostředky, které upravujete. Všechny závislé prostředky musíte aktualizovat samostatně.   
    
Přesné nastavení, které upravíte, závisí na typu prostředku. [Přečtěte si další informace](modify-target-settings.md) o úpravě nastavení cíle.

## <a name="prepare-resources-to-move"></a>Příprava prostředků k přesunu

Teď, když je zdrojová skupina prostředků přesunutá, můžete připravit na přesun dalších prostředků.

1. V **různých oblastech**vyberte prostředky, které chcete připravit. 

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

1. V **různých oblastech**vyberte prostředky se stavem *zahájit přesun čeká na vyřízení*. Pak vyberte **Spustit přesun**.
2. V **Přesunutí prostředků**vyberte **Spustit přesun**.

    ![Vybrat pro tlačítko Zahájit přesun](./media/move-region-within-resource-group/initiate-move.png)

3. Sledujte průběh přesunu na panelu oznámení.


> [!NOTE]
> - Virtuální počítače repliky se v cílové oblasti vytvoří. Zdrojový virtuální počítač je vypnutý a dojde k nějakému výpadku (obvykle minuty).<br/>
> - Stěhovací společnosti znovu vytvoří další prostředky pomocí připravených šablon ARM. Obvykle se nejedná o výpadky.<br/> 
> - V případě nástrojů pro vyrovnávání zatížení se nekopírují pravidla NAT. Po potvrzení přesunu je vytvořte v cílové oblasti.
> - U veřejných IP adres se popisek názvu DNS nezkopíruje. Po potvrzení přesunutí znovu vytvořte popisek.
> - Po přípravě prostředků se stav přesune do stavu *čeká na potvrzení* .


## <a name="discard-or-commit"></a>Zahodit nebo potvrdit?

Po počátečním přesunu se můžete rozhodnout, jestli chcete přesunutí potvrdit, nebo ho zahodit. 

- **Zahodit**: při testování můžete zrušit jeho přesunutí a nechcete skutečně přesunout zdrojový prostředek. Zrušením přesunutí se daný prostředek vrátí do stavu *zahájení přesunu čeká na vyřízení*.
- **Potvrdit**: potvrzení dokončí přesun do cílové oblasti. Po potvrzení bude zdrojový prostředek ve stavu *čeká na odstranění zdroje*a Vy se můžete rozhodnout, jestli ho chcete odstranit.


## <a name="discard-the-move"></a>Zrušit přesun 

Přesunutí můžete zrušit následujícím způsobem:

1. V **různých oblastech**vyberte prostředky s *potvrzením změny stavu čeká na přesunutí*a vyberte **zrušit přesun**.
2. V **Zrušit přesunutí**vyberte **Zrušit**.
3. Sledujte průběh přesunu na panelu oznámení.
4. Po zobrazení oznámení o úspěšném přesunutí vyberte **aktualizovat**. 

> [!NOTE]
> V případě virtuálních počítačů se po zahození prostředků v stavech *zahájit přesun čeká na vyřízení* .

## <a name="commit-the-move"></a>Potvrdit přesun

Pokud chcete dokončit proces přesunutí, potvrďte přesunutí. 


1. V **různých oblastech**vyberte prostředky s *potvrzením změny stavu čeká na vyřízení*a vyberte **Potvrdit přesunutí**.
2. V v **potvrzení zdroje**vyberte **Potvrdit**.

    ![Stránka pro potvrzení přesunutí prostředků](./media/move-region-within-resource-group/commit-resources.png)

3. Sledujte průběh potvrzení na panelu oznámení.

> [!NOTE]
> - Po potvrzení přesunu se virtuální počítače přestanou replikovat. Zdrojový virtuální počítač není ovlivněný potvrzením změn.
> - Potvrzení změn nemá vliv na zdrojové síťové prostředky.
> - Po potvrzení přesunu jsou prostředky ve stavu *odstranění zdroje, který čeká na vyřízení* .

## <a name="configure-settings-after-the-move"></a>Konfigurovat nastavení po přesunutí

1. Vzhledem k tomu, že popisky názvů DNS se pro veřejné IP adresy nekopírují, přejděte po dokončení přesunutí do cílových prostředků a aktualizujte popisek. 
2. Pro interní nástroje pro vyrovnávání zatížení, protože pravidla překladu adres (NAT) nejsou kopírována, přejděte k prostředkům vytvořeným v cílové oblasti a aktualizujte pravidla překladu adres (NAT).
3. Služba mobility není automaticky odinstalována z virtuálních počítačů. Odinstalujte ji ručně nebo ji nechte v případě, že plánujete přesunout server znovu.
## <a name="delete-source-resources-after-commit"></a>Odstranit zdrojové prostředky po potvrzení změn

Po přesunutí můžete případně Odstranit prostředky ve zdrojové oblasti. 

1. V **oblasti napříč oblastmi**vyberte název každého zdrojového prostředku, který chcete odstranit.
2. Na stránce vlastnosti každého prostředku vyberte možnost **Odstranit**.

## <a name="delete-additional-resources-created-for-move"></a>Odstranění dalších prostředků vytvořených pro přesun

Po přesunutí můžete ručně odstranit kolekci přesunů a Site Recovery prostředky, které byly vytvořeny.

- Kolekce přesunů je ve výchozím nastavení skrytá. Pokud se chcete podívat, jak to potřebujete, zapněte skryté prostředky.
- Úložiště mezipaměti má zámek, který je třeba odstranit, aby jej bylo možné odstranit.

Odstraňte následujícím způsobem: 

1. Vyhledejte prostředky ve skupině prostředků ```RegionMoveRG-<sourceregion>-<target-region>``` ve zdrojové oblasti.
2. Ověřte, že se všechny virtuální počítače a další zdrojové prostředky v kolekci Move přesunuly nebo odstranily. Tím se zajistí, že se nepoužívají žádné nedokončené prostředky.
2. Odstranit prostředky:

    - Název kolekce přesunutí je ```movecollection-<sourceregion>-<target-region>``` .
    - Název účtu úložiště mezipaměti je ```resmovecache<guid>```
    - Název trezoru je ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Další kroky


[Přečtěte si informace o](about-move-process.md) procesu přesunutí.
