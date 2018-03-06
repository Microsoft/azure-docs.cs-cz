
# [Dokumentace ke službě Batch](index.yml)

# Přehled
## [Co je Azure Batch?](batch-technical-overview.md)

# Šablony Rychlý start
## [Spuštění úlohy Batch – CLI](quick-create-cli.md)
## [Spuštění úlohy Batch – portál](quick-create-portal.md)
## [Spuštění úlohy Batch – .NET](quick-run-dotnet.md)
## [Spuštění úlohy Batch – Python](quick-run-python.md)


# Kurzy

## [Paralelní zpracování souborů – .NET](tutorial-parallel-dotnet.md)
## [Paralelní zpracování souborů – Python](tutorial-parallel-python.md)
## [Vykreslování scén s využitím Arnoldu](tutorial-rendering-cli.md)
## [Paralelní simulace R](tutorial-r-doazureparallel.md)

# Ukázky
## Ukázky kódů
### [Ukázky kódu Azure](https://azure.microsoft.com/resources/samples/?service=batch)
### [Úložiště ukázek služby Batch](https://github.com/Azure/azure-batch-samples)
## [Azure CLI](cli-samples.md)

# Koncepty
## [Funkce pro vývojáře](batch-api-basics.md)
## [Rozhraní API a nástroje](batch-apis-tools.md)
## [Kvóty a omezení](batch-quota-limit.md)

# Návody
## Správa účtů služby Batch
### [Správa účtů služby Batch pomocí portálu](batch-account-create-portal.md)
### [Správa účtů služby Batch pomocí rozhraní Batch Management .NET](batch-management-dotnet.md)

## Ověřování pomocí Azure AD
### [Azure AD se službou Batch](batch-aad-auth.md)
### [Azure AD se službou Batch Management](batch-aad-auth-management.md)

## [Vytváření balíčků aplikací](batch-application-packages.md)

## Vytváření a správa fondů
### [Automatické škálování výpočetních uzlů](batch-automatic-scaling.md)
### [Výběr velikosti virtuálního počítače pro výpočetní uzly](batch-pool-vm-sizes.md)
### [Konfigurace přístupu k výpočetním uzlům](pool-endpoint-configuration.md)
### [Vytvoření fondu s využitím vlastní image](batch-custom-images.md)
### [Vytvoření fondu ve virtuální síti](batch-virtual-network.md)
### [Použití virtuálních počítačů náročných na výpočetní výkon](batch-pool-compute-intensive-sizes.md)
### [Použití výpočetních uzlů s Linuxem](batch-linux-nodes.md)
### [Použití virtuálních počítačů s nízkou prioritou](batch-low-pri-vms.md)

## Správa úloh a úkolů
### [Příprava úlohy a dokončovací úkoly](batch-job-prep-release.md)
### [Souběžné úlohy uzlu](batch-parallel-node-tasks.md)
### [Počet úloh podle stavu](batch-get-task-counts.md)
### [Závislosti úkolů](batch-task-dependencies.md)
### [Uživatelské účty pro spouštění úloh](batch-user-accounts.md)
### [Spuštění dávkových úloh bez kódu](batch-cli-templates.md)


## [Trvalý výstup úloh a funkcí](batch-task-output.md)
### [Trvalý výstup pomocí rozhraní API služby Batch](batch-task-output-files.md)
### [Trvalý výstup pomocí knihovny File Conventions](batch-task-output-file-conventions.md)

## Spouštění úloh
### [Úlohy MPI](batch-mpi.md)
### [Úlohy kontejneru](batch-docker-container-workloads.md)
### [Vykreslování](batch-rendering-service.md)
### [Zpracování dat pomocí služeb Batch a Data Factory](../data-factory/v1/data-factory-data-processing-using-batch.md?toc=%2fazure%2fbatch%2ftoc.json)

## Sledování prostředků
### [Protokolování diagnostiky](batch-diagnostics.md)
### [Efektivní seznamové dotazy](batch-efficient-list-queries.md)

## Použití nástrojů pro vývojáře
### [Použití .NET služby Batch](batch-dotnet-get-started.md)
### [Použití Pythonu služby Batch](batch-python-tutorial.md)
### [Použití Node.js služby Batch](batch-nodejs-get-started.md)
### [Použití rutin PowerShell](batch-powershell-cmdlets-get-started.md)
### [Použití rozhraní příkazového řádku Azure](batch-cli-get-started.md)

# Referenční informace

## [Azure CLI](/cli/azure/batch)
## [Azure PowerShell](/powershell/module/azurerm.batch)
## [.NET](/dotnet/api/overview/azure/batch)
## [Java](/java/api/overview/azure/batch)
## [Node.js](/javascript/api/overview/azure/batch)
## [Python](/python/api/overview/azure/batch)
## REST
### [Služba Batch](/rest/api/batchservice)
### [Správa služby Batch](/rest/api/batchmanagement/)
## [Dávková analýza](batch-analytics.md)
### [Událost vytvoření fondu](batch-pool-create-event.md)
### [Událost zahájení odstranění fondu](batch-pool-delete-start-event.md)
### [Událost dokončení odstranění fondu](batch-pool-delete-complete-event.md)
### [Událost zahájení změny velikosti fondu](batch-pool-resize-start-event.md)
### [Událost dokončení změny velikosti fondu](batch-pool-resize-complete-event.md)
### [Událost zahájení úlohy](batch-task-start-event.md)
### [Událost dokončení úlohy](batch-task-complete-event.md)
### [Událost selhání úlohy](batch-task-fail-event.md)
## [Proměnné prostředí výpočetních uzlů Batch](batch-compute-node-environment-variables.md)

# Zdroje a prostředky
## [Plány Azure do budoucna](https://azure.microsoft.com/roadmap/)
## [Fórum k službě Batch](https://social.msdn.microsoft.com/Forums/en-us/home?forum=azurebatch)
## [Batch Shipyard](https://github.com/Azure/batch-shipyard)
## [Blog](https://blogs.technet.microsoft.com/windowshpc/)
## [Řešení HPC v Azure](../virtual-machines/linux/high-performance-computing.md?toc=%2fazure%2fbatch%2ftoc.json)
## [Ceny](https://azure.microsoft.com/pricing/details/batch/)
## [ Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/)
## [Šablona Resource Manageru](/azure/templates/microsoft.batch/batchaccounts)
## [Aktualizace služeb](https://azure.microsoft.com/updates/?product=batch&updatetype=&platform=)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-batch)
## [Videa](https://azure.microsoft.com/documentation/videos/index/?services=batch)


