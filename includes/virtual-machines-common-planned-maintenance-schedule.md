---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: c7fe0d6f8e03501cca7a8b98f95286b6a21c0476
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735722"
---
## <a name="multi-and-single-instance-vms"></a>Virtuální počítače jedné Instance a více
Mnoho zákazníků a systémem Azure počet je důležité, můžete naplánovat při své virtuální počítače této oblasti podstupovali plánované údržby kvůli výpadku – přibližně 15 minut –, který nastane během údržby. Můžete použít skupiny dostupnosti, které pomáhá řídit při zřízené virtuální počítače přijímat plánované údržby.

Existují dva možné konfigurace pro virtuální počítače spuštěné v Azure. Virtuální počítače jsou buď nakonfigurované jako více instancí nebo s jednou instancí. Pokud virtuální počítače jsou ve skupině dostupnosti, pak jsou nakonfigurované jako více instancemi. Mějte na paměti, dokonce i jeden virtuální počítače je možné nasadit ve skupině dostupnosti, takže jsou považovány za více instancemi. Pokud virtuální počítače nejsou ve skupině dostupnosti, pak jsou nakonfigurované jako jedinou instancí.  Podrobnosti o skupinách dostupnosti najdete v tématu [Správa dostupnosti virtuálních počítačů z Windows](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) nebo [Správa dostupnosti z vašich virtuálních počítačů s Linuxem](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Plánovaná údržba aktualizací do jednou instancí a s více instancemi virtuálních počítačů dojít samostatně. Překonfigurováním vaše virtuální počítače jedné instance (v případě, že jsou s více instancemi) nebo víc instancí (v případě, že jsou jednou instancí) můžete řídit, kdy své virtuální počítače přijímat plánované údržby. Zobrazit [plánované údržby pro virtuální počítače Azure s Linuxem](../articles/virtual-machines/linux/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [plánované údržby pro virtuální počítače s Windows Azure](../articles/virtual-machines/windows/planned-maintenance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) podrobnosti o plánované údržbě pro virtuální počítače Azure.

## <a name="for-multi-instance-configuration"></a>Pro konfiguraci s více instancemi
Můžete vybrat čas plánované údržby ovlivní vaše virtuální počítače, které jsou nasazeny v konfiguraci skupiny dostupnosti odstraněním těchto virtuálních počítačů ze skupiny dostupnosti.

1. E-mail je odeslán, sedm dní před plánovanou údržbou k vašim virtuálním počítačům v konfiguraci s více instancemi. ID předplatného a názvů příslušných virtuálních počítačích s více instancemi jsou zahrnuty v textu e-mailu.
2. Během těchto sedm dní můžete zvolit, čas, kdy vaše instance se aktualizují tak, že odeberete více instancemi virtuálních počítačů v dané oblasti z jejich dostupnosti. Tato změna konfigurace způsobí restartování, jako virtuální počítač se přesouvá z jednoho fyzického hostitele, je určená pro údržbu, do jiného fyzického hostitele, která není určená pro údržbu.
3. Virtuální počítač můžete odebrat z její skupiny dostupnosti na webu Azure Portal.

   1. Na portálu vyberte virtuální počítač odebrat ze sady dostupnosti.  

   2. V části **nastavení**, klikněte na tlačítko **dostupnosti**.

      ![Výběr skupiny dostupnosti](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)

   3. Ve skupině dostupně rozevírací nabídku, vyberte "Není součástí nastavení dostupnosti."

      ![Odebrat ze sady](./media/virtual-machines-planned-maintenance-schedule/availabilitysetwarning.png)

   4. V horní části stránky, klikněte na tlačítko **Uložit**. Klikněte na tlačítko **Ano** potvrďte, že tato akce restartuje virtuální počítač.

   >[!TIP]
   >Je možné změnit konfiguraci virtuálního počítače s více instancemi později výběrem jedné z uvedených dostupnosti.

4. Odebrat ze skupiny dostupnosti virtuálních počítačů přesunou do hostitele s jednou instancí a nejsou aktualizovány během plánované údržby pro konfiguraci nastavení dostupnosti.
5. Po aktualizace pro virtuální počítače nastavení dostupnosti je dokončena (podle plánu uvedených v původního e-mailu), měli byste přidat virtuální počítače zpět do jejich dostupnosti. Stávají součástí nastavení dostupnosti změní konfiguraci virtuálních počítačů jako více instancemi a má za následek restartování. Obvykle po dokončení všechny aktualizace více instancí napříč celým prostředím Azure řídí údržby jednou instancí.

Odebrání nastavení dostupnosti virtuálního počítače můžete také dosáhnout pomocí Azure Powershellu:

```
Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM
```

## <a name="for-single-instance-configuration"></a>U konfigurací s jednou instancí
Můžete vybrat čas plánované údržby se vás týká virtuálních počítačů v konfiguraci s jednou instancí tak, že přidáte tyto virtuální počítače do skupiny dostupnosti.

Podrobný postup

1. E-mail je odeslán, sedm dní před plánovaná údržba pro virtuální počítače v konfiguraci s jednou instancí. ID předplatného a názvů příslušných virtuálních počítačích jednou instancí jsou zahrnuty v textu e-mailu.
2. Během těchto sedm dní můžete zvolit, čas, kdy restartování vaší instance tak, že přidáte jednou instancí virtuálních počítačů do skupiny dostupnosti v dané stejné oblasti. Tato změna konfigurace způsobí restartování, jako virtuální počítač se přesouvá z jednoho fyzického hostitele, je určená pro údržbu, do jiného fyzického hostitele, která není určená pro údržbu.
3. Postupujte podle pokynů pro přidání existujících virtuálních počítačů do skupiny dostupnosti pomocí webu Azure portal a Azure Powershellu. (Viz ukázky Azure Powershellu, který zahrnuje následující kroky.)
4. Jakmile jsou tyto virtuální počítače překonfigurovat jako více instancemi, jsou vyloučeny ze plánovaná údržba pro virtuální počítače s jednou instancí.
5. Po dokončení aktualizace jednou instancí virtuálních počítačů (podle plánu do původního e-mailu) můžete vrátit virtuální počítače s jednou instancí tak, že odeberete virtuální počítače z jejich dostupnosti.

Přidání virtuálního počítače do skupiny dostupnosti také lze dosáhnout pomocí Azure Powershellu:

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/
