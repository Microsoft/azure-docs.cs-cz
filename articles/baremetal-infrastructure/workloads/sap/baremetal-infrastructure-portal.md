---
title: Jednotky instancí BareMetal v Azure
description: Naučte se identifikovat a interagovat s jednotkami instancí BareMetal prostřednictvím Azure Portal.
ms.topic: how-to
ms.date: 1/4/2021
ms.openlocfilehash: 30e1661e82546dbaf6d8dc4288ad896df89f401e
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861033"
---
# <a name="manage-baremetal-instances-through-the-azure-portal"></a>Správa instancí BareMetal pomocí webu Azure Portal
 
Tento článek ukazuje, jak [Azure Portal](https://portal.azure.com/) zobrazuje [instance BareMetal](baremetal-overview-architecture.md). Tento článek také ukazuje aktivity, které můžete provádět v Azure Portal s nasazenými jednotkami instancí BareMetal. 
 
## <a name="register-the-resource-provider"></a>Registrace poskytovatele prostředků
Poskytovatel prostředků Azure pro instance BareMetal poskytuje viditelnost instancí v Azure Portal v současnosti ve verzi Public Preview. Ve výchozím nastavení předplatné Azure, které používáte pro nasazení instancí BareMetal, zaregistruje poskytovatele prostředků *BareMetalInfrastructure* . Pokud se nezobrazuje vaše nasazené jednotky instance BareMetal, musíte poskytovatele prostředků zaregistrovat u svého předplatného. 

Existují dva způsoby, jak zaregistrovat poskytovatele prostředků instance BareMetal:
 
* [Azure CLI](#azure-cli)
 
* [Azure Portal](#azure-portal)
 
### <a name="azure-cli"></a>Azure CLI
 
Přihlaste se k předplatnému Azure, které používáte pro nasazení instance BareMetal prostřednictvím rozhraní příkazového řádku Azure CLI. Poskytovatele prostředků BareMetalInfrastructure můžete zaregistrovat pomocí:

```azurecli-interactive
az provider register --namespace Microsoft.BareMetalInfrastructure
```
 
Další informace najdete v článku [poskytovatelé a typy prostředků Azure](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell).
 
### <a name="azure-portal"></a>portál Azure
 
Poskytovatele prostředků BareMetalInfrastructure můžete zaregistrovat prostřednictvím Azure Portal.
 
V Azure Portal budete muset uvést své předplatné a pak dvakrát kliknout na předplatné, které jste použili k nasazení jednotek instance BareMetal.
 
1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

1. V nabídce webu Azure Portal vyberte **Všechny služby**.

1. V poli **všechny služby** zadejte **předplatné** a pak vyberte **předplatná**.

1. Vyberte předplatné ze seznamu odběrů, které chcete zobrazit.

1. Vyberte **poskytovatelé prostředků** a do hledání zadejte **BareMetalInfrastructure** . Poskytovatel prostředků by měl být **zaregistrován**, jak ukazuje obrázek.
 
>[!NOTE]
>Pokud poskytovatel prostředků není zaregistrovaný, vyberte **zaregistrovat**.
 
:::image type="content" source="media/baremetal-infrastructure-portal/register-resource-provider-azure-portal.png" alt-text="Snímek obrazovky zobrazující registrovanou jednotku instance BareMetal":::
 
## <a name="baremetal-instance-units-in-the-azure-portal"></a>Jednotky instance BareMetal v Azure Portal
 
Když odešlete žádost o nasazení instance BareMetal, zadáte předplatné Azure, které se připojujete k instancím BareMetal. Použijte stejné předplatné, které používáte k nasazení aplikační vrstvy, která funguje s jednotkami instancí BareMetal.
 
Během nasazování instancí BareMetal se v předplatném Azure, které jste použili v žádosti o nasazení, vytvoří nová [Skupina prostředků Azure](../../../azure-resource-manager/management/manage-resources-portal.md) . Tato nová skupina prostředků obsahuje všechny jednotky instancí BareMetal, které jste nasadili v rámci konkrétního předplatného.

1. V předplatném BareMetal ve Azure Portal vyberte **skupiny prostředků**.
 
   :::image type="content" source="media/baremetal-infrastructure-portal/view-baremetal-instance-units-azure-portal.png" alt-text="Snímek obrazovky, který zobrazuje seznam skupin prostředků":::

1. V seznamu vyhledejte novou skupinu prostředků.
 
   :::image type="content" source="media/baremetal-infrastructure-portal/filter-resource-groups.png" alt-text="Snímek obrazovky zobrazující jednotku instance BareMetal v seznamu filtrovaných skupin prostředků" lightbox="media/baremetal-infrastructure-portal/filter-resource-groups.png":::
   
   >[!TIP]
   >Můžete filtrovat podle předplatného, které jste použili k nasazení instance BareMetal. Po filtrování na správné předplatné můžete mít dlouhý seznam skupin prostředků. Vyhledejte ho s následnou opravou **-TXXX** , kde xxx je tři číslice jako **-T250**.

1. Vyberte novou skupinu prostředků, pro kterou chcete zobrazit podrobnosti. V imagi se zobrazuje jedna nasazená jednotka BareMetal instance.
   
   >[!NOTE]
   >Pokud jste nasadili několik tenantů instancí BareMetal v rámci stejného předplatného Azure, měli byste vidět několik skupin prostředků Azure.
 
## <a name="view-the-attributes-of-a-single-instance"></a>Zobrazení atributů jedné instance
 
Můžete zobrazit podrobnosti jedné jednotky. V seznamu instance BareMetal vyberte jednu instanci, kterou chcete zobrazit.
 
:::image type="content" source="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png" alt-text="Snímek obrazovky zobrazující atributy jednotky instance BareMetal jedné instance" lightbox="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png":::
 
Atributy v imagi se neliší od atributů virtuálních počítačů Azure. Vlevo uvidíte skupinu prostředků, oblast Azure a název a ID předplatného. Pokud jste přiřadili značky, uvidíte je také tady. Ve výchozím nastavení nejsou k jednotkám instance BareMetal přiřazeny štítky.
 
Napravo se zobrazí název jednotky, operační systém (OS), IP adresa a SKU, které zobrazují počet vláken procesoru a paměti. Zobrazí se také stav napájení a verze hardwaru (revize razítka instance BareMetal). Stav napájení označuje, zda je hardwarová jednotka zapnutá nebo vypnutá. Podrobnosti o operačním systému ale neoznačují, jestli jsou spuštěné a spuštěné.
 
Možné hardwarové Revize:

* Revize 3 (rev 3)

* Revize 4 (Rev 4)
 
* Revize 4,2 (Rev 4,2)
 
>[!NOTE]
>Rev 4,2 je nejnovější přepálená infrastruktura BareMetal s využitím existující architektury Rev 4. Rev 4 poskytuje užší blízkost pro hostitele virtuálních počítačů Azure. Má významná vylepšení latence sítě mezi virtuálními počítači Azure a jednotkami instancí BareMetal nasazenými v razítkech a na řádcích Rev 4. Pomocí Azure Portal můžete přistupovat k instancím služby BareMetal a spravovat je. Další informace najdete v tématu [infrastruktura BareMetal v Azure](baremetal-overview-architecture.md).
 
Na pravé straně se také nachází název [skupiny umístění s blízkostií Azure](../../../virtual-machines/linux/co-location.md) , který se vytvoří automaticky pro každou nasazenou jednotku instance BareMetal. Pokud nasadíte virtuální počítače Azure, které hostují aplikační vrstvu, odkažte na skupinu umístění blízkosti. Když použijete skupinu umístění blízkosti přidruženou ke jednotce instance BareMetal, zajistíte, že se virtuální počítače Azure nasadí blízko do jednotky instance BareMetal.
 
>[!TIP]
>Pokud chcete vrstvu aplikace najít ve stejném datovém centru Azure jako revizi 4. x, přečtěte si téma [skupiny umístění blízkosti Azure pro optimální latenci sítě](../../../virtual-machines/workloads/sap/sap-proximity-placement-scenarios.md).
 
## <a name="check-activities-of-a-single-instance"></a>Kontrolovat aktivity jedné instance
 
Můžete kontrolovat aktivity jedné jednotky. Jedna z hlavních aktivit zaznamenaných je restart jednotky. Uvedená data zahrnují stav aktivity, časové razítko, aktivované aktivity, ID předplatného a uživatele Azure, který aktivitu aktivoval.
 
:::image type="content" source="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png" alt-text="Snímek obrazovky zobrazující aktivity jednotky instance BareMetal" lightbox="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png":::
 
Změny v metadatech jednotky v Azure se také zaznamenávají v protokolu aktivit. Kromě iniciace restartování můžete zobrazit aktivitu **BareMetallnstances zápisu**. Tato aktivita neprovede žádné změny v samotné jednotce instance BareMetal, ale dokumentuje změny v metadatech jednotky v Azure.
 
Další zaznamenaná aktivita je při přidání nebo odstranění [značky](../../../azure-resource-manager/management/tag-resources.md) instance.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Přidání a odstranění značky Azure do instance
 
Můžete přidat značky Azure do jednotky instance BareMetal nebo je odstranit. Způsob přiřazování značek se neliší od přiřazení značek k virtuálním počítačům. Stejně jako u virtuálních počítačů existují značky v metadatech Azure a u instancí BareMetal mají stejná omezení jako značky pro virtuální počítače.
 
Odstranění značek funguje stejným způsobem jako u virtuálních počítačů. Použití a odstranění značky jsou uvedeny v protokolu aktivit jednotky instance BareMetal.
 
## <a name="check-properties-of-an-instance"></a>Zkontroluje vlastnosti instance.
 
Když získáte instance, můžete přejít do části Properties (vlastnosti) a zobrazit shromážděná data o instancích. Shromažďovaná data zahrnují připojení Azure, back-end úložiště, ID okruhu ExpressRoute, jedinečné ID prostředku a ID předplatného. Tyto informace použijete v žádostech o podporu nebo při nastavování konfigurace snímku úložiště.
 
Další důležité informace najdete v části IP adresa úložiště NFS. Izoluje vaše úložiště do svého **tenanta** v zásobníku instance BareMetal. Tuto IP adresu použijete při úpravách [konfiguračního souboru pro zálohy snímků úložiště](../../../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots).
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-properties.png" alt-text="Snímek obrazovky zobrazující nastavení vlastnosti instance BareMetal" lightbox="media/baremetal-infrastructure-portal/baremetal-instance-properties.png":::
 
## <a name="restart-a-unit-through-the-azure-portal"></a>Restartování jednotky pomocí Azure Portal
 
Existují různé situace, kdy operační systém nedokončí restart, což vyžaduje restart jednotky instance BareMetal. Restartování jednotky můžete provést přímo z Azure Portal:
 
Vyberte **restartovat** a potvrďte restartování jednotky kliknutím na **Ano** .
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-restart.png" alt-text="Snímek obrazovky, který ukazuje, jak restartovat jednotku instance BareMetal":::
 
Po restartování jednotky instance BareMetal se dostanou zpoždění. Během této prodlevy se stav napájení přesune od **začátku** do **začátku**, což znamená, že se operační systém kompletně spustil. V důsledku toho se po restartování nemůžete přihlásit k jednotce hned po přepnutí stavu na **začátek**.
 
>[!IMPORTANT]
>V závislosti na velikosti paměti v jednotce instance BareMetal může restartování a restartování hardwaru a operačního systému trvat až jednu hodinu.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>Otevření žádosti o podporu pro instance BareMetal
 
Žádosti o podporu můžete odeslat konkrétně pro jednotku instance BareMetal.
1. V Azure Portal v části **pomoc a podpora** vytvořte **[novou žádost o podporu](https://rc.portal.azure.com/#create/Microsoft.Support)** a zadejte pro lístek následující informace:
 
   - **Typ problému:** Vyberte typ problému.
 
   - **Předplatné:** Vyberte své předplatné.
 
   - **Služba:** Infrastruktura BareMetal
 
   - **Prostředek:** Zadejte název instance.
 
   - **Shrnutí:** Zadejte souhrn vaší žádosti.
 
   - **Typ problému:** Vyberte typ problému.
 
   - **Podtyp problému:** Vyberte podtyp pro daný problém.

1. Vyberte kartu **řešení** a najděte řešení problému. Pokud nemůžete najít řešení, pokračujte na další krok.

1. Vyberte kartu **Podrobnosti** a vyberte, jestli se jedná o problém s virtuálními počítači nebo jednotkami instance BareMetal. Tyto informace pomáhají nasměrovat požadavek na podporu na správné odborníky.

1. Určete, kdy problém začal a vyberte oblast instance.

1. Zadejte další podrobnosti o žádosti a v případě potřeby nahrajte soubor.

1. Kliknutím na tlačítko **zkontrolovat + vytvořit** odešlete žádost.
 
Zástupce podpory může potvrdit vaši žádost až pět pracovních dnů.

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další informace o úlohách, přečtěte si téma [typy úloh BareMetal](../../../virtual-machines/workloads/sap/get-started.md).