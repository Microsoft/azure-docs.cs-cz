

Rozšíření virtuálních počítačů vám můžou pomoci:

* Upravit funkce zabezpečení a identity, jako je resetování hodnot účtu a použití antimalwaru
* Spustit, zastavit nebo konfigurovat monitorování a diagnostiku
* Resetovat nebo instalovat funkce připojení, jako je RDP a SSH
* Diagnostikovat, monitorovat a spravovat virtuální počítače

Existuje ještě mnoho dalších funkcí. Pravidelně se vydávají nové funkce rozšíření virtuálních počítačů. Tento článek popisuje agenty virtuálních počítačů Azure pro Windows a Linux, a jak podporují funkce rozšíření virtuálních počítačů. Seznam rozšíření virtuálních počítačů podle kategorií funkcí najdete v tématu [Rozšíření a funkce virtuálních počítačů Azure](../articles/virtual-machines/extensions/features-windows.md).

## <a name="azure-vm-agents-for-windows-and-linux"></a>Agenti virtuálních počítačů Azure pro Windows a Linux
Agent virtuálního počítače Azure (agent virtuálního počítače) je zabezpečený, nenáročný proces, který instaluje, konfiguruje a odebírá rozšíření virtuálních počítačů na instancích virtuálních počítačů Azure. Agent virtuálního počítače představuje místní, zabezpečenou službu řízení pro virtuální počítače Azure. Rozšíření, která agent načítá, poskytují konkrétní funkce pro zvýšení produktivity při používání instance.

Existují dva agenti virtuálních počítačů Azure – jeden pro virtuální počítače s Windows a jeden pro virtuální počítače s Linuxem.

Pokud chcete, aby instance virtuálního počítače využívala jedno nebo více rozšíření virtuálních počítačů, musí být na příslušné instanci nainstalovaný agent virtuálního počítače. Image virtuálního počítače vytvořená pomocí webu Azure Portal a image z **Marketplace** nainstaluje agenta virtuálního počítače automaticky jako součást procesu vytváření. Pokud v instanci virtuálního počítače chybí agent virtuálního počítače, můžete agenta virtuálního počítače nainstalovat po vytvoření instance virtuálního počítače. Nebo můžete agenta nainstalovat do vlastní image virtuálního počítače, kterou následně nahrajete.

> [!IMPORTANT]
> Tito agenti virtuálního počítače jsou velmi nenáročné služby, které umožňují zabezpečenou správu instancí virtuálního počítače. Můžou nastat případy, kdy nebudete chtít agenta virtuálního počítače. V takovém případě se ujistěte, že vytváříte virtuální počítače bez nainstalovaného agenta virtuálního počítače, a to pomocí Azure CLI nebo PowerShellu. I když je možné agenta virtuálního počítače odebrat ručně, chování rozšíření virtuálního počítače pak není definované. Proto se odebírání nainstalovaného agenta virtuálního počítače nepodporuje.
>

Agent virtuálního počítače je povolený v následujících situacích:

* Když vytvoříte instanci virtuálního počítače pomocí webu Azure Portal a výběrem image z **Marketplace**.
* Když vytvoříte instanci virtuálního počítače pomocí rutiny [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) nebo [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx). Virtuální počítač můžete vytvořit bez agenta virtuálního počítače, pokud do rutiny [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx) přidáte parametr **–DisableGuestAgent**.

* Když ručně stáhnete a nainstalujete agenta virtuálního počítače na existující instanci virtuálního počítače a nastavíte hodnotu **ProvisionGuestAgent** na **true**. Tento postup můžete použít pro agenty Windows i Linux pomocí příkazu PowerShellu nebo volání REST. (Pokud po ruční instalaci agenta virtuálního počítače nenastavíte hodnotu **ProvisionGuestAgent**, přidání agenta virtuálního počítače se nerozpozná správně.) Následující příklad kódu ukazuje, jak to lze provést pomocí PowerShellu s již určenými argumenty `$svc` a `$name`:

      $vm = Get-AzureVM –ServiceName $svc –Name $name
      $vm.VM.ProvisionGuestAgent = $TRUE
      Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

* Když vytvoříte image virtuálního počítače, která obsahuje nainstalovaného agenta virtuálního počítače. Jakmile se image s agentem virtuálního počítače vytvoří, můžete ji nahrát do Azure. Pro virtuální počítače s Windows si stáhněte [soubor .msi agenta virtuálního počítače s Windows](http://go.microsoft.com/fwlink/?LinkID=394789) a nainstalujte agenta virtuálního počítače. Pro virtuální počítač s Linuxem, nainstalujte agenta virtuálního počítače z úložiště Githubu, které jsou umístěné v <https://github.com/Azure/WALinuxAgent>. Další informace o instalaci agenta virtuálního počítače v Linuxu najdete v [Uživatelské příručce k agentu virtuálního počítače Azure s Linuxem](../articles/virtual-machines/extensions/agent-linux.md).

> [!NOTE]
> V modelu PaaS se agent virtuálního počítače nazývá **WindowsAzureGuestAgent** a vždy je k dispozici na virtuálních počítačích webové role nebo role pracovního procesu. (Další informace najdete v článku [Infrastruktura rolí Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx).) Agent virtuálního počítače pro virtuální počítače rolí nyní může přidávat rozšíření do virtuálních počítačů cloudových služeb stejným způsobem jako do trvalých virtuálních počítačů. Největší rozdíl mezi rozšířeními virtuálních počítačů na virtuálních počítačích rolí a trvalých virtuálních počítačích je při přidání rozšíření virtuálních počítačů. V případě virtuálních počítačů rolí se rozšíření přidávají nejprve do cloudové služby a následně do nasazení v rámci této cloudové služby.
>
> Pokud chcete zobrazit výpis všech dostupných rozšíření virtuálních počítačů rolí, použijte rutinu [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx).
>
>

## <a name="find-add-update-and-remove-vm-extensions"></a>Vyhledání, přidání, aktualizace a odebrání rozšíření virtuálních počítačů
Podrobnosti o těchto úlohách najdete v tématu, které se zabývá [přidáním, vyhledáním, aktualizací a odebráním rozšíření virtuálního počítače Azure](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
