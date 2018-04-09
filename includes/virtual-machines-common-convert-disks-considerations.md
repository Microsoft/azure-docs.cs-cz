
* Převod vyžaduje restartování virtuálního počítače, proto naplánujte migraci virtuálních počítačů během už existujícího časového období údržby. 

* Převod je nevratný. 

* Upozorňujeme, že všechny uživatele s [Přispěvatel virtuálních počítačů](../articles/active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor) role nebude možné změnit velikost virtuálního počítače (jak se může před převod). Je to proto, že virtuální počítače s spravované disky vyžadovat, aby uživatel má oprávnění Microsoft.Compute/disks/write na discích operačního systému.

* Nezapomeňte převod otestovat. Před migrací v produkčním prostředí proveďte migraci testovacího virtuálního počítače.

* Během převodu virtuální počítač uvolníte. Virtuální počítač obdrží novou IP adresu při spuštění po převodu. V případě potřeby můžete virtuálnímu počítači [přiřadit statickou IP adresu](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md).

* Původní virtuální pevné disky a účet úložiště používané virtuálním počítačem před převodem se neodstraní. Budou se vám za ně i nadále účtovat poplatky. Abyste se vyhnuli účtování poplatků za tyto artefakty, po ověření dokončení převodu odstraňte původní objekty blob virtuálních pevných disků.

* Zkontrolujte minimální verzi agenta virtuálního počítače Azure, které jsou potřebné k podpoře procesu převodu. Informace o tom, jak zkontrolovat a aktualizovat vaše verze agenta najdete v tématu [minimální verzi podporu pro agenty virtuálních počítačů v Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)